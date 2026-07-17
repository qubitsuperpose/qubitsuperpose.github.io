---
title: 16차시 2:Variational Algorithms 2
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 5. 참조 상태(Reference states)

이 강의에서는, 변분 알고리즘(variational algorithm)이 더 빠르게 수렴할 수 있도록 reference state(우리 문제의 초기 고정 시작점입니다. 일반적으로 이는 $\|0\rangle$을 의미하지만, 문제를 적절하게 나타내는 다양한 상태로 구성할 수 있습니다.)로 시스템을 초기화하는 방법을 살펴보겠습니다. 먼저 reference state를 수동으로 구성하는 방법을 배우고, 그 다음 변분 알고리즘에서 사용할 수 있는 여러 표준 옵션들을 탐색할 것입니다.

![Diagram of options for references states including default, application-specific, and quantum.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/reference-states/reference-workflow.svg)


## 1. 기본 상태

*참조 상태(reference state)*는 우리의 문제에 대한 초기 고정된 시작점을 의미합니다. 참조 상태를 준비하려면, 양자 회로의 시작 부분에 적절한 비매개변수화 유니타리 $U_R$을 적용해야 하며, 이를 통해 $\|\rho\rangle = U_R \|0\rangle$이 성립합니다. 만약 기존 최적해로부터 얻은 합리적인 추정이나 데이터 포인트가 있다면, 이를 시작점으로 삼으면 변분 알고리즘이 더 빠르게 수렴할 가능성이 높습니다.

가장 단순한 참조 상태는 기본 상태(default state)로, $n$-큐비트 양자 회로의 시작 상태인 $\|0\rangle^{\otimes n}$을 사용합니다. 기본 상태의 경우, 유니타리 연산자 $U_R \equiv I$입니다. 이러한 단순성 덕분에 기본 상태는 다양한 상황에서 유효한 참조 상태로 사용됩니다.

## 2. 고전적 기준 상태

세 개의 큐비트 시스템이 있고, 기본 상태 $\|000\rangle$ 대신 $\|001\rangle$ 상태에서 시작하고 싶다고 가정하자. 이는 순수하게 고전적인 기준 상태의 예시이며, 이를 구성하기 위해서는 단순히 큐비트 $0$에 X 게이트를 적용하면 된다(Qiskit의 큐비트 순서에 따르면, $\|001\rangle = X_0 \|000\rangle$이기 때문이다).

이 경우, 우리의 유니터리 연산자는 $U_R \equiv X_0$이며, 이는 기준 상태 $\|\rho\rangle \equiv \|001\rangle$를 유도한다.

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(3)
qc.x(0)

qc.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Freference-states%2Fextracted-outputs%2Fcfd71225-1f9f-4a34-b825-ae19813956a8-0.avif&w=384&q=75)



## 3. 양자 참조 상태

더 복잡한 중첩 및/또는 얽힘 상태를 시작점으로 삼고자 한다고 가정해봅시다. 예를 들어, $\frac{1}{\sqrt{2}}(\|100\rangle+\|111\rangle)$와 같은 상태를 생각할 수 있습니다.

이 상태를 $\|000\rangle$에서 얻기 위한 한 가지 방법은, 큐비트 $0$에 하다마드 게이트를 적용하고 ($H_0$), 큐비트 $0$을 제어 큐비트로, 큐비트 $1$을 대상 큐비트로 하는 CNOT (CX) 게이트를 적용하며 ($CNOT_{01}$), 마지막으로 큐비트 $2$에 $X$ 게이트를 적용하는 것입니다 ($X_2$).

이 시나리오에서, 우리의 유니터리 연산자는 $U_{R} \equiv X_2CNOT_{01}H_0\|000\rangle$이며, 우리의 참조 상태는 $\|\rho\rangle \equiv \frac{1}{\sqrt{2}}(\|100\rangle+\|111\rangle)$입니다.


```python
qc = QuantumCircuit(3)
qc.h(0)
qc.cx(0, 1)
qc.x(2)

qc.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Freference-states%2Fextracted-outputs%2F77297158-60d7-469a-8a82-27d38a52d795-0.avif&w=640&q=75)


## 4. 템플릿 회로를 사용하여 기준 상태 구성하기

우리는 또한 `TwoLocal`과 같은 다양한 템플릿 회로를 사용할 수 있는데, 이는 여러 개의 조정 가능한 매개변수와 얽힘을 손쉽게 표현할 수 있게 해준다. 우리는 다음 수업에서 이러한 템플릿 회로에 대해 더 자세히 다룰 것이지만, 매개변수를 바인딩하기만 한다면 기준 상태를 위해 사용할 수 있다

```python
from qiskit.circuit.library import TwoLocal
from math import pi

reference_circuit = TwoLocal(2, "rx", "cz", entanglement="linear", reps=1)
theta_list = [pi / 2, pi / 3, pi / 3, pi / 2]

reference_circuit = reference_circuit.assign_parameters(theta_list)

reference_circuit.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Freference-states%2Fextracted-outputs%2F8f3948ad-4caf-4ee6-8a72-bb91ed1bfeae-0.avif&w=640&q=75)


## 5. 애플리케이션별 참조 상태

### 5.1 양자 머신 러닝

변분 양자 분류기(VQC)에서 훈련 데이터는 매개변수화된 회로인 *특징 맵*을 사용하여 양자 상태로 인코딩됩니다. 여기서 각 매개변수 값은 훈련 데이터 세트의 데이터 포인트를 나타냅니다. `zz_feature_map`은 데이터 포인트($x$)를 이 특징 맵에 전달하는 데 사용할 수 있는 매개변수화된 회로의 한 유형입니다.

```python
from qiskit.circuit.library import zz_feature_map

data = [0.1, 0.2]

zz_feature_map_reference = zz_feature_map(feature_dimension=2, reps=2)
zz_feature_map_reference = zz_feature_map_reference.assign_parameters(data)
zz_feature_map_reference.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Freference-states%2Fextracted-outputs%2F4bfd5a76-0db3-499d-bda1-0cb95c3f23c2-0.avif&w=1920&q=75)


## 6. 요약

이 강의를 통해 다음을 사용하여 시스템을 초기화하는 방법을 배웠습니다:

* 기본 참조 상태
* 고전적 참조 상태
* 양자 참조 상태
* 응용 프로그램별 참조 상태

우리의 고수준 변분 워크로드는 다음과 같습니다:

![참조 상태를 준비하는 유니타리 연산자의 회로 다이어그램.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/reference-states/reference-circuit.svg)

참조 상태는 고정된 초기 시작점인 반면, 변분 알고리즘이 탐색할 매개변수화된 상태의 집합을 정의하는 *ansatz*를 정의하기 위해 *변분 형태(variational form)*를 사용할 수 있습니다.