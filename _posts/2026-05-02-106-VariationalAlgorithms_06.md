---
title: 16차시 6:Variational Algorithms 6
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 6. 사례 및 확장

이 장에서는 다음과 같은 여러 양자 변분 알고리즘을 다룹니다.

* [변분 양자 고유값 해결기(VQE)](https://arxiv.org/abs/1304.3061)
* [부분 공간 탐색 VQE(SSVQE)](https://arxiv.org/abs/1810.09434)
* [변분 양자 축소(VQD)](https://arxiv.org/abs/1805.08138)
* [양자 샘플링 회귀(QSR)](https://arxiv.org/pdf/2012.02338)

이러한 알고리즘을 사용하여 가중치, 페널티, 과표본 추출, 저표본 추출과 같은 사용자 정의 변분 알고리즘에 통합할 수 있는 여러 설계 아이디어를 배우게 됩니다. 이러한 개념들을 직접 실험해보고 그 결과를 커뮤니티와 공유해 주시길 바랍니다.

Qiskit 패턴 프레임워크는 이러한 모든 알고리즘에 적용되지만, 첫 번째 예제에서만 구체적인 단계를 설명하겠습니다.

## 1. Variational Quantum Eigensolver (VQE)
[VQE](https://arxiv.org/abs/1304.3061)는 가장 널리 사용되는 변분 양자 알고리즘 중 하나로, 다른 알고리즘들이 기반으로 삼을 수 있는 템플릿을 제공합니다.

![VQE가 기준 상태와 가설을 사용하여 비용 함수를 추정하고, 변분 매개변수를 이용하여 반복 계산하는 방식을 보여주는 다이어그램입니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/instances-and-extensions/instances-vqe.svg)

### 1.1 1단계: 고전적 입력을 양자 문제로 매핑

#### 1.1.1 이론적 구조

VQE의 구조는 단순하다:

* 참조 연산자 $U_R$ 준비
  * 상태 $\|0\rangle$에서 시작하여 참조 상태 $\|\rho\rangle$로 이동한다
* 변분 형태 $U_V(\vec\theta_{i,j})$를 적용하여 ansatz $U_A(\vec\theta_{i,j})$를 생성
  * 상태 $\|\rho\rangle$에서 $U_V(\vec\theta_{i,j})\|\rho\rangle = \|\psi(\vec\theta_{i,j})\rangle$로 이동한다
* 유사한 문제가 있는 경우 $i=0$에서 부트스트랩 (일반적으로 고전적 시뮬레이션이나 샘플링을 통해 발견됨)
  * 각 최적화기는 서로 다르게 부트스트랩되어 초기 매개변수 벡터 집합 $\Theta_0 := \\{ {\vec\theta_{0,j} \| j \in \mathcal{J}_\text{opt}^0} \\}$이 생성된다 (예를 들어, 초기점 $\vec\theta_0$에서).
* 양자 컴퓨터에서 준비된 모든 상태에 대해 비용 함수 $C(\vec\theta_{i,j}) := \langle \psi(\vec{\theta}) \| \hat{H} \| \psi(\vec{\theta})\rangle$를 평가한다.
* 고전적 최적화기를 사용하여 다음 매개변수 집합 $\Theta_{i+1}$을 선택한다.
* 수렴에 도달할 때까지 과정을 반복한다.

이는 비용 함수를 평가하는 단순한 고전적 최적화 루프이다. 일부 최적화기는 다음 반복을 계산하거나 수렴을 평가하기 위해 그래디언트를 계산하는 데 여러 번의 평가가 필요할 수 있다.

다음 관측가능량에 대한 예시는 다음과 같다:
<p>
$$
\hat{O}_1 = 2 II - 2 XX + 3 YY - 3 ZZ,
$$
</p>

### 1.1.2 Implementation

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import SparsePauliOp
from qiskit.circuit.library import TwoLocal
import numpy as np

theta_list = (2 * np.pi * np.random.rand(1, 8)).tolist()
observable = SparsePauliOp.from_list([("II", 2), ("XX", -2), ("YY", 3), ("ZZ", -3)])

reference_circuit = QuantumCircuit(2)
reference_circuit.x(0)

variational_form = TwoLocal(
    2,
    rotation_blocks=["rz", "ry"],
    entanglement_blocks="cx",
    entanglement="linear",
    reps=1,
)

ansatz = reference_circuit.compose(variational_form)

ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Finstances-and-extensions%2Fextracted-outputs%2Fdb20526e-48d4-44eb-9d2b-6c5d2ce5777e-0.avif&w=1080&q=75)

```python
def cost_func_vqe(parameters, ansatz, hamiltonian, estimator):
    """Return estimate of energy from estimator

    Parameters:
        params (ndarray): Array of ansatz parameters
        ansatz (QuantumCircuit): Parameterized ansatz circuit
        hamiltonian (SparsePauliOp): Operator representation of Hamiltonian
        estimator (Estimator): Estimator primitive instance

    Returns:
        float: Energy estimate
    """

    estimator_job = estimator.run([(ansatz, hamiltonian, [parameters])])
    estimator_result = estimator_job.result()[0]

    cost = estimator_result.data.evs[0]
    return cost
```

```python
from qiskit.primitives import StatevectorEstimator

estimator = StatevectorEstimator()
```

우리는 이 비용 함수를 사용하여 최적 매개변수를 계산할 수 있습니다.

```python
# SciPy minimizer routine
from scipy.optimize import minimize

x0 = np.ones(8)

result = minimize(
    cost_func_vqe, x0, args=(ansatz, observable, estimator), method="COBYLA"
)

result
```

```bash
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: -5.999999982445723
       x: [ 1.741e+00  9.606e-01  1.571e+00  2.115e-05  1.899e+00
            1.243e+00  6.063e-01  6.063e-01]
    nfev: 136
   maxcv: 0.0
```


### 1.2 2단계: 양자 실행을 위한 문제 최적화

가장 덜 바쁜 백엔드를 선택하고, `qiskit_ibm_runtime`에서 필요한 구성 요소를 가져옵니다.

```python
from qiskit_ibm_runtime import SamplerV2 as Sampler
from qiskit_ibm_runtime import EstimatorV2 as Estimator
from qiskit_ibm_runtime import Session, EstimatorOptions
from qiskit_ibm_runtime import QiskitRuntimeService


service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
print(backend)
```

최적화 레벨 3을 사용하는 사전 설정된 패스 관리자를 이용하여 회로를 트랜스파일링하고, 그에 따른 레이아웃을 관측 가능한 데이터에 적용합니다.

```python
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager

pm = generate_preset_pass_manager(backend=backend, optimization_level=3)
isa_ansatz = pm.run(ansatz)
isa_observable = observable.apply_layout(layout=isa_ansatz.layout)
```



### 1.3 3단계: Qiskit 런타임 기본 요소를 사용하여 실행

이제 IBM Quantum® 하드웨어에서 계산을 실행할 준비가 되었습니다. 비용 함수 최소화는 반복적인 작업이기 때문에 런타임 세션을 시작합니다. 이렇게 하면 대기열에서 한 번만 기다리면 됩니다. 작업이 시작되면 매개변수 업데이트가 포함된 각 반복이 즉시 실행됩니다.

```python
x0 = np.ones(8)

estimator_options = EstimatorOptions(resilience_level=1, default_shots=10_000)

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)

    result = minimize(
        cost_func_vqe,
        x0,
        args=(isa_ansatz, isa_observable, estimator),
        method="COBYLA",
        options={"maxiter": 200, "disp": True},
    )
session.close()
print(result)
```



### 1.4 4단계: 후처리, 기존 형식으로 결과 반환

최소화 루틴이 성공적으로 종료되었음을 확인할 수 있습니다. 이는 COBYLA 클래식 최적화 프로그램의 기본 허용 오차에 도달했음을 의미합니다. 더 정확한 결과가 필요한 경우 더 작은 공차를 지정할 수 있습니다. 결과가 위의 시뮬레이터에서 얻은 결과와 비교하여 몇 퍼센트 차이가 나기 때문에 실제로 그럴 수도 있습니다.

얻은 x 값은 비용 함수를 최소화하는 매개변수에 대한 현재 최선의 추측입니다. 더 높은 정밀도를 얻기 위해 반복하는 경우 처음에 사용된 x0(1로 구성된 벡터) 대신 해당 값을 사용해야 합니다.

마지막으로, 최적화 과정에서 함수가 96번 평가되었음을 알 수 있습니다. 일부 최적화 프로그램은 기울기를 추정할 때와 같이 단일 단계에서 여러 함수 평가를 요구하기 때문에 이는 최적화 단계 수와 다를 수 있습니다.

## 2. 부분공간 탐색 VQE (SSVQE)

[SSVQE](https://arxiv.org/abs/1810.09434)은 관측가능량 $\hat{H}$의 고유값 $\{\lambda_0, \lambda_1,...,\lambda_{N-1}\}$ 중 처음 $k$개의 고유값을 얻을 수 있게 해주는 VQE의 변형이다. 여기서 $N\geq k$이다. 일반성을 잃지 않고, $\lambda_0<\lambda_1<...<\lambda_{N-1}$이라고 가정한다. SSVQE는 가장 큰 가중치를 가진 항을 우선적으로 최적화하는 데 도움이 되도록 가중치를 추가하는 새로운 아이디어를 도입한다.

![부분공간 탐색 VQE가 변분 알고리즘의 구성 요소들을 어떻게 사용하는지 보여주는 다이어그램](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/instances-and-extensions/instances-ssvqe.svg)

이 알고리즘을 구현하기 위해서는 $j,l<k$에 대해 $\langle \rho_j \| \rho_l \rangle = \delta_{jl}$을 만족하는 $k$개의 서로 직교하는 참조 상태 $\{ \|\rho_j\rangle \}_{j=0}^{k-1}$이 필요하다. 이 상태들은 Pauli 연산자를 사용하여 구성할 수 있다. 그러면 이 알고리즘의 비용 함수는 다음과 같다:

<p>
$$
\begin{aligned}
C(\vec{\theta})
& := \sum_{j=0}^{k-1} w_j \langle \rho_j | U_{V}^{\dagger}(\vec{\theta})\hat{H} U_{V}(\vec{\theta})|\rho_j \rangle \\[1mm]
& := \sum_{j=0}^{k-1} w_j \langle \psi_{j}(\vec{\theta}) | \hat{H} | \psi_{j}(\vec{\theta}) \rangle \\[1mm]
\end{aligned}
$$
</p>

여기서 $w_j$는 $j<l<k$이면 $w_j>w_l$이 되는 임의의 양수이고, $U_V(\vec{\theta})$는 사용자가 정의한 변분 형태이다.

SSVQE 알고리즘은 서로 다른 고유값에 대응하는 고유 상태들이 서로 직교한다는 사실에 의존한다. 구체적으로, $U_V(\vec{\theta})\|\rho_j\rangle$와 $U_V(\vec{\theta})\|\rho_l\rangle$의 내적은 다음과 같이 표현될 수 있다:

<p>
$$
\begin{aligned}
\langle \rho_j | U_{V}^{\dagger}(\vec{\theta})U_{V}(\vec{\theta})|\rho_l \rangle
& = \langle \rho_j | I |\rho_l \rangle \\[1mm]
& = \langle \rho_j | \rho_l \rangle \\[1mm]
& = \delta_{jl}
\end{aligned}
$$
</p>

첫 번째 등식은 $U_{V}(\vec{\theta})$가 양자 연산자이므로 유니타리하기 때문에 성립한다. 마지막 등식은 참조 상태 $\|\rho_j\rangle$의 직교성 때문에 성립한다. 유니타리 변환을 통해 직교성이 보존된다는 사실은 양자 정보 과학에서 표현되는 정보 보존의 원리와 깊이 관련되어 있다. 이러한 관점에서, 비유니타리 변환은 정보가 손실되거나 주입되는 과정을 나타낸다.

가중치 $w_j$는 모든 상태들이 고유 상태가 되도록 돕는다. 가중치가 충분히 다르다면, 가장 큰 가중치를 가진 항(즉, $w_0$)이 최적화 과정에서 다른 항들보다 우선순위를 갖게 될 것이다. 결과적으로, 결과 상태 $U_{V}(\vec{\theta})\|\rho_0 \rangle$는 $\lambda_0$에 대응하는 고유 상태가 될 것이다. $$\{ U_{V}(\vec{\theta})\vert\rho_j\rangle \}_{j=0}^{k-1}$$이 서로 직교하기 때문에, 나머지 상태들은 이 상태에 직교하며, 따라서 고유값 $\{\lambda_1,...,\lambda_{N-1}\}$에 대응하는 부분공간에 포함될 것이다.

나머지 항들에 대해서도 동일한 논증을 적용하면, 다음 우선순위는 가중치 $w_1$을 가진 항이 될 것이므로, $U_{V}(\vec{\theta})\|\rho_1 \rangle$는 $\lambda_1$에 대응하는 고유 상태가 되고, 다른 항들은 $\{\lambda_2,...,\lambda_{N-1}\}$의 고유 공간에 포함될 것이다.

귀납적으로 추론하면, $0\leq j < k$에 대해 $U_{V}(\vec{\theta})\|\rho_j \rangle$는 $\lambda_j$의 근사 고유 상태가 됨을 추론할 수 있다.

### 2.1 이론적 구조

SSVQE는 다음과 같이 요약될 수 있다:

* 유니타리 $U_R$을 $k$개의 서로 다른 계산 기저 상태에 적용하여 여러 참조 상태를 준비한다
  * 이 알고리즘은 $j,l<k$에 대해 $\langle \rho_j \| \rho_l \rangle = \delta_{jl}$을 만족하는 $k$개의 서로 직교하는 참조 상태 $\{ \|\rho_j\rangle \}_{j=0}^{k-1}$의 사용을 필요로 한다.
* 각 참조 상태에 변분 형태 $U_V(\vec\theta_{i,j})$를 적용하여, 다음과 같은 ansatz $U_A(\vec\theta_{i,j})$를 얻는다.
* 유사한 문제가 사용 가능한 경우 $i=0$에서 부트스트랩한다(보통 고전적 시뮬레이션이나 샘플링을 통해 찾는다).
* 양자 컴퓨터에서 모든 준비된 상태에 대해 비용 함수 $C(\vec\theta_{i,j}) := \sum_{j=0}^{k-1} w_j \langle \psi_{j}(\vec{\theta}) \| \hat{H} \| \psi_{j}(\vec{\theta}) \rangle$를 평가한다.
  * 이는 관측가능량의 기댓값 $\langle \psi_{j}(\vec{\theta}) \| \hat{H} \| \psi_{j}(\vec{\theta}) \rangle$을 계산하고 그 결과에 $w_j$를 곱하는 것으로 분리될 수 있다.
  * 이후, 비용 함수는 모든 가중 기댓값의 합을 반환한다.
* 고전적 최적화기를 사용하여 다음 매개변수 집합 $\Theta_{i+1}$을 결정한다.
* 수렴이 달성될 때까지 위 단계들을 반복한다.

평가에서 SSVQE의 비용 함수를 재구성할 것이지만, 다음 코드 조각이 해결책에 대한 동기를 제공한다:

```python
import numpy as np


def cost_func_ssvqe(
    params, initialized_anastz_list, weights, ansatz, hamiltonian, estimator
):
    # """Return estimate of energy from estimator

    # Parameters:
    #     params (ndarray): Array of ansatz parameters
    #     initialized_anastz_list (list QuantumCircuit): Array of initialised ansatz with reference
    #     weights (list): List of weights
    #     ansatz (QuantumCircuit): Parameterized ansatz circuit
    #     hamiltonian (SparsePauliOp): Operator representation of Hamiltonian
    #     estimator (Estimator): Estimator primitive instance

    # Returns:
    #     float: Weighted energy estimate
    # """

    energies = []

    # Define SSVQE

    weighted_energy_sum = np.dot(energies, weights)
    return weighted_energy_sum
```



## 3. 변분 양자 축소(VQD)

[VQD](https://arxiv.org/abs/1805.08138)는 관측량 $\hat{H}$의 고유값 $\{\lambda_0, \lambda_1,...,\lambda_{N-1}\}$ (단, $N\geq k$)에 대해, 기존 변분 양자 최적화(VQE) 방법이 첫 번째 고유값만을 구하는 것이 아니라, 처음 $k$개의 고유값을 모두 구하는 반복적인 방법입니다. 이 절의 나머지 부분에서는 일반성을 잃지 않고 $\lambda_0\leq\lambda_1\leq...\leq\lambda_{N-1}$이라고 가정합니다. VQD는 최적화 과정을 안내하기 위해 페널티 비용 개념을 도입합니다.

![VQD가 변분 알고리즘의 구성 요소를 사용하는 방법을 보여주는 다이어그램입니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/instances-and-extensions/instances-vqd.svg)


VQD는 각 중첩(overlap) 항이 비용에 기여하는 정도를 균형 있게 조절하기 위해 $\beta$로 표기되는 패널티 항을 도입한다. 이 패널티 항은 직교성(orthogonality)이 달성되지 않을 경우 최적화 과정에 페널티를 부과하는 역할을 한다. 우리는 이 제약을 부과하는데, 그 이유는 관측가능량(observable) 또는 에르미트 연산자(Hermitian operator)의 고유상태(eigenstate)는 서로 다른 고유값(eigenvalue)에 대해 항상 서로 직교하며, 축퇴(degeneracy)나 반복 고유값의 경우에도 그렇게 만들 수 있기 때문이다. 따라서 $\lambda_0$에 해당하는 고유상태와의 직교성을 강제함으로써, 우리는 나머지 고유값 $\{\lambda_1, \lambda_2,..., \lambda_{N-1}\}$에 해당하는 부분공간(subspace) 위에서 효과적으로 최적화를 수행하게 된다. 여기서 $\lambda_1$은 나머지 고유값 중 가장 낮은 고유값이며, 따라서 새로운 문제의 최적해는 변분 정리(variational theorem)를 사용하여 얻을 수 있다.

VQD의 일반적인 아이디어는 평소와 같이 VQE를 사용하여 어떤 최적 매개변수 벡터 $\vec{\theta^0}$에 대해 최저 고유값 $\lambda_0 := C_0(\vec\theta^0) \equiv C_\text{VQE}(\vec\theta^0)$과 이에 해당하는 (근사) 고유상태 $\|\psi(\vec{\theta^0})\rangle$를 얻는 것이다. 그 다음, 다음 고유값 $\lambda_1 > \lambda_0$를 얻기 위해, 비용 함수 $C_0(\vec{\theta}) := \langle \psi(\vec{\theta}) \| \hat{H} \| \psi(\vec{\theta})\rangle$를 최소화하는 대신 다음을 최적화한다:

<p>
$$
C_1(\vec{\theta}) :=
C_0(\vec{\theta})+ \beta_0 |\langle \psi(\vec{\theta})| \psi(\vec{\theta^0})\rangle  |^2
$$
</p>

양의 값 $\beta_0$는 이상적으로 $\lambda_1-\lambda_0$보다 커야 한다.

이것은 새로운 비용 함수를 도입하는 것으로, 이는 제약이 있는 문제로 볼 수 있는데, 여기서는 $C_\text{VQE}(\vec{\theta}) = \langle \psi(\vec{\theta}) \| \hat{H} \| \psi(\vec{\theta})\rangle$를 최소화하되, 상태가 이전에 얻은 $\|\psi(\vec{\theta^0})\rangle$와 직교해야 한다는 제약 조건을 만족시켜야 하며, $\beta_0$는 제약이 만족되지 않을 경우 페널티 항으로 작용한다.

또는, 이 새로운 문제는 다음의 새로운 관측가능량에 대해 VQE를 실행하는 것으로 해석될 수 있다:

<p>
$$
\hat{H_1} := \hat{H} + \beta_0 |\psi(\vec{\theta^0})\rangle \langle \psi(\vec{\theta^0})|
\quad \Rightarrow \quad
C_1(\vec{\theta}) = \langle \psi(\vec{\theta}) | \hat{H_1} | \psi(\vec{\theta})\rangle,
$$
</p>

새로운 문제의 해가 $\|\psi(\vec{\theta^1})\rangle$라고 가정하면, $\hat{H}$($\hat{H_1}$이 아님)의 기댓값은 $\langle \psi(\vec{\theta^1}) \| \hat{H} \| \psi(\vec{\theta^1})\rangle = \lambda_1$이어야 한다.

세 번째 고유값 $\lambda_2$를 얻기 위해 최적화할 비용 함수는:

<p>
$$
C_2(\vec{\theta}) :=
C_1(\vec{\theta}) + \beta_1 |\langle \psi(\vec{\theta})| \psi(\vec{\theta^1})\rangle  |^2
$$
</p>

여기서 $\beta_1$은 해 상태가 $\|\psi(\vec{\theta^0})\rangle$와 $\|\psi(\vec{\theta^1})\rangle$ 모두와 직교하도록 충분히 큰 양의 상수이다. 이는 이 요구 조건을 충족하지 않는 탐색 공간의 상태에 페널티를 부과하여, 효과적으로 탐색 공간을 제한한다. 따라서 새로운 문제의 최적 해는 $\lambda_2$에 해당하는 고유 상태가 되어야 한다.

이전 경우와 마찬가지로, 이 새로운 문제는 다음 관측 가능량에 대한 VQE로도 해석될 수 있다:

<p>
$$
\hat{H_2} := \hat{H_1} + \beta_1 |\psi(\vec{\theta^1})\rangle \langle \psi(\vec{\theta^1})|
\quad \Rightarrow \quad
C_2(\vec{\theta}) = \langle \psi(\vec{\theta}) | \hat{H_2} | \psi(\vec{\theta})\rangle.
$$
</p>

이 새로운 문제의 해가 $\|\psi(\vec{\theta^2})\rangle$라면, $\hat{H_2}$가 아닌 $\hat{H}$의 기댓값은 $ \langle \psi(\vec{\theta^2}) \| \hat{H} \| \psi(\vec{\theta^2})\rangle = \lambda_2$가 되어야 한다.

유사하게, $k$-번째 고유값 $\lambda_{k-1}$을 얻기 위해, 다음 비용 함수를 최소화하면 됩니다:

<p>
$$
C_{k-1}(\vec{\theta}) :=
C_{k-2}(\vec{\theta}) + \beta_{k-2} |\langle \psi(\vec{\theta})| \psi(\vec{\theta^{k-2}})\rangle  |^2,
$$
</p>

우리가 $\langle \psi(\vec{\theta^j}) \| \hat{H} \| \psi(\vec{\theta^j})\rangle = \lambda_j, \forall j<k$가 되도록 $\vec{\theta^j}$를 정의했음을 기억하세요. 이 문제는 $C(\vec{\theta}) = \langle \psi(\vec{\theta}) \| \hat{H} \| \psi(\vec{\theta})\rangle$를 최소화하는 것과 동일하지만, 상태가 $\|\psi(\vec{\theta^j})\rangle ; \forall j \in {0, \cdots, k-1}$에 대해 직교해야 하는 제약 조건이 있어, 탐색 공간을 고유값 $\{\lambda_{k-1},\cdots,\lambda_{N-1}\}$에 해당하는 부분공간으로 제한합니다.

이 문제는 다음 관측 가능량을 가진 VQE와 동일합니다:
<p>
$$
\hat{H}_{k-1} :=
\hat{H}_{k-2} + \beta_{k-2} |\psi(\vec{\theta^{k-2}})\rangle \langle \psi(\vec{\theta^{k-2}})|
\quad \Rightarrow \quad
C_{k-1}(\vec{\theta}) = \langle \psi(\vec{\theta}) | \hat{H}_{k-1} | \psi(\vec{\theta})\rangle,
$$
</p>

이 과정에서 보듯이, $k$-번째 고유값을 얻기 위해서는 이전 $k-1$개 고유값의 (근사) 고유 상태가 필요하므로, 총 $k$번의 VQE를 실행해야 합니다. 따라서 VQD의 비용 함수는 다음과 같습니다:

<p>
$$
C_k(\vec{\theta}) =
\langle \psi(\vec{\theta}) | \hat{H} | \psi(\vec{\theta})\rangle +
\sum_{j=0}^{k-1}\beta_j |\langle \psi(\vec{\theta})| \psi(\vec{\theta^j})\rangle |^2
$$
</p>

### 3.1 이론적 레이아웃

VQD의 레이아웃은 다음과 같이 요약할 수 있다:

* 기준 연산자 $U_R$을 준비한다.
* 기준 상태에 변분 형태 $U_V(\vec\theta_{i,j})$를 적용하여 다음의 ansatze $U_A(\vec\theta_{i,j})$를 생성한다.
* 유사한 문제가 있는 경우 $i=0$에서 부트스트랩한다(일반적으로 고전 시뮬레이션이나 샘플링을 통해 발견됨).
* $k$개의 여기 상태와 각 중첩 항에 대한 중첩 페널티를 정의하는 $\beta$ 배열을 포함하는 비용 함수 $C_k(\vec{\theta})$를 평가한다.
  * 각 $k$에 대해 관측 가능량 $\langle \psi_{j}(\vec{\theta}) \| \hat{H} \| \psi_{j}(\vec{\theta}) \rangle$의 기댓값을 계산한다.
  * 페널티 $\sum_{j=0}^{k-1}\beta_j \|\langle \psi(\vec{\theta})\| \psi(\vec{\theta^j})\rangle \|^2$를 계산한다.
  * 비용 함수는 이 두 항의 합을 반환해야 한다.
* 다음 매개변수 집합 $\Theta_{i+1}$을 선택하기 위해 고전적 최적화 기법을 사용한다.
* 수렴에 도달할 때까지 이 과정을 반복한다.

### 3.2 구현

이 구현에서는 중첩 페널티 함수를 생성합니다. 이 페널티는 각 반복에서 비용 함수에 사용됩니다. 이 과정은 각 여기 상태에 대해 반복됩니다.

```python
from qiskit.circuit.library import TwoLocal

ansatz = TwoLocal(2, rotation_blocks=["ry", "rz"], entanglement_blocks="cz", reps=1)

ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Finstances-and-extensions%2Fextracted-outputs%2F9d368de5-d512-4fe8-8842-9c735944b22b-0.avif&w=1080&q=75)

먼저, 두 상태 간의 중복 비율인 상태 충실도를 계산하는 함수를 설정하겠습니다. 이 값은 VQD에 대한 페널티로 사용됩니다.

```python
import numpy as np


def calculate_overlaps(ansatz, prev_circuits, parameters, sampler):
    def create_fidelity_circuit(circuit_1, circuit_2):
        """
        Constructs the list of fidelity circuits to be evaluated.
        These circuits represent the state overlap between pairs of input circuits,
        and their construction depends on the fidelity method implementations.
        """

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
        sampler_job = sampler.run([(fidelity_circuit, parameters)])
        meas_data = sampler_job.result()[0].data.meas

        counts_0 = meas_data.get_int_counts().get(0, 0)
        shots = meas_data.num_shots
        overlap = counts_0 / shots
        overlaps.append(overlap)

    return np.array(overlaps)
```

이제 VQD의 비용 함수를 작성할 차례입니다. 이전처럼 바닥 상태만 계산할 때와 마찬가지로, Estimator 프리미티브를 사용하여 가장 낮은 에너지 상태를 결정합니다. 하지만 위에서 설명한 것처럼, 이제 더 높은 에너지 상태들의 직교성을 보장하기 위해 페널티 항을 추가합니다. 즉, 새로운 여기 상태가 생성될 때마다 현재 변분 상태와 이미 발견된 더 낮은 에너지 고유 상태들 사이의 중복에 대해 페널티가 추가됩니다.

```python
def cost_func_vqd(
    parameters, ansatz, prev_states, step, betas, estimator, sampler, hamiltonian
):
    estimator_job = estimator.run([(ansatz, hamiltonian, [parameters])])

    total_cost = 0

    if step > 1:
        overlaps = calculate_overlaps(ansatz, prev_states, parameters, sampler)
        total_cost = np.sum(
            [np.real(betas[state] * overlap) for state, overlap in enumerate(overlaps)]
        )

    estimator_result = estimator_job.result()[0]

    value = estimator_result.data.evs[0] + total_cost

    return value
```

특히 위의 비용 함수는 실제로 새로운 양자 회로를 생성하는 `calculate_overlaps` 함수를 참조한다는 점에 유의하십시오. 실제 하드웨어에서 실행하려면 이 새로운 회로도 선택한 백엔드에서 실행될 수 있도록 최적화된 방식으로 트랜스파일링해야 합니다. 트랜스파일링은 `calculate_overlaps` 또는 `cost_func_vqd` 함수에 내장되어 있습니다. 직접 코드를 수정하여 이 추가적인 (조건부) 트랜스파일링을 구현해 볼 수도 있지만, 다음 강의에서 이 부분도 다룰 예정입니다.

이번 강의에서는 Statevector Sampler와 Statevector Estimator를 사용하여 VQD 알고리즘을 실행해 보겠습니다.

```python
from qiskit.primitives import StatevectorEstimator as Estimator

sampler = Sampler()
estimator = Estimator()
```

우리는 추정해야 할 관측량을 소개할 것입니다. 다음 시간에는 분자의 들뜬 상태와 같은 물리적 맥락을 추가해 보겠습니다. 이 관측량은 특정 분자나 원자에 맞춰 선택된 것은 아니지만, 들뜬 상태를 가질 수 있는 시스템의 해밀턴으로 생각하면 이해하기 쉬울 것입니다.

```python
from qiskit.quantum_info import SparsePauliOp

observable = SparsePauliOp.from_list([("II", 2), ("XX", -2), ("YY", 3), ("ZZ", -3)])
```

여기서는 계산하고자 하는 전체 상태 수(바닥 상태와 들뜬 상태, k)와 직교해야 하는 상태 벡터 간의 중첩에 대한 페널티(베타)를 설정합니다. 베타 값을 너무 높거나 낮게 선택했을 때의 결과는 다음 강의에서 좀 더 자세히 살펴보겠습니다. 지금은 아래에 제공된 값을 그대로 사용하겠습니다. 처음에는 모든 매개변수를 0으로 설정합니다. 실제 계산에서는 시스템에 대한 지식이나 이전 계산 결과를 바탕으로 더 적절한 초기 매개변수를 사용할 수 있습니다.

```python
k = 3
betas = [33, 33, 33]
x0 = np.zeros(8)
```

이제 계산을 실행할 수 있습니다.

```python
from scipy.optimize import minimize

prev_states = []
prev_opt_parameters = []
eigenvalues = []

for step in range(1, k + 1):
    if step > 1:
        prev_states.append(ansatz.assign_parameters(prev_opt_parameters))

    result = minimize(
        cost_func_vqd,
        x0,
        args=(ansatz, prev_states, step, betas, estimator, sampler, observable),
        method="COBYLA",
        options={
            "maxiter": 200,
        },
    )
    print(result)

    prev_opt_parameters = result.x
    eigenvalues.append(result.fun)
```

```bash
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: -5.999999979545955
       x: [-5.150e-01 -5.452e-02 -1.571e+00 -2.853e-05  2.671e-01
           -2.672e-01 -8.509e-01 -8.510e-01]
    nfev: 131
   maxcv: 0.0
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: 4.024550284767612
       x: [-3.745e-01  1.041e+00  8.637e-01  1.202e+00 -8.847e-02
            1.181e-02  7.611e-01 -3.006e-01]
    nfev: 110
   maxcv: 0.0
 message: Optimization terminated successfully.
 success: True
  status: 1
     fun: 5.608925562838559
       x: [-2.670e-01  1.280e+00  1.070e+00 -8.031e-01 -1.524e-01
           -6.956e-02  7.018e-01  1.514e+00]
    nfev: 90
   maxcv: 0.0
```

비용 함수에서 얻은 값은 대략 -6.00, 4.02, 5.61입니다. 이 결과에서 중요한 점은 함수 값이 증가한다는 것입니다. 만약 우리가 얻은 첫 번째 들뜬 상태의 에너지가 초기 제약 조건 없는 계산으로 얻은 바닥 상태의 에너지보다 낮았다면, 코드 어딘가에 오류가 있음을 의미했을 것입니다.

x 값은 이러한 비용(에너지) 각각에 해당하는 상태 벡터를 생성하는 데 사용된 매개변수입니다.

마지막으로, 세 가지 최소화 과정 모두 고전적 최적화 알고리즘(여기서는 COBYLA)의 기본 허용 오차 범위 내에서 수렴했습니다. 각각 131회, 110회, 90회의 함수 평가가 필요했습니다.



## 4. 양자 샘플링 회귀(QSR)

VQE의 주요 문제점 중 하나는 각 단계의 매개변수를 얻기 위해 양자 컴퓨터를 여러 번 호출해야 한다는 것입니다. 예를 들어, $k$, $k-1$ 등의 매개변수를 얻기 위해 말입니다. 이는 특히 양자 장치에 대한 접근이 대기열에 의해 관리될 때 문제가 됩니다. `Session`을 사용하여 여러 번의 반복적 호출을 그룹화할 수 있지만, 대안으로 샘플링을 사용하는 방법도 있습니다. 더 많은 고전적 자원을 활용하여 전체 최적화 과정을 단일 호출로 완료할 수 있습니다. 이것이 바로 [양자 샘플링 회귀](https://arxiv.org/pdf/2012.02338)가 등장하는 곳입니다. 양자 컴퓨터에 대한 접근이 여전히 공급 부족/수요 과잉의 상품이기 때문에, 우리는 이러한 절충안이 많은 현재 연구에서 가능하고 편리하다고 생각합니다. 이 접근 방식은 사용 가능한 모든 고전적 기능을 활용하면서도 시뮬레이션에서는 나타나지 않는 양자 계산의 많은 내부 작동 방식과 고유한 특성을 포착합니다.

![변분 알고리즘의 구성 요소를 QSR이 어떻게 사용하는지 보여주는 다이어그램.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/instances-and-extensions/instances-qsr.svg)

QSR의 핵심 아이디어는 비용 함수 $C(\theta) := \langle \psi(\theta) \| \hat{H} \| \psi(\theta)\rangle$가 다음과 같은 방식으로 푸리에 급수로 표현될 수 있다는 것입니다:

<p>
$$
\begin{aligned}
C(\vec{\theta})
& := \langle \psi(\theta) | \hat{H} | \psi(\theta)\rangle \\[1mm]
& := a_0 + \sum_{k=1}^S[a_k\cos(k\theta)+ b_k\sin(k\theta)] \\[1mm]
\end{aligned}
$$
</p>

원래 함수의 주기성과 대역폭에 따라, 집합 $S$는 유한하거나 무한할 수 있습니다. 본 논의의 목적상, 이를 무한하다고 가정하겠습니다. 다음 단계는 푸리에 계수 $\{a_0, a_k, b_k\}_{k=1}^S$를 얻기 위해 비용 함수 $C(\theta)$를 여러 번 샘플링하는 것입니다. 구체적으로, $2S+1$개의 미지수가 있으므로 비용 함수를 $2S+1$번 샘플링해야 합니다.

그런 다음 $2S+1$개의 매개변수 값 $\{\theta_1,...,\theta_{2S+1}\}$에 대해 비용 함수를 샘플링하면, 다음과 같은 체계를 얻을 수 있습니다:

<p>
$$
\begin{pmatrix} 1 & \cos(\theta_1) & \sin(\theta_1) & \cos(2\theta_1) & ... & \sin(S\theta_1) \\
1 & \cos(\theta_2) & \sin(\theta_2) & \cos(2\theta_2) & \cdots & \sin(S\theta_2)\\
\vdots & \vdots & \vdots & \vdots & \ddots & \vdots\\
1 & \cos(\theta_{2S+1}) & \sin(\theta_{2S+1}) & \cos(2\theta_{2S+1}) & \cdots & \sin(S\theta_{2S+1})
\end{pmatrix} \begin{pmatrix} a_0 \\ a_1 \\ b_1 \\ a_2 \\ \vdots \\ b_S \end{pmatrix} = \begin{pmatrix} C(\theta_1) \\ C(\theta_2) \\ \vdots \\ C(\theta_{2S+1}) \end{pmatrix},
$$
</p>

이를 다음과 같이 다시 쓰면
<p>
$$
Fa=c.
$$
</p>

실제로 이 체계는 일반적으로 일관되지 않습니다. 왜냐하면 비용 함수 값 $c$가 정확하지 않기 때문입니다. 따라서 보통 좌측에 $F^\dagger$를 곱하여 정규화하는 것이 좋습니다. 그 결과는 다음과 같습니다:

<p>
$$
F^\dagger Fa = F^\dagger c.
$$
</p>

이 새로운 체계는 항상 일관되며, 그 해는 원래 문제에 대한 최소제곱해입니다. 만약 매개변수가 하나가 아닌 $k$개이고, 각 매개변수 $\theta^i$가 $i \in {1,...,k}$에 대해 자신만의 $S_i$를 가진다면, 필요한 총 샘플 수는 다음과 같습니다:

<p>
$$
T=\prod_{i=1}^k(2S_i+1)\leq \prod_{i=1}^k(2S_{max}+1) = (2S_{max}+1)^n,
$$
</p>

여기서 $S_{\max} = \max_i(S_i)$입니다. 또한 $S_{\max}$를 추론하는 대신 조정 가능한 매개변수로 설정하면 다음과 같은 새로운 가능성이 열립니다:

* **과샘플링(over-sampling)** 을 통해 정확도를 향상시킵니다.
* **부족 샘플링(under-sampling)** 을 통해 런타임 오버헤드를 줄이거나 국소 최소값을 제거하여 성능을 향상시킵니다.

### 4.1 이론적 구조

QSR의 구조는 다음과 같이 요약할 수 있다:

* 참조 연산자 $U_R$을 준비한다.
  * 상태 $\|0\rangle$에서 참조 상태 $\|\rho\rangle$로 이동한다.
* ansatz $U_A(\vec\theta_{i,j})$를 생성하기 위해 변분 형태 $U_V(\vec\theta_{i,j})$를 적용한다.
  * ansatz의 각 매개변수에 연관된 대역폭을 결정한다. 상한만으로도 충분하다.
* 유사한 문제가 있는 경우(일반적으로 고전 시뮬레이션이나 샘플링을 통해 발견됨) $i=0$에서 부트스트랩한다.
* 비용 함수 $C(\vec\theta) := a_0 + \sum_{k=1}^S[a_k\cos(k\theta)+ b_k\sin(k\theta)]$를 최소 $T$회 샘플링한다.
  * $T=\prod_{i=1}^k(2S_i+1)\leq \prod_{i=1}^k(2S_{max}+1) = (2S_{max}+1)^n$
  * $T$를 조정하여 속도와 정확도의 균형을 맞추기 위해 과표본/부족표본 여부를 결정한다.
* 샘플로부터 푸리에 계수를 계산한다(즉, 정규화된 선형 방정식을 푼다).
* 결과 회귀 함수의 전역 최솟값을 고전 컴퓨터에서 구한다.

## 5. 요약

이번 강의에서는 다음과 같은 다양한 변분 알고리즘에 대해 알아보았습니다.

* 일반적인 구조
* 비용 함수를 조정하기 위한 가중치 및 페널티 도입
* 속도와 정확도 사이의 균형을 맞추기 위한 언더샘플링과 오버샘플링 탐구

이러한 개념들을 바탕으로 여러분의 문제에 맞는 맞춤형 변분 알고리즘을 만들 수 있습니다. 여러분의 결과를 커뮤니티와 공유해 주시기 바랍니다. 다음 강의에서는 변분 알고리즘을 사용하여 실제 응용 문제를 해결하는 방법을 살펴보겠습니다.