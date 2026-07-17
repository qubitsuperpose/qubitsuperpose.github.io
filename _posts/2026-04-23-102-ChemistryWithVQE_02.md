---
title: 15차시 2:Quantum Chemistry with VQE 2
layout: single
classes: wide
categories:
  - Chemistry with VQE
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 2. Hamiltonians for Quantum Chemistry

VQE에서 해밀토니언이 수행하는 역할에 대한 간략한 개요부터 시작하겠습니다.

## 1. VQE 개요에서의 해밀토니언

빅토리아 리핀스카 박사가 해밀토니언과 이를 양자 컴퓨팅에서 사용하기 위해 매핑하는 방법을 안내합니다.

<iframe width="640" height="360" src="https://video.ibm.com/embed/recorded/132414924" scrolling="no" allowfullscreen webkitallowfullscreen frameborder="0" style="border: 0 none transparent;"></iframe>

### 참고 문헌

위 비디오에서 인용된 다음 문서들을 참조하십시오.

* [Quantum Algorithms for Fermionic Simulations, Ortiz, et al.](https://arxiv.org/pdf/cond-mat/0012334.pdf)
* [Simulating Chemistry using Quantum Computers, Kassal et al.](https://arxiv.org/pdf/1007.2648.pdf)
* [A Comparison of the Bravyi–Kitaev and Jordan–Wigner Transformations for the Quantum Simulation of Quantum Chemistry, Tranter, et al.](https://pubs.acs.org/doi/full/10.1021/acs.jctc.8b00450)
* [Quantum Chemistry in the Age of Quantum Computing, Cao, et al.](https://arxiv.org/pdf/1812.09976.pdf)
* [Quantum computational chemistry, McArdle, et al.](https://arxiv.org/pdf/1808.10402.pdf)
* [The Bravyi-Kitaev transformation for quantum computation of electronic structure, Seeley, et al., McArdle, et al.](https://arxiv.org/pdf/1812.09976.pdf)

## 2. 양자 화학을 위한 해밀토니언 준비

양자 컴퓨팅을 화학 문제에 적용하는 좋은 첫 번째 단계는 관심 계에 대한 해밀토니언을 정의하는 것입니다. 여기서는 논의를 양자 화학 해밀토니언으로 제한하겠습니다. 왜냐하면 그러한 해밀토니언은 동일한 페르미온 계에 특화된 일부 매핑을 필요로 하기 때문입니다.

양자 화학 분야에서 일하는 분이라면, 아마 분자 모델링을 위한 선호하는 소프트웨어가 이미 있을 것이며, 이를 통해 관심 계를 설명하는 해밀토니언을 생성할 수 있습니다. 여기서는 PySCF, numpy 및 Qiskit만을 기반으로 구축된 코드를 사용하겠습니다. 그러나 해밀토니언 준비 과정은 사전 패키지화된 솔루션에도 동일하게 적용됩니다. 이 접근 방식과 다른 소프트웨어 간의 유일한 차이점은 사소한 구문 차이입니다; 그중 일부는 기존 워크플로우의 통합을 용이하게 하기 위해 "타사 소프트웨어" 하위 섹션에서 다룹니다.

IBM Quantum® QPU에서 사용하기 위한 양자 화학 해밀토니언을 생성하는 과정은 다음 단계를 포함합니다:

1. 분자 정의 (기하 구조, 스핀, 활성 공간 등)
2. 페르미온 해밀토니언 생성 (생성 및 소멸 연산자)
3. 페르미온 해밀토니언에서 보손 연산자로의 매핑 (이 맥락에서는 파울리 연산자 사용)
4. 타사 소프트웨어 사용 시: 생성 소프트웨어와 Qiskit 간의 구문 불일치 처리

페르미온 해밀토니언은 페르미온 연산자로 표현되며, 특히 전자가 구별 불가능한 페르미온이라는 점을 고려합니다. 이는 구별 가능한 보손인 큐비트와 완전히 다른 통계를 따른다는 것을 의미합니다. 따라서 매핑 과정이 필요합니다.

이러한 과정에 이미 익숙한 분들은 이 섹션을 건너뛰어도 될 것입니다.

목표:

최종 목표는 다음과 같은 형태의 해밀턴ian을 얻는 것입니다.

```python
H = [(1, "XX"), (1, "YY"), (1, "ZZ")]
print(H)
```

```
[(1, 'XX'), (1, 'YY'), (1, 'ZZ')]
```

혹은  

```python
from qiskit.quantum_info import SparsePauliOp

H = SparsePauliOp(["XX", "YY", "ZZ"], coeffs=[1.0 + 0.0j, 1.0 + 0.0j, 1.0 + 0.0j])
print(H)
```

```
SparsePauliOp(['XX', 'YY', 'ZZ'],
              coeffs=[1.+0.j, 1.+0.j, 1.+0.j])
```

먼저 몇 가지 패키지를 가져오겠습니다.

```python
import numpy as np
from pyscf import ao2mo, gto, mcscf, scf
```

### 2.1 분자 정의

여기에서는 관심 분자의 속성을 지정합니다. 이 예에서는 이원자 수소를 선택했습니다(결과로 나온 해밀턴이 표시할 수 있을 만큼 짧기 때문).

Python 기반 화학 시뮬레이션 프레임워크([PySCF](https://pyscf.org/))는 양자 계산에 적합한 분자 해밀토니안을 생성하는 것을 포함하여 다양한 용도로 사용할 수 있는 전자 구조 모듈을 폭넓게 제공합니다. [PySCF Quickstart](https://pyscf.org/quickstart.html) 가이드는 모든 변수와 기능에 대한 전체 설명을 제공하는 훌륭한 자료입니다. 우리는 여러분 중 많은 분들이 이미 익숙할 것이므로, 가장 간략한 개요만 제공하겠습니다. 더 자세히 이해하려면 [PySCF](https://pyscf.org/)를 방문하십시오.

간략히:

**distance**는 이원자 분자에 사용할 수 있으며, 또는 각 원자에 대한 직교 좌표를 간단히 지정할 수 있습니다. 거리의 단위는 옹스트롬입니다.

**gto**는 가우스형 오비탈을 생성합니다.

**basis**는 분자 오비탈을 모델링하는 데 사용되는 함수들을 나타냅니다. 여기서 'sto-6g'는 6개의 원시 가우스 오비탈을 사용하여 슬레이터형 오비탈을 근사화한 것에서 이름을 따온, 흔한 최소 기저 집합입니다.

**spin**은 짝지어지지 않은 전자의 수를 나타내는 정수 값입니다($2S$와 같음). 일부 소프트웨어는 대신 다중도($2S+1$)를 사용한다는 점에 유의하십시오.

**charge**는 분자의 전하입니다.

**symmetry** - 분자의 점대칭군으로, 문자열로 지정하거나 "symmetry = True"로 설정하여 자동으로 감지할 수 있습니다. 여기서 "Dooh"는 동일한 원자 종의 두 원자로 이루어진 이원자 분자에 적합한 대칭군입니다.

```python
distance = 0.735
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
```

```
<pyscf.gto.mole.Mole at 0x7fc718f07610>
```

다음 사항을 유의하라: 총 에너지(핵 반발 에너지뿐만 아니라 전자 에너지도 포함) 또는 총 전자 궤도 에너지, 또는 일부 전자 궤도 부분집합의 에너지(보완적 부분집합은 고정됨)를 설명할 수 있다. $\text{H}_2$의 특정 경우에, 아래의 서로 다른 에너지들을 주목하고, 총 에너지에서 핵 반발 에너지를 뺀 것이 실제로 전자 에너지를 산출한다는 점을 주목하라:

```python
mf = scf.RHF(mol)
mf.scf()

print(
    mf.energy_nuc(),
    mf.energy_elec()[0],
    mf.energy_tot(),
    mf.energy_tot() - mol.energy_nuc(),
)
```

```
0.7199689944489797 -1.8455976628764188 -1.125628668427439 -1.8455976628764188
```

```python
active_space = range(mol.nelectron // 2 - 1, mol.nelectron // 2 + 1)
```

### 2.2 Generate fermionic Hamiltonian

**scf**는 다양한 self-consistent field 방법들을 가리킵니다.

**rhf**는 **mf** = scf.RHF(mol)에서와 같이 **mf**가 Restricted Hartree Fock 계산을 사용하는 solver입니다. 이 (아래 E)의 kernel은 핵반발력과 molecular orbitals를 포함한 총 에너지입니다.

**mcscf**는 multi-configuration self-consistent fields 패키지입니다.

**ao2mo**는 atomic orbitals에서 molecular orbitals로의 변환입니다.

또한 다음 변수를 사용합니다:

**ncas**: complete active space 내의 orbital 수

**nelecas**: complete active space 내의 electron 수

```python
E1 = mf.kernel()
mx = mcscf.CASCI(mf, ncas=2, nelecas=(1, 1))
mo = mx.sort_mo(active_space, base=0)
E2 = mx.kernel(mo)[:2]
```

우리는 해밀토니안을 원하며, 이는 종종 전자 핵의 에너지(ecore, 최소화 과정에는 관여하지 않음), 단일 전자 연산자(h1e), 그리고 이중 전자 에너지(h2e)로 분리됩니다. 이들은 아래 마지막 두 줄에서 명시적으로 추출됩니다.

```python
h1e, ecore = mx.get_h1eff()
h2e = ao2mo.restore(1, mx.get_h2eff(), mx.ncas)
```

이 해밀토니안들은 현재 페르미온 (생성 및 소멸) 연산자이며, (구별 불가능한) 페르미온 시스템에 적용되며, 이에 따라 교환 하에서의 반대칭성을 만족합니다. 이는 구별 가능한 시스템이나 보손 시스템에 적용되는 것과 다른 통계를 초래합니다. IBM Quantum QPU에서 계산을 수행하기 위해, 우리는 에너지를 기술하는 보손 연산자가 필요합니다. 이러한 매핑의 결과는 일반적으로 파울리 연산자로 표현되는데, 이는 에르미트이면서 동시에 유니터리하기 때문입니다. 사용할 수 있는 여러 매핑이 있습니다. 가장 간단한 것 중 하나는 조던-위그너 변환입니다.

### 2.3 해밀토니안 매핑

화학적 해밀토니안을 양자 컴퓨터에서 실행하기에 적합한 형태로 매핑하는 데 사용할 수 있는 많은 도구들이 있다는 점에 유의해야 합니다. 여기서는 PySCF, numpy, 그리고 Qiskit만을 사용하여 조던-위그너 매핑을 직접 구현합니다. 다른 솔루션에 대한 문법 고려사항은 아래에서 언급합니다.

Cholesky 함수는 Hamiltonian의 이전자 항들에 대한 저랭크 분해를 얻는 데 도움을 줍니다.

```python
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
```

함수 `identity`와 `creators_destructors`는 fermionic Hamiltonian의 creation 및 annihilation operators를 Pauli operators로 대체합니다; `creators_destructors`는 Jordan-Wigner mapping을 사용합니다.

```python
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

마지막으로, `build_hamiltonian`은 양자 컴퓨터에서 실행할 수 있는 최종 Hamiltonian을 생성하기 위해 `cholesky`, `identity`, `creators_destructors` 함수들을 사용한다.

```python
def build_hamiltonian(ecore: float, h1e: np.ndarray, h2e: np.ndarray) -> SparsePauliOp:
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

마지막으로, 우리는 Jordan-Wigner 변환을 사용하여 Pauli 연산자로부터 큐빗 해밀토니안을 구성하기 위해 `build_hamiltonian`을 사용합니다. 이것은 또한 우리가 사용한 촐레스키 분해의 정확도를 제공합니다.

```python
H = build_hamiltonian(ecore, h1e, h2e)
print(H)
```

```
accuracy of Cholesky decomposition  2.220446049250313e-16
SparsePauliOp(['IIII', 'IIIZ', 'IZII', 'IIZI', 'ZIII', 'IZIZ', 'IIZZ', 'ZIIZ', 'IZZI', 'ZZII', 'ZIZI', 'YYYY', 'XXYY', 'YYXX', 'XXXX'],
              coeffs=[-0.09820182+0.j, -0.1740751 +0.j, -0.1740751 +0.j,  0.2242933 +0.j,
  0.2242933 +0.j,  0.16891402+0.j,  0.1210099 +0.j,  0.16631441+0.j,
  0.16631441+0.j,  0.1210099 +0.j,  0.17504456+0.j,  0.04530451+0.j,
  0.04530451+0.j,  0.04530451+0.j,  0.04530451+0.j])
```

다음은 원문에 충실하게 번역한 한국어 문장입니다:

이 [예시 분자 노트북](https://ibm.ent.box.com/public/static/59c2kjqefvjy5wy5lct099thr4y0ry8c.zip)은 복잡도가 다양한 여러 분자들의 설정과 해밀토니안을 보여준다; 약간의 수정을 거치면, 이를 통해 대부분의 작은 분자들을 검토할 수 있을 것이다.

분자의 페르미온 연산자를 구성할 때 고려해야 할 두 가지 중요한 점을 간략히 언급하겠다. 분자의 종류가 바뀌면 대칭성도 바뀐다. 마찬가지로, 원통 대칭인 "A1"과 같은 다양한 대칭성을 가진 궤도의 수도 바뀐다. 이러한 변화는 여기에서 보듯이, LiH로의 간단한 확장에서도 명확하게 드러난다:

```python
distance = 1.56
mol = gto.Mole()
mol.build(
    verbose=0,
    atom=[["Li", (0, 0, 0)], ["H", (0, 0, distance)]],
    basis="sto-6g",
    spin=0,
    charge=0,
    symmetry="Coov",
)
mf = scf.RHF(mol)
E1 = mf.kernel()

# %% ----------------------------------------------------------------------------------------------

mx = mcscf.CASCI(mf, ncas=5, nelecas=(1, 1))
cas_space_symmetry = {"A1": 3, "E1x": 1, "E1y": 1}
mo = mcscf.sort_mo_by_irrep(mx, mf.mo_coeff, cas_space_symmetry)
E2 = mx.kernel(mo)[:2]
h1e, ecore = mx.get_h1eff()
h2e = ao2mo.restore(1, mx.get_h2eff(), mx.ncas)
```

또한 최종적으로 얻어지는 해밀토니안에 대한 직관을 쉽게 잃을 수 있다는 점도 주목할 만합니다. LiH의 해밀토니안(조던-위그너 매퍼 사용)은 이미 276개의 항으로 구성되어 있습니다.

```python
len(build_hamiltonian(ecore, h1e, h2e))
```

```
accuracy of Cholesky decomposition  1.1102230246251565e-16
```

```
276
```

대칭성에 대해 의심이 들 때는, `symmetry = True`와 `verbose = 4`를 설정하여 분자의 대칭성 정보를 생성할 수도 있습니다.

```python
distance = 1.56
mol = gto.Mole()
mol.build(
    verbose=4,
    atom=[["Li", (0, 0, 0)], ["H", (0, 0, distance)]],
    basis="sto-6g",
    spin=0,
    charge=0,
    symmetry=True,
)
```

```
System: uname_result(system='Linux', node='IBM-R912JTRT', release='5.10.102.1-microsoft-standard-WSL2', version='#1 SMP Wed Mar 2 00:30:59 UTC 2022', machine='x86_64')  Threads 16
Python 3.11.12 (main, May 16 2025, 02:33:32) [GCC 11.4.0]
numpy 2.3.1  scipy 1.16.0  h5py 3.14.0
Date: Mon Jun 30 12:56:55 2025
PySCF version 2.9.0
PySCF path  /home/porter284/.pyenv/versions/3.11.12/lib/python3.11/site-packages/pyscf

[CONFIG] conf_file None
[INPUT] verbose = 4
[INPUT] num. atoms = 2
[INPUT] num. electrons = 4
[INPUT] charge = 0
[INPUT] spin (= nelec alpha-beta = 2S) = 0
[INPUT] symmetry True subgroup None
[INPUT] Mole.unit = angstrom
[INPUT] Symbol           X                Y                Z      unit          X                Y                Z       unit  Magmom
[INPUT]  1 Li     0.000000000000   0.000000000000   0.000000000000 AA    0.000000000000   0.000000000000   0.000000000000 Bohr   0.0
[INPUT]  2 H      0.000000000000   0.000000000000   1.560000000000 AA    0.000000000000   0.000000000000   2.947972754321 Bohr   0.0

nuclear repulsion = 1.01764848253846
point group symmetry = Coov
symmetry origin: [0.         0.         0.73699319]
symmetry axis x: [1. 0. 0.]
symmetry axis y: [0. 1. 0.]
symmetry axis z: [0. 0. 1.]
...
number of NR cGTOs = 6
basis = sto-6g
ecp = {}
CPU time:         9.85
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
```

 다른 유용한 정보 외에도, 이것은 `point group symmetry = Coov`와 각 불가환 표현에 있는 오비탈의 수를 모두 반환합니다.

```
point group symmetry = Coov
num. orbitals of irrep A1 = 4
num. orbitals of irrep E1x = 1
num. orbitals of irrep E1y = 1
number of shells = 4
```

이것이 반드시 활성 공간에 포함하고자 하는 오비탈의 수를 알려주는 것은 아니지만, 어떤 오비탈이 존재하는지와 그것들의 대칭성을 파악하는 데 도움이 됩니다.

대칭성과 오비탈을 지정하는 것이 종종 도움이 되지만, 포함하고자 하는 오비탈의 수를 지정할 수도 있습니다. 아래의 에텐(ethene) 경우를 고려해 보십시오. `verbose = 4`를 사용하면 다양한 오비탈의 대칭성을 출력할 수 있습니다:

```python
# Replace these variables with correct distances:
a = 1
b = 1
c = 1

# Build
mol = gto.Mole()
mol.build(
    verbose=4,
    atom=[
        ["C", (0, 0, a)],
        ["C", (0, 0, -a)],
        ["H", (0, c, b)],
        ["H", (0, -c, b)],
        ["H", (0, c, -b)],
        ["H", (0, -c, -b)],
    ],
    basis="sto-6g",
    spin=0,
    charge=0,
    symmetry=True,
)
```

```
System: uname_result(system='Linux', node='IBM-R912JTRT', release='5.10.102.1-microsoft-standard-WSL2', version='#1 SMP Wed Mar 2 00:30:59 UTC 2022', machine='x86_64')  Threads 16
Python 3.11.12 (main, May 16 2025, 02:33:32) [GCC 11.4.0]
numpy 2.3.1  scipy 1.16.0  h5py 3.14.0
Date: Mon Jun 30 12:57:07 2025
PySCF version 2.9.0
PySCF path  /home/porter284/.pyenv/versions/3.11.12/lib/python3.11/site-packages/pyscf

[CONFIG] conf_file None
[INPUT] verbose = 4
[INPUT] num. atoms = 6
[INPUT] num. electrons = 16
[INPUT] charge = 0
[INPUT] spin (= nelec alpha-beta = 2S) = 0
[INPUT] symmetry True subgroup None
[INPUT] Mole.unit = angstrom
[INPUT] Symbol           X                Y                Z      unit          X                Y                Z       unit  Magmom
[INPUT]  1 C      0.000000000000   0.000000000000   1.000000000000 AA    0.000000000000   0.000000000000   1.889726124565 Bohr   0.0
[INPUT]  2 C      0.000000000000   0.000000000000  -1.000000000000 AA    0.000000000000   0.000000000000  -1.889726124565 Bohr   0.0
[INPUT]  3 H      0.000000000000   1.000000000000   1.000000000000 AA    0.000000000000   1.889726124565   1.889726124565 Bohr   0.0
[INPUT]  4 H      0.000000000000  -1.000000000000   1.000000000000 AA    0.000000000000  -1.889726124565   1.889726124565 Bohr   0.0
[INPUT]  5 H      0.000000000000   1.000000000000  -1.000000000000 AA    0.000000000000   1.889726124565  -1.889726124565 Bohr   0.0
[INPUT]  6 H      0.000000000000  -1.000000000000  -1.000000000000 AA    0.000000000000  -1.889726124565  -1.889726124565 Bohr   0.0

nuclear repulsion = 29.3377079104231
point group symmetry = D2h
...
number of NR cGTOs = 14
basis = sto-6g
ecp = {}
CPU time:         9.92
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
```


우리는 다음을 얻는다:

num. orbitals of irrep Ag = 4

num. orbitals of irrep B2g = 2

num. orbitals of irrep B3g = 1

num. orbitals of irrep B1u = 4

num. orbitals of irrep B2u = 1

num. orbitals of irrep B3u = 2

그러나 모든 궤도함수를 대칭성별로 지정하는 대신, 우리는 단순히 다음과 같이 쓸 수 있다:

```python
active_space = range(mol.nelectron // 2 - 2, mol.nelectron // 2 + 2)
```

이 접근 방식에서는 원자가 전자 궤도와 비점유 궤도를 포함하여 전자 채움 수준 근처의 여러 궤도를 고려합니다. 여기서는 활성 공간에 포함할 5개의 궤도(6번째부터 10번째 궤도)를 선택했습니다.

```python
print(
    mol.nelectron // 2 - 2,
    mol.nelectron // 2 + 2,
)
```

```
6 10
```

### 2.4 타사 소프트웨어

양자화학을 위해 개발된 여러 소프트웨어 패키지가 있으며, 일부는 여러 매퍼와 활성 공간을 제한하는 도구를 제공합니다. 위에서 설명한 단계는 일반적인 것이며 타사 소프트웨어에도 적용됩니다. 그러나 이 다른 소프트웨어는 Qiskit에서 허용하지 않는 형식으로 해밀토니안을 반환할 수 있습니다. 예를 들어, 일부 소프트웨어는 다음과 같은 형식의 해밀토니안을 반환합니다:

```
H = -0.042 [] + -0.045 [X0 X1 Y2 Y3] + ... + 0.178 [Z0] + ... + 0.176 [Z2 Z3] + -0.243 [Z3]
```


특히 게이트가 번호가 매겨져 있고, 항등 연산자는 표시되지 않는다는 점에 유의하십시오. 이는 Qiskit에서 사용되는 해밀토니안과 대조되는데, Qiskit에서는 항 $[Z_2 Z_3]$을 $ZZII$로 표기합니다 (큐비트 0과 1은 항등 연산자가 작용하고, 큐비트 2와 3은 Z 연산자가 작용하며, 큐비트 0이 가장 오른쪽에 오도록 정렬됨).

기존에 진행 중인 작업 흐름을 고려하여, 아래 코드 블록은 한 구문에서 다른 구문으로 변환하는 방법을 보여줍니다. `convert_openfermion_to_qiskit` 함수는 OpenFermion 또는 Tangelo에서 생성된 해밀토니안(이미 사용 가능한 매퍼를 이용해 파울리 연산자로 매핑된 상태)과 해당 분자에 필요한 큐비트 수를 인자로 받습니다.

```python
from openfermion import QubitOperator
from qiskit.quantum_info import SparsePauliOp


def convert_openfermion_to_qiskit(
    openfermion_operator: QubitOperator, num_qubits: int
) -> SparsePauliOp:
    terms = openfermion_operator.terms

    labels = []
    coefficients = []

    for term, constant in terms.items():
        # Default set to identity
        operator = list("I" * num_qubits)

        # Iterate through PauliSum and replace I with Pauli
        for index, pauli in term:
            operator[index] = pauli
        label = "".join(operator)
        labels.append(label)
        coefficients.append(constant)

    return SparsePauliOp(labels, coefficients)
```


추가로, [이 Python 노트북](https://ibm.ent.box.com/s/fh3xele1e7k0nrgd1imivvq52hy3wz9c)에는 다른 소프트웨어 워크플로우에서 Qiskit으로 해밀토니안을 마이그레이션하는 완전한 샘플 코드가 포함되어 있으며, 위의 변환도 포함됩니다.

이제 IBM® 양자 컴퓨터에서 양자 화학 계산을 수행하는 데 필요한 해밀토니안을 얻기 위한 도구를 갖추게 되었을 것입니다.