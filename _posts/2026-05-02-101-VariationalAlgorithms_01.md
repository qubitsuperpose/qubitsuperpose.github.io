---
title: 16차시 1:Variational Algorithms 1
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 1. 변분 알고리즘(Variational algorithms)

이 과정에서는 **양자역학의 변분 정리(variational theorem)** 에 기반한 **변분 알고리즘(variational algorithms)** 과 **근미래 하이브리드 양자-고전 알고리즘(near-term hybrid quantum-classical algorithms)** 의 구체적인 내용을 다룹니다. 이러한 알고리즘은 오늘날의 **오류 내성(fault-tolerant)을 갖추지 않은 양자 컴퓨터**가 제공할 수 있는 활용 가능성을 이용할 수 있으므로, **양자 우위(quantum advantage)** 를 달성하기 위한 유력한 후보로 여겨집니다.

이 과정에서는 다음 내용을 살펴봅니다.

* 변분 알고리즘 설계 워크플로의 각 단계
* 각 단계와 관련된 트레이드오프(trade-off)
* 속도와 정확도를 최적화하기 위해 **Qiskit Runtime primitives**를 사용하는 방법

이 과정은 연구자와 개발자가 양자 컴퓨터의 활용 가능성을 탐구하기 위한 출발점으로 설계되었습니다. 양자 컴퓨팅의 이론적 배경과 기초 지식을 더 깊이 학습하고 싶다면 **「Basics of Quantum Information and Computation」** 과정(또는 **YouTube 동영상 시리즈**)을 참고하시기 바랍니다.


## 1. Simplified hybrid workflow

![](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/variational-algorithms/variational-workflow.svg)


변분 알고리즘(Variational algorithms)은 알고리즘, 소프트웨어, 그리고 하드웨어의 발전에 따라 조합하고 최적화할 수 있는 여러 모듈식 구성 요소로 이루어져 있다. 이러한 구성 요소에는 특정 문제를 매개변수 집합으로 표현하는 **비용 함수(cost function)**, 이러한 매개변수를 사용하여 탐색 공간을 표현하는 **안자츠(ansatz)**, 그리고 탐색 공간을 반복적으로 탐색하는 **최적화기(optimizer)**가 포함된다. 각 반복(iteration*마다 최적화기는 현재의 매개변수를 사용하여 **비용 함수(cost function)**를 평가하고, 최적의 해에 **수렴(converge)**할 때까지 다음 반복에서 사용할 매개변수를 선택한다. 이러한 알고리즘 계열이 **하이브리드(hybrid)**라고 불리는 이유는 비용 함수의 평가는 양자 자원을 사용하여 수행되는 반면, 최적화는 고전적(클래식) 계산 자원을 사용하여 이루어지기 때문이다.

### 1.문제 초기화 (Initialize problem)

변분 알고리즘은 먼저 양자 컴퓨터를 **기본 상태(default state)**인 $\|0\rangle$로 초기화한 후, 이를 원하는 (매개변수가 없는) 상태 $\|\rho\rangle$로 변환한다. 이 상태를 **참조 상태(reference state)**라고 부른다.

이 변환은 기본 상태에 유니터리 참조 연산자(unitary reference operator) $U_R$를 적용하여 수행되며, 다음과 같이 표현된다.
<p>
$$
U_R|0\rangle = |\rho\rangle
$$
</p>

### 2.안자츠 준비 (Prepare ansatz)

기본 상태 $\|0\rangle$에서 목표 상태 $\|\psi(\vec\theta)\rangle$까지 반복적으로 최적화를 수행하기 위해서는, 변분 알고리즘이 탐색할 수 있는 매개변수화된 상태들의 집합을 표현하는 **변분 형식(variational form)** $U_V(\vec\theta)$를 정의해야 한다.

특정한 참조 상태(reference state)와 변분 형식(variational form)의 조합을 **안자츠(ansatz)**라고*하며, 다음과 같이 정의한다.
<p>
$$
U_A(\vec\theta) := U_V(\vec\theta) U_R
$$
</p>

안자츠는 결국 기본 상태 $\|0\rangle$를 목표 상태 $\|\psi(\vec\theta)\rangle$로 변환할 수 있는 **매개변수화된 양자 회로(parametrized quantum circuit)**의 형태를 갖게 된다.

전체 과정은 다음과 같이 나타낼 수 있다.
<p>
$$
\begin{aligned}
|0\rangle \xrightarrow{U_R} U_R|0\rangle
& = |\rho\rangle \xrightarrow{U_V(\vec{\theta})} U_A(\vec{\theta})|0\rangle \\[1mm]
& = U_V(\vec{\theta})U_R|0\rangle \\[1mm]
& = U_V(\vec{\theta})|\rho\rangle \\[1mm]
& = |\psi(\vec{\theta})\rangle \\[1mm]
\end{aligned}
$$
</p>



### 3.비용 함수 평가 (Evaluate cost function)

문제를 **비용 함수(cost function)** $C(\vec\theta)$로 표현할 수 있으며, 이는 양자 시스템에서 실행되는 **파울리 연산자(Pauli operators)**의 선형 결합으로 인코딩된다. 이 비용 함수는 에너지나 스핀과 같은 물리 시스템의 정보를 나타낼 수도 있지만, 물리적인 문제뿐만 아니라 비물리적인 문제 역시 표현할 수 있다.

비용 함수를 평가하는 과정에서는 **Qiskit Runtime primitives**를 활용하여 오류 억제(error suppression) 및 오류 완화(error mitigation)를 적용함으로써 양자 잡음(noise)의 영향을 줄일 수 있다.

### 4.매개변수 최적화 (Optimize parameters)

비용 함수의 평가 결과는 고전 컴퓨터로 전달되며, 고전적 최적화기(classical optimizer)는 이 결과를 분석하여 다음 반복에서 사용할 변분 매개변수의 값을 결정한다.

이미 알려진 최적해가 존재하는 경우에는 이를 **초기점(initial point)** $\vec\theta_0$으로*설정하여 최적화를 **부트스트랩(bootstrap)**할*수 있다. 이러한 **초기 상태(initial state)**

<p>
$$
|\psi(\vec\theta_0)\rangle
$$
</p>
를 사용하면 최적화기가 더 빠르게 적절한 해를 찾는 데 도움이 될 수 있다.

### 5.안자츠의 매개변수를 갱신하고 반복 실행 (Adjust ansatz parameters with results, and re-run)

전체 과정은 고전적 최적화기의 종료 조건(finalization criteria)이 만족될 때까지 반복된다. 최종적으로 최적의 매개변수 집합 $\vec\theta^*$가 반환되며, 문제에 대한 제안된 해(solution state)는 다음과 같다.
<p>
$$
|\psi(\vec\theta^*)\rangle = U_A(\vec\theta^*)|0\rangle
$$
</p>

## 2. 변분 정리

변분 알고리즘의 일반적인 목표는 특정 관측량의 고유값이 가장 낮거나 가장 높은 양자 상태를 찾는 것입니다. 여기서 중요한 통찰력을 얻기 위해 양자 역학의 *변분 정리*를 활용할 것입니다. 변분 정리의 전체 내용을 살펴보기 전에, 그 이면에 숨겨진 수학적 직관을 먼저 살펴보겠습니다.

### 2.1 에너지와 바닥 상태에 대한 수학적 직관

양자역학에서 에너지는 일반적으로 해밀토니안이라고 불리는 양자 관측가능량의 형태로 나타나며, 이를 우리는 $\hat{\mathcal{H}}$로 표기하겠습니다. 그것의 스펙트럼 분해를 고려해 봅시다:

<p>
$$
\hat{\mathcal{H}} = \sum_{k=0}^{N-1} \lambda_k |\phi_k\rangle \langle \phi_k|
$$
</p>

여기서 $N$은 상태 공간의 차원, $\lambda_{k}$는 $k$번째 고유값 또는 물리적으로는 $k$번째 에너지 준위, 그리고 $\|\phi_k\rangle$는 이에 대응하는 고유상태로서 $\hat{\mathcal{H}}\|\phi_k\rangle = \lambda_k \|\phi_k\rangle$를 만족합니다. (정규화된) 상태 $\|\psi\rangle$에 있는 계의 기대 에너지는 다음과 같을 것입니다:

<p>
$$
\begin{aligned}
\langle \psi | \hat{\mathcal{H}} | \psi \rangle
& = \langle \psi |\bigg(\sum_{k=0}^{N-1} \lambda_k |\phi_k\rangle \langle \phi_k|\bigg) | \psi \rangle \\[1mm]
& = \sum_{k=0}^{N-1} \lambda_k \langle \psi |\phi_k\rangle \langle \phi_k| \psi \rangle \\[1mm]
& = \sum_{k=0}^{N-1} \lambda_k |\langle \psi |\phi_k\rangle|^2 \\[1mm]
\end{aligned}
$$
</p>


$$ \lambda_0 \leq \lambda_k, \forall k $$
임을 고려하면, 우리는 다음을 얻습니다:

<p>
$$
\begin{aligned}
\langle \psi | \hat{\mathcal{H}} | \psi \rangle
& = \sum_{k=0}^{N-1} \lambda_k |\langle \psi |\phi_k\rangle|^2 \\[1mm]
& \geq  \sum_{k=0}^{N-1} \lambda_0 |\langle \psi |\phi_k\rangle|^2 \\[1mm]
& = \lambda_0 \sum_{k=0}^{N-1} |\langle \psi |\phi_k\rangle|^2 \\[1mm]
& = \lambda_0 \\[1mm]
\end{aligned}
$$
</p>


$$\{\vert\phi_k\rangle \}_{k=0}^{N-1}$$이 정규직교기저이므로, $\|\phi_{k} \rangle$를 측정할 확률은 $p_k = \|\langle \psi \|\phi_{k} \rangle \|^2$이며, 모든 확률의 합은 $\sum_{k=0}^{N-1} \|\langle \psi \|\phi_k\rangle\|^2 = \sum_{k=0}^{N-1}p_k = 1$을 만족합니다. 요컨대, 임의의 계의 기대 에너지는 가장 낮은 에너지, 즉 바닥 상태 에너지보다 높습니다:

<p>
$$
\langle \psi | \hat{\mathcal{H}} | \psi \rangle \geq \lambda_0.
$$
</p>


위의 논증은 임의의 유효한 (정규화된) 양자 상태 $\vert\psi\rangle$에 적용되므로, 매개변수 벡터 $$\vec\theta$$에 의존하는 매개변수화된 상태 $\vert\psi(\vec\theta)\rangle$를 고려하는 것이 전적으로 가능합니다. 이 부분이 바로 "변분"이라는 요소가 작용하는 지점입니다. $C(\vec\theta) := \langle \psi(\vec\theta)\|\hat{\mathcal{H}}\|\psi(\vec\theta)\rangle$로 주어진 비용 함수를 고려하고 이를 최소화하려 한다면, 그 최솟값은 항상 다음을 만족합니다:

<p>
$$
\min_{\vec\theta} C(\vec\theta) =
\min_{\vec\theta} \langle \psi(\vec\theta)|\hat{\mathcal{H}}|\psi(\vec\theta)\rangle \geq \lambda_0.
$$
</p>

$C(\vec\theta)$의 최솟값은 매개변수화된 상태 $\|\psi(\vec\theta)\rangle$를 사용하여 $\lambda_0$에 도달할 수 있는 가장 가까운 값이 될 것이며, 등호는 $\vert\psi(\vec\theta^{\*})\rangle$ = $\|\phi_0\rangle$ 를 만족하는 매개변수 벡터 $\vec\theta^{\*}$가 존재할 경우에만 도달됩니다.


### 2.2 양자역학의 변분 정리

양자계의 (정규화된) 상태 $\|\psi\rangle$가 매개변수 벡터 $\vec\theta$에 의존할 때, 바닥상태(즉, 최소 고유값 $\lambda_0$을 갖는 고유상태 $\|\phi_0\rangle$)의 최적 근사는 해밀토니안 $\hat{\mathcal{H}}$의 기댓값(특정 상태에서 양자계를 측정했을 때, 해당 결과가 나올 확률로 가중치를 부여한 측정값의 평균)을 최소화하는 것이다:

<p>
$$
\langle \hat{\mathcal{H}} \rangle(\vec\theta) :=
\langle \psi(\vec\theta) |\hat{\mathcal{H}}| \psi(\vec\theta) \rangle \geq
\lambda_0
$$
</p>

변분 정리가 에너지 최솟값의 관점에서 서술되는 이유는 다음과 같은 여러 수학적 가정들을 포함하고 있기 때문이다:

* 물리적인 이유로 인해, $N\rightarrow\infty$인 경우에도 에너지에 대한 유한한 하한 $E \geq \lambda_0 > -\infty$이 존재해야 한다.
* 상한은 일반적으로 존재하지 않는다.

그러나 수학적으로 말하자면, 이러한 가정들 이상으로 해밀토니안 $\hat{\mathcal{H}}$에 대해 특별한 것은 없으므로, 동일한 제약 조건을 따르는 다른 양자 관측가능량과 그 고유상태들로 이 정리를 일반화할 수 있다. 또한, 유한한 상한이 존재한다면 하한을 상한으로 대체하여 고유값을 최대화하는 동일한 수학적 논리를 전개할 수 있다는 점에 유의하라.

## 3. Summary
이번 강의를 통해 변형 알고리즘에 대한 높은 수준의 관점을 배웠습니다. 다음 강의에서는 각 단계와 관련 장단점을 더 자세히 살펴보겠습니다.



