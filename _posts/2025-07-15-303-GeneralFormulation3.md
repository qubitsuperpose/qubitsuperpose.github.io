---
title: 7차시 3:General formulation of quantum information3(General Measurements)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 측정의 수학적 공식화

이 강의는 측정에 대한 두 가지 동등한 수학적 설명으로 시작합니다:
1. 일반적인 측정은 투영 측정(projective measurements)의 설명을 일반화하는 방식으로, 각 측정 결과에 대해 하나씩, **행렬들의 모음**으로 설명될 수 있습니다.
2. 일반적인 측정은 출력이 항상 고전적 상태(대각 밀도 행렬로 표현됨)인 **채널**로 설명될 수 있습니다.

우리는 가능한 결과가 **유한한** 측정에 초점을 맞출 것입니다. 무한히 많은 가능한 결과를 가진 측정을 정의하는 것이 가능하지만, 이는 계산 및 정보 처리의 맥락에서는 흔히 접하지 않으며, 적절하게 공식화하기 위해서는 추가적인 수학(즉, 측도 이론(measure theory))이 필요합니다.

우리의 초기 초점은 소위 **파괴적(destructive) 측정**에 맞춰질 것입니다. 이러한 측정에서는 측정의 출력이 고전적 측정 결과만 포함하며 — 측정된 시스템의 측정 후 양자 상태에 대한 명세는 없습니다. 직관적으로 말해서, 이러한 측정은 양자 시스템 자체를 파괴하거나, 측정이 이루어지자마자 시스템이 즉시 폐기된다고 상상할 수 있습니다. 강의 후반부에서는 시야를 넓혀 **비파괴적(non-destructive) 측정**을 고려할 것입니다. 비파괴적 측정에서는 고전적 측정 결과와 측정된 시스템의 측정 후 양자 상태가 모두 존재합니다.

## 1. 행렬들의 모음으로서의 측정
$X$를 측정될 시스템이라고 가정하고, 단순화를 위해 $X$의 고전적 상태 집합이 어떤 양의 정수 $n$에 대해 $\\{0,\ldots, n-1\\}$이라고 가정합니다. 따라서 $X$의 양자 상태를 나타내는 밀도 행렬은 $n \times n$ 행렬입니다. 실제로 $X$의 고전적 상태를 참조할 필요는 많지 않지만, $X$의 고전적 상태의 수인 $n$을 참조하는 것은 편리할 것입니다. 또한 측정의 가능한 결과는 어떤 양의 정수 $m$에 대해 정수 $0,\ldots,m-1$이라고 가정합니다.

우리는 단지 단순함을 유지하기 위해 이러한 이름을 사용하고 있다는 점에 유의하십시오. 다음에 이어지는 모든 것을 다른 유한한 고전적 상태 집합과 측정 결과로 일반화하고 원하는 대로 이름을 바꾸는 것은 간단합니다.

### 1.1 투영 측정

**투영 측정(projective measurement)**은 항등 행렬(identity matrix)로 합산되는 **투영 행렬(projection matrices)**의 모음으로 설명된다는 것을 상기하십시오. 기호로 표현하면,
<p>$$\{ \Pi_0,\ldots,\Pi_{m-1} \}$$</p>
는 각 $\Pi_a$가 $n \times n$ 투영 행렬이고 다음 조건이 충족될 경우 $X$의 투영 측정을 설명합니다.

<p>$$\Pi_0 + \cdots + \Pi_{m-1} = \mathbb{I}_{\mathsf{X}}$$</p>

이러한 측정이 어떤 양자 상태 벡터 $\vert\psi\rangle$로 기술되는 상태에 있는 시스템 $X$에 대해 수행될 때, 각 결과 $a$는 $\|\Pi_a\vert\psi\rangle\|^2$와 같은 확률로 얻어집니다. 또한 $X$의 측정 후 상태는 벡터 $\Pi_a\vert\psi\rangle$를 정규화하여 얻어지지만, 현재는 측정 후 상태를 무시하고 있습니다.

만약 $X$의 상태가 양자 상태 벡터 $\vert\psi\rangle$ 대신 밀도 행렬 $\rho$로 기술된다면, 우리는 결과 $a$를 얻을 확률을 $\operatorname{Tr}(\Pi_a \rho)$로 다르게 표현할 수 있습니다.

만약 $\rho = \vert \psi\rangle\langle\psi\vert$가 순수 상태(pure state)라면, 두 표현은 같습니다:
<p>$$\operatorname{Tr}(\Pi_a \rho) = \operatorname{Tr}(\Pi_a \vert \psi\rangle\langle\psi \vert) = \langle \psi \vert \Pi_a \vert \psi \rangle = \langle \psi \vert \Pi_a \Pi_a \vert \psi \rangle = \|\Pi_a\vert\psi\rangle\|^2$$</p>

여기서 우리는 두 번째 등식에 대해 트레이스의 순환 속성(cyclic property of the trace)을 사용하고 있으며, 세 번째 등식에 대해서는 각 $\Pi_a$가 투영 행렬이므로 $\Pi_a^2 = \Pi_a$를 만족한다는 사실을 사용하고 있습니다.

일반적으로, 만약 $\rho$가 순수 상태들의 볼록 조합(convex combination)
<p>$$\rho = \sum_{k = 0}^{N-1} p_k \vert \psi_k\rangle\langle \psi_k \vert$$</p>
이라면, 이 표현 $\operatorname{Tr}(\Pi_a \rho)$가 $\rho$에 대해 선형이라는 사실 때문에 결과 $a$에 대한 평균 확률과 일치합니다.

<p>$$\operatorname{Tr}(\Pi_a \rho) = \sum_{k = 0}^{N-1} p_k \operatorname{Tr}(\Pi_a \vert \psi_k\rangle\langle\psi_k\vert) = \sum_{k = 0}^{N-1} p_k \|\Pi_a\vert\psi_k\rangle\|^2$$</p>

### 1.2 일반 측정
일반 측정에 대한 수학적 설명은 투영 측정의 정의를 완화함으로써 얻어집니다. 구체적으로, 우리는 측정을 설명하는 행렬 모음이 투영 행렬 대신 임의의 **양의 준정부호(positive semidefinite) 행렬**이 되는 것을 허용합니다. (투영 행렬은 항상 양의 준정부호입니다. 이들은 대안적으로 모든 고유값이 0 또는 1인 양의 준정부호 행렬로 정의될 수 있습니다.).

특히, 결과 $0,\ldots,m-1$을 갖는 시스템 $X$의 일반 측정은 $X$의 고전적 상태에 행과 열이 대응되고 다음 조건을 충족하는 양의 준정부호 행렬들의 모음 $\\{P_0,\ldots,P_{m-1}\\}$로 지정됩니다.
<p>$$P_0 + \cdots + P_{m-1} = \mathbb{I}_{\mathsf{X}}$$</p>
시스템 $X$가 밀도 행렬 $\rho$로 기술되는 상태에 있을 때 측정되면, 각 결과 $a \in \\{0,\ldots,m-1\\}$는 확률 $\operatorname{Tr}(P_a \rho)$로 나타납니다.

우리가 당연히 요구해야 하듯이, 일반 측정의 결과 확률 벡터
<p>$$\bigl(\operatorname{Tr}(P_0 \rho),\ldots,\operatorname{Tr}(P_{m-1} \rho)\bigr)$$</p>
는 밀도 행렬 $\rho$의 어떤 선택에 대해서도 항상 확률 벡터를 형성합니다. 다음 두 가지 관찰은 이것이 사실임을 입증합니다.

1\. 임의의 두 양의 준정부호 행렬의 곱의 트레이스는 항상 음이 아닌 값이라는 사실 때문에, 각 값 $\operatorname{Tr}(P_a \rho)$는 음이 아니어야 합니다.

<p>$$Q, R \geq 0 \; \Rightarrow \: \operatorname{Tr}(QR) \geq 0$$</p>
이 사실을 주장하는 한 가지 방법은 $Q$와 $R$의 스펙트럼 분해(spectral decompositions)와 트레이스의 순환 속성을 사용하여 곱 $QR$의 트레이스를 음이 아닌 실수들의 합으로 표현하는 것인데, 따라서 이 값은 음이 아니어야 합니다.

2\. 조건 $P_0 + \cdots + P_{m-1} = \mathbb{I}_{\mathsf{X}}$는 트레이스의 선형성(linearity)과 함께 확률의 합이 $1$이 되도록 보장합니다.

<p>$$\sum_{a = 0}^{m-1} \operatorname{Tr}(P_a \rho) = \operatorname{Tr}\Biggl(\sum_{a = 0}^{m-1} P_a \rho\Biggr) = \operatorname{Tr}(\mathbb{I}\rho) = \operatorname{Tr}(\rho) = 1$$</p>

### 1.3 예시 1: 모든 투영 측정
투영은 항상 양의 준정부호이므로, 모든 투영 측정은 일반 측정의 예시입니다.

예를 들어, 큐비트의 표준 기저 측정은 $\{P_0,P_1\}$로 나타낼 수 있습니다. 여기서
<p>$$P_0 = \vert 0\rangle\langle 0\vert = \begin{pmatrix} 1 & 0 \\ 0 & 0 \end{pmatrix} \quad\text{and}\quad P_1 = \vert 1\rangle\langle 1\vert = \begin{pmatrix} 0 & 0 \\ 0 & 1 \end{pmatrix}$$</p>

상태 $\rho$에 있는 큐비트를 측정하면 다음과 같은 결과 확률이 나옵니다.

<p>$$\begin{aligned} \operatorname{Prob}(\text{outcome} = 0) & = \operatorname{Tr}(P_0 \rho) = \operatorname{Tr}\bigl(\vert 0\rangle\langle 0\vert \rho\bigr) = \langle 0\vert \rho \vert 0 \rangle \\[1mm] \operatorname{Prob}(\text{outcome} = 1) & = \operatorname{Tr}(P_1 \rho) = \operatorname{Tr}\bigl(\vert 1\rangle\langle 1\vert\rho\bigr) = \langle 1 \vert \rho \vert 1 \rangle \end{aligned}$$</p>

### 1.4 예시 2: 비투영 큐비트 측정
$X$가 큐비트라고 가정하고, 두 행렬을 다음과 같이 정의합니다.
<p>$$P_0 = \begin{pmatrix} \frac{2}{3} & \frac{1}{3}\\[2mm] \frac{1}{3} & \frac{1}{3} \end{pmatrix} \qquad P_1 = \begin{pmatrix} \frac{1}{3} & -\frac{1}{3}\\[2mm] -\frac{1}{3} & \frac{2}{3} \end{pmatrix}$$</p>

이들은 둘 다 양의 준정부호 행렬입니다. 이들은 에르미트(Hermitian) 행렬이며, 두 경우 모두 고유값은 $1/2 \pm \sqrt{5}/6$으로, 둘 다 양수입니다. 또한 $P_0 + P_1 = \mathbb{I}$이므로, $\{P_0, P_1\}$는 측정을 설명합니다.

만약 $X$의 상태가 밀도 행렬 $\rho$로 기술되고 이 측정을 수행한다면, 결과 $0$을 얻을 확률은 $\operatorname{Tr}(P_0 \rho)$이고 결과 $1$을 얻을 확률은 $\operatorname{Tr}(P_1 \rho)$입니다. 예를 들어, $\rho = \vert + \rangle \langle + \vert$인 경우, 두 결과 $0$과 $1$에 대한 확률은 다음과 같습니다.

<p>$$\begin{aligned} \operatorname{Tr}(P_0 \rho) & = \operatorname{Tr}\left( \begin{pmatrix} \frac{2}{3} & \frac{1}{3}\\[2mm] \frac{1}{3} & \frac{1}{3} \end{pmatrix} \begin{pmatrix} \frac{1}{2} & \frac{1}{2}\\[2mm] \frac{1}{2} & \frac{1}{2} \end{pmatrix} \right)\\[4mm] & = \biggl(\frac{2}{3} \cdot \frac{1}{2} + \frac{1}{3} \cdot \frac{1}{2}\biggr) + \biggl(\frac{1}{3}\cdot\frac{1}{2} + \frac{1}{3}\cdot\frac{1}{2}\biggr)\\ & = \frac{1}{2} + \frac{1}{3} = \frac{5}{6}\\[4mm] \operatorname{Tr}(P_1 \rho) & = \operatorname{Tr}\left( \begin{pmatrix} \frac{1}{3} & -\frac{1}{3}\\[2mm] -\frac{1}{3} & \frac{2}{3} \end{pmatrix} \begin{pmatrix} \frac{1}{2} & \frac{1}{2}\\[2mm] \frac{1}{2} & \frac{1}{2} \end{pmatrix} \right)\\[4mm] & = \biggl(\frac{1}{3} \cdot \frac{1}{2} - \frac{1}{3} \cdot \frac{1}{2}\biggr) + \biggl(-\frac{1}{3}\cdot\frac{1}{2} + \frac{2}{3}\cdot\frac{1}{2}\biggr)\\ & = 0 + \frac{1}{6} = \frac{1}{6} \end{aligned}$$</p>

### 1.5 예시 3: 사면체 측정 (tetrahedral measurement)
다음과 같이 네 개의 단일 큐비트 양자 상태 벡터를 정의합니다.
<p>$$\begin{aligned} \vert\phi_0\rangle & = \vert 0 \rangle\\ \vert\phi_1\rangle & = \frac{1}{\sqrt{3}}\vert 0 \rangle + \sqrt{\frac{2}{3}} \vert 1\rangle \\ \vert\phi_2\rangle & = \frac{1}{\sqrt{3}}\vert 0 \rangle + \sqrt{\frac{2}{3}} e^{2\pi i/3} \vert 1\rangle \\ \vert\phi_3\rangle & = \frac{1}{\sqrt{3}}\vert 0 \rangle + \sqrt{\frac{2}{3}} e^{-2\pi i/3} \vert 1\rangle \end{aligned}$$</p>

이 네 가지 상태는 블로흐 구(Bloch sphere) 안에 내접하는 **정사면체(regular tetrahedron)**의 꼭짓점이기 때문에 때때로 **사면체 상태**로 알려져 있습니다.

<img src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fgeneral-formulation-of-quantum-information%2Fgeneral-measurements%2Ftetrahedral-states.avif&w=3840&q=75" style="width:70%">

블로흐 구에서 이 네 가지 상태의 직교 좌표(Cartesian coordinates)는 다음과 같습니다.
<p>$$\begin{gather}(0,0,1),\\[2mm] \left( \frac{2\sqrt{2}}{3} , 0 , -\frac{1}{3} \right),\\[1mm] \left( -\frac{\sqrt{2}}{3} , \sqrt{\frac{2}{3}} , -\frac{1}{3} \right),\\[1mm] \left( -\frac{\sqrt{2}}{3} , -\sqrt{\frac{2}{3}} , -\frac{1}{3} \right)\end{gather}$$</p>

이는 이 상태들의 밀도 행렬 표현을 파울리 행렬(Pauli matrices)의 선형 조합으로 표현하여 확인할 수 있습니다.
<p>$$\begin{gather}\vert \phi_0 \rangle\langle \phi_0 \vert = \begin{pmatrix} 1 & 0\\[1mm] 0 & 0 \end{pmatrix} = \frac{\mathbb{I} + \sigma_z}{2} \\
$\vert \phi_1 \rangle\langle \phi_1 \vert = \begin{pmatrix} \frac{1}{3} & \frac{\sqrt{2}}{3} \\[2mm] \frac{\sqrt{2}}{3} & \frac{2}{3} \end{pmatrix} = \frac{\mathbb{I} + \frac{2\sqrt{2}}{3} \sigma_x - \frac{1}{3}\sigma_z}{2} \\
$\vert \phi_2 \rangle\langle \phi_2 \vert = \begin{pmatrix} \frac{1}{3} & -\frac{1}{3\sqrt{2}} - \frac{i}{\sqrt{6}} \\[2mm] -\frac{1}{3\sqrt{2}} + \frac{i}{\sqrt{6}} & \frac{2}{3} \end{pmatrix} = \frac{\mathbb{I} - \frac{\sqrt{2}}{3} \sigma_x + \sqrt{\frac{2}{3}} \sigma_y - \frac{1}{3}\sigma_z}{2} \\
$\vert \phi_3 \rangle\langle \phi_3 \vert = \begin{pmatrix} \frac{1}{3} & -\frac{1}{3\sqrt{2}} + \frac{i}{\sqrt{6}} \\[2mm] -\frac{1}{3\sqrt{2}} - \frac{i}{\sqrt{6}} & \frac{2}{3} \end{pmatrix} = \frac{\mathbb{I} - \frac{\sqrt{2}}{3} \sigma_x - \sqrt{\frac{2}{3}} \sigma_y - \frac{1}{3}\sigma_z}{2}\end{gather}$$</p>

이 네 가지 상태는 블로흐 구 위에 완벽하게 퍼져 있으며, 각각은 나머지 세 상태로부터 등거리에 있고 임의의 두 상태 사이의 각도는 항상 동일합니다.

이제 각 $a = 0, \ldots, 3$에 대해 $P_a$를 다음과 같이 설정하여 큐비트의 측정 $\\{P_0, P_1, P_2, P_3\\}$를 정의해 보겠습니다.
<p>$$P_a = \frac{\vert\phi_a\rangle\langle\phi_a\vert}{2}$$</p>
이것이 유효한 측정임을 다음과 같이 확인할 수 있습니다.

1. 각 $P_a$는 순수 상태를 2분의 1로 나눈 것이므로 명백히 양의 준정부호입니다. 즉, 각각은 에르미트 행렬이며 하나의 고유값은 $1/2$이고 다른 모든 고유값은 0입니다.
2. 이 행렬들의 합은 항등 행렬입니다: $P_0 + P_1 + P_2 + P_3 = \mathbb{I}$. 이 행렬들을 파울리 행렬의 선형 조합으로 표현하면 이를 쉽게 확인할 수 있습니다.

## 2. 채널로서의 측정

측정을 수학적으로 설명하는 두 번째 방법은 채널로서 설명하는 것입니다.


우리가 확률적 상태를 대각 밀도 행렬과 동일시할 수 있는 한, 고전적 정보는 양자 정보의 특별한 경우로 간주될 수 있습니다. 따라서 운영적인 용어(operational terms)로 볼 때, 우리는 측정을, 입력은 측정되는 시스템의 상태를 기술하는 행렬이고 출력은 측정 결과의 결과 분포를 기술하는 **대각** 밀도 행렬인 채널로 생각할 수 있습니다.

우리는 곧 이 속성을 가진 모든 채널이 양의 준정부호 행렬들의 모음으로서의 측정 설명과 직접적으로 연결되는 간단하고 정식적인 형태(canonical form)로 항상 작성될 수 있음을 알게 될 것입니다. 반대로, 행렬들의 모음으로서 임의의 측정이 주어지면, 이전 단락에서 제시된 대로 주어진 측정을 설명하는 대각 출력 속성을 갖는 유효한 채널이 항상 존재합니다. 이러한 관찰들을 종합하면, 일반 측정에 대한 두 가지 설명이 동등하다는 것을 알 수 있습니다.

더 나아가기 전에, 측정, 그것을 채널로 보는 방식, 그리고 우리가 그것에 대해 하는 가정에 대해 더 정확히 해봅시다.

이전과 마찬가지로, $X$는 측정될 시스템이고, 측정의 가능한 결과는 어떤 양의 정수 $m$에 대해 정수 $0,\ldots,m-1$이라고 가정합니다. 우리는 $Y$를 측정 결과를 저장하는 시스템으로 두며, 따라서 그것의 고전적 상태 집합은 $\\{0,\ldots,m-1\\}$입니다. 그리고 우리는 측정을 $X$에서 $Y$로 가는 $\Phi$라는 채널로 나타냅니다. 우리의 가정은 $Y$가 **고전적**이라는 것입니다 — 즉, $X$에 대해 어떤 상태에서 시작하든, 우리가 얻는 $Y$의 상태는 대각 밀도 행렬로 표현된다는 의미입니다.

$\Phi$의 출력이 항상 대각임을 수학적 용어로 다음과 같이 표현할 수 있습니다. 먼저 $Y$에 대한 완전한 디페이징 채널(completely dephasing channel) $\Delta_m$을 정의합니다.
<p>$$\Delta_m(\sigma) = \sum_{a = 0}^{m-1} \langle a \vert \sigma \vert a\rangle \,\vert a\rangle\langle a\vert$$</p>
이 채널은 이전 강의의 완전한 디페이징 큐비트 채널 $\Delta$와 유사합니다. 선형 매핑으로서, 이것은 입력 행렬의 모든 비대각 항목을 0으로 만들고 대각 항목은 그대로 둡니다.

이제, 주어진 밀도 행렬 $\sigma$가 대각임을 표현하는 간단한 방법은 $\sigma = \Delta_m(\sigma)$라는 방정식입니다. 말로 표현하면, 밀도 행렬의 모든 비대각 항목을 0으로 만드는 것은, 비대각 항목들이 처음부터 모두 0이었을 때만 효과가 없습니다. 따라서 채널 $\Phi$는 ( $Y$가 고전적이라는) 우리의 가정을 다음의 경우에만 그리고 그 경우에만 만족합니다.
<p>$$\Phi(\rho) = \Delta_m(\Phi(\rho))$$</p>

$X$의 상태를 나타내는 모든 밀도 행렬 $\rho$에 대해서입니다.

## 3. 공식화의 동등성
### 3.1 채널에서 행렬로

모든 밀도 행렬 $\rho$에 대해
<p>$$\Phi(\rho) = \Delta_m(\Phi(\rho))$$</p>
라는 속성을 갖는 $X$에서 $Y$로 가는 채널이 있다고 가정해 봅시다. 이것은 다음과 같이 다르게 표현될 수 있습니다.
<p>$$\Phi(\rho) = \sum_{a = 0}^{m-1} \langle a \vert \Phi(\rho) \vert a\rangle\, \vert a\rangle\langle a \vert \tag{1} $$</p> 

모든 채널과 마찬가지로, 우리는 크라우스 행렬(Kraus matrices) $A_0,\ldots,A_{N-1}$을 선택하는 어떤 방식으로 $\Phi$를 크라우스 형식(Kraus form)으로 표현할 수 있습니다.
<p>$$\Phi(\rho) = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger}$$</p>
이것은 $\Phi(\rho)$의 대각 항목에 대한 대체 표현을 제공합니다:
<p>$$\begin{aligned} \langle a \vert \Phi(\rho) \vert a\rangle & = \sum_{k = 0}^{N-1} \langle a \vert A_k \rho A_k^{\dagger} \vert a\rangle \\ & = \sum_{k = 0}^{N-1} \operatorname{Tr}\bigl( A_k^{\dagger} \vert a\rangle\langle a \vert A_k \rho\bigr)\\ & = \operatorname{Tr}\bigl(P_a\rho\bigr) \end{aligned}$$</p>

여기서
<p>$$P_a = \sum_{k = 0}^{N-1} A_k^{\dagger} \vert a\rangle\langle a \vert A_k$$</p>
따라서, 이 동일한 행렬 $P_0,\ldots,P_{m-1}$에 대해 우리는 채널 $\Phi$를 다음과 같이 표현할 수 있습니다.
<p>$\Phi(\rho) = \sum_{a = 0}^{m-1} \operatorname{Tr}(P_a \rho) \vert a\rangle\langle a\vert$</p>

이 표현은 각 측정 결과가 확률 $\operatorname{Tr}(P_a \rho)$로 나타나는 것을 보여주므로 행렬 관점에서 일반 측정을 설명하는 것과 일치합니다.

이제 일반 측정을 설명하기 위해 행렬 모음 $\\{P_0,\ldots,P_{m-1}\\}$에 요구되는 두 가지 속성이 실제로 충족되는지 관찰해 봅시다. 첫 번째 속성은 이들이 모두 양의 준정부호 행렬이라는 것입니다. 이를 확인하는 한 가지 방법은 $X$의 고전적 상태에 대응하는 항목을 갖는 모든 벡터 $\vert \psi\rangle$에 대해 다음이 성립한다는 것을 관찰하는 것입니다.

<p>$$\langle \psi \vert P_a \vert \psi\rangle = \sum_{k = 0}^{N-1} \langle \psi \vert A_k^{\dagger} \vert a\rangle\langle a \vert A_k\vert \psi\rangle = \sum_{k = 0}^{N-1} \bigl\vert\langle a \vert A_k\vert \psi\rangle\bigr\vert^2 \geq 0$$</p>
두 번째 속성은 이 행렬들을 합하면 항등 행렬을 얻는다는 것입니다.

<p>$$\begin{aligned} \sum_{a = 0}^{m-1} P_a & = \sum_{a = 0}^{m-1} \sum_{k = 0}^{N-1} A_k^{\dagger} \vert a\rangle\langle a \vert A_k \\ & = \sum_{k = 0}^{N-1} A_k^{\dagger} \Biggl(\sum_{a = 0}^{m-1} \vert a\rangle\langle a \vert\Biggr) A_k \\ & = \sum_{k = 0}^{N-1} A_k^{\dagger} A_k \\ & = \mathbb{I}_{\mathsf{X}} \end{aligned}$$</p>

마지막 등식은 $\Phi$가 채널이므로 그것의 크라우스 행렬이 이 조건을 만족해야 한다는 사실에서 비롯됩니다.

### 3.2 행렬에서 채널로
이제 $$P_0 + \cdots + P_{m-1} = \mathbb{I}_{\mathsf{X}}$$를 만족하는 양의 준정부호 행렬들의 임의의 모음 $\\{P_0,\ldots,P_{m-1}\\}$에 대해, 다음으로 정의되는 매핑이
<p>$$\Phi(\rho) = \sum_{a = 0}^{m-1} \operatorname{Tr}(P_a \rho) \vert a \rangle\langle a\vert$$</p>

실제로 $X$에서 $Y$로 가는 유효한 채널임을 확인해 봅시다.

이를 수행하는 한 가지 방법은 이 매핑의 초이 표현(Choi representation)을 계산하는 것입니다.
<p>$$\begin{aligned} J(\Phi) & = \sum_{b,c = 0}^{n-1} \vert b \rangle \langle c \vert \otimes \Phi(\vert b \rangle \langle c \vert)\\[1mm] & = \sum_{b,c = 0}^{n-1} \sum_{a = 0}^{m-1} \vert b \rangle \langle c \vert \otimes \operatorname{Tr}(P_a \vert b \rangle \langle c \vert) \vert a \rangle\langle a\vert\\[1mm] & = \sum_{b,c = 0}^{n-1} \sum_{a = 0}^{m-1} \vert b \rangle \langle b \vert P_a^T \vert c \rangle \langle c \vert \otimes \vert a \rangle\langle a\vert\\[1mm] & = \sum_{a = 0}^{m-1} P_a^T \otimes \vert a \rangle\langle a\vert \end{aligned}$$</p>

세 번째 등식에서 각 $P_a$의 전치(transpose)가 도입된 이유는 다음과 같습니다.
<p>$$\langle c \vert P_a \vert b\rangle = \langle b \vert P_a^T \vert c\rangle$$</p>
이것은 $\vert b \rangle \langle b \vert$와 $\vert c \rangle \langle c \vert$ 표현이 나타나도록 허용하며, 이들은 각각 $b$와 $c$에 대해 합산될 때 항등 행렬로 단순화됩니다.

$P_0, \ldots, P_{m-1}$가 양의 준정부호라는 가정에 의해, $P_0^{T}, \ldots, P_{m-1}^{T}$ 역시 양의 준정부호입니다. 특히, 에르미트 행렬을 전치하면 또 다른 에르미트 행렬이 되며, 임의의 정방 행렬과 그 전치 행렬의 고유값은 항상 일치합니다. 따라서 $J(\Phi)$는 양의 준정부호입니다. 출력 시스템 $Y$ (오른쪽에 있는 시스템)에 대해 트레이스를 취하면 다음을 얻습니다.

<p>$$\operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) = \sum_{a = 0}^{m-1} P_a^T = \mathbb{I}_{\mathsf{X}}^T = \mathbb{I}_{\mathsf{X}}$$</p>
따라서 우리는 $\Phi$가 채널이라고 결론 내립니다.

## 4. 부분 측정 (Partial measurements)

우리가 전체적으로 양자 상태에 있는 여러 시스템을 가지고 있고, 그 시스템들 중 하나에 대해 일반 측정이 수행된다고 가정해 봅시다. 이는 측정과 측정 전 시스템 상태에 의해 결정되는 확률에 따라 무작위로 선택된 측정 결과 중 하나를 초래합니다. 그러면 나머지 시스템의 결과 상태는 일반적으로 어떤 측정 결과가 얻어졌는지에 따라 달라집니다.

시스템 $X$가 측정될 때 시스템 쌍 $(X, Z)$에 대해 이것이 어떻게 작동하는지 살펴봅시다. (우리는 오른쪽에 있는 시스템을 $Z$라고 명명하는데, 이는 측정을 채널로 볼 때 $Y$를 측정의 고전적 출력을 나타내는 시스템으로 취할 것이기 때문입니다.). 그런 다음 시스템이 교환되는 상황뿐만 아니라 세 개 이상의 시스템으로도 쉽게 일반화할 수 있습니다.

측정 전 $(X, Z)$의 상태가 밀도 행렬 $\rho$로 기술된다고 가정하며, 이는 다음과 같이 작성할 수 있습니다.

<p>$$\rho = \sum_{b,c = 0}^{n-1} \vert b\rangle\langle c\vert \otimes \rho_{b,c}$$</p>
이 표현에서 우리는 $X$의 고전적 상태가 $0,\ldots,n-1$이라고 가정하고 있습니다.
우리는 측정이 행렬 모음 $\\{P_0,\ldots,P_{m-1}\\}$로 기술된다고 가정할 것입니다. 이 측정은 $X$에서 $Y$로 가는 채널 $\Phi$로 다르게 기술될 수도 있으며, 여기서 $Y$는 고전적 상태 집합 $\\{0,\ldots,m-1\\}$을 갖는 새로운 시스템입니다. 구체적으로, 이 채널의 작용은 다음과 같이 표현될 수 있습니다.

<p>$$\Phi(\xi) = \sum_{a = 0}^{m-1} \operatorname{Tr}(P_a \xi)\, \vert a \rangle \langle a \vert$$</p>

### 4.1 결과 확률
우리는 시스템 $X$의 측정을 고려하고 있으므로, 다른 측정 결과가 얻어지는 확률은 $X$의 축소 상태(reduced state)인 $\rho_{\mathsf{X}}$에만 의존할 수 있습니다. 특히, 각 결과 $a \in \\{0,\ldots,m-1\\}$가 나타날 확률은 세 가지 동등한 방식으로 표현될 수 있습니다.

<p>$$\operatorname{Tr}\bigl( P_a \rho_{\mathsf{X}}\bigr) = \operatorname{Tr}\bigl( P_a \operatorname{Tr}_{\mathsf{Z}}(\rho)\bigr) = \operatorname{Tr}\bigl( (P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho \bigr)$$</p>

첫 번째 표현은 단일 시스템 측정에 대해 이미 알고 있는 것을 기반으로 결과 $a$를 얻을 확률을 자연스럽게 나타냅니다. 두 번째 표현을 얻기 위해 우리는 단순히 정의 $\rho_{\mathsf{X}} = \operatorname{Tr}_{\mathsf{Z}}(\rho)$를 사용하고 있습니다.

세 번째 표현을 얻는 것은 더 많은 생각을 필요로 하며 — 학습자들은 그것이 사실임을 스스로 납득하도록 권장됩니다. 힌트: 두 번째 표현과 세 번째 표현 사이의 동등성은 $\rho$가 밀도 행렬이거나 각 $P_a$가 양의 준정부호라는 사실에 의존하지 않습니다. 먼저 $\rho = M\otimes N$ 형태의 텐서 곱에 대해 이를 보여준 다음, 선형성에 의해 일반적으로 참이어야 한다고 결론지으십시오.

이전 방정식에서 첫 번째 표현과 세 번째 표현의 동등성이 즉각적이지 않을 수 있지만, 이는 이치에 맞습니다. $X$에 대한 측정에서 시작하여, 우리는 사실상 $(X, Z)$의 측정을 정의하고 있으며, 여기서 우리는 단순히 $Z$를 버리고 $X$를 측정합니다. 모든 측정과 마찬가지로, 이 새로운 측정은 행렬들의 모음으로 기술될 수 있으며, 이 측정이 다음 모음으로 기술된다는 것은 놀라운 일이 아닙니다.

<p>$$\{P_0\otimes\mathbb{I}_{\mathsf{Z}}, \ldots, P_{m-1}\otimes\mathbb{I}_{\mathsf{Z}}\}$$</p>

### 4.2 측정 결과에 따른 조건부 상태
우리가 다른 결과들에 대한 확률뿐만 아니라 각 측정 결과에 따른 $Z$의 결과 상태까지 결정하고 싶다면, 우리는 측정의 채널 설명을 살펴볼 수 있습니다. 특히, $X$에 $\Phi$를 적용하고 $Z$에는 아무것도 하지 않을 때 우리가 얻는 상태를 조사해 봅시다.

<p>$$\begin{aligned} (\Phi\otimes\operatorname{Id}_{\mathsf{Z}})(\rho) & = \sum_{b,c = 0}^{n-1} \Phi(\vert b\rangle\langle c\vert) \otimes \rho_{b,c}\\ & = \sum_{a = 0}^{m-1} \sum_{b,c = 0}^{n-1} \operatorname{Tr}(P_a \vert b\rangle\langle c\vert) \,\vert a\rangle \langle a \vert \otimes \rho_{b,c}\\ & = \sum_{a = 0}^{m-1} \vert a\rangle \langle a \vert \otimes \sum_{b,c = 0}^{n-1} \operatorname{Tr}(P_a \vert b\rangle\langle c\vert) \rho_{b,c}\\ & = \sum_{a = 0}^{m-1} \vert a\rangle \langle a \vert \otimes \sum_{b,c = 0}^{n-1} \operatorname{Tr}_{\mathsf{X}}\bigl((P_a\otimes\mathbb{I}_{\mathsf{Z}}) (\vert b\rangle\langle c\vert\otimes\rho_{b,c})\bigr)\\ & = \sum_{a = 0}^{m-1} \vert a\rangle \langle a \vert \otimes \operatorname{Tr}_{\mathsf{X}}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho\bigr) \end{aligned}$$</p>

$\Phi$가 채널이라는 사실 덕분에 이것은 밀도 행렬이며, 따라서 각 행렬 $$\operatorname{Tr}_{\mathsf{X}}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)$$는 필연적으로 양의 준정부호입니다.

마지막 한 단계는 이 표현을 우리가 찾고 있는 것을 드러내는 표현으로 변환합니다.
<p>$$\sum_{a = 0}^{m-1} \operatorname{Tr}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)\, \vert a\rangle \langle a \vert \otimes \frac{\operatorname{Tr}_{\mathsf{X}}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)}{\operatorname{Tr}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)}$$</p>

이것은 **고전-양자 상태(classical-quantum state)**의 예시입니다.
<p>$$\sum_{a = 0}^{m-1} p(a)\, \vert a\rangle\langle a\vert \otimes \sigma_a,$$</p>
이는 *밀도 행렬* 강의에서 보았듯이 말입니다. 각 측정 결과 $a \in \\{0,\ldots,m-1\\}$에 대해, 우리는 다음 확률로:
<p>$$p(a) = \operatorname{Tr}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)$$</p>

$Y$가 고전적 상태 $\vert a \rangle \langle a \vert$에 있고 $Z$가 다음 상태에 있게 됩니다.
<p>$$\sigma_a = \frac{\operatorname{Tr}_{\mathsf{X}}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)}{\operatorname{Tr}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)}. \tag{2}$$</p>

즉, 이것은
<p>$$\operatorname{Tr}_{\mathsf{X}}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)$$</p>
를 그것의 트레이스로 나누어 정규화함으로써 우리가 얻는 밀도 행렬입니다. (공식적으로 말하면, 상태 $\sigma_a$는 확률 $p(a)$가 0이 아닐 때만 정의됩니다. $p(a) = 0$일 때 이 상태는 무관합니다. 왜냐하면 이는 확률 0으로 발생하는 이산적인 사건을 나타내기 때문입니다.).

당연히, 결과 확률은 우리의 이전 관찰과 일치합니다.
요약하자면, $(X, Z)$가 상태 $\rho$에 있을 때 $X$에 대해 측정 $\\{P_0, \ldots, P_{m-1}\\}$이 수행될 때 일어나는 일은 다음과 같습니다.
1. 각 결과 $a$는 확률 $p(a) = \operatorname{Tr}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)$로 나타납니다.
2. 결과 $a$를 얻는다는 조건 하에, $Z$의 상태는 그 다음 방정식 (2)에 표시된 밀도 행렬 $\sigma_a$로 표현되며, 이는 $$\operatorname{Tr}_{\mathsf{X}}\bigl((P_a \otimes \mathbb{I}_{\mathsf{Z}}) \rho)$$를 정규화하여 얻어집니다.

### 4.3 일반화
우리는 이 설명을 시스템 순서가 역전되거나 시스템이 세 개 이상일 때와 같은 다른 상황에 적용할 수 있습니다. 개념적으로는 간단하지만, 공식을 작성하는 것이 번거로워질 수 있습니다.

일반적으로, 우리가 $r$개의 시스템 $X_1,\ldots,X_r$을 가지고 있고, 복합 시스템 $(X_1,\ldots,X_r)$의 상태가 $\rho$이며, 측정 $\\{P_0,\ldots,P_{m-1}\\}$이 $X_k$에 대해 수행된다면, 다음이 발생합니다.

1\. 각 결과 $a$는 다음 확률로 나타납니다.
<p>$$p(a) = \operatorname{Tr}\bigl((\mathbb{I}_{\mathsf{X}_1}\otimes \cdots \otimes\mathbb{I}_{\mathsf{X}_{k-1}} \otimes P_a \otimes \mathbb{I}_{\mathsf{X}_{k+1}} \otimes \cdots \otimes\mathbb{I}_{\mathsf{X}_r}) \rho\bigr)$$</p>

2\. 결과 $a$를 얻는다는 조건 하에, $(X_1,\ldots,X_{k-1},X_{k+1},\ldots,X_r)$의 상태는 그 다음 다음 밀도 행렬로 표현됩니다.
<p>$$\frac{\operatorname{Tr}_{\mathsf{X}_k}\bigl((\mathbb{I}_{\mathsf{X}_1}\otimes \cdots \otimes\mathbb{I}_{\mathsf{X}_{k-1}} \otimes P_a \otimes \mathbb{I}_{\mathsf{X}_{k+1}} \otimes \cdots \otimes\mathbb{I}_{\mathsf{X}_r}) \rho\bigr)}{\operatorname{Tr}\bigl((\mathbb{I}_{\mathsf{X}_1}\otimes \cdots \otimes\mathbb{I}_{\mathsf{X}_{k-1}} \otimes P_a \otimes \mathbb{I}_{\mathsf{X}_{k+1}} \otimes \cdots \otimes\mathbb{I}_{\mathsf{X}_r}) \rho\bigr)}$$</p>

--- 

# 나이마크 정리 (Naimark's theorem)

*나이마크 정리*(Naimark's theorem)는 측정에 관한 근본적인 사실입니다. 이 정리는 모든 일반적인 측정이 채널의 **스타인스프링 표현**(Stinespring representations of channels)을 연상시키는 간단한 방식으로 구현될 수 있다고 명시합니다:
1. 측정될 시스템은 먼저 초기화된 작업 공간 시스템과 결합하여 복합 시스템을 형성합니다.
2. 그런 다음 복합 시스템에 유니터리 연산이 수행됩니다.
3. 마지막으로, 작업 공간 시스템은 **표준 기저 측정**(standard basis measurement)에 따라 *측정*되며, 이는 원래 일반 측정의 결과를 산출합니다.

## 1. 정리 진술 및 증명 (Theorem statement and proof)
$\mathsf{X}$를 시스템이라고 하고, 다음을 만족하는 양의 준정부호 행렬(positive semidefinite matrices)의 모음을 $\\{P_0, \ldots, P_{m-1}\\}$라고 합시다.
<p>$$
P_0 + \cdots + P_{m-1} = \mathbb{I}_{\mathsf{X}} \text{},
$$</p>

이는 그것들이 $\mathsf{X}$의 측정을 설명한다는 의미입니다. 또한 $\mathsf{Y}$를 고전적 상태 집합이 $\\{0, \ldots, m-1\\}$인 시스템이라고 합시다. 이는 이 측정의 가능한 결과 집합입니다.

나이마크 정리는 복합 시스템 $(\mathsf{Y},\mathsf{X})$에 대한 유니터리 연산 $U$가 존재하여, 다음 그림에서 제시된 구현이 주어진 측정 $\\{P_0, \ldots, P_{m-1}\\}$와 일치하는 측정 결과를 산출하며, 이는 가능한 상이한 측정 결과에 대한 확률이 정확히 일치함을 의미한다고 명시합니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/general-measurements/Naimark.svg" style="width:60%">

명확히 하자면, 시스템 $\mathsf{X}$는 임의의 상태 $\rho$로 시작하며, $\mathsf{Y}$는 $\|\mathbf{0}\rangle$ 상태로 초기화됩니다. 유니터리 연산 $U$가 $(\mathsf{Y},\mathsf{X})$에 적용된 후, 시스템 $\mathsf{Y}$는 표준 기저 측정으로 측정되어 $a\in\\{0,\ldots,m-1\\}$인 어떤 결과(outcome)를 산출합니다.

시스템 $\mathsf{X}$는 회로 출력의 일부로 그려지지만, 지금은 $U$가 수행된 후의 $\mathsf{X}$의 상태에 대해 신경 쓰지 않고, 그것이 트레이스 아웃(traced out)된다고 상상할 수 있습니다. 다만, 우리는 이 레슨의 후반부에서 $U$가 수행된 후의 $\mathsf{X}$의 상태에 관심을 가질 것입니다.

이러한 방식으로 측정을 구현하는 것은 채널의 스타인스프링 표현을 분명히 연상시키며, 수학적 기초 또한 유사합니다. 여기서의 차이점은 스타인스프링 표현의 경우처럼 작업 공간 시스템이 트레이스 아웃되는 대신 측정된다는 것입니다.

모든 측정이 이러한 방식으로 구현될 수 있다는 사실은 증명하기 매우 간단하지만, 먼저 양의 준정부호 행렬에 관한 사실이 필요합니다.

>**사실 (Fact)**
>
$P$가 $n \times n$ 양의 준정부호 행렬이라고 가정해 봅시다. $Q^2 = P$인 고유한 $n \times n$ 양의 준정부호 행렬 $Q$가 존재합니다. 이 고유한 양의 준정부호 행렬은 $P$의 *제곱근*(square root)이라고 불리며 $\sqrt{P}$로 표기됩니다.

양의 준정부호 행렬의 제곱근을 찾는 한 가지 방법은 먼저 스펙트럼 분해(spectral decomposition)를 계산하는 것입니다.

<p>$$
P = \sum_{k=0}^{n-1} \lambda_k |\psi_k\rangle \langle \psi_k| \text{}
$$</p>

$P$가 양의 준정부호이므로, 그것의 고윳값은 음이 아닌 실수여야 하며, 이를 제곱근으로 대체함으로써 $\sqrt{P}$의 제곱근에 대한 표현을 얻습니다.
<p>$$
\sqrt{P} = \sum_{k=0}^{n-1} \sqrt{\lambda_k} |\psi_k\rangle \langle \psi_k| \text{}
$$</p>

이 개념을 염두에 두고, 우리는 나이마크 정리를 증명할 준비가 되었습니다. $\mathsf{X}$가 $n$개의 고전적 상태를 가진다고 가정하면, 쌍 $(\mathsf{Y},\mathsf{X})$에 대한 유니터리 연산 $U$는 $nm\times nm$ 행렬로 표현될 수 있으며, 이는 블록이 $n \times n$인 $m \times m$ 블록 행렬로 간주될 수 있습니다. 증명의 핵심은 $U$를 다음 패턴과 일치하는 임의의 유니터리 행렬로 취하는 것입니다.
<p>$$
U = \begin{pmatrix} \sqrt{P_0} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \sqrt{P_1} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] \sqrt{P_{m-1}} & \fbox{?} & \cdots & \fbox{?} \end{pmatrix} \text{}
$$</p>

물음표로 표시된 블록을 채워 $U$가 유니터리가 되도록 하는 것이 가능하기 위해서는, 블록 $\sqrt{P_0}, \ldots, \sqrt{P_{m-1}}$로 형성된 처음 $n$개의 열이 정규직교(orthonormal)인 것이 필요충분조건입니다. 그러면 우리는 이전 레슨에서 접했던 것처럼 그람-슈미트 직교화 과정(Gram-Schmidt orthogonalization process)을 사용하여 나머지 열들을 채울 수 있습니다.

$U$의 처음 $n$개의 열은 다음 방식과 같이 벡터로 표현될 수 있으며, 여기서 $c = 0, \ldots, n-1$은 $0$부터 시작하는 열 번호를 나타냅니다.

<p>$$
|\gamma_c\rangle = \sum_{a = 0}^{m-1} |a\rangle \otimes \sqrt{P_a} |c\rangle \text{}
$$</p>

우리는 이들 중 임의의 두 벡터 사이의 내적을 다음과 같이 계산할 수 있습니다.
<p>$$
\langle \gamma_c | \gamma_d \rangle = \sum_{a,b = 0}^{m-1} \langle a | b \rangle \cdot \langle c | \sqrt{P_a}\sqrt{P_b}\, | d\rangle = \langle c | \Biggl(\sum_{a = 0}^{m-1} P_a \Biggr) | d\rangle = \langle c | d\rangle \text{}
$$</p>

이는 이 열들이 실제로 정규직교함을 보여주며, 따라서 우리는 전체 행렬이 유니터리가 되도록 보장하는 방식으로 $U$의 나머지 열들을 채울 수 있습니다.

이제 시뮬레이션의 측정 결과 확률이 원래 측정과 일치하는지 확인하는 것만 남았습니다. $\mathsf{X}$의 주어진 초기 상태 $\rho$에 대해, 모음 $\\{P_0, \ldots, P_{m-1}\\}$에 의해 설명되는 측정은 각 결과 $a \in \\{0, \ldots, m-1\\}$를 확률 $\operatorname{Tr}(P_a \rho)$로 산출합니다.

시뮬레이션의 결과 확률을 얻기 위해, 먼저 $U$가 수행된 후의 $(\mathsf{Y},\mathsf{X})$의 상태에 $\sigma$라는 이름을 부여합시다. 이 상태는 다음과 같이 표현될 수 있습니다.

<p>$$
\sigma = U \bigl(|0\rangle \langle 0| \otimes \rho\bigr) U^{\dagger} = \sum_{a,b=0}^{m-1} |a\rangle \langle b| \otimes \sqrt{P_a} \rho \sqrt{P_b} \text{}
$$
</p>

동등하게, 블록 행렬 형태로 다음 방정식을 얻습니다.
<p>$$
\begin{aligned} \sigma & = \begin{pmatrix} \sqrt{P_0} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \sqrt{P_1} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] \sqrt{P_{m-1}} & \fbox{?} & \cdots & \fbox{?} \end{pmatrix} \begin{pmatrix} \rho & 0 & \cdots & 0 \\[1mm] 0 & 0 & \cdots & 0 \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] 0 & 0 & \cdots & 0 \end{pmatrix} \begin{pmatrix} \sqrt{P_0} & \sqrt{P_1} & \cdots & \sqrt{P_{m-1}} \\[1mm] \fbox{?} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] \fbox{?} & \fbox{?} & \cdots & \fbox{?} \end{pmatrix}\\[5mm] & = \begin{pmatrix} \sqrt{P_0}\rho\sqrt{P_0} & \cdots & \sqrt{P_0}\rho\sqrt{P_{m-1}} \\[1mm] \vdots & \ddots & \vdots\\[1mm] \sqrt{P_{m-1}}\rho\sqrt{P_0} & \cdots & \sqrt{P_{m-1}}\rho\sqrt{P_{m-1}} \end{pmatrix} \end{aligned} \text{}
$$</p>

물음표로 표시된 블록에 해당하는 $U$의 항목들은 우리가 $\|0\rangle \langle 0\| \otimes \rho$ 형태의 행렬을 켤레 변환(conjugating)한다는 사실 때문에 결과에 영향을 미치지 않는다는 점에 주목하십시오. 따라서 행렬 곱셈이 계산될 때 물음표 항목들은 항상 $\|0\rangle \langle 0\| \otimes \rho$의 영(zero) 항목과 곱해집니다.

이제 $\mathsf{Y}$에 표준 기저 측정이 수행될 때 발생하는 상황을 분석할 수 있습니다. 가능한 결과의 확률은 $\mathsf{Y}$의 축소 상태(reduced state) $\sigma_{\mathsf{Y}}$의 대각선 항목으로 주어집니다.
<p>
$$
\sigma_{\mathsf{Y}} = \sum_{a,b=0}^{m-1} \operatorname{Tr}\Bigl(\sqrt{P_a} \rho \sqrt{P_b}\Bigr) |a\rangle \langle b| \text{}
$$</p>
특히, 트레이스(trace)의 순환 속성(cyclic property)을 사용하여, 주어진 결과 $a \in \{0, \ldots, m-1\}$를 얻을 확률은 다음과 같음을 알 수 있습니다.
<p>$$
\langle a | \sigma_{\mathsf{Y}} | a \rangle = \operatorname{Tr}\Bigl(\sqrt{P_a} \rho \sqrt{P_a}\Bigr) = \operatorname{Tr}(P_a \rho) \text{}
$$</p>

이는 원래 측정과 일치하며, 시뮬레이션의 정확성을 입증합니다.

## 2. 비파괴 측정 (Non-destructive measurements)
이 레슨에서 지금까지 우리는 *파괴적인(destructive)* 측정에 관심을 가져왔는데, 여기서 출력은 고전적인 측정 결과 자체로만 구성되며 측정된 시스템의 측정 후 양자 상태에 대한 명세(specification)는 없습니다.

반면에, *비파괴적인(Non-destructive)* 측정은 정확히 이를 수행합니다. 구체적으로, 비파괴 측정은 고전적인 측정 결과 확률뿐만 아니라, 가능한 각 측정 결과에 조건화된 측정된 시스템의 상태도 설명합니다. *비파괴적*이라는 용어는 측정되는 *시스템*을 지칭하지만, 반드시 그 상태를 지칭하는 것은 아니며, 측정의 결과로 상태가 상당히 바뀔 수도 있음에 유의하십시오.

일반적으로, 주어진 파괴적인 측정에 대해, 주어진 파괴적인 측정과 *호환되는(compatible)* 다수의 (사실상 무한히 많은) 비파괴 측정이 존재할 것입니다. 이는 고전적인 측정 결과 확률이 파괴적인 측정과 정확히 일치함을 의미합니다. 따라서 주어진 측정에 대해 시스템의 측정 후 양자 상태를 정의하는 유일한 방법은 없습니다.

사실, 비파괴 측정을 훨씬 더 일반화하여, 입력 시스템과 반드시 동일하지 않은 시스템의 양자 상태 출력과 함께 고전적인 측정 결과를 생성하도록 할 수 있습니다.

비파괴 측정의 개념은 흥미롭고 유용한 추상화입니다. 그러나 비파괴 측정은 항상 채널과 파괴 측정의 합성(compositions of channels and destructive measurements)으로 설명될 수 있다는 점을 인식해야 합니다. 따라서 파괴 측정의 개념이 더 근본적이라는 의미가 있습니다.

### 2.1 나이마크 정리로부터 (From Naimark's theorem)

나이마크 정리에 있는 것과 같은 일반적인 측정의 시뮬레이션을 고려하십시오. 이 시뮬레이션으로부터 비파괴 측정을 얻는 간단한 방법은 이전 그림에서 드러나는데, 여기서 시스템 $\mathsf{X}$는 트레이스 아웃되지 않고 출력의 일부입니다. 이는 고전적인 측정 결과 $a \in \\{0, \ldots, m-1\\}$와 $\mathsf{X}$의 측정 후 양자 상태를 모두 산출합니다.

이러한 상태를 수학적 용어로 설명해 봅시다. 우리는 $\mathsf{X}$의 초기 상태가 $\rho$이고, 초기화된 시스템 $\mathsf{Y}$가 도입되고 $U$가 수행된 후 $(\mathsf{Y},\mathsf{X})$가 다음 상태에 있다고 가정하고 있습니다.

<p>$$
\sigma = U \bigl(|0\rangle \langle 0| \otimes \rho\bigr) U^{\dagger} = \sum_{a,b=0}^{m-1} |a\rangle \langle b| \otimes \sqrt{P_a} \rho \sqrt{P_b} \text{}.
$$</p>

상이한 고전적 결과가 나타날 확률은 이전과 동일합니다. 우리가 $\mathsf{X}$를 무시하기로 결정하든 무시하지 않기로 결정하든 그 결과로 인해 확률이 변경될 수는 없습니다. 즉, 우리는 각 $a \in \\{0, \ldots, m-1\\}$를 확률 $\operatorname{Tr}(P_a \rho)$로 얻습니다.

특정 측정 결과 $a$를 얻었다는 조건 하에, $\mathsf{X}$의 결과 상태는 이 표현으로 주어집니다.

<p>$$
\frac{\sqrt{P_a} \rho \sqrt{P_a}}{\operatorname{Tr}(P_a \rho)} \text{}
$$</p>

이를 보는 한 가지 방법은 $\mathsf{Y}$의 표준 기저 측정을 완전 디페이징 채널(completely dephasing channel) $\Delta_m$로 표현하는 것입니다. 여기서 채널 출력은 고전적 측정 결과를 (대각선) 밀도 행렬로 설명합니다. 우리가 얻는 상태의 표현은 다음과 같습니다.

<p>$$
\sum_{a,b=0}^{m-1} \Delta_m(|a\rangle \langle b|) \otimes \sqrt{P_a} \rho \sqrt{P_b} = \sum_{a=0}^{m-1} |a\rangle \langle a| \otimes \sqrt{P_a} \rho \sqrt{P_a} \text{}.
$$</p>

그런 다음 이 상태를 곱 상태(product states)의 볼록 조합(convex combination)으로 작성할 수 있으며,

<p>$$
\sum_{a=0}^{m-1} \operatorname{Tr}(P_a \rho)\, |a\rangle \langle a| \otimes \frac{\sqrt{P_a} \rho \sqrt{P_a}}{\operatorname{Tr}(P_a \rho)} \text{},
$$</p>
이는 가능한 각 측정 결과에 조건화된 $\mathsf{X}$의 상태에 대해 우리가 얻은 표현과 일치합니다.

### 2.2 크라우스 표현으로부터 (From a Kraus representation)
나이마크 정리의 맥락에서 $U$에 대한 대체 선택 사항들이 있으며, 이는 동일한 측정 결과 확률을 생성하지만 $\mathsf{X}$의 완전히 다른 출력 상태를 제공합니다.

예를 들어, 한 가지 옵션은 $U$ 대신 $(\mathbb{I}_{\mathsf{Y}} \otimes V) U$를 대입하는 것인데, 여기서 $V$는 $\mathsf{X}$에 대한 임의의 유니터리 연산입니다. $\mathsf{X}$에 대한 $V$의 적용은 $\mathsf{Y}$의 측정과 교환 가능(commutes)하므로 고전적 결과 확률은 변하지 않지만, 이제 결과 $a$에 조건화된 $\mathsf{X}$의 상태는 다음과 같이 됩니다.

<p>$$
\frac{V \sqrt{P_a} \rho \sqrt{P_a}V^{\dagger}}{\operatorname{Tr}(P_a \rho)} \text{}.
$$</p>

더 일반적으로, 우리는 $\mathsf{X}$에 대한 임의의 유니터리 연산 $V_0, \ldots, V_{m-1}$의 선택에 대해 $U$를 유니터리 행렬

<p>$$
\Biggl(\sum_{a=0}^{m-1} |a\rangle\langle a| \otimes V_a\Biggr) U \text{}
$$</p>

로 대체할 수 있습니다. 다시 말하지만, 고전적 결과 확률은 변하지 않지만, 이제 결과 $a$에 조건화된 $\mathsf{X}$의 상태는 다음과 같이 됩니다.
<p>$$
\frac{V_a \sqrt{P_a} \rho \sqrt{P_a}V_a^{\dagger}}{\operatorname{Tr}(P_a \rho)} \text{}.
$$</p>

이러한 자유를 표현하는 동등한 방법은 크라우스 표현(Kraus representations)과 연결되어 있습니다. 즉, 우리는 $n$개의 고전적 상태를 가진 시스템의 $m$-결과 비파괴 측정을 크라우스 행렬에 대한 일반적인 조건을 만족하는 $n \times n$ 크라우스 행렬 $A_0, \ldots, A_{m-1}$의 선택으로 설명할 수 있습니다.
<p>$$
\sum_{a = 0}^{m-1} A_a^{\dagger} A_a = \mathbb{I}_{\mathsf{X}} \tag{1} \text{}
$$</p>
$\mathsf{X}$의 초기 상태가 $\rho$라고 가정하면, 고전적 측정 결과는 확률
<p>$$
\operatorname{Tr}\bigl(A_a \rho A_a^{\dagger}\bigr) = \operatorname{Tr}\bigl(A_a^{\dagger} A_a \rho \bigr) \text{}
$$</p>
로 $a$이며, 결과가 $a$이라는 조건 하에 $\mathsf{X}$의 상태는 다음과 같이 됩니다.
<p>$$
\frac{A_a \rho A_a^{\dagger}}{\operatorname{Tr}(A_a^{\dagger}A_a \rho)} \text{}.
$$</p>
이는 나이마크 정리에서 유니터리 연산 $U$를 다음과 같이 선택하는 것과 동등하다는 점에 주목하십시오.

<p>$$
U = \begin{pmatrix} A_{0} & \fbox{?} & \cdots & \fbox{?} \\[1mm] A_{1} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] A_{m-1} & \fbox{?} & \cdots & \fbox{?} \end{pmatrix} \text{}
$$</p>

이전 레슨에서 우리는 블록 $A_0, \ldots, A_{m-1}$로 형성된 열들이 조건 (1) 때문에 반드시 직교(orthogonal)한다는 것을 확인했습니다.

### 2.3 일반화 (Generalizations)
우리가 논의한 방식보다 비파괴 측정을 공식화하는 훨씬 더 일반적인 방법들이 있습니다. *양자 도구*(quantum instrument)의 개념(여기서는 설명되지 않음)이 이를 수행하는 한 가지 방법을 나타냅니다.


----

# 양자 상태 식별 및 단층 촬영 (Quantum state discrimination and tomography)

이 레슨의 마지막 부분에서, 우리는 측정과 관련된 두 가지 작업, 즉 **양자 상태 식별**(*quantum state discrimination*)과 **양자 상태 단층 촬영**(*quantum state tomography*)을 간략하게 고려할 것입니다.

1\. **양자 상태 식별**(*Quantum state discrimination*) \
양자 상태 식별을 위해, 우리는 알려진 양자 상태들의 모음 $\rho_0,\ldots,\rho_{m-1}$과 이 상태들과 관련된 확률 $p_0,\ldots,p_{m-1}$을 가지고 있습니다. 이를 표현하는 간결한 방법은 우리가 양자 상태들의 *앙상블*(*ensemble*)을 가지고 있다고 말하는 것입니다.
<p>$$
\{(p_0,\rho_0),\ldots,(p_{m-1},\rho_{m-1})\}
$$</p>

숫자 $a\in\\{0,\ldots,m-1\\}$는 확률 $(p_0,\ldots,p_{m-1})$에 따라 무작위로 선택되며, 시스템 $\mathsf{X}$는 상태 $\rho_a$로 준비됩니다. 목표는 $\mathsf{X}$만의 측정을 통해 어떤 $a$ 값이 선택되었는지 결정하는 것입니다.

따라서, 우리는 유한한 수의 대안을 가지고 있으며, 각각의 $a$가 선택될 확률에 대한 우리의 지식인 *사전 확률*(*prior*)과 함께, 실제로 어떤 대안이 발생했는지 결정하는 것이 목표입니다. 이는 일부 상태 및 확률 선택에 대해서는 쉬울 수 있으며, 다른 경우에는 오류를 범할 가능성 없이 불가능할 수도 있습니다.

2\. **양자 상태 단층 촬영**(*Quantum state tomography*) \
양자 상태 단층 촬영을 위해, 우리는 시스템의 *알려지지 않은*(*unknown*) 양자 상태를 가지고 있습니다. 따라서 양자 상태 식별에서와는 달리 일반적으로 사전 확률이나 가능한 대안에 대한 정보는 없습니다.

하지만 이번에는 상태의 단일 복사본이 제공되는 것이 아니라, 오히려 많은 *독립적인*(*independent*) 복사본이 제공됩니다. 즉, $N$개의 동일한 시스템 $\mathsf{X}_1,\ldots,\mathsf{X}_N$ 각각은 어떤 (아마도 큰) 수 $N$에 대해 독립적으로 상태 $\rho$로 준비됩니다. 목표는 시스템들을 측정함으로써 밀도 행렬로서 알려지지 않은 상태의 근사치를 찾는 것입니다.

## 1. 두 상태를 식별하기 (Discriminating between two states)
양자 상태 식별의 가장 간단한 경우는 식별되어야 할 두 상태 $\rho_0$와 $\rho_1$이 있는 경우입니다.

비트 $a$가 무작위로 선택되는 상황을 상상해 봅시다: 확률 $p$로 $a=0$이고 확률 $1-p$로 $a=1$입니다. 시스템 $\mathsf{X}$는 $a$ 값에 따라 $\rho_0$ 또는 $\rho_1$을 의미하는 상태 $\rho_a$로 준비되어 우리에게 주어집니다. 우리의 목표는 $\mathsf{X}$에 대한 측정을 통해 $a$의 값을 정확하게 추측하는 것입니다. 정확히 말하자면, 우리는 우리의 추측이 정확할 확률을 최대화하는 것을 목표로 할 것입니다.

### 1.1 최적 측정 (An optimal measurement)
이 문제를 해결하는 최적의 방법은 $\rho_0$와 $\rho_1$ 사이의 가중 차이(weighted difference)를 스펙트럼 분해하는 것으로 시작하며, 여기서 가중치는 해당 확률입니다.
<p>$$
p \rho_0 - (1-p) \rho_1 = \sum_{k = 0}^{n-1} \lambda_k |\psi_k\rangle \langle \psi_k|
$$</p>

이 표현에는 더하기 기호가 아닌 빼기 기호가 있다는 점에 유의하십시오: 이것은 가중 *합*이 아니라 가중 *차이*입니다.

우리는 다음과 같이 투영 측정(projective measurement) $\\{\Pi_0, \Pi_1\\}$를 선택함으로써 정확한 추측의 확률을 최대화할 수 있습니다. 먼저 $\\{0, \ldots, n-1\\}$의 원소를 가중 차이의 해당 고유값이 음이 아닌지 또는 음수인지에 따라 두 개의 서로소 집합 $S_0$와 $S_1$로 나눕시다.
<p>$$
\begin{gathered}
S_0 = \{k\in\{0,\ldots,n-1\} : \lambda_k \geq 0 \}\\[2mm]
S_1 = \{k\in\{0,\ldots,n-1\} : \lambda_k < 0 \}
\end{gathered}
$$</p>

그런 다음 다음과 같이 *투영*(*projective*) 측정을 선택할 수 있습니다.
<p>$$
\Pi_0 = \sum_{k \in S_0} |\psi_k\rangle \langle \psi_k| \quad\text{and}\quad \Pi_1 = \sum_{k \in S_1} |\psi_k\rangle \langle \psi_k|
$$</p>

($\lambda_k = 0$인 $k$ 값들을 $S_0$ 또는 $S_1$ 중 어느 집합에 포함하든 실제로 중요하지 않습니다. 여기서는 임의로 이 값들을 $S_0$에 포함하도록 선택하고 있습니다.)

이는 선택된 상태를 부정확하게 결정할 확률을 최소화하는 현재 상황에서 **최적의 측정**입니다.

### 1.2 정확도 확률 (Correctness probability)
이제 우리는 측정 $\\{\Pi_0, \Pi_1\\}$에 대한 정확도 확률을 결정할 것입니다.
시작하기 위해, 우리는 $\Pi_0$와 $\Pi_1$에 대해 우리가 만든 특정 선택에 대해 실제로 걱정할 필요는 없지만, 염두에 두는 것은 도움이 될 수 있습니다. *어떤* 측정 $\\{P_0, P_1\\}$ (반드시 투영일 필요는 없음)에 대해서도 우리는 정확도 확률을 다음과 같이 쓸 수 있습니다.
<p>$$
p \operatorname{Tr}(P_0 \rho_0) + (1 - p) \operatorname{Tr}(P_1 \rho_1)
$$</p>

$\\{P_0, P_1\\}$이 측정이라는 사실, 즉 $P_1 = \mathbb{I} - P_0$라는 사실을 사용하여, 우리는 이 표현을 다음과 같이 다시 쓸 수 있습니다.

<p>$$\begin{aligned} & p \operatorname{Tr}(P_0 \rho_0) + (1 - p) \operatorname{Tr}((\mathbb{I} - P_0) \rho_1) \\
&= p \operatorname{Tr}(P_0 \rho_0) - (1 - p) \operatorname{Tr}(P_0 \rho_1) + (1-p) \operatorname{Tr}(\rho_1)\\
&= \operatorname{Tr}\bigl( P_0 (p \rho_0 - (1-p)\rho_1) \bigr) + 1 - p
\end{aligned}$$</p>

다른 한편으로, 우리는 대신 치환 $P_0 = \mathbb{I} - P_1$를 할 수도 있었습니다. 이는 값을 바꾸지는 않지만, 대안적인 표현을 제공합니다.
<p>$$\begin{aligned}
& p \operatorname{Tr}((\mathbb{I} - P_1) \rho_0) + (1 - p) \operatorname{Tr}(P_1 \rho_1)\\
& = p \operatorname{Tr}(\rho_0) - p \operatorname{Tr}(P_1 \rho_0) + (1 - p) \operatorname{Tr}(P_1 \rho_1)\\[1mm]
& = p - \operatorname{Tr}\bigl( P_1 (p \rho_0 - (1-p)\rho_1) \bigr)
\end{aligned}
$$</p>

두 표현식은 동일한 값을 가지므로, 우리는 이 값에 대한 또 다른 표현을 제공하기 위해 두 표현식을 평균화할 수 있습니다. (두 표현식을 평균화하는 것은 결과 표현식을 단순화하기 위한 속임수일 뿐입니다.)
<p>$$\begin{aligned}
& \frac{1}{2} \bigl(\operatorname{Tr}\bigl( P_0 (p \rho_0 - (1-p)\rho_1) \bigr) + 1-p\bigr) + \frac{1}{2} \bigl(p - \operatorname{Tr}\bigl( P_1 (p \rho_0 - (1-p)\rho_1) \bigr)\bigr)\\
& = \frac{1}{2} \operatorname{Tr}\bigl( (P_0-P_1) (p \rho_0 - (1-p)\rho_1)\bigr) + \frac{1}{2} \end{aligned}
$$</p>

이제 우리는 왜 (위에 명시된 대로) 투영 $\Pi_0$와 $\Pi_1$을 각각 $P_0$와 $P_1$로 선택하는 것이 합리적인지 알 수 있습니다. 왜냐하면 그것이 최종 표현식에서 트레이스(trace)를 가능한 한 크게 만들 수 있는 방법이기 때문입니다. 특히,
<p>$$
(\Pi_0-\Pi_1) (p \rho_0 - (1-p)\rho_1) = \sum_{k = 0}^{n-1} |\lambda_k| \cdot |\psi_k\rangle \langle \psi_k|.
$$</p>

따라서, 우리가 트레이스를 취할 때, 우리는 고유값의 **절댓값**(*absolute values*)의 합을 얻습니다. 이는 가중 차이의 **트레이스 노름**(*trace norm*)으로 알려진 것과 같습니다.
<p>$$
\operatorname{Tr}\bigl( (\Pi_0-\Pi_1) (p \rho_0 - (1-p)\rho_1)\bigr) = \sum_{k = 0}^{n-1} |\lambda_k| = \| p \rho_0 - (1-p)\rho_1 \|_1
$$</p>

따라서, 측정 $\{\Pi_0, \Pi_1\}$가 확률 $p$와 $1-p$로 주어진 $\rho_0$와 $\rho_1$을 **정확하게 식별할 확률**은 다음과 같습니다.
<p>$$
\frac{1}{2} + \frac{1}{2} \| p \rho_0 - (1-p)\rho_1 \|_1
$$</p>

이것이 확률 $p$와 $1-p$로 주어진 $\rho_0$와 $\rho_1$의 정확한 식별을 위한 **최적의 확률**이라는 사실은 일반적으로 *헬스트롬-홀레보 정리*(*Helstrom–Holevo theorem*) (또는 때로는 단순히 *헬스트롬의 정리*(*Helstrom's theorem*))라고 불립니다.

## 2. 세 개 이상의 상태 식별하기 (Discriminating three or more states)
세 개 이상의 상태가 있는 양자 상태 식별의 경우, 최적 측정에 대한 알려진 닫힌 형식(closed-form) 해법은 없지만, 이 문제를 반정부호 계획법(semidefinite program)으로 공식화하는 것은 가능합니다. 이는 컴퓨터의 도움으로 최적 측정의 효율적인 수치적 근사치를 가능하게 합니다.

또한 *홀레보-위엔-케네디-랙스*(*Holevo-Yuen-Kennedy-Lax*) 조건으로 알려진 조건을 통해 상태 식별 작업에서 주어진 측정의 최적성을 *검증*하거나 (*falsify*) *반증*할 수 있습니다. 특히, 앙상블
<p>$$
\{(p_0,\rho_0),\ldots,(p_{m-1},\rho_{m-1})\}\text{,}
$$</p>

로 정의된 상태 식별 작업의 경우, 측정 $\\{P_0, \ldots, P_{m-1}\\}$은 행렬 $Q_a$가 모든 $a \in \\{0, \ldots, m-1\\}$에 대해 **양의 준정부호**(positive semidefinite)인 경우에만 **최적**입니다.
<p>$$
Q_a = \sum_{b = 0}^{m-1} p_b \rho_b P_b - p_a \rho_a
$$</p>

예를 들어, 네 개의 정사면체 상태(tetrahedral states) $|\phi_0\rangle, \ldots, |\phi_3\rangle$ 중 하나가 균일하게 무작위로 선택되는 양자 상태 식별 작업을 고려해 봅시다. 정사면체 측정 $\{P_0, P_1, P_2, P_3\}$은 확률
<p>$$
\frac{1}{4} \operatorname{Tr}(P_0 |\phi_0\rangle\langle \phi_0 |) + \frac{1}{4} \operatorname{Tr}(P_1 |\phi_1\rangle\langle \phi_1 |) + \frac{1}{4} \operatorname{Tr}(P_2 |\phi_2\rangle\langle \phi_2 |) + \frac{1}{4} \operatorname{Tr}(P_3 |\phi_3\rangle\langle \phi_3 |) = \frac{1}{2} \text{.}
$$</p>

로 성공합니다.
계산 결과
<p>$$
Q_a = \frac{1}{4}(\mathbb{I} - |\phi_a\rangle\langle\phi_a|) \geq 0
$$</p>

이 $a = 0, 1, 2, 3$에 대해 성립하므로, 이것은 홀레보-위엔-케네디-랙스 조건에 의해 **최적**입니다.

## 3. 양자 상태 단층 촬영 (Quantum state tomography)
마지막으로, 우리는 *양자 상태 단층 촬영*(*quantum state tomography*) 문제를 간략하게 논의할 것입니다. 이 문제에서, 우리는 알려지지 않은 양자 상태 $\rho$의 많은 수 $N$의 독립적인 복사본을 제공받으며, 목표는 $\rho$의 근사치 $\tilde{\rho}$를 재구성하는 것입니다. 분명히 말하자면, 이것은 우리가 $\rho$에 가능한 한 가까운 밀도 행렬 $\tilde{\rho}$의 고전적 설명을 찾고자 함을 의미합니다.

우리는 이 설정을 다음과 같은 방식으로 다르게 설명할 수 있습니다. 알려지지 않은 밀도 행렬 $\rho$가 선택되고, 우리는 $N$개의 양자 시스템 $\mathsf{X}_1,\ldots,\mathsf{X}_N$에 접근할 수 있으며, 이들 각각은 *독립적으로* 상태 $\rho$로 준비되었습니다. 따라서 복합 시스템 $(\mathsf{X}_1,\ldots,\mathsf{X}_N)$의 상태는
<p>$$
\rho^{\otimes N} = \rho \otimes \rho \otimes \cdots \otimes \rho \quad \text{($N$ times)}
$$</p>

입니다.

목표는 시스템 $\mathsf{X}_1,\ldots,\mathsf{X}_N$에 대한 측정을 수행하고, 그 측정 결과에 기초하여 $\rho$를 근접하게 근사하는 밀도 행렬 $\tilde{\rho}$를 계산하는 것입니다. 이것은 매혹적인 문제임이 밝혀졌으며 이에 대한 연구가 진행 중입니다.

문제에 접근하기 위한 다양한 유형의 전략이 고려될 수 있습니다. 예를 들어, 우리는 시스템 $\mathsf{X}_1,\ldots,\mathsf{X}_N$ 각각이 차례로 개별적으로 측정되어 일련의 측정 결과를 생성하는 전략을 상상할 수 있습니다. 어떤 측정을 수행할지에 대한 다양한 특정 선택이 이루어질 수 있으며, 여기에는 *적응적*(*adaptive*) 선택과 *비적응적*(*non-adaptive*) 선택이 포함됩니다. 다시 말해, 특정 시스템에서 수행되는 측정의 선택은 이전 측정 결과에 따라 달라질 수도 있고 달라지지 않을 수도 있습니다. 측정 결과의 순서에 기초하여, 상태 $\rho$에 대한 추측 $\tilde{\rho}$가 도출됩니다. 그리고 이를 수행하는 데에도 다양한 방법론이 있습니다.

대안적인 접근 방식은 전체 컬렉션에 대한 단일 *공동 측정*(*joint measurement*)을 수행하는 것입니다. 여기서 우리는 $(\mathsf{X}_1,\ldots,\mathsf{X}_N)$을 단일 시스템으로 생각하고 출력이 상태 $\rho$에 대한 추측 $\tilde{\rho}$인 단일 측정을 선택합니다. 이는 개별 시스템의 개별 측정으로 가능한 것보다 개선된 추정치를 얻을 수 있지만, 모든 시스템에 대한 공동 측정은 구현하기가 훨씬 더 어려울 가능성이 높습니다.

### 3.1 파울리 측정을 이용한 큐비트 단층 촬영 (Qubit tomography using Pauli measurements)
이제 $\rho$가 큐비트 밀도 행렬인 간단한 경우의 양자 상태 단층 촬영을 고려할 것입니다. 우리는 $\mathsf{X}_1,\ldots,\mathsf{X}_N$ 큐비트가 각각 독립적으로 상태 $\rho$에 있으며, 우리의 목표는 $\rho$에 가까운 근사치 $\tilde{\rho}$를 계산하는 것이라고 가정합니다.

우리의 전략은 $N$개의 큐비트 $\mathsf{X}_1,\ldots,\mathsf{X}_N$를 세 개의 거의 같은 크기의 컬렉션으로 나누는 것입니다. 각각은 세 개의 파울리 행렬 $\sigma_x$, $\sigma_y$, $\sigma_z$ 중 하나와 관련됩니다. 그런 다음 각 큐비트는 다음과 같이 독립적으로 측정됩니다.


1\. $\sigma_x$와 관련된 컬렉션의 각 큐비트에 대해 우리는 $\sigma_x$ 측정을 수행합니다. 이것은 큐비트가 $\sigma_x$의 고유 벡터로 이루어진 정규 직교 기저인 $\{|\!+\!\rangle, |-\!\rangle\}$에 대해 측정됨을 의미하며, 해당하는 측정 결과는 두 고유 벡터와 관련된 고유값입니다: 상태 $|\!+\!\rangle$에 대해서는 $+1$, 상태 $|-\!\rangle$에 대해서는 $-1$입니다. $\sigma_x$와 관련된 컬렉션의 모든 상태에 대한 결과를 평균화함으로써, 우리는 기대값
<p>$$
\langle + | \rho | + \rangle - \langle - | \rho | - \rangle = \operatorname{Tr}(\sigma_x \rho) \text{.}
$$</p>

의 근사치를 얻습니다.

2\. $\sigma_y$와 관련된 컬렉션의 각 큐비트에 대해 우리는 $\sigma_y$ 측정을 수행합니다. 이러한 측정은 $\sigma_x$ 측정과 유사하지만, 측정 기저가 $\sigma_y$의 고유 벡터인 $\{|\!+\!i\rangle, |-\!i\rangle\}$이라는 점만 다릅니다. $\sigma_y$와 관련된 컬렉션의 모든 상태에 대한 결과를 평균화함으로써, 우리는 기대값
<p>$$
\langle +i | \rho | \!+\!i \rangle - \langle -i | \rho | \!-\!i \rangle = \operatorname{Tr}(\sigma_y \rho) \text{.}
$$</p>

의 근사치를 얻습니다.

3\. $\sigma_z$와 관련된 컬렉션의 각 큐비트에 대해 우리는 $\sigma_z$ 측정을 수행합니다. 이번에 측정 기저는 $\sigma_z$의 고유 벡터인 표준 기저 $\{|0\rangle, |1\rangle\}$입니다. $\sigma_z$와 관련된 컬렉션의 모든 상태에 대한 결과를 평균화함으로써, 우리는 기대값
<p>$$
\langle 0 | \rho | 0 \rangle - \langle 1 | \rho | 1 \rangle = \operatorname{Tr}(\sigma_z \rho) \text{.}
$$</p>

의 근사치를 얻습니다.


일단 우리가 다음 근사치들을 얻으면:
<p>$$
\alpha_x \approx \operatorname{Tr}(\sigma_x \rho),\; \alpha_y \approx \operatorname{Tr}(\sigma_y \rho),\; \alpha_z \approx \operatorname{Tr}(\sigma_z \rho)
$$</p>

각 컬렉션의 측정 결과를 평균화하여, 우리는 $\rho$를 다음과 같이 근사할 수 있습니다.
<p>$$
\tilde{\rho} = \frac{\mathbb{I} + \alpha_x \sigma_x + \alpha_y \sigma_y + \alpha_z \sigma_z}{2} \approx \frac{\mathbb{I} + \operatorname{Tr}(\sigma_x \rho) \sigma_x + \operatorname{Tr}(\sigma_y \rho) \sigma_y + \operatorname{Tr}(\sigma_z \rho) \sigma_z}{2} = \rho \text{.}
$$</p>

$N$이 무한대로 접근하는 극한에서, 이 근사치는 *대수의 법칙*(*law of large numbers*)에 의해 실제 밀도 행렬 $\rho$로 확률적으로 수렴하며, 잘 알려진 통계적 경계(예: *회프딩 부등식*(*Hoeffding's inequality*))는 근사치 $\tilde{\rho}$가 $\rho$로부터 다양한 양만큼 벗어날 확률을 제한하는 데 사용될 수 있습니다.

그러나 인식해야 할 중요한 점은 이러한 방식으로 얻은 행렬 $\tilde{\rho}$가 밀도 행렬이 아닐 수도 있다는 것입니다. 특히, $\tilde{\rho}$는 항상 트레이스가 $1$과 같지만, 양의 준정부호(positive semidefinite)가 아닐 수 있습니다. 이러한 근사치 $\tilde{\rho}$를 밀도 행렬로 "반올림"(*rounding*)하는 알려진 다양한 전략이 있으며, 그중 하나는 스펙트럼 분해를 계산하고, 음수 고유값을 $0$으로 대체한 다음, (얻은 행렬을 트레이스로 나누어) 재규격화하는 것입니다.

### 3.2 정사면체 측정을 이용한 큐비트 단층 촬영 (Qubit tomography using the tetrahedral measurement)

큐비트 단층 촬영을 수행하는 또 다른 옵션은 이전에 설명된 **정사면체 측정** $\\{P_0, P_1, P_2, P_3\\}$을 사용하여 모든 큐비트 $\mathsf{X}_1,\ldots,\mathsf{X}_N$를 측정하는 것입니다. 즉,

<p>$$
P_0 = \frac{|\phi_0\rangle \langle \phi_0|}{2}, \quad P_1 = \frac{|\phi_1\rangle \langle \phi_1|}{2}, \quad P_2 = \frac{|\phi_2\rangle \langle \phi_2|}{2}, \quad P_3 = \frac{|\phi_3\rangle \langle \phi_3|}{2}
$$</p>

다음 상태들에 대해:
<p>$$
\begin{aligned}
|\phi_0\rangle & = |0\rangle\\
|\phi_1\rangle & = \frac{1}{\sqrt{3}} |0\rangle + \sqrt{\frac{2}{3}} |1\rangle\\
|\phi_2\rangle & = \frac{1}{\sqrt{3}} |0\rangle + \sqrt{\frac{2}{3}} e^{2\pi i/3} |1\rangle\\
|\phi_3\rangle & = \frac{1}{\sqrt{3}} |0\rangle + \sqrt{\frac{2}{3}} e^{-2\pi i/3} |1\rangle.
\end{aligned}
$$</p>

각 결과는 여러 번 얻어지며, 우리는 이를 $a \in \\{0, 1, 2, 3\\}$ 각각에 대해 $n_a$로 나타낼 것입니다. 따라서 $n_0 + n_1 + n_2 + n_3 = N$입니다. 이 숫자의 $N$에 대한 비율은 각 가능한 결과와 관련된 확률의 추정치를 제공합니다.
<p>$$
\frac{n_a}{N} \approx \operatorname{Tr}(P_a \rho) \text{.}
$$</p>

마지막으로, 우리는 다음의 놀라운 공식을 사용할 것입니다.
<p>$$
\rho = \sum_{a=0}^3 \Bigl( 3 \operatorname{Tr}(P_a \rho) - \frac{1}{2}\Bigr) |\phi_a\rangle \langle \phi_a| \text{.}
$$</p>

이 공식을 확립하기 위해, 우리는 직접 계산을 통해 확인할 수 있는 정사면체 상태의 내적의 절댓값 제곱에 대한 다음 방정식을 사용할 수 있습니다.
<p>$$
\bigl|\langle \phi_a | \phi_b \rangle \bigr|^2 = \begin{cases} 1 & a=b\\ \frac{1}{3} & a\neq b. \end{cases}
$$</p>

네 행렬
<p>$$
\begin{aligned}
|\phi_0\rangle \langle \phi_0| & = \begin{pmatrix} 1 & 0\\[2mm] 0 & 0\end{pmatrix}\\[3mm]
|\phi_1\rangle \langle \phi_1| & = \begin{pmatrix} \frac{1}{3} & \frac{\sqrt{2}}{3}\\[2mm] \frac{\sqrt{2}}{3} & \frac{2}{3}\end{pmatrix}\\[3mm]
|\phi_2\rangle \langle \phi_2| & = \begin{pmatrix} \frac{1}{3} & \frac{\sqrt{2}}{3}e^{-2\pi i/3}\\[2mm] \frac{\sqrt{2}}{3}e^{2\pi i/3} & \frac{2}{3}\end{pmatrix}\\[3mm]
|\phi_3\rangle \langle \phi_3| & = \begin{pmatrix} \frac{1}{3} & \frac{\sqrt{2}}{3}e^{2\pi i/3}\\[2mm] \frac{\sqrt{2}}{3}e^{-2\pi i/3} & \frac{2}{3}\end{pmatrix}
\end{aligned}
$$</p>

는 선형 독립이므로, 공식이 $b = 0, 1, 2, 3$에 대해 $\rho = |\phi_b\rangle\langle\phi_b|$일 때 참임을 증명하는 것으로 충분합니다. 특히,
<p>$$
3 \operatorname{Tr}(P_a |\phi_b\rangle\langle\phi_b|) - \frac{1}{2} = \frac{3}{2} |\langle \phi_a | \phi_b \rangle|^2 - \frac{1}{2} = \begin{cases} 1 & a=b\\ 0 & a\neq b \end{cases}
$$</p>

이며, 따라서
<p>$$
\sum_{a=0}^3 \biggl( 3 \operatorname{Tr}(P_a |\phi_b\rangle\langle\phi_b|) - \frac{\operatorname{Tr}(|\phi_b\rangle\langle\phi_b|)}{2}\biggr) |\phi_a\rangle \langle \phi_a| = |\phi_b\rangle\langle \phi_b| \text{.}
$$</p>

우리는 $\rho$의 근사치에 도달합니다.
<p>$$
\tilde{\rho} = \sum_{a=0}^3 \Bigl( \frac{3 n_a}{N} - \frac{1}{2}\Bigr) |\phi_a\rangle \langle \phi_a| \text{.}
$$</p>
이 근사치는 항상 트레이스가 1과 같은 헤르미트 행렬일 것이지만, 양의 준정부호가 아닐 수도 있습니다. 이 경우, 근사치는 파울리 측정을 포함하는 전략과 유사하게 밀도 행렬로 "반올림"되어야 합니다.
