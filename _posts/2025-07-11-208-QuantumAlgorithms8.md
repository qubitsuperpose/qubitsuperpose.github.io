---
title: 6차시 8:Fundamentals of quantum algorithms(The phase estimation problem)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 위상 추정 문제

- 출처: [The phase estimation problem](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/phase-estimation-problem)

## 1. **스펙트럼 정리**

**스펙트럼 정리**는 특정 유형의 행렬, 즉 **정규 행렬**이라고 불리는 행렬이 간단하고 유용한 방식으로 표현될 수 있다고 명시하는 선형 대수학의 중요한 사실이다.
우리는 이 단원에서 유니타리 행렬에 대해서만 이 정리가 필요하지만, 이 시리즈의 앞으로의 과정에서 에르미트 행렬에도 적용할 것이다.

### 1.1 정규 행렬

복소수 항목을 가진 정방 행렬 $M$은 켤레 전치 행렬과 교환될 때 **정규 행렬**이라고 불린다.
$M M^{\dagger} = M^{\dagger} M.$

모든 유니타리 행렬 $U$는 다음 이유로 정규 행렬이다.
\[
U U^{\dagger} = \mathbb{I} = U^{\dagger} U.
\]
자신의 켤레 전치 행렬과 같은 행렬인 에르미트 행렬은 정규 행렬의 또 다른 중요한 분류이다.
만약 $H$가 에르미트 행렬이라면,
\[
H H^{\dagger} = H^2 = H^{\dagger} H,
\]

따라서 $H$는 정규 행렬이다.

모든 정방 행렬이 정규 행렬인 것은 아니다.
예를 들어, 이 행렬은 정규 행렬이 아니다.
<br>
$$
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}
$$

(이것은 간단하지만 고려하는 데 종종 매우 도움이 되는 훌륭한 행렬 예시이다.)
그것이 정규 행렬이 아닌 이유는 다음과 같다.
<br>
$$
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}^{\dagger}
=
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}
\begin{pmatrix}
0 & 0\\
1 & 0
\end{pmatrix}
=
\begin{pmatrix}
1 & 0\\
0 & 0
\end{pmatrix}
$$

반면에
<br>
$$
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}^{\dagger}
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}
=
\begin{pmatrix}
0 & 0\\
1 & 0
\end{pmatrix}
\begin{pmatrix}
0 & 1\\
0 & 0
\end{pmatrix}
=
\begin{pmatrix}
0 & 0\\
0 & 1
\end{pmatrix}.
$$

### 1.2 정리 진술
이제 스펙트럼 정리의 진술이 있다.
>$M$이 정규 $N \times N$ 복소 행렬이라고 하자.
>복소수 $\lambda_1,\ldots,\lambda_N$와 함께 $N$차원 복소 벡터의 정규직교 기저 $$\bigl\{ \vert\psi_1\rangle,\ldots,\vert\psi_N\rangle \bigr\}$$가 존재한다.\
> $$
> M = \lambda_1 \vert \psi_1\rangle\langle \psi_1\vert + \cdots + \lambda_N \vert \psi_N\rangle\langle \psi_N\vert.
> $$

다음 형태의 행렬 표현은
\[
M = \sum_{k = 1}^N \lambda_k \vert \psi_k\rangle\langle \psi_k\vert \tag{1}
\]

일반적으로 **스펙트럼 분해**라고 불린다.
만약 $M$이 형태 (1)로 표현된 정규 행렬이라면, 다음 방정식이
[\
M \vert \psi_j \rangle = \lambda_j \vert \psi_j \rangle
\]
모든 $j = 1,\ldots,N$에 대해 참이어야 한다는 점에 주목하라.
이는 $$\bigl\{ \vert\psi_1\rangle,\ldots,\vert\psi_N\rangle \bigr\}$$가 정규직교라는 사실의 결과이다.
\[
M \vert \psi_j \rangle
= \left(\sum_{k = 1}^N \lambda_k \vert \psi_k\rangle\langle \psi_k\vert\right)\vert \psi_j\rangle
= \sum_{k = 1}^N \lambda_k \vert \psi_k\rangle\langle \psi_k\vert\psi_j \rangle
= \lambda_j \vert\psi_j \rangle
\]


즉, 각 숫자 $\lambda_j$는 $M$의 **고유값**이고 $\vert\psi_j\rangle$는 해당 고유값에 해당하는 **고유 벡터**이다.

**예시 1**.
다음과 같다고 하자.
    <br>
    $$
    \mathbb{I} = \begin{pmatrix}1 & 0\\0 & 1\end{pmatrix},
    $$

이는 정규 행렬이다.
이 정리는 $\mathbb{I}$가 $\lambda_1,$ $\lambda_2,$ $\vert\psi_1\rangle,$ 및 $\vert\psi_2\rangle$의 어떤 선택에 대해 형태 (1)로 작성될 수 있음을 의미한다.
여러 가지 가능한 선택지가 있는데, 다음을 포함한다.
    \[
    \lambda_1 = 1, \hspace{5pt}
    \lambda_2 = 1, \hspace{5pt}
    \vert\psi_1\rangle = \vert 0\rangle, \hspace{5pt}
    \vert\psi_2\rangle = \vert 1\rangle.
    \]

정리는 복소수 $\lambda_1,\ldots,\lambda_N$가 **서로 다르다**고 말하지 않는다는 점에 주목하라 — 같은 복소수가 반복될 수 있으며, 이는 이 예시에서 필요하다.
이러한 선택은 다음 때문에 유효하다.
    \[
    \mathbb{I} = \vert 0\rangle\langle 0\vert + \vert 1\rangle\langle 1\vert.
    \]


실제로, 우리는 $\{\vert\psi_1\rangle,\vert\psi_2\rangle\}$를 **임의의** 정규직교 기저로 선택할 수 있으며
방정식은 참이 될 것이다. 예를 들어,
\[
    \mathbb{I} = \vert +\rangle\langle +\vert + \vert -\rangle\langle -\vert.
\]


**예시 2**.

하마르 연산을 고려해보자.
    <br>
    $$
    H = \frac{1}{\sqrt{2}}
    \begin{pmatrix}
    1 & 1\\[1mm]
    1 & -1
    \end{pmatrix}
    $$

이것은 유니타리 행렬이므로 정규 행렬이다.
스펙트럼 정리는 $H$가 형태 (1)로 작성될 수 있음을 의미하며, 특히 우리는 다음을 갖는다.
\[
H =
\vert\psi_{\pi/8}\rangle \langle \psi_{\pi/8}\vert
\- \vert\psi_{5\pi/8}\rangle \langle \psi_{5\pi/8}\vert
\]

여기서
    \[
    \vert\psi_{\theta}\rangle
    = \cos(\theta)\vert 0\rangle + \sin(\theta) \vert 1\rangle.
    \]

더 명시적으로는,
<br>
    $$
    \begin{aligned}
    \vert\psi_{\pi/8}\rangle & = \frac{\sqrt{2 + \sqrt{2}}}{2}\vert 0\rangle
    + \frac{\sqrt{2 - \sqrt{2}}}{2}\vert 1\rangle, \\[3mm]
    \vert\psi_{5\pi/8}\rangle & = -\frac{\sqrt{2 - \sqrt{2}}}{2}\vert 0\rangle
    + \frac{\sqrt{2 + \sqrt{2}}}{2}\vert 1\rangle.
    \end{aligned}
    $$

우리는 필요한 계산을 수행하여 이 분해가 올바른지 확인할 수 있다.
<br>
$$
    \vert\psi_{\pi/8}\rangle \langle \psi_{\pi/8}\vert
    - \vert\psi_{5\pi/8}\rangle \langle \psi_{5\pi/8}\vert
    = \begin{pmatrix}
    \frac{2 + \sqrt{2}}{4} & \frac{\sqrt{2}}{4}\\[2mm]
    \frac{\sqrt{2}}{4} & \frac{2 - \sqrt{2}}{4}
    \end{pmatrix}
    -
    \begin{pmatrix}
    \frac{2 - \sqrt{2}}{4} & -\frac{\sqrt{2}}{4}\\[2mm]
    -\frac{\sqrt{2}}{4} & \frac{2 + \sqrt{2}}{4}
    \end{pmatrix}
    = H.
    $$

위의 첫 번째 예시가 보여주듯이, 고유 벡터가 선택되는 방식에는 어느 정도 **자유**가 있을 수 있다.
하지만, 고유값의 순서를 제외하고는 고유값이 선택되는 방식에는 **전혀 자유가 없다**.
동일한 복소수의 반복을 포함할 수 있는 동일한 $N$개의 복소수 $\lambda_1,\ldots,\lambda_N$는 주어진 행렬 $M$의 선택에 대해 항상 방정식 (1)에 나타날 것이다.

이제 유니타리 행렬에 집중해 보자.
우리가 방정식을 만족하는 복소수 $\lambda$와 0이 아닌 벡터 $\vert\psi\rangle$를 가지고 있다고 가정해보자.
\[
U\vert\psi\rangle = \lambda\vert\psi\rangle.
\tag{2}
\]

즉, $\lambda$는 $U$의 고유값이고 $\vert\psi\rangle$는 이 고유값에 해당하는 고유 벡터이다.

유니타리 행렬은 유클리드 노름을 보존하므로, 우리는 (2)로부터 다음을 결론짓는다.
<br>
$$
\bigl\| \vert\psi\rangle \bigr\|
= \bigl\| U \vert\psi\rangle \bigr\|
= \bigl\| \lambda \vert\psi\rangle \bigr\|
= \vert \lambda \vert \bigl\| \vert\psi\rangle \bigr\|
$$

$\vert\psi\rangle$가 0이 아니라는 조건은 $\bigl\| \vert\psi\rangle \bigr\|\neq 0$임을 의미하므로, 양변에서 이를 소거하여 다음을 얻을 수 있다.
\[
\vert \lambda \vert = 1.
\]

이는 유니타리 행렬의 고유값이 항상 **절대값이 1**이어야 함을 보여주므로, 그것들은 **단위 원** 위에 놓여있다.

\[
\mathbb{T} = \{\alpha\in\mathbb{C} : \vert\alpha\vert = 1\}
\]
(기호 $\mathbb{T}$는 복소 단위 원에 대한 일반적인 이름이다. $S^1$이라는 이름도 흔하다.)

## 2. 위상 추정 문제 진술

**위상 추정 문제**에서, 우리는 $n$ 큐비트의 양자 상태 $\vert \psi\rangle$와 $n$ 큐비트에 작용하는 유니타리 양자 회로를 받는다.
우리는 $\vert \psi\rangle$가 회로의 작용을 설명하는 유니타리 행렬 $U$의 **고유 벡터**라는 **약속**을 받으며, 우리의 목표는 $\vert \psi\rangle$에 해당하는 고유값 $\lambda$를 계산하거나 근사하는 것이다.
더 정확히 말하면, $\lambda$가 복소 단위 원 위에 놓여있기 때문에, 우리는 다음처럼 쓸 수 있다.
\[
\lambda = e^{2\pi i \theta}
\]

$0\leq\theta<1$을 만족하는 유일한 실수 $\theta$에 대해.
이 문제의 목표는 이 실수 $\theta$를 계산하거나 근사하는 것이다.

> **위상 추정 문제** \
> 입력: $n$ 큐비트 연산 $U$를 위한 유니타리 양자 회로와 $n$ 큐비트 양자 상태 $\vert\psi\rangle$ \
> 약속: $\vert\psi\rangle$는 $U$의 고유 벡터이다 \
> 출력: $U\vert\psi\rangle = e^{2\pi i \theta}\vert\psi\rangle$를 만족하는 숫자 $\theta\in[0,1)$에 대한 근사값

이 문제 진술에 대한 몇 가지 주석은 다음과 같다:

1. 위상 추정 문제는 지금까지 과정에서 보았던 다른 문제들과 입력에 양자 상태가 포함된다는 점에서 다르다. 일반적으로 우리는 고전적인 입력과 출력을 갖는 문제에 초점을 맞추지만, 이와 같은 양자 상태 입력을 고려하는 것을 막는 것은 없다. 실제 관련성 측면에서, 위상 추정 문제는 일반적으로 나중에 단원에서 보게 될 정수 인수분해와 같은 더 큰 계산 내부의 **하위 문제**로 마주하게 된다.

2. 위의 위상 추정 문제 진술은 $\theta$의 근사가 무엇을 구성하는지에 대해 구체적이지 않지만, 우리의 필요와 관심에 따라 더 정밀한 문제 진술을 공식화할 수 있다. 정수 인수분해의 맥락에서는 $\theta$에 대한 매우 정밀한 근사를 요구할 것이지만, 다른 경우에는 매우 대략적인 근사로 만족할 수도 있다. 우리는 필요한 정밀도가 해법의 계산 비용에 어떻게 영향을 미치는지 곧 논의할 것이다.

3. 위상 추정 문제에서 $\theta = 0$에서 $\theta = 1$로 이동함에 따라, 우리는 $e^{2\pi i \cdot 0} = 1$에서 시작하여 반시계 방향으로 $e^{2\pi i \cdot 1} = 1$을 향해 단위 원을 완전히 한 바퀴 도는 것임을 주목하라. 즉, $\theta = 1$에 도달하면 $\theta = 0$에서 시작했던 곳으로 돌아온다. 따라서, 근사의 정확도를 고려할 때, 1에 가까운 $\theta$의 선택은 0에 가까운 것으로 간주되어야 한다. 예를 들어, 근사값 $\theta = 0.999$는 $\theta = 0$에서 $1/1000$ 이내인 것으로 간주되어야 한다.