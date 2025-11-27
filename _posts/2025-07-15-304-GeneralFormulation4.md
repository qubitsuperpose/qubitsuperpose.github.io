---
title: 7차시 4:General formulation of quantum information3(Purifications and fidelity)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 정화 (Purifications)
## 1. 정화의 정의
정화에 대한 정밀한 수학적 정의부터 시작합시다.

>정의
>
$X$가 밀도 행렬 $\rho$로 표현되는 상태에 있는 시스템이라고 가정하고, $\vert\psi\rangle$가 쌍 $(X, Y)$의 양자 상태 벡터이며, $Y$를 부분 대각합(traced out)할 때 $\rho$를 남기는 경우:
> <p>$\rho = \operatorname{Tr}_{\mathsf{Y}} \bigl( \vert \psi\rangle\langle\psi\vert\bigr).$</p>
그러면 상태 벡터 $\vert\psi\rangle$는 $\rho$의 **정화**라고 불립니다.

양자 상태 벡터가 아닌 밀도 행렬로 표현되는 순수 상태 $\vert\psi\rangle\langle\psi\vert$ 또한 정의에 있는 방정식이 참일 때 일반적으로 $\rho$의 정화로 불리지만, 우리는 일반적으로 이 용어를 양자 상태 벡터를 지칭하는 데 사용할 것입니다.

*정화*라는 용어는 시스템의 순서가 역전되거나, 시스템 및 상태의 이름이 다를 때(당연히), 그리고 두 개 이상의 시스템이 있을 때도 더 일반적으로 사용됩니다. 예를 들어, $\vert \psi \rangle$가 복합 시스템 $(A, B, C)$의 순수 상태를 나타내는 양자 상태 벡터이고, 방정식

<p>$$\rho = \operatorname{Tr}_{\mathsf{B}} \bigl(\vert\psi\rangle\langle\psi\vert\bigr)$$</p>

가 시스템 $(A, C)$의 상태를 나타내는 밀도 행렬 $\rho$에 대해 참일 경우, $\vert\psi\rangle$는 여전히 $\rho$의 정화라고 불립니다.

그러나 이 강의의 목적을 위해 우리는 정의에 설명된 특정 형식에 초점을 맞출 것입니다. 이 정의에 따른 정화에 관한 속성 및 사실들은 일반적으로 시스템을 재배열하고, 시스템 중 하나가 $X$의 역할을 하고 다른 하나가 $Y$의 역할을 하는 두 개의 복합 시스템으로 분할함으로써 두 개 이상의 시스템으로 일반화될 수 있습니다.

## 2. 정화의 존재
$X$와 $Y$가 임의의 두 시스템이고 $\rho$가 $X$의 주어진 상태라고 가정합시다. 시스템 $Y$가 충분히 크다면, $\rho$를 **정화하는** — 즉, $\vert\psi\rangle$가 $\rho$의 정화라는 또 다른 표현인 — $(X, Y)$의 양자 상태 벡터 $\vert\psi\rangle$가 존재함을 증명할 것입니다. 특히, 만약 $Y$가 $X$만큼 많은 고전적 상태를 가지고 있다면, 이러한 형태의 정화는 모든 상태 $\rho$에 대해 필연적으로 존재합니다. 일부 상태 $\rho$에 대해서는 $Y$의 더 적은 고전적 상태가 요구됩니다. 일반적으로, $\rho$를 정화하는 $(X, Y)$의 양자 상태 벡터의 존재를 위해 $Y$의 $\operatorname{rank}(\rho)$개의 고전적 상태가 필요하고 충분합니다.

먼저, 임의의 양의 정수 $n$에 대해, $\rho$를 $n$개의 순수 상태의 볼록 조합으로 표현한 것을 고려합니다.

<p>$$\rho = \sum_{a = 0}^{n-1} p_a \vert\phi_a\rangle\langle\phi_a\vert$$</p>

이 표현에서, $(p_0,\ldots,p_{n-1})$는 확률 벡터이고 $\vert\phi_0\rangle,\ldots,\vert\phi_{n-1}\rangle$는 $X$의 양자 상태 벡터입니다.

이러한 표현을 얻는 한 가지 방법은 스펙트럼 정리를 이용하는 것인데, 이 경우 $n$은 $X$의 고전적 상태의 수이며, $p_0,\ldots,p_{n-1}$는 $\rho$의 고유값이고, $\vert\phi_0\rangle,\ldots,\vert\phi_{n-1}\rangle$는 이 고유값에 해당하는 정규 직교 고유 벡터입니다.

실제로 합산에 $\rho$의 0 고유값에 해당하는 항을 포함할 필요는 없으며, 이를 통해 $n = \operatorname{rank}(\rho)$를 선택하고 $p_0,\ldots,p_{n-1}$를 $\rho$의 0이 아닌 고유값으로 대체하여 선택할 수 있습니다. 이것은 위와 같은 형태를 취하는 $\rho$의 표현이 존재하는 $n$의 최소값입니다.

명확히 하자면, 순수 상태의 볼록 조합으로서 선택된 $\rho$의 표현이 스펙트럼 정리에서 비롯될 **필요는 없습니다** — 이것은 그러한 표현을 얻는 하나의 방법일 뿐입니다. 특히, $n$은 임의의 양의 정수일 수 있으며, 단위 벡터 $\vert\phi_0\rangle,\ldots,\vert\phi_{n-1}\rangle$는 직교할 필요가 없으며, 확률 $p_0,\ldots,p_{n-1}$는 $\rho$의 고유값일 필요가 없습니다.

이제 $\rho$의 정화를 다음과 같이 식별할 수 있습니다.

<p>$$\vert\psi\rangle = \sum_{a = 0}^{n-1} \sqrt{p_a} \, \vert\phi_a\rangle \otimes \vert a \rangle$$</p>

여기서 우리는 $Y$의 고전적 상태가 $0,\ldots,n-1$를 포함한다고 가정합니다. 만약 그렇지 않다면, $Y$의 $n$개의 서로 다른 고전적 상태에 대한 임의의 선택을 $0,\ldots,n-1$에 대체할 수 있습니다. 이것이 실제로 $\rho$의 정화임을 확인하는 것은 부분 대각합을 계산하는 간단한 문제이며, 이는 다음의 두 가지 동등한 방식으로 수행될 수 있습니다.

<p>$$\begin{gather}\operatorname{Tr}_{\mathsf{Y}} \bigl(\vert\psi\rangle\langle\psi\vert\bigr) = \sum_{a = 0}^{n-1} (\mathbb{I}_{\mathsf{X}} \otimes \langle a\vert) \vert\psi\rangle\langle\psi\vert (\mathbb{I}_{\mathsf{X}} \otimes \vert a\rangle) = \sum_{a = 0}^{n-1} p_a \vert\phi_a\rangle\langle\phi_a\vert = \rho \\
\operatorname{Tr}_{\mathsf{Y}} \bigl(\vert\psi\rangle\langle\psi\vert\bigr) = \sum_{a,b = 0}^{n-1} \sqrt{p_a} \sqrt{p_b} \, \vert\phi_a\rangle\langle \phi_b\vert \, \operatorname{Tr}(\vert a \rangle \langle b \vert) = \sum_{a = 0}^{n-1} p_a \, \vert\phi_a\rangle\langle \phi_a\vert = \rho\end{gather}$$</p>

더 일반적으로, 임의의 정규 직교 벡터 집합 $\{\vert\gamma_0\rangle,\ldots,\vert\gamma_{n-1}\rangle\}$에 대해, 양자 상태 벡터
<p>$$\vert\psi\rangle = \sum_{a = 0}^{n-1} \sqrt{p_a} \, \vert\phi_a\rangle \otimes \vert \gamma_a \rangle$$</p>

는 $\rho$의 정화입니다.

### 2.1 예시
$X$와 $Y$가 모두 큐비트이고
<p>$$\rho = \begin{pmatrix} \frac{3}{4} & \frac{1}{4}\\[2mm] \frac{1}{4} & \frac{1}{4} \end{pmatrix}$$</p>

가 $X$의 상태를 나타내는 밀도 행렬이라고 가정합니다.
우리는 스펙트럼 정리를 사용하여 $\rho$를 다음과 같이 표현할 수 있습니다.
<p>$$\rho = \cos^2(\pi/8) \vert \psi_{\pi/8}\rangle\langle\psi_{\pi/8}\vert + \sin^2(\pi/8) \vert \psi_{5\pi/8}\rangle\langle\psi_{5\pi/8}\vert,$$</p>

여기서 $\vert \psi_{\theta} \rangle = \cos(\theta) \vert 0\rangle + \sin(\theta)\vert 1\rangle$입니다. 쌍 $(X, Y)$의 순수 상태를 설명하는 양자 상태 벡터
<p>$$\cos(\pi/8) \vert \psi_{\pi/8}\rangle \otimes \vert 0\rangle + \sin(\pi/8) \vert \psi_{5\pi/8}\rangle \otimes \vert 1\rangle$$</p>
는 따라서 $\rho$의 정화입니다.

대안적으로, 우리는 다음과 같이 쓸 수 있습니다.
<p>$$\rho = \frac{1}{2} \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert +\rangle\langle +\vert.$$</p>

이것은 순수 상태의 볼록 조합이지만, $\vert 0\rangle$와 $\vert +\rangle$가 직교하지 않고 $1/2$이 $\rho$의 고유값이 아니기 때문에 스펙트럼 분해는 아닙니다. 그럼에도 불구하고, 양자 상태 벡터

<p>$$\frac{1}{\sqrt{2}} \vert 0 \rangle \otimes \vert 0\rangle + \frac{1}{\sqrt{2}} \vert + \rangle \otimes \vert 1\rangle$$</p>

는 $\rho$의 정화입니다.

## 3. 슈미트 분해 (Schmidt decompositions)
다음으로, 우리는 특정 형태를 취하는 시스템의 **쌍**의 양자 상태 벡터에 대한 표현인 **슈미트 분해**에 대해 논의할 것입니다. 슈미트 분해는 정화와 밀접하게 연결되어 있으며, 그 자체로 매우 유용합니다. 실제로, 주어진 시스템 쌍의 양자 상태 벡터 $\vert\psi\rangle$에 대해 추론할 때, 첫 번째 단계는 종종 이 상태의 슈미트 분해를 식별하거나 고려하는 것입니다.

>정의
>
$\vert \psi\rangle$를 시스템 쌍 $(X, Y)$의 주어진 양자 상태 벡터라고 합시다. $\vert\psi\rangle$의 **슈미트 분해**는 다음과 같은 형태의 표현입니다.
><p>$$\vert \psi\rangle = \sum_{a = 0}^{r-1} \sqrt{p_a}\, \vert x_a\rangle \otimes \vert y_a \rangle,$$</p>
> 여기서 $p_0,\ldots,p_{r-1}$는 합이 $1$이 되는 양의 실수이고, 집합 $\{\vert x_0\rangle,\ldots,\vert x_{r-1}\rangle\}$과 $\{\vert y_0\rangle,\ldots,\vert y_{r-1}\rangle\}$ **모두** 정규 직교합니다.

$\vert\psi\rangle$의 슈미트 분해에서 값 
<p>$$\sqrt{p_0},\ldots,\sqrt{p_{r-1}}$$</p> 은 그 **슈미트 계수**로 알려져 있으며, 이들은 (순서에 관계없이) 고유하게 결정됩니다 — 이들은 $\vert\psi\rangle$의 그러한 표현에 나타날 수 있는 유일한 양의 실수입니다. 집합
<p>$$\{\vert x_0\rangle,\ldots,\vert x_{r-1}\rangle\} \quad\text{and}\quad \{\vert y_0\rangle,\ldots,\vert y_{r-1}\rangle\},$$</p>

반면에, 고유하게 결정되지는 않으며, 이러한 벡터 집합을 선택하는 데 있어서의 자유도는 이어지는 설명에서 명확해질 것입니다.

이제 주어진 양자 상태 벡터 $\vert\psi\rangle$가 실제로 슈미트 분해를 가지고 있음을 확인하고, 그 과정에서 그것을 찾는 방법을 배울 것입니다.

먼저 시스템 $X$에 해당하는 벡터 공간의 임의의 (반드시 직교할 필요는 없는) 기저 $\\{\vert x_0\rangle, \ldots, \vert x_{n-1}\rangle\\}$를 고려합시다. 이것이 기저이므로, 다음 방정식이 참이 되도록 고유하게 결정되는 벡터 $\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle$의 선택이 항상 존재할 것입니다.

<p> $$\vert \psi\rangle = \sum_{a = 0}^{n-1} \vert x_a\rangle \otimes \vert z_a \rangle \tag{1} $$</p>

예를 들어, $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$가 $X$와 관련된 표준 기저라고 가정합시다. $X$의 고전적 상태 집합이 $\\{0,\ldots,n-1\\}$이라고 가정하면, 이는 각 $a\in\\{0,\ldots,n-1\\}$에 대해 $\vert x_a\rangle = \vert a\rangle$임을 의미하며, 우리는 다음을 발견합니다.

<p>$$\vert\psi\rangle = \sum_{a = 0}^{n-1} \vert a\rangle \otimes \vert z_a\rangle$$</p>

각 $a\in\\{0,\ldots,n-1\\}$에 대해
<p>$$\vert z_a \rangle = ( \langle a \vert \otimes \mathbb{I}_{\mathsf{Y}}) \vert \psi\rangle$$</p> 
일 때. 우리는 $X$의 표준 기저 측정을 고려할 때 이와 같은 표현을 자주 고려합니다.

이 예시에서 벡터 $\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle$에 대한 공식 
<p>$$\vert z_a \rangle = ( \langle a \vert \otimes \mathbb{I}_{\mathsf{Y}}) \vert \psi\rangle$$</p>
는 $\\{\vert 0\rangle,\ldots,\vert n-1\rangle\\}$이 **정규 직교** 기저이기 때문에 작동한다는 점에 유의하는 것이 중요합니다. 

일반적으로, $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$가 반드시 정규 직교하지는 않은 기저인 경우, 벡터 $\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle$는 여전히 방정식 (1)에 의해 고유하게 결정되지만, 다른 공식이 필요합니다. 그것들을 찾는 한 가지 방법은 먼저 다음 방정식을 만족하는 벡터 $\vert w_0\rangle,\ldots,\vert w_{n-1}\rangle$를 식별하는 것입니다.

<p>$$\langle w_a \vert x_b \rangle = \begin{cases} 1 & a=b\\ 0 & a\neq b \end{cases}$$</p>

가 모든 $a,b\in\\{0,\ldots,n-1\\}$에 대해 만족될 때, 우리는 다음을 가집니다.
<p>$$\vert z_a \rangle = (\langle w_a \vert \otimes \mathbb{I}_{\mathsf{Y}}) \vert \psi\rangle.$$</p>

$X$에 해당하는 벡터 공간의 주어진 기저 $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$에 대해, 방정식 (1)이 만족되는 고유하게 결정된 벡터 $\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle$는 $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$이 정규 직교 기저인 경우에도 반드시 특별한 속성을 만족하지는 않습니다. 그러나 만약 우리가 $\\{\vert x_0\rangle, \ldots, \vert x_{n-1}\rangle\\}$를 축소된 상태
<p>$$\rho = \operatorname{Tr}_{\mathsf{Y}} \bigl( \vert \psi\rangle \langle \psi \vert \bigr),$$</p>

의 **고유 벡터**의 정규 직교 기저로 선택한다면, 흥미로운 일이 발생합니다. 구체적으로, 방정식 (1)이 참인 고유하게 결정된 집합 $\\{\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle\\}$에 대해, 우리는 이 집합이 **직교**해야 한다는 것을 발견합니다.
더 자세히, $\rho$의 스펙트럼 분해를 고려해 봅시다.
<p>$$\rho = \sum_{a = 0}^{n-1} p_a \vert x_a \rangle \langle x_a \vert$$</p>

여기서 우리는 $\rho$가 밀도 행렬이라는 사실을 인식하여 $\rho$의 고유값을 $p_0,\ldots,p_{n-1}$로 나타내고 있습니다 — 따라서 고유값의 벡터 $(p_0,\ldots,p_{n-1})$는 확률 벡터를 형성합니다 — 한편, $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$는 이 고유값에 해당하는 고유 벡터의 정규 직교 기저입니다. 방정식 (1)이 참인 고유한 집합 $\\{\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle\\}$이 필연적으로 직교한다는 것을 확인하기 위해, 우리는 부분 대각합을 계산하는 것부터 시작할 수 있습니다.
<p>$$\operatorname{Tr}_{\mathsf{Y}} (\vert\psi\rangle\langle\psi\vert) = \sum_{a,b = 0}^{n-1} \vert x_a\rangle\langle x_b\vert \operatorname{Tr}(\vert z_a\rangle\langle z_b\vert) = \sum_{a,b = 0}^{n-1} \langle z_b\vert z_a\rangle \, \vert x_a\rangle\langle x_b\vert.$$</p>

이 표현은 $\rho$의 스펙트럼 분해와 일치해야 합니다. $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$가 기저이므로, 우리는 행렬 집합
<p>$$\bigl\{ \vert x_a\rangle\langle x_b\vert \,:\, a,b\in\{0,\ldots,n-1\} \bigr\}$$</p>

이 선형적으로 독립적이라고 결론지을 수 있으며, 따라서 다음이 도출됩니다.
<p>$$\langle z_b \vert z_a\rangle = \begin{cases} p_a & a=b\\[1mm] 0 & a\neq b, \end{cases}$$</p>

이는 $\\{\vert z_0\rangle,\ldots,\vert z_{n-1}\rangle\\}$가 직교함을 입증합니다.

우리는 $\vert\psi\rangle$의 슈미트 분해를 거의 얻었습니다. 이제 남은 것은 (1)에서 $p_a = 0$인 항들을 버리고, 나머지 각 항에 대해 단위 벡터 $\vert y_a\rangle$를 사용하여 $\vert z_a\rangle = \sqrt{p_a}\vert y_a\rangle$로 쓰는 것입니다.

이를 수행하는 편리한 방법은 축소된 상태 $\rho$의 스펙트럼 분해에서 고유값/고유 벡터 쌍의 번호를 원하는 대로 매길 자유가 있다는 관찰에서 시작됩니다 — 따라서 우리는 고유값이 내림차순으로 정렬되어 있다고 가정할 수 있습니다:
<p>$$p_0 \geq p_1 \geq \cdots \geq p_{n-1}$$</p>

$r = \operatorname{rank}(\rho)$라고 하면, 우리는 $p_0,\ldots,p_{r-1} > 0$이고 $p_r = \cdots = p_{n-1} = 0$임을 발견합니다. 따라서 우리는 다음을 가집니다.
<p>$$\rho = \sum_{a = 0}^{r-1} p_a \vert x_a \rangle \langle x_a \vert$$</p>

그리고 우리는 양자 상태 벡터 $\vert \psi \rangle$를 다음과 같이 쓸 수 있습니다.
<p>$$\vert\psi\rangle = \sum_{a = 0}^{r-1} \vert x_a\rangle \otimes \vert z_a\rangle.$$</p>

$a=0,\ldots,r-1$에 대해 
<p>$$\| \vert z_a \rangle \|^2 = \langle z_a \vert z_a \rangle = p_a > 0$$</p> 임을 감안할 때, 우리는 단위 벡터 $\vert y_0 \rangle,\ldots,\vert y_{r-1}\rangle$를 다음과 같이 정의할 수 있습니다.

<p>$$\vert y_a\rangle = \frac{\vert z_a\rangle}{\|\vert z_a\rangle\|} = \frac{\vert z_a\rangle}{\sqrt{p_a}},$$</p>

따라서 각 $a\in\\{0,\ldots,r-1\\}$에 대해 $\vert z_a\rangle = \sqrt{p_a}\vert y_a\rangle$입니다. 벡터 $\\{\vert z_0\rangle, \ldots, \vert z_{r-1}\rangle\\}$가 직교하고 0이 아니므로, $\\{\vert y_0\rangle, \ldots, \vert y_{r-1}\rangle\\}$은 **정규 직교** 집합이며, 따라서 우리는 $\vert\psi\rangle$의 슈미트 분해를 얻었습니다.
<p>$$\vert \psi\rangle = \sum_{a = 0}^{r-1} \sqrt{p_a}\, \vert x_a\rangle \otimes \vert y_a \rangle$$</p>

벡터 $\\{\vert x_0\rangle,\ldots,\vert x_{r-1}\rangle\\}$와 $\\{\vert y_0\rangle,\ldots,\vert y_{r-1}\rangle\\}$의 선택에 관하여, 우리는 $\\{\vert x_0\rangle,\ldots,\vert x_{r-1}\rangle\\}$를 축소된 상태 $$\operatorname{Tr}_{\mathsf{Y}}(\vert\psi\rangle\langle\psi\vert)$$의 0이 아닌 고유값에 해당하는 임의의 정규 직교 고유 벡터 집합으로 선택할 수 있으며(위에서 수행한 것처럼), 이 경우 벡터 $$\{\vert y_0\rangle,\ldots,\vert y_{r-1}\rangle\}$$는 고유하게 결정됩니다.

상황은 두 시스템 사이에서 대칭적이므로, 우리는 대안적으로 $\\{\vert y_0\rangle,\ldots,\vert y_{r-1}\rangle\\}$를 축소된 상태 $$\operatorname{Tr}_{\mathsf{X}}(\vert\psi\rangle\langle\psi\vert)$$의 0이 아닌 고유값에 해당하는 임의의 정규 직교 고유 벡터 집합으로 선택할 수 있으며, 이 경우 벡터 $\\{\vert x_0\rangle,\ldots,\vert x_{r-1}\rangle\\}$는 고유하게 결정될 것입니다.

그러나 주목할 점은, 방금 설명한 것처럼 해당 축소된 상태의 고유 벡터 집합으로 두 집합 중 하나가 선택되면, 다른 하나는 결정된다는 것입니다 — 따라서 이들은 독립적으로 선택될 수 없습니다.

이 시리즈에서 다시 언급되지는 않겠지만, 시스템 쌍 $(X, Y)$의 임의의 순수 상태 $\vert\psi\rangle$에 대해 축소된 상태 $$\operatorname{Tr}_{\mathsf{X}}(\vert\psi\rangle\langle\psi\vert)$$의 0이 아닌 고유값 $p_0,\ldots,p_{r-1}$은 항상 축소된 상태 $$\operatorname{Tr}_{\mathsf{Y}}(\vert\psi\rangle\langle\psi\vert)$$의 0이 아닌 고유값과 일치해야 한다는 점은 주목할 만합니다.

직관적으로 말하면, 쌍 $(X, Y)$가 순수 상태에 있을 때 $X$와 $Y$의 축소된 상태는 정확히 동일한 양의 무작위성(randomness)을 가집니다. 이 사실은 슈미트 분해에 의해 드러납니다: 두 경우 모두 축소된 상태의 고유값은 순수 상태의 슈미트 계수의 제곱과 일치해야 합니다.

## 4. 정화의 유니타리 동등성 (Unitary equivalence of purifications)
우리는 슈미트 분해를 사용하여 *정화의 유니타리 동등성*으로 알려진 정화에 관한 근본적으로 중요한 사실을 확립할 수 있습니다.

>정리
>
정화의 유니타리 동등성: $X$와 $Y$가 시스템이고, $\vert\psi\rangle$와 $\vert\phi\rangle$가 $(X, Y)$의 양자 상태 벡터이며, 둘 다 $X$의 동일한 상태를 정화한다고 가정합시다. 기호로 나타내면,
> <p>$$\operatorname{Tr}_{\mathsf{Y}} (\vert\psi\rangle\langle\psi\vert) = \rho = \operatorname{Tr}_{\mathsf{Y}} (\vert\phi\rangle\langle\phi\vert)$$</p>
>
> $X$의 상태를 나타내는 어떤 밀도 행렬 $\rho$에 대해서입니다. 그러면 첫 번째 정화를 두 번째 정화로 변환하는 $Y$ 단독에 대한 유니타리 연산 $U$가 반드시 존재해야 합니다.
> <p>$$(\mathbb{I}_{\mathsf{X}} \otimes U) \vert\psi\rangle = \vert\phi\rangle.$$</p>

우리는 강의를 계속하면서 이 정리의 몇 가지 함의에 대해 논의하겠지만, 먼저 이것이 슈미트 분해에 대한 우리의 이전 논의로부터 어떻게 도출되는지 살펴보겠습니다.

우리의 가정은 $\vert\psi\rangle$와 $\vert\phi\rangle$가 $X$의 상태를 나타내는 어떤 밀도 행렬 $\rho$에 대해 방정식 
<p>$$\operatorname{Tr}_{\mathsf{Y}} (\vert\psi\rangle\langle\psi\vert) = \rho = \operatorname{Tr}_{\mathsf{Y}} (\vert\phi\rangle\langle\phi\vert)$$</p>
를 만족하는 시스템 쌍 $(X, Y)$의 양자 상태 벡터라는 것입니다.

$\rho$의 스펙트럼 분해를 고려해 봅시다.
<p>$$\rho = \sum_{a = 0}^{n-1} p_a \vert x_a\rangle\langle x_a\vert$$</p>

여기서 $\\{\vert x_0\rangle,\ldots,\vert x_{n-1}\rangle\\}$는 $\rho$의 고유 벡터의 정규 직교 기저입니다. 이전에 설명된 절차를 따르면 우리는 $\vert\psi\rangle$와 $\vert\phi\rangle$ 모두에 대해 다음 형태를 갖는 슈미트 분해를 얻을 수 있습니다.

<p>$$\begin{gather} \vert\psi\rangle = \sum_{a = 0}^{r-1} \sqrt{p_a} \, \vert x_a\rangle \otimes \vert u_a\rangle \\
\vert\phi\rangle = \sum_{a = 0}^{r-1} \sqrt{p_a} \, \vert x_a\rangle \otimes \vert v_a\rangle \end{gather}$$</p>

이러한 표현에서 $r$은 $\rho$의 계수(rank)이며, $\\{\vert u_0\rangle,\ldots,\vert u_{r-1}\rangle\\}$와 $\\{\vert v_0\rangle,\ldots,\vert v_{r-1}\rangle\\}$는 $Y$에 해당하는 공간의 정규 직교 벡터 집합입니다.

동일한 수의 요소를 가진 동일한 공간 내의 임의의 두 정규 직교 집합에 대해, 첫 번째 집합을 두 번째 집합으로 변환하는 유니타리 행렬이 항상 존재하므로, 우리는 $a = 0,\ldots,r-1$에 대해 $U \vert u_a\rangle = \vert v_a\rangle$가 되도록 유니타리 행렬 $U$를 선택할 수 있습니다. 특히, 그러한 행렬 $U$를 찾기 위해 우리는 먼저 그람-슈미트 정규 직교화 과정을 사용하여 우리의 정규 직교 집합을 정규 직교 기저 $\\{\vert u_0\rangle,\ldots,\vert u_{m-1}\rangle\\}$와 $\\{\vert v_0\rangle,\ldots,\vert v_{m-1}\rangle\\}$로 확장할 수 있으며, 여기서 $m$은 $Y$에 해당하는 공간의 차원이고, 그런 다음 다음을 취합니다.

<p>$$U = \sum_{a = 0}^{m-1} \vert v_a\rangle\langle u_a\vert.$$</p>

이제 우리는 다음을 발견합니다.
<p>$$(\mathbb{I}_{\mathsf{X}} \otimes U) \vert\psi\rangle = \sum_{a = 0}^{r-1} \sqrt{p_a} \, \vert x_a\rangle \otimes U \vert u_a\rangle = \sum_{a = 0}^{r-1} \sqrt{p_a} \, \vert x_a\rangle \otimes \vert v_a\rangle = \vert\phi\rangle,$$</p>

이것으로 증명이 완료됩니다.

다음은 정화의 유니타리 동등성과 관련된 흥미로운 예시 및 함의 중 일부입니다. 우리는 나중에 이 강의에서 충실도(fidelity)의 맥락에서 **울만의 정리(Uhlmann's theorem)**로 알려진 또 다른 매우 중요한 것을 보게 될 것입니다.

### 4.1 초밀도 코딩 (Superdense coding)
초밀도 코딩 프로토콜에서, 앨리스와 밥은 e-비트(e-bit)를 공유합니다. 이는 앨리스가 큐비트 $A$를, 밥이 큐비트 $B$를 가지고 있으며, 쌍 $(A, B)$가 함께 $\vert\phi^{+}\rangle$ 벨 상태에 있다는 것을 의미합니다. 이 프로토콜은 앨리스가 자신의 큐비트 $A$에 유니타리 연산을 적용함으로써 이 공유된 상태를 네 가지 벨 상태, 즉 $\vert\phi^+\rangle$, $\vert\phi^-\rangle$, $\vert\psi^+\rangle$, $\vert\psi^-\rangle$ 중 하나로 변환할 수 있는 방법을 설명합니다. 그녀가 이것을 수행한 후, 그녀는 $A$를 밥에게 보내고, 밥은 쌍 $(A, B)$에 대해 측정을 수행하여 자신이 어떤 벨 상태를 가지고 있는지 확인합니다.

네 가지 벨 상태 모두에 대해, 밥의 큐비트 $B$의 축소된 상태는 완전 혼합 상태(completely mixed state)입니다.
<p>$$\operatorname{Tr}_{\mathsf{A}}(\vert\phi^+\rangle\langle\phi^+\vert) = \operatorname{Tr}_{\mathsf{A}}(\vert\phi^-\rangle\langle\phi^-\vert) = \operatorname{Tr}_{\mathsf{A}}(\vert\psi^+\rangle\langle\psi^+\vert) = \operatorname{Tr}_{\mathsf{A}}(\vert\psi^-\rangle\langle\psi^-\vert) = \frac{\mathbb{I}}{2}$$</p>

정화의 유니타리 동등성에 의해, 우리는 각 벨 상태에 대해 앨리스의 큐비트 $A$ 단독에 대한 유니타리 연산이 존재하여 $\vert\phi^+\rangle$를 선택된 벨 상태로 변환한다는 것을 즉시 결론지을 수 있습니다. 이것이 프로토콜의 정확한 세부 사항을 드러내지는 않지만, 정화의 유니타리 동등성은 초밀도 코딩이 가능하다는 것을 즉시 시사합니다.

우리는 또한 벨 상태를 완전 혼합 상태의 정화에 대한 임의의 정규 직교 기저로 대체한다면, 더 큰 시스템으로의 초밀도 코딩의 일반화가 항상 가능하다는 결론을 내릴 수 있습니다.

### 4.2 암호학적 함의
정화의 유니타리 동등성은 양자 정보를 사용하여 암호학적 기본 요소(cryptographic primitives)를 구현하는 것과 관련하여 함의를 가집니다. 예를 들어, 정화의 유니타리 동등성은 양자 정보를 사용하여 **비트 커밋먼트(bit commitment)**의 이상적인 형태를 구현하는 것이 불가능하다는 것을 보여줍니다.

비트 커밋먼트 기본 요소는 두 참가자, 앨리스와 밥(서로를 신뢰하지 않음)을 포함하며 두 단계가 있습니다.
* 첫 번째 단계는 **커밋(commit)** 단계로, 앨리스는 이 단계를 통해 이진 값 $b\in\\{0, 1\\}$에 커밋합니다. 이 커밋먼트는 **구속력**이 있어야 합니다. 이는 앨리스가 마음을 바꿀 수 없음을 의미하며, **은폐성**도 있어야 합니다. 이는 밥이 앨리스가 어떤 값에 커밋했는지 알 수 없음을 의미합니다.
* 두 번째 단계는 **공개(reveal)** 단계로, 앨리스가 커밋한 비트가 밥에게 알려지며, 밥은 그때 공개된 값이 실제로 커밋되었던 값이라고 확신해야 합니다.

직관적이고 작동적인 관점에서, 비트 커밋먼트의 첫 번째 단계는 앨리스가 종이에 이진 값을 쓰고, 그 종이를 금고 안에 가두고, 열쇠는 자신이 가진 채 금고를 밥에게 주는 것처럼 작동해야 합니다. 금고가 밥의 소유에 있기 때문에 앨리스는 종이에 쓰인 이진 값에 커밋한 것입니다 (따라서 구속력이 있음). 그러나 밥이 금고를 열 수 없기 때문에 앨리스가 어떤 값에 커밋했는지 알 수 없습니다 (따라서 은폐성이 있음). 두 번째 단계는 앨리스가 밥에게 금고의 열쇠를 건네주어, 밥이 금고를 열어 앨리스가 커밋한 값을 공개하도록 하는 것처럼 작동해야 합니다.

결국, 양자 정보만으로는 완벽한 비트 커밋먼트 프로토콜을 구현하는 것이 불가능한데, 이는 정화의 유니타리 동등성과 모순되기 때문입니다. 이를 입증하는 주장의 높은 수준 요약은 다음과 같습니다.

시작하기 위해, 우리는 프로토콜이 실행될 때 앨리스와 밥이 유니타리 연산만을 수행하거나 새로운 초기화된 시스템을 도입한다고 가정할 수 있습니다. 모든 채널이 스타인스프링 표현(Stinespring representation)을 가진다는 사실은 우리가 이 가정을 할 수 있도록 합니다.

프로토콜의 커밋 단계가 끝날 때, 밥은 자신이 소유한 어떤 복합 시스템을 가지고 있는데, 이는 앨리스가 값 $0$에 커밋했다면 $\rho_0$ 상태에, 앨리스가 값 $1$에 커밋했다면 $\rho_1$ 상태에 있어야 합니다. 프로토콜이 완벽하게 은폐되려면, 밥은 이 두 상태를 구별할 수 없어야 합니다 — 따라서 $\rho_0 = \rho_1$이어야 합니다. (그렇지 않다면 이 상태들을 확률적으로 구별하는 측정이 존재할 것입니다.)

그러나 앨리스와 밥이 유니타리 연산만을 사용했기 때문에, 커밋 단계 후 프로토콜에 관련된 모든 시스템의 상태는 함께 순수 상태에 있어야 합니다. 특히, 앨리스가 $0$에 커밋했을 때 프로토콜에 관련된 모든 시스템의 순수 상태를 $\vert\psi_0\rangle$라고 가정하고, 앨리스가 $1$에 커밋했을 때 프로토콜에 관련된 모든 시스템의 순수 상태를 $\vert\psi_1\rangle$라고 가정합시다. 앨리스와 밥의 (복합 시스템일 수 있는) 시스템을 나타내기 위해 $A$와 $B$를 사용한다면,

<p>$$\rho_0 = \operatorname{Tr}_{\mathsf{A}}(\vert\psi_0\rangle\langle\psi_0\vert)$$</p>
<p>$$\rho_1 = \operatorname{Tr}_{\mathsf{A}}(\vert\psi_1\rangle\langle\psi_1\vert).$$</p>

완벽하게 은폐하는 프로토콜에 대해 $\rho_0 = \rho_1$라는 요구 사항을 감안할 때, 우리는 $\vert\psi_0\rangle$와 $\vert\psi_1\rangle$이 동일한 상태의 정화라는 것을 알게 됩니다 — 따라서 정화의 유니타리 동등성에 의해, $A$ 단독에 대한 유니타리 연산 $U$가 존재해야 합니다.
<p>$$(U\otimes\mathbb{I}_{\mathsf{B}})\vert\psi_0\rangle = \vert\psi_1\rangle.$$</p>

따라서 앨리스는 $A$에 $U$를 적용하여 커밋먼트를 $0$에서 $1$로 변경하거나, $U^{\dagger}$를 적용하여 $1$에서 $0$으로 변경할 자유가 있으며, 이로 인해 고려 중인 가상의 프로토콜은 구속력을 갖는 데 완전히 실패합니다.

### 4.3 휴스턴-조자-우터스 정리 (Hughston-Jozsa-Wootters theorem)
이 강의 부분에서 논의할 정화의 유니타리 동등성의 마지막 함의는 휴스턴-조자-우터스 정리로 알려진 다음 정리입니다. (이것은 사실 이 이름으로 알려진 정리의 약간 단순화된 진술입니다.)

>정리
>
휴스턴-조자-우터스 정리: $X$와 $Y$를 시스템이라고 하고, $\vert\phi\rangle$를 쌍 $(X, Y)$의 양자 상태 벡터라고 합시다. 또한 $N$을 임의의 양의 정수라고 하고, $(p_0,\ldots,p_{N-1})$를 확률 벡터라고 하며, $\vert\psi_0\rangle,\ldots,\vert\psi_{N-1}\rangle$를 $X$의 상태를 나타내는 양자 상태 벡터라고 합시다.
><p>$$\operatorname{Tr}_{\mathsf{Y}}\bigl(\vert\phi\rangle\langle\phi\vert\bigr) = \sum_{a = 0}^{N-1} p_a \vert\psi_a\rangle\langle\psi_a\vert.$$</p>
$(X, Y)$가 상태 $\vert\phi\rangle$에 있을 때 $Y$에 대해 이 측정이 수행되면 다음 두 진술이 참이 되는 $Y$에 대한 (일반적인) 측정 $\{P_0,\ldots,P_{N-1}\}$이 존재합니다.
>
1. 각 측정 결과 $a\in\{0,\ldots,N-1\}$는 확률 $p_a$로 나타납니다.
2. 측정 결과 $a$를 얻는다는 조건 하에, $X$의 상태는 $\vert\psi_a\rangle$가 됩니다.

직관적으로 말하면, 이 정리는 두 시스템의 순수 상태가 있는 한, 첫 번째 시스템의 축소된 상태를 순수 상태의 볼록 조합으로 생각하는 **어떤** 방식에 대해서도, 두 번째 시스템에 대한 측정이 존재하여 첫 번째 시스템에 대한 이러한 생각 방식을 효과적으로 현실로 만든다는 것을 의미합니다. 숫자 $N$은 $X$나 $Y$의 고전적 상태의 수에 의해 반드시 제한되는 것은 아님에 주목하십시오. 예를 들어, $X$와 $Y$가 큐비트인 반면 $N = 1,000,000$일 수 있습니다.

우리는 정화의 유니타리 동등성을 사용하여 이 정리를 증명할 것이며, 고전적 상태 집합이 $\{0,\ldots,N-1\}$인 새로운 시스템 $Z$를 도입하는 것부터 시작할 것입니다. 세 시스템 $(X, Y, Z)$의 다음 두 양자 상태 벡터를 고려해 봅시다.

<p>$$\vert\gamma_0\rangle = \vert\phi\rangle_{\mathsf{XY}}\otimes\vert 0\rangle_{\mathsf{Z}}$$</p>
<p>$$\vert\gamma_1\rangle = \sum_{a = 0}^{N-1} \sqrt{p_a}\, \vert\psi_a\rangle_{\mathsf{X}} \otimes \vert 0\rangle_{\mathsf{Y}} \otimes \vert a\rangle_{\mathsf{Z}}$$</p>

첫 번째 벡터 $\vert\gamma_0\rangle$는 단순히 주어진 양자 상태 벡터 $\vert\phi\rangle$와 새로운 시스템 $Z$에 대한 $\vert 0\rangle$를 텐서곱한 것입니다. 두 번째 벡터 $\vert\gamma_1\rangle$의 경우, 우리는 본질적으로 정리를 사소하게 만드는 양자 상태 벡터를 가집니다 — 적어도 $Y$가 $Z$로 대체된다면 — 왜냐하면 $Z$에 대해 수행된 표준 기저 측정은 각 결과 $a$를 확률 $p_a$로 명확하게 산출하고, 이 결과를 얻는다는 조건 하에 $X$의 상태는 $\vert\psi_a\rangle$가 되기 때문입니다.

쌍 $(Y, Z)$를 $X$를 남기기 위해 부분 대각합될 수 있는 단일 복합 시스템으로 생각함으로써, 우리는 상태 
<p>$$\rho = \sum_{a = 0}^{N-1} p_a \vert\psi_a\rangle\langle\psi_a\vert.$$</p>
의 두 가지 다른 정화를 식별했음을 발견합니다.

구체적으로, 첫 번째에 대해 우리는 다음을 가집니다.
<p>$$\operatorname{Tr}_{\mathsf{YZ}} (\vert\gamma_0\rangle\langle\gamma_0\vert) = \operatorname{Tr}_{\mathsf{Y}} (\vert\phi\rangle\langle\phi\vert) = \rho$$</p>

그리고 두 번째에 대해 우리는 다음을 가집니다.
<p>$$\begin{align}\operatorname{Tr}_{\mathsf{YZ}} (\vert\gamma_1\rangle\langle\gamma_1\vert) &= \sum_{a,b = 0}^{N-1} \sqrt{p_a}\sqrt{p_b} \, \vert\psi_a\rangle\langle\psi_a\vert \operatorname{Tr}(\vert 0\rangle\langle 0\vert \otimes \vert a\rangle\langle b\vert) \\ &= \sum_{a = 0}^{N-1} p_a \, \vert\psi_a\rangle\langle\psi_a\vert \\ &= \rho.\end{align}$$</p>

따라서 정화의 유니타리 동등성에 의해 
<p>$$(\mathbb{I}_{\mathsf{X}} \otimes U) \vert \gamma_0 \rangle = \vert\gamma_1\rangle$$</p> 를 만족하는 $(Y, Z)$에 대한 유니타리 연산 $U$가 존재해야 합니다.

이 유니타리 연산 $U$를 사용하여, 우리는 다음 다이어그램이 보여주는 것처럼 정리의 요구 사항을 만족하는 측정을 구현할 수 있습니다. 말로 설명하면, 우리는 $\vert 0\rangle$ 상태로 초기화된 새로운 시스템 $Z$를 도입하고, $U$를 $(Y, Z)$에 적용하며, 이는 $(X, Y, Z)$의 상태를 $\vert\gamma_0\rangle$에서 $\vert\gamma_1\rangle$로 변환하고, 그런 다음 $Z$를 표준 기저 측정으로 측정하는데, 이는 우리가 이미 원하는 동작을 제공함을 관찰했습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/purifications-and-fidelity/HSW-measurement.svg" style="width:70%">

그림의 점선 직사각형은 이 측정의 구현을 나타내며, 이는 다음과 같이 양의 준정부호 행렬(positive semidefinite matrices) $\\{P_0,\ldots,P_{N-1}\\}$의 집합으로 설명될 수 있습니다.

<p>$P_a = (\mathbb{I}_{\mathsf{Y}} \otimes \langle 0\vert) U^{\dagger} (\mathbb{I}_{\mathsf{Y}} \otimes \vert a\rangle\langle a \vert)U (\mathbb{I}_{\mathsf{Y}} \otimes \vert 0\rangle)$$</p>

---

# 충실도 (Fidelity)

이 강의 부분에서는 양자 상태 간의 **충실도**에 대해 논의할 것인데, 이는 양자 상태들의 유사성 — 즉, 얼마나 많이 "겹치는지" — 에 대한 척도입니다. 

두 양자 상태 벡터가 주어졌을 때, 이 양자 상태 벡터와 관련된 순수 상태 간의 충실도는 양자 상태 벡터 간의 내적의 절댓값과 같습니다. 이는 유사성을 측정하는 기본적인 방법을 제공합니다: 그 결과는 $0$과 $1$ 사이의 값이며, 값이 클수록 더 큰 유사성을 나타냅니다. 특히, 직교 상태의 경우 (정의상) 값은 $0$이고, 전역 위상(global phase)까지 동등한 상태의 경우 값은 $1$입니다.

직관적으로 말하면, 충실도는 이 기본적인 유사성 척도를 양자 상태 벡터에서 밀도 행렬로 확장한 것으로 볼 수 있습니다.

## 1. 충실도의 정의
충실도의 정의부터 시작하는 것이 적절합니다. 언뜻 보기에, 이어지는 정의는 특이하거나 신비롭게 보일 수 있으며, 다루기 쉽지 않을 수도 있습니다. 그러나 이 함수는 많은 흥미로운 속성과 여러 대안적인 공식을 가지고 있음이 밝혀져, 처음 보일 수 있는 것보다 훨씬 다루기 쉽습니다.

> 정의
>
$\rho$와 $\sigma$를 동일한 시스템의 양자 상태를 나타내는 밀도 행렬이라고 합시다. $\rho$와 $\sigma$ 사이의 **충실도**는 다음과 같이 정의됩니다:
> <p>$$\operatorname{F}(\rho,\sigma) = \operatorname{Tr}\sqrt{\sqrt{\rho} \sigma \sqrt{\rho}}.$$</p>
>
비고 \
이것이 일반적인 정의이지만, 충실도가 여기서 정의된 양의 **제곱**으로 정의되는 것도 흔하며, 이 경우 이를 **제곱근 충실도**(*root-fidelity*)라고 부릅니다. 어느 정의도 옳거나 틀린 것은 아니며 — 이는 본질적으로 선호의 문제입니다. 그럼에도 불구하고, 어떤 정의가 사용되고 있는지 항상 이해하거나 명확히 해야 합니다.

정의의 공식이 의미를 가지게 하려면, 먼저 $\sqrt{\rho} \sigma \sqrt{\rho}$가 양의 준정부호 행렬(positive semidefinite matrix)임을 주목해야 합니다:

<p>$$\sqrt{\rho} \sigma \sqrt{\rho} = M^{\dagger} M$$</p>

여기서 $M = \sqrt{\sigma}\sqrt{\rho}$입니다. 모든 양의 준정부호 행렬과 마찬가지로, 이 양의 준정부호 행렬은 고유한 양의 준정부호 제곱근을 가지며, 그 대각합(trace)이 충실도입니다.

모든 정사각 행렬 $M$에 대해, 두 양의 준정부호 행렬 $M^{\dagger} M$와 $M M^{\dagger}$의 고유값은 항상 동일하며, 따라서 이 행렬들의 제곱근에 대해서도 마찬가지입니다. $M = \sqrt{\sigma}\sqrt{\rho}$를 선택하고 정사각 행렬의 대각합이 그 고유값의 합이라는 사실을 사용하면, 다음을 알 수 있습니다:

<p>$$\begin{aligned} \operatorname{F}(\rho,\sigma) & = \operatorname{Tr}\sqrt{\sqrt{\rho} \sigma \sqrt{\rho}}\\ & = \operatorname{Tr}\sqrt{M^{\dagger} M} = \operatorname{Tr}\sqrt{M M^{\dagger}}\\ & = \operatorname{Tr}\sqrt{\sqrt{\sigma} \rho \sqrt{\sigma}}\\ & = \operatorname{F}(\sigma,\rho). \end{aligned}$$</p>

따라서, 정의만으로는 즉각적이지 않지만, 충실도는 두 인수에 대해 대칭적입니다.

### 1.1 대각합 노름(trace norm) 관점의 충실도
충실도를 표현하는 동등한 방법은 다음 공식에 의한 것입니다:
<p>$$\operatorname{F}(\rho,\sigma) = \bigl\|\sqrt{\sigma}\sqrt{\rho}\bigr\|_1.$$</p>

여기서 우리는 상태 구별(state discrimination)의 맥락에서 이전 강의에서 접했던 **대각합 노름**을 봅니다. (반드시 정사각 행렬일 필요는 없는) 행렬 $M$의 대각합 노름은 다음과 같이 정의될 수 있습니다:
<p>$$\| M \|_1 = \operatorname{Tr}\sqrt{M^{\dagger} M},$$</p>

그리고 이 정의를 행렬 $\sqrt{\sigma}\sqrt{\rho}$에 적용함으로써 우리는 정의에 있는 공식을 얻습니다.

(정사각) 행렬 $M$의 대각합 노름을 표현하는 대안적인 방법은 다음 공식을 통하는 것입니다.
<p>$$\| M \|_1 = \max_{U\:\text{unitary}} \bigl\vert \operatorname{Tr}(M U) \bigr\vert.$$</p>

여기서 최댓값은 $M$과 동일한 수의 행과 열을 가진 모든 **유니타리** 행렬 $U$에 대한 것입니다. 당면한 상황에 이 공식을 적용하면 충실도의 또 다른 표현이 드러납니다.
<p>$$\operatorname{F}(\rho,\sigma) = \max_{U\:\text{unitary}} \bigl\vert\operatorname{Tr}\bigl( \sqrt{\sigma}\sqrt{\rho}\, U\bigr) \bigr\vert$$</p>

### 1.2 순수 상태에 대한 충실도
충실도 정의에 대한 마지막 요점은 모든 순수 상태는 (밀도 행렬로서) 자체 제곱근과 같다는 것이며, 이는 상태 중 하나 또는 둘 다 순수할 때 충실도 공식을 상당히 단순화할 수 있도록 합니다. 특히, 두 상태 중 하나가 순수하면 우리는 다음 공식을 가집니다.
<p>$$\operatorname{F}\bigl( \vert\phi\rangle\langle\phi\vert, \sigma \bigr) = \sqrt{\langle \phi\vert \sigma \vert \phi \rangle}$$</p>

두 상태 모두 순수하다면, 이 공식은 섹션 시작 부분에서 언급되었듯이 해당 양자 상태 벡터의 내적의 절댓값으로 단순화됩니다.
<p>$$\operatorname{F}\bigl( \vert\phi\rangle\langle\phi\vert, \vert\psi\rangle\langle\psi\vert \bigr) = \bigl\vert \langle \phi\vert \psi \rangle \bigr\vert$$</p>

## 2. 충실도의 기본 속성
충실도는 많은 놀라운 속성과 여러 대안적인 공식을 가지고 있습니다. 다음은 증명 없이 나열된 몇 가지 기본적인 속성입니다.

1\. 동일한 크기를 가진 임의의 두 밀도 행렬 $\rho$와 $\sigma$에 대해, 충실도 $\operatorname{F}(\rho,\sigma)$는 $0$과 $1$ 사이에 있습니다: $0\leq \operatorname{F}(\rho,\sigma) \leq 1$. $\operatorname{F}(\rho,\sigma)=0$인 경우는 $\rho$와 $\sigma$가 직교 이미지(orthogonal images)를 가질 때(따라서 오류 없이 구별될 수 있음)에 한하며, $\operatorname{F}(\rho,\sigma)=1$인 경우는 $\rho = \sigma$일 때에 한합니다.

2\. 충실도는 **곱셈적**(*multiplicative*)입니다. 즉, 두 곱 상태(product states) 사이의 충실도는 개별 충실도의 곱과 같습니다:
<p>$$\operatorname{F}(\rho_1\otimes\cdots\otimes\rho_m,\sigma_1\otimes\cdots\otimes\sigma_m) = \operatorname{F}(\rho_1,\sigma_1)\cdots \operatorname{F}(\rho_m,\sigma_m).$$</p>

3\. 상태 간의 충실도는 임의의 채널(channel)의 작용 하에서 감소하지 않습니다. 즉, $\rho$와 $\sigma$가 밀도 행렬이고 $\Phi$가 이 두 상태를 입력으로 취할 수 있는 채널이라면, 다음이 반드시 성립합니다:
<p>$$\operatorname{F}(\rho,\sigma) \leq \operatorname{F}(\Phi(\rho),\Phi(\sigma)).$$</p>

4\. **푹스-반 데 그라프 부등식**(*Fuchs-van de Graaf inequalities*)은 충실도와 대각합 거리(trace distance) 사이의 긴밀한 (비록 정확하지는 않지만) 관계를 설정합니다: 임의의 두 상태 $\rho$와 $\sigma$에 대해 다음이 성립합니다:
<p>$$1 - \frac{1}{2}\|\rho - \sigma\|_1 \leq \operatorname{F}(\rho,\sigma) \leq \sqrt{1 - \frac{1}{4}\|\rho - \sigma\|_1^2}.$$</p>

최종 속성은 그림의 형태로 표현될 수 있습니다:
<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/purifications-and-fidelity/FvdG-plot.svg" style="width:60%">

구체적으로, 동일한 시스템의 $\rho$와 $\sigma$ 상태를 임의로 선택할 때, $y$축을 $\operatorname{F}(\rho,\sigma)$에서 가로지르는 수평선과 $x$축을 $\frac{1}{2}\|\|\rho-\sigma\|\|_1$에서 가로지르는 수직선은 아래쪽으로는 $y = 1-x$ 선에 의해, 위쪽으로는 단위 원에 의해 경계가 정해진 회색 영역 내에서 교차해야 합니다. 실용적인 관점에서 이 그림에서 가장 흥미로운 영역은 회색 영역의 왼쪽 위 모서리입니다: 두 상태 사이의 충실도가 $1$에 가까우면, 그들의 대각합 거리는 $0$에 가깝고, 그 **역**도 마찬가지입니다.

## 3. 온건한 측정 보조정리 (Gentle measurement lemma)
다음으로 우리는 충실도를 비파괴 측정(non-destructive measurements)과 연결하는 단순하지만 중요한 사실인 **온건한 측정 보조정리**를 살펴볼 것입니다. 이는 때때로 등장하는 매우 유용한 보조정리이며, 충실도에 대한 겉보기에는 다루기 힘든 정의가 실제로 이 보조정리를 증명하기 매우 쉽게 만든다는 점에서도 주목할 만합니다.

설정은 다음과 같습니다. $\mathsf{X}$가 상태 $\rho$에 있는 시스템이라고 하고, $\\{P_0,\ldots,P_{m-1}\\}$를 $\mathsf{X}$의 일반적인 측정을 나타내는 양의 준정부호 행렬들의 모음이라고 합시다. 또한 이 측정이 상태 $\rho$에 있는 시스템 $\mathsf{X}$에 대해 수행될 때, 결과 중 하나가 매우 가능성이 높다고 가정합니다. 구체적으로, 가능성이 높은 측정 결과가 $0$이라고 가정하고, 특히 다음을 가정합니다.
<p>$$\operatorname{Tr}(P_0 \rho) > 1 - \varepsilon$$</p>

작은 양의 실수 $\varepsilon > 0$에 대해서입니다.

온건한 측정 보조정리가 말하는 것은, 이러한 가정 하에서, 네이마르크 정리(Naimark's theorem)를 통해 $\\{P_0,\ldots,P_{m-1}\\}$로부터 얻은 비파괴 측정이, 가능성이 높은 측정 결과 $0$이 관찰되는 경우, $\rho$에 작은 교란(disturbance)만을 야기한다는 것입니다.

더 구체적으로, 이 보조정리는 $\rho$와 결과가 $0$이라는 조건 하에 비파괴 측정으로부터 얻는 상태 사이의 충실도 제곱이 $1-\varepsilon$보다 크다고 명시합니다.
<p>$$\operatorname{F}\Biggl(\rho,\frac{\sqrt{P_0}\rho\sqrt{P_0}}{\operatorname{Tr}(P_0\rho)}\Biggr)^2 > 1-\varepsilon.$$</p>

우리는 이것을 증명하기 위해 측정에 관한 기본 사실이 필요할 것입니다. 측정 행렬 $P_0, \ldots, P_{m-1}$은 양의 준정부호이며 항등 행렬(identity)로 합산되는데, 이는 $P_0$의 모든 고유값이 $0$과 $1$ 사이의 실수라는 결론을 내릴 수 있도록 합니다. 이는 임의의 단위 벡터 $\vert\psi\rangle$에 대해, $\langle \psi \vert P_a \vert \psi \rangle$ 값이 각 $a\in\\{0,\ldots,m-1\\}$에 대해 음이 아닌 실수라는 사실(각 $P_a$가 양의 준정부호이기 때문)과, 이 수들이 $1$로 합산된다는 사실로부터 도출됩니다.

<p>$$\sum_{a = 0}^{m-1} \langle \psi \vert P_a \vert \psi \rangle = \langle \psi \vert \Biggl(\sum_{a = 0}^{m-1} P_a \Biggr) \vert \psi \rangle = \langle \psi \vert \mathbb{I} \vert \psi \rangle = 1.$$</p>

따라서 $\langle \psi \vert P_0 \vert \psi \rangle$는 항상 $0$과 $1$ 사이의 실수이며, 이는 $P_0$의 모든 고유값이 $0$과 $1$ 사이의 실수임을 의미합니다. 왜냐하면 우리는 관심 있는 고유값에 해당하는 단위 고유 벡터가 되도록 $\vert\psi\rangle$를 구체적으로 선택할 수 있기 때문입니다.

이 관찰로부터 우리는 모든 밀도 행렬 $\rho$에 대해 다음 부등식을 결론내릴 수 있습니다.
<p>$$\operatorname{Tr}\bigl( \sqrt{P_0} \rho\bigr) \geq \operatorname{Tr}\bigl( P_0 \rho\bigr)$$</p>

더 자세히, 스펙트럼 분해
<p>$$P_0 = \sum_{k=0}^{n-1} \lambda_k \vert\psi_k\rangle\langle\psi_k\vert$$</p>

로부터 시작하여 우리는 다음을 결론내립니다.
<p>$$\operatorname{Tr}\bigl( \sqrt{P_0} \rho\bigr) = \sum_{k = 0}^{n-1} \sqrt{\lambda_k} \langle \psi_k \vert \rho \vert \psi_k \rangle \geq \sum_{k = 0}^{n-1} \lambda_k \langle \psi_k \vert \rho \vert \psi_k \rangle = \operatorname{Tr}\bigl( P_0 \rho\bigr)$$</p>

이는 $\langle \psi_k \vert \rho \vert \psi_k \rangle$가 음이 아닌 실수이고, 각 $k = 0,\ldots,n-1$에 대해 $\sqrt{\lambda_k} \geq \lambda_k$라는 사실로부터 기인합니다. ($0$과 $1$ 사이의 숫자를 제곱하면 결코 커질 수 없습니다.)

이제 우리는 충실도를 평가한 다음 우리의 부등식을 사용하여 온건한 측정 보조정리를 증명할 수 있습니다. 먼저, 우리가 관심 있는 표현을 단순화해 봅시다.
<p>$$\begin{aligned} \operatorname{F}\Biggl(\rho,\frac{\sqrt{P_0}\rho\sqrt{P_0}}{\operatorname{Tr}(P_0\rho)}\Biggr) & = \operatorname{Tr}\sqrt{\frac{\sqrt{\rho}\sqrt{P_0}\rho\sqrt{P_0}\sqrt{\rho}}{ \operatorname{Tr}(P_0\rho)}}\\ & = \operatorname{Tr}\sqrt{\Biggl(\frac{\sqrt{\rho}\sqrt{P_0}\sqrt{\rho}}{ \sqrt{\operatorname{Tr}(P_0\rho)}}\Biggr)^2}\\ & = \operatorname{Tr}\Biggl(\frac{\sqrt{\rho}\sqrt{P_0}\sqrt{\rho}}{ \sqrt{\operatorname{Tr}(P_0\rho)}}\Biggr)\\ & = \frac{\operatorname{Tr}\bigl(\sqrt{P_0}\rho\bigr)}{\sqrt{\operatorname{Tr}(P_0\rho)}} \end{aligned}$$</p>

이것들은 모두 등식이라는 점에 주목하십시오 — 이 시점에서 우리는 우리의 부등식(또는 다른 어떤 부등식)도 사용하지 않았으므로, 충실도에 대한 정확한 표현을 가지고 있습니다. 이제 우리는 우리의 부등식을 사용하여 다음을 결론내릴 수 있습니다.
<p>$$\operatorname{F}\Biggl(\rho,\frac{\sqrt{P_0}\rho\sqrt{P_0}}{\operatorname{Tr}(P_0\rho)}\Biggr) = \frac{\operatorname{Tr}\bigl(\sqrt{P_0}\rho\bigr)}{\sqrt{\operatorname{Tr}(P_0\rho)}} \geq \frac{\operatorname{Tr}\bigl(P_0\rho\bigr)}{\sqrt{\operatorname{Tr}(P_0\rho)}} = \sqrt{\operatorname{Tr}\bigl(P_0\rho\bigr)}$$</p>

그리고 따라서, 양변을 제곱함으로써,
<p>$$\operatorname{F}\Biggl(\rho,\frac{\sqrt{P_0}\rho\sqrt{P_0}}{\operatorname{Tr}(P_0\rho)}\Biggr)^2 \geq \operatorname{Tr}\bigl(P_0\rho\bigr) > 1-\varepsilon.$$</p>

## 4. 울만의 정리 (Uhlmann's theorem)
강의를 마무리하기 위해, 우리는 충실도와 정화(purification) 개념을 연결하는 근본적인 사실인 **울만의 정리**를 살펴볼 것입니다. 이 정리가 간단히 말하는 것은, 임의의 두 양자 상태 사이의 충실도는 이 상태들의 두 정화 사이의 **최대** 내적(절댓값)과 같다는 것입니다.

>정리
>
울만의 정리: $\rho$와 $\sigma$를 시스템 $\mathsf{X}$의 상태를 나타내는 밀도 행렬이라고 하고, $\mathsf{Y}$를 $\mathsf{X}$만큼 많은 고전적 상태를 가진 시스템이라고 가정합시다. $\rho$와 $\sigma$ 사이의 충실도는 다음으로 주어집니다:
> <p>$$\operatorname{F}(\rho,\sigma) = \max\bigl\{ \vert \langle \phi \vert \psi \rangle \vert \,:\, \operatorname{Tr}_{\mathsf{Y}}\bigl(\vert\phi\rangle\langle\phi\vert\bigr) = \rho,\; \operatorname{Tr}_{\mathsf{Y}}\bigl(\vert\psi\rangle\langle\psi\vert\bigr) = \sigma\bigr\},$$</p>
>
여기서 최댓값은 $(\mathsf{X},\mathsf{Y})$의 모든 양자 상태 벡터 $\vert\phi\rangle$와 $\vert\psi\rangle$에 대해 취해집니다.

우리는 정화의 유니타리 동등성(unitary equivalence of purifications)을 사용하여 이 정리를 증명할 수 있지만 — 완전히 간단하지는 않으며 도중에 트릭을 사용할 것입니다.

시작하기 위해, 두 밀도 행렬 $\rho$와 $\sigma$의 스펙트럼 분해를 고려해 봅시다.
<p>$$\begin{aligned} \rho & = \sum_{a = 0}^{n-1} p_a \vert u_a\rangle\langle u_a\vert \\[2mm] \sigma & = \sum_{b = 0}^{n-1} q_b \vert v_b\rangle\langle v_b\vert \end{aligned}$$</p>

두 모음 $\\{\vert u_0 \rangle,\ldots,\vert u_{n-1}\rangle\\}$와 $\\{\vert v_0 \rangle,\ldots,\vert v_{n-1}\rangle\\}$은 각각 $\rho$와 $\sigma$의 고유 벡터의 정규 직교 기저이며, $p_0,\ldots,p_{n-1}$와 $q_0,\ldots,q_{n-1}$는 해당 고유값입니다.

우리는 또한 $\vert u_0 \rangle,\ldots,\vert u_{n-1}\rangle$와 $\vert v_0 \rangle,\ldots,\vert v_{n-1}\rangle$의 각 항목의 복소 켤레(complex conjugate)를 취하여 얻은 벡터를 $\vert \overline{u_0} \rangle,\ldots,\vert \overline{u_{n-1}}\rangle$와 $\vert \overline{v_0} \rangle,\ldots,\vert \overline{v_{n-1}}\rangle$로 정의할 것입니다. 즉, 임의의 벡터 $\vert w \rangle$에 대해, 각 $c\in\\{0,\ldots,n-1\\}$에 대해 다음 방정식에 따라 $\vert\overline{w}\rangle$를 정의할 수 있습니다.
<p>$$\langle c \vert \overline{w}\rangle = \overline{\langle c \vert w\rangle}$$</p>

임의의 두 벡터 $\vert u \rangle$와 $\vert v \rangle$에 대해 $\langle \overline{u} \vert \overline{v}\rangle = \langle v\vert u\rangle$임을 주목하십시오. 더 일반적으로, 임의의 정사각 행렬 $M$에 대해 우리는 다음 공식을 가집니다.
<p>$$\langle \overline{u} \vert M \vert \overline{v}\rangle = \langle v\vert M^T \vert u\rangle$$</p>

그 결과 $\vert u \rangle$와 $\vert v \rangle$가 직교하는 것은 $\vert \overline{u}\rangle$와 $\vert \overline{v}\rangle$가 직교하는 경우에 한하며, 따라서 $\{\vert \overline{u_0} \rangle,\ldots,\vert \overline{u_{n-1}}\rangle\}$와 $\{\vert \overline{v_0} \rangle,\ldots,\vert \overline{v_{n-1}}\rangle\}$는 모두 정규 직교 기저입니다.

이제 $\rho$와 $\sigma$의 정화인 다음 두 벡터 $\vert\phi\rangle$와 $\vert\psi\rangle$를 고려해 봅시다.
<p>$$\begin{aligned} \vert\phi\rangle & = \sum_{a = 0}^{n-1} \sqrt{p_a}\, \vert u_a\rangle \otimes \vert \overline{u_a}\rangle \\[2mm] \vert\psi\rangle & = \sum_{b = 0}^{n-1} \sqrt{q_b}\, \vert v_b\rangle \otimes \vert \overline{v_b}\rangle \end{aligned}$$</p>

이것이 이전에 언급된 트릭입니다. 이 시점에서 $\rho$와 $\sigma$의 정화에 대해 이러한 특정 선택을 하는 것이 좋은 생각임을 명시적으로 나타내는 것은 없지만, 이들은 유효한 정화이며, 복소 켤레는 우리가 필요한 방식으로 대수 계산을 진행할 수 있도록 할 것입니다.

정화의 유니타리 동등성에 의해, 우리는 시스템 쌍 $(\mathsf{X},\mathsf{Y})$에 대한 $\rho$의 모든 정화가 어떤 유니타리 행렬 $U$에 대해 $$(\mathbb{I}_{\mathsf{X}}\otimes U)\vert\phi\rangle$$ 형태를 취해야 한다는 것을 알고 있으며, 마찬가지로 쌍 $$(\mathsf{X},\mathsf{Y})$$에 대한 $\sigma$의 모든 정화는 어떤 유니타리 행렬 $V$에 대해 $(\mathbb{I}_{\mathsf{X}}\otimes V)\vert\psi\rangle$ 형태를 취해야 합니다. 그러한 두 정화의 내적은 다음과 같이 단순화될 수 있습니다.
<p>$$\begin{aligned} \langle \phi \vert (\mathbb{I}\otimes U^{\dagger}) (\mathbb{I}\otimes V) \vert \psi \rangle \hspace{-2.5cm}\\ & = \sum_{a,b = 0}^{n-1} \sqrt{p_a} \sqrt{q_b}\, \langle u_a \vert v_b \rangle \langle \overline{u_a} \vert U^{\dagger} V \vert \overline{v_b} \rangle \\ & = \sum_{a,b = 0}^{n-1} \sqrt{p_a} \sqrt{q_b}\, \langle u_a \vert v_b \rangle \langle v_b \vert (U^{\dagger} V)^T \vert u_a \rangle \\ & = \operatorname{Tr}\Biggl( \sum_{a,b = 0}^{n-1} \sqrt{p_a} \sqrt{q_b}\, \vert u_a \rangle\langle u_a \vert v_b \rangle \langle v_b \vert (U^{\dagger} V)^T\Biggr)\\ & = \operatorname{Tr}\Bigl( \sqrt{\rho}\sqrt{\sigma}\, (U^{\dagger} V)^T\Bigr) \end{aligned}$$</p>

$U$와 $V$가 모든 가능한 유니타리 행렬에 걸쳐 변할 때, 행렬 $(U^{\dagger} V)^T$ 또한 모든 가능한 유니타리 행렬에 걸쳐 변합니다. 따라서 $\rho$와 $\sigma$의 두 정화의 내적의 절댓값을 최대화하면 다음 방정식을 얻습니다.
<p>$$\begin{aligned} \max_{U,V\:\text{unitary}} \biggl\vert \operatorname{Tr}\Bigl( \sqrt{\rho}\sqrt{\sigma}\, (U^{\dagger} V)^T\Bigr)\biggr\vert & = \max_{W\:\text{unitary}} \biggl\vert \operatorname{Tr}\Bigl( \sqrt{\rho}\sqrt{\sigma}\, W\Bigr)\biggr\vert\\[2mm] & = \bigl\| \sqrt{\rho}\sqrt{\sigma} \bigr\|_1\\[2mm] & = \operatorname{F}(\rho,\sigma) \end{aligned}$$</p>
