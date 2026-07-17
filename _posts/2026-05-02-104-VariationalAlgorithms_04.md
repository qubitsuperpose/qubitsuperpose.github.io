---
title: 16차시 4:Variational Algorithms 4
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 4. Cost functions

이번 수업에서는 *cost function*을 평가하는 방법을 배웁니다:

* 먼저, [Qiskit Runtime primitives](/docs/guides/primitives)에 대해 배웁니다
* *cost function* $C(\vec\theta)$를 정의합니다. 이는 최적화기가 최소화(또는 최대화)할 문제의 목표를 정의하는 문제별 함수입니다
* 속도와 정확성 사이의 균형을 위해 Qiskit Runtime primitives로 측정 전략을 정의합니다

![cost function의 핵심 구성 요소를 보여주는 다이어그램으로, estimator와 sampler와 같은 primitive 사용을 포함합니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/cost-functions/cost-function-workflow.svg)

## 1. Primitives

모든 물리 시스템은 고전적이든 양자적이든 서로 다른 상태를 가질 수 있다. 예를 들어, 도로 위의 자동차는 질량, 위치, 속도 또는 가속도와 같은 특정 물리적 특성으로 그 상태를 특징지을 수 있다. 마찬가지로 양자 시스템도 서로 다른 구성이나 상태를 가질 수 있지만, 측정과 상태의 시간적 변화를 다루는 방식에서 고전 시스템과 다르다. 이는 양자역학에만 존재하는 *중첩(superposition)*과 *얽힘(entanglement)*과 같은 독특한 성질로 이어진다. 속도나 가속도와 같은 물리적 특성을 사용하여 자동차의 상태를 기술할 수 있는 것처럼, *관측가능량(observables)*이라는 수학적 대상을 사용하여 양자 시스템의 상태를 기술할 수도 있다.

양자역학에서, 상태는 정규화된 복소수 열 벡터, 즉 *켓(ket)* ($\|\psi\rangle$)으로 표현되며, 관측가능량은 켓에 작용하는 에르미트 선형 연산자 ($\hat{H}=\hat{H}^{\dagger}$)이다. 관측가능량의 고유벡터 ($\|\lambda\rangle$)는 *고유상태(eigenstate)*라고 한다. 고유상태 ($\|\lambda\rangle$)에 대해 관측가능량을 측정하면 해당하는 고유값 ($\lambda$)을 판독값으로 얻는다.

양자 시스템을 어떻게 측정하고 무엇을 측정할 수 있는지 궁금하다면, Qiskit이 제공하는 두 가지 primitives(기본적이고 근원적인 연산 또는 데이터 타입. Qiskit에는 Sampler와 Estimator라는 프리미티브가 있어 복잡한 알고리즘 작업부하를 쉽게 구성할 수 있는 구성 요소로 작용한다)가 도움이 될 수 있다:

* `Sampler`: 양자 상태 $\|\psi\rangle$가 주어졌을 때, 이 프리미티브는 각 가능한 계산 기저 상태의 확률을 얻는다.
* `Estimator`: 양자 관측가능량 $\hat{H}$와 상태 $\|\psi\rangle$가 주어졌을 때, 이 프리미티브는 $\hat{H}$의 기댓값을 계산한다.

### 1.1 Sampler 프리미티브

`Sampler` 프리미티브는 상태 $\|\psi\rangle$를 준비하는 양자 회로가 주어졌을 때, 계산 기저로부터 각 가능한 상태 $\|k\rangle$를 얻을 확률을 계산합니다. 이는 다음을 계산합니다.

<p>
$$
p_k = |\langle k | \psi \rangle|^2 \quad \forall k \in \mathbb{Z}_2^n \equiv \{0,1,\cdots,2^n-1\},
$$
</p>

여기서 $n$은 큐비트의 수이고, $k$는 임의의 가능한 출력 이진 문자열 $\{0,1\}^n$의 정수 표현(즉, $2$진법 정수)입니다.


Qiskit Runtime `Sampler`는 회로를 양자 장치에서 여러 번 실행하여, 각 실행마다 측정을 수행하고, 복원된 비트열로부터 확률 분포를 재구성합니다. 실행 횟수(또는 *샷*)가 많을수록 결과는 더 정확해지지만, 이는 더 많은 시간과 양자 자원을 필요로 합니다.

그러나 가능한 출력의 수가 큐비트 수 $n$에 대해 지수적으로 증가하기 때문에(즉, $2^n$), *밀집된* 확률 분포를 포착하기 위해서는 샷의 수 역시 지수적으로 증가해야 합니다. 따라서 `Sampler`는 *희소한* 확률 분포에 대해서만 효율적입니다; 여기서 목표 상태 $\|\psi\rangle$는 계산 기저 상태들의 선형 결합으로 표현 가능해야 하며, 항의 수는 큐비트 수에 대해 최대 다항식적으로 증가해야 합니다:

<p>
$$
|\psi\rangle = \sum^{\text{Poly}(n)}_k w_k |k\rangle.
$$
</p>

`Sampler`는 또한 회로의 부분 구간으로부터 확률을 검색하도록 구성할 수 있으며, 이는 전체 가능한 상태들의 부분 집합을 나타냅니다.

### 1.2 Estimator 원시 연산

`Estimator` 원시 연산은 관측가능량 $\hat{H}$에 대한 양자 상태 $\|\psi\rangle$의 기대값을 계산한다; 여기서 관측가능 확률은 $p_\lambda = \|\langle\lambda\|\psi\rangle\|^2$로 표현할 수 있으며, $\|\lambda\rangle$는 관측가능량 $\hat{H}$의 고유상태이다. 기대값은 상태 $\|\psi\rangle$의 측정 결과인 모든 가능한 결과 $\lambda$(즉, 관측가능량의 고유값)를 해당 확률로 가중 평균한 값으로 정의된다:

<p>
$$
\langle\hat{H}\rangle_\psi := \sum_\lambda p_\lambda \lambda = \langle \psi | \hat{H} | \psi \rangle
$$
</p>

그러나 관측가능량의 기대값을 계산하는 것이 항상 가능한 것은 아니다. 왜냐하면 우리는 종종 그 고유기저를 알지 못하기 때문이다. Qiskit Runtime `Estimator`는 관측가능량을 우리가 실제로 알고 있는 고유기저를 가진 다른 관측가능량들의 조합으로 분해함으로써, 실제 양자 장치에서 기대값을 추정하는 복잡한 대수적 과정을 사용한다.

더 간단히 말하면, `Estimator`는 측정 방법을 모르는 임의의 관측가능량을 파울리 연산자(양자 컴퓨팅에서 양자 상태를 표현하고 조작하는 데 일반적으로 사용되는 행렬 집합으로, 단위 행렬과 세 개의 파울리 행렬 X, Y, Z로 구성)라고 불리는 더 간단하고 측정 가능한 관측가능량으로 분해한다.

임의의 연산자는 $4^n$개의 Pauli 연산자의 조합으로 표현될 수 있다.

<p>
$$
\hat{P}_k :=
\sigma_{k_{n-1}}\otimes \cdots \otimes \sigma_{k_0} \quad
\forall k \in \mathbb{Z}_4^n \equiv \{0,1,\cdots,4^n-1\}, \\
$$
</p>
이를 통해

<p>
$$
\hat{H} = \sum^{4^n-1}_{k=0} w_k \hat{P}_k
$$
</p>

여기서 $n$은 큐비트의 수, $k \equiv k_{n-1} \cdots k_0$ (단, $k_l \in \mathbb{Z}_4 \equiv \{0, 1, 2, 3\}$, 즉 4진법 정수)이며, $(\sigma_0, \sigma_1, \sigma_2, \sigma_3) := (I, X, Y, Z)$이다.

이 분해를 수행한 후, `Estimator`는 각 관측가능량 $\hat{P}_k$에 대해 (원래 회로로부터) 새로운 회로 $V_k\|\psi\rangle$를 도출하여, 계산 기저에서 Pauli 관측가능량을 효과적으로 *대각화*하고 측정한다. 우리는 다른 관측가능량의 경우와 달리 $V_k$를 미리 알고 있으므로, Pauli 관측가능량을 쉽게 측정할 수 있다.

각 $$\hat{P}_{k}$$에 대해, `Estimator`는 해당 회로를 양자 장치에서 여러 번 실행하고, 계산 기저에서 출력 상태를 측정하며, 각 가능한 출력 $j$를 얻을 확률 $p_{kj}$를 계산한다. 그런 다음 각 출력 $j$에 대응하는 $P_k$의 고유값 $\lambda_{kj}$를 찾아 $w_k$를 곱하고, 모든 결과를 더하여 주어진 상태 $\|\psi\rangle$에 대한 관측가능량 $\hat{H}$의 기대값을 얻는다.

<p>
$$
\langle\hat{H}\rangle_\psi =
\sum_{k=0}^{4^n-1} w_k \sum_{j=0}^{2^n-1}p_{kj} \lambda_{kj},
$$
</p>


$4^n$개 Pauli들의 기대값을 계산하는 것은 비현실적이므로(즉, 지수적으로 증가함), `Estimator`는 다수의 $w_k$가 0일 때(즉, *밀집(dense)*이 아닌 *희소(sparse)* Pauli 분해)에만 효율적일 수 있다. 형식적으로, 이 계산이 *효율적으로 해결 가능*하려면, 0이 아닌 항의 수가 큐비트 수 $n$에 대해 다항식으로 최대한 증가해야 한다: $\hat{H} = \sum^{\text{Poly}(n)}_k w_k \hat{P}_k.$

독자는 확률 샘플링(하나 또는 여러 것들에 대한 여러 측정을 수행하는 과정) 또한 `Sampler`에 대해 설명된 바와 같이 효율적이어야 한다는 암묵적 가정을 눈치챘을 것이다. 이는

<p>
$$
\langle\hat{H}\rangle_\psi =
\sum_{k}^{\text{Poly}(n)} w_k \sum_{j}^{\text{Poly}(n)}p_{kj} \lambda_{kj}.
$$
</p>

### 1.3 기대값 계산을 위한 안내 예시

단일 큐비트 상태 $\|+\rangle := H\|0\rangle = \frac{1}{\sqrt{2}}(\|0\rangle + \|1\rangle)$과 관측가능량

<p>
$$
\begin{aligned}
\hat{H}
& = \begin{pmatrix}
-1 & 2 \\
2 & 1 \\
\end{pmatrix}\\[1mm]
& = 2X - Z
\end{aligned}
$$
</p>

를 가정하자. 이에 대한 이론적 기대값은 $\langle\hat{H}\rangle_+ = \langle+\|\hat{H}\|+\rangle = 2$이다.

이 관측가능량을 어떻게 측정하는지 알지 못하므로, 기대값을 직접 계산할 수 없고, 이를 $\langle\hat{H}\rangle_+ = 2\langle X \rangle_+ - \langle Z \rangle_+$로 재표현해야 한다. $\langle+\|X\|+\rangle = 1$이고 $\langle+\|Z\|+\rangle = 0$임을 이용하면 동일한 결과가 나오는 것을 확인할 수 있다.

$\langle X \rangle_+$와 $\langle Z \rangle_+$를 직접 계산하는 방법을 살펴보자. $X$와 $Z$는 교환하지 않으므로(즉, 같은 고유기저를 공유하지 않으므로) 동시에 측정할 수 없으므로, 보조 회로가 필요하다:

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import SparsePauliOp

# The following code will work for any other initial single-qubit state and observable
original_circuit = QuantumCircuit(1)
original_circuit.h(0)

H = SparsePauliOp(["X", "Z"], [2, -1])

aux_circuits = []
for pauli in H.paulis:
    aux_circ = original_circuit.copy()
    aux_circ.barrier()
    if str(pauli) == "X":
        aux_circ.h(0)
    elif str(pauli) == "Y":
        aux_circ.sdg(0)
        aux_circ.h(0)
    else:
        aux_circ.id(0)
    aux_circ.measure_all()
    aux_circuits.append(aux_circ)

original_circuit.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2F7c32e970-f9cb-48ec-a60b-2dccb0744141-0.avif&w=384&q=75)

```python
# Auxiliary circuit for X
aux_circuits[0].draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2Fec3cf7af-66b2-4ea9-94d7-6ec6ba2bfc5e-0.avif&w=1080&q=75)

```python
# Auxiliary circuit for Z
aux_circuits[1].draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2F678d3bd0-d8fd-4767-b0a6-77cae945810a-0.avif&w=1080&q=75)

이제 `Sampler`를 사용하여 수동으로 계산을 수행하고 `Estimator`에서 결과를 확인할 수 있습니다.

```python
from qiskit.primitives import StatevectorSampler, StatevectorEstimator
from qiskit.result import QuasiDistribution
import numpy as np


## SAMPLER
shots = 10000
sampler = StatevectorSampler()
job = sampler.run(aux_circuits, shots=shots)

# Run the sampler job and step through results
expvals = []
for index, pauli in enumerate(H.paulis):
    data_pub = job.result()[index].data
    bitstrings = data_pub.meas.get_bitstrings()
    counts = data_pub.meas.get_counts()
    quasi_dist = QuasiDistribution(
        {outcome: freq / shots for outcome, freq in counts.items()}
    )

    # Use the probabilities and known eigenvalues of Pauli operators to estimate
    # the expectation value.
    val = 0

    if str(pauli) == "X":
        val += -1 * quasi_dist.get(1, 0)
        val += 1 * quasi_dist.get(0, 0)

    if str(pauli) == "Y":
        val += -1 * quasi_dist.get(1, 0)
        val += 1 * quasi_dist.get(0, 0)

    if str(pauli) == "Z":
        val += 1 * quasi_dist.get(0, 0)
        val += -1 * quasi_dist.get(1, 0)

    expvals.append(val)

# Print expectation values

print("Sampler results:")
for pauli, expval in zip(H.paulis, expvals):
    print(f"  >> Expected value of {str(pauli)}: {expval:.5f}")

total_expval = np.sum(H.coeffs * expvals).real
print(f"  >> Total expected value: {total_expval:.5f}")

# Use estimator for comparison
observables = [
    *H.paulis,
    H,
]  # Note: run for individual Paulis as well as full observable H

estimator = StatevectorEstimator()
job = estimator.run([(original_circuit, observables)])
estimator_expvals = job.result()[0].data.evs

# Print results
print("Estimator results:")
for obs, expval in zip(observables, estimator_expvals):
    if obs is not H:
        print(f"  >> Expected value of {str(obs)}: {expval:.5f}")
    else:
        print(f"  >> Total expected value: {expval:.5f}")
```

```bash
Sampler results:
  >> Expected value of X: 1.00000
  >> Expected value of Z: 0.00420
  >> Total expected value: 1.99580
Estimator results:
  >> Expected value of X: 1.00000
  >> Expected value of Z: 0.00000
  >> Total expected value: 2.00000
```



### 1.4 Mathematical rigor (optional)
### Mathematical rigor (optional)

$\hat{H}$의 고유상태 기저에 대해 $\|\psi\rangle$을 표현하면, $\|\psi\rangle = \sum_\lambda a_\lambda \|\lambda\rangle$이 되고, 다음이 성립한다.

<p>
$$
\begin{aligned}
\langle \psi | \hat{H} | \psi \rangle
& = \bigg(\sum_{\lambda'}a^*_{\lambda'} \langle \lambda'|\bigg) \hat{H}
  \bigg(\sum_{\lambda} a_\lambda | \lambda\rangle\bigg)\\[1mm]

& = \sum_{\lambda}\sum_{\lambda'} a^*_{\lambda'}a_{\lambda}
  \langle \lambda'|\hat{H}| \lambda\rangle\\[1mm]

& = \sum_{\lambda}\sum_{\lambda'} a^*_{\lambda'}a_{\lambda} \lambda
\langle \lambda'| \lambda\rangle\\[1mm]

& = \sum_{\lambda}\sum_{\lambda'} a^*_{\lambda'}a_{\lambda} \lambda
\cdot \delta_{\lambda, \lambda'}\\[1mm]

& = \sum_\lambda |a_\lambda|^2 \lambda\\[1mm]

& = \sum_\lambda p_\lambda \lambda\\[1mm]

\end{aligned}
$$
</p>

목표 관측가능량 $\hat{H}$의 고유값이나 고유상태를 알지 못하므로, 먼저 이를 대각화해야 한다. $\hat{H}$가 Hermitian(에르미트 행렬은 자기 자신의 켤레 전치 행렬과 같은 정방 행렬이거나, 자기 수반적인 선형 연산자입니다.) 이므로, $\hat{H}=V^\dagger \Lambda V$가 되는 유니터리 변환 $V$가 존재한다. 여기서 $\Lambda$는 대각 고유값 행렬이므로 $j\neq k$이면 $\langle j \| \Lambda \| k \rangle = 0$이고, $\langle j \| \Lambda \| j \rangle = \lambda_j$이다.

이는 기댓값을 다음과 같이 다시 쓸 수 있음을 의미한다:

<p>
$$
\begin{aligned}
\langle\psi|\hat{H}|\psi\rangle
& = \langle\psi|V^\dagger \Lambda V|\psi\rangle\\[1mm]
& = \langle\psi|V^\dagger \bigg(\sum_{j=0}^{2^n-1} |j\rangle
\langle j|\bigg) \Lambda \bigg(\sum_{k=0}^{2^n-1} |k\rangle \langle k|\bigg) V|\psi\rangle\\[1mm]
& = \sum_{j=0}^{2^n-1} \sum_{k=0}^{2^n-1}\langle\psi|V^\dagger |j\rangle
\langle j| \Lambda  |k\rangle \langle k| V|\psi\rangle\\[1mm]
& = \sum_{j=0}^{2^n-1}\langle\psi|V^\dagger |j\rangle
\langle j| \Lambda  |j\rangle \langle j| V|\psi\rangle\\[1mm]
& = \sum_{j=0}^{2^n-1}|\langle j| V|\psi\rangle|^2 \lambda_j\\[1mm]
\end{aligned}
$$
</p>

만약 시스템이 상태 $\|\phi\rangle = V \|\psi\rangle$에 있을 때 $\| j\rangle$를 측정할 확률이 $p_j = \|\langle j\|\phi \rangle\|^2$라는 점을 고려하면, 위의 기댓값은 다음과 같이 표현될 수 있다:

<p>
$$
\langle\psi|\hat{H}|\psi\rangle =
\sum_{j=0}^{2^n-1} p_j \lambda_j.
$$
</p>

확률이 $\|\psi\rangle$가 아닌 $V \|\psi\rangle$에서 취해진다는 점을 유의하는 것이 매우 중요하다. 이것이 바로 행렬 $V$가 절대적으로 필요한 이유이다.

행렬 $V$와 고유값 $\Lambda$를 어떻게 얻는지 궁금할 수 있습니다. 이미 고유값을 알고 있다면, 변분 알고리즘의 목표가 $\hat{H}$의 고유값을 찾는 것이므로 양자 컴퓨터를 사용할 필요가 없을 것입니다.

다행히 이를 우회할 방법이 있습니다: 임의의 $2^n \times 2^n$ 행렬은 $n$개의 Pauli 행렬과 항등 행렬의 텐서 곱 $4^n$개의 선형 결합으로 표현할 수 있으며, 이들 모두는 에르미트이면서 유니타리이고, $V$와 $\Lambda$가 알려져 있습니다. 이것이 Runtime의 `Estimator`가 임의의 `Operator` 객체를 `SparsePauliOp` 으로 분해하여 내부적으로 수행하는 작업입니다.

사용할 수 있는 연산자는 다음과 같습니다:

<p>
$$
\begin{array}{c|c|c|c}
  \text{연산자} & \sigma & V & \Lambda \\[1mm]
  \hline
  I & \sigma_0 = \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix} & V_0 = I & \Lambda_0 = I = \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix} \\[4mm]

  X & \sigma_1 = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix} & V_1 = H =\frac{1}{\sqrt{2}} \begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix} & \Lambda_1 = \sigma_3 = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix} \\[4mm]

  Y & \sigma_2 = \begin{pmatrix} 0 & -i \\ i & 0 \end{pmatrix} & V_2 = HS^\dagger  =\frac{1}{\sqrt{2}} \begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix}\cdot  \begin{pmatrix} 1 & 0 \\ 0 & -i \end{pmatrix} = \frac{1}{\sqrt{2}} \begin{pmatrix} 1 & -i \\ 1 & i \end{pmatrix}\quad & \Lambda_2 = \sigma_3 = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix} \\[4mm]

  Z & \sigma_3 = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix} & V_3 = I & \Lambda_3 = \sigma_3 = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix}
\end{array}
$$
</p>

그러면 $\hat{H}$를 파울리 행렬과 단위 행렬에 대해 다시 써 보자:

<p>
$$
\hat{H} =
\sum_{k_{n-1}=0}^3...
\sum_{k_0=0}^3 w_{k_{n-1}...k_0}
\sigma_{k_{n-1}}\otimes ... \otimes \sigma_{k_0} = \sum_{k=0}^{4^n-1} w_k \hat{P}_k,
$$
</p>

여기서 $k = \sum_{l=0}^{n-1} 4^l k_l \equiv k_{n-1}...k_0$이고 $k_{n-1},...,k_0\in \{0,1,2,3\}$ (즉, 4진법), 그리고 $$\hat{P}_{k} := \sigma_{k_{n-1}}\otimes ... \otimes \sigma_{k_0}$$이다:

<p>
$$
\begin{aligned}
\langle\psi|\hat{H}|\psi\rangle
& = \sum_{k=0}^{4^n-1} w_k
\sum_{j=0}^{2^n-1}|\langle j| V_k|\psi\rangle|^2 \langle j| \Lambda_k |j\rangle \\[1mm]
& = \sum_{k=0}^{4^n-1} w_k \sum_{j=0}^{2^n-1}p_{kj} \lambda_{kj}, \\[1mm]
\end{aligned}
$$
</p>

여기서 $V_k := V_{k_{n-1}}\otimes ... \otimes V_{k_0}$이고 $\Lambda_k := \Lambda_{k_{n-1}}\otimes ... \otimes \Lambda_{k_0}$이며, $\hat{P_k}=V_k^\dagger \Lambda_k V_k$를 만족한다.

## 2. 비용 함수

일반적으로 비용 함수는 문제의 목표와 그 목표에 대해 시험 상태가 얼마나 잘 수행되고 있는지를 설명하는 데 사용된다. 이 정의는 화학, 기계 학습, 금융, 최적화 등의 다양한 예에 적용될 수 있다.

시스템의 기저 상태를 찾는 간단한 예를 고려해 보자. 우리의 목표는 에너지를 나타내는 관측 가능량(해밀토니안 $\hat{\mathcal{H}}$)의 기대값을 최소화하는 것이다:

<p>
$$
\min_{\vec\theta} \langle\psi(\vec\theta)|\hat{\mathcal{H}}|\psi(\vec\theta)\rangle
$$
</p>

`Estimator`를 사용하여 기대값을 평가하고, 이 값을 최소화할 최적화기에 전달할 수 있다. 최적화가 성공하면, 최적 매개변수 값 집합 $\vec\theta^*$가 반환되며, 이로부터 제안된 해 상태 $\|\psi(\vec\theta^{\*})\rangle$를 구성하고 관측된 기대값을 $C(\vec\theta^{\*})$로 계산할 수 있을 것이다.

우리가 고려하는 제한된 상태 집합에 대해서만 비용 함수를 최소화할 수 있음을 유의하라. 이는 두 가지 별개의 가능성으로 이어진다:

* **우리의 ansatz가 탐색 공간 전체에 걸쳐 해 상태를 정의하지 않는 경우**: 이 경우, 우리의 최적화기는 해를 결코 찾지 못할 것이며, 우리는 탐색 공간을 더 정확하게 표현할 수 있는 다른 ansatz들을 실험해 보아야 한다.
* **우리의 최적화기가 이 유효한 해를 찾을 수 없는 경우**: 최적화는 전역적으로 정의될 수도 있고 국소적으로 정의될 수도 있다. 이것이 의미하는 바는 뒷부분에서 살펴볼 것이다.

종합적으로 볼 때, 우리는 고전적인 최적화 루프를 수행하지만 비용 함수의 평가는 양자 컴퓨터에 의존할 것이다. 이 관점에서, 최적화는 순전히 고전적인 노력으로 생각할 수 있으며, 최적화기가 비용 함수를 평가할 필요가 있을 때마다 어떤 블랙박스 양자 오라클(특정 기능을 수행하는 가상의 장치 또는 소프트웨어 구성 요소이지만, 그 내부 작동 방식은 알려져 있지 않다. 사용자는 블랙 박스의 입력과 출력만 알 뿐, 블랙 박스가 입력을 처리하여 출력을 생성하는 방식에 대해서는 전혀 알지 못한다.)을 호출하는 것이다.

```python
def cost_func_vqe(params, circuit, hamiltonian, estimator):
    """Return estimate of energy from estimator

    Parameters:
        params (ndarray): Array of ansatz parameters
        ansatz (QuantumCircuit): Parameterized ansatz circuit
        hamiltonian (SparsePauliOp): Operator representation of Hamiltonian
        estimator (Estimator): Estimator primitive instance

    Returns:
        float: Energy estimate
    """
    pub = (circuit, hamiltonian, params)
    cost = estimator.run([pub]).result()[0].data.evs
    return cost
```

```python
from qiskit.circuit.library import TwoLocal

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

theta_list = (2 * np.pi * np.random.rand(1, 8)).tolist()
ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2Fa01a54f2-da16-4008-b422-fa7ded531f67-0.avif&w=1080&q=75)

먼저 시뮬레이터인 상태벡터추정기(StatevectorEstimator)를 사용하여 이를 수행해 보겠습니다. 이는 일반적으로 디버깅에 유용하지만, 디버깅 실행 직후 실제 양자 하드웨어에서 계산을 진행할 것입니다. 점점 더 많은 관심 문제들이 최첨단 슈퍼컴퓨팅 시설 없이는 고전적인 방식으로 시뮬레이션할 수 없는 상황에 직면하고 있습니다.

```python
estimator = StatevectorEstimator()
cost = cost_func_vqe(theta_list, ansatz, observable, estimator)
print(cost)
```

$\[-0.58744589\]$

이제 실제 양자 컴퓨터에서 실행해 보겠습니다. 구문 변경 사항에 유의하십시오. pass_manager 관련 단계는 다음 예제에서 자세히 설명합니다. 변분 알고리즘에서 특히 중요한 단계 중 하나는 Qiskit 런타임 세션을 사용하는 것입니다. 세션을 시작하면 매개변수가 업데이트될 때마다 새 대기열에서 기다리지 않고 변분 알고리즘을 여러 번 반복 실행할 수 있습니다. 이는 대기 시간이 길거나 많은 반복이 필요한 경우에 중요합니다. IBM Quantum® 네트워크의 파트너만 런타임 세션을 사용할 수 있습니다. 세션에 액세스할 수 없는 경우, 한 번에 제출하는 반복 횟수를 줄이고 가장 최근 매개변수를 저장하여 다음 실행에 사용할 수 있습니다. 너무 많은 반복을 제출하거나 대기 시간이 너무 길어지면 작업 제출 간의 긴 지연을 나타내는 오류 코드 1217이 발생할 수 있습니다.


```python
# Estimated usage: < 1 min. Benchmarked at 7 seconds on an Eagle processor
# Load necessary packages:

from qiskit_ibm_runtime import (
    QiskitRuntimeService,
    Session,
    EstimatorOptions,
    EstimatorV2 as Estimator,
)
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager

# Select the least busy backend:

service = QiskitRuntimeService()
backend = service.least_busy(
    operational=True, min_num_qubits=ansatz.num_qubits, simulator=False
)
# Or get a specific backend:
# backend = service.backend("ibm_brisbane")

# Use a pass manager to transpile the circuit and observable for the specific backend being used:

pm = generate_preset_pass_manager(backend=backend, optimization_level=1)
isa_ansatz = pm.run(ansatz)
isa_observable = observable.apply_layout(layout=isa_ansatz.layout)


# Set estimator options
estimator_options = EstimatorOptions(resilience_level=1, default_shots=10_000)

# Open a Runtime session:

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)
    cost = cost_func_vqe(theta_list, isa_ansatz, isa_observable, estimator)

session.close()
print(cost)
```

위의 두 계산에서 얻은 값이 매우 유사하다는 점에 유의하십시오. 결과 개선 기법은 아래에서 자세히 설명하겠습니다.

### 2.1 비물리적 시스템으로의 예시 매핑

최대 컷(max-cut) 문제는 그래프의 정점을 두 개의 서로소 집합으로 나누어 두 집합 사이의 간선 수를 최대화하는 조합 최적화 문제이다. 더 형식적으로, 무방향 그래프 $G=(V,E)$가 주어졌을 때, 여기서 $V$는 정점의 집합이고 $E$는 간선의 집합이며, max-cut 문제는 정점을 두 개의 서로소 부분집합 $S$와 $T$로 분할하여 한 끝점이 $S$에 있고 다른 끝점이 $T$에 있는 간선의 수를 최대화하는 것을 요구한다.

우리는 군집화, 네트워크 설계, 상전이 등을 포함한 다양한 문제를 해결하기 위해 max-cut을 적용할 수 있다. 문제 그래프를 생성하는 것으로 시작하겠다:

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

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2F739ca7a0-fae3-4389-a8de-7ae93c13b9e1-0.avif&w=1920&q=75)

이 문제는 이진 최적화 문제로 표현할 수 있다. 각 노드 $0 \leq i < n$에 대해, 여기서 $n$은 그래프의 노드 수(이 경우 $n=4$)이며, 이진 변수 $x_i$를 고려할 것이다. 이 변수는 노드 $i$가 우리가 $1$이라고 레이블할 그룹 중 하나에 있으면 값 $1$을 갖고, 다른 그룹, 즉 우리가 $0$이라고 레이블할 그룹에 있으면 $0$을 갖는다. 또한 $w_{ij}$를 (인접 행렬 $w$의 원소 $(i,j)$) 노드 $i$에서 노드 $j$로 가는 간선의 가중치로 나타낸다. 그래프가 무방향이기 때문에, $w_{ij}=w_{ji}$이다. 그러면 우리는 다음 비용 함수를 최대화하는 것으로 문제를 공식화할 수 있다:

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

이 문제를 양자 컴퓨터로 풀기 위해, 우리는 비용 함수를 관측가능량의 기댓값으로 표현할 것이다. 그러나 Qiskit이 기본적으로 허용하는 관측가능량은 고유값이 $0$과 $1$이 아닌 $1$과 $-1$인 Pauli 연산자로 구성된다. 그래서 우리는 다음과 같은 변수 치환을 할 것이다:

여기서 $\vec{x}=(x_0,x_1,\cdots ,x_{n-1})$이다. 우리는 인접 행렬 $w$를 사용하여 모든 간선의 가중치에 편리하게 접근할 수 있다. 이것은 우리의 비용 함수를 얻는 데 사용될 것이다:

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

따라서 우리가 최대화하고자 하는 새로운 비용 함수는:

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

또한, 양자 컴퓨터의 자연스러운 경향은 최대값이 아닌 최소값(보통 가장 낮은 에너지)을 찾는 것이므로, $C(\vec{z})$를 최대화하는 대신 우리는 다음을 최소화할 것이다:

<p>
$$
-C(\vec{z}) =  \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2} z_iz_j -  \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2}
$$
</p>

이제 우리는 변수가 $-1$과 $1$의 값을 가질 수 있는 최소화할 비용 함수를 가지고 있으므로, Pauli $Z$와 다음과 같은 유추를 할 수 있다:

<p>
$$
z_i \equiv Z_i = \overbrace{I}^{n-1}\otimes ... \otimes \overbrace{Z}^{i} \otimes ... \otimes \overbrace{I}^{0}
$$
</p>

다시 말해, 변수 $z_i$는 큐빗 $i$에 작용하는 $Z$ 게이트에 해당할 것이다. 또한:

<p>
$$
Z_i|x_{n-1}\cdots x_0\rangle = z_i|x_{n-1}\cdots x_0\rangle \rightarrow \langle x_{n-1}\cdots x_0 |Z_i|x_{n-1}\cdots x_0\rangle = z_i
$$
</p>

그러면 우리가 고려할 관측가능량은:
<p>
$$
\hat{H} = \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2} Z_iZ_j
$$
</p>

여기에 나중에 다음과 같은 독립 항을 더해야 한다:

<p>
$$
\texttt{offset} = - \sum_{i=0}^n \sum_{j=0}^i \frac{w_{ij}}{2}
$$
</p>

연산자는 에지로 연결된 노드에 있는 Z개의 연산자를 포함하는 항들의 선형 조합입니다(0번째 큐비트가 가장 오른쪽에 있다는 점을 기억하십시오): $IIZZ + IZIZ + IZZI + ZIIZ + ZZII$. 연산자가 구성되면 Qiskit 회로 라이브러리의 `QAOAAnsatz` 회로를 사용하여 QAOA 알고리즘의 앙상블을 쉽게 구축할 수 있습니다.

```python
from qiskit.circuit.library import QAOAAnsatz
from qiskit.quantum_info import SparsePauliOp

hamiltonian = SparsePauliOp.from_list(
    [("IIZZ", 1), ("IZIZ", 1), ("IZZI", 1), ("ZIIZ", 1), ("ZZII", 1)]
)


ansatz = QAOAAnsatz(hamiltonian, reps=2)
# Draw
ansatz.decompose(reps=3).draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2F29398315-3baf-4363-88fc-69b6438e4afa-0.avif&w=3840&q=75)

```python
# Sum the weights, and divide by 2

offset = -sum(edge[2] for edge in edges) / 2
print(f"""Offset: {offset}""")
```

Offset: -2.5

런타임 추정기는 해밀턴ian과 매개변수화된 ansatz를 직접 입력받아 필요한 에너지를 반환하므로, QAOA 인스턴스의 비용 함수는 매우 간단합니다.

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
import numpy as np

x0 = 2 * np.pi * np.random.rand(ansatz.num_parameters)

estimator = StatevectorEstimator()
cost = cost_func_vqe(x0, ansatz, hamiltonian, estimator)
print(cost)
```

1.473098768180865


```python
# Estimated usage: < 1 min, benchmarked at 6 seconds on ibm_osaka, 5-23-24
# Load some necessary packages:

from qiskit_ibm_runtime import QiskitRuntimeService
from qiskit_ibm_runtime import Session, EstimatorV2 as Estimator

# Select the least busy backend:

backend = service.least_busy(
    operational=True, min_num_qubits=ansatz.num_qubits, simulator=False
)

# Or get a specific backend:
# backend = service.backend("ibm_brisbane")

# Use a pass manager to transpile the circuit and observable for the specific backend being used:

pm = generate_preset_pass_manager(backend=backend, optimization_level=1)
isa_ansatz = pm.run(ansatz)
isa_hamiltonian = hamiltonian.apply_layout(layout=isa_ansatz.layout)

# Set estimator options
estimator_options = EstimatorOptions(resilience_level=1, default_shots=10_000)

# Open a Runtime session:

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)
    cost = cost_func_vqe(x0, isa_ansatz, isa_hamiltonian, estimator)

# Close session after done
session.close()
print(cost)
```

1.1120776913677988

이 예제는 응용 부분에서 다시 살펴보면서 최적화 알고리즘을 활용하여 탐색 공간을 탐색하는 방법을 알아보겠습니다. 일반적으로 다음과 같은 단계를 포함합니다.

* 최적화 알고리즘을 사용하여 최적의 매개변수 찾기
* 최적의 매개변수를 가정에 적용하여 고유값 찾기
* 고유값을 문제 정의에 맞게 변환하기



## 3. 측정 전략: 속도 대 정확도

앞서 언급했듯이, 우리는 잡음이 있는 양자 컴퓨터를 *블랙박스 오라클*로 사용하고 있습니다. 이 잡음으로 인해 검색된 값이 비결정적이 되어 무작위 변동이 발생하고, 이는 특정 최적화 알고리즘이 제안된 해로 수렴하는 것을 방해하거나 심지어 완전히 막을 수도 있습니다. 이는 양자 활용도를 점진적으로 탐구하고 양자 우위를 향해 나아가는 과정에서 해결해야 할 일반적인 문제입니다.

![회로 복잡성에 따른 시뮬레이션 비용 변화를 보여주는 그래프입니다. 기존 컴퓨터를 사용할 경우 비용은 기하급수적으로 증가합니다. 양자 오류 완화를 적용하면, 어느 시점에서 효율성이 향상될 것입니다.양자 오류 수정은 시뮬레이션 비용의 선형적 증가를 허용하므로 확실히 이점을 가져다 줄 것입니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/cost-functions/cost-function-path-to-quantum-advantage.svg)

Qiskit Runtime Primitive의 오류 억제 및 오류 완화 옵션을 사용하여 노이즈를 처리하고 오늘날의 양자 컴퓨터의 활용도를 극대화할 수 있습니다.

### 3.1 오류 억제

오류 억제는 컴파일 중에 오류를 최소화하기 위해 회로를 최적화하고 변환하는 데 사용되는 기술을 말합니다. 이는 기본적인 오류 처리 기술로, 일반적으로 전체 실행 시간에 약간의 고전적인 전처리 오버헤드(기본 구현에 비해 새로운 기술로 인해 발생하는 추가 비용)를 발생시킵니다. 이 오버헤드에는 다음과 같은 과정을 통해 양자 하드웨어에서 실행되도록 회로를 트랜스파일링하는 작업이 포함됩니다.

* 양자 시스템에서 사용 가능한 네이티브 게이트를 사용하여 회로 표현
* 가상 큐비트를 물리적 큐비트에 매핑
* 연결 요구 사항에 따라 SWAP 추가
* 1Q 및 2Q 게이트 최적화
* 디코히어런스의 영향을 방지하기 위해 유휴 큐비트에 동적 디커플링 추가

기본 함수는 `optimization_level` 옵션을 설정하고 고급 트랜스파일링 옵션을 선택하여 오류 억제 기술을 사용할 수 있도록 합니다. 추후 과정에서는 결과 개선을 위한 다양한 회로 구성 방법에 대해 자세히 살펴보겠지만, 대부분의 경우 `optimization_level=3`으로 설정하는 것을 권장합니다.

단순하고 이상적인 동작을 보이는 예제 회로를 통해 트랜스파일링 과정에서 최적화 수준을 높이는 것이 얼마나 효과적인지 시각적으로 보여드리겠습니다.


```python
from qiskit.circuit import Parameter, QuantumCircuit
from qiskit.quantum_info import SparsePauliOp

theta = Parameter("theta")

qc = QuantumCircuit(2)
qc.x(1)
qc.h(0)
qc.cp(theta, 0, 1)
qc.h(0)
observables = SparsePauliOp.from_list([("ZZ", 1)])

qc.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2F9e0bf552-32b0-477e-9ac3-4ab643c30623-0.avif&w=750&q=75)

위의 회로는 $\[0,2\pi\]$와 같은 적절한 간격에 걸쳐 위상을 삽입하는 경우 주어진 관측 가능 항목의 정현파 기대값을 생성할 수 있습니다.

```python
## Setup phases
import numpy as np

phases = np.linspace(0, 2 * np.pi, 50)

# phases need to be expressed as a list of lists in order to work
individual_phases = [[phase] for phase in phases]
```

최적화된 트랜스파일의 유용성을 보여주기 위해 시뮬레이터를 사용할 수 있습니다. 아래에서는 오류 완화의 유용성을 보여주기 위해 실제 하드웨어를 사용하는 방법으로 돌아가겠습니다. 우리는 QiskitRuntimeService를 사용하여 실제 백엔드(이 경우 ibm\_brisbane)를 얻고 AerSimulator를 사용하여 노이즈 동작을 포함하여 해당 백엔드를 시뮬레이션합니다.

```python
from qiskit_ibm_runtime import QiskitRuntimeService
from qiskit_aer import AerSimulator

# get a real backend from the runtime service
service = QiskitRuntimeService()
backend = service.backend("ibm_brisbane")

# generate a simulator that mimics the real quantum system with the latest calibration results
backend_sim = AerSimulator.from_backend(backend)
```

이제 패스 매니저를 사용하여 회로를 백엔드의 "명령어 세트 아키텍처(ISA)"로 트랜스파일링할 수 있습니다. 이는 Qiskit 런타임의 새로운 요구 사항입니다. 백엔드에 제출되는 모든 회로는 백엔드의 목표에 대한 제약 조건을 준수해야 합니다. 즉, 백엔드의 ISA, 다시 말해 디바이스가 이해하고 실행할 수 있는 명령어 집합으로 작성되어야 합니다. 이러한 목표 제약 조건은 디바이스의 기본 기저 게이트, 큐비트 연결성, 그리고 관련이 있는 경우 펄스 및 기타 명령어 타이밍 사양과 같은 요소에 의해 정의됩니다.

참고로, 이 예제에서는 최적화 레벨을 0으로 설정한 경우와 3으로 설정한 경우, 두 번에 걸쳐 이 작업을 수행합니다. 각 경우에 Estimator 프리미티브를 사용하여 위상의 여러 값에서 관측량의 기대값을 추정합니다.

```python
# Import estimator and specify that we are using the simulated backend:

from qiskit_ibm_runtime import EstimatorV2 as Estimator

estimator = Estimator(mode=backend_sim)

circuit = qc
```

```python
# Use a pass manager to transpile the circuit and observable for the backend being simulated.
# Start with no optimization:

from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager

pm = generate_preset_pass_manager(backend=backend_sim, optimization_level=0)
isa_circuit = pm.run(circuit)
isa_observables = observables.apply_layout(layout=isa_circuit.layout)

noisy_exp_values = []
pub = (isa_circuit, isa_observables, [individual_phases])
cost = estimator.run([pub]).result()[0].data.evs
noisy_exp_values = cost[0]

# Repeat above steps, but now with optimization = 3:

exp_values_with_opt_es = []
pm = generate_preset_pass_manager(backend=backend_sim, optimization_level=3)
isa_circuit = pm.run(circuit)
isa_observables = observables.apply_layout(layout=isa_circuit.layout)

pub = (isa_circuit, isa_observables, [individual_phases])
cost = estimator.run([pub]).result()[0].data.evs
exp_values_with_opt_es = cost[0]
```

마지막으로 결과를 그래프로 나타내면 최적화를 하지 않은 상태에서도 계산의 정확도가 상당히 좋았지만, 최적화 수준을 3으로 높이면 정확도가 확실히 향상되는 것을 확인할 수 있습니다. 더욱 복잡하고 심층적인 회로에서는 최적화 수준 0과 3 사이의 차이가 훨씬 더 커질 가능성이 높다는 점에 유의하십시오. 이 회로는 예시로 사용된 매우 간단한 회로입니다.

```python
import matplotlib.pyplot as plt

plt.plot(phases, noisy_exp_values, "o", label="opt=0")
plt.plot(phases, exp_values_with_opt_es, "o", label="opt=3")
plt.plot(phases, 2 * np.sin(phases / 2) ** 2 - 1, label="ideal")
plt.ylabel("Expectation")
plt.legend()
plt.show()
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2Ff3d3f805-f8d0-474d-9a88-2d1f164639b0-0.avif&w=1200&q=75)


### 3.2 오류 완화

오류 완화는 실행 시점의 장치 노이즈를 모델링하여 사용자가 회로 오류를 줄일 수 있게 하는 기법을 의미합니다. 일반적으로 이는 모델 훈련과 관련된 양자 전처리 오버헤드, 그리고 생성된 모델을 사용하여 원시 결과의 오류를 완화하기 위한 고전적 후처리 오버헤드를 수반합니다.

Qiskit Runtime 프리미티브의 `resilience_level` 옵션은 오류에 대해 구축할 복원력의 정도를 지정합니다. 높은 수준은 양자 샘플링 오버헤드로 인한 더 긴 처리 시간을 대가로 더 정확한 결과를 생성합니다. 복원력 수준은 프리미티브 쿼리에 오류 완화를 적용할 때 비용과 정확도 간의 트레이드오프를 구성하는 데 사용할 수 있습니다.

어떤 오류 완화 기법을 구현할 때, 이전의 완화되지 않은 편향에 비해 결과의 <DefinitionTooltip definition="오류로 인해 발생하는 측정량의 계통적 편향.">편향</DefinitionTooltip>이 줄어들 것으로 기대합니다. 어떤 경우에는 편향이 완전히 사라질 수도 있습니다. 그러나 이는 대가가 따릅니다. 추정량의 편향을 줄이면 통계적 변동성(즉, 분산)이 증가하며, 이는 샘플링 과정에서 회로당 샷 수를 더 늘려서 보정할 수 있습니다. 이는 편향을 줄이는 데 필요한 것 이상의 오버헤드를 도입하므로 기본적으로 수행되지 않습니다. 아래 예시와 같이 options.executions.shots에서 회로당 샷 수를 조정하여 이 동작을 쉽게 선택할 수 있습니다.

![편향/분산 트레이드오프처럼 넓어지거나 좁아지는 분포를 보여주는 다이어그램.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/cost-functions/cost-function-bias-variance-trade-off.svg)

이 과정에서는 Qiskit Runtime 프리미티브가 전체 구현 세부 정보 없이도 수행할 수 있는 오류 완화를 설명하기 위해 이러한 오류 완화 모델을 높은 수준에서 살펴볼 것입니다.

### 3.3 트월드 판독 오류 소멸 (T-REx)

트월드 판독 오류 소멸(T-REx)은 양자 측정 과정에서 도입된 노이즈를 줄이기 위해 폴리 트월링(Pauli twirling)으로 알려진 기법을 사용합니다. 이 기법은 특정 형태의 노이즈를 가정하지 않으므로 매우 일반적이고 효과적입니다.

전체 워크플로우:

1. 무작위 비트 플립(측정 전 폴리 X)이 있는 영 상태에 대한 데이터 획득
2. 무작위 비트 플립(측정 전 폴리 X)이 있는 원하는 (노이즈가 있는) 상태에 대한 데이터 획득
3. 각 데이터 세트에 대해 특수 함수를 계산하고 나눔

 

![T-REX의 측정 및 보정 회로를 보여주는 다이어그램.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/cost-functions/cost-function-trex-data-collection.svg)

아래 예시에서 보여주듯이 `options.resilience_level = 1`로 설정할 수 있습니다.

### 3.4 제로 노이즈 외삽

제로 노이즈 외삽(ZNE)은 원하는 양자 상태를 준비하는 회로의 노이즈를 먼저 증폭시킨 후, 여러 수준의 노이즈에 대한 측정값을 얻고, 이 측정값을 사용하여 노이즈가 없는 결과를 추론하는 방식으로 작동합니다.

전체 워크플로우:

1. 여러 노이즈 계수에 대해 회로 노이즈 증폭
2. 모든 노이즈 증폭 회로 실행
3. 제로 노이즈 극한으로 외삽

 

![ZNE의 단계를 보여주는 다이어그램. 노이즈는 인위적으로 다양한 계수만큼 증폭됩니다. 그런 다음 값들은 제로 노이즈에서의 값으로 외삽됩니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/cost-functions/cost-function-zne-stages.svg)

`options.resilience_level = 2`로 설정할 수 있습니다. 다양한 `noise_factors`, `noise_amplifiers`, `extrapolators`를 탐색하여 더 최적화할 수 있지만, 이는 본 과정의 범위를 벗어납니다. [여기에 설명된 옵션](https://quantum.cloud.ibm.com/docs/en/guides/error-mitigation-and-suppression-techniques)으로 실험해 보시기를 권장합니다.

각 방법에는 고유한 관련 오버헤드가 따릅니다: 필요한 양자 계산 횟수(시간)와 결과의 정확도 간의 트레이드오프입니다:
<p>
$$
\begin{array}{c|c|c|c}
  \text{Methods} & R=1 \text{, T-REx} & R=2 \text{, ZNE} \\[1mm]
  \hline
  \text{Assumptions} & \text{None} & \text{Ability to scale noise} \\[1mm]
  \text{Qubit overhead} & 1 & 1 \\[1mm]
  \text{Sampling overhead} & 2 & N_{\text{noise-factors}} \\[1mm]
  \text{Bias} & 0 & \mathcal{O}(\lambda^{N_{\text{noise-factors}}}) \\[1mm]
\end{array}
$$
</p>


### 3.5 Qiskit Runtime의 오류 완화 및 억제 옵션 사용

Qiskit Runtime에서 오류 완화 및 억제를 사용하면서 기대값을 계산하는 방법을 살펴보겠습니다. 이전과 동일한 회로와 관측값을 사용하지만, 최적화 수준은 레벨 2로 고정하고, 오류 완화 기법(복원력)을 조정합니다. 이 오류 완화 과정은 최적화 루프 동안 여러 번 발생합니다.

시뮬레이터에서는 오류 완화 기능을 사용할 수 없으므로, 이 부분은 실제 하드웨어에서 수행합니다.

```python
# Estimated usage: 8 minutes, benchmarked on an Eagle processor, 5-23-24

from qiskit_ibm_runtime import QiskitRuntimeService
from qiskit_ibm_runtime import (
    Session,
    EstimatorOptions,
    EstimatorV2 as Estimator,
)

# We select the least busy backend

# Select the least busy backend
# backend = service.least_busy(
#    operational=True, min_num_qubits=ansatz.num_qubits, simulator=False
# )

# Or use a specific backend
backend = service.backend("ibm_brisbane")

# Initialize some variables to save the results from different runs:

exp_values_with_em0_es = []
exp_values_with_em1_es = []
exp_values_with_em2_es = []

# Use a pass manager to optimize the circuit and observables for the backend chosen:

pm = generate_preset_pass_manager(backend=backend, optimization_level=2)
isa_circuit = pm.run(circuit)
isa_observables = observables.apply_layout(layout=isa_circuit.layout)

# Open a session and run with no error mitigation:

estimator_options = EstimatorOptions(resilience_level=0, default_shots=10_000)

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)

    pub = (isa_circuit, isa_observables, [individual_phases])
    cost = estimator.run([pub]).result()[0].data.evs

session.close()

exp_values_with_em0_es = cost[0]

# Open a session and run with resilience = 1:

estimator_options = EstimatorOptions(resilience_level=1, default_shots=10_000)

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)

    pub = (isa_circuit, isa_observables, [individual_phases])
    cost = estimator.run([pub]).result()[0].data.evs

session.close()

exp_values_with_em1_es = cost[0]

# Open a session and run with resilience = 2:

estimator_options = EstimatorOptions(resilience_level=2, default_shots=10_000)

with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)

    pub = (isa_circuit, isa_observables, [individual_phases])
    cost = estimator.run([pub]).result()[0].data.evs

session.close()

exp_values_with_em2_es = cost[0]
```

이전과 마찬가지로, 세 가지 수준의 오류 완화에 대해 위상각의 함수로 결과적인 기대값을 그래프로 나타낼 수 있습니다. 매우 어렵게나마 오류 완화가 결과를 약간 개선한다는 것을 확인할 수 있습니다. 이 효과는 더욱 복잡하고 심층적인 회로에서 훨씬 더 두드러지게 나타납니다.

```python
import matplotlib.pyplot as plt

plt.plot(phases, exp_values_with_em0_es, "o", label="unmitigated")
plt.plot(phases, exp_values_with_em1_es, "o", label="resil = 1")
plt.plot(phases, exp_values_with_em2_es, "o", label="resil = 2")
plt.plot(phases, 2 * np.sin(phases / 2) ** 2 - 1, label="ideal")
plt.ylabel("Expectation")
plt.legend()
plt.show()
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fcost-functions%2Fextracted-outputs%2F474f1a1a-ee90-468d-9390-fdb455aeb142-0.avif&w=1200&q=75)


## 4. 요약

이번 강의에서는 비용 함수를 생성하는 방법을 배웠습니다.

* 비용 함수 생성
* Qiskit 런타임 기본 요소를 활용하여 노이즈를 완화하고 억제하는 방법
* 속도와 정확도를 최적화하는 측정 전략을 정의하는 방법

다음은 개략적인 변분 워크로드입니다.

![유니터리 연산을 통해 기준 상태와 변분 상태를 준비하고, 이어서 측정을 수행하는 양자 회로 다이어그램입니다. 이러한 측정은 비용 함수를 평가하는 데 사용됩니다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/cost-functions/cost-function-circuit.svg)

비용 함수는 최적화 루프의 모든 반복에서 실행됩니다. 다음 강의에서는 고전적 최적화기가 비용 함수 평가를 사용하여 새로운 매개변수를 선택하는 방법을 살펴보겠습니다.

