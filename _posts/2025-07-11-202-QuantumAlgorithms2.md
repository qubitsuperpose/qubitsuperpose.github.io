---
title: 6차시 2:Fundamentals of quantum algorithms(Deutsch's algorithm)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# Deutsch's algorithm

- 출처: [Deutsch's algorithm](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/quantum-query-algorithms/deutsch-algorithm)

**도이치 알고리즘**
도이치 알고리즘은 $n=1$인 특별한 경우의 패리티 문제를 해결합니다.
양자 컴퓨팅 맥락에서 이 문제는 때때로 **도이치 문제**라고 불리며, 우리는 이 강의에서 해당 용어를 따를 것입니다.

정확히 말하면, 입력은 한 비트에서 한 비트로 가는 함수 $f:\Sigma \rightarrow \Sigma$로 표현됩니다.
이러한 함수는 네 가지가 있습니다.

<br>
$$
\rule[-10mm]{0mm}{10mm}
\begin{array}{c|c}
  a & f_1(a)\\
  \hline
  0 & 0\\
  1 & 0
\end{array}
\qquad
\begin{array}{c|c}
  a & f_2(a)\\
  \hline
  0 & 0\\
  1 & 1
\end{array}
\qquad
\begin{array}{c|c}
  a & f_3(a)\\
  \hline
  0 & 1\\
  1 & 0
\end{array}
\qquad
\begin{array}{c|c}
  a & f_4(a)\\
  \hline
  0 & 1\\
  1 & 1
\end{array}
$$


이 함수들 중 첫 번째와 마지막은 **상수**이며, 중간 두 함수는 **균형** 함수입니다. 이는 입력값을 범위로 할 때 함수의 두 가지 가능한 출력값이 같은 횟수로 나타난다는 의미입니다.
도이치 문제는 입력 함수가 이 두 범주 중 어느 것에 속하는지 결정하는 것입니다: 상수 또는 균형.

>**도이치 문제** \
>입력: 함수 $f:\\{0,1\\}\rightarrow\\{0,1\\}$ \
>출력: $f$가 상수이면 $0$, $f$가 균형이면 $1$

도이치 문제의 입력 함수 $f$를 문자열에 대한 임의 접근을 나타내는 것으로 보면, 우리는 두 비트 문자열: $f(0)f(1)$을 생각하고 있는 것입니다.

<br>
$$
\begin{array}{cc}
\mathsf{function} & \mathsf{string}\\
\hline
f_1 & 00 \\
f_2 & 01 \\
f_3 & 10 \\
f_4 & 11
\end{array}
$$

이런 식으로 볼 때, 도이치 문제는 두 비트의 패리티(또는 동등하게는 배타적 논리합)를 계산하는 것입니다.

이 문제를 정확하게 해결하는 모든 고전적 질의 알고리즘은 $f(0)$과 $f(1)$ 두 비트를 모두 질의해야 합니다.
예를 들어, $f(1)=1$이라는 것을 알더라도, $f(0)=1$인지 $f(0)=0$인지에 따라 답은 여전히 $0$ 또는 $1$이 될 수 있습니다.
다른 모든 경우도 유사합니다. 두 비트 중 하나만 아는 것은 그들의 패리티에 대해 어떤 정보도 제공하지 않습니다.
따라서, 이전 섹션에서 설명된 불리언 회로는 이 문제를 해결하는 데 필요한 질의 수 측면에서 우리가 할 수 있는 최선입니다.

##  1. **양자 회로 설명**

도이치 알고리즘은 단 한 번의 질의를 사용하여 도이치 문제를 해결하며, 이로써 양자 컴퓨팅이 고전 컴퓨팅에 비해 정량화 가능한 이점을 제공합니다.
이것은 겸손한 이점일 수 있습니다 — 두 번의 질의에 비해 한 번의 질의 — 하지만 우리는 어딘가에서 시작해야 합니다.
과학적 진보는 때때로 겉으로는 보잘것없는 기원을 가집니다.

여기에 도이치 알고리즘을 설명하는 양자 회로가 있습니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/Deutsch-circuit.svg" alt="Example of a Boolean circuit" style="width: 50%;" />


##  2. **분석**

도이치 알고리즘을 분석하기 위해, 우리는 위 회로의 작동을 추적하고 이 그림에 제시된 시점의 큐비트 상태를 식별할 것입니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/Deutsch-circuit-states.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

초기 상태는 $\vert 1\rangle \vert 0 \rangle$ 이고, 회로 왼쪽에 있는 두 개의 하다마르 연산은 이 상태를 $\vert \pi_1 \rangle = \vert - \rangle \vert + \rangle$로 변환.
\[
\vert \pi_1 \rangle = \vert - \rangle \vert + \rangle
= \frac{1}{2} \bigl( \vert 0\rangle - \vert 1\rangle \bigr) \vert 0\rangle
\+ \frac{1}{2} \bigl( \vert 0\rangle - \vert 1\rangle \bigr) \vert 1\rangle.
\]

(항상 그렇듯이, 우리는 Qiskit의 큐비트 순서 규칙을 따르고 있으며, 이는 상단 큐비트를 오른쪽에, 하단 큐비트를 왼쪽에 둡니다).


다음으로, $U_f$ 게이트가 수행됩니다.
$U_f$ 게이트의 정의에 따르면, 상단/가장 오른쪽 큐비트의 고전적 상태에 대한 함수 $f$의 값은 하단/가장 왼쪽 큐비트에 XOR되어 $\vert \pi_1\rangle$을 $\vert \pi_2 \rangle$ 상태로 변환합니다.
\[
\vert \pi_2 \rangle
= \frac{1}{2} \bigl( \vert 0 \oplus f(0) \rangle - \vert 1 \oplus f(0) \rangle \bigr) \vert 0 \rangle
\+ \frac{1}{2} \bigl( \vert 0 \oplus f(1) \rangle - \vert 1 \oplus f(1) \rangle \bigr) \vert 1 \rangle.
\]

우리는 다음 공식을 통해 이 표현을 단순화할 수 있습니다.
$$
\vert 0 \oplus a\rangle - \vert 1 \oplus a\rangle = (-1)^a \bigl( \vert 0\rangle - \vert 1\rangle \bigr)
$$

이 공식은 가능한 두 값 $a \in \Sigma$ 모두에 대해 작동합니다.
더 명시적으로, 두 가지 경우는 다음과 같습니다.

<br>
$$
\begin{aligned}
\vert 0 \oplus 0\rangle - \vert 1 \oplus 0\rangle
& = \vert 0 \rangle - \vert 1 \rangle
= (-1)^0 \bigl( \vert 0\rangle - \vert 1\rangle \bigr)\\
\vert 0 \oplus 1\rangle - \vert 1 \oplus 1\rangle & = \vert 1 \rangle - \vert 0\rangle
= (-1)^1 \bigl( \vert 0\rangle - \vert 1\rangle \bigr)
\end{aligned}
$$


따라서, 우리는 $\vert\pi_2\rangle$를 다음과 같이 다르게 표현할 수 있습니다.
\[
  \vert\pi_2\rangle
   = \frac{1}{2} (-1)^{f(0)} \bigl( \vert 0 \rangle - \vert 1 \rangle \bigr) \vert 0 \rangle
  \+ \frac{1}{2} (-1)^{f(1)} \bigl( \vert 0 \rangle - \vert 1 \rangle \bigr) \vert 1 \rangle \]
   \[ = \vert - \rangle \biggl( \frac{(-1)^{f(0)} \vert 0\rangle + (-1)^{f(1)} \vert 1\rangle}{\sqrt{2}}\biggr).
\]

흥미로운 일이 방금 일어났습니다!
표준 기저 상태에 대한 $U_f$ 게이트의 동작은 상단/가장 오른쪽 큐비트를 그대로 두고 함수 값을 하단/가장 왼쪽 큐비트에 XOR하지만, 여기서는 상단/가장 오른쪽 큐비트의 상태가 (일반적으로) 변경된 반면, 하단/가장 왼쪽 큐비트의 상태는 $U_f$ 게이트가 수행되기 전과 후에 $\vert - \rangle$ 상태로 동일하게 유지되는 것을 볼 수 있습니다.
이 현상은 **위상 킥백**으로 알려져 있으며, 우리는 곧 이에 대해 더 자세히 이야기할 것입니다.

마지막으로, 합에서 $(-1)^{f(0)}$ 인수를 밖으로 빼는 최종 단순화를 통해 우리는 상태 $\vert\pi_2\rangle$의 다음 표현을 얻는다.
<br>
$$
\begin{aligned}
  \vert\pi_2\rangle
  & = (-1)^{f(0)} \vert - \rangle
      \biggl( \frac{\vert 0\rangle + (-1)^{f(0) \oplus f(1)} \vert 1\rangle}{\sqrt{2}}\biggr) \\
  & = \begin{cases}
        (-1)^{f(0)} \vert - \rangle \vert + \rangle & \text{if $f(0) \oplus f(1) = 0$}\\[1mm]
        (-1)^{f(0)} \vert - \rangle \vert - \rangle & \text{if $f(0) \oplus f(1) = 1$}.
      \end{cases}
\end{aligned}
$$

이 표현에서, 우리는 순수하게 대수적인 관점에서 예상할 수 있는 $f(1) - f(0)$ 대신 $-1$의 지수에 $f(0) \oplus f(1)$을 가지고 있지만, 어느 쪽이든 같은 결과를 얻는다는 점에 유의하십시오.
이는 임의의 정수 $k$에 대한 값 $(-1)^k$가 $k$가 짝수인지 홀수인지에만 의존하기 때문입니다.

상단 큐비트에 최종 하다마르 게이트를 적용하면 상태 $\vert \pi_3 \rangle$가 됩니다.
<br>
$$
\vert \pi_3 \rangle =
\begin{cases}
  (-1)^{f(0)} \vert - \rangle \vert 0 \rangle & \text{if $f(0) \oplus f(1) = 0$}\\[1mm]
  (-1)^{f(0)} \vert - \rangle \vert 1 \rangle & \text{if $f(0) \oplus f(1) = 1$},
\end{cases}
$$

이것은 오른쪽/가장 위 큐비트가 측정될 때 확률 1로 올바른 결과를 가져옵니다.

##  3. **위상 킥백에 대한 추가 설명**
넘어가기 전에, 위상 킥백 현상에 대해 더 명확하게 설명할 수 있는 약간 다른 관점에서 위의 분석을 살펴보겠습니다.

첫째, 다음 공식이 모든 비트 $b,c \in \Sigma$의 선택에 대해 작동한다는 점에 유의하십시오.
\[
\vert b \oplus c\rangle = X^c \vert b \rangle
\]

이것은 두 가지 가능한 값 $c=0$과 $c=1$에 대해 확인하여 검증할 수 있습니다.
\[
\vert b \oplus 0 \rangle  = \vert b\rangle = \mathbb{I} \vert b \rangle = X^0 \vert b \rangle \]
\[\vert b \oplus 1 \rangle  = \vert \neg b\rangle = X \vert b \rangle = X^1 \vert b \rangle.
\]

이 공식을 사용하여, 우리는 모든 비트 $a,b \in \Sigma$에 대해 $U_f \bigl(\vert b\rangle \vert a \rangle\bigr)$임을 알 수 있습니다.
\[
U_f \bigl(\vert b\rangle \vert a \rangle\bigr)
= \vert b \oplus f(a) \rangle \vert a \rangle
= \bigl(X^{f(a)}\vert b \rangle\bigr) \vert a \rangle
\]

이 공식이 $b=0$과 $b=1$에 대해 참이기 때문에, 선형성에 의해 모든 큐비트 상태 벡터 
\[
U_f \bigl( \vert \psi \rangle \vert a \rangle \bigr) = \bigl(X^{f(a)}\vert \psi \rangle\bigr) \vert a \rangle
\]

따라서 다음을 알 수 있다.
\[
U_f \bigl( \vert - \rangle \vert a \rangle \bigr) = \bigl(X^{f(a)} \vert - \rangle \bigr) \vert a \rangle
= (-1)^{f(a)} \vert - \rangle \vert a \rangle.
\]

이것이 작동하는 핵심은 $X\vert - \rangle = - \vert - \rangle$라는 것입니다.
수학적 용어로, 벡터 $\vert - \rangle$는 **고유값** $-1$을 갖는 행렬 $X$의 **고유 벡터**입니다.

우리는 다가오는 *위상 추정 및 인수분해* 강의에서 고유 벡터와 고유값을 더 자세히 논의할 것입니다. 거기서 위상 킥백 현상이 다른 유니터리 연산으로 일반화됩니다.

스칼라가 텐서 곱을 자유롭게 통과한다는 점을 염두에 두고, 우리는 $U_f$ 연산이 위 분석에서 $\vert \pi_1\rangle$을 $\vert \pi_2\rangle$로 어떻게 변환하는지에 대한 대안적인 추론 방법을 찾을 수 있습니다.

<br>
$$
\begin{aligned}
  \vert \pi_2 \rangle
  & = U_f \bigl( \vert - \rangle \vert + \rangle \bigr)\\
  & = \frac{1}{\sqrt{2}} U_f \bigl(\vert - \rangle \vert 0\rangle \bigr)
    + \frac{1}{\sqrt{2}} U_f \bigl(\vert - \rangle \vert 1\rangle \bigr)\\
  & = \vert - \rangle \biggl( \frac{(-1)^{f(0)} \vert 0\rangle + (-1)^{f(1)} \vert 1\rangle}{\sqrt{2}}\biggr).
\end{aligned}
$$