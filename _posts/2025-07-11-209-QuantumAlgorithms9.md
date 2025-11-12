---
title: 6차시 9:Fundamentals of quantum algorithms(Phase estimation procedure)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 위상 추정 절차

- 출처: [Phase estimation procedure](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/phase-estimation-procedure)

다음으로, 위상 추정 문제를 해결하기 위한 양자 알고리즘인 **위상 추정 절차**에 대해 논의할 것입니다.

먼저, 이 방법의 기본 직관을 설명하는 저정밀 워밍업부터 시작하겠습니다.
그 다음, 위상 추정 절차에서 사용되는 중요한 양자 연산인 **양자 푸리에 변환**과 그 양자 회로 구현에 대해 이야기할 것입니다.
양자 푸리에 변환을 이해하고 나면, 위상 추정 절차를 전반적으로 설명하고 그 성능을 분석할 것입니다.

## 1. 워밍업: 저정밀도로 위상 근사하기

위상 추정 문제에 대한 저정밀 해법을 제공하는 위상 추정 절차의 몇 가지 간단한 버전부터 시작하겠습니다.
이는 이 강의의 뒷부분에서 보게 될 일반적인 절차 뒤에 숨겨진 직관을 설명하는 데 도움이 됩니다.

### 1.1 위상 킥백 사용하기

우리가 찾고 있는 값 $\theta$에 대해 무언가를 배울 수 있게 해주는 위상 추정 문제에 대한 간단한 접근 방식은 **위상 킥백** 현상에 기반을 둡니다.
보시다시피, 이는 본질적으로 이 강의 뒷부분에서 논의될 일반적인 위상 추정 절차의 단일 큐비트 버전입니다.

위상 추정 문제의 입력의 일부로, 우리는 연산 $U$에 대한 유니터리 양자 회로를 가지고 있습니다.
우리는 이 회로의 설명을 사용하여 **제어된**-$U$ 연산을 위한 회로를 만들 수 있으며, 이는 이 그림이 시사하는 바와 같이 묘사될 수 있습니다 (왼쪽에는 양자 게이트로 간주되는 연산 $U$, 오른쪽에는 제어된-$U$ 연산).

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/uncontrolled-and-controlled-unitary.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

우리는 $U$를 위한 회로에 제어 큐비트를 추가한 다음, $U$를 위한 회로의 모든 게이트를 해당 게이트의 제어된 버전으로 교체함으로써 제어된-$U$ 연산을 위한 양자 회로를 만들 수 있습니다 — 따라서 우리의 새로운 제어 큐비트 하나가 $U$를 위한 회로의 모든 단일 게이트를 효과적으로 제어합니다.

이는 우리 회로의 모든 게이트에 대한 제어된 버전이 있어야 함을 요구하지만, 우리의 게이트 세트에 포함되어 있지 않은 경우에도 이러한 제어된 연산을 위한 회로를 항상 만들 수 있습니다.

이제 다음 회로를 고려해 봅시다. 여기서 맨 위 큐비트를 제외한 모든 큐비트의 입력 상태$\vert\psi\rangle$는 $U$의 양자 상태 고유 벡터입니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/estimate-phase-with-kickback.svg" alt="Example of a Boolean circuit" style="width: 50%;" />


이 회로의 측정 결과 확률은 고유 벡터$\vert\psi\rangle$에 해당하는 $U$의 고유값에 따라 달라집니다.
정확히 어떻게 되는지 알아보기 위해 회로를 자세히 분석해 봅시다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/estimate-phase-with-kickback-analysis.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

회로의 초기 상태는 다음과 같고, 
\[
\vert\pi_0\rangle = \vert\psi\rangle \vert 0\rangle
\]

첫 번째 하닥마르 게이트는 이 상태를 다음으로 변환합니다.
\[
\vert\pi_1\rangle = \vert\psi\rangle \vert +\rangle
= \frac{1}{\sqrt{2}} \vert\psi\rangle \vert 0\rangle + \frac{1}{\sqrt{2}} \vert\psi\rangle \vert 1\rangle.
\]

다음으로, 제어된-$U$ 연산이 수행되어 다음 상태가 됩니다.
\[
\vert\pi_2\rangle
= \frac{1}{\sqrt{2}} \vert\psi\rangle \vert 0\rangle + \frac{1}{\sqrt{2}} \bigl(U \vert\psi\rangle\bigr) \vert 1\rangle.
\]

$\vert\psi\rangle$이 고유값 $\lambda = e^{2\pi\theta}$를 갖는 $U$의 고유벡터라고 가정하면,
\[
\vert\pi_2\rangle
= \frac{1}{\sqrt{2}} \vert\psi\rangle \vert 0\rangle + \frac{e^{2\pi i \theta}}{\sqrt{2}} \vert\psi\rangle \vert 1\rangle = \vert\psi\rangle \otimes \left( \frac{1}{\sqrt{2}} \vert 0\rangle + \frac{e^{2\pi i \theta}}{\sqrt{2}} \vert 1\rangle\right)
\]

상태를 다음과 같이 다르게 표현할 수 있습니다.

여기서 우리는 위상 킥백 현상을 관찰합니다.
이번에는 Deutsch 알고리즘 및 Deutsch-Jozsa 알고리즘에서와는 약간 다릅니다. 쿼리 게이트를 사용하지 않기 때문이지만, 아이디어는 비슷합니다.

마지막으로, 두 번째 하닥마르 게이트가 수행됩니다. 약간의 단순화 후, 이 상태에 대한 다음 표현을 얻습니다.
\[
\vert\pi_3\rangle
= \vert\psi\rangle \otimes \left( \frac{1+ e^{2\pi i \theta}}{2} \vert 0\rangle + \frac{1 - e^{2\pi i \theta}}{2} \vert 1\rangle\right)
\]

따라서 측정은 다음 확률로 $0$과 $1$의 결과를 산출합니다.
<br>
$$
\begin{aligned}
p_0 &= \left\vert \frac{1+ e^{2\pi i \theta}}{2} \right\vert^2 = \cos^2(\pi\theta)\\[1mm]
p_1 &= \left\vert \frac{1- e^{2\pi i \theta}}{2} \right\vert^2 = \sin^2(\pi\theta).
\end{aligned}
$$

여기 $\theta$의 함수로 가능한 두 가지 결과인 $0$과 $1$에 대한 확률을 나타낸 그래프가 있습니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/kickback-probabilities.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

당연히 두 확률의 합은 항상 $1$입니다.
$\theta = 0$일 때 측정 결과는 항상 $0$이고, $\theta = 1/2$일 때 측정 결과는 항상 $1$이라는 점에 주목하십시오.
따라서 측정 결과가 $\theta$가 정확히 무엇인지를 밝히지는 않지만, $\theta$에 대한 일부 정보를 제공하며 — 만약 $\theta = 0$ 또는 $\theta = 1/2$ 둘 중 하나라고 약속되었다면, 우리는 회로를 통해 오류 없이 어떤 것이 올바른지 알 수 있었습니다.

직관적으로 말해, 우리는 회로의 측정 결과를 $\theta$에 대한 "1비트 정확도"의 추측으로 생각할 수 있습니다.
다시 말해, $\theta$를 이진 표기법으로 쓰고 1비트로 반올림한다면, 다음과 같은 숫자를 얻을 것입니다.
<br>
$$
0.a = \begin{cases}
0 & a = 0\\
\frac{1}{2} & a = 1.
\end{cases}
$$

측정 결과는 비트 $a$에 대한 추측으로 볼 수 있습니다.
$\theta$가 $0$도 $1/2$도 아닐 때, 추측이 틀릴 확률이 0이 아니지만 — 오류를 범할 확률은 $0$ 또는 $1/2$에 가까워질수록 점점 작아집니다.

이 절차에서 두 하닥마르 게이트가 어떤 역할을 하는지 묻는 것은 자연스러운 일입니다.

*   **첫 번째 하닥마르 게이트**는 제어 큐비트를 $\vert 0\rangle$과 $\vert 1\rangle$의 균일 중첩으로 설정하여, 위상 킥백이 발생할 때 $\vert 0\rangle$ 상태가 아닌 $\vert 1\rangle$ 상태에 대해 발생하도록 합니다. 이는 측정 결과에 영향을 미치는 **상대적** 위상 차이를 생성합니다. 만약 우리가 이렇게 하지 않고 위상 킥백이 **전역** 위상을 생성했다면, 다른 측정 결과를 얻을 확률에 아무런 영향을 미치지 않았을 것입니다.

*   **두 번째 하닥마르 게이트**는 **간섭** 현상을 통해 숫자 $\theta$에 대해 무언가를 배우도록 해줍니다. 두 번째 하닥마르 게이트 이전에 맨 위 큐비트의 상태는 다음과 같으며,
\[
    \frac{1}{\sqrt{2}} \vert 0\rangle + \frac{e^{2\pi i \theta}}{\sqrt{2}} \vert 1\rangle,
\]

이 상태를 측정한다면, $0$과 $1$을 각각 $1/2$의 확률로 얻게 되어 $\theta$에 대해 아무것도 알려주지 않을 것입니다. 그러나 두 번째 하닥마르 게이트를 수행함으로써 숫자 $\theta$가 출력 확률에 영향을 미치게 합니다.

### 1.2 위상 두 배로 늘리기

위 회로는 위상 킥백 현상을 사용하여 $\theta$를 1비트 정확도로 근사합니다.
어떤 상황에서는 1비트 정확도만으로 충분할 수 있지만 — 인수분해에는 그보다 훨씬 더 높은 정확도가 필요합니다.
자연스러운 질문은, $\theta$에 대해 어떻게 더 많이 알 수 있는가? 입니다.

우리가 할 수 있는 한 가지 매우 간단한 방법은 우리 회로의 제어된-$U$ 연산을 이 회로처럼 이 연산의 **두 개의 복사본**으로 대체하는 것입니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/double-phase-kickback.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

제어된-$U$ 연산의 두 개의 복사본은 제어된-$U^2$ 연산과 동일합니다.
만약 $\vert\psi\rangle$가 고유값 $\lambda = e^{2\pi i \theta}$를 가지는 $U$의 고유 벡터라면, 이 상태는 또한 $U^2$의 고유 벡터이며, 이번에는 고유값 $\lambda^2 = e^{2\pi i (2\theta)}$를 가집니다.

따라서 이 버전의 회로를 실행하면, 숫자 $\theta$가 $2\theta$로 대체되는 것을 제외하고는 이전과 동일한 계산을 효과적으로 수행하는 것입니다.
여기 $\theta$가 $0$에서 $1$까지 변할 때 출력 확률을 보여주는 그래프가 있습니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/double-kickback-probabilities.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

이렇게 하면 $\theta$에 대한 추가 정보를 얻을 수 있습니다.
만약 $\theta$의 이진 표현이 다음과 같다면, 
\[
\theta = 0.a_1 a_2 a_3\cdots
\]
$\theta$를 두 배로 늘리면 이진 소수점이 오른쪽으로 한 자리 이동하는 효과가 있습니다.
\[
2\theta = a_1. a_2 a_3\cdots
\]

그리고 단위 원을 따라 움직일 때 $\theta = 1$을 $\theta = 0$과 동일시하므로, 비트 $a_1$이 우리의 확률에 영향을 미치지 않으며, $\theta$를 두 비트로 반올림하면 이진 소수점 다음의 **두 번째** 비트에 대한 추측을 효과적으로 얻는다는 것을 알 수 있습니다.
예를 들어, 만약 $\theta$가 $0$ 또는 $1/4$ 둘 중 하나라는 것을 미리 알고 있었다면, 우리는 측정 결과를 전적으로 신뢰하여 어떤 것인지 알 수 있었을 것입니다.

하지만 이 추정치가 $\theta$에 대해 가능한 가장 정확한 정보를 제공하기 위해 원래의 (두 배로 늘리지 않은) 위상 킥백 회로에서 배운 것과 어떻게 조화되어야 하는지는 즉시 명확하지 않습니다.
그러니 한 걸음 물러서서 어떻게 진행해야 할지 생각해 봅시다.

### 1.3 두 큐비트 위상 추정

위에 설명된 두 가지 옵션을 개별적으로 고려하기보다는, 다음과 같이 단일 회로로 결합해 봅시다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/two-bit-phase-estimation-initial.svg" alt="Example of a Boolean circuit" style="width: 50%;" />


제어된 연산 뒤의 하닥마르 게이트는 제거되었고 아직 측정이 없습니다.
$\theta$에 대해 가능한 한 많은 것을 배우기 위한 우리의 옵션을 고려하면서 회로에 더 많은 것을 추가할 것입니다.

만약 $\vert\psi\rangle$가 $U$의 고유 벡터일 때 이 회로를 실행하면, 아래쪽 큐비트들의 상태는 전체 회로 동안 $\vert\psi\rangle$로 유지될 것이고, 위상들은 맨 위 두 큐비트의 상태로 "킥백"될 것입니다.
다음 그림을 통해 회로를 신중하게 분석해 봅시다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/two-bit-phase-estimation-states.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

상태는 다음과 같이 쓸 수 있습니다.
\[
\vert\pi_1\rangle = \vert \psi\rangle \otimes \frac{1}{2} \sum_{a_0 = 0}^1 \sum_{a_1 = 0}^1 \vert a_1 a_0 \rangle.
\]

첫 번째 제어된-$U$ 연산이 수행될 때, 고유값 $\lambda = e^{2\pi i\theta}$는 $a_0$ (맨 위 큐비트)가 $1$일 때 위상으로 킥백되지만, $0$일 때는 킥백되지 않습니다.
따라서 결과 상태를 다음과 같이 표현할 수 있습니다.
\[
\vert\pi_2\rangle
= \vert\psi\rangle \otimes \frac{1}{2} \sum_{a_0=0}^1 \sum_{a_1=0}^1 e^{2 \pi i a_0 \theta} \vert a_1 a_0 \rangle.
\]

두 번째 및 세 번째 제어된-$U$ 게이트는 $a_0$ 대신 $a_1$에 대해 유사한 작업을 수행하며, $\theta$는 $2\theta$로 대체됩니다.
결과 상태를 다음과 같이 표현할 수 있습니다.
\[
\vert\pi_3\rangle
= \vert\psi\rangle\otimes\frac{1}{2}\sum_{a_0 = 0}^1 \sum_{a_1 = 0}^1
e^{2\pi i (2 a_1 + a_0)\theta} \vert a_1 a_0 \rangle.
\]

이진 문자열 $a_1 a_0$을 이진 표기법으로 $x = 2 a_1 + a_0$인 정수 $x \in \{0,1,2,3\}$로 생각한다면, 이 상태를 다음과 같이 다르게 표현할 수 있습니다.
\[
\vert\pi_3\rangle = \vert \psi\rangle \otimes \frac{1}{2} \sum_{x = 0}^3 e^{2\pi i x \theta} \vert x \rangle
\]

우리의 목표는 이 상태에서 $\theta$에 대한 가능한 한 많은 정보를 추출하는 것입니다.

이 시점에서 우리는 $\theta = y/4$ (여기서 $y\in\{0,1,2,3\}$는 정수)라고 약속된 특별한 경우를 고려할 것입니다.
다시 말해, 우리는 $\theta\in \{0, 1/4, 1/2, 3/4\}$를 가지므로, 이 숫자를 두 비트의 이진 표기법으로 $0.00, 0.01, 0.10,$ 또는 $0.11$과 같이 정확하게 표현할 수 있습니다.
일반적으로 $\theta$는 이 네 가지 값 중 하나가 아닐 수 있지만, 이 특별한 경우를 생각하면 일반적인 $\theta$에 대한 정보를 가장 효과적으로 추출하는 방법을 알아내는 데 도움이 될 것입니다.

먼저 각 가능한 $y \in \{0, 1, 2, 3\}$ 값에 대한 두 큐비트 상태 벡터를 정의할 것입니다.
\[
\vert \phi_y\rangle = \frac{1}{2} \sum_{x = 0}^3 e^{2\pi i x (\frac{y}{4})} \vert x \rangle
= \frac{1}{2} \sum_{x = 0}^3 e^{2\pi i \frac{x y}{4}} \vert x \rangle
\]

지수를 단순화한 후, 이 벡터들을 다음과 같이 쓸 수 있습니다.
<br>
$$
\begin{aligned}
\vert\phi_0\rangle & = \frac{1}{2} \vert 0 \rangle + \frac{1}{2} \vert 1 \rangle + \frac{1}{2} \vert 2 \rangle + \frac{1}{2} \vert 3 \rangle \\[3mm]
\vert\phi_1\rangle & = \frac{1}{2} \vert 0 \rangle + \frac{i}{2} \vert 1 \rangle - \frac{1}{2} \vert 2 \rangle - \frac{i}{2} \vert 3 \rangle \\[3mm]
\vert\phi_2\rangle & = \frac{1}{2} \vert 0 \rangle - \frac{1}{2} \vert 1 \rangle + \frac{1}{2} \vert 2 \rangle - \frac{1}{2} \vert 3 \rangle \\[3mm]
\vert\phi_3\rangle & = \frac{1}{2} \vert 0 \rangle - \frac{i}{2} \vert 1 \rangle - \frac{1}{2} \vert 2 \rangle + \frac{i}{2} \vert 3 \rangle
\end{aligned}
$$

이 벡터들은 직교합니다: 그들 중 어떤 쌍을 선택하여 내적을 계산하더라도 $0$을 얻습니다.
각각은 또한 단위 벡터이므로, $\{\vert\phi_0\rangle, \vert\phi_1\rangle, \vert\phi_2\rangle, \vert\phi_3\rangle\}$는 정규 직교 기저입니다.
따라서 우리는 그들을 완벽하게 구별할 수 있는 측정이 있다는 것을 즉시 압니다 — 즉, 우리에게 그들 중 하나가 주어졌지만 어떤 것인지 모를 때, 오류 없이 어떤 것인지 알아낼 수 있습니다.

양자 회로로 그러한 구별을 수행하기 위해, 먼저 표준 기저 상태를 위에 나열된 네 가지 상태로 변환하는 유니터리 연산 $V$를 정의할 수 있습니다.

<br>
$$
\begin{aligned}
V \vert 00 \rangle & = \vert\phi_0\rangle \\
V \vert 01 \rangle & = \vert\phi_1\rangle \\
V \vert 10 \rangle & = \vert\phi_2\rangle \\
V \vert 11 \rangle & = \vert\phi_3\rangle
\end{aligned}
$$

$V$를 $4\times 4$ 행렬로 작성하는 것은 단지 $V$의 열을 상태 $\vert\phi_0\rangle,\ldots,\vert\phi_3\rangle$로 취하는 문제입니다.
<br>
$$
V =
\frac{1}{2}
\begin{pmatrix}
1 & 1 & 1 & 1\\[1mm]
1 & i & -1 & -i\\[1mm]
1 & -1 & 1 & -1\\[1mm]
1 & -i & -1 & i
\end{pmatrix}
$$

이것은 특별한 행렬이며, 일부 독자는 이전에 접했을 가능성이 있습니다. 이는 4차원 **이산 푸리에 변환**과 관련된 행렬입니다.
이 사실을 고려하여, $V$ 대신 **$QFT_4$**라는 이름으로 부르겠습니다.
**$QFT$**는 **양자 푸리에 변환**의 약자입니다 — 이는 본질적으로 이산 푸리에 변환을 유니터리 연산으로 본 것과 같습니다.
양자 푸리에 변환에 대해 더 자세하고 일반적으로 곧 논의할 것입니다.
<br>
$$
QFT_4 =
\frac{1}{2}
\begin{pmatrix}
1 & 1 & 1 & 1\\[1mm]
1 & i & -1 & -i\\[1mm]
1 & -1 & 1 & -1\\[1mm]
1 & -i & -1 & i
\end{pmatrix}
$$

우리는 이 연산의 역을 수행하여 반대 방향으로, 즉 상태 $\vert\phi_0\rangle,\ldots,\vert\phi_3\rangle$를 표준 기저 상태 $\vert 0\rangle,\ldots,\vert 3\rangle$로 변환할 수 있습니다.
이렇게 하면, 우리는 $\theta$를 $\theta = y/4$로 설명하는 $y\in\{0,1,2,3\}$ 값을 측정하여 알 수 있습니다.
여기 이를 수행하는 양자 회로 다이어그램이 있습니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/two-bit-phase-estimation.svg" alt="Example of a Boolean circuit" style="width: 50%;" />

요약하자면, $y\in\{0,1,2,3\}$에 대해 $\theta = y/4$일 때 이 회로를 실행하면, 측정 직전의 상태는 $\vert \psi\rangle \vert y\rangle$ (y는 두 비트 이진 문자열로 인코딩됨)가 되므로, 측정은 $y$ 값을 오류 없이 밝혀낼 것입니다.

이 회로는 $\theta \in \{0,1/4,1/2,3/4\}$인 특별한 경우에 의해 동기 부여되었지만 — 우리가 원하는 $U$와 $\vert \psi\rangle$의 어떤 선택에 대해서도, 따라서 어떤 $\theta$ 값에 대해서도 실행할 수 있습니다.
여기 임의의 $\theta$ 선택에 대해 회로가 생성하는 출력 확률의 그래프가 있습니다.

<img src="{{site.url}}{{site.baseurl}}/assets/images/QC2/two-bit-probabilities.svg" alt="Example of a Boolean circuit" style="width: 50%;" />


이는 이 강의의 앞부분에서 설명된 단일 큐비트 변형에 비해 명확한 개선입니다.
완벽하지는 않습니다 — 잘못된 답을 줄 수도 있습니다 — 하지만 답은 $y/4$가 $\theta$에 가까운 $y$ 값 쪽으로 크게 치우쳐 있습니다.
특히, 가장 가능성 있는 결과는 항상 $\theta$에 가장 가까운 $y/4$ 값에 해당하며 (이전과 같이 $\theta = 0$과 $\theta = 1$을 동일시함), 그래프에서 보면 이 $x$에 대한 가장 가까운 값은 항상 40%를 약간 넘는 확률로 나타나는 것 같습니다.
예를 들어 $\theta = 0.375$와 같이 $\theta$가 두 값의 정확히 중간에 있을 때는, 동일하게 가까운 두 $y$ 값이 동일한 확률을 가집니다.

### 1.4 다중 큐비트로 일반화 준비

하나 대신 두 개의 제어 큐비트를 사용하고 4차원 양자 푸리에 변환의 역을 함께 사용하여 방금 얻은 개선을 고려할 때, 더 많은 제어 큐비트를 추가하여 이를 더 일반화하는 것을 고려하는 것은 자연스러운 일입니다.
이렇게 하면 일반적인 **위상 추정 절차**를 얻습니다.
곧 이것이 어떻게 작동하는지 보겠지만, 이를 정확하게 설명하기 위해서는 양자 푸리에 변환을 더 일반적으로 논의해야 합니다. 다른 차원에서 어떻게 정의되는지, 그리고 양자 회로로 어떻게 구현할 수 있는지 (또는 그 역을) 알아보기 위해서입니다.

## 2. 양자 푸리에 변환

양자 푸리에 변환은 어떤 양의 정수 차원 $N$에 대해서도 정의될 수 있는 유니터리 연산입니다.
이 섹션에서는 이 연산이 어떻게 정의되는지, 그리고 $N = 2^m$일 때 $O(m^2)$의 비용으로 $m$ 큐비트 상에서 양자 회로로 어떻게 구현될 수 있는지 알아볼 것입니다.

양자 푸리에 변환을 설명하는 행렬은 **이산 푸리에 변환**이라고 알려진 $N$차원 벡터에 대한 유사한 연산에서 파생됩니다.
이 연산은 여러 가지 방식으로 생각할 수 있습니다.
예를 들어, 우리는 이산 푸리에 변환을 순수하게 추상적이고 수학적인 용어로 선형 매핑으로 생각할 수 있습니다.
또는 우리는 $N$차원 복소수 벡터가 주어지고 (항목의 실수부와 허수부를 인코딩하기 위해 이진 표기법을 사용한다고 가정합시다) 이산 푸리에 변환을 적용하여 얻은 $N$차원 벡터를 계산하는 것이 목표인 계산적 용어로 생각할 수 있습니다.
우리의 초점은 세 번째 방식, 즉 이 변환을 양자 시스템에서 수행할 수 있는 유니터리 연산으로 보는 것에 맞춰질 것입니다.

주어진 입력 벡터에 대해 이산 푸리에 변환을 계산하는 효율적인 알고리즘이 있는데, 이를 **고속 푸리에 변환**이라고 합니다.
이는 신호 처리 및 기타 여러 분야에 응용되며, 많은 사람들에게 지금까지 발견된 가장 중요한 알고리즘 중 하나로 여겨집니다.

결과적으로, 우리가 연구할 $N$이 2의 거듭제곱일 때 양자 푸리에 변환의 구현은 고속 푸리에 변환을 가능하게 하는 바로 그 동일한 기본 구조에 기반을 둡니다.

### 2.1 양자 푸리에 변환의 정의

양자 푸리에 변환을 정의하기 위해, 먼저 각 양의 정수 $N$에 대해 복소수 $\omega_N$를 다음과 같이 정의할 것입니다.
\[
\omega_N = e^{\frac{2\pi i}{N}} = \cos\left(\frac{2\pi}{N}\right) + i \sin\left(\frac{2\pi}{N}\right).
\]

이는 $1$에서 시작하여 반시계 방향으로 $2\pi/N$ 라디안 각도, 또는 원주 둘레의 $1/N$만큼 이동할 때 복소수 단위 원에서 얻는 숫자입니다. 몇 가지 예는 다음과 같습니다.
<br>
$$
\begin{gathered}
\omega_1 = 1\\[1mm]
\omega_2 = -1\\[1mm]
\omega_3 = -\frac{1}{2} + \frac{\sqrt{3}}{2} i\\[2mm]
\omega_4 = i\\[1mm]
\omega_8 = \frac{1+i}{\sqrt{2}}\\[3mm]
\omega_{16} = \frac{\sqrt{2 + \sqrt{2}}}{2} + \frac{\sqrt{2 - \sqrt{2}}}{2} i\\[2mm]
\omega_{100} \approx 0.998 + 0.063 i
\end{gathered}
$$

이제 우리는 표준 기저 상태 $\vert 0\rangle,\ldots,\vert N-1\rangle$와 행과 열이 연관된 $N\times N$ 행렬로 설명되는 $N$차원 양자 푸리에 변환을 정의할 수 있습니다.
우리는 위상 추정을 위해 $N = 2^m$이 2의 거듭제곱인 경우에만 이 연산이 필요할 것이지만, 이 연산은 어떤 양의 정수 $N$에 대해서도 정의될 수 있습니다.
\[
QFT_N = \frac{1}{\sqrt{N}} \sum_{x = 0}^{N-1} \sum_{y = 0}^{N-1} \omega_N^{xy} \vert x \rangle\langle y\vert
\]

이미 언급했듯이, 이것은 $N$차원 **이산 푸리에 변환**과 관련된 행렬입니다.
종종 이 행렬의 정의에는 선행 계수 $1/\sqrt{N}$가 포함되지 않지만, 유니터리 행렬을 얻기 위해서는 이를 포함해야 합니다.

여기 몇 가지 작은 $N$ 값에 대해 행렬로 작성된 양자 푸리에 변환이 있습니다.

<br>
$$
QFT_1 = \begin{pmatrix} 1 \end{pmatrix}
$$
<br>
<br>
$$
QFT_2 =
\frac{1}{\sqrt{2}} \begin{pmatrix} 1 & 1\\[1mm] 1 & -1 \end{pmatrix}
$$
<br>
<br>
$$
QFT_3 =
\frac{1}{\sqrt{3}}
\begin{pmatrix}
   1 & 1 & 1\\[2mm]
   1 & \frac{-1 + i\sqrt{3}}{2} & \frac{-1 - i\sqrt{3}}{2}\\[2mm]
   1 & \frac{-1 - i\sqrt{3}}{2} & \frac{-1 + i\sqrt{3}}{2}
\end{pmatrix}
$$
<br>
<br>
$$
QFT_4 =
\frac{1}{2}
\begin{pmatrix}
1 & 1 & 1 & 1\\[1mm]
1 & i & -1 & -i\\[1mm]
1 & -1 & 1 & -1\\[1mm]
1 & -i & -1 & i
\end{pmatrix}
$$
<br>
<br>
$$
QFT_8 =
\frac{1}{2\sqrt{2}}
\begin{pmatrix}
  1 & 1 & 1 & 1 & 1 & 1 & 1 & 1\\[2mm]
  1 & \frac{1+i}{\sqrt{2}} & i & \frac{-1+i}{\sqrt{2}} & -1 & \frac{-1-i}{\sqrt{2}} & -i & \frac{1-i}{\sqrt{2}}\\[2mm]
  1 & i & -1 & -i & 1 & i & -1 & -i\\[2mm]
  1 & \frac{-1+i}{\sqrt{2}} & -i & \frac{1+i}{\sqrt{2}} & -1 & \frac{1-i}{\sqrt{2}} & i & \frac{-1-i}{\sqrt{2}}\\[2mm]
  1 & -1 & 1 & -1 & 1 & -1 & 1 & -1\\[2mm]
  1 & \frac{-1-i}{\sqrt{2}} & i & \frac{1-i}{\sqrt{2}} & -1 & \frac{1+i}{\sqrt{2}} & -i & \frac{-1+i}{\sqrt{2}}\\[2mm]
  1 & -i & -1 & i & 1 & -i & -1 & i\\[2mm]
  1 & \frac{1-i}{\sqrt{2}} & -i & \frac{-1-i}{\sqrt{2}} & -1 & \frac{-1+i}{\sqrt{2}} & i & \frac{1+i}{\sqrt{2}}\\[2mm]
\end{pmatrix}
$$

특히, $QFT_2$는 하다마르 연산의 다른 이름이라는 점에 주목하십시오.

### 2.2 단일성

$QFT_N$ 이 임의의 $N$에 대해 단일성을 만족하는지 확인해 보자. 이를 확인하는 한 가지 방법은 그 열들이 정규직교 기저를 이룬다는 것을 보이는 것이다. 열 번호 $y$에 해당하는 벡터를 $y=0$ 부터 $y=N-1$까지 다음과 같이 정의할 수 있다:  

$\|\phi_y\rangle = \frac{1}{\sqrt{N}} \sum_{x=0}^{N-1} \omega_N^{xy} \|x\rangle.$  

이 벡터들 사이의 내적을 취하면 다음 식을 얻는다:  
$\langle \phi_z \| \phi_y \rangle = \frac{1}{N} \sum_{x=0}^{N-1} \omega_N^{x(y-z)}.$  

이런 합은 기하급수의 처음 $N$항 합 공식을 이용해 계산할 수 있다.  

$1 + \alpha + \alpha^2 + \cdots + \alpha^{N-1}$ = $$\begin{cases} \dfrac{\alpha^N - 1}{\alpha - 1} & \text{if } \alpha \neq 1 \\ N & \text{if } \alpha = 1 \end{cases}$$

특히 $\alpha = \omega_N^{y-z}$로 놓을 수 있다. $ y=z $일 때 $ \alpha=1 $이므로 공식을 적용하고 $N$으로 나누면  
$\langle \phi_y | \phi_y \rangle = 1.$  

$y \neq z$일 때 $\alpha \neq 1$ 이므로 공식에 의해 다음을 얻는다:  \
$\langle \phi_z | \phi_y \rangle = \frac{1}{N} \frac{\omega_N^{N(y-z)} - 1}{\omega_N^{y-z} - 1} = \frac{1}{N} \frac{1 - 1}{\omega_N^{y-z} - 1} = 0.$  

이는 $ \omega_N^N = e^{2\pi i} = 1 $이므로 $ \omega_N^{N(y-z)} = 1 $이 되어 분자가 0이 되고, 분모는 $ \omega_N^{y-z} \neq 1 $이므로 0이 아닌 데서 비롯된다. 직관적으로, 단위원 상에 분포된 점들을 합하면 서로 상쇄되어 $ 0 $이 된다.  

따라서 $ \{ |\phi_0\rangle, \dots, |\phi_{N-1}\rangle \} $가 정규직교 집합임을 확인하였으며,  
$\langle \phi_z | \phi_y \rangle$ = $$\begin{cases} 1 & y=z \\ 0 & y \neq z \end{cases}$$ \
이는 $ QFT_N $이 단일성임을 드러낸다.  

### 2.3 제어된 위상 게이트  
양자 푸리에 변환을 양자 회로로 구현하려면 제어된 위상 게이트를 사용해야 한다. 위상 연산은 단일 큐비트 단위 연산으로 다음 형태를 가진다:  
$$P_\alpha = \begin{pmatrix} 1 & 0 \\ 0 & e^{i\alpha} \end{pmatrix}$$

임의 실수 $ \alpha $에 대해. 이 게이트의 제어 버전은 다음 행렬을 가진다:  
$$CP_\alpha = \begin{pmatrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & e^{i\alpha} \end{pmatrix}$$

이 제어 게이트의 경우, 두 가능성이 동등하기 때문에 어떤 큐비트가 제어 큐비트이고 어떤 큐비트가 대상 큐비트인지는 실제로 중요하지 않습니다. 양자 회로도에서 이 게이트를 나타내기 위해 다음 기호 중 하나를 사용할 수 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/controlled-phase-gates.svg" style="width: 60%;">

세 번째 형태에서는 편의에 따라 $ \alpha $ 레이블을 제어선 옆이나 아래 제어에 놓을 수도 있다.  

$N=2^m$이고 $m \geq2 $일 때 양자 푸리에 변환을 수행하려면, $m$ 큐비트에 대해 표준 기저 상태에 다음 작용을 하는 연산이 필요하다:  

$\|y\rangle\|a\rangle \mapsto \omega_{2^m}^{a y} \|y\rangle \|a\rangle,$ 

여기서 $ a $는 비트이고 $ y \in \\{0, \dots, 2^{m-1}-1\\} $는 $ m-1 $ 비트 이진 문자열로 인코딩된 수이다. 이는 다음 예를 일반화하여 제어된 위상 게이트로 수행할 수 있으며, 예시로 $ m=5 $를 들 수 있다.  

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/phase-injection.svg" style="width: 60%;">

일반적으로 임의 $ m \geq 2 $에 대해, 비트 $ a $에 해당하는 최상위 큐비트를 제어로 보고, $ y $의 최하위 비트에 해당하는 큐비트에 $ \alpha = \pi / 2^{m-1} $부터 $ y $의 최상위 비트에 해당하는 큐비트에 $ \alpha = \pi /2 $까지의 위상 게이트 $ P_\alpha $를 적용한다. 이 제어된 위상 게이트들은 서로 교환 가능하므로 순서는 임의이다.  


### 2.4 QFT의 회로 구현  
이제 차원 $ N=2^m $이 2의 거듭제곱일 때 양자 푸리에 변환을 회로로 구현하는 방법을 보자. 양자 푸리에 변환을 구현하는 방법은 여러 가지가 있지만, 이는 알려진 가장 간단한 방법 중 하나이다. 양자 푸리에 변환의 역변환은 각 게이트를 그 역(또는 켤레 전치)으로 대칭하고 순서를 반대로 하면 간단히 구현된다. 단위 게이트로만 구성된 모든 양자 회로는 이 방식으로 가역이다.  

구현은 재귀적이며, 따라서 가장 자연스럽게 그렇게 설명된다. 기본 사례는 $ m=1 $로, 이 경우 양자 푸리에 변환은 아다마르 연산이다.  

$ m \geq 2 $일 때 $ m $ 큐비트에 양자 푸리에 변환을 수행하려면, $\|x\rangle \|a\rangle $ 형태의 표준 기저 상태에 대해 다음 단계를 수행한다. 여기서 $ x \in \\{0, \dots, 2^{m-1}-1\\} $는 $ m-1 $ 비트 이진 표기로 인코딩된 정수이고 $ a $는 단일 비트이다.  

1. 먼저 아래/왼쪽 $ m-1 $ 큐비트에 $ 2^{m-1} $ 차원 양자 푸리에 변환을 적용하여 다음 상태를 얻는다:  
  $(QFT_{2^{m-1}} \|x\rangle) \|a\rangle = \frac{1}{\sqrt{2^{m-1}}} \sum\limits_{y=0}^{2^{m-1}-1} \omega_{2^{m-1}}^{x y} \|y\rangle \|a\rangle$

   이는 설명 중인 방법을 한 큐비트 적게 재귀적으로 적용하며, 기본 사례로 단일 큐비트 아다마르 연산을 사용한다.  

2. 최상위/오른쪽 큐비트를 제어로 사용하여 나머지 $ m-1 $ 큐비트의 각 표준 기저 상태 $ \|y\rangle $에 위상 $ \omega_{2^m}^{y} $를 주입(위에서 설명한 대로)하여 다음 상태를 얻는다:  
  $\frac{1}{\sqrt{2^{m-1}}} \sum\limits_{y=0}^{2^{m-1}-1} \omega_{2^{m-1}}^{x y} \omega_{2^m}^{a y} \|y\rangle \|a\rangle.$  

3. 최상위/오른쪽 큐비트에 아다마르 게이트를 적용하여 다음 상태를 얻는다:  
  $\frac{1}{\sqrt{2^m}} \sum\limits_{y=0}^{2^{m-1}-1} \sum\limits_{b=0}^{1} (-1)^{a b} \omega_{2^{m-1}}^{x y} \omega_{2^m}^{a y} \|y\rangle \|b\rangle.$  

4. 큐비트 순서를 순열하여 최하위 비트가 최상위 비트가 되고 나머지는 위/오른쪽으로 이동:  
  $\frac{1}{\sqrt{2^m}} \sum\limits_{y=0}^{2^{m-1}-1} \sum\limits_{b=0}^{1} (-1)^{a b} \omega_{2^{m-1}}^{x y} \omega_{2^m}^{a y} \|b\rangle \|y\rangle.$  

예를 들어, $ N=32=2^5 $에 대한 회로를 얻는다. 이 다이어그램에서 큐비트는 입력 $ \|x\rangle \|a\rangle $과 출력 $ \|b\rangle \|y\rangle $에 대응하는 표준 기저 벡터 이름으로 명확히 표시된다.  

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/QFT_32.svg" style="width:60%;">

### 2.5 분석  
방금 설명한 회로가 $ 2^m $ 차원 양자 푸리에 변환을 구현하는지 확인하는 핵심 공식은 다음과 같다:  
$(-1)^{a b} \omega_{2^{m-1}}^{x y} \omega_{2^m}^{a y} = \omega_{2^m}^{(2 x + a)(2^{m-1} b + y)}.$  

이 공식은 임의 정수 $ a, b, x, y $에 대해 성립하지만, 우리는 $ a,b \in \\{0,1\\} $과 $ x,y \in \\{0, \dots, 2^{m-1}-1\\} $에만 필요하다. 우변 지수의 곱을 전개하여 확인:  

$\omega_{2^m}^{(2 x + a)(2^{m-1} b + y)} = \omega_{2^m}^{2^m x b} \omega_{2^m}^{2 x y} \omega_{2^m}^{2^{m-1} a b} \omega_{2^m}^{a y} = (-1)^{a b} \omega_{2^{m-1}}^{x y} \omega_{2^m}^{a y},$  

여기서 두 번째 등식은  
$\omega_{2^m}^{2^m x b} = (\omega_{2^m}^{2^m})^{x b} = 1^{x b} = 1.$  
관찰을 이용한다.  

$ 2^m $ 차원 양자 푸리에 변환은 모든 $ u \in \\{0, \dots, 2^m - 1\\} $에 대해 다음과 같이 정의된다:  

$QFT_{2^m} \|u\rangle = \frac{1}{\sqrt{2^m}} \sum\limits_{v=0}^{2^m - 1} \omega_{2^m}^{u v} \|v\rangle.$  

$ u $와 $ v $를  
$u = 2 x + a $\
$v = 2^{m-1} b + y$  ($ a,b \in \\{0,1\\} $, $ x,y \in \\{0, \dots, 2^{m-1}-1\\} $)로 쓰면 


$QFT_{2^m} \|2 x + a\rangle = \frac{1}{\sqrt{2^m}} \sum_\limits{y=0}^{2^{m-1}-1} \sum\limits_{b=0}^{1} \omega_{2^m}^{(2 x + a)(2^{m-1} b + y)} \|b 2^{m-1} + y\rangle $ 
$= \frac{1}{\sqrt{2^m}} \sum\limits_{y=0}^{2^{m-1}-1} \sum\limits_{b=0}^{1} (-1)^{a b} \omega_{2^{m-1}}^{x y} \omega_{2^m}^{a y} \|b 2^{m-1} + y\rangle.$ 

마지막으로, 표준 기저 상태 $ \|x\rangle \|a\rangle $와 $ \|b\rangle \|y\rangle $를 범위 $ \\{0, \dots, 2^m - 1\\} $ 
내 정수의 이진 인코딩으로 생각하면,  
$\|x\rangle \|a\rangle = \|2 x + a\rangle$  
$\|b\rangle \|y\rangle = \|2^{m-1} b + y\rangle,$  

위 회로가 요구된 연산을 구현함을 알 수 있다. 양자 푸리에 변환을 수행하는 이 방법이 놀라운 이유는, 본질적으로 빠른 푸리에 변환을 양자 회로 형태로 구현한 것이기 때문이다.  

마지막으로, 설명한 회로에서 사용된 게이트 수를 세어 보자. 제어된 위상 게이트는 이전 수업에서 논의한 표준 게이트 집합에 없지만, 우선 이를 무시하고 각각을 단일 게이트로 센다.  

각 $ m $에 필요한 게이트 수를 $ s_m $이라 하자. $ m=1 $이면 양자 푸리에 변환은 아다마르 연산이므로  
$s_1 = 1.$  

$ m \geq 2 $이면 위 회로에서 $ m-1 $ 큐비트 양자 푸리에 변환에 $ s_{m-1} $ 게이트, 제어된 위상 게이트 $ m-1 $개, 아다마르 게이트 1개, 스왑 게이트 $ m-1 $개가 필요하므로  
$s_m = s_{m-1} + (2 m - 1).$  

합으로 닫힌 형태를 얻는다:  
$s_m = \sum\limits_{k=1}^m (2 k - 1) = m^2.$  

설명된 방법만큼 많은 스왑 게이트가 실제로 필요하지 않다. 게이트를 약간 재배치하면 모든 스왑 게이트를 오른쪽으로 밀어 스왑 게이트 수를 $ \lfloor m/2 \rfloor $로 줄일 수 있다. 점근적으로 큰 개선은 아니며, 여전히 $ QFT_{2^m} $을 수행하는 회로 크기는 $ O(m^2) $이다.  

표준 게이트 집합만으로 양자 푸리에 변환을 구현하려면 각 제어된 위상 게이트를 해당 집합의 게이트로 구성하거나 근사해야 한다. 필요한 수는 요구 정밀도에 따라 다르지만, $ m $의 함수로 총 비용은 여전히 이차이다.  

실제로 $ P_\alpha $가 $ \alpha $가 매우 작을 때 항등 연산에 매우 가까운 점을 이용하면, 대부분의 제어된 위상 게이트를 생략해도 정밀도 손실이 크지 않아 이차 미만 게이트 수로 양자 푸리에 변환을 상당히 근사할 수 있다.  

## 3. 일반 절차와 분석  
이제 위상 추정 절차를 일반적으로 살펴보자. 아이디어는 위에서 고려한 2큐비트 위상 추정 버전을 다음 다이어그램이 제안하는 자연스러운 방식으로 확장하는 것이다.  

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/phase-estimation-procedure.svg" style="width:60%;">

새로운 제어 큐비트가 위에 추가될 때마다 단위 연산 $ U $의 수행 횟수가 두 배가 됨을 주목하라. 이는 다이어그램에서 각 제어된 단위 연산의 $ U $ 지수로 표시된다.  


어떤 $ k $에 대한 제어된-$ U^k $ 연산을 구현하는 가장 직관적인 방법은 제어된-$ U $ 연산을 $ k $번 반복하는 것이다. 이 방법론을 사용하면 제어 큐비트 추가가 회로 크기에 크게 기여함을 인지해야 한다: 다이어그램처럼 $ m $ 제어 큐비트가 있으면 총 $ 2^m - 1 $개의 제어된-$ U $ 연산이 필요하다. 이는 $ m $이 증가함에 따라 상당한 계산 비용이 들지만, $ \theta $의 훨씬 더 정확한 근사를 제공한다.  

그러나 일부 $ U $ 선택에 대해서는 $ k $가 클 때 $ U^k $를 단순히 $ k $번 반복하는 것보다 더 효율적인 회로로 구현할 수 있음을 유의하라. 이는 수업 후반 정수 인수분해 맥락에서 구체적 예를 볼 것이며, 이전 수업에서 논의한 모듈러 지수 연산 효율적 알고리즘이 이를 구한다.  

이제 설명한 회로를 분석하자. 역 양자 푸리에 변환 직전 상태는 다음과 같다:  

$\frac{1}{\sqrt{2^m}} \sum\limits_{x=0}^{2^m - 1} (U^x \|\psi\rangle) \|x\rangle = \|\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum\limits_{x=0}^{2^m - 1} e^{2 \pi i x \theta} \|x\rangle.$  

### 3.1 특별한 경우  
$ m=2 $ 경우와 유사하게, 먼저 $ \theta = y / 2^m $인 ($ y \in \\{0, \dots, 2^m - 1\\} $) 특별한 경우를 고려한다. 이 경우 역 양자 푸리에 변환 직전 상태는 다음과 같이 쓸 수 있다:

$\|\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum\limits_{x=0}^{2^m - 1} e^{2 \pi i x y / 2^m} \|x\rangle = \|\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum\limits_{x=0}^{2^m - 1} \omega_{2^m}^{x y} \|x\rangle = \|\psi\rangle \otimes QFT_{2^m} \|y\rangle.$  

따라서 역 양자 푸리에 변환을 적용하면 상태는   
$\|\psi\rangle \|y\rangle$  가 되고, 
측정으로 $ y $ (이진 인코딩)를 드러낸다.  

### 3.2 확률 상한  
$ \theta $의 다른 값, 즉 정수 $ y $에 대해 $ y/2^m $ 형태가 아닌 경우, 측정 결과는 확정적이지 않지만 서로 다른 결과에 대한 확률 상한을 증명할 수 있다. 앞으로 $ 0 \leq \theta < 1 $을 만족하는 임의 $ \theta $를 고려한다.

역 양자 푸리에 변환 수행 후 회로 상태는 다음과 같다:  

$\|\psi\rangle \otimes \frac{1}{\sqrt{2^m}} \sum\limits_{y=0}^{2^m - 1} \sum\limits_{x=0}^{2^m - 1} e^{2 \pi i x (\theta - y/2^m)} \|y\rangle.$  

따라서 위 $ m $ 큐비트 측정에서 결과 $ y $가 나타날 확률은  

$p_y = \| \frac{1}{\sqrt{2^m}} \sum_\limits{x=0}^{2^m - 1} e^{2 \pi i x (\theta - y/2^m)} \|^2.$  

이 확률을 더 잘 이해하기 위해 이전에 본 기하급수 초기 부분 합 공식을 사용한다.  
$$1 + \alpha + \alpha^2 + \cdots + \alpha^{N-1} = \begin{cases} \dfrac{\alpha^N - 1}{\alpha - 1} & \text{if } \alpha \neq 1 \\ N & \text{if } \alpha = 1 \end{cases}$$

$ p_y $ 공식에 나타나는 합을 $ \alpha = e^{2 \pi i (\theta - y/2^m)} $로 놓아 간소화한다. 얻는 결과는 다음과 같다.  

$\sum\limits_{x=0}^{2^m - 1} e^{2 \pi i x (\theta - y/2^m)}$ = $$\begin{cases} 2^m & \theta = y/2^m \\ \dfrac{e^{2 \pi i (2^m \theta - y)} - 1}{e^{2 \pi i (\theta - y/2^m)} - 1} & \theta \neq y/2^m \end{cases}$$

따라서 $ \theta = y/2^m $일 때 $ p_y = 1 $ (이미 이 특별한 경우에서 알았음), $ \theta \neq y/2^m $일 때  
$p_y = \frac{1}{2^m} \left| \frac{e^{2 \pi i (2^m \theta - y)} - 1}{e^{2 \pi i (\theta - y/2^m)} - 1} \right|^2.$  

이 확률에 대해 더 알기 위해 단위원 상의 호 길이와 현 길이 관계를 생각한다. 다음 그림은 임의 실수 $ \delta \in [-1/2, 1/2] $에 필요한 관계를 보여준다.  

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/arc-and-chord.svg" style="width:40%;">

먼저, 현 길이(파랑)는 호 길이(보라)보다 클 수 없다:  
$\|e^{2 \pi i \delta} - 1\| \leq 2 \pi \|\delta\|.$  

반대 방향 관계에서, 호 길이 대 현 길이 비율은 $ \delta = \pm 1/2 $일 때 최대이며, 이 경우 원 둘레의 절반을 지름으로 나눈 값 $ \pi/2 $이다. 따라서  

$\frac{2 \pi \|\delta\|}{\|e^{2 \pi i \delta} - 1\|} \leq \frac{\pi}{2}$,

그래서  
$\|e^{2 \pi i \delta} - 1\| \geq 4 \|\delta\|.$  

이 관계에 기반한 분석은 다음 두 사실을 드러낸다.  

1. $ \theta $가 실수이고 $ y \in \\{0, \dots, 2^m - 1\\} $가  
  $\left| \theta - \frac{y}{2^m} \right| \leq 2^{-(m+1)}$  
  를 만족한다고 하자. 이는 $ y/2^m $이 $ \theta $의 최적 $ m $-비트 근사이거나, $ y/2^m $과 $ (y-1)/2^m $ 또는 $ (y+1)/2^m $ 중간에 정확히 있어 두 최적 근사 중 하나임을 의미한다.  

    이 경우 $ p_y $가 상당히 크다는 것을 증명한다. 가정에 의해 $ \|2^m \theta - y\| \leq 1/2 $이므로 위 호와 현 길이 두 번째 관찰을 사용해  
  $\|e^{2 \pi i (2^m \theta - y)} - 1\| \geq 4 \|2^m \theta - y\| = 4 \cdot 2^m \cdot \left| \theta - \frac{y}{2^m} \right|.$  

    또한 호와 현 길이 첫 번째 관찰을 사용해  
  $\|e^{2 \pi i (\theta - y/2^m)} - 1\| \leq 2 \pi \left| \theta - \frac{y}{2^m} \right|.$  

    이 두 부등식을 $ p_y $에 적용하면  
  $p_y \geq \frac{1}{2^{2m}} \frac{16 \cdot 2^{2m}}{4 \pi^2} = \frac{4}{\pi^2} \approx 0.405.$  

    이는 이전 $ m=2 $ 위상 추정에서 최적 결과가 40% 이상 확률로 나타난 관찰을 설명한다. 실제로는 40%가 아니라 $ 4/\pi^2 $이며, 모든 $ m $에 이 상한이 성립한다.  

2. 이제 $ y \in \\{0, \dots, 2^m - 1\\} $가  
   $2^{-m} \leq \left| \theta - \frac{y}{2^m} \right| \leq \frac{1}{2}$  
   를 만족한다고 하자. 이는 $ \theta $와 $ y/2^m $ 사이에 더 나은 근사 $ z/2^m $가 있음을 의미한다.  

    이번에는 $ p_y $가 너무 크지 않음을 증명한다. 간단한 관찰부터:  
   $|e^{2 \pi i (2^m \theta - y)} - 1| \leq 2,$  

    이는 단위원 상 임의 두 점의 절댓값 차가 최대 2이기 때문이다.  

    또한 위 호와 현 길이 두 번째 관찰을 이번에는 $ p_y $ 분모에 적용해  
   $|e^{2 \pi i (\theta - y/2^m)} - 1| \geq 4 \left| \theta - \frac{y}{2^m} \right| \geq 4 \cdot 2^{-m}.$  

    두 부등식을 합치면  
   $p_y \leq \frac{1}{2^{2m}} \frac{4}{16 \cdot 2^{-2m}} = \frac{1}{4}.$  
   이 상한은 우리의 목적에 충분하지만 상당히 조잡하며, 확률은 보통 $ 1/4 $보다 훨씬 낮다.  

이 분석의 중요한 교훈은 $ \theta $에 매우 가까운 근사가 나타날 가능성이 높다는 것 — 최적 $ m $-비트 근사가 40% 이상 확률로 얻어짐 — 반면 $ 2^{-m} $ 이상 차이나는 근사는 나타날 가능성이 낮아 확률이 25%로 상한된다.  

이 보장을 바탕으로 위상 추정 절차를 여러 번 반복해 $ \theta $에 대한 통계적 증거를 수집함으로써 신뢰도를 높일 수 있다. 아래 큐비트 집합의 상태 $ \|\psi\rangle $는 위상 추정 절차에 의해 변하지 않으므로 원하는 만큼 절차를 반복 실행할 수 있다. 특히, 회로를 실행할 때마다 최적 $ m $-비트 근사를 40% 이상 확률로 얻고, $ 2^{-m} $ 이상 차이나는 확률은 25%로 상한된다. 회로를 여러 번 실행해 가장 자주 나타나는 결과를 취하면, 가장 자주 나타나는 결과가 25% 이하로 나타날 가능성은 극히 낮다. 결과적으로 $ y/2^m $이 $ \theta $로부터 $ 1/2^m $ 이내인 근사를 매우 높은 확률로 얻는다. 실제로 $ 1/2^m $ 이상 차이나는 드문 경우는 절차 실행 횟수에 지수적으로 감소한다.  


다음은 $ m=3 $과 $ m=4 $일 때 $ \theta $의 함수로 연속 세 $ y $ 값에 대한 확률을 보여주는 두 플롯이다. (명확성을 위해 세 결과만 표시. 다른 결과의 확률은 동일 기본 함수를 주기적으로 이동시켜 얻는다.)

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/three-qubit-probabilities.svg" style="width:60%;">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/four-qubit-probabilities.svg" style="width:60%;">
