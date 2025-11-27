---
title: 7차시 1:General formulation of quantum information(Density Matrices)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 밀도 행렬 기본 사항
우리는 밀도 행렬이 수학적으로 무엇인지 설명하는 것으로 시작하여 몇 가지 예시를 살펴볼 것입니다. 그 후, 우리는 밀도 행렬이 작동하는 방식과 양자 정보의 단순화된 공식화에서 양자 상태 벡터와 어떻게 관련되는지에 대한 몇 가지 기본 측면을 논의할 것입니다.

## 1. 정의
시스템 $\mathsf{X}$라는 양자 시스템이 있다고 가정하고, $\Sigma$를 이 시스템의 (유한하고 비어 있지 않은) 고전적 상태 집합이라고 합시다. 여기서 우리는 "양자 정보의 기초" 과정에서 사용된 명명 규칙을 따르고 있으며, 기회가 있을 때 계속 그렇게 할 것입니다.

양자 정보의 일반적인 공식화에서, 시스템 $\mathsf{X}$의 양자 상태는 엔트리(성분)가 복소수이고 인덱스(행과 열 모두)가 고전적 상태 집합 $\Sigma$와 대응하도록 배치된 *밀도 행렬* $\rho$로 기술됩니다. 소문자 그리스 문자 $\rho$는 밀도 행렬 이름으로 관례적으로 첫 번째 선택이지만, $\sigma$와 $\xi$ 또한 흔한 선택입니다. 여기에 큐비트 상태를 기술하는 몇 가지 밀도 행렬의 예가 있습니다.

<p>$$
\begin{pmatrix}
1 & 0\\
0 & 0
\end{pmatrix},
\quad
\begin{pmatrix}
\frac{1}{2} & \frac{1}{2}\\[2mm]
\frac{1}{2} & \frac{1}{2}
\end{pmatrix},
\quad
\begin{pmatrix}
\frac{3}{4} & \frac{i}{8}\\[2mm]
-\frac{i}{8} & \frac{1}{4}
\end{pmatrix},
\quad\text{and}\quad
\begin{pmatrix}
\frac{1}{2} & 0\\[2mm]
0 & \frac{1}{2}
\end{pmatrix}.
$$</p>

$\rho$가 밀도 행렬이라는 것은 잠시 후에 설명할 다음 두 가지 조건이 모두 충족됨을 의미합니다:
1. 단위 대각합: $\operatorname{Tr}(\rho) = 1$.
2. 양의 준정부성 (Positive semidefiniteness): $\rho \geq 0$.

### 1.1 행렬의 대각합 (The trace of a matrix)
밀도 행렬에 대한 첫 번째 조건은 행렬의 *대각합(trace)*을 나타냅니다. 이것은 모든 정사각 행렬에 대해 대각선 엔트리(성분)의 합으로 정의되는 함수입니다.

<p>$$
\operatorname{Tr}
\begin{pmatrix}
\alpha_{0,0} & \alpha_{0,1} & \cdots & \alpha_{0,n-1}\\[1.5mm]
\alpha_{1,0} & \alpha_{1,1} & \cdots & \alpha_{1,n-1}\\[1.5mm]
\vdots & \vdots & \ddots & \vdots\\[1.5mm]
\alpha_{n-1,0} & \alpha_{n-1,1} & \cdots & \alpha_{n-1,n-1}
\end{pmatrix}
= \alpha_{0,0} + \alpha_{1,1} + \cdots + \alpha_{n-1,n-1}.
$$</p>

대각합은 *선형* 함수입니다: 동일한 크기의 임의의 두 정사각 행렬 $A$와 $B$, 그리고 임의의 두 복소수 $\alpha$와 $\beta$에 대해 다음 방정식은 항상 참입니다. 

<p>$$
\operatorname{Tr}(\alpha A + \beta B) = \alpha \operatorname{Tr}(A) + \beta\operatorname{Tr}(B)
$$</p>

대각합은 매우 중요한 함수이며, 이에 대해 더 많은 것을 말할 수 있지만, 필요할 때까지 기다릴 것입니다.

### 1.2 양의 준정부 행렬
두 번째 조건은 행렬이 *양의 준정부(positive semidefinite)*라는 속성을 나타내는데, 이는 양자 정보 이론 및 다른 많은 주제에서 근본적인 개념입니다. 행렬 $P$는 $P = M^{\dagger} M$을 만족하는 행렬 $M$이 존재할 경우 *양의 준정부*입니다. 

<p>$$
P = M^{\dagger} M.
$$</p>

여기서 우리는 $M$이 $P$와 같은 크기의 정사각 행렬이 되도록 요구하거나 비정사각 행렬이 되도록 허용할 수 있습니다. 어떤 경우든 우리는 동일한 부류의 행렬을 얻습니다. 

이 조건을 정의하는 몇 가지 대안적인 (그러나 동등한) 방법이 있으며, 다음을 포함합니다:

*   행렬 $P$는 $P$가 에르미트 행렬(즉, 켤레 전치와 같음)이고 모든 고유값(eigenvalues)이 음수가 아닌 실수인 경우에만 양의 준정부입니다. 행렬이 에르미트 행렬이고 모든 고유값이 음수가 아닌지 확인하는 것은 그것이 양의 준정부임을 검증하는 간단한 계산 방법입니다. 

*   행렬 $P$는 $P$의 행과 열과 동일한 인덱스를 갖는 모든 복소수 벡터 $\vert\psi\rangle$에 대해 $\langle \psi \vert P \vert \psi \rangle \geq 0$인 경우에만 양의 준정부입니다.

양의 준정부 행렬에 대해 생각하는 직관적인 방법은 그것들이 음수가 아닌 실수에 대한 행렬 유사체와 같다고 보는 것입니다. 즉, 양의 준정부 행렬이 복소 정사각 행렬에 대해 갖는 관계는 음수가 아닌 실수가 복소수에 대해 갖는 관계와 같습니다. 예를 들어, 복소수 $\alpha$는 $\alpha = \overline{\beta} \beta$를 만족하는 어떤 복소수 $\beta$가 존재하는 경우에만 음수가 아닌 실수인데, 이는 행렬을 스칼라로 대체했을 때 양의 준정부성 정의와 일치합니다. 일반적으로 행렬은 스칼라보다 더 복잡한 객체이지만, 그럼에도 불구하고 이것은 양의 준정부 행렬에 대해 생각하는 데 유용한 방법입니다.

이는 또한 $P$가 양의 준정부임을 나타내는 일반적인 표기법 $P\geq 0$를 설명합니다. 특히, 이 문맥에서 $P \geq 0$이 $P$의 각 엔트리가 음수가 아님을 의미하는 *것은 아님*에 유의하십시오. 음수 엔트리를 갖는 양의 준정부 행렬이 있으며, 모든 엔트리가 양수이지만 양의 준정부가 아닌 행렬도 있습니다.

### 1.3 밀도 행렬의 해석
이 시점에서 밀도 행렬의 정의는 다소 임의적이고 추상적으로 보일 수 있는데, 이는 우리가 아직 이 행렬들 또는 그 엔트리들과 어떤 의미도 연관 짓지 않았기 때문입니다. 밀도 행렬이 작동하고 해석될 수 있는 방식은 레슨이 계속됨에 따라 명확해질 것이지만, 지금은 밀도 행렬의 엔트리에 대해 다음의 (다소 비공식적인) 방식으로 생각하는 것이 도움이 될 수 있습니다.

*   밀도 행렬의 **대각선** 엔트리는 표준 기저 측정(standard basis measurement)을 수행할 경우 각 고전적 상태가 나타날 확률을 제공합니다. 따라서 우리는 이러한 엔트리를 각 고전적 상태와 관련된 "가중치" 또는 "가능성"을 설명하는 것으로 생각할 수 있습니다. 

*   밀도 행렬의 **비대각선** 엔트리는 해당 엔트리에 해당하는 두 고전적 상태(즉, 행에 해당하는 상태와 열에 해당하는 상태)가 양자 중첩되어 있는 정도와 그 사이의 상대 위상을 설명합니다.

양자 상태가 밀도 행렬로 표현되어야 한다는 것은 *선험적으로* 명확하지 않습니다. 실제로, 양자 상태를 밀도 행렬로 표현하기로 한 선택은 양자 정보의 전체 수학적 기술로 자연스럽게 이어진다는 의미가 있습니다. 양자 정보에 대한 다른 모든 것은 실제로 이 하나의 선택으로부터 상당히 논리적으로 따릅니다!

### 2. 양자 상태 벡터와의 연결
시스템 $\mathsf{X}$의 양자 상태를 기술하는 양자 상태 벡터 $\vert\psi\rangle$는 유클리드 노름이 1과 같고 엔트리가 고전적 상태 집합 $\Sigma$와 대응하도록 배치된 열 벡터임을 상기하십시오. 동일한 상태의 밀도 행렬 표현 $\rho$는 다음과 같이 정의됩니다. $\rho = \vert\psi\rangle\langle\psi\vert$. 

명확히 하자면, 우리는 열 벡터에 행 벡터를 곱하는 것이므로, 그 결과는 행과 열이 $\Sigma$에 해당하는 정사각 행렬입니다. 이 형태의 행렬은 밀도 행렬일 뿐만 아니라, 항상 사영(projections)이며 랭크(rank)는 1과 같습니다. 

예를 들어, 두 큐비트 상태 벡터를 정의해 봅시다.
<p>$$
\begin{aligned}
\vert {+i} \rangle & = \frac{1}{\sqrt{2}} \vert 0 \rangle + \frac{i}{\sqrt{2}} \vert 1 \rangle
= \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] \frac{i}{\sqrt{2}} \end{pmatrix} \\[5mm]
\vert {-i} \rangle & = \frac{1}{\sqrt{2}} \vert 0 \rangle - \frac{i}{\sqrt{2}} \vert 1 \rangle
= \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] -\frac{i}{\sqrt{2}} \end{pmatrix}
\end{aligned}
$$</p>

이 두 벡터에 해당하는 밀도 행렬은 다음과 같습니다.
<p>$$
\begin{aligned}
\vert {+i} \rangle\langle{+i}\vert
& = \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] \frac{i}{\sqrt{2}}\end{pmatrix}
\begin{pmatrix} \frac{1}{\sqrt{2}} & - \frac{i}{\sqrt{2}}\end{pmatrix}
= \begin{pmatrix}
\frac{1}{2} & -\frac{i}{2}\\[2mm]
\frac{i}{2} & \frac{1}{2}
\end{pmatrix}\\[5mm]
\vert {-i} \rangle\langle{-i}\vert
& = \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] -\frac{i}{\sqrt{2}}\end{pmatrix}
\begin{pmatrix} \frac{1}{\sqrt{2}} & \frac{i}{\sqrt{2}}\end{pmatrix}
= \begin{pmatrix}
\frac{1}{2} & \frac{i}{2}\\[2mm]
-\frac{i}{2} & \frac{1}{2}
\end{pmatrix}
\end{aligned}
$$</p>

여기에 이 상태들과 $\vert 0\rangle$, $\vert 1\rangle$, $\vert {+}\rangle$, 그리고 $\vert {-}\rangle$를 포함한 몇 가지 다른 기본 예시들을 나열한 표가 있습니다. 우리는 이 여섯 가지 상태를 이 레슨의 후반부에서 다시 보게 될 것입니다.

|                                            State vector                                            |                                                           Density matrix                                                           |
| :------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------------------------: |
|                    $$\vert 0\rangle = \begin{pmatrix} 1 \\[1mm] 0 \end{pmatrix}$$                    |                          $$\vert 0\rangle\langle 0\vert = \begin{pmatrix} 1 & 0\\[1mm] 0 & 0 \end{pmatrix}$$                         |
|                    $$\vert 1\rangle = \begin{pmatrix} 0 \\[1mm] 1 \end{pmatrix}$$                    |                          $$\vert 1\rangle\langle 1\vert = \begin{pmatrix} 0 & 0\\[1mm] 0 & 1 \end{pmatrix}$$                         |
|  $$\vert {+}\rangle = \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] \frac{1}{\sqrt{2}} \end{pmatrix}$$  |    $$\vert {+}\rangle\langle {+}\vert = \begin{pmatrix} \frac{1}{2} & \frac{1}{2}\\[2mm] \frac{1}{2} & \frac{1}{2} \end{pmatrix}$$   |
|  $$\vert {-} \rangle = \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] -\frac{1}{\sqrt{2}}\end{pmatrix}$$ |   $$\vert {-}\rangle\langle {-}\vert = \begin{pmatrix} \frac{1}{2} & -\frac{1}{2}\\[2mm] -\frac{1}{2} & \frac{1}{2} \end{pmatrix}$$  |
| $$\vert {+i} \rangle = \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] \frac{i}{\sqrt{2}} \end{pmatrix}$$ | $$\vert {+i} \rangle\langle {+i} \vert = \begin{pmatrix} \frac{1}{2} & -\frac{i}{2}\\[2mm] \frac{i}{2} & \frac{1}{2} \end{pmatrix}$$ |
| $$\vert {-i} \rangle = \begin{pmatrix} \frac{1}{\sqrt{2}} \\[2mm] -\frac{i}{\sqrt{2}}\end{pmatrix}$$ | $$\vert {-i} \rangle\langle {-i} \vert = \begin{pmatrix} \frac{1}{2} & \frac{i}{2}\\[2mm] -\frac{i}{2} & \frac{1}{2} \end{pmatrix}$$ |

한 가지 예를 더 들자면, "양자 정보의 기초" 과정의 *단일 시스템* 레슨에 나오는 상태가 여기 있으며, 상태 벡터 표현과 밀도 행렬 표현 모두를 포함합니다.

<p>$$
\vert v\rangle = \frac{1 + 2 i}{3}\,\vert 0\rangle - \frac{2}{3}\,\vert 1\rangle
\qquad
\vert v\rangle\langle v\vert =
\begin{pmatrix}
  \frac{5}{9} & \frac{-2 - 4 i}{9}\\[2mm]
  \frac{-2 + 4 i}{9} & \frac{4}{9}
\end{pmatrix}
$$</p>

양자 상태 벡터 $\vert \psi \rangle$에 대해 $\rho = \vert \psi \rangle \langle \psi \vert$ 형태를 취하는 밀도 행렬은 ***순수 상태(pure states)***로 알려져 있습니다. 모든 밀도 행렬이 이 형태로 쓰여질 수 있는 것은 아니며, 일부 상태는 순수하지 않습니다. 

밀도 행렬로서, 순수 상태는 항상 하나의 고유값이 1과 같고 다른 모든 고유값은 0과 같습니다. 이는 밀도 행렬의 고유값이 그 상태에 내재된 무작위성(randomness) 또는 불확실성을 설명한다는 해석과 일치합니다. 본질적으로, 순수 상태 $\rho = \vert \psi \rangle \langle \psi \vert$에는 불확실성이 없으며, 그 상태는 분명히 $\vert \psi \rangle$입니다.

일반적으로, $n$개의 고전적 상태를 가진 시스템에 대한 양자 상태 벡터에 대해, 

<p>$$
\vert\psi\rangle =
\begin{pmatrix}
\alpha_0\\
\alpha_1\\
\vdots\\
\alpha_{n-1}
\end{pmatrix}
$$</p>

동일한 상태의 밀도 행렬 표현은 다음과 같습니다.
<p>$$
\begin{aligned}
\vert\psi\rangle\langle\psi\vert
& =
\begin{pmatrix}
\alpha_0 \overline{\alpha_0} & \alpha_0 \overline{\alpha_1} & \cdots & \alpha_0 \overline{\alpha_{n-1}}\\[1mm]
\alpha_1 \overline{\alpha_0} & \alpha_1 \overline{\alpha_1} & \cdots & \alpha_1 \overline{\alpha_{n-1}}\\[1mm]
\vdots & \vdots & \ddots & \vdots\\[1mm]
\alpha_{n-1} \overline{\alpha_0} & \alpha_{n-1} \overline{\alpha_1} & \cdots & \alpha_{n-1} \overline{\alpha_{n-1}}
\end{pmatrix}\\[10mm]
& = \begin{pmatrix}
\vert\alpha_0\vert^2 & \alpha_0 \overline{\alpha_1} & \cdots & \alpha_0 \overline{\alpha_{n-1}}\\[1mm]
\alpha_1 \overline{\alpha_0} & \vert\alpha_1\vert^2 & \cdots & \alpha_1 \overline{\alpha_{n-1}}\\[1mm]
\vdots & \vdots & \ddots & \vdots\\[1mm]
\alpha_{n-1} \overline{\alpha_0} & \alpha_{n-1} \overline{\alpha_1} & \cdots & \vert\alpha_{n-1}\vert^2
\end{pmatrix}
\end{aligned}$$</p>


따라서 순수 상태의 특별한 경우에, 우리는 밀도 행렬의 대각선 엔트리가 표준 기저 측정이 가능한 각 고전적 상태를 출력할 확률을 설명한다는 것을 확인할 수 있습니다. 

순수 상태에 대한 마지막 언급은 밀도 행렬이 양자 상태 벡터에서 발견되는 **전역 위상(global phases)**에 관한 퇴화(degeneracy)를 제거한다는 것입니다. 어떤 실수 $\theta$에 대해 전역 위상으로 인해 차이가 나는 두 양자 상태 벡터 $\vert \psi \rangle$와 $\vert \phi \rangle = e^{i \theta} \vert \psi \rangle$가 있다고 가정해 봅시다. 이들은 전역 위상으로 인해 차이가 나기 때문에, 비록 벡터 자체가 다를 수 있음에도 불구하고 정확히 동일한 양자 상태를 나타냅니다. 반면에, 이 두 상태 벡터로부터 얻는 밀도 행렬은 동일합니다.

<p>$$
\vert \phi \rangle \langle \phi \vert =
\bigl( e^{i\theta} \vert \psi \rangle \bigr) \bigl( e^{i\theta} \vert \psi \rangle \bigr)^{\dagger}
= e^{i(\theta - \theta)} \vert \psi \rangle \langle \psi \vert
= \vert \psi \rangle \langle \psi \vert
$$</p>

일반적으로, 밀도 행렬은 양자 상태의 고유한 표현을 제공합니다. 두 양자 상태는, 그들에 대해 수행될 수 있는 모든 가능한 측정에 대해 정확히 동일한 결과 통계를 생성하며, 이는 그들의 밀도 행렬 표현이 동일한 경우에만 성립합니다. 수학적 용어를 사용하여, 우리는 밀도 행렬이 양자 상태의 ***충실한(faithful)*** 표현을 제공한다고 말할 수 있습니다.

---

# 밀도 행렬의 볼록 조합

## 1. 밀도 행렬의 확률적 선택
밀도 행렬의 핵심 측면은 양자 상태의 **확률적 선택**이 관련 밀도 행렬의 **볼록 조합**으로 표현된다는 것입니다. 

예를 들어, 시스템 $\mathsf{X}$의 양자 상태를 나타내는 두 개의 밀도 행렬 $\rho$와 $\sigma$가 있고, 우리가 확률 $p$로 상태 $\rho$를, 확률 $1-p$로 상태 $\sigma$를 준비한다면, 결과 양자 상태는 밀도 행렬로 표현됩니다.

<p>$$
p \rho + (1 - p) \sigma.
$$</p>

더 일반적으로, 밀도 행렬 $\rho_0, \ldots, \rho_{m-1}$로 표현되는 $m$개의 양자 상태가 있고, 어떤 확률 벡터 $(p_0, \ldots, p_{m-1})$에 대해 확률 $p_k$로 시스템이 상태 $\rho_k$에 준비된다면, 결과 상태는 밀도 행렬로 표현됩니다.

<p>$$
\sum_{k = 0}^{m-1} p_k \rho_k.
$$</p>

이것은 밀도 행렬 $\rho_0, \ldots, \rho_{m-1}$의 **볼록 조합**입니다. 따라서 $m$개의 양자 상태 벡터 $\vert\psi_0\rangle, \ldots, \vert\psi_{m-1}\rangle$가 있고, 각 $k \in \\{0, \ldots, m-1\\}$에 대해 확률 $p_k$로 시스템을 상태 $\vert\psi_k\rangle$에 준비한다면, 우리가 얻는 상태는 밀도 행렬로 표현됩니다.

<p>$$
\sum_{k = 0}^{m-1} p_k \vert\psi_k\rangle\langle\psi_k\vert.
$$</p>

예를 들어, 큐비트가 확률 $1/2$로 상태 $\vert 0\rangle$에, 확률 $1/2$로 상태 $\vert {+}\rangle$에 준비된다면, 우리가 얻는 상태의 밀도 행렬 표현은 다음과 같습니다.

<p>$$
\frac{1}{2} \vert 0\rangle\langle 0 \vert + \frac{1}{2} \vert +\rangle\langle + \vert
= \frac{1}{2} \begin{pmatrix} 1 & 0\\[1mm] 0 & 0 \end{pmatrix} + \frac{1}{2} \begin{pmatrix} \frac{1}{2} & \frac{1}{2}\\[2mm] \frac{1}{2} & \frac{1}{2} \end{pmatrix}
= \begin{pmatrix} \frac{3}{4} & \frac{1}{4}\\[2mm]
\frac{1}{4} & \frac{1}{4}
\end{pmatrix}.
$$</p>

양자 정보의 단순화된 공식화에서, 이와 같이 양자 상태 벡터를 평균화하는 것은 작동하지 않습니다. 예를 들어, 
<p>$$
\frac{1}{2} \vert 0\rangle + \frac{1}{2} \vert + \rangle
= \frac{1}{2} \begin{pmatrix}1\\[1mm] 0\end{pmatrix} + \frac{1}{2} \begin{pmatrix}\frac{1}{\sqrt{2}}\\[2mm]\frac{1}{\sqrt{2}}\end{pmatrix}
= \begin{pmatrix}\frac{2 + \sqrt{2}}{4}\\[2mm]\frac{\sqrt{2}}{4}\end{pmatrix}
$$</p>

벡터는 유클리드 노름이 1과 같지 않기 때문에 유효한 양자 상태 벡터가 아닙니다. 이것이 양자 상태 벡터에 대해 작동하지 않음을 보여주는 더 극단적인 예는, 우리가 원하는 어떤 양자 상태 벡터 $\vert\psi\rangle$를 고정하고, 확률 $1/2$로 우리의 상태를 $\vert\psi\rangle$로, 그리고 확률 $1/2$로 $-\vert\psi\rangle$로 취하는 것입니다. 이 상태들은 전역 위상(global phase)만큼 차이가 나기 때문에, 실제로는 같은 상태입니다. 하지만 평균화는 우리에게 영 벡터(zero vector)를 제공하며, 이는 유효한 양자 상태 벡터가 아닙니다.

## 2. 완전히 혼합된 상태
큐비트의 상태를 확률 $1/2$로 각각 $\vert 0\rangle$ 또는 $\vert 1\rangle$이 되도록 무작위로 설정한다고 가정해 봅시다. 결과 상태를 나타내는 밀도 행렬은 다음과 같습니다.

<p>$$
\frac{1}{2} \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert 1\rangle\langle 1\vert
= \frac{1}{2}
\begin{pmatrix}
1 & 0\\[1mm]
0 & 0
\end{pmatrix} + \frac{1}{2}
\begin{pmatrix}
0 & 0\\[1mm]
0 & 1
\end{pmatrix}
= \begin{pmatrix}
\frac{1}{2} & 0\\[1mm]
0 & \frac{1}{2}
\end{pmatrix}
= \frac{1}{2} \mathbb{I}
$$</p>

(이 방정식에서 기호 $\mathbb{I}$는 $2 \times 2$ 항등 행렬을 나타냅니다.) 이것은 **완전히 혼합된 상태(completely mixed state)**로 알려진 특별한 상태입니다. 이는 확률적 설정에서의 균일한 무작위 비트와 유사하게, 큐비트 상태에 대한 완전한 불확실성을 나타냅니다. 

이제 절차를 변경한다고 가정해 봅시다: 상태 $\vert 0\rangle$와 $\vert 1\rangle$ 대신 상태 $\vert {+}\rangle$와 $\vert {-}\rangle$를 사용할 것입니다. 유사한 방식으로 결과 상태를 설명하는 밀도 행렬을 계산할 수 있습니다.

<p>$$
\frac{1}{2} \vert +\rangle\langle +\vert + \frac{1}{2} \vert -\rangle\langle -\vert
= \frac{1}{2}
\begin{pmatrix}
\frac{1}{2} & \frac{1}{2}\\[2mm]
\frac{1}{2} & \frac{1}{2}
\end{pmatrix}
+ \frac{1}{2}
\begin{pmatrix}
\frac{1}{2} & -\frac{1}{2}\\[2mm]
-\frac{1}{2} & \frac{1}{2}
\end{pmatrix}
=
\begin{pmatrix}
\frac{1}{2} & 0\\[2mm]
0 & \frac{1}{2}
\end{pmatrix}
= \frac{1}{2} \mathbb{I}
$$</p>

상태를 변경했음에도 불구하고 이전과 동일한 밀도 행렬입니다. 사실, $\vert 0\rangle$와 $\vert 1\rangle$ 대신 *어떤* 두 개의 직교하는 큐비트 상태 벡터를 대입하더라도 동일한 결과, 즉 완전히 혼합된 상태를 다시 얻게 될 것입니다. 

이것은 버그가 아니라 **특징**입니다! 우리는 실제로 어떤 방식으로든 정확히 동일한 상태를 얻습니다. 즉, 통계적인 의미에서조차도, 이 두 절차가 생성하는 큐비트를 측정하여 구별할 수 있는 방법은 없습니다. 우리의 두 가지 다른 절차는 단순히 이 상태를 준비하는 서로 다른 방법일 뿐입니다.

우리는 두 가지 가능한 상태 집합 $\{\vert 0\rangle, \vert 1\rangle\}$와 $\{\vert {+}\rangle, \vert {-}\rangle\}$ 중 하나에서 상태를 무작위로 선택했을 때 무엇을 알 수 있는지 생각해 봄으로써 이것이 타당함을 검증할 수 있습니다. 단순하게 하기 위해, 우리가 큐비트에 유니타리 연산 $U$를 수행한 다음 표준 기저에서 측정한다고 가정해 봅시다. 


첫 번째 시나리오에서, 큐비트의 상태는 집합 $\\{\vert 0\rangle, \vert 1\rangle\\}$에서 균일하게 선택됩니다. 상태가 $\vert 0\rangle$라면, 

<p>$$
\vert \langle 0 \vert U \vert 0 \rangle \vert^2
\quad\text{and}\quad
\vert \langle 1 \vert U \vert 0 \rangle \vert^2
$$</p>

우리는 각각의 확률로 결과 $0$과 $1$을 얻습니다. 상태가 $\vert 1\rangle$이라면, 우리는 확률로 결과 $0$과 $1$을 얻습니다. 

<p>$$
\vert \langle 0 \vert U \vert 1 \rangle \vert^2
\quad\text{and}\quad
\vert \langle 1 \vert U \vert 1 \rangle \vert^2.
$$</p>

두 가지 가능성이 각각 확률 $1/2$로 발생하기 때문에, 우리는 확률로 결과 $0$을 얻고, 
<p>$$
\frac{1}{2}\vert \langle 0 \vert U \vert 0 \rangle \vert^2
+ \frac{1}{2}\vert \langle 0 \vert U \vert 1 \rangle \vert^2
$$</p>

확률로 결과 $1$을 얻습니다.

<p>$$
\frac{1}{2}\vert \langle 1 \vert U \vert 0 \rangle \vert^2
+ \frac{1}{2}\vert \langle 1 \vert U \vert 1 \rangle \vert^2.
$$</p>

이 두 표현 모두 $1/2$와 같습니다. 이것을 주장하는 한 가지 방법은 피타고라스 정리의 일반화로 볼 수 있는 선형 대수학의 사실을 사용하는 것입니다. 

> 정리: \
> $\\{\vert\psi_1\rangle,\ldots,\vert\psi_n\rangle\\}$이 (실수 또는 복소수) 벡터 공간 $\mathcal{V}$의 정규 직교 기저라고 가정해 봅시다. $\mathcal{V}$ 내의 모든 벡터 $\vert \phi\rangle$에 대해 다음이 성립합니다.
$\vert \langle \psi_1\vert\phi\rangle\vert^2 + \cdots + \vert \langle \psi_n \vert \phi \rangle\vert^2 = \| \vert\phi\rangle \|^2.$

우리는 이 정리를 적용하여 다음과 같이 확률을 결정할 수 있습니다. $0$을 얻을 확률은, 
<p>$$
\begin{aligned}
\frac{1}{2}\vert \langle 0 \vert U \vert 0 \rangle \vert^2
+ \frac{1}{2}\vert \langle 0 \vert U \vert 1 \rangle \vert^2
& = \frac{1}{2} \Bigl(
\vert \langle 0 \vert U \vert 0 \rangle \vert^2
+ \vert \langle 0 \vert U \vert 1 \rangle \vert^2
\Bigr) \\[2mm]
& = \frac{1}{2} \Bigl(
\vert \langle 0 \vert U^{\dagger} \vert 0 \rangle \vert^2
+ \vert \langle 1 \vert U^{\dagger} \vert 0 \rangle \vert^2
\Bigr)\\[2mm]
& = \frac{1}{2} \bigl\| U^{\dagger} \vert 0 \rangle \bigr\|^2
\end{aligned}
$$</p>

그리고 $1$을 얻을 확률은
<p>$$
\begin{aligned}
\frac{1}{2}\vert \langle 1 \vert U \vert 0 \rangle \vert^2
+ \frac{1}{2}\vert \langle 1 \vert U \vert 1 \rangle \vert^2
& = \frac{1}{2} \Bigl(
\vert \langle 1 \vert U \vert 0 \rangle \vert^2
+ \vert \langle 1 \vert U \vert 1 \rangle \vert^2
\Bigr) \\[2mm]
& = \frac{1}{2} \Bigl(
\vert \langle 0 \vert U^{\dagger} \vert 1 \rangle \vert^2
+ \vert \langle 1 \vert U^{\dagger} \vert 1 \rangle \vert^2
\Bigr)\\[2mm]
& = \frac{1}{2} \bigl\| U^{\dagger} \vert 1 \rangle \bigr\|^2.
\end{aligned}
$$</p>

입니다.

$U$는 유니타리(unitary)이므로, $U^{\dagger}$ 또한 유니타리이며, 이는 $U^{\dagger} \vert 0\rangle$와 $U^{\dagger} \vert 1\rangle$ 모두 단위 벡터임을 의미합니다. 따라서 두 확률 모두 $1/2$와 같습니다. 이는 우리가 $U$를 어떻게 선택하든 상관없이 측정에서 균일한 무작위 비트를 얻게 될 것임을 의미합니다. 

$\vert 0\rangle$와 $\vert 1\rangle$ 대신 다른 임의의 직교 상태 쌍에 대해서도 유사한 검증을 수행할 수 있습니다. 예를 들어, $\\{\vert {+}\rangle, \vert {-}\rangle\\}$는 정규 직교 기저이므로, 두 번째 절차에서 측정 결과 $0$을 얻을 확률은 $1/2$이고, 
<p>$$
\frac{1}{2}\vert \langle 0 \vert U \vert + \rangle \vert^2
+ \frac{1}{2}\vert \langle 0 \vert U \vert - \rangle \vert^2
= \frac{1}{2} \bigl\| U^{\dagger} \vert 0 \rangle \bigr\|^2 = \frac{1}{2}
$$</p>

$1$을 얻을 확률은 $1/2$입니다. 
<p>$$
\frac{1}{2}\vert \langle 1 \vert U \vert + \rangle \vert^2
+ \frac{1}{2}\vert \langle 1 \vert U \vert - \rangle \vert^2
= \frac{1}{2} \bigl\| U^{\dagger} \vert 1 \rangle \bigr\|^2 = \frac{1}{2}.
$$</p>

특히, 우리는 상태 $\vert 0\rangle$와 $\vert 1\rangle$에 대해 얻었던 것과 정확히 동일한 출력 통계를 얻습니다.

## 3. 확률적 상태
고전적 상태는 밀도 행렬로 표현될 수 있습니다. 특히, 시스템 $\mathsf{X}$의 각 고전적 상태 $a$에 대해, 밀도 행렬 $\rho = \vert a\rangle \langle a \vert$는 $\mathsf{X}$가 고전적 상태 $a$에 명확하게 있다는 것을 나타냅니다. 

큐비트에 대해서는 다음과 같습니다. 
<p>$$
\vert 0\rangle \langle 0 \vert = \begin{pmatrix}1 & 0 \\ 0 & 0\end{pmatrix}
\quad\text{and}\quad
\vert 1\rangle \langle 1 \vert = \begin{pmatrix}0 & 0 \\ 0 & 1\end{pmatrix},
$$</p>

그리고 일반적으로 우리는 생각하고 있는 고전적 상태에 해당하는 위치에 대각선상에 하나의 $1$을 가지고 있으며, 다른 모든 엔트리는 $0$입니다. 

그런 다음 이러한 밀도 행렬의 볼록 조합을 취하여 확률적 상태를 나타낼 수 있습니다. 단순화를 위해 우리의 고전적 상태 집합이 $\\{0, \ldots, n-1\\}$이라고 가정할 때, 각 $a \in \\{0, \ldots, n-1\\}$에 대해 $\mathsf{X}$가 확률 $p_a$로 상태 $a$에 있다면, 우리가 얻는 밀도 행렬은 다음과 같습니다. 

<p>$$
\rho = \sum_{a = 0}^{n-1} p_a \vert a\rangle \langle a \vert
= \begin{pmatrix}
p_0 & 0 & \cdots & 0\\
0 & p_1 & \ddots & \vdots\\
\vdots & \ddots & \ddots & 0\\
0 & \cdots & 0 & p_{n-1}
\end{pmatrix}.
$$</p>

반대 방향으로, 임의의 대각선 밀도 행렬은 대각선에서 확률 벡터를 단순히 읽어냄으로써 우리가 얻는 확률적 상태와 자연스럽게 동일시될 수 있습니다. 

분명히 하자면, 밀도 행렬이 대각 행렬일 때, 우리가 고전적 시스템에 대해 이야기하고 있다거나, 시스템이 고전적 상태의 무작위 선택을 통해 준비되었어야 한다는 것을 의미하는 것이 아니라, 오히려 그 상태가 고전적 상태의 무작위 선택을 통해 **얻어졌을 수도 있다**는 것을 의미합니다. 

확률적 상태가 대각선 밀도 행렬로 표현된다는 사실은, 이 레슨 시작 부분에서 제시된, 비대각선 엔트리가 해당 엔트리의 행과 열에 해당하는 두 고전적 상태가 양자 중첩되어 있는 정도를 설명한다는 직관과 일치합니다. 여기서는 모든 비대각선 엔트리가 0이므로, 우리는 단지 고전적인 무작위성을 가지고 있으며 아무것도 양자 중첩되어 있지 않습니다.

## 4. 밀도 행렬과 스펙트럼 정리
우리는 순수 상태의 볼록 조합을 취하면, 밀도 행렬을 얻는다는 것을 보았습니다. 

<p>$$
\rho = \sum_{k = 0}^{m-1} p_k \vert \psi_k\rangle \langle \psi_k \vert,
$$</p>

사실, 모든 밀도 행렬 $\rho$는 이와 같이 순수 상태의 볼록 조합으로 표현될 수 있습니다. 즉, 위의 방정식이 참이 되는 단위 벡터 $\{\vert\psi_0\rangle,\ldots,\vert\psi_{m-1}\rangle\}$의 모음과 확률 벡터 $(p_0,\ldots,p_{m-1})$가 항상 존재할 것입니다. 


더욱이, 우리는 항상 $m$의 수를 고려 중인 시스템의 고전적 상태 수와 일치하도록 선택할 수 있으며, 양자 상태 벡터를 직교하도록 선택할 수 있습니다. 우리가 "양자 알고리즘의 기초" 과정에서 접했던 스펙트럼 정리는 우리가 이것을 결론짓도록 허용합니다. 편의를 위해 스펙트럼 정리를 다시 설명합니다. 

> **정리. 스펙트럼 정리:** \
$M$이 *정규(normal)* $n \times n$ 복소 행렬이라고 합시다. 복소수 $\lambda_0, \ldots, \lambda_{n-1}$와 함께 $n$차원 복소 벡터의 정규 직교 기저 $\{\vert\psi_0\rangle,\ldots,\vert\psi_{n-1}\rangle\}$가 존재하며, 다음을 만족합니다. 
> <p>$$
  M = \lambda_0 \vert \psi_0\rangle\langle \psi_0\vert + \cdots + \lambda_{n-1} \vert \psi_{n-1}\rangle\langle \psi_{n-1}\vert.
  $$</p>

(행렬 $M$은 $M^{\dagger} M = M M^{\dagger}$를 만족하면 *정규*임을 상기하십시오. 즉, 정규 행렬은 자신의 켤레 전치와 교환되는 행렬입니다). 

밀도 행렬은 항상 에르미트 행렬이고 따라서 정규 행렬이므로, 우리는 임의의 주어진 밀도 행렬 $\rho$에 스펙트럼 정리를 적용할 수 있습니다. 이것은 우리가 다음과 같이 작성할 수 있게 합니다.

<p>$$
\rho = \lambda_0 \vert \psi_0\rangle\langle \psi_0\vert + \cdots + \lambda_{n-1} \vert \psi_{n-1}\rangle\langle \psi_{n-1}\vert
$$</p>

일부 정규 직교 기저 $\{\vert\psi_0\rangle,\ldots,\vert\psi_{n-1}\rangle\}$에 대해서입니다. 이제 $(\lambda_0,\ldots,\lambda_{n-1})$가 확률 벡터임을 검증하는 것이 남아 있으며, 원한다면 이를 $(p_0,\ldots,p_{n-1})$로 이름을 바꿀 수 있습니다. 

숫자 $\lambda_0, \ldots, \lambda_{n-1}$는 $\rho$의 고유값이며, $\rho$가 양의 준정부이므로 이 숫자들은 음수가 아닌 실수여야 합니다. $\rho$의 대각합이 1과 같다는 사실로부터 $\lambda_0 + \cdots + \lambda_{n-1} = 1$임을 결론내릴 수 있습니다. 세부 사항을 살펴보면 대각합의 다음과 같은 중요하고 매우 유용한 속성을 지적할 기회를 얻게 될 것입니다.

> **정리. 대각합의 순환 속성:** \
곱셈을 통해 정사각 행렬 $AB$를 제공하는 임의의 두 행렬 $A$와 $B$에 대해, $\operatorname{Tr}(AB) = \operatorname{Tr}(BA)$라는 등식은 참입니다. 

이 정리는 $A$와 $B$ 자체가 정사각 행렬이 아니더라도 작동한다는 점에 유의하십시오. 즉, 양의 정수 $n$과 $m$을 선택하여 $A$가 $n \times m$이고 $B$가 $m \times n$일 수 있으며, 이 경우 $AB$는 $n \times n$ 정사각 행렬이고 $BA$는 $m \times m$입니다. 

특히, $A$를 열 벡터 $\vert \phi\rangle$로, $B$를 행 벡터 $\langle \phi \vert$로 두면, 우리는 다음을 알 수 있습니다. 
<p>$$
\operatorname{Tr}\bigl(\vert\phi\rangle\langle\phi\vert\bigr)
= \operatorname{Tr}\bigl(\langle\phi\vert\phi\rangle\bigr) = \langle\phi\vert\phi\rangle.
$$</p>

두 번째 등식은 $\langle \phi \vert \phi \rangle$가 스칼라라는 사실에서 비롯되며, 이는 또한 대각합이 유일한 엔트리인 $1 \times 1$ 행렬로 생각할 수 있습니다. 이 사실을 사용하여, 우리는 대각합 함수의 선형성에 의해 $\lambda_0 + \cdots + \lambda_{n-1} = 1$임을 결론내릴 수 있습니다.

<p>$$
\begin{gathered}
1 = \operatorname{Tr}(\rho) =
\operatorname{Tr}\bigl(\lambda_0 \vert \psi_0\rangle\langle \psi_0\vert + \cdots + \lambda_{n-1} \vert \psi_{n-1}\rangle\langle \psi_{n-1}\vert\bigr)\\[2mm]
= \lambda_0 \operatorname{Tr}\bigl(\vert \psi_0\rangle\langle \psi_0\vert\bigr) + \cdots + \lambda_{n-1} \operatorname{Tr}\bigl(\vert \psi_{n-1}\rangle\langle \psi_{n-1}\vert\bigr)
= \lambda_0 + \cdots + \lambda_{n-1}
\end{gathered}
$$</p>

또는, (정규 행렬이 아닌 경우에도) 정사각 행렬의 대각합이 해당 고유값의 합과 같다는 사실을 사용하여 동일한 결론에 도달할 수 있습니다. 

따라서 우리는 임의의 주어진 밀도 행렬 $\rho$가 순수 상태의 볼록 조합으로 표현될 수 있다는 결론을 내렸습니다. 
우리는 또한 순수 상태를 **직교**하게 취할 수도 있음을 알 수 있습니다. 이는 특히, 우리가 $n$의 수가 $\mathsf{X}$의 고전적 상태 집합의 크기보다 클 필요가 없음을 의미합니다.

일반적으로, 밀도 행렬을 순수 상태의 볼록 조합으로 작성하는 데 스펙트럼 정리가 제공하는 방식뿐만 아니라 다른 방식도 있을 수 있음을 이해해야 합니다. 이전 예시가 이를 보여줍니다. 

<p>$$
\frac{1}{2} \vert 0\rangle\langle 0 \vert + \frac{1}{2} \vert +\rangle\langle + \vert
= \begin{pmatrix}
\frac{3}{4} & \frac{1}{4}\\[2mm]
\frac{1}{4} & \frac{1}{4}
\end{pmatrix}
$$</p>

이 행렬은 $\vert 0\rangle$와 $\vert {+}\rangle$가 직교하지 않기 때문에 이 행렬의 스펙트럼 분해가 아닙니다. 여기에 스펙트럼 분해가 있습니다. 

<p>$$
\begin{pmatrix}
\frac{3}{4} & \frac{1}{4}\\[2mm]
\frac{1}{4} & \frac{1}{4}
\end{pmatrix}
= \cos^2(\pi/8) \vert \psi_{\pi/8} \rangle \langle \psi_{\pi/8}\vert +
\sin^2(\pi/8) \vert \psi_{5\pi/8} \rangle \langle \psi_{5\pi/8}\vert,
$$</p>

여기서 $\vert \psi_{\theta} \rangle = \cos(\theta)\vert 0\rangle + \sin(\theta)\vert 1\rangle$입니다. 고유값은 아마도 친숙해 보일 숫자일 것입니다.

<p>$$
\cos^2(\pi/8) = \frac{2+\sqrt{2}}{4} \approx 0.85 \quad\text{and}\quad \sin^2(\pi/8) = \frac{2-\sqrt{2}}{4} \approx 0.15.
$$</p>

고유 벡터는 다음과 같이 명시적으로 작성될 수 있습니다.

<p>$$
\begin{aligned}
  \vert\psi_{\pi/8}\rangle & =
  \frac{\sqrt{2 + \sqrt{2}}}{2}\vert 0\rangle + \frac{\sqrt{2 - \sqrt{2}}}{2}\vert 1\rangle \\[3mm]
  \vert\psi_{5\pi/8}\rangle & =
  -\frac{\sqrt{2 - \sqrt{2}}}{2}\vert 0\rangle + \frac{\sqrt{2 + \sqrt{2}}}{2}\vert 1\rangle
\end{aligned}$$</p>

또 다른, 더 일반적인 예로, $\vert \phi_0\rangle, \ldots, \vert \phi_{99}\rangle$를 단일 큐비트의 상태를 나타내는 양자 상태 벡터라고 가정해 봅시다. 이 벡터들은 임의로 선택되었으므로, 우리는 이 벡터들 사이에 어떤 특정한 관계도 가정하지 않습니다. 그런 다음 우리는 이 100개의 상태 중 하나를 균일하게 무작위로 선택하여 얻는 상태를 고려할 수 있습니다. 

<p>$$
\rho = \frac{1}{100} \sum_{k = 0}^{99} \vert \phi_k\rangle\langle \phi_k \vert.$$</p>

우리가 큐비트에 대해 이야기하고 있기 때문에, 밀도 행렬 $\rho$는 $2 \times 2$입니다. 따라서 스펙트럼 정리에 의해 우리는 어떤 실수 $p\in[0,1]$와 정규 직교 기저 $\{\vert\psi_0\rangle, \vert\psi_1\rangle\}$에 대해 대안적으로 다음과 같이 작성할 수 있습니다. 

<p>$$
\rho = p \vert\psi_0\rangle\langle\psi_0\vert + (1 - p) \vert\psi_1\rangle\langle\psi_1\vert$$</p>

하지만 당연히 이 표현의 존재가 우리가 원한다면 $\rho$를 100개의 순수 상태의 평균으로 작성하는 것을 금지하지는 않습니다.

---

# **블로흐 구**

큐비트 상태를 나타내는 **블로흐 구(Bloch sphere)**라고 알려진 유용한 기하학적 방법이 있습니다. 이것은 매우 편리하지만, 불행하게도 큐비트에만 적용됩니다. 즉, 시스템이 세 개 이상의 고전적 상태를 가질 경우, 이와 유사한 표현은 더 이상 구형 객체에 해당하지 않습니다.

## 1. **구 위의 점으로서의 큐비트 상태**

큐비트의 양자 상태 벡터에 대해 생각해 보는 것으로 시작하겠습니다. $\alpha \vert 0\rangle + \beta \vert 1\rangle.$ 모든 큐비트 상태 벡터는 $\alpha \geq 0$인 벡터와 전역 위상(global phase)까지 동등하기 때문에, $\alpha$가 음이 아닌 실수인 벡터에만 우리의 관심을 한정할 수 있습니다. 이를 통해 다음과 같이 작성할 수 있습니다.

<p>$$
\vert\psi\rangle = \cos\bigl(\theta/2\bigr) \vert 0\rangle + e^{i\phi} \sin\bigl(\theta/2\bigr) \vert 1\rangle$$</p>

두 실수 $\theta \in [0, \pi]$와 $\phi \in [0, 2\pi)$에 대해. 여기서, 우리는 $\theta$가 $0$에서 $\pi$까지의 범위를 갖도록 하고, 사인과 코사인의 인수를 $2$로 나누고 있는데, 이는 이러한 유형의 벡터를 매개변수화하는 전통적인 방식이며, 나중에 상황을 좀 더 단순하게 만들 것입니다.

이제, 주어진 양자 상태 벡터에 의해 숫자 $\theta$와 $\phi$가 고유하게 결정되는 것은 아닙니다만, 거의 그렇습니다. 특히, $\beta = 0$인 경우, $\theta = 0$이며 $\phi$가 어떤 값을 취하든 상관없으므로, 임의로 선택될 수 있습니다. 마찬가지로, $\alpha = 0$인 경우, $\theta = \pi$이며, 다시 한 번 $\phi$는 무의미합니다 (왜냐하면 우리의 상태는 임의의 $\phi$에 대해 전역 위상까지 동등하기 때문입니다). 하지만 $\alpha$와 $\beta$ 모두 $0$이 아닌 경우에는, 전역 위상까지 동등하도록 만드는 쌍 $(\theta, \phi)$에 대한 고유한 선택이 있습니다.

다음으로, 이 상태의 밀도 행렬 표현을 고려해 봅시다.

<p>$$
\vert\psi\rangle\langle\psi\vert
= \begin{pmatrix}
\cos^2(\theta/2) & e^{-i\phi}\cos(\theta/2)\sin(\theta/2)\\[2mm]
e^{i\phi}\cos(\theta/2)\sin(\theta/2) & \sin^2(\theta/2)
\end{pmatrix}$$</p>

우리는 몇 가지 삼각 항등식과, 공식$e^{i\phi} = \cos(\phi) + i\sin(\phi)$을 사용하여 밀도 행렬을 다음과 같이 단순화할 수 있습니다.
<p>$$
\begin{gathered}
\cos^2(\theta/2) = \frac{1 + \cos(\theta)}{2},\\[2mm]
\sin^2(\theta/2) = \frac{1 - \cos(\theta)}{2},\\[2mm]
\cos(\theta/2) \sin(\theta/2) = \frac{\sin(\theta)}{2},
\end{gathered}$$</p>

이는 이 밀도 행렬을 파울리 행렬(Pauli matrices)의 선형 결합으로 표현하는 것을 쉽게 만듭니다. 파울리 행렬은 다음과 같습니다. 

<p>$$
  \mathbb{I} =
  \begin{pmatrix}
    1 & 0\\[1mm]
    0 & 1
  \end{pmatrix},
  \quad
  \sigma_x =
  \begin{pmatrix}
    0 & 1\\[1mm]
    1 & 0
  \end{pmatrix},
  \quad
  \sigma_y =
  \begin{pmatrix}
    0 & -i\\[1mm]
    i & 0
  \end{pmatrix},
  \quad
  \sigma_z =
  \begin{pmatrix}
    1 & 0\\[1mm]
    0 & -1
  \end{pmatrix}.$$</p>

특히, 우리는 다음을 결론짓습니다.
<p>$$
\vert\psi\rangle\langle\psi\vert
= \frac{\mathbb{I} + \sin(\theta) \cos(\phi)\sigma_x + \sin(\theta)\sin(\phi) \sigma_y + \cos(\theta) \sigma_z}{2}.$$</p>


이 표현의 분자에 있는 $\sigma_x$, $\sigma_y$, $\sigma_z$의 계수들은 모두 실수이므로, 우리는 이들을 모아 일반적인 3차원 유클리드 공간의 벡터를 형성할 수 있습니다. 

<p>$$
\bigl(\sin(\theta) \cos(\phi), \sin(\theta)\sin(\phi), \cos(\theta)\bigr)
$$</p>

사실, 이것은 단위 벡터입니다. **구면 좌표(spherical coordinates)**를 사용하면 이것은 $(1, \theta, \phi)$로 작성될 수 있습니다. 첫 번째 좌표인 $1$은 **반지름(radius)** 또는 **반지름 거리(radial distance)**(이 경우 항상 $1$입니다)를 나타내고, $\theta$는 **극각(polar angle)**을, $\phi$는 **방위각(azimuthal angle)**을 나타냅니다.

말로 설명하자면, 구를 지구(planet Earth)로 생각할 때, 극각 $\theta$는 북극에서 남쪽으로 회전하여 설명되는 지점에 도달하는 거리이며, $0$에서 $\pi = 180^{\circ}$까지의 범위를 갖습니다. 반면 방위각 $\phi$는 본초 자오선에서 동쪽으로 회전하는 거리이며, $0$에서 $2\pi = 360^{\circ}$까지의 범위를 갖습니다. 이는 우리가 본초 자오선을 양의 $x$축을 통과하는 한 극에서 다른 극까지 구 표면상의 곡선으로 정의한다고 가정합니다.

<img src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fgeneral-formulation-of-quantum-information%2Fdensity-matrices%2Fspherical-coordinates.avif&w=3840&q=75" style="width:70%">

구 위의 모든 점은 이러한 방식으로 설명될 수 있습니다. 다시 말해, 큐비트의 가능한 모든 순수 상태(pure states)를 아우를 때 우리가 얻는 점들은 3개의 실수 차원 공간에서 정확히 하나의 구에 해당합니다. (이 구의 표면이 2차원이기 때문에 이 구는 일반적으로 **단위 2-구(unit 2-sphere)**라고 불립니다). 

단위 2-구 위의 점들을 큐비트의 순수 상태와 연관시킬 때, 우리는 이 상태들의 **블로흐 구** 표현을 얻습니다.

## 2. **여섯 가지 중요한 예시**

1\. **표준 기저** $$\left\{\vert 0\rangle, \vert 1\rangle\right\}$$. 상태 $\vert 0\rangle$부터 시작해 보겠습니다. 밀도 행렬로서는 다음과 같이 작성될 수 있습니다.

<p>$$
    \vert 0 \rangle \langle 0 \vert = \frac{\mathbb{I} + \sigma_z}{2}$$</p>

분자에서 파울리 행렬의 계수들을 모으면, 카르테시안 좌표(Cartesian coordinates)를 사용하여 단위 2-구에 해당하는 점이 $(0, 0, 1)$임을 알 수 있습니다. 구면 좌표에서 이 점은 $(1, 0, \phi)$이며, 여기서 $\phi$는 임의의 각도가 될 수 있습니다. 이는 다음 표현과 일치합니다.

<p>$$
    \vert 0\rangle = \cos(0) \vert 0\rangle + e^{i \phi} \sin(0) \vert 1\rangle,$$</p>

이는 또한 임의의 $\phi$에 대해 작동합니다. 직관적으로 말하자면, 극각 $\theta$는 $0$이므로, 우리는 블로흐 구의 북극에 있으며, 여기서 방위각은 무의미합니다. 이와 비슷한 맥락으로, 상태 $\vert 1\rangle$에 대한 밀도 행렬은 다음과 같이 작성될 수 있습니다.

<p>$$
    \vert 1 \rangle \langle 1 \vert = \frac{\mathbb{I} - \sigma_z}{2}$$</p>

이번에는 카르테시안 좌표가 $(0, 0, -1)$입니다. 구면 좌표에서 이 점은 $(1, \pi, \phi)$이며, 여기서 $\phi$는 임의의 각도가 될 수 있습니다. 이 경우 극각은 $\pi$까지 완전히 도달하므로, 우리는 남극에 있으며, 여기서 방위각은 다시 무의미합니다.

2\. **기저** $$\left\{\vert +\rangle, \vert -\rangle\right\}$$. 이 상태들에 해당하는 밀도 행렬에 대한 표현은 다음과 같습니다.

<p>$$
    \begin{aligned}
    \vert {+} \rangle\langle {+} \vert & = \frac{\mathbb{I} + \sigma_x}{2}\\[2mm]
    \vert {-} \rangle\langle {-} \vert & = \frac{\mathbb{I} - \sigma_x}{2}
    \end{aligned}$$</p>

단위 2-구 위의 해당하는 점들은 각각 카르테시안 좌표 $(1, 0, 0)$과 $(-1, 0, 0)$을 가지며, 구면 좌표 $(1, \pi/2, 0)$와 $(1, \pi/2, \pi)$를 가집니다.

말로 설명하자면, $\vert +\rangle$는 양의 $x$축이 단위 2-구와 교차하는 지점에 해당하고, $\vert -\rangle$는 음의 $x$축이 교차하는 지점에 해당합니다. 더 직관적으로, $\vert +\rangle$는 본초 자오선과 만나는 블로흐 구의 적도 위에 있고, $\vert -\rangle$는 구의 반대편 적도 위에 있습니다.

3\. **기저** $$\left\{\vert +i\rangle, \vert -i\rangle\right\}$$. 이 강의의 앞부분을 포함하여 이전에 이 시리즈에서 보았듯이, 이 두 상태는 다음과 같이 정의됩니다.

<p>$$
    \begin{aligned}
    \vert {+i} \rangle & = \frac{1}{\sqrt{2}} \vert 0 \rangle + \frac{i}{\sqrt{2}} \vert 1 \rangle\\[2mm]
    \vert {-i} \rangle & = \frac{1}{\sqrt{2}} \vert 0 \rangle - \frac{i}{\sqrt{2}} \vert 1 \rangle.
    \end{aligned}$$</p>

이번에는 다음과 같은 표현을 가집니다.

<p>$$
    \begin{aligned}
    \vert {+i} \rangle\langle {+i} \vert & = \frac{\mathbb{I} + \sigma_y}{2}\\[2mm]
    \vert {-i} \rangle\langle {-i} \vert & = \frac{\mathbb{I} - \sigma_y}{2}
    \end{aligned}$$</p>

단위 2-구 위의 해당하는 점들은 각각 카르테시안 좌표 $(0, 1, 0)$와 $(0, -1, 0)$을 가지며, 구면 좌표 $(1, \pi/2, \pi/2)$와 $(1, \pi/2, 3\pi/2)$를 가집니다. 

말로 설명하자면, $\vert +i\rangle$는 양의 $y$축이 단위 2-구와 교차하는 지점에 해당하고, $\vert -i\rangle$는 음의 $y$축이 교차하는 지점에 해당합니다.

<img src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fgeneral-formulation-of-quantum-information%2Fdensity-matrices%2Fsix-Bloch-sphere-points.avif&w=3840&q=75" style="width:70%">

다음은 이 시리즈 전체에서, 그리고 이 강의의 이전 부분에서도 때때로 등장했던 또 다른 종류의 양자 상태 벡터입니다.

<p>$$
\vert \psi_{\alpha} \rangle = \cos(\alpha) \vert 0\rangle + \sin(\alpha) \vert 1\rangle \qquad \text{(for $\alpha \in [0,\pi)$)}$$</p>

이 상태들 각각의 밀도 행렬 표현은 다음과 같습니다.
<p>$$
\vert \psi_{\alpha} \rangle \langle \psi_{\alpha} \vert =
\begin{pmatrix}
\cos^2(\alpha) & \cos(\alpha)\sin(\alpha)\\[2mm]
\cos(\alpha)\sin(\alpha) & \sin^2(\alpha)
\end{pmatrix}
= \frac{\mathbb{I} + \sin(2\alpha) \sigma_x + \cos(2\alpha) \sigma_z}{2}$$</p>

다음 그림은 $\alpha$의 몇 가지 선택에 대한 블로흐 구 위의 해당 점들을 보여줍니다.
<img src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fgeneral-formulation-of-quantum-information%2Fdensity-matrices%2Freal-Bloch-sphere-points.avif&w=3840&q=75" style="width:70%">

## 3. **점의 볼록 조합(Convex combinations of points)**

밀도 행렬에 대해 이미 논의했던 것과 유사하게, 우리는 큐비트 밀도 행렬의 표현을 얻기 위해 블로흐 구 위의 점들의 볼록 조합을 취할 수 있습니다. 일반적으로, 이는 **블로흐 구 *안쪽***의 점들을 초래하며, 이는 순수 상태가 아닌 상태의 밀도 행렬을 나타냅니다. 때때로 우리는 큐비트 밀도 행렬의 표현으로서 블로흐 구 안쪽의 점들의 포함을 명시적으로 밝히고자 할 때 **블로흐 볼(Bloch ball)**이라고 언급하기도 합니다.

예를 들어, 큐비트의 완전히 혼합된 상태(completely mixed state)를 나타내는 밀도 행렬을 다음과 같은 두 가지 대체 방식으로 작성할 수 있음을 보았습니다.

<p>$$
\frac{1}{2} \mathbb{I} = \frac{1}{2} \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert 1\rangle\langle 1\vert
\quad\text{and}\quad
\frac{1}{2} \mathbb{I} = \frac{1}{2} \vert +\rangle\langle +\vert + \frac{1}{2} \vert -\rangle\langle -\vert.$$</p>

우리는 또한 다음을 가집니다.

<p>$$
\frac{1}{2} \mathbb{I} =
\frac{1}{2} \vert {+i}\rangle\langle {+i} \vert
+ \frac{1}{2} \vert {-i} \rangle\langle {-i}\vert,$$</p>

그리고 더 일반적으로, 우리는 임의의 두 직교 큐비트 상태 벡터(이들은 항상 블로흐 구 위의 두 대척점(antipodal points)에 해당합니다)를 사용할 수 있습니다. 유사한 방식으로 블로흐 구 위의 해당 점들을 평균하면, 이 경우 구의 중심에 있는 동일한 점을 얻게 됩니다. 이는 다음 관찰과 일치합니다.

<p>$$
\frac{1}{2} \mathbb{I} = \frac{\mathbb{I} + 0 \cdot \sigma_x + 0 \cdot \sigma_y + 0 \cdot \sigma_z}{2},$$</p>

이는 우리에게 카르테시안 좌표 $(0, 0, 0)$을 제공합니다. 블로흐 구 점들의 볼록 조합에 관한 다른 예시는 이전 하위 섹션에서 논의된 것입니다.

<p>$$
\frac{1}{2} \vert 0\rangle\langle 0 \vert + \frac{1}{2} \vert +\rangle\langle + \vert
= \begin{pmatrix} \frac{3}{4} & \frac{1}{4}\\[2mm]
\frac{1}{4} & \frac{1}{4}
\end{pmatrix}
= \cos^2(\pi/8) \vert \psi_{\pi/8} \rangle \langle \psi_{\pi/8}\vert +
\sin^2(\pi/8) \vert \psi_{5\pi/8} \rangle \langle \psi_{5\pi/8}\vert$$</p>

다음 그림은 이 밀도 행렬을 순수 상태의 볼록 조합으로 얻는 두 가지 다른 방식을 보여줍니다.

<img src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fgeneral-formulation-of-quantum-information%2Fdensity-matrices%2FBloch-sphere-zero-plus-average.avif&w=3840&q=75" style="width:70%">

---

# 다중 시스템 및 축소 상태
이제 밀도 행렬이 **다중 시스템**에 대해 어떻게 작동하는지, 이들이 표현할 수 있는 다양한 유형의 상관관계의 예시를 포함하여, 그리고 이들이 복합 시스템의 고립된 부분의 상태를 설명하는 데 어떻게 사용될 수 있는지에 대해 주의를 돌릴 것입니다.

## 1. 다중 시스템 (Multiple systems)
밀도 행렬은 양자 정보의 단순화된 공식에서 상태 벡터와 유사한 방식으로 **다중 시스템의 상태**를 나타낼 수 있으며, 다중 시스템이 단일의 **복합 시스템**처럼 보일 수 있다는 동일한 기본 아이디어를 따릅니다. 수학적 용어로, 다중 시스템의 상태를 나타내는 밀도 행렬의 행과 열은 개별 시스템의 **고전적 상태 집합의 데카르트 곱(Cartesian product)**과 일치하게 배치됩니다.

예를 들어, 네 가지 벨 상태의 상태 벡터 표현을 상기하십시오.

<p>$$
\begin{aligned}
\vert \phi^+ \rangle & = \frac{1}{\sqrt{2}} \vert 00 \rangle + \frac{1}{\sqrt{2}} \vert 11 \rangle \\[2mm]
\vert \phi^- \rangle & = \frac{1}{\sqrt{2}} \vert 00 \rangle - \frac{1}{\sqrt{2}} \vert 11 \rangle \\[2mm]
\vert \psi^+ \rangle & = \frac{1}{\sqrt{2}} \vert 01 \rangle + \frac{1}{\sqrt{2}} \vert 10 \rangle \\[2mm]
\vert \psi^- \rangle & = \frac{1}{\sqrt{2}} \vert 01 \rangle - \frac{1}{\sqrt{2}} \vert 10 \rangle
\end{aligned}
$$</p>

이러한 상태의 **밀도 행렬 표현**은 다음과 같습니다.

<p>$$
\vert \phi^+ \rangle \langle \phi^+ \vert = \begin{pmatrix} \frac{1}{2} & 0 & 0 & \frac{1}{2}\\[2mm] 0 & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0\\[2mm] \frac{1}{2} & 0 & 0 & \frac{1}{2} \end{pmatrix}$$</p>
<p>$$\vert \phi^- \rangle \langle \phi^- \vert = \begin{pmatrix} \frac{1}{2} & 0 & 0 & -\frac{1}{2}\\[2mm] 0 & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0\\[2mm] -\frac{1}{2} & 0 & 0 & \frac{1}{2} \end{pmatrix}$$</p>
<p>$$\vert \psi^+ \rangle \langle \psi^+ \vert = \begin{pmatrix} 0 & 0 & 0 & 0\\[2mm] 0 & \frac{1}{2} & \frac{1}{2} & 0\\[2mm] 0 & \frac{1}{2} & \frac{1}{2} & 0\\[2mm] 0 & 0 & 0 & 0 \end{pmatrix}$$</p> 
<p>$$\vert \psi^- \rangle \langle \psi^- \vert = \begin{pmatrix} 0 & 0 & 0 & 0\\[2mm] 0 & \frac{1}{2} & -\frac{1}{2} & 0\\[2mm] 0 & -\frac{1}{2} & \frac{1}{2} & 0\\[2mm] 0 & 0 & 0 & 0 \end{pmatrix}
$$</p>

### 1.1 곱 상태 (Product states)
상태 벡터에서와 유사하게, **밀도 행렬의 텐서 곱(tensor products)**은 다중 시스템 상태 간의 ***독립성(independence)***을 나타냅니다. 예를 들어, $\mathsf{X}$가 밀도 행렬 $\rho$로 표현되는 상태로 준비되고 $\mathsf{Y}$가 독립적으로 $\sigma$로 표현되는 상태로 준비되는 경우, $(\mathsf{X},\mathsf{Y})$의 상태를 설명하는 밀도 행렬은 텐서 곱 $\rho\otimes\sigma$입니다.

양자 정보의 단순화된 공식에서와 동일한 용어가 여기에서 사용됩니다. 이러한 형태의 상태를 ***곱 상태(product states)***라고 합니다.

### 1.2 상관된 및 얽힌 상태 (Correlated and entangled states)
곱 상태로 표현될 수 없는 상태는 시스템 간의 ***상관관계(correlations)***를 나타냅니다. 실제로 밀도 행렬로 표현될 수 있는 다양한 유형의 상관관계가 있습니다. 몇 가지 예시는 다음과 같습니다.

1\. *상관된 고전적 상태 (Correlated classical states).* \
예를 들어, 앨리스와 밥이 무작위 비트(random bit)를 공유하는 상황을 다음과 같이 표현할 수 있습니다.

<p>$$\frac{1}{2} \vert 0 \rangle \langle 0 \vert \otimes \vert 0 \rangle \langle 0 \vert + \frac{1}{2} \vert 1 \rangle \langle 1 \vert \otimes \vert 1 \rangle \langle 1 \vert = \begin{pmatrix} \frac{1}{2} & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & \frac{1}{2} \end{pmatrix}$$</p>

2\. *양자 상태 앙상블 (Ensembles of quantum states).* \
시스템 $\mathsf{X}$의 상태를 모두 나타내는 $m$개의 밀도 행렬 $\rho_0,\ldots,\rho_{m-1}$이 있고, 확률 벡터 $(p_0,\ldots,p_{m-1})$에 따라 이러한 상태 중 하나를 무작위로 선택한다고 가정해 봅시다. 이러한 프로세스는 상태의 ***앙상블(ensemble)***로 표현되며, 여기에는 밀도 행렬 $\rho_0,\ldots,\rho_{m-1}$의 사양뿐만 아니라 확률 $(p_0,\ldots,p_{m-1})$도 포함됩니다. 우리는 $k$의 무작위 선택과 해당 밀도 행렬 $\rho_k$를 모두 설명하는 단일 밀도 행렬에 상태 앙상블을 연결할 수 있으며, 이는 다음과 같습니다.

<p>$$\sum_{k = 0}^{m-1} p_k \vert k\rangle \langle k \vert \otimes \rho_k$$</p>

명확히 하자면, 이것은 $k$의 고전적 선택을 나타내는 $\mathsf{Y}$를 가진 쌍 $(\mathsf{Y},\mathsf{X})$의 상태입니다. 따라서 우리는 $\mathsf{Y}$의 고전적 상태 집합이 $\\{0,\ldots,m-1\\}$이라고 가정하고 있습니다. 이러한 형태의 상태는 때때로 ***고전-양자 상태(classical-quantum states)***라고 불립니다.

3\. *분리 가능한 상태 (Separable states)*. \
우리는 다음과 같이 두 시스템의 양자 상태 간에 고전적인 상관관계가 있는 상황을 상상할 수 있습니다.

<p>$$\sum_{k = 0}^{m-1} p_k \rho_k \otimes \sigma_k$$</p>

즉, $0$에서 $m-1$까지의 각 $k$에 대해, 확률 $p_k$로 왼쪽 시스템은 상태 $\rho_k$에 있고 오른쪽 시스템은 상태 $\sigma_k$에 있습니다. 이와 같은 상태를 ***분리 가능한 상태(separable states)***라고 부릅니다. 이 개념은 또한 두 개 이상의 시스템으로 확장될 수 있습니다.

4\. *얽힌 상태 (Entangled states)*. \
모든 쌍 시스템의 상태가 분리 가능한 것은 아닙니다. 양자 정보의 일반적인 공식에서, **얽힘(entanglement)**은 다음과 같이 정의됩니다: **분리 가능하지 않은 상태**는 ***얽힌(entangled)*** 상태라고 합니다.

이 용어는 "양자 정보의 기초" 과정에서 사용했던 용어와 일치한다는 점에 유의하십시오. 거기서 우리는 곱 상태가 아닌 양자 상태 벡터는 얽힌 상태를 나타낸다고 말했습니다. 실제로 곱 상태가 아닌 모든 양자 상태 벡터 $\|\psi\rangle$에 대해, 밀도 행렬 $\|\psi\rangle\langle\psi\|$로 표현되는 상태는 분리 가능하지 않음(not separable)을 알 수 있습니다. **순수하지 않은 상태**의 경우, 얽힘은 이보다 훨씬 더 복잡합니다.

## 2. 축소 상태 및 부분 대각합 (Reduced states and the partial trace)
다중 시스템의 맥락에서 밀도 행렬로 할 수 있는 간단하지만 중요한 일은, 일부 시스템을 **무시함으로써 얻는 상태**를 설명하는 것입니다. 여러 시스템이 양자 상태에 있고 우리가 그 시스템들 중 하나 이상을 폐기하거나 무시하기로 선택할 때, 남아있는 시스템의 상태는 해당 시스템의 ***축소 상태(reduced state)***라고 불립니다. 축소 상태에 대한 밀도 행렬 설명은 **전체 상태**를 설명하는 밀도 행렬로부터 ***부분 대각합(partial trace)***으로 알려진 매핑을 통해 쉽게 얻을 수 있습니다.

### 2.1 예시: e-bit에 대한 축소 상태 (Example: reduced states for an e-bit)
우리는 함께 상태
$$\vert\phi^+\rangle = \frac{1}{\sqrt{2}} \vert 00 \rangle + \frac{1}{\sqrt{2}} \vert 11 \rangle$$
에 있는 한 쌍의 큐비트 $(\mathsf{A},\mathsf{B})$를 가지고 있다고 가정해 봅시다. 

우리는 앨리스가 큐비트 $\mathsf{A}$를 가지고 있고 밥이 $\mathsf{B}$를 가지고 있다고 상상할 수 있는데, 이는 그들이 함께 e-bit를 공유하고 있다는 것을 의미합니다. 밥이 자신의 큐비트를 가지고 별을 방문하여 다시는 볼 수 없게 된 것처럼, **앨리스의 큐비트 $\mathsf{A}$의 고립된 상태**에 대한 밀도 행렬 설명을 갖고 싶습니다.

먼저, 밥이 여행 중 어딘가에서 **표준 기저 측정**을 사용하여 자신의 큐비트를 측정하기로 결정한다면 어떤 일이 일어날지 생각해 봅시다. 그가 이렇게 한다면, 그는 $\frac{1}{2}$의 확률로 결과 $0$을 얻게 되며, 

<p>$$
\bigl\| \bigl( \mathbb{I}_{\mathsf{A}} \otimes \langle 0\vert \bigr) \vert \phi^+ \rangle \bigr\|^2 = \Bigl\| \frac{1}{\sqrt{2}} \vert 0 \rangle \Bigr\|^2 = \frac{1}{2}
$$</p>

이 경우 **앨리스 큐비트의 상태**는 $\vert 0\rangle$이 됩니다. 그리고 그는 $\frac{1}{2}$의 확률로 결과 $1$을 얻게 됩니다.

<p>$$
\bigl\| \bigl( \mathbb{I}_{\mathsf{A}} \otimes \langle 1\vert \bigr) \vert \phi^+ \rangle \bigr\|^2 = \Bigl\| \frac{1}{\sqrt{2}} \vert 1 \rangle \Bigr\|^2 = \frac{1}{2}
$$</p>

이 경우 앨리스 큐비트의 상태는 $\vert 1\rangle$이 됩니다. 

따라서, 우리가 밥의 측정 결과를 무시하고 앨리스의 큐비트에 초점을 맞춘다면, 그녀는 확률 $1/2$로 상태 $\vert 0\rangle$를 얻고 확률 $1/2$로 상태 $\vert 1\rangle$를 얻는다고 결론 내립니다. 이는 고립된 앨리스의 큐비트 상태를 밀도 행렬로 설명하게 합니다.

<p>$$\frac{1}{2} \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert 1\rangle\langle 1\vert = \frac{1}{2} \mathbb{I}_{\mathsf{A}}$$</p>


즉, 앨리스의 큐비트는 **완전히 혼합된 상태(completely mixed state)**에 있습니다. 명확히 하자면, 앨리스 큐비트의 상태에 대한 이 설명은 밥의 측정 결과를 포함하지 않습니다. 우리는 밥을 완전히 무시하고 있습니다. 

이제, 우리가 방금 얻은 고립된 앨리스 큐비트의 밀도 행렬 설명이 밥이 자신의 큐비트를 측정했다는 가정에 의존하는 것처럼 보일 수 있지만, 실제로는 그렇지 않습니다. 우리가 한 일은, 밥이 자신의 큐비트를 측정할 가능성을 사용하여, 우리가 이미 배운 내용을 바탕으로 **완전히 혼합된 상태**가 앨리스 큐비트의 상태로 발생한다고 주장하는 것입니다. 물론, 밥이 자신의 큐비트를 측정해야 한다고 말하는 것은 없지만, 그가 측정하지 않는다고 말하는 것도 없습니다. 그리고 그가 광년(light years) 떨어진 곳에 있다면, 그가 하거나 하지 않는 어떤 것도 고립된 상태로 보이는 앨리스 큐비트의 상태에 영향을 미칠 수 없습니다. 즉, 우리가 앨리스 큐비트 상태에 대해 얻은 설명은 **초광속 통신(faster-than-light communication)의 불가능성**과 일치하는 유일한 설명입니다.

우리는 밥의 큐비트 $\mathsf{B}$의 상태도 고려할 수 있으며, 이 역시 **완전히 혼합된 상태**입니다. 실제로, 네 가지 벨 상태 모두에 대해 앨리스 큐비트와 밥 큐비트의 **축소 상태**가 **완전히 혼합된 상태**임을 알 수 있습니다.

### 2.2 일반 양자 상태 벡터에 대한 축소 상태 (Reduced states for a general quantum state vector)
이제 방금 논의한 예시를 임의의 두 시스템 $\mathsf{A}$와 $\mathsf{B}$로 일반화해 봅시다. 이들은 반드시 상태 $\vert \phi^+\rangle$에 있는 큐비트일 필요는 없습니다. 우리는 $\mathsf{A}$와 $\mathsf{B}$의 고전적 상태 집합이 각각 $\Sigma$와 $\Gamma$라고 가정할 것입니다. 따라서 결합된 시스템 $(\mathsf{A},\mathsf{B})$의 상태를 나타내는 밀도 행렬 $\rho$는 데카르트 곱 $\Sigma\times\Gamma$에 해당하는 행 및 열 인덱스를 가집니다.

$(\mathsf{A},\mathsf{B})$의 상태가 양자 상태 벡터 $\vert\psi\rangle$로 설명된다고 가정하면, 이 상태를 설명하는 밀도 행렬은 $\rho = \vert\psi\rangle\langle\psi\vert$입니다. 우리는 관례적으로 $\rho_{\mathsf{A}}$로 표기되는 고립된 $\mathsf{A}$의 상태에 대한 밀도 행렬 설명을 얻을 것입니다. (때로는 아래 첨자 대신 위 첨자도 사용됩니다.)


상태 벡터 $\vert\psi\rangle$는 고유하게 결정된 벡터 컬렉션 $\{\vert\phi_b\rangle : b\in\Gamma\}$에 대해 다음과 같은 형태로 표현될 수 있습니다.
<p>$$\vert\psi\rangle = \sum_{b\in\Gamma} \vert\phi_b\rangle \otimes \vert b\rangle$$</p>

특히, 이 벡터들은 간단한 공식을 통해 결정될 수 있습니다.
<p>$$\vert\phi_b\rangle = \bigl(\mathbb{I}_{\mathsf{A}} \otimes \langle b\vert\bigr)\vert\psi\rangle$$</p>

e-bit의 이전 예시와 유사하게 추론하면, 시스템 $\mathsf{B}$를 표준 기저 측정으로 측정할 경우, 우리는 확률 $\|\vert\phi_b\rangle\|^2$로 각 결과 $b\in\Gamma$를 얻게 되며, 이 경우 $\mathsf{A}$의 상태는
<p>$$\frac{\vert \phi_b \rangle}{\|\vert\phi_b\rangle\|}$$</p>
이 됩니다. 

밀도 행렬로서, 이 상태는 다음과 같이 작성될 수 있습니다.
<p>$$\biggl(\frac{\vert \phi_b \rangle}{\|\vert\phi_b\rangle\|}\biggr) \biggl(\frac{\vert \phi_b \rangle}{\|\vert\phi_b\rangle\|}\biggr)^{\dagger} = \frac{\vert \phi_b \rangle\langle\phi_b\vert}{\|\vert\phi_b\rangle\|^2}$$</p>

각 결과의 확률에 따라 다른 상태들을 평균화하면, 우리는 밀도 행렬

<p>$$\rho_{\mathsf{A}} = \sum_{b\in\Gamma} \|\vert\phi_b\rangle\|^2 \frac{\vert \phi_b \rangle\langle\phi_b\vert}{\|\vert\phi_b\rangle\|^2} = \sum_{b\in\Gamma} \vert \phi_b \rangle\langle\phi_b\vert = \sum_{b\in\Gamma} \bigl(\mathbb{I}_{\mathsf{A}} \otimes \langle b\vert\bigr) \vert\psi\rangle\langle\psi\vert \bigl(\mathbb{I}_{\mathsf{A}} \otimes \vert b\rangle\bigr)$$</p>
에 도달합니다.

### 2.4 부분 대각합 (The partial trace)
공식

<p>$$\rho_{\mathsf{A}} = \sum_{b\in\Gamma} \bigl(\mathbb{I}_{\mathsf{A}} \otimes \langle b\vert\bigr) \vert\psi\rangle\langle\psi\vert \bigl(\mathbb{I}_{\mathsf{A}} \otimes \vert b\rangle\bigr)$$</p>

는 순수 상태뿐만 아니라 쌍 $(\mathsf{A},\mathsf{B})$의 모든 밀도 행렬 $\rho$에 대해 $\mathsf{A}$의 축소 상태에 대한 설명으로 이어집니다.

<p>$$\rho_{\mathsf{A}} = \sum_{b\in\Gamma} \bigl( \mathbb{I}_{\mathsf{A}} \otimes \langle b \vert\bigr) \rho \bigl( \mathbb{I}_{\mathsf{A}} \otimes \vert b \rangle\bigr)$$</p>

모든 밀도 행렬이 순수 상태의 **볼록 조합(convex combination)**으로 작성될 수 있다는 사실과 함께 **선형성(linearity)**에 의해 이 공식은 작동해야 합니다. 

이 방정식에서 $\rho$에 수행되어 $\rho_{\mathsf{A}}$를 얻는 연산은 ***부분 대각합(partial trace)***으로 알려져 있으며, 더 정확하게는 $\mathsf{B}$에 대해 부분 대각합이 수행되거나 $\mathsf{B}$가 ***소거(traced out)***된다고 말합니다. 이 연산은 $\operatorname{Tr}_{\mathsf{B}}$로 표기되므로, 우리는 다음과 같이 작성할 수 있습니다.

<p>$$\operatorname{Tr}_{\mathsf{B}} (\rho) = \sum_{b\in\Gamma} \bigl( \mathbb{I}_{\mathsf{A}} \otimes \langle b \vert\bigr) \rho \bigl( \mathbb{I}_{\mathsf{A}} \otimes \vert b \rangle\bigr)$$</p>

우리는 $\mathsf{A}$에 대한 부분 대각합도 정의할 수 있으며, 이 경우 $\mathsf{B}$가 아닌 시스템 $\mathsf{A}$가 소거됩니다. 이는 다음과 같습니다.

<p>$$\operatorname{Tr}_{\mathsf{A}} (\rho) = \sum_{a\in\Sigma} \bigl(\langle a \vert\otimes\mathbb{I}_{\mathsf{B}}\bigr) \rho \bigl(\vert a \rangle\otimes\mathbb{I}_{\mathsf{B}}\bigr)$$</p>

이는 $\mathsf{A}$가 아닌 고립된 $\mathsf{B}$의 상태에 대한 밀도 행렬 설명 $\rho_{\mathsf{B}}$를 제공합니다. 

요약하자면, $(\mathsf{A},\mathsf{B})$가 임의의 시스템 쌍이고 우리가 $(\mathsf{A},\mathsf{B})$의 상태를 설명하는 밀도 행렬 $\rho$를 가지고 있다면, 시스템 $\mathsf{A}$와 $\mathsf{B}$의 ***축소 상태(reduced states)***는 다음과 같습니다.

<p>$$
\begin{aligned}
\rho_{\mathsf{A}} & = \operatorname{Tr}_{\mathsf{B}}(\rho) = \sum_{b\in\Gamma} \bigl( \mathbb{I}_{\mathsf{A}} \otimes \langle b \vert\bigr) \rho \bigl( \mathbb{I}_{\mathsf{A}} \otimes \vert b \rangle\bigr)\\[2mm]
\rho_{\mathsf{B}} & = \operatorname{Tr}_{\mathsf{A}}(\rho) = \sum_{a\in\Sigma} \bigl( \langle a \vert \otimes \mathbb{I}_{\mathsf{B}}\bigr) \rho \bigl( \vert a \rangle\otimes \mathbb{I}_{\mathsf{B}} \bigr)
\end{aligned}
$$</p>

$\rho$가 밀도 행렬이라면, $\rho_{\mathsf{A}}$와 $\rho_{\mathsf{B}}$도 필연적으로 밀도 행렬이 될 것입니다. 

이러한 개념은 두 개 대신 **임의의 수의 시스템**으로 자연스러운 방식으로 일반화될 수 있습니다. 일반적으로, 우리는 밀도 행렬 $\rho$의 아래 첨자에 원하는 시스템의 이름을 넣어 해당 시스템만의 축소 상태를 설명할 수 있습니다. 예를 들어, $\mathsf{A}$, $\mathsf{B}$, $\mathsf{C}$가 시스템이고 $\rho$가 $(\mathsf{A},\mathsf{B},\mathsf{C})$의 상태를 설명하는 밀도 행렬이라면, 우리는 다음과 같이 정의할 수 있습니다.

<p>
$$
\begin{aligned}
\rho_{\mathsf{AC}} & = \operatorname{Tr}_{\mathsf{B}}(\rho) = \sum_{b\in\Gamma} \bigl( \mathbb{I}_{\mathsf{A}} \otimes \langle b \vert \otimes \mathbb{I}_{\mathsf{C}} \bigr) \rho \bigl( \mathbb{I}_{\mathsf{A}} \otimes \vert b \rangle \otimes \mathbb{I}_{\mathsf{C}} \bigr) \\[2mm]
\rho_{\mathsf{C}} & = \operatorname{Tr}_{\mathsf{AB}}(\rho) = \sum_{a\in\Sigma} \sum_{b\in\Gamma} \bigl( \langle a \vert \otimes \langle b \vert \otimes \mathbb{I}_{\mathsf{C}} \bigr) \rho \bigl( \vert a \rangle \otimes \vert b \rangle \otimes \mathbb{I}_{\mathsf{C}} \bigr)
\end{aligned}
$$</p>

그리고 시스템의 다른 선택에 대해서도 유사합니다.

### 2.5 부분 대각합의 대체 설명 (Alternative description of the partial trace)
부분 대각합 매핑 $$\operatorname{Tr}_{\mathsf{A}}$$와 $$\operatorname{Tr}_{\mathsf{B}}$$를 설명하는 대체 방법은, 이들이 다음 공식
<p>$$
\begin{aligned}
\operatorname{Tr}_{\mathsf{A}}(M \otimes N) & = \operatorname{Tr}(M) N \\[2mm]
\operatorname{Tr}_{\mathsf{B}}(M \otimes N) & = \operatorname{Tr}(N) M.
\end{aligned}
$$</p>
을 만족하는 ***고유한(unique)*** 선형 매핑이라는 것입니다.

이 공식에서 $N$과 $M$은 적절한 크기의 **정사각 행렬**입니다. $M$의 행과 열은 $\mathsf{A}$의 고전적 상태에 해당하고 $N$의 행과 열은 $\mathsf{B}$의 고전적 상태에 해당합니다. 

부분 대각합에 대한 이 특성화는 수학적 관점에서 근본적일 뿐만 아니라, 일부 상황에서 빠른 계산을 가능하게 할 수도 있습니다. 예를 들어, 한 쌍의 큐비트 $(\mathsf{A},\mathsf{B})$의 이 상태를 고려해 봅시다.

<p>$$\rho = \frac{1}{2} \vert 0\rangle\langle 0\vert \otimes \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert 1\rangle\langle 1\vert \otimes \vert +\rangle\langle +\vert$$</p>

예를 들어, **축소 상태 $\rho_{\mathsf{A}}$**를 계산하기 위해, 우리는 선형성과 $\vert 0\rangle\langle 0\vert$ 및 $\vert +\rangle\langle +\vert$가 **단위 대각합(unit trace)**을 가진다는 사실을 함께 사용할 수 있습니다.

<p>$$
\rho_{\mathsf{A}} = \operatorname{Tr}_{\mathsf{B}}(\rho) = \frac{1}{2} \operatorname{Tr}\bigl(\vert 0\rangle\langle 0\vert\bigr)\, \vert 0\rangle\langle 0\vert + \frac{1}{2} \operatorname{Tr}\bigl(\vert +\rangle\langle +\vert\bigr) \vert 1\rangle\langle 1\vert = \frac{1}{2} \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert 1\rangle\langle 1\vert
$$</p>
**축소 상태 $\rho_{\mathsf{B}}$**도 유사하게 계산될 수 있습니다.
<p>$$
\rho_{\mathsf{B}} = \operatorname{Tr}_{\mathsf{A}}(\rho) = \frac{1}{2} \operatorname{Tr}\bigl(\vert 0\rangle\langle 0\vert\bigr)\, \vert 0\rangle\langle 0\vert + \frac{1}{2} \operatorname{Tr}\bigl(\vert 1\rangle\langle 1\vert\bigr) \vert +\rangle\langle +\vert = \frac{1}{2} \vert 0\rangle\langle 0\vert + \frac{1}{2} \vert +\rangle\langle +\vert
$$</p>

### 2.6 두 큐비트에 대한 부분 대각합 (The partial trace for two qubits)
부분 대각합은 **행렬**을 사용하여 명시적으로 설명될 수도 있습니다. 여기서는 **두 큐비트**에 대해서만 설명하지만, 이는 더 큰 시스템으로도 일반화될 수 있습니다. 우리는 두 큐비트 $(\mathsf{A},\mathsf{B})$를 가지고 있다고 가정합니다. 따라서 이 두 큐비트의 상태를 설명하는 모든 밀도 행렬은 복소수 $\{\alpha_{jk} : 0\leq j,k\leq 3\}$의 선택에 대해 다음과 같이 작성될 수 있습니다.

<p>$$
\rho = \begin{pmatrix} \alpha_{00} & \alpha_{01} & \alpha_{02} & \alpha_{03}\\[2mm] \alpha_{10} & \alpha_{11} & \alpha_{12} & \alpha_{13}\\[2mm] \alpha_{20} & \alpha_{21} & \alpha_{22} & \alpha_{23}\\[2mm] \alpha_{30} & \alpha_{31} & \alpha_{32} & \alpha_{33} \end{pmatrix}
$$</p>

첫 번째 시스템에 대한 부분 대각합은 다음 공식을 가집니다.
<p>$$
\operatorname{Tr}_{\mathsf{A}} \begin{pmatrix} \alpha_{00} & \alpha_{01} & \alpha_{02} & \alpha_{03}\\[2mm] \alpha_{10} & \alpha_{11} & \alpha_{12} & \alpha_{13}\\[2mm] \alpha_{20} & \alpha_{21} & \alpha_{22} & \alpha_{23}\\[2mm] \alpha_{30} & \alpha_{31} & \alpha_{32} & \alpha_{33} \end{pmatrix} = \begin{pmatrix} \alpha_{00} & \alpha_{01} \\[2mm] \alpha_{10} & \alpha_{11} \end{pmatrix} + \begin{pmatrix} \alpha_{22} & \alpha_{23}\\[2mm] \alpha_{32} & \alpha_{33} \end{pmatrix} = \begin{pmatrix} \alpha_{00} + \alpha_{22} & \alpha_{01} + \alpha_{23}\\[2mm] \alpha_{10} + \alpha_{32} & \alpha_{11} + \alpha_{33} \end{pmatrix}
$$</p>

이 공식에 대해 생각하는 한 가지 방법은 $4\times 4$ 행렬을 각 블록이 $2\times 2$인 $2\times 2$ **블록 행렬(block matrices)**로 보는 것에서 시작합니다. 즉,
<p>$$
\rho = \begin{pmatrix} M_{0,0} & M_{0,1} \\[1mm] M_{1,0} & M_{1,1} \end{pmatrix}
$$</p>
이며, 여기서
<p>$$
M_{0,0} = \begin{pmatrix} \alpha_{00} & \alpha_{01} \\[2mm] \alpha_{10} & \alpha_{11} \end{pmatrix}, \quad M_{0,1} = \begin{pmatrix} \alpha_{02} & \alpha_{03} \\[2mm] \alpha_{12} & \alpha_{13} \end{pmatrix}, \quad M_{1,0} = \begin{pmatrix} \alpha_{20} & \alpha_{21} \\[2mm] \alpha_{30} & \alpha_{31} \end{pmatrix}, \quad M_{1,1} = \begin{pmatrix} \alpha_{22} & \alpha_{23} \\[2mm] \alpha_{32} & \alpha_{33} \end{pmatrix}
$$</p>
그러면
<p>$$
\operatorname{Tr}_{\mathsf{A}}\begin{pmatrix} M_{0,0} & M_{0,1} \\[1mm] M_{1,0} & M_{1,1} \end{pmatrix} = M_{0,0} + M_{1,1}
$$</p>
이 됩니다. 다음은 첫 번째 시스템이 아닌 두 번째 시스템이 소거될 때의 공식입니다.
<p>$$
\operatorname{Tr}_{\mathsf{B}} \begin{pmatrix} \alpha_{00} & \alpha_{01} & \alpha_{02} & \alpha_{03}\\[2mm] \alpha_{10} & \alpha_{11} & \alpha_{12} & \alpha_{13}\\[2mm] \alpha_{20} & \alpha_{21} & \alpha_{22} & \alpha_{23}\\[2mm] \alpha_{30} & \alpha_{31} & \alpha_{32} & \alpha_{33} \end{pmatrix} = \begin{pmatrix} \operatorname{Tr} \begin{pmatrix} \alpha_{00} & \alpha_{01}\\[1mm] \alpha_{10} & \alpha_{11} \end{pmatrix} & \operatorname{Tr} \begin{pmatrix} \alpha_{02} & \alpha_{03}\\[1mm] \alpha_{12} & \alpha_{13} \end{pmatrix} \\[4mm] \operatorname{Tr} \begin{pmatrix} \alpha_{20} & \alpha_{21}\\[1mm] \alpha_{30} & \alpha_{31} \end{pmatrix} & \operatorname{Tr} \begin{pmatrix} \alpha_{22} & \alpha_{23}\\[1mm] \alpha_{32} & \alpha_{33} \end{pmatrix} \end{pmatrix} = \begin{pmatrix} \alpha_{00} + \alpha_{11} & \alpha_{02} + \alpha_{13}\\[2mm] \alpha_{20} + \alpha_{31} & \alpha_{22} + \alpha_{33} \end{pmatrix}
$$</p>

이전과 유사한 형태의 블록 행렬로 표현하면, 우리는 이 공식을 가집니다.
<p>$$
\operatorname{Tr}_{\mathsf{B}} \begin{pmatrix} M_{0,0} & M_{0,1} \\[1mm] M_{1,0} & M_{1,1} \end{pmatrix} = \begin{pmatrix} \operatorname{Tr}(M_{0,0}) & \operatorname{Tr}(M_{0,1}) \\[1mm] \operatorname{Tr}(M_{1,0}) & \operatorname{Tr}(M_{1,1}) \end{pmatrix}
$$</p>

이 함수들의 블록 행렬 설명은 큐비트보다 큰 시스템으로도 자연스럽고 직접적인 방식으로 확장될 수 있습니다. 

이 강의를 마치기 위해, 위에서 고려했던 동일한 상태에 이 공식들을 적용해 봅시다.
<p>$$
\rho = \frac{1}{2} \vert 0\rangle \langle 0 \vert \otimes \vert 0\rangle \langle 0 \vert + \frac{1}{2} \vert 1\rangle \langle 1 \vert \otimes \vert +\rangle \langle + \vert = \begin{pmatrix} \frac{1}{2} & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0 \\[2mm] 0 & 0 & \frac{1}{4} & \frac{1}{4}\\[2mm] 0 & 0 & \frac{1}{4} & \frac{1}{4} \end{pmatrix}
$$</p>

첫 번째 시스템 $\mathsf{A}$의 축소 상태는 다음과 같습니다.
<p>$$
\operatorname{Tr}_{\mathsf{B}} \begin{pmatrix} \frac{1}{2} & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0\\[2mm] 0 & 0 & \frac{1}{4} & \frac{1}{4}\\[2mm] 0 & 0 & \frac{1}{4} & \frac{1}{4} \end{pmatrix} = \begin{pmatrix} \operatorname{Tr} \begin{pmatrix} \frac{1}{2} & 0\\[1mm] 0 & 0 \end{pmatrix} & \operatorname{Tr} \begin{pmatrix} 0 & 0\\[1mm] 0 & 0 \end{pmatrix} \\[4mm] \operatorname{Tr} \begin{pmatrix} 0 & 0\\[1mm] 0 & 0 \end{pmatrix} & \operatorname{Tr} \begin{pmatrix} \frac{1}{4} & \frac{1}{4}\\[2mm] \frac{1}{4} & \frac{1}{4} \end{pmatrix} \end{pmatrix} = \begin{pmatrix} \frac{1}{2} & 0\\[2mm] 0 & \frac{1}{2} \end{pmatrix}
$$</p>

그리고 두 번째 시스템 $\mathsf{B}$의 축소 상태는 다음과 같습니다.
<p>$$
\operatorname{Tr}_{\mathsf{A}} \begin{pmatrix} \frac{1}{2} & 0 & 0 & 0\\[2mm] 0 & 0 & 0 & 0\\[2mm] 0 & 0 & \frac{1}{4} & \frac{1}{4}\\[2mm] 0 & 0 & \frac{1}{4} & \frac{1}{4} \end{pmatrix} = \begin{pmatrix} \frac{1}{2} & 0\\[1mm] 0 & 0 \end{pmatrix} + \begin{pmatrix} \frac{1}{4} & \frac{1}{4}\\[2mm] \frac{1}{4} & \frac{1}{4} \end{pmatrix} = \begin{pmatrix} \frac{3}{4} & \frac{1}{4}\\[2mm] \frac{1}{4} & \frac{1}{4} \end{pmatrix}
$$</p>
