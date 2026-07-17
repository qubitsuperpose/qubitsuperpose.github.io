---
title: 16차시 5:Variational Algorithms 5
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 5. 최적화 루프

이 강의에서는 *최적화기*를 사용하여 ansatz의 매개변수화된 양자 상태를 반복적으로 탐색하는 방법을 배울 것입니다:

* 최적화 루프 부트스트래핑
* 지역 및 전역 최적화기 사용 시의 절충 이해
* 황폐한 고원(barren plateaus) 탐색 및 회피 방법

높은 수준에서 볼 때, 최적화기는 탐색 공간을 탐색하는 데 핵심적입니다. 최적화기는 비용 함수 평가를 사용하여 변분 루프에서 다음 매개변수 집합을 선택하고, 안정 상태에 도달할 때까지 이 과정을 반복합니다. 이 단계에서 최적의 매개변수 값 집합 $\vec\theta^*$가 반환됩니다.

![최적화의 중요한 요인들을 보여주는 다이어그램. 황폐한 고원, 기울기 기반 대 기울기 없는 최적화기, 부트스트래핑 등이 포함됩니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/optimization-loops/optimization-workflow.svg)

## 1. 지역 최적화 및 전역 최적화

각 최적화 알고리즘을 살펴보기 전에 먼저 문제를 설정하겠습니다. 8개의 변분 매개변수를 포함하는 회로로 시작하겠습니다.

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import SparsePauliOp
from qiskit.circuit.library import TwoLocal
import numpy as np

theta_list = (2 * np.pi * np.random.rand(1, 8)).tolist()
observable = SparsePauliOp.from_list([("XX", 1), ("YY", -3)])

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

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Foptimization-loops%2Fextracted-outputs%2F15cf7810-1d25-4c54-aff0-91d3a0c51cec-0.avif&w=1080&q=75)

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
from qiskit.primitives import StatevectorEstimator

estimator = StatevectorEstimator()
```



### 1.1 로컬 최적화 알고리즘

로컬 최적화 알고리즘은 초기점 $C(\vec{\theta_0})$에서 시작하여 비용 함수를 최소화하는 지점을 찾고, 연속적인 반복 과정에서 현재 평가 영역에서 관찰되는 결과를 바탕으로 다른 지점으로 이동합니다. 따라서 이러한 알고리즘은 일반적으로 수렴 속도가 빠르지만, 초기점에 크게 의존할 수 있습니다. 로컬 최적화 알고리즘은 평가 영역 너머를 볼 수 없기 때문에 특히 지역 최소값에 취약하며, 최소값을 찾으면 수렴했다고 보고하고 더 유리한 평가 결과를 가진 다른 상태를 무시할 수 있습니다.

```python
# SciPy minimizer routine
from scipy.optimize import minimize

x0 = np.ones(8)

result = minimize(
    cost_func_vqe, x0, args=(ansatz, observable, estimator), method="SLSQP"
)

result
```

```bash
 message: Optimization terminated successfully
 success: True
  status: 0
     fun: -3.9999999964520634
       x: [ 1.000e+00  1.000e+00 -1.571e+00 -4.556e-05 -1.207e+00
           -1.935e+00  4.079e-01 -4.079e-01]
     nit: 12
     jac: [ 0.000e+00  0.000e+00 -7.957e-04  2.543e-04  1.381e-03
            1.381e-03  5.430e-04  5.431e-04]
    nfev: 112
    njev: 12
```



### 1.2 Global optimizers

Global optimizers는 비용 함수의 정의역에 걸쳐 여러 영역에서(즉, 비국소적으로) 비용 함수를 최소화하는 점을 탐색하며, 최적화기에 의해 결정된 매개변수 벡터 집합 $\Theta_i := \\{ {\vec\theta_{i,j} \| j \in \mathcal{J}_\text{opt}^i} \\}$ 위에서 반복적으로(즉, 반복 $i$에서) 이를 평가한다. 이로 인해 이들은 지역 최소값에 덜 민감하고 초기화에 다소 독립적이지만, 제안된 해로 수렴하는 데 현저히 느리다.

### 1.3 부트스트래핑 최적화

*부트스트래핑*, 즉 사전 최적화 결과를 바탕으로 매개변수 $\vec\theta$의 초기값을 설정하는 것은 옵티마이저가 더 빠르게 수렴하도록 도울 수 있다. 이를 초기점 $\vec\theta_0$이라 하며, $\|\psi(\vec\theta_0)\rangle = U_V(\vec\theta_0)\|\rho\rangle$를 초기 상태라 한다. 이 초기 상태는 기준 상태 $\|\rho\rangle$와 다르다. 전자는 최적화 루프에서 설정된 초기 매개변수에 초점을 맞추고, 후자는 알려진 "기준" 해에 초점을 맞추기 때문이다. $U_V(\vec\theta_0) \equiv I$ (즉, 항등 연산)인 경우에는 둘이 일치할 수 있다.

지역 옵티마이저가 비최적인 지역 최소값에 수렴할 때, 전역적으로 최적화를 부트스트래핑하고 지역적으로 수렴을 정제하는 방법을 시도할 수 있다. 이는 두 개의 변분 워크로드를 설정해야 하지만, 지역 옵티마이저만 사용하는 경우보다 더 최적인 해를 찾을 수 있게 해준다.

## 2. 기울기 기반 및 기울기 없는 최적화 알고리즘

### 2.1 기울기 기반

비용 함수 $C(\vec\theta)$에 대해, 초기 지점부터 함수의 기울기 $\vec{\nabla} C(\vec\theta)$에 접근할 수 있다면, 함수를 최소화하는 가장 간단한 방법은 함수의 가장 가파른 하강 방향으로 매개변수를 업데이트하는 것이다. 즉, $\vec\theta_{n+1} = \vec\theta_n - \eta \vec{\nabla} C(\vec\theta)$와 같이 매개변수를 업데이트하며, 여기서 $\eta$는 학습률로 — 업데이트의 크기를 제어하는 작은 양수 하이퍼파라미터(하이퍼파라미터는 우리의 알고리즘을 제어하는 데 사용하는 매개변수이다. hyper라는 용어는 알고리즘이 찾고자 하는 매개변수θ와 구분하기 위한 것)이다. 이를 비용 함수의 국소 최솟값(국소 최솟값은 θ 값의 작은 범위에 대해 함수의 가장 낮은 지점이다. 이와 대조적으로, 전역 최솟값은 함수의 어떤 지점(즉, θ의 어떤 값에 대해서도)에서 가장 낮은 지점) $C({\vec\theta^*})$에 수렴할 때까지 계속한다.

우리는 이 비용 함수와 최적화 도구를 사용하여 최적의 매개변수를 계산할 수 있습니다.

```python
# SciPy minimizer routine
from scipy.optimize import minimize

x0 = np.ones(8)

result = minimize(
    cost_func_vqe, x0, args=(ansatz, observable, estimator), method="BFGS"
)

result
```

```bash
  message: Optimization terminated successfully.
  success: True
   status: 0
      fun: -3.9999999999997025
        x: [ 1.000e+00  1.000e+00  1.571e+00  3.220e-07  2.009e-01
            -2.009e-01  6.342e-01 -6.342e-01]
      nit: 14
      jac: [-1.192e-07 -2.980e-08  8.345e-07  1.103e-06  5.960e-08
             0.000e+00 -5.960e-08  2.980e-08]
 hess_inv: [[ 1.000e+00  1.872e-10 ...  5.077e-05  3.847e-05]
            [ 1.872e-10  1.000e+00 ... -5.208e-05 -4.060e-05]
            ...
            [ 5.077e-05 -5.208e-05 ...  7.243e-01 -2.604e-01]
            [ 3.847e-05 -4.060e-05 ... -2.604e-01  8.179e-01]]
     nfev: 144
     njev: 16
```

이러한 유형의 최적화의 주요 단점은 수렴 속도가 매우 느릴 수 있으며 최적해를 찾는다는 보장이 없다는 것입니다.

![f(theta)와 theta의 그래프. 여러 개의 점은 곡선의 최소값을 찾는 경사 하강 알고리즘의 다양한 상태를 나타냅니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/optimization-loops/optimization-gradient-descent.svg)

### 2.2 경사 정보 없는 최적화

경사 정보 없는 최적화 알고리즘은 경사 정보를 필요로 하지 않으며, 경사 계산이 어렵거나 비용이 많이 들거나 노이즈가 심한 상황에서 유용할 수 있습니다. 또한 경사 기반 방법이 지역 최적해로 수렴하는 경향이 있는 반면, 경사 정보 없는 방법은 전역 최적해를 찾는 데 더 안정적입니다. 경사 정보 없는 최적화 알고리즘이 불모지(bare plateau)를 피하는 데 도움이 될 수 있는 몇 가지 사례를 살펴보겠습니다. 그러나 경사 정보 없는 방법은 특히 탐색 공간의 차원이 높은 문제의 경우 더 많은 계산 자원을 필요로 합니다.

다음은 [`COBYLA`](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-cobyla.html#optimize-minimize-cobyla) 최적화 도구를 사용하는 예입니다.

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
     fun: -3.999999973369678
       x: [ 1.631e+00  1.492e+00  1.571e+00  3.142e+00  1.375e+00
           -1.767e+00  1.484e+00  1.658e+00]
    nfev: 137
   maxcv: 0.0
```



## 3. 황폐한 고원(barren plateau)

실제로, 아래 예시의 언덕과 골짜기에서 보듯이, 비용 경관은 꽤 복잡할 수 있다. 최적화 방법은 검은 점과 선으로 표시된 것처럼, 비용 경관을 돌아다니며 최솟값을 찾는다. 세 번의 탐색 중 두 번이 전역 최솟값이 아닌 지역 최솟값에서 끝나는 것을 볼 수 있다.

![많은 봉우리와 골짜기가 있는 복잡한 곡면 다양체.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/optimization-loops/optimization-loss-landscape.svg)

사용된 최적화 방법의 유형에 관계없이, 비용 경관이 상대적으로 평평하다면, 방법이 탐색할 적절한 방향을 결정하기 어려울 수 있다. 이 시나리오는 황폐한 고원(매개변수화된 양자 회로의 기울기가 큐비트 수에 대해 지수적으로 작아져 최적화를 어렵게 하고 잠재적으로 불가능하게 만드는 경우)이라고 하며, 비용 경관이 점점 더 평평해져(따라서 최솟값으로의 방향을 결정하기 더 어려워진다). 광범위한 범위의 매개변수화된 양자 회로에 대해, 어떤 합리적인 방향을 따라 기울기가 어떤 고정된 정밀도로 0이 아닐 확률은 큐비트 수가 증가함에 따라 지수적으로 감소한다.

![지리적 고원과 산의 경사를 비교하는 도표. 기울기가 최솟값을 찾는 데 어떻게 도움이 되고 고원이 어떻게 노력을 방해하는지 설명한다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/optimization-loops/optimization-barren-plateaus.svg)

이 영역은 여전히 활발히 연구 중이지만, 최적화 성능을 향상시키기 위한 몇 가지 권장 사항이 있다:

* **부트스트래핑(Bootstrapping)**은 최적화 루프가 기울기가 작은 매개변수 공간에 갇히는 것을 피하는 데 도움이 될 수 있다.
* **하드웨어 효율적인 ansatz 실험**: 노이즈가 있는 양자 시스템을 블랙박스 오라클로 사용하기 때문에, 그 평가의 품질이 최적화 도구의 성능에 영향을 미칠 수 있다. [`EfficientSU2`](/docs/api/qiskit/qiskit.circuit.library.EfficientSU2)와 같은 하드웨어 효율적인 ansatz를 사용하면 지수적으로 작은 기울기를 생성하는 것을 피할 수 있다.
* **오류 억제 및 오류 완화 실험**: Qiskit Runtime primitives는 각각 `optimization_level`과 `resilience_setting`에 대한 다양한 값을 실험할 수 있는 간단한 인터페이스를 제공한다. 이는 노이즈의 영향을 줄이고 최적화 과정을 더 효율적으로 만들 수 있다.
* **무기울기 최적화 도구 실험**: 기울기 기반 최적화 알고리즘과 달리, `COBYLA`와 같은 최적화 도구는 매개변수를 최적화하기 위해 기울기 정보에 의존하지 않으므로 황폐한 고원의 영향을 덜 받을 가능성이 있다.

## 4. 요약

이번 강의에서는 최적화 루프를 정의하는 방법을 배웠습니다.

* 최적화 루프 부트스트랩
* 로컬 최적화와 전역 최적화 사용 시의 장단점 이해
* 최적화가 어려운 구간(barren plateau) 탐색 및 회피 방법

이번 강의에서 다룬 고수준 변분 워크로드가 완료되었습니다.

![A quantum circuit now with both a unitary to prepare the reference state, and a second unitary to vary the state using variational parameters.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/optimization-loops/optimization-circuit.svg)

다음으로, 이러한 틀을 염두에 두고 구체적인 변분 알고리즘을 살펴보겠습니다.
