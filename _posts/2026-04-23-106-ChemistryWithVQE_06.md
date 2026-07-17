---
title: 15차시 6:Quantum Chemistry with VQE 6
layout: single
classes: wide
categories:
  - Chemistry with VQE
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 6. Determining a molecular geometry

이전 절에서는 분자의 바닥 상태 에너지를 결정하기 위해 VQE를 구현했습니다. 그것도 양자 컴퓨팅의 유효한 용도이지만, 분자의 구조를 결정하는 것은 훨씬 더 유용할 것입니다.

## 1단계: 고전적 입력을 양자 문제로 매핑하기

이원자 수소의 기본 예시를 계속 사용하면, 변경할 수 있는 유일한 기하학적 매개변수는 결합 길이입니다. 이를 수행하기 위해 이전과 동일하게 진행하되, 초기 분자 구조 생성 시 변수(인수의 결합 길이 *x*)를 사용합니다. 이는 상당히 간단한 변경이지만, 페르미온 해밀토니안 구조에서 시작하여 매핑을 거쳐 최종적으로 비용 함수에 이르기까지 전파되므로 전체 과정의 함수에 이 변수가 포함되어야 합니다.

먼저, 이전에 사용했던 일부 패키지를 로드하고 숄레스키(Cholesky) 함수를 정의합니다.

```python
from qiskit.quantum_info import SparsePauliOp
import matplotlib.pyplot as plt
import numpy as np

#!pip install pyscf==2.4.0
from pyscf import ao2mo, gto, mcscf, scf


def cholesky(V, eps):
    # see https://arxiv.org/pdf/1711.02242.pdf section B2
    # see https://arxiv.org/abs/1808.02625
    # see https://arxiv.org/abs/2104.08957
    no = V.shape[0]
    chmax, ng = 20 * no, 0
    W = V.reshape(no**2, no**2)
    L = np.zeros((no**2, chmax))
    Dmax = np.diagonal(W).copy()
    nu_max = np.argmax(Dmax)
    vmax = Dmax[nu_max]
    while vmax > eps:
        L[:, ng] = W[:, nu_max]
        if ng > 0:
            L[:, ng] -= np.dot(L[:, 0:ng], (L.T)[0:ng, nu_max])
        L[:, ng] /= np.sqrt(vmax)
        Dmax[: no**2] -= L[: no**2, ng] ** 2
        ng += 1
        nu_max = np.argmax(Dmax)
        vmax = Dmax[nu_max]
    L = L[:, :ng].reshape((no, no, ng))
    print(
        "accuracy of Cholesky decomposition ",
        np.abs(np.einsum("prg,qsg->prqs", L, L) - V).max(),
    )
    return L, ng


def identity(n):
    return SparsePauliOp.from_list([("I" * n, 1)])


def creators_destructors(n, mapping="jordan_wigner"):
    c_list = []
    if mapping == "jordan_wigner":
        for p in range(n):
            if p == 0:
                ell, r = "I" * (n - 1), ""
            elif p == n - 1:
                ell, r = "", "Z" * (n - 1)
            else:
                ell, r = "I" * (n - p - 1), "Z" * p
            cp = SparsePauliOp.from_list([(ell + "X" + r, 0.5), (ell + "Y" + r, -0.5j)])
            c_list.append(cp)
    else:
        raise ValueError("Unsupported mapping.")
    d_list = [cp.adjoint() for cp in c_list]
    return c_list, d_list
```

이제 해밀턴을 정의하기 위해 이전 예제와 똑같이 PySCF를 사용하지만 이제는 원자간 거리 역할을 하는 변수 `x`를 포함합니다. 그러면 이전과 마찬가지로 핵심 에너지, 단일 전자 에너지, 2전자 에너지가 반환됩니다.

```python
def ham_terms(x: float):
    distance = x
    a = distance / 2
    mol = gto.Mole()
    mol.build(
        verbose=0,
        atom=[
            ["H", (0, 0, -a)],
            ["H", (0, 0, a)],
        ],
        basis="sto-6g",
        spin=0,
        charge=0,
        symmetry="Dooh",
    )

    # mf = scf.RHF(mol)
    # mx = mcscf.CASCI(mf, ncas=2, nelecas=(1, 1))
    # mx.kernel()

    mf = scf.RHF(mol)
    mf.kernel()
    if not mf.converged:
        raise RuntimeError(f"SCF did not converge for distance {x}")

    mx = mcscf.CASCI(mf, ncas=2, nelecas=(1, 1))
    casci_energy = mx.kernel()
    if casci_energy is None:
        raise RuntimeError(f"CASCI failed for distance {x}")

    # Other variables that might come in handy:
    # active_space = range(mol.nelectron // 2 - 1, mol.nelectron // 2 + 1)
    #    E1 = mf.kernel()
    # mo = mx.sort_mo(active_space, base=0)
    #    E2 = mx.kernel(mo)[:2]

    h1e, ecore = mx.get_h1eff()
    h2e = ao2mo.restore(1, mx.get_h2eff(), mx.ncas)
    return ecore, h1e, h2e
```

위에서 설명한 구성은 원자 종류, 기하학적 구조 및 전자 오비탈을 기반으로 페르미온 해밀턴ian을 생성하는 것입니다. 아래에서는 이 페르미온 해밀턴ian을 파울리 연산자로 매핑합니다. 이 `build_hamiltonian` 함수는 기하학적 변수를 인수로 포함합니다.

```python
def build_hamiltonian(distx: float) -> SparsePauliOp:
    ecore = ham_terms(distx)[0]
    h1e = ham_terms(distx)[1]
    h2e = ham_terms(distx)[2]

    ncas, _ = h1e.shape

    C, D = creators_destructors(2 * ncas, mapping="jordan_wigner")
    Exc = []
    for p in range(ncas):
        Excp = [C[p] @ D[p] + C[ncas + p] @ D[ncas + p]]
        for r in range(p + 1, ncas):
            Excp.append(
                C[p] @ D[r]
                + C[ncas + p] @ D[ncas + r]
                + C[r] @ D[p]
                + C[ncas + r] @ D[ncas + p]
            )
        Exc.append(Excp)

    # low-rank decomposition of the Hamiltonian
    Lop, ng = cholesky(h2e, 1e-6)
    t1e = h1e - 0.5 * np.einsum("pxxr->pr", h2e)

    H = ecore * identity(2 * ncas)
    # one-body term
    for p in range(ncas):
        for r in range(p, ncas):
            H += t1e[p, r] * Exc[p][r - p]
    # two-body term
    for g in range(ng):
        Lg = 0 * identity(2 * ncas)
        for p in range(ncas):
            for r in range(p, ncas):
                Lg += Lop[p, r, g] * Exc[p][r - p]
        H += 0.5 * Lg @ Lg

    return H.chop().simplify()
```

이제 efficient _su2 ansatz와 SciPy 최소화 도구와 같은 VQE 자체를 실행하는 데 필요한 나머지 패키지를 로드하겠습니다.

```python
# General imports

# Pre-defined ansatz circuit and operator class for Hamiltonian
from qiskit.circuit.library import efficient_su2
from qiskit.quantum_info import SparsePauliOp

# SciPy minimizer routine
from scipy.optimize import minimize

# Plotting functions

# Qiskit Runtime tools
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService()
```

우리는 다시 비용 함수를 정의하겠지만, 이 함수는 항상 완전히 구성되고 매핑된 해밀턴ian을 인수로 받았으므로 이 함수 자체에는 아무런 변화가 없습니다.

```python
def cost_func(params, ansatz, H, estimator):
    pub = (ansatz, [H], [params])
    result = estimator.run(pubs=[pub]).result()
    energy = result[0].data.evs[0]
    return energy


# def cost_func_sim(params, ansatz, H, estimator):
#    energy = estimator.run(ansatz, H, parameter_values=params).result().values[0]
#    return energy
```

## 2단계: 양자 실행을 위한 문제 최적화 

해밀토니안(Hamiltonian)은 새로운 기하학적 구조(geometry)마다 변하기 때문에, 연산자의 트랜스파일링(transpiling)도 각 단계마다 바뀔 것입니다. 그럼에도 불구하고 우리는 우리가 사용하고자 하는 하드웨어에 특화되어 각 단계에 적용될 일반적인 패스 매니저(pass manager)를 정의할 수 있습니다.

여기서는 이용 가능한 가장 덜 바쁜 백엔드(backend)를 사용할 것입니다. 우리는 그 백엔드를 AerSimulator의 모델로 사용하여, 예를 들어 시뮬레이터가 실제 백엔드의 노이즈 동작을 모방하도록 할 것입니다. 이러한 노이즈 모델이 완벽하지는 않지만, 실제 하드웨어에서 무엇을 기대할 수 있는지 아는 데 도움이 될 수 있습니다.

```python
# Here, we select the least busy backend available:
backend = service.least_busy(operational=True, simulator=False)
print(backend)
# Or to select a specific real backend use the line below, and substitute 'ibm_strasbourg'
# for your chosen device. backend = service.get_backend('ibm_strasbourg')
```

```python
# To run on a simulator:
# -----------
from qiskit_aer import AerSimulator

backend_sim = AerSimulator.from_backend(backend)
```

회로 최적화를 위해 패스 관리자와 관련 패키지를 가져옵니다. 이 단계와 바로 위의 단계는 Hamiltonian과는 무관하므로 이전 강의와 동일합니다.

```python
from qiskit.transpiler import PassManager
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager
from qiskit.transpiler.passes import (
    ALAPScheduleAnalysis,
    PadDynamicalDecoupling,
    ConstrainedReschedule,
)
from qiskit.circuit.library import XGate

target = backend.target
pm = generate_preset_pass_manager(target=target, optimization_level=3)
pm.scheduling = PassManager(
    [
        ALAPScheduleAnalysis(target=target),
        ConstrainedReschedule(
            acquire_alignment=target.acquire_alignment,
            pulse_alignment=target.pulse_alignment,
            target=target,
        ),
        PadDynamicalDecoupling(
            target=target,
            dd_sequence=[XGate(), XGate()],
            pulse_alignment=target.pulse_alignment,
        ),
    ]
)
```

## 3단계: Qiskit 프리미티브(primitives)를 사용하여 실행하기.

아래 코드 블록에서는 원자 간 거리 $x$의 각 단계별 출력을 저장하기 위한 배열을 설정합니다. 우리는 평형 결합 길이의 실험값인 0.74 옹스트롬(Angstrom)에 대한 지식을 바탕으로 $x$의 범위를 선택했습니다. 먼저 시뮬레이터에서 이를 실행할 것이므로, `qiskit.primitives`에서 Estimator(BackendEstimator)를 가져올 것입니다. 각 기하학적 구조(geometry) 단계마다 해밀토니안(Hamiltonian)을 구축하고, "cobyla" 옵티마이저를 사용하여 일정 횟수의 최적화 단계(여기서는 500회)를 허용합니다. 각 기하학적 구조 단계에서 총 에너지와 전자 에너지를 모두 저장합니다. 옵티마이저 단계 횟수가 많기 때문에 이 과정은 한 시간 이상 걸릴 수 있습니다. 필요한 시간을 줄이기 위해 아래의 입력값을 수정할 수 있습니다.


```python
from qiskit.primitives import BackendEstimatorV2

estimator = BackendEstimatorV2(backend=backend_sim)

distances_sim = np.arange(0.3, 1.3, 0.1)
vqe_energies_sim = []
vqe_elec_energies_sim = []

for dist in distances_sim:
    xx = dist

    # Random initial state and efficient_su2 ansatz
    H = build_hamiltonian(xx)
    ansatz = efficient_su2(H.num_qubits)
    ansatz_isa = pm.run(ansatz)
    x0 = 2 * np.pi * np.random.random(ansatz_isa.num_parameters)
    H_isa = H.apply_layout(ansatz_isa.layout)
    nuclear_repulsion = ham_terms(xx)[0]

    res = minimize(
        cost_func,
        x0,
        args=(ansatz_isa, H_isa, estimator),
        method="cobyla",
        options={"maxiter": 20, "disp": True},
    )

    # Note this returns the total energy, and we are often interested in the electronic energy
    tot_energy = getattr(res, "fun")
    electron_energy = getattr(res, "fun") - nuclear_repulsion
    print(electron_energy)
    vqe_energies_sim.append(tot_energy)
    vqe_elec_energies_sim.append(electron_energy)

    # Print all results
    print(res)

print("All energies have been calculated")
```

```
accuracy of Cholesky decomposition  1.1102230246251565e-15
/home/porter284/.pyenv/versions/3.11.12/lib/python3.11/site-packages/scipy/_lib/pyprima/common/preproc.py:68: UserWarning: COBYLA: Invalid MAXFUN; it should be at least num_vars + 2; it is set to 34
  warn(f'{solver}: Invalid MAXFUN; it should be at least {min_maxfun_str}; it is set to {maxfun}')
Return from COBYLA because the objective function has been evaluated MAXFUN times.
Number of function values = 34   Least value of F = 1.316011435623847
The corresponding X is:
[2.32948769 5.39918229 3.03787975 4.11789904 4.97130735 2.68662232
 1.76573151 2.48982571 5.40431972 3.65780829 1.33792786 5.48472494
 6.18738702 1.78741883 0.78195251 2.96658955 1.35827677 5.599321
 4.54850148 1.0939048  4.26158726 0.52100721 0.82318    4.76796961
 3.75795507 3.8526447  5.51100375 5.91023075 2.61494836 1.79908918
 2.65937756 5.53964148]

-0.44791260077615314
 message: Return from COBYLA because the objective function has been evaluated MAXFUN times.
 success: False
  status: 3
     fun: 1.316011435623847
       x: [ 2.329e+00  5.399e+00 ...  2.659e+00  5.540e+00]
    nfev: 34
   maxcv: 0.0
accuracy of Cholesky decomposition  5.551115123125783e-16
Return from COBYLA because the objective function has been evaluated MAXFUN times.
Number of function values = 34   Least value of F = 0.7235003672327549
The corresponding X is:
[2.56282915 5.63369524 5.58059887 4.049643   4.2021266  3.06866011
 6.01619635 1.52520776 4.35403161 0.33673958 0.32623161 1.2179545
 2.84001371 3.98956684 4.89632562 1.38303588 1.96194695 2.13182089
...
       x: [ 4.058e+00  3.991e+00 ...  5.378e+00  5.602e-01]
    nfev: 34
   maxcv: 0.0
All energies have been calculated
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
```

```python
xx
```

```
np.float64(1.2000000000000004)
```

이 출력 결과는 아래 후처리 섹션에서 자세히 설명합니다. 지금은 시뮬레이션이 성공적으로 완료되었다는 점만 기억해 두세요. 이제 실제 하드웨어에서 실행할 준비가 되었습니다. TREX 오류 완화 기능을 사용하기 위해 복원력을 '1'로 설정하겠습니다. 실제 하드웨어 환경에서는 Qiskit 런타임과 런타임 프리미티브를 사용합니다. 기하 계산과 관련된 for 루프와 여러 변분 시뮬레이션 모두 세션 내에 포함되어 있다는 점에 유의하세요.

실제 하드웨어 실행에는 비용과 시간 제약이 따르기 때문에 아래에서는 기하 계산 단계와 최적화 단계 수를 줄였습니다. 원하는 정밀도와 시간 제약에 따라 이러한 단계를 적절히 조정하세요.

```python
# To continue running on real hardware use
from qiskit_ibm_runtime import Session
from qiskit_ibm_runtime import EstimatorV2 as Estimator
from qiskit_ibm_runtime import EstimatorOptions

estimator_options = EstimatorOptions(resilience_level=1, default_shots=2000)

distances = np.arange(0.5, 0.9, 0.1)
vqe_energies = []
vqe_elec_energies = []

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)

    for dist in distances:
        xx = dist

        # Random initial state and efficient_su2 ansatz

        H = build_hamiltonian(xx)
        ansatz = efficient_su2(H.num_qubits)
        ansatz_isa = pm.run(ansatz)
        H_isa = H.apply_layout(ansatz_isa.layout)
        nuclear_repulsion = ham_terms(xx)[0]
        x0 = 2 * np.pi * np.random.random(ansatz_isa.num_parameters)

        res = minimize(
            cost_func,
            x0,
            args=(ansatz_isa, H_isa, estimator),
            method="cobyla",
            options={"maxiter": 50, "disp": True},
        )

        # Note this returns the total energy, and we are often interested in the electronic energy
        tot_energy = getattr(res, "fun")
        electron_energy = getattr(res, "fun") - nuclear_repulsion
        print(electron_energy)
        vqe_energies.append(tot_energy)
        vqe_elec_energies.append(electron_energy)

        # Print all results
        print(res)

print("All energies have been calculated")
```

## 4단계: 후처리

시뮬레이터와 실제 하드웨어 모두에서 각 원자 간 거리에 대해 계산된 바닥 상태 에너지를 그래프로 나타내고 가장 낮은 에너지가 달성되는 지점을 확인할 수 있습니다. 이 거리가 자연에서 발견되는 원자 간 거리와 일치해야 하며, 실제로도 매우 가깝습니다. 다른 근사값, 최적화 알고리즘을 시도하고, 각 기하 구조 단계에서 계산을 여러 번 실행하고 여러 개의 무작위 초기 조건에 대해 평균을 내면 더욱 매끄러운 곡선을 얻을 수 있습니다.

```python
# Here we can plot the results from this simulation.
plt.plot(distances_sim, vqe_energies_sim, label="VQE Energy")
plt.xlabel("Atomic distance (Angstrom)")
plt.ylabel("Energy")
plt.legend()
plt.show()
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fgeometry%2Fextracted-outputs%2Fe81f3ead-27ac-415e-a9f2-64a51d4b7aa3-0.avif&w=1200&q=75)

시뮬레이터에서는 최적화 단계 수를 단순히 늘리는 것만으로는 결과가 개선되지 않을 가능성이 높습니다. 실제 최적화는 최대 반복 횟수보다 적은 횟수 내에 요구되는 허용 오차에 수렴했기 때문입니다.

실제 하드웨어에서의 결과는 샘플링된 값의 범위가 약간 다른 것을 제외하면 시뮬레이터 결과와 유사합니다.

```python
plt.plot(distances, vqe_energies, label="VQE Energy")
plt.xlabel("Atomic distance (Angstrom)")
plt.ylabel("Energy")
plt.legend()
plt.show()
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fgeometry%2Fextracted-outputs%2Fde8f53cf-9547-4578-b6cb-20d2b5602ee0-0.avif&w=1200&q=75)

$H_2$ 결합 길이가 $0.74\text{ \AA}$으로 예상되는 것 외에도, 총 에너지는 $-1.17\text{ Hartrees}$가 되어야 합니다. 실제 하드웨어 결과가 시뮬레이터보다 이러한 값에 더 가깝게 나온 것을 볼 수 있습니다. 이는 두 경우 모두 노이즈가 존재했거나(또는 시뮬레이션되었거나) 오류 완화(error mitigation)는 실제 하드웨어의 경우에만 적용되었기 때문일 가능성이 높습니다.

## Closing

이것으로 양자 화학을 위한 VQE 과정을 마칩니다. 양자 컴퓨팅에 사용되는 일부 기초 정보 이론을 이해하는 데 관심이 있으시다면, John Watrous의 [Basics of Quantum Information](https://www.google.com/search?q=/learning/courses/basics-of-quantum-information) 과정을 확인해 보세요. VQE 워크플로우의 추가적인 단편 예시를 보시려면, [Ground-state energy estimation of the Heisenberg chain with VQE tutorial](https://www.google.com/search?q=/docs/tutorials/spin-chain-vqe)을 참조하시기 바랍니다. 또는 [tutorials](https://www.google.com/search?q=/docs/tutorials) 및 [courses](https://www.google.com/search?q=/learning)를 찾아보며 양자 컴퓨팅의 최신 기술에 대한 더 많은 교육 자료를 탐색해 보세요.

