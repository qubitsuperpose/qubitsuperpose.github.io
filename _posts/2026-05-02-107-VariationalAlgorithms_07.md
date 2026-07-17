---
title: 16차시 7:Variational Algorithms 7
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 7. 예시 및 응용

이 강의에서는 몇 가지 변형 알고리즘 예제와 이를 적용하는 방법을 살펴보겠습니다.

* 맞춤형 변형 알고리즘을 작성하는 방법
* 최소 고유값을 찾기 위해 변이 알고리즘을 적용하는 방법
* 애플리케이션 사용 사례를 해결하기 위해 변형 알고리즘을 활용하는 방법

키스킷 패턴 프레임워크는 여기서 소개하는 모든 문제에 적용될 수 있습니다. 그러나 반복을 피하기 위해 실제 하드웨어에서 실행되는 한 가지 예시 사례에서만 프레임워크 단계를 명시적으로 호출하겠습니다.

## 1. 문제 정의

변분 알고리즘을 사용하여 다음 관측가능량의 고유값을 찾고자 한다고 가정하자:

<p>
$$
\hat{O}_1 = 2 II - 2 XX + 3 YY - 3 ZZ,
$$
</p>

이 관측가능량은 다음과 같은 고유값을 가진다:

<p>
$$
\left\{
\begin{array}{c}
\lambda_0 = -6 \\
\lambda_1 = 4 \\
\lambda_2 = 4 \\
\lambda_3 = 6
\end{array}
\right\}
$$
</p>

그리고 고유상태는 다음과 같다:
<p>
$$
\left\{
\begin{array}{c}
|\phi_0\rangle = \frac{1}{\sqrt{2}}(|00\rangle + |11\rangle)\\
|\phi_1\rangle = \frac{1}{\sqrt{2}}(|00\rangle - |11\rangle)\\
|\phi_2\rangle = \frac{1}{\sqrt{2}}(|01\rangle - |10\rangle)\\
|\phi_3\rangle = \frac{1}{\sqrt{2}}(|01\rangle + |10\rangle)
\end{array}
\right\}
$$
</p>

```python
from qiskit.quantum_info import SparsePauliOp

observable_1 = SparsePauliOp.from_list([("II", 2), ("XX", -2), ("YY", 3), ("ZZ", -3)])
```


## 2. 사용자 정의 VQE

먼저 $\hat{O}_1$의 가장 작은 고유값을 찾기 위해 VQE 인스턴스를 수동으로 구성하는 방법을 살펴보겠습니다. 이 과정에서는 이 강좌 전반에 걸쳐 다룬 다양한 기법들을 활용할 것입니다.

```python
def cost_func_vqe(params, ansatz, hamiltonian, estimator):
    """Return estimate of energy from estimator

    Parameters:
        params (ndarray): Array of ansatz parameters
        ansatz (QuantumCircuit): Parameterized ansatz circuit
        hamiltonian (SparsePauliOp): Operator representation of Hamiltonian
        estimator (Estimator): Estimator primitive instance

    Returns:
        float: Energy estimate
    """
    pub = (ansatz, hamiltonian, params)
    cost = estimator.run([pub]).result()[0].data.evs

    return cost
```

```python
from qiskit.circuit.library.n_local import n_local
from qiskit import QuantumCircuit

import numpy as np

reference_circuit = QuantumCircuit(2)
reference_circuit.x(0)

variational_form = n_local(
    num_qubits=2,
    rotation_blocks=["rz", "ry"],
    entanglement_blocks="cx",
    entanglement="linear",
    reps=1,
)

raw_ansatz = reference_circuit.compose(variational_form)
raw_ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F57c071aa-e8ab-4b39-b5db-773016da2550-0.avif&w=1080&q=75)

로컬 시뮬레이터에서 디버깅을 시작하겠습니다.

```python
from qiskit.primitives import StatevectorEstimator as Estimator
from qiskit.primitives import StatevectorSampler as Sampler

estimator = Estimator()
sampler = Sampler()
```

이제 초기 매개변수 세트를 설정합니다.

```python
x0 = np.ones(raw_ansatz.num_parameters)
print(x0)
```

```
[1. 1. 1. 1. 1. 1. 1. 1.]
```

우리는 이 비용 함수를 최소화하여 최적의 매개변수를 계산할 수 있습니다.

```python
# SciPy minimizer routine
from scipy.optimize import minimize
import time

start_time = time.time()

result = minimize(
    cost_func_vqe,
    x0,
    args=(raw_ansatz, observable_1, estimator),
    method="COBYLA",
    options={"maxiter": 1000, "disp": True},
)

end_time = time.time()
execution_time = end_time - start_time
```

```
Return from COBYLA because the trust region radius reaches its lower bound.
Number of function values = 103   Least value of F = -5.999999998357189
The corresponding X is:
[2.27483579e+00 8.37593091e-01 1.57080508e+00 5.82932911e-06
 2.49973063e+00 6.41884255e-01 6.33686904e-01 6.33688223e-01]
```

```python
result
```

```
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: -5.999999998357189
       x: [ 2.275e+00  8.376e-01  1.571e+00  5.829e-06  2.500e+00
            6.419e-01  6.337e-01  6.337e-01]
    nfev: 103
   maxcv: 0.0
```

이 간단한 문제는 큐비트가 두 개만 사용되므로 NumPy의 선형 대수 고유값 해결기를 사용하여 이를 확인할 수 있습니다.

```python
from numpy.linalg import eigvalsh

solution_eigenvalue = min(eigvalsh(observable_1.to_matrix()))

print(f"""Number of iterations: {result.nfev}""")
print(f"""Time (s): {execution_time}""")

print(
    f"Percent error: {100*abs((result.fun - solution_eigenvalue)/solution_eigenvalue):.2e}"
)
```

```
Number of iterations: 103
Time (s): 0.4394676685333252
Percent error: 2.74e-08
```

보시다시피, 결과는 이상적인 결과에 매우 가깝습니다.


## 3. 속도와 정확도 향상을 위한 실험

### 3.1 참조 상태 추가

이전 예제에서는 참조 연산자 $U_R$을 사용하지 않았습니다. 이제 이상적인 고유 상태 $\frac{1}{\sqrt{2}}(\|00\rangle + \|11\rangle)$을 어떻게 얻을 수 있는지 생각해 보겠습니다. 다음 회로를 고려해 보세요.

```python
from qiskit import QuantumCircuit

ideal_qc = QuantumCircuit(2)
ideal_qc.h(0)
ideal_qc.cx(0, 1)

ideal_qc.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F55b26ae7-ad26-4b54-b4f3-2004bb2db0c9-0.avif&w=640&q=75)

이 회로가 우리가 원하는 상태를 제공하는지 빠르게 확인할 수 있습니다.

```python
from qiskit.quantum_info import Statevector

Statevector(ideal_qc)
```

```
Statevector([0.70710678+0.j, 0.        +0.j, 0.        +0.j,
             0.70710678+0.j],
            dims=(2, 2))
```

이제 해법 상태를 준비하는 회로가 어떻게 생겼는지 살펴보았으므로, 하드마르 게이트를 기준 회로로 사용하는 것이 합리적일 것 같습니다. 따라서 전체 근사식은 다음과 같습니다.

```python
reference = QuantumCircuit(2)
reference.h(0)
reference.cx(0, 1)
# Include barrier to separate reference from variational form
reference.barrier()

ref_ansatz = variational_form.decompose().compose(reference, front=True)

ref_ansatz.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F14e04c7a-e81f-41e1-b0cf-790954581be9-0.avif&w=1920&q=75)

이 새로운 회로의 경우 모든 매개변수를 0으로 설정했을 때 최적의 해를 얻을 수 있었으므로, 기준 회로 선택이 적절했음을 확인할 수 있습니다.

이제 비용 함수 평가 횟수, 최적화 반복 횟수 및 소요 시간을 이전 시도와 비교해 보겠습니다.

```python
import time

start_time = time.time()

ref_result = minimize(
    cost_func_vqe, x0, args=(ref_ansatz, observable_1, estimator), method="COBYLA"
)

end_time = time.time()
execution_time = end_time - start_time
```

최적 매개변수를 사용하여 최소 고유값을 계산합니다.

```python
experimental_min_eigenvalue_ref = cost_func_vqe(
    ref_result.x, ref_ansatz, observable_1, estimator
)
print(experimental_min_eigenvalue_ref)
```

-5.999999996759607


```python
print("ADDED REFERENCE STATE:")
print(f"""Number of iterations: {ref_result.nfev}""")
print(f"""Time (s): {execution_time}""")
print(
    f"Percent error: {100*abs((experimental_min_eigenvalue_ref - solution_eigenvalue)/solution_eigenvalue):.2e}"
)
```

```
ADDED REFERENCE STATE:
Number of iterations: 127
Time (s): 0.5620882511138916
Percent error: 5.40e-08
```

특정 시스템에 따라 이 아주 작은 규모의 예에서는 속도나 정확도가 향상될 수도 있고 그렇지 않을 수도 있습니다. 요점은 문제 규모가 커짐에 따라 속도와 정확성을 향상시키는 데 신체적으로 동기를 부여받은 참조 상태로 시작하는 것이 점점 더 중요해진다는 것입니다.

### 3.2 초기 지점을 변경

이제 참조 상태 추가의 효과를 살펴보았으므로 다른 초기점 $\vec{\theta_0}$을 선택할 때 어떤 일이 발생하는지 살펴보겠습니다. 특히 $\vec{\theta_0}=(0,0,0,0,6,0,0,0)$ 및 $\vec{\theta_0}=(6,6,6,6,6,6,6,6,6)$을 사용합니다.

참조 상태가 도입되었을 때 논의한 것처럼 모든 매개변수가 $0$일 때 이상적인 솔루션을 찾을 수 있으므로 첫 번째 초기 지점에서는 더 적은 평가를 제공해야 합니다.

```python
import time

start_time = time.time()

x0 = [0, 0, 0, 0, 6, 0, 0, 0]

x0_1_result = minimize(
    cost_func_vqe, x0, args=(raw_ansatz, observable_1, estimator), method="COBYLA"
)

end_time = time.time()
execution_time = end_time - start_time
```

```python
print("INITIAL POINT 1:")
print(f"""Number of iterations: {x0_1_result.nfev}""")
print(f"""Time (s): {execution_time}""")
```

```
INITIAL POINT 1:
Number of iterations: 108
Time (s): 0.4492197036743164
```

초기 지점을 $\vec{\theta_0}=(6,6,6,6,6,6,6,6,6,6)$로 조정합니다.

```python
import time

start_time = time.time()

x0 = 6 * np.ones(raw_ansatz.num_parameters)

x0_2_result = minimize(
    cost_func_vqe, x0, args=(raw_ansatz, observable_1, estimator), method="COBYLA"
)

end_time = time.time()
execution_time = end_time - start_time
```

```python
print("INITIAL POINT 2:")
print(f"""Number of iterations: {x0_2_result.nfev}""")
print(f"""Time (s): {execution_time}""")
```

```
INITIAL POINT 2:
Number of iterations: 107
Time (s): 0.40889453887939453
```

다양한 초기값을 사용하여 실험해 보면 더 빠른 수렴과 더 적은 함수 평가 횟수를 달성할 수 있을 것입니다.



### 3.3 다양한 최적화 알고리즘 실험

SciPy의 `minimize` 함수의 `method` 인수를 사용하여 최적화 알고리즘을 조정할 수 있으며, 더 많은 옵션은 [여기](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html)에서 확인할 수 있습니다. 원래는 제약 조건이 있는 최소화 알고리즘(`COBYLA`)을 사용했지만, 이 예제에서는 제약 조건이 없는 최소화 알고리즘(`BFGS`)을 사용하는 방법을 살펴보겠습니다.

```python
import time

start_time = time.time()

result = minimize(
    cost_func_vqe, x0, args=(raw_ansatz, observable_1, estimator), method="BFGS"
)

end_time = time.time()
execution_time = end_time - start_time
```

```python
print("CHANGED TO BFGS OPTIMIZER:")
print(f"""Number of iterations: {result.nfev}""")
print(f"""Time (s): {execution_time}""")
```

```
CHANGED TO BFGS OPTIMIZER:
Number of iterations: 117
Time (s): 0.31656408309936523
```


## 4. VQD 예시

여기서는 Qiskit 패턴 프레임워크를 명시적으로 구현합니다.

### 4.1 1단계: 고전적 입력을 양자 문제에 매핑하기

이제 관측가능량의 최저 고유값만 찾는 대신, 총 4개를 찾을 것이다(여기서 $k=4$).

VQD의 비용 함수는 다음과 같다:
<p>
$$
C_{l}(\vec{\theta}) :=
\langle \psi(\vec{\theta}) | \hat{H} | \psi(\vec{\theta})\rangle +
\sum_{j=0}^{l-1}\beta_j |\langle \psi(\vec{\theta})| \psi(\vec{\theta^j})\rangle  |^2
\quad \forall l\in\{1,\cdots,k\}=\{1,\cdots,4\}
$$
</p>

이는 특히 중요한데, 벡터 $\vec{\beta}=(\beta_0,\cdots,\beta_{k-1})$ (이 경우 $(\beta_0, \beta_1, \beta_2, \beta_3)$)가 `VQD` 객체를 정의할 때 인자로 전달되어야 하기 때문이다.

또한 Qiskit의 VQD 구현에서는 이전 노트북에서 설명한 유효 관측가능량을 고려하는 대신, `ComputeUncompute` 알고리즘을 통해 $\|\langle \psi(\vec{\theta})\| \psi(\vec{\theta^j})\rangle \|^2$ 충실도를 직접 계산하는데, 이는 `Sampler` 원시 연산(primitive)을 활용하여 회로 $U_A^\dagger(\vec{\theta})U_A(\vec{\theta^j})$에 대해 $\|0\rangle$을 얻을 확률을 샘플링한다. 이것이 정확히 작동하는 이유는 이 확률이

<p>
$$
\begin{aligned}
p_0
& = |\langle 0|U_A^\dagger(\vec{\theta})U_A(\vec{\theta^j})|0\rangle|^2 \\[1mm]
& = |\big(\langle 0|U_A^\dagger(\vec{\theta})\big)\big(U_A(\vec{\theta^j})|0\rangle\big)|^2 \\[1mm]
& = |\langle \psi(\vec{\theta}) |\psi(\vec{\theta^j}) \rangle|^2 \\[1mm]
\end{aligned}
$$
</p>

이기 때문이다.


```python
ansatz = n_local(
    num_qubits=2,
    rotation_blocks=["ry", "rz"],
    entanglement_blocks="cz",
    #    entanglement="linear",
    reps=1,
)

ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2Fe4d59381-43ff-4322-a6f2-df55cef18536-0.avif&w=1200&q=75)


다음 관측가(observable)를 살펴보자:
<p>
$$
\hat{O}_2 := 2 II - 3 XX + 2 YY - 4 ZZ
$$
</p>

이 관측가는 다음과 같은 고유값을 갖는다:
<p>
$$
\left\{
\begin{array}{c}
\lambda_0 = -7 \\
\lambda_1 = 3\\
\lambda_2 = 5 \\
\lambda_3 = 7
\end{array}
\right\}
$$
</p>

그리고 고유상태는 다음과 같다:
<p>
$$
\left\{
\begin{array}{c}
|\phi_0\rangle = \frac{1}{\sqrt{2}}(|00\rangle + |11\rangle)\\
|\phi_1\rangle = \frac{1}{\sqrt{2}}(|00\rangle - |11\rangle)\\
|\phi_2\rangle = \frac{1}{\sqrt{2}}(|01\rangle + |10\rangle)\\
|\phi_3\rangle = \frac{1}{\sqrt{2}}(|01\rangle - |10\rangle)
\end{array}
\right\}
$$
</p>

```python
from qiskit.quantum_info import SparsePauliOp

observable_2 = SparsePauliOp.from_list([("II", 2), ("XX", -3), ("YY", 2), ("ZZ", -4)])
```

다음 함수를 사용하여 오버랩 페널티를 계산할 것입니다. 이는 여전히 문제를 양자 회로로 매핑하는 과정의 일부입니다. 이전 강의에서 설명했듯이, 이 함수는 현재의 변분 회로와 이전에 얻은 더 낮은 에너지/비용 상태에서 최적화된 회로 간의 오버랩을 계산합니다. 새로 생성된 회로는 실제 하드웨어에서 실행되도록 트랜스파일링되어야 합니다. 이 함수는 시뮬레이터에서 사용되는 것을 본 적이 있습니다. 여기서는 실제 백엔드를 사용할 때 트랜스파일링 및 관련 최적화를 고려해야 하므로 `if realbackend == 1` 주변의 코드가 필요합니다. 이는 2단계와 다소 유사하지만, 2단계는 나중에 명시적으로 설명하겠습니다.

```python
import numpy as np
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager


def calculate_overlaps(
    ansatz, prev_circuits, parameters, sampler, realbackend, backend
):
    def create_fidelity_circuit(circuit_1, circuit_2):
        if len(circuit_1.clbits) > 0:
            circuit_1.remove_final_measurements()
        if len(circuit_2.clbits) > 0:
            circuit_2.remove_final_measurements()

        circuit = circuit_1.compose(circuit_2.inverse())
        circuit.measure_all()
        return circuit

    overlaps = []

    for prev_circuit in prev_circuits:
        fidelity_circuit = create_fidelity_circuit(ansatz, prev_circuit)
        if realbackend == 1:
            pm = generate_preset_pass_manager(backend=backend, optimization_level=3)
            fidelity_circuit = pm.run(fidelity_circuit)
        sampler_job = sampler.run([(fidelity_circuit, parameters)])
        meas_data = sampler_job.result()[0].data.meas

        counts_0 = meas_data.get_int_counts().get(0, 0)
        shots = meas_data.num_shots
        overlap = counts_0 / shots
        overlaps.append(overlap)

    return np.array(overlaps)
```

이제 VQD의 비용 함수를 추가해 보겠습니다. 이전 강의와 비교해 보면, 실제 백엔드를 사용할 때 트랜스파일링을 돕기 위해 `realbackend`와 `backend`라는 두 개의 추가 인자가 있다는 점에 유의하세요.


```python
def cost_func_vqd(
    parameters,
    ansatz,
    prev_states,
    step,
    betas,
    estimator,
    sampler,
    hamiltonian,
    realbackend,
    backend,
):
    estimator_job = estimator.run([(ansatz, hamiltonian, [parameters])])

    total_cost = 0

    if step > 1:
        overlaps = calculate_overlaps(
            ansatz, prev_states, parameters, sampler, realbackend, backend
        )
        total_cost = np.sum(
            [np.real(betas[state] * overlap) for state, overlap in enumerate(overlaps)]
        )

    estimator_result = estimator_job.result()[0]

    value = estimator_result.data.evs[0] + total_cost

    return value
```

이번에도 디버깅을 위해 시뮬레이터를 사용한 다음 실제 하드웨어로 넘어갈 것입니다.

```python
from qiskit.primitives import StatevectorSampler
from qiskit.primitives import StatevectorEstimator

sampler = StatevectorSampler(default_shots=4092)
estimator = StatevectorEstimator()
```

여기서는 계산하고자 하는 상태의 수, 벌점, 그리고 초기 매개변수 집합 x0를 소개합니다.

```python
from qiskit.quantum_info import SparsePauliOp

k = 4
betas = [50, 60, 40]
x0 = np.ones(8)
```

이제 시뮬레이터를 사용하여 알고리즘을 테스트해 보겠습니다.

```python
from scipy.optimize import minimize

prev_states = []
prev_opt_parameters = []
eigenvalues = []

realbackend = 0

for step in range(1, k + 1):
    if step > 1:
        prev_states.append(ansatz.assign_parameters(prev_opt_parameters))

    result = minimize(
        cost_func_vqd,
        x0,
        args=(
            ansatz,
            prev_states,
            step,
            betas,
            estimator,
            sampler,
            observable_2,
            realbackend,
            None,
        ),
        method="COBYLA",
        options={"maxiter": 200, "tol": 0.000001},
    )
    print(result)

    prev_opt_parameters = result.x
    eigenvalues.append(result.fun)
```

```
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: -6.9999999999996
       x: [ 1.571e+00  1.571e+00  2.519e+00  2.100e+00  1.242e+00
            6.935e-01  2.298e+00  1.991e+00]
    nfev: 151
   maxcv: 0.0
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: 3.698974255258432
       x: [ 1.269e+00  1.109e+00  1.080e+00  1.200e+00  1.094e+00
            1.163e+00  9.752e-01  9.519e-01]
    nfev: 103
   maxcv: 0.0
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: 4.731320121938101
       x: [ 1.533e+00  2.451e+00  2.526e+00  2.406e+00  1.968e+00
            2.105e+00  8.537e-01  8.442e-01]
    nfev: 110
   maxcv: 0.0
 message: Return from COBYLA because the trust region radius reaches its lower bound.
...
       x: [ 4.150e+00  2.120e+00  3.495e+00  7.262e-01  1.953e+00
           -1.982e-01  3.263e-01  2.563e+00]
    nfev: 126
   maxcv: 0.0
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
```

```python
eigenvalues
```

```
[np.float64(-6.9999999999996),
 np.float64(3.698974255258432),
 np.float64(4.731320121938101),
 np.float64(7.008239313655201)]
```

근사 오차와 전역 위상을 제외하면 이러한 결과는 예상 결과와 상당히 유사합니다. 보다 정확한 값을 얻기 위해 기존 최적화 알고리즘의 허용 오차 또는 상태 벡터 중첩에 대한 페널티를 조정할 수 있습니다.

```python
solution_eigenvalues = [-7, 3, 5, 7]

for index, experimental_eigenvalue in enumerate(eigenvalues):
    solution_eigenvalue = solution_eigenvalues[index]

    print(
        f"Percent error: {abs((experimental_eigenvalue - solution_eigenvalue)/solution_eigenvalue):.2e}"
    )
```

```
Percent error: 5.71e-14
Percent error: 2.33e-01
Percent error: 5.37e-02
Percent error: 1.18e-03
```


### 4.2 beta 변경

이전 강의에서 언급했듯이, $\vec{\beta}$의 값은 고윳값들 사이의 차이보다 커야 한다. 이제 그 조건을 만족하지 않을 때 어떤 일이 일어나는지 $\hat{O}_2$를 통해 살펴보자.
<p>
$$
\hat{O}_2 = 2 II - 3 XX + 2 YY - 4 ZZ
$$
</p>

고윳값은

<p>
$$
\left\{
\begin{array}{c}
\lambda_0 = -7 \\
\lambda_1 = 3\\
\lambda_2 = 5 \\
\lambda_3 = 7
\end{array}
\right\}
$$
</p>


```python
from qiskit.quantum_info import SparsePauliOp

k = 4
betas = np.ones(3)
x0 = np.zeros(8)
```

```python
from scipy.optimize import minimize

prev_states = []
prev_opt_parameters = []
eigenvalues = []

realbackend = 0

for step in range(1, k + 1):
    if step > 1:
        prev_states.append(ansatz.assign_parameters(prev_opt_parameters))

    result = minimize(
        cost_func_vqd,
        x0,
        args=(
            ansatz,
            prev_states,
            step,
            betas,
            estimator,
            sampler,
            observable_2,
            realbackend,
            None,
        ),
        method="COBYLA",
        options={"tol": 0.01, "maxiter": 200},
    )
    print(result)

    prev_opt_parameters = result.x
    eigenvalues.append(result.fun)
```

```
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: -6.999916534745094
       x: [ 1.568e+00 -1.569e+00  1.385e-01  1.398e-01 -7.972e-01
            7.835e-01 -2.375e-01  4.539e-02]
    nfev: 125
   maxcv: 0.0
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: -1.515139929812874
       x: [-5.317e-04 -2.514e-03  1.016e+00  9.998e-01  3.890e-04
            1.772e-04  1.568e-04  8.497e-04]
    nfev: 35
   maxcv: 0.0
 message: Return from COBYLA because the trust region radius reaches its lower bound.
 success: True
  status: 0
     fun: -0.509948114293115
       x: [-3.796e-03  8.853e-03  3.015e-04  9.997e-01  6.271e-04
           -2.554e-03  1.017e-04  2.766e-04]
    nfev: 37
   maxcv: 0.0
 message: Return from COBYLA because the trust region radius reaches its lower bound.
...
       x: [-7.178e-03 -8.652e-03  1.125e+00 -5.428e-02 -1.586e-03
            2.031e-03 -3.462e-03  5.734e-03]
    nfev: 35
   maxcv: 0.0
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
```

```python
solution_eigenvalues = [-7, 3, 5, 7]

for index, experimental_eigenvalue in enumerate(eigenvalues):
    solution_eigenvalue = solution_eigenvalues[index]

    print(
        f"Percent error: {abs((experimental_eigenvalue - solution_eigenvalue)/solution_eigenvalue):.2e}"
    )
```

```
Percent error: 1.19e-05
Percent error: 1.51e+00
Percent error: 1.10e+00
Percent error: 9.30e-01
```

이번에는 옵티마이저가 모든 고유상태에 대한 제안된 해법으로 동일한 상태 $\|\phi_0\rangle = \frac{1}{\sqrt{2}}(\|00\rangle + \|11\rangle)$을 반환하는데, 이는 명백히 틀렸다. 이는 연속적인 비용 함수에서 최소 고유상태를 페널티화하기에 베타 값이 너무 작았기 때문에 발생한다. 따라서 알고리즘의 후속 반복에서 유효한 탐색 공간에서 제외되지 않았고, 항상 최선의 가능한 해법으로 선택되었다.

우리는 $\vec{\beta}$ 값을 실험해 보고, 이것이 고유값들 사이의 차이보다 크도록 보장할 것을 권장한다.

### 4.3 2단계: 양자 컴퓨팅 실행을 위한 문제 최적화

실제 하드웨어에서 실행하려면 선택한 양자 컴퓨터에 맞게 양자 회로를 최적화해야 합니다. 여기서는 가장 부하가 적은 백엔드를 사용하겠습니다.

```python
from qiskit_ibm_runtime import SamplerV2 as Sampler
from qiskit_ibm_runtime import EstimatorV2 as Estimator
from qiskit_ibm_runtime import Session, EstimatorOptions
from qiskit_ibm_runtime import QiskitRuntimeService


service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
# Or use a specific backend
# backend = service.backend("ibm_brisbane")
print(backend)
```

우리는 미리 설정된 패스 관리자와 최적화 레벨 3을 사용하여 회로를 트랜스파일링할 것입니다.

```python
pm = generate_preset_pass_manager(backend=backend, optimization_level=3)
isa_ansatz = pm.run(ansatz)
isa_observable = observable_2.apply_layout(layout=isa_ansatz.layout)
```



### 4.4 3단계: Qiskit 기본 기능을 사용하여 실행

베타 값을 충분히 높은 값으로 재설정한 후, 이제 실제 양자 하드웨어에서 계산을 실행할 수 있습니다.

```python
# Estimated compute resource usage: 25 minutes.
# Benchmarked at 24 min, 30 sec on an Eagle r3 processor on 5-30-24

k = 2
betas = [30, 50, 80]
x0 = np.zeros(8)

real_prev_states = []
real_prev_opt_parameters = []
real_eigenvalues = []

realbackend = 1

estimator_options = EstimatorOptions(resilience_level=1, default_shots=10_000)

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)
    sampler = Sampler(mode=session)

    for step in range(1, k + 1):
        if step > 1:
            real_prev_states.append(isa_ansatz.assign_parameters(prev_opt_parameters))

        result = minimize(
            cost_func_vqd,
            x0,
            args=(
                isa_ansatz,
                real_prev_states,
                step,
                betas,
                estimator,
                sampler,
                isa_observable,
                realbackend,
                backend,
            ),
            method="COBYLA",
            options={"maxiter": 200},
        )
        print(result)

        real_prev_opt_parameters = result.x
        real_eigenvalues.append(result.fun)

session.close()
print(real_eigenvalues)
```



### 4.5 4단계: 후처리, 결과를 고전적 형식으로 반환

출력 결과는 이전 강의 및 예제에서 다룬 내용과 구조적으로 유사합니다. 하지만 위 결과에는 들뜬 상태와 관련된 맥락에서 주의해야 할 점을 시사하는 문제가 있습니다. 이 학습 예제에서 계산 시간을 제한하기 위해 고전적 최적화 알고리즘의 최대 반복 횟수를 200회로 설정했는데, 이는 너무 적은 횟수일 수 있습니다. 시뮬레이터에서 수행한 이전 계산에서는 200회 반복에도 수렴하지 못했습니다. 여기서는 수렴은 했지만, 허용 오차는 어느 정도일까요? COBYLA가 "수렴"으로 간주할 허용 오차를 지정하지 않았습니다. 함수 값을 이전 실행 결과와 비교해 보면 COBYLA가 필요한 정밀도에 근접하지 못했음을 알 수 있습니다.

또 다른 문제가 있습니다. 첫 번째 들뜬 상태의 에너지가 바닥 상태의 에너지보다 낮아 보입니다! 어떻게 이런 일이 발생할 수 있는지 설명해 보세요. 힌트: 방금 언급한 수렴 지점과 관련이 있습니다. VQD를 H2 분자에 적용한 후 나타나는 이러한 현상은 아래에서 자세히 설명합니다.



## 5. 양자 화학: 바닥 상태 및 들뜬 상태 에너지 계산

우리의 목표는 에너지를 나타내는 관측량(해밀턴 연산자 $\hat{\mathcal{H}}$)의 기대값을 최소화하는 것입니다.

<p>
$$
\min_{\vec\theta} \langle\psi(\vec\theta)|\hat{\mathcal{H}}|\psi(\vec\theta)\rangle
$$
</p>


```python
from qiskit.quantum_info import SparsePauliOp
from qiskit.circuit.library import efficient_su2

H2_op = SparsePauliOp.from_list(
    [
        ("II", -1.052373245772859),
        ("IZ", 0.39793742484318045),
        ("ZI", -0.39793742484318045),
        ("ZZ", -0.01128010425623538),
        ("XX", 0.18093119978423156),
    ]
)

chem_ansatz = efficient_su2(H2_op.num_qubits)

chem_ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2Fde006d72-db04-49d4-806b-89bd1e5b9947-0.avif&w=1920&q=75)

```python
from qiskit import QuantumCircuit


def cost_func_vqe(params, ansatz, hamiltonian, estimator):
    """Return estimate of energy from estimator

    Parameters:
        params (ndarray): Array of ansatz parameters
        ansatz (QuantumCircuit): Parameterized ansatz circuit
        hamiltonian (SparsePauliOp): Operator representation of Hamiltonian
        estimator (Estimator): Estimator primitive instance

    Returns:
        float: Energy estimate
    """
    pub = (ansatz, hamiltonian, params)
    cost = estimator.run([pub]).result()[0].data.evs
    #    cost = estimator.run(ansatz, hamiltonian, parameter_values=params).result().values[0]
    return cost
```

이제 초기 매개변수 세트를 설정합니다.

```python
import numpy as np

x0 = np.ones(chem_ansatz.num_parameters)
```

우리는 이 비용 함수를 최소화하여 최적의 매개변수를 계산할 수 있으며, 먼저 로컬 시뮬레이터를 사용하여 코드를 확인할 수 있습니다.

```python
from qiskit.primitives import StatevectorEstimator as Estimator
from qiskit.primitives import StatevectorSampler as Sampler

estimator = Estimator()
sampler = Sampler()
```

```python
# SciPy minimizer routine
from scipy.optimize import minimize
import time

start_time = time.time()

result = minimize(
    cost_func_vqe, x0, args=(chem_ansatz, H2_op, estimator), method="COBYLA"
)

end_time = time.time()
execution_time = end_time - start_time

result
```

```
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: -1.857275029048451
       x: [ 7.326e-01  1.354e+00 ...  1.040e+00  1.508e+00]
    nfev: 242
   maxcv: 0.0
```

비용 함수의 최솟값(-1.857...)은 하트리 단위로 나타낸 H2 분자의 바닥 상태 에너지입니다.

### 5.1 들뜬 상태

또한 VQD를 활용하여 총 $k=2$개의 상태(바닥 상태와 첫 번째 들뜬 상태)를 구할 수 있습니다.

```python
from qiskit.quantum_info import SparsePauliOp
import numpy as np

k = 2
betas = [33, 33]
# x0 = np.zeros(ansatz.num_parameters)
x0 = [
    1.164e00,
    -2.438e-01,
    9.358e-04,
    6.745e-02,
    1.990e00,
    9.810e-02,
    6.154e-01,
    5.454e-01,
]
```

이제 중복 계산을 추가하겠습니다.

```python
from scipy.optimize import minimize

prev_states = []
prev_opt_parameters = []
eigenvalues = []

realbackend = 0

for step in range(1, k + 1):
    if step > 1:
        prev_states.append(ansatz.assign_parameters(prev_opt_parameters))

    result = minimize(
        cost_func_vqd,
        x0,
        args=(
            ansatz,
            prev_states,
            step,
            betas,
            estimator,
            sampler,
            H2_op,
            realbackend,
            None,
        ),
        method="COBYLA",
        options={"tol": 0.001, "maxiter": 2000},
    )
    print(result)

    prev_opt_parameters = result.x
    eigenvalues.append(result.fun)
```

```
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: -1.8572671093941977
       x: [ 1.164e+00 -2.437e-01  2.118e-03  6.448e-02  1.990e+00
            9.870e-02  6.167e-01  5.476e-01]
    nfev: 58
   maxcv: 0.0
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: -1.0322873777662176
       x: [ 3.205e+00  1.502e+00  1.699e+00 -1.107e-02  3.086e+00
            1.530e+00  4.445e-02  7.013e-02]
    nfev: 99
   maxcv: 0.0
```

```python
eigenvalues
```

```
[-1.8572671093941977, -1.0322873777662176]
```



### 5.2 실제 하드웨어 및 최종 주의 사항

실제 하드웨어에서 이를 실행하려면 선택한 양자 컴퓨터에 맞게 양자 회로를 최적화해야 합니다. 여기서는 가장 부하가 적은 백엔드를 사용하겠습니다.

```python
from qiskit_ibm_runtime import SamplerV2 as Sampler
from qiskit_ibm_runtime import EstimatorV2 as Estimator
from qiskit_ibm_runtime import Session, EstimatorOptions
from qiskit_ibm_runtime import QiskitRuntimeService


service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
```

트랜스파일링을 위해 미리 설정된 패스 관리자를 사용하고, 최적화 레벨 3을 사용하여 회로를 최대한 최적화할 것입니다.

```python
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager

pm = generate_preset_pass_manager(backend=backend, optimization_level=3)
isa_ansatz = pm.run(ansatz)
isa_observable = H2_op.apply_layout(layout=isa_ansatz.layout)
```

VQD는 반복적인 작업이 많기 때문에 모든 단계를 런타임 세션 내에서 수행합니다. 따라서 작업은 처음에만 큐에 추가되고, 매 매개변수 업데이트 사이에는 큐에 추가되지 않습니다. 비용 함수나 추정기의 구문은 변경되지 않습니다.

```python
x0 = [
    1.306e00,
    -2.284e-01,
    6.913e-02,
    -2.530e-02,
    1.849e00,
    7.433e-02,
    6.366e-01,
    5.600e-01,
]
```

```python
# Estimated hardware usage: 20 min benchmarked on an Eagle r3 processor on 5-30-24

real_prev_states = []
real_prev_opt_parameters = []
real_eigenvalues = []

realbackend = 1

estimator_options = EstimatorOptions(resilience_level=1, default_shots=4096)

with Session(backend=backend) as session:
    estimator = Estimator(mode=session)
    sampler = Sampler(mode=session)

    for step in range(1, k + 1):
        if step > 1:
            real_prev_states.append(
                isa_ansatz.assign_parameters(real_prev_opt_parameters)
            )

        result = minimize(
            cost_func_vqd,
            x0,
            args=(
                isa_ansatz,
                real_prev_states,
                step,
                betas,
                estimator,
                sampler,
                isa_observable,
                realbackend,
                backend,
            ),
            method="COBYLA",
            options={"tol": 0.001, "maxiter": 300},
        )
        print(result)

        real_prev_opt_parameters = result.x
        real_eigenvalues.append(result.fun)

session.close()
print(real_eigenvalues)
```

계산된 바닥 상태 에너지(-1.83 하트리)는 정확한 값(-1.85 하트리)과 크게 차이가 나지 않습니다. 그러나 들뜬 상태 에너지는 상당히 오차가 있습니다. 이는 앞서 이 단원에서 보았던 오류와 유사합니다. 들뜬 상태 에너지가 바닥 상태 에너지와 거의 같다는 것입니다. 이전 사례에서는 들뜬 상태 에너지가 바닥 상태 에너지보다 *낮은* 경우도 있었습니다.

변분 계산에서 실제 바닥 상태 에너지보다 낮은 에너지가 나오는 것은 불가능합니다. 이전 사례에서는 계산된 바닥 상태 에너지가 실제 바닥 상태 에너지와 상당히 가까웠습니다. 하지만 그 경우에는 실제 바닥 상태 에너지를 얻은 것이 아니므로 모순이 없습니다. 이번 사례에서는 바닥 상태 에너지는 정확한 값에 상당히 근접했지만, 들뜬 상태 에너지는 이상하리만치 같은 값에 가깝습니다.

이 문제가 어떻게 발생했는지 더 잘 이해하려면, 들뜬 상태를 찾는 방식이 변분 상태가 바닥 상태에 직교하도록 요구하는 것임을 떠올려 보십시오(겹침 회로와 페널티 항 사용). 만약 정확한 바닥 상태 에너지를 얻지 못하거나 몇 퍼센트 정도 오차가 발생하면, 정확한 바닥 상태 벡터도 얻지 못하게 됩니다! 따라서 들뜬 상태가 우리가 찾은 첫 번째 상태와 직교하도록 요구할 때, 우리는 실제 바닥 상태와의 직교성을 강제하는 것이 아니라, 실제 바닥 상태의 근사치(때로는 불완전한 근사치)와의 직교성을 강제하는 것입니다. 결과적으로 들뜬 상태는 실제 바닥 상태와 직교하도록 강제되지 않았고, 들뜬 상태에 대한 에너지 추정치는 실제로는 바닥 상태 에너지에 매우 가까웠습니다.

이는 VQD에서 항상 문제가 될 수 있습니다. 하지만 원칙적으로는 고전적 최적화기의 최대 반복 횟수를 늘리거나, 고전적 최적화기의 허용 오차를 낮추거나, 실제 바닥 상태를 자주 놓치는 경우 다른 접근 방식을 시도하는 등의 방법으로 이 문제를 해결할 수 있습니다. 앞서 살펴본 바와 같이, 중첩 페널티(베타)를 수정해야 할 수도 있습니다. 하지만 이는 사실상 별개의 문제입니다. 중첩 회로의 실제 접지 상태를 정확하게 추정하지 못했다면, 중첩에 대한 어떤 페널티도 실제 접지 상태에서 벗어나는 것을 막을 수는 없습니다.



### 6. 최적화: 최대 절단(max-cut)

최대 절단(max-cut) 문제는 그래프의 정점들을 서로 겹치지 않는 두 개의 집합으로 나누어, 두 집합 사이의 간선 수를 최대화하는 조합 최적화 문제입니다. 좀 더 구체적으로 설명하면, 정점 집합 $V$와 간선 집합 $E$가 주어졌을 때, 무방향 그래프 $G=(V,E)$에서, 최대 절단 문제는 정점들을 서로 겹치지 않는 두 개의 부분집합 $S$와 $T$로 나누어, 한쪽 끝점이 $S$에 있고 다른 쪽 끝점이 $T$에 있는 간선의 수를 최대화하는 문제입니다.

최대 절단은 클러스터링, 네트워크 설계, 상전이 등 다양한 문제에 적용할 수 있습니다. 먼저 문제 그래프를 생성해 보겠습니다.

```python
import rustworkx as rx
from rustworkx.visualization import mpl_draw

n = 4
G = rx.PyGraph()
G.add_nodes_from(range(n))
# The edge syntax is (start, end, weight)
edges = [(0, 1, 1.0), (0, 2, 1.0), (0, 3, 1.0), (1, 2, 1.0), (2, 3, 1.0)]
G.add_edges_from(edges)

mpl_draw(
    G, pos=rx.shell_layout(G), with_labels=True, edge_labels=str, node_color="#1192E8"
)
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F3eb71b79-a988-4807-aa88-7fb25a78a236-0.avif&w=1920&q=75)


이 문제는 이진 최적화 문제로 표현할 수 있다. 각 노드 $0 \leq i < n$에 대해, 여기서 $n$은 그래프의 노드 개수(이 경우 $n=4$)이며, 이진 변수 $x_i$를 고려할 것이다. 이 변수는 노드 $i$가 우리가 $1$로 표기할 그룹에 속하면 값 $1$을 갖고, 다른 그룹, 즉 $0$으로 표기할 그룹에 속하면 $0$을 갖는다. 또한 노드 $i$에서 노드 $j$로 가는 간선의 가중치를 $w_{ij}$ (인접 행렬 $w$의 원소 $(i,j)$)로 표기하겠다. 그래프가 무방향이므로 $w_{ij}=w_{ji}$이다. 그러면 다음 비용 함수를 최대화하는 것으로 문제를 공식화할 수 있다:

<p>
$$
\begin{aligned}
C(\vec{x})
& =\sum_{i,j=0}^n w_{ij} x_i(1-x_j)\\[1mm]
& = \sum_{i,j=0}^n w_{ij} x_i - \sum_{i,j=0}^n w_{ij} x_ix_j\\[1mm]
& = \sum_{i,j=0}^n w_{ij} x_i - \sum_{i=0}^n \sum_{j=0}^i 2w_{ij} x_ix_j
\end{aligned}
$$
</p>

이 문제를 양자 컴퓨터로 풀기 위해, 비용 함수를 관측가능량의 기댓값으로 표현할 것이다. 그러나 Qiskit이 기본적으로 허용하는 관측가능량은 고유값이 $0$과 $1$이 아닌 $1$과 $-1$인 파울리 연산자로 구성된다. 따라서 다음과 같은 변수 치환을 할 것이다:

여기서 $\vec{x}=(x_0,x_1,\cdots ,x_{n-1})$. 인접 행렬 $w$를 사용하여 모든 간선의 가중치에 편리하게 접근할 수 있다. 이를 사용하여 우리의 비용 함수를 얻을 것이다:

<p>
$$
z_i = 1-2x_i \rightarrow x_i = \frac{1-z_i}{2}
$$
</p>

이것은 다음을 의미한다:
<p>
$$
\begin{array}{lcl} x_i=0 & \rightarrow & z_i=1 \\ x_i=1 & \rightarrow & z_i=-1.\end{array}
$$
</p>

따라서 최대화하고자 하는 새로운 비용 함수는 다음과 같다:

<p>
$$
\begin{aligned}
C(\vec{z})
& = \sum_{i,j=0}^n w_{ij} \bigg(\frac{1-z_i}{2}\bigg)\bigg(1-\frac{1-z_j}{2}\bigg)\\[1mm]
& = \sum_{i,j=0}^n \frac{w_{ij}}{4} - \sum_{i,j=0}^n \frac{w_{ij}}{4} z_iz_j\\[1mm]
& = \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2} -  \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2} z_iz_j
\end{aligned}
$$
</p>

또한 양자 컴퓨터의 자연적인 경향은 최대값이 아닌 최소값(보통 최저 에너지)을 찾는 것이므로 $C(\vec{z})$를 최대화하는 대신 다음을 최소화할 것이다:

<p>
$$
-C(\vec{z}) =  \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2} z_iz_j -  \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2}
$$
</p>

이제 최소화할 비용 함수가 변수가 $-1$과 $1$의 값을 가질 수 있게 되었으므로, 파울리 $Z$와 다음과 같은 유추를 할 수 있다:

<p>
$$
z_i \equiv Z_i = \overbrace{I}^{n-1}\otimes ... \otimes \overbrace{Z}^{i} \otimes ... \otimes \overbrace{I}^{0}
$$
</p>

다시 말해, 변수 $z_i$는 큐비트 $i$에 작용하는 $Z$ 게이트와 동일하다. 또한:

<p>
$$
Z_i|x_{n-1}\cdots x_0\rangle = z_i|x_{n-1}\cdots x_0\rangle \rightarrow \langle x_{n-1}\cdots x_0 |Z_i|x_{n-1}\cdots x_0\rangle = z_i
$$
</p>

그러면 고려할 관측가능량은 다음과 같다:

<p>
$$
\hat{H} = \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2} Z_iZ_j
$$
</p>

여기에 이후 독립항을 더해야 한다:

<p>
$$
\texttt{offset} = - \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2}
$$
</p>

연산자는 에지로 연결된 노드에 있는 Z개의 연산자를 포함하는 항들의 선형 조합입니다(0번째 큐비트가 가장 오른쪽에 있다는 점을 기억하십시오): $IIZZ + IZIZ + IZZI + ZIIZ + ZZII$. 연산자가 구성되면 Qiskit 회로 라이브러리의 `QAOAAnsatz` 회로를 사용하여 QAOA 알고리즘의 앙상블을 쉽게 구축할 수 있습니다.

```python
from qiskit.circuit.library import QAOAAnsatz
from qiskit.quantum_info import SparsePauliOp

max_hamiltonian = SparsePauliOp.from_list(
    [("IIZZ", 1), ("IZIZ", 1), ("IZZI", 1), ("ZIIZ", 1), ("ZZII", 1)]
)


max_ansatz = QAOAAnsatz(max_hamiltonian, reps=2)
# Draw
max_ansatz.decompose(reps=3).draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2Fd28c4262-a080-4fe4-9427-762326184cd8-0.avif&w=3840&q=75)


```python
# Sum the weights, and divide by 2

offset = -sum(edge[2] for edge in edges) / 2
print(f"""Offset: {offset}""")
```

Offset: -2.5

```python
def cost_func(params, ansatz, hamiltonian, estimator):
    """Return estimate of energy from estimator

    Parameters:
        params (ndarray): Array of ansatz parameters
        ansatz (QuantumCircuit): Parameterized ansatz circuit
        hamiltonian (SparsePauliOp): Operator representation of Hamiltonian
        estimator (Estimator): Estimator primitive instance

    Returns:
        float: Energy estimate
    """
    pub = (ansatz, hamiltonian, params)
    cost = estimator.run([pub]).result()[0].data.evs
    #    cost = estimator.run(ansatz, hamiltonian, parameter_values=params).result().values[0]
    return cost
```

```python
from qiskit.primitives import StatevectorEstimator as Estimator
from qiskit.primitives import StatevectorSampler as Sampler

estimator = Estimator()
sampler = Sampler()
```

이제 초기 무작위 매개변수 세트를 설정합니다.

```python
import numpy as np

x0 = 2 * np.pi * np.random.rand(max_ansatz.num_parameters)
print(x0)
```

$\[6.0252949  0.58448176 2.15785731 1.13646074\]$

어떤 고전적인 최적화 알고리즘이든 비용 함수를 최소화하는 데 사용할 수 있습니다. 하지만 실제 양자 시스템에서는 매끄럽지 않은 비용 함수 지형에 맞춰 설계된 최적화 알고리즘이 일반적으로 더 나은 성능을 보입니다. 여기서는 SciPy의 [COBYLA 루틴](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-cobyla.html#optimize-minimize-cobyla)을 minimize 함수를 통해 사용합니다.

Runtime 함수를 반복적으로 여러 번 호출하기 때문에, 모든 호출을 단일 블록 내에서 실행하기 위해 세션을 사용합니다. 또한, QAOA의 경우, 해는 최소화 과정에서 얻은 최적 매개변수로 경계가 지정된 앙상블 회로의 출력 분포에 인코딩되어 있습니다. 따라서 Sampler 프리미티브가 필요하며, 동일한 세션을 사용하여 이를 인스턴스화합니다.


그리고 최소화 루틴을 실행합니다.

```python
result = minimize(
    cost_func, x0, args=(max_ansatz, max_hamiltonian, estimator), method="COBYLA"
)
```

```python
print(result)
```

```
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: -2.585287311689236
       x: [ 7.332e+00  3.904e-01  2.045e+00  1.028e+00]
    nfev: 80
   maxcv: 0.0
```

매개변수 각도(`x`)의 해 벡터를 가정한 회로에 대입하면 우리가 찾던 그래프 분할 결과를 얻을 수 있습니다.

```python
eigenvalue = cost_func(result.x, max_ansatz, max_hamiltonian, estimator)
print(f"""Eigenvalue: {eigenvalue}""")
print(f"""Max-Cut Objective: {eigenvalue + offset}""")
```

```
Eigenvalue: -2.585287311689236
Max-Cut Objective: -5.085287311689235
```

```python
from qiskit.result import QuasiDistribution
from qiskit.primitives import StatevectorSampler

sampler = StatevectorSampler()

# Assign solution parameters to ansatz
qc = max_ansatz.assign_parameters(result.x)

# Add measurements to our circuit
qc.measure_all()

# Sample ansatz at optimal parameters
# samp_dist = sampler.run(qc).result().quasi_dists[0]

shots = 1024
job = sampler.run([qc], shots=shots)
```

```python
qc.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F686ee28e-9327-40d1-8d7c-3ec527160f01-0.avif&w=3840&q=75)

```python
data_pub = job.result()[0].data
bitstrings = data_pub.meas.get_bitstrings()
counts = data_pub.meas.get_counts()
quasi_dist = QuasiDistribution(
    {outcome: freq / shots for outcome, freq in counts.items()}
)
probabilities = quasi_dist

# Close the session since we are now done with it
# session.close()
```

```python
from qiskit.visualization import plot_distribution

plot_distribution(counts)
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F13a4c371-fd40-4ce3-b737-268d4a4c9c2c-0.avif&w=1920&q=75)

```python
binary_string = max(counts.items(), key=lambda kv: kv[1])[0]
x = np.asarray([int(y) for y in reversed(list(binary_string))])

colors = ["r" if x[i] == 0 else "c" for i in range(n)]
mpl_draw(
    G, pos=rx.shell_layout(G), with_labels=True, edge_labels=str, node_color=colors
)
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fexamples-and-applications%2Fextracted-outputs%2F4238cc64-6910-43d2-889e-6322bf2864eb-0.avif&w=1920&q=75)



## 7. 요약

이번 강의를 통해 다음 내용을 학습했습니다.

* 사용자 정의 변분 알고리즘 작성 방법
* 변분 알고리즘을 사용하여 최소 고유값 찾기
* 변분 알고리즘을 활용하여 응용 사례 해결 방법


