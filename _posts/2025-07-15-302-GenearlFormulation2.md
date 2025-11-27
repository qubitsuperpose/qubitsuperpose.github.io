---
title: 7차시 2:General formulation of quantum information2(Quantum Channels)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 양자 채널 기본 사항

수학적 용어로, 채널은 특정 요구 사항을 충족하는 밀도 행렬에서 밀도 행렬로의 선형 매핑입니다. 이 레슨 전체에서 우리는 채널을 지칭하기 위해 $\Phi$와 $\Psi$를 포함한 대문자 그리스 문자들과 특정 경우에 일부 다른 문자들을 사용할 것입니다. 모든 채널 $\Phi$는 입력 시스템과 출력 시스템을 가지며, 우리는 일반적으로 $\mathsf{X}$를 입력 시스템을 지칭하는 데 사용하고 $\mathsf{Y}$를 출력 시스템을 지칭하는 데 사용할 것입니다. 채널의 출력 시스템이 입력 시스템과 동일한 경우가 흔하며, 이 경우 우리는 둘 다 지칭하기 위해 동일한 문자 $\mathsf{X}$를 사용할 수 있습니다.

## 1. 채널은 선형 매핑입니다
채널은 고전 정보의 표준 공식화에서의 확률적 연산이나 양자 정보의 단순화된 공식화에서의 유니터리 연산과 마찬가지로, **선형** 매핑으로 설명됩니다. 

만약 상태가 밀도 행렬 $\rho$로 설명되는 입력 시스템 $\mathsf{X}$에 채널 $\Phi$가 수행되면, 채널의 출력 시스템은 밀도 행렬 $\Phi(\rho)$로 설명됩니다. $\Phi$의 출력 시스템 역시 $\mathsf{X}$인 상황에서, 우리는 채널이 $\mathsf{X}$의 상태를 $\rho$에서 $\Phi(\rho)$로 변경하는 것을 나타낸다고 단순히 볼 수 있습니다. $\Phi$의 출력 시스템이 $\mathsf{X}$가 아닌 다른 시스템, $\mathsf{Y}$일 때, $\mathsf{Y}$는 채널을 적용하는 과정에서 생성되는 새로운 시스템이며, 입력 시스템인 $\mathsf{X}$는 채널이 적용된 후에는 더 이상 사용할 수 없게 된다는 것을 이해해야 합니다 — 마치 채널 자체가 $\mathsf{X}$를 $\mathsf{Y}$로 변환하여 상태 $\Phi(\rho)$로 남겨두는 것과 같습니다.

채널이 **선형** 매핑으로 설명된다는 가정은 공리, 즉 증명되는 것이 아니라 이론의 기본 전제로 간주될 수 있습니다. 하지만, 채널이 확률 이론 및 우리가 밀도 행렬에 대해 이미 배운 내용과 일치하려면 밀도 행렬 입력의 볼록 조합에 선형적으로 작용해야 할 필요성을 볼 수 있습니다. 

더 구체적으로 말해, 채널 $\Phi$가 있고 밀도 행렬 $\rho$와 $\sigma$로 표현되는 두 상태 중 하나에 시스템이 있을 때 이를 적용한다고 가정해 봅시다. $\rho$에 채널을 적용하면 밀도 행렬 $\Phi(\rho)$를 얻고, $\sigma$에 적용하면 밀도 행렬 $\Phi(\sigma)$를 얻습니다. 따라서 $\mathsf{X}$의 입력 상태를 확률 $p$로 $\rho$로 선택하고 확률 $1-p$로 $\sigma$로 무작위로 선택하면, 확률 $p$로 출력 상태 $\Phi(\rho)$를 얻고, 확률 $1-p$로 $\Phi(\sigma)$를 얻게 되며, 이는 밀도 행렬의 가중 평균으로 $p\Phi(\rho) + (1-p)\Phi(\sigma)$와 같이 표현됩니다.

반면에, 우리는 채널의 입력 상태가 가중 평균 $p\rho + (1-p)\sigma$로 표현된다고 생각할 수 있으며, 이 경우 출력은 $\Phi(p\rho + (1-p)\sigma)$입니다. 우리가 그것을 어떻게 생각하든 관계없이 동일한 상태이므로, 다음이 성립해야 합니다.

<p>$\Phi(p\rho + (1-p)\sigma) = p\Phi(\rho) + (1-p)\Phi(\sigma)$</p>

밀도 행렬 $\rho$와 $\sigma$ 및 스칼라 $p\in$의 모든 선택에 대해 이 조건을 만족하는 매핑이 있을 때마다, 그 매핑을 모든 행렬 입력(즉, 밀도 행렬 입력뿐만 아니라)에 대해 선형이 되도록 확장하는 고유한 방법이 항상 존재합니다.

## 2. 채널은 밀도 행렬을 밀도 행렬로 변환합니다
당연히, 채널은 선형 매핑인 것 외에도 밀도 행렬을 밀도 행렬로 변환해야 합니다. 채널 $\Phi$가 밀도 행렬 $\rho$로 표현되는 상태에 있는 입력 시스템에 적용되면, 우리는 상태가 $\Phi(\rho)$로 표현되는 시스템을 얻게 되며, 이것이 상태로 해석될 수 있도록 유효한 밀도 행렬이어야 합니다.

그러나 채널 $\Phi$가 아무 일도 일어나지 않는 추가 시스템 $\mathsf{Z}$가 있는 상태에서 시스템 $\mathsf{X}$를 시스템 $\mathsf{Y}$로 변환하는 더 일반적인 상황을 고려하는 것이 **매우 중요합니다**. 즉, 우리가 어떤 밀도 행렬로 설명되는 상태에 있는 시스템 쌍 $(\mathsf{Z},\mathsf{X})$에서 시작하여, $\mathsf{X}$에만 $\Phi$를 적용하여 $\mathsf{Y}$로 변환하면, 쌍 $(\mathsf{Z},\mathsf{Y})$의 상태를 설명하는 밀도 행렬을 얻어야 합니다.

입력 시스템 $\mathsf{X}$와 출력 시스템 $\mathsf{Y}$를 가진 채널 $\Phi$가 $\mathsf{Z}$에 아무것도 하지 않을 때 쌍 $(\mathsf{Z},\mathsf{X})$의 상태를 $(\mathsf{Z},\mathsf{Y})$의 상태로 어떻게 변환하는지 수학적 용어로 설명할 수 있습니다. 단순함을 위해, $\mathsf{Z}$의 고전적 상태 집합이 $\{0,\ldots,m-1\}$이라고 가정하겠습니다. 이를 통해 $(\mathsf{Z},\mathsf{X})$의 상태를 나타내는 임의의 밀도 행렬 $\rho$를 다음 형식으로 작성할 수 있습니다.
<p>$$\rho = \sum_{a,b = 0}^{m-1} \vert a\rangle\langle b\vert \otimes \rho_{a,b} = \begin{pmatrix} \rho_{0,0} & \rho_{0,1} & \cdots & \rho_{0,m-1} \\[1mm] \rho_{1,0} & \rho_{1,1} & \cdots & \rho_{1,m-1} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] \rho_{m-1,0} & \rho_{m-1,1} & \cdots & \rho_{m-1,m-1} \end{pmatrix}$$</p>

이 방정식의 오른쪽에는 블록 행렬이 있으며, 내부 괄호가 제거된 행렬들의 행렬로 생각할 수 있습니다. 이것은 중간 표현에서처럼 Dirac 표기법을 사용하여 다르게 설명될 수 있는 일반적인 행렬을 남깁니다. 각 행렬 $\rho_{a,b}$는 $\mathsf{X}$의 고전적 상태에 해당하는 행과 열을 가지며, 이 행렬들은 간단한 공식으로 결정될 수 있습니다.
<p>$$\rho_{a,b} = \bigl(\langle a \vert \otimes \mathbb{I}_{\mathsf{X}} \bigr) \rho \bigl(\vert b \rangle \otimes \mathbb{I}_{\mathsf{X}} \bigr)$$</p>

일반적으로 이것들은 밀도 행렬이 아니며 — 그것들이 함께 배열되어 $\rho$를 형성할 때만 밀도 행렬을 얻는다는 점에 유의하십시오.

다음 방정식은 $\mathsf{X}$에 $\Phi$가 적용될 때 얻어지는 $(\mathsf{Z},\mathsf{Y})$의 상태를 설명합니다.
<p>$$\sum_{a,b = 0}^{m-1} \vert a\rangle\langle b\vert \otimes \Phi(\rho_{a,b}) = \begin{pmatrix} \Phi(\rho_{0,0}) & \Phi(\rho_{0,1}) & \cdots & \Phi(\rho_{0,m-1}) \\[1mm] \Phi(\rho_{1,0}) & \Phi(\rho_{1,1}) & \cdots & \Phi(\rho_{1,m-1}) \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] \Phi(\rho_{m-1,0}) & \Phi(\rho_{m-1,1}) & \cdots & \Phi(\rho_{m-1,m-1}) \end{pmatrix}$$</p>

주어진 $\Phi$와 $\rho$의 선택에 대해 이 표현식을 평가하려면, 각 $\rho_{a,b}$가 일반적으로 그 자체로 밀도 행렬이 아니기 때문에, $\Phi$가 비-밀도 행렬 입력에 대한 선형 매핑으로 어떻게 작동하는지 이해해야 한다는 점에 유의하십시오. 이 방정식은 $$(\operatorname{Id}_{\mathsf{Z}} \otimes \,\Phi)(\rho)$$ 표현식과 일치하며, 여기서 $$\operatorname{Id}_{\mathsf{Z}}$$는 시스템 $\mathsf{Z}$에 대한 **항등 채널**을 나타냅니다. 이는 우리가 텐서 곱의 개념을 행렬에서 행렬로의 선형 매핑으로 확장했다고 가정하는 것인데, 이는 간단하지만—이 레슨의 목적상 필수적인 것은 아니므로 더 이상 설명하지 않겠습니다.

위에서 언급된 내용을 다시 한번 강조하자면, 선형 매핑 $\Phi$가 유효한 채널이 되기 위해서는, $\mathsf{Z}$에 대한 모든 선택과 쌍 $(\mathsf{Z},\mathsf{X})$의 모든 밀도 행렬 $\rho$에 대해, $\mathsf{X}$에 $\Phi$를 적용할 때 항상 밀도 행렬을 얻어야 합니다. 수학적 용어로, 매핑이 채널이 되기 위해 가져야 하는 속성은 **트레이스 보존**이어야 한다는 것입니다 — 채널을 적용하여 얻은 행렬의 트레이스가 1과 같도록 — 그리고 **완전히 양의** (completely positive) 성질을 가져야 한다는 것입니다 — 결과 행렬이 양의 반정부호(positive semidefinite)가 되도록. 이 두 가지 모두 개별적으로 고려하고 연구할 수 있는 중요한 속성이지만, 이 레슨을 위해 이러한 속성들을 개별적으로 고려하는 것이 필수적인 것은 아닙니다.

사실, 밀도 행렬을 입력으로 주었을 때 항상 밀도 행렬을 출력하지만, 복합 시스템에 대해 밀도 행렬을 밀도 행렬로 매핑하는 데 실패하는 선형 매핑들이 있습니다. 따라서 우리는 이런 방식으로 일부 선형 매핑들을 채널의 분류에서 제외합니다. (행렬 전치에 의해 주어진 선형 매핑이 가장 간단한 예입니다.)

두 시스템 $\mathsf{X}$와 $\mathsf{Z}$가 서로 바뀌어 $\Phi$가 오른쪽 시스템이 아닌 왼쪽 시스템에 적용되는 경우에 대한 위의 공식과 유사한 공식이 있습니다.

<p>$$(\Phi\otimes\operatorname{Id}_{\mathsf{Z}})(\rho) = \sum_{a,b = 0}^{m-1} \Phi(\rho_{a,b}) \otimes \vert a\rangle\langle b\vert$$</p>

이것은 $\rho$가 $(\mathsf{Z},\mathsf{X})$의 상태가 아니라 $(\mathsf{X},\mathsf{Z})$의 상태라고 가정합니다. 이번에는 행렬 $\rho_{a,b}$가 $\rho$에서 연속적인 행과 열에 속하지 않기 때문에 블록 행렬 설명이 작동하지 않지만, 기본 수학적 구조는 동일합니다.

복합 시스템의 단지 한 부분에만 적용될 때에도 밀도 행렬을 항상 밀도 행렬로 변환해야 한다는 요구 사항을 충족하는 모든 선형 매핑은 유효한 채널을 나타냅니다. 따라서 추상적인 의미에서, 채널의 개념은 밀도 행렬의 개념과 채널이 선형적으로 작용한다는 가정에 의해 결정됩니다. 이러한 점에서 채널은 양자 정보의 단순화된 공식화에서 주어진 시스템에 대해 양자 상태 벡터를 항상 양자 상태 벡터로 변환하는 선형 매핑인 유니터리 연산과 유사합니다. 또한 채널은 고전 정보의 표준 공식화에서 확률 벡터를 항상 확률 벡터로 변환하는 선형 매핑인 확률적 연산(확률 행렬로 표현됨)과도 유사합니다.

## 3. 채널로서의 유니터리 연산
$\mathsf{X}$가 시스템이고 $U$가 $\mathsf{X}$에 대한 연산을 나타내는 유니터리 행렬이라고 가정해 봅시다. 밀도 행렬에 대한 이 연산을 설명하는 채널 $\Phi$는 $\mathsf{X}$의 양자 상태를 나타내는 모든 밀도 행렬 $\rho$에 대해 다음과 같이 정의됩니다.
<p>$$\Phi(\rho) = U \rho U^{\dagger} \tag{1}$$</p>

왼쪽에서 $U$를 곱하고 오른쪽에서 $U^{\dagger}$를 곱하는 이 작용은 행렬 $U$에 의한 **켤레 변환(conjugation)**으로 흔히 불립니다.

이 설명은 주어진 양자 상태 벡터 $\vert\psi\rangle$를 나타내는 밀도 행렬이 $\vert\psi\rangle\langle\psi\vert$라는 사실과 일치합니다. 특히, 유니터리 연산 $U$가 $\vert\psi\rangle$에 대해 수행되면, 출력 상태는 벡터 $U\vert\psi\rangle$로 표현되며, 따라서 이 상태를 설명하는 밀도 행렬은 다음과 같습니다.
<p>$$(U \vert \psi \rangle )( U \vert \psi \rangle )^{\dagger} = U \vert\psi\rangle\langle\psi\vert U^{\dagger}$$</p>

채널로서 연산 $U$가 순수 상태에 대해 $\vert\psi\rangle\langle \psi\vert \mapsto U \vert\psi\rangle\langle\psi\vert U^{\dagger}$의 작용을 한다는 것을 알면, 선형성에 의해 임의의 밀도 행렬 $\rho$에 대해 위의 방정식 (1)에 명시된 대로 작동해야 한다고 결론 내릴 수 있습니다.

우리가 $U = \mathbb{I}$를 취할 때 얻는 특정 채널은 **항등 채널** $\operatorname{Id}$이며, 이 채널이 어떤 시스템에 작용하는지 명시적으로 나타내고자 할 때 우리는 첨자(예: 우리가 이미 접한 $\operatorname{Id}_{\mathsf{Z}}$)를 붙일 수도 있습니다. 그것의 출력은 항상 입력과 같습니다: $\operatorname{Id}(\rho) = \rho$. 이것은 흥미롭지 않은 채널처럼 보일 수 있지만, 실제로는 매우 중요한 채널입니다 — 그리고 이것이 우리의 첫 번째 예시라는 것은 적절합니다. 항등 채널은 일부 맥락에서 **완벽한** 채널이며, 이상적인 기억 또는 송신자에서 수신자로의 완벽하고 잡음 없는 정보 전송을 나타냅니다.

이런 방식으로 유니터리 연산으로 정의된 모든 채널은 실제로 유효한 채널입니다. 행렬 $U$에 의한 켤레 변환은 우리에게 선형 매핑을 제공합니다. 그리고 만약 $\rho$가 시스템 $(\mathsf{Z},\mathsf{X})$의 밀도 행렬이고 $U$가 유니터리라면, 우리가
<p>$$(\mathbb{I}_{\mathsf{Z}} \otimes U) \rho (\mathbb{I}_{\mathsf{Z}} \otimes U^{\dagger})$$</p>

로 표현할 수 있는 결과 역시 밀도 행렬입니다. 구체적으로, 이 행렬은 양의 반정부호여야 합니다. 왜냐하면 만약 $\rho = M^{\dagger} M$이라면
<p>$$(\mathbb{I}_{\mathsf{Z}} \otimes U) \rho (\mathbb{I}_{\mathsf{Z}} \otimes U^{\dagger}) = K^{\dagger} K$$</p>

$K = M (\mathbb{I}_{\mathsf{Z}} \otimes U^{\dagger})$일 때, 이 행렬은 트레이스의 순환 속성(cyclic property)에 의해 트레이스가 1이어야 합니다.
<p>$$\operatorname{Tr}\bigl((\mathbb{I}_{\mathsf{Z}} \otimes U) \rho (\mathbb{I}_{\mathsf{Z}} \otimes U^{\dagger})\bigr) = \operatorname{Tr}\bigl((\mathbb{I}_{\mathsf{Z}} \otimes U^{\dagger})(\mathbb{I}_{\mathsf{Z}} \otimes U) \rho \bigr) = \operatorname{Tr}\bigl((\mathbb{I}_{\mathsf{Z}} \otimes \mathbb{I}_{\mathsf{X}}) \rho \bigr) = \operatorname{Tr}(\rho) = 1$$</p>

## 4. 채널의 볼록 조합
동일한 입력 시스템과 동일한 출력 시스템을 공유하는 두 채널 $\Phi_0$와 $\Phi_1$가 있다고 가정해 봅시다. 임의의 실수 $p\in[0,1]$에 대해, 우리는 확률 $p$로 $\Phi_0$를 적용하고 확률 $1-p$로 $\Phi_1$를 적용하기로 결정할 수 있으며, 이는 $p \Phi_0 + (1-p) \Phi_1$로 작성될 수 있는 새로운 채널을 제공합니다. 명시적으로, 이 채널이 주어진 밀도 행렬에 작용하는 방식은 다음 간단한 방정식으로 명시됩니다.

<p>$$(p \Phi_0 + (1-p) \Phi_1)(\rho) = p \Phi_0(\rho) + (1-p) \Phi_1(\rho)$$</p>

더 일반적으로, 만약 우리가 채널 $\Phi_{0},\ldots,\Phi_{m-1}$과 확률 벡터 $(p_0,\ldots, p_{m-1})$를 가지고 있다면, 우리는 이 채널들을 함께 평균하여 새로운 채널을 얻을 수 있습니다.
<p>$$\sum_{k = 0}^{m-1} p_k \Phi_k$$</p>

이것은 채널의 **볼록 조합(convex combination)**이며, 이 과정을 통해 우리는 항상 유효한 채널을 얻습니다. 이것을 수학적 용어로 간단하게 말하자면, 입력 및 출력 시스템의 주어진 선택에 대해 모든 채널의 집합은 **볼록 집합(convex set)**이라는 것입니다.

예를 들어, 우리는 특정 시스템에 **유니터리** 연산들의 컬렉션 중 하나를 적용하도록 선택할 수 있습니다. 우리는 다음과 같은 형식으로 표현될 수 있는 채널인 **혼합 유니터리(mixed unitary)** 채널이라고 알려진 것을 얻습니다.
<p>$$\Phi(\rho) = \sum_{k=0}^{m-1} p_k U_k \rho U_k^{\dagger}$$</p>

모든 유니터리 연산이 파울리 행렬(또는 파울리 행렬의 텐서 곱)인 혼합 유니터리 채널은 **파울리 채널(Pauli channels)**이라고 불리며, 양자 컴퓨팅에서 흔히 접하게 됩니다.

##  5. 큐비트 채널의 예시
이제 유니터리가 아닌 채널들의 몇 가지 구체적인 예시를 살펴보겠습니다. 이 모든 예시에서 입력 및 출력 시스템은 모두 단일 큐비트이며, 즉 이들은 **큐비트 채널**의 예시입니다.

### 5.1 큐비트 리셋 채널
이 채널은 매우 간단한 작업을 수행합니다: 큐비트를 $\vert 0\rangle$ 상태로 리셋합니다. 선형 매핑으로서 이 채널은 모든 큐비트 밀도 행렬 $\rho$에 대해 다음과 같이 표현될 수 있습니다.
<p>$$\Lambda(\rho) = \operatorname{Tr}(\rho) \vert 0\rangle\langle 0\vert$$</p>

모든 밀도 행렬 $\rho$의 트레이스가 $1$과 같지만, 채널을 이런 식으로 작성하면 밀도 행렬뿐만 아니라 모든 $2\times 2$ 행렬에 적용될 수 있는 선형 매핑이라는 점이 명확해집니다. 우리가 이미 관찰했듯이, 채널이 복합 시스템의 한 부분에만 적용될 때 무슨 일이 일어나는지 설명하기 위해 비-밀도 행렬 입력에 대한 선형 매핑으로서 채널이 어떻게 작동하는지 이해해야 합니다.

예를 들어, $\mathsf{A}$와 $\mathsf{B}$가 큐비트이고 쌍 $(\mathsf{A},\mathsf{B})$가 함께 벨 상태 $\vert \phi^+\rangle$에 있다고 가정해 봅시다. 밀도 행렬로서 이 상태는 다음과 같이 주어집니다.

<p>$$\vert \phi^+\rangle\langle \phi^+ \vert = \begin{pmatrix} \frac{1}{2} & 0 & 0 & \frac{1}{2} \\[1mm] 0 & 0 & 0 & 0 \\[1mm] 0 & 0 & 0 & 0 \\[1mm] \frac{1}{2} & 0 & 0 & \frac{1}{2} \end{pmatrix}$$</p>

Dirac 표기법을 사용하여 이 상태를 다음과 같이 다르게 표현할 수 있습니다.
<p>$$\vert \phi^+\rangle\langle \phi^+ \vert = \frac{1}{2} \vert 0 \rangle \langle 0 \vert \otimes \vert 0 \rangle \langle 0 \vert + \frac{1}{2} \vert 0 \rangle \langle 1 \vert \otimes \vert 0 \rangle \langle 1 \vert + \frac{1}{2} \vert 1 \rangle \langle 0 \vert \otimes \vert 1 \rangle \langle 0 \vert + \frac{1}{2} \vert 1 \rangle \langle 1 \vert \otimes \vert 1 \rangle \langle 1 \vert$$</p>

큐비트 리셋 채널을 $\mathsf{A}$에 적용하고 $\mathsf{B}$에는 아무것도 하지 않음으로써 우리는 다음 상태를 얻습니다.
<p>$$\begin{aligned} \frac{1}{2} \Lambda(\vert 0 \rangle \langle 0 \vert) \otimes \vert 0 \rangle \langle 0 \vert + \frac{1}{2} \Lambda(\vert 0 \rangle \langle 1 \vert) \otimes \vert 0 \rangle \langle 1 \vert + \frac{1}{2} \Lambda(\vert 1 \rangle \langle 0 \vert) \otimes \vert 1 \rangle \langle 0 \vert + \frac{1}{2} \Lambda(\vert 1 \rangle \langle 1 \vert) \otimes \vert 1 \rangle \langle 1 \vert \qquad & \\[1mm] = \frac{1}{2} \vert 0 \rangle \langle 0 \vert \otimes \vert 0 \rangle \langle 0 \vert + \frac{1}{2} \vert 0 \rangle \langle 0 \vert \otimes \vert 1 \rangle \langle 1 \vert = \vert 0\rangle \langle 0\vert \otimes \frac{\mathbb{I}}{2} & \end{aligned}$$</p>

$\mathsf{A}$를 리셋하는 것이 $\mathsf{B}$에 영향을 미쳐 완전히 혼합된 상태가 되었다고 말하고 싶을 수도 있지만—어떤 의미에서는 실제로는 그 반대입니다. $\mathsf{A}$가 리셋되기 전에도 $\mathsf{B}$의 축소 상태(reduced state)는 완전히 혼합된 상태였으며, $\mathsf{A}$를 리셋한 결과로 그것이 변하지는 않습니다.

### 5.2 완전 역위상 채널 (The completely dephasing channel)
여기에 $\Delta$라고 불리는 큐비트 채널의 예가 있으며, $2\times 2$ 행렬에 대한 작용으로 설명됩니다.
<p>$$\Delta \begin{pmatrix} \alpha_{00} & \alpha_{01}\\[1mm] \alpha_{10} & \alpha_{11} \end{pmatrix} = \begin{pmatrix} \alpha_{00} & 0\\[1mm] 0 & \alpha_{11} \end{pmatrix}$$</p>

말로 설명하면, $\Delta$는 $2\times 2$ 행렬의 비대각선 요소들을 0으로 만듭니다. 이 예시는 큐비트가 아닌 임의의 시스템으로 일반화될 수 있습니다: 어떤 밀도 행렬이 입력되든, 채널은 모든 비대각선 요소들을 0으로 만들고 대각선 요소들은 그대로 둡니다.

이 채널은 **완전 역위상 채널**이라고 불리며, **디코히어런스(decoherence)**라고 알려진 과정의 극단적인 형태를 나타내는 것으로 생각될 수 있습니다 — 이는 본질적으로 양자 중첩을 망가뜨리고 이를 고전적 확률 상태로 바꿉니다.

이 채널에 대해 생각하는 또 다른 방법은, 입력 큐비트가 측정된 후 버려지고, 출력은 측정 결과를 설명하는 밀도 행렬인, 큐비트에 대한 표준 기저 측정을 설명한다는 것입니다. 또는 동등하게, 측정 결과는 버려지고 큐비트가 측정 후 상태로 남는다고 상상할 수 있습니다.

다시 e-bit을 고려하고, $\Delta$가 두 큐비트 중 하나에만 적용될 때 무슨 일이 일어나는지 봅시다. 구체적으로, $(\mathsf{A},\mathsf{B})$가 상태 $\vert\phi^+\rangle$에 있는 큐비트 $\mathsf{A}$와 $\mathsf{B}$가 있으며, 이번에는 두 번째 큐비트에 채널을 적용해 봅시다. 우리가 얻는 상태는 다음과 같습니다.
<p>$$\begin{aligned} \frac{1}{2} \vert 0 \rangle \langle 0 \vert \otimes \Delta(\vert 0 \rangle \langle 0 \vert) + \frac{1}{2} \vert 0 \rangle \langle 1 \vert \otimes \Delta(\vert 0 \rangle \langle 1 \vert) + \frac{1}{2} \vert 1 \rangle \langle 0 \vert \otimes \Delta(\vert 1 \rangle \langle 0 \vert) + \frac{1}{2} \vert 1 \rangle \langle 1 \vert \otimes \Delta(\vert 1 \rangle \langle 1 \vert) \qquad & \\[1mm] = \frac{1}{2} \vert 0 \rangle \langle 0 \vert \otimes \vert 0 \rangle \langle 0 \vert + \frac{1}{2} \vert 1 \rangle \langle 1 \vert \otimes \vert 1 \rangle \langle 1 \vert & \end{aligned}$$</p>

또는 이 방정식을 블록 행렬을 사용하여 표현할 수 있습니다.
<p>$$\begin{pmatrix} \Delta\begin{pmatrix} \frac{1}{2} & 0\\[1mm] 0 & 0 \end{pmatrix} & \Delta\begin{pmatrix} 0 & \frac{1}{2}\\[1mm] 0 & 0 \end{pmatrix} \\[4mm] \Delta\begin{pmatrix} 0 & 0\\[1mm] \frac{1}{2} & 0 \end{pmatrix} & \Delta\begin{pmatrix} 0 & 0\\[1mm] 0 & \frac{1}{2} \end{pmatrix} \end{pmatrix} = \begin{pmatrix} \frac{1}{2} & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & \frac{1}{2} \end{pmatrix}$$</p>

우리는 큐비트를 완전히 역위상시키는 것이 아니라 약간만 역위상시키는 큐비트 채널을 고려할 수도 있는데, 이는 완전 역위상 채널이 나타내는 것보다 덜 극단적인 형태의 디코히어런스입니다. 특히, $\varepsilon \in (0,1)$이 작지만 0이 아닌 실수라고 가정해 봅시다. 우리는 다음 채널을 정의할 수 있습니다.
<p>$$\Delta_{\varepsilon} = (1 - \varepsilon) \operatorname{Id} + \varepsilon \Delta,$$</p>
이 채널은 주어진 큐비트 밀도 행렬 $\rho$를 다음과 같이 변환합니다.

<p>$$\Delta_{\varepsilon}(\rho) = (1 - \varepsilon) \rho + \varepsilon \Delta(\rho)$$</p>

즉, 확률 $1-\varepsilon$로는 아무 일도 일어나지 않고, 확률 $\varepsilon$로는 큐비트가 역위상됩니다. 행렬 관점에서, 이 작용은 다음과 같이 표현될 수 있으며, 여기서 대각선 요소는 그대로 유지되고 비대각선 요소는 $1-\varepsilon$이 곱해집니다.

<p>$$\rho = \begin{pmatrix} \langle 0\vert \rho \vert 0 \rangle & \langle 0\vert \rho \vert 1 \rangle \\[1mm] \langle 1\vert \rho \vert 0 \rangle & \langle 1\vert \rho \vert 1 \rangle \end{pmatrix} \mapsto \begin{pmatrix} \langle 0\vert \rho \vert 0 \rangle & (1-\varepsilon) \langle 0\vert \rho \vert 1 \rangle \\[1mm] (1-\varepsilon) \langle 1\vert \rho \vert 0 \rangle & \langle 1\vert \rho \vert 1 \rangle \end{pmatrix}$$</p>

### 5.3 완전 탈분극 채널 (The completely depolarizing channel)
여기에 $\Omega$라고 불리는 큐비트 채널의 또 다른 예시가 있습니다.
<p>$$\Omega(\rho) = \operatorname{Tr}(\rho) \frac{\mathbb{I}}{2}$$</p>
여기서 $\mathbb{I}$는 $2\times 2$ 항등 행렬을 나타냅니다. 말로 설명하면, 임의의 밀도 행렬 입력 $\rho$에 대해 채널 $\Omega$는 완전히 혼합된 상태(completely mixed state)를 출력합니다. 이보다 더 잡음이 심할 수는 없습니다! 이 채널은 **완전 탈분극 채널**이라고 불리며, 완전 역위상 채널과 마찬가지로 큐비트 대신 임의의 시스템으로 일반화될 수 있습니다.

우리는 또한 역위상 채널에서 보았던 것과 유사하게, 확률 $\varepsilon$로 탈분극이 발생하는 이 채널의 덜 극단적인 변형을 고려할 수 있습니다.
<p>$$\Omega_{\varepsilon}(\rho) = (1 - \varepsilon) \rho + \varepsilon \Omega(\rho)$$</p>

---

# 채널 표현(Channel representations)

다음으로, 채널의 수학적 표현에 대해 논의할 것입니다. 

벡터에서 벡터로의 선형 매핑은 익숙한 방식으로 행렬로 표현될 수 있으며, 여기서 선형 매핑의 작용은 행렬-벡터 곱셈으로 설명됩니다. 하지만 채널은 벡터에서 벡터로의 선형 매핑이 아니라 행렬에서 행렬로의 선형 매핑입니다. 그렇다면, *일반적으로* 채널을 수학적 용어로 어떻게 표현할 수 있을까요?

일부 채널의 경우, 이전에 설명된 비유니터리 큐비트 채널의 세 가지 예시처럼 채널을 설명하는 간단한 공식이 있을 수 있습니다. 하지만 임의의 채널은 그러한 깔끔한 공식을 갖지 않을 수 있으므로, 일반적으로 채널을 이런 식으로 표현하는 것은 실용적이지 않습니다.

비교의 관점에서, 양자 정보의 단순화된 공식화에서는 양자 상태 벡터에 대한 연산을 나타내기 위해 *유니터리 행렬*을 사용합니다. 모든 유니터리 행렬은 유효한 연산을 나타내고 모든 유효한 연산은 유니터리 행렬로 표현될 수 있습니다. 본질적으로 던져지는 질문은: 채널에 대해 이와 유사한 것을 어떻게 할 수 있을까요?

이 질문에 답하기 위해, 우리는 몇 가지 추가적인 수학적 도구가 필요할 것입니다. 우리는 채널이 실제로 개발에 핵심적인 역할을 했던 세 개인의 이름을 딴 표현을 포함하여 몇 가지 다른 방식으로 수학적으로 기술될 수 있음을 알게 될 것입니다. **William Forrest Stinespring** (1929 – 2012)은 연산자 이론을 전문으로 하는 수학자였으며, 이는 양자 정보에 많은 흥미로운 함의를 갖는 함수 해석학의 주제입니다. **Karl Kraus** (1938 – 1988)는 양자 역학의 기초를 전문으로 하는 이론 물리학자였습니다. 이 레슨에서 논의된 수학적 형식론의 많은 부분이 그의 1983년 강의 노트(상태, 효과, 연산: 양자 이론의 기본 개념)에서 설명되었습니다. 그리고 **Man-Duen Choi**는 연산자 이론과 행렬 이론을 전문으로 하는 토론토 대학의 명예 교수입니다. 완전히 양의 매핑에 대한 그의 1975년 논문은 채널을 이해하는 핵심 수학적 도구를 제공합니다. 종합적으로, 채널을 기술하는 이러한 다른 방식들은 채널을 보고 분석할 수 있는 다양한 관점을 제공합니다.

## 1. Stinespring 표현
Stinespring 표현은 모든 채널이 표준적인 방식으로 구현될 수 있다는 아이디어에 기반을 두고 있습니다. 이 방식에서는 입력 시스템이 먼저 초기화된 작업 공간 시스템과 결합되어 복합 시스템을 형성합니다. 그런 다음 복합 시스템에 유니터리 연산이 수행되고, 마지막으로 작업 공간 시스템은 버려져(또는 트레이스 아웃되어) 채널의 출력을 남깁니다.

다음 그림은 입력 및 출력 시스템이 동일한 시스템인 $\mathsf{X}$인 채널에 대한 이러한 구현을 회로 다이어그램 형태로 보여줍니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/Stinespring-X-to-X.svg" style="width:60%">

이 다이어그램에서, 와이어 위의 레이블이 나타내는 것처럼, 와이어는 임의의 시스템을 나타내며, 반드시 단일 큐비트를 나타내는 것은 아닙니다. 또한, 전기 공학에서 흔히 사용되는 *접지* 기호는 $\mathsf{W}$가 버려짐을 명시적으로 나타냅니다.

말로 설명하자면, 구현 방식은 다음과 같습니다. 입력 시스템 $\mathsf{X}$는 어떤 상태 $\rho$에서 시작하며, 동시에 작업 공간 시스템 $\mathsf{W}$는 표준 기저 상태 $\vert 0\rangle$로 초기화됩니다. 쌍 $(\mathsf{W},\mathsf{X})$에 유니터리 연산 $U$가 수행되고, 마지막으로 작업 공간 시스템 $\mathsf{W}$는 *트레이스 아웃*되어, $\mathsf{X}$가 출력으로 남습니다.

우리는 $0$이 $\mathsf{W}$의 고전적 상태라고 가정하고 있으며, 수학을 단순화하는 데 도움이 되도록 이 시스템의 초기화된 상태로 이를 선택합니다. 그러나 표현의 기본 속성을 변경하지 않고 $\mathsf{W}$의 초기화된 상태를 나타내기 위해 임의의 고정된 순수 상태를 선택할 수 있습니다.

결과 채널 $\Phi$의 수학적 표현은 다음과 같습니다.
<p>$$\Phi(\rho) = \operatorname{Tr}_{\mathsf{W}} \bigl( U (\vert 0\rangle \langle 0 \vert_{\mathsf{W}} \otimes \rho) U^{\dagger} \bigr)$$</p>

평소처럼, 우리는 Qiskit의 순서 규칙을 사용합니다. 시스템 $\mathsf{X}$는 다이어그램에서 위에 있으며, 따라서 공식에서 오른쪽 텐서 인수에 해당합니다.

일반적으로 채널의 입력 및 출력 시스템이 동일할 필요는 없습니다. 여기에 입력 시스템이 $\mathsf{X}$이고 출력 시스템이 $\mathsf{Y}$인 채널 $\Phi$의 구현을 묘사하는 그림이 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/Stinespring-X-to-Y.svg" style="width:60%">

이번에는 유니터리 연산이 $(\mathsf{W},\mathsf{X})$를 쌍 $(\mathsf{G},\mathsf{Y})$로 변환하며, 여기서 $\mathsf{G}$는 트레이스 아웃되는 새로운 "쓰레기(garbage)" 시스템이며, $\mathsf{Y}$를 출력 시스템으로 남깁니다. $U$가 유니터리이기 위해서는, 그것은 정방 행렬이어야 합니다. 이것은 쌍 $(\mathsf{G},\mathsf{Y})$가 쌍 $(\mathsf{W},\mathsf{X})$와 동일한 수의 고전적 상태를 가져야 함을 요구하며, 따라서 시스템 $\mathsf{W}$와 $\mathsf{G}$는 이것을 허용하는 방식으로 선택되어야 합니다.

우리는 이전에 가졌던 것과 유사한, 결과 채널 $\Phi$의 수학적 표현을 얻습니다.
<p>$$\Phi(\rho) = \operatorname{Tr}_{\mathsf{G}} \bigl( U (\vert 0\rangle \langle 0 \vert_{\mathsf{W}} \otimes \rho) U^{\dagger} \bigr)$$</p>
채널이 이러한 방식으로, 즉 유니터리 연산과 함께 작업 공간 시스템이 어떻게 초기화되고 출력 시스템이 어떻게 선택되는지에 대한 명시를 통해 설명될 때, 우리는 그것이 *Stinespring 형식*으로 표현되었다거나 채널의 *Stinespring 표현*이라고 말합니다.

전혀 명확하지는 않지만, 모든 채널은 실제로 Stinespring 표현을 가지고 있으며, 이는 레슨이 끝날 때까지 알게 될 것입니다. 또한 Stinespring 표현은 고유하지 않다는 것을 알게 될 것입니다. 설명된 방식으로 동일한 채널을 구현하는 항상 다른 방법들이 있을 것입니다.

>비고
>
양자 정보의 맥락에서, *Stinespring 표현*이라는 용어는 일반적으로 다음 형태를 갖는 채널의 약간 더 일반적인 표현을 의미합니다.
><p>$$\Phi(\rho) = \operatorname{Tr}_{\mathsf{G}} \bigl( A \rho A^{\dagger} \bigr)$$</p>
>
>여기서 $A$는 *등거리 행렬(isometry)*이며, 그 열들이 정규 직교하지만 정방 행렬이 아닐 수도 있는 행렬입니다. 우리가 정의로 채택한 형태를 갖는 Stinespring 표현에 대해, 우리는 다음을 취함으로써 이 다른 형태의 표현을 얻을 수 있습니다.
><p>$$A = U (\vert 0\rangle_{\mathsf{W}} \otimes \mathbb{I}_{\mathsf{X}})$$</p>

### 1.1 완전 역위상 채널
여기에 큐비트 역위상 채널 $\Delta$의 Stinespring 표현이 있습니다. 이 다이어그램에서, 두 와이어 모두 단일 큐비트를 나타내므로 — 이는 일반적인 양자 회로 다이어그램입니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/dephasing-circuit.svg" style="width:60%">

이 회로가 입력 큐비트에 미치는 영향이 실제로 완전 역위상 채널로 설명됨을 확인하기 위해, 우리는 이전 레슨에서 논의된 부분 트레이스의 명시적 행렬 표현을 사용하여 회로를 한 단계씩 살펴볼 수 있습니다. 우리는 위쪽 큐비트를 $\mathsf{X}$라고 부를 것입니다 — 이것이 채널의 입력이자 출력입니다 — 그리고 $\mathsf{X}$가 임의의 상태 $\rho$에서 시작한다고 가정할 것입니다.

첫 번째 단계는 작업 공간 큐비트 $\mathsf{W}$의 도입입니다. 제어-NOT 게이트가 수행되기 전에, 쌍 $(\mathsf{W},\mathsf{X})$의 상태는 다음 밀도 행렬로 표현됩니다.

<p>$$\begin{aligned} \vert 0\rangle \langle 0 \vert_{\mathsf{W}} \otimes \rho & = \begin{pmatrix} 1 & 0\\ 0 & 0 \end{pmatrix} \otimes \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[4mm] & = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & 0 & 0 \\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & 0 & 0 \\[1mm] 0 & 0 & 0 & 0 \\[1mm] 0 & 0 & 0 & 0 \end{pmatrix} \end{aligned}$$</p>

Qiskit의 순서 규칙에 따라, 위쪽 큐비트 $\mathsf{X}$는 오른쪽에 있고 아래쪽 큐비트 $\mathsf{W}$는 왼쪽에 있습니다. 우리는 양자 상태 벡터 대신 밀도 행렬을 사용하고 있지만, 양자 정보의 단순화된 공식화에서 수행되는 것과 유사한 방식으로 텐서 곱으로 결합됩니다.

다음 단계는 제어-NOT 연산을 수행하는 것입니다. 여기서 $\mathsf{X}$가 제어(control)이고 $\mathsf{W}$가 대상(target)입니다. 여전히 Qiskit 순서 규칙을 염두에 두고, 이 게이트의 행렬 표현은 다음과 같습니다.
<p>$$\begin{pmatrix} 1 & 0 & 0 & 0\\ 0 & 0 & 0 & 1\\ 0 & 0 & 1 & 0\\ 0 & 1 & 0 & 0 \end{pmatrix}$$</p>

이것은 유니터리 연산이며, 이를 밀도 행렬에 적용하기 위해 우리는 유니터리 행렬로 켤레 변환을 합니다. 켤레 전치는 이 특정 행렬을 변경하지 않으므로, 결과는 다음과 같습니다.
<p>$$\begin{pmatrix} 1 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 1\\[1mm] 0 & 0 & 1 & 0\\[1mm] 0 & 1 & 0 & 0 \end{pmatrix} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & 0 & 0 \\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & 0 & 0 \\[1mm] 0 & 0 & 0 & 0 \\[1mm] 0 & 0 & 0 & 0 \end{pmatrix} \begin{pmatrix} 1 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 1\\[1mm] 0 & 0 & 1 & 0\\[1mm] 0 & 1 & 0 & 0 \end{pmatrix}\\[3mm] = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0 & 0 & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & 0 & 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}$$</p>

마지막으로, $\mathsf{W}$에 대해 부분 트레이스가 수행됩니다. 이전 레슨에서 설명된 $4\times 4$ 행렬에 대한 이 연산의 작용을 상기하면, 우리는 다음 밀도 행렬 출력을 얻습니다.

<p>$$\begin{aligned} \operatorname{Tr}_{\mathsf{W}} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0 & 0 & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & 0 & 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix} & = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0 \\[1mm] 0 & 0 \end{pmatrix} + \begin{pmatrix} 0 & 0 \\[1mm] 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[3mm] & = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0 \\[1mm] 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[4mm] & = \Delta(\rho) \end{aligned}$$</p>

우리는 대안적으로 먼저 Dirac 표기법으로 변환하여 부분 트레이스를 계산할 수 있습니다.

<p>$$\begin{pmatrix} \langle 0\vert \rho \vert 0\rangle & 0 & 0 & \langle 0\vert \rho \vert 1\rangle\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] \langle 1\vert \rho \vert 0\rangle & 0 & 0 & \langle 1\vert \rho \vert 1\rangle \end{pmatrix} = \begin{array}{r} \langle 0\vert \rho \vert 0\rangle \, \vert 0\rangle\langle 0\vert \otimes \vert 0\rangle\langle 0\vert \\[1mm] +\, \langle 0\vert \rho \vert 1\rangle \, \vert 0\rangle\langle 1\vert \otimes \vert 0\rangle\langle 1\vert \\[1mm] +\, \langle 1\vert \rho \vert 0\rangle \, \vert 1\rangle\langle 0\vert \otimes \vert 1\rangle\langle 0\vert \\[1mm] +\, \langle 1\vert \rho \vert 1\rangle \, \vert 1\rangle\langle 1\vert \otimes \vert 1\rangle\langle 1\vert \end{array}$$</p>

왼쪽 큐비트를 트레이스 아웃하면 이전과 동일한 답이 나옵니다.
<p>$$\langle 0\vert \rho \vert 0\rangle \, \vert 0\rangle\langle 0\vert +\, \langle 1\vert \rho \vert 1\rangle \, \vert 1\rangle\langle 1\vert = \Delta(\rho)$$</p>

이 회로에 대해 생각하는 직관적인 방법은 제어-NOT 연산이 입력 큐비트의 고전적 상태를 효과적으로 복사하고, 복사본이 쓰레기통에 버려지면 입력 큐비트가 확률적으로 두 가지 가능한 고전적 상태 중 하나로 "붕괴"하며, 이는 완전 역위상과 동일하다는 것입니다.

### 1.2 완전 역위상 채널 (대안)
위에서 설명된 회로가 완전 역위상 채널을 구현하는 유일한 방법은 아닙니다. 여기 그것을 수행하는 다른 방법이 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/dephasing-alternative.svg" style="width:60%">

여기에 이 구현이 작동함을 보여주는 빠른 분석이 있습니다. Hadamard 게이트가 수행된 후 우리는 이 2-큐비트 상태를 밀도 행렬로 가집니다.

<p>$$\begin{aligned} \vert + \rangle\langle + \vert \otimes \rho & = \frac{1}{2}\begin{pmatrix} 1 & 1\\[1mm] 1 & 1 \end{pmatrix} \otimes \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[4mm] & = \frac{1}{2} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle\\[1mm] \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}. \end{aligned}$$</p>

제어-$\sigma_z$ 게이트는 다음과 같이 켤레 변환에 의해 작동합니다.
<p>$$\frac{1}{2} \begin{pmatrix} 1 & 0 & 0 & 0\\[1mm] 0 & 1 & 0 & 0\\[1mm] 0 & 0 & 1 & 0\\[1mm] 0 & 0 & 0 & -1 \end{pmatrix} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle\\[1mm] \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix} \begin{pmatrix} 1 & 0 & 0 & 0\\[1mm] 0 & 1 & 0 & 0\\[1mm] 0 & 0 & 1 & 0\\[1mm] 0 & 0 & 0 & -1 \end{pmatrix}\\[3mm] = \frac{1}{2} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & -\langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & \langle 1 \vert \rho \vert 0 \rangle & -\langle 1 \vert \rho \vert 1 \rangle\\[1mm] \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & -\langle 0 \vert \rho \vert 1 \rangle\\[1mm] -\langle 1 \vert \rho \vert 0 \rangle & -\langle 1 \vert \rho \vert 1 \rangle & -\langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}$$</p>

마지막으로 작업 공간 시스템 $\mathsf{W}$가 트레이스 아웃됩니다.
<p>$$\frac{1}{2} \operatorname{Tr}_{\mathsf{W}} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & -\langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle & \langle 1 \vert \rho \vert 0 \rangle & -\langle 1 \vert \rho \vert 1 \rangle\\[1mm] \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle & \langle 0 \vert \rho \vert 0 \rangle & -\langle 0 \vert \rho \vert 1 \rangle\\[1mm] -\langle 1 \vert \rho \vert 0 \rangle & -\langle 1 \vert \rho \vert 1 \rangle & -\langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[3mm] \begin{aligned} & = \frac{1}{2} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix} + \frac{1}{2} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & -\langle 0 \vert \rho \vert 1 \rangle\\[1mm] -\langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[4mm] & = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0\\[2mm] 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix} \end{aligned}$$</p>

이 구현은 간단한 아이디어에 기반합니다. 역위상은 아무것도 하지 않거나(즉, 항등 연산을 적용하거나) $\sigma_z$ 게이트를 적용하는 것과 각각 $1/2$ 확률로 동등합니다.

<p>$$\begin{aligned} \frac{1}{2} \rho + \frac{1}{2} \sigma_z \rho \sigma_z & = \frac{1}{2} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & \langle 0 \vert \rho \vert 1 \rangle\\[1mm] \langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix} + \frac{1}{2} \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & -\langle 0 \vert \rho \vert 1 \rangle\\[1mm] -\langle 1 \vert \rho \vert 0 \rangle & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[4mm] & = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0\\[1mm] 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix}\\[2mm] & = \Delta(\rho) \end{aligned}$$</p>

즉, 완전 역위상 채널은 혼합 유니터리 채널의 예이며, 더 구체적으로는 파울리 채널입니다.

### 1.3 큐비트 리셋 채널
큐비트 리셋 채널은 다음과 같이 구현될 수 있습니다.
<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/reset-stinespring-1.svg" style="width:60%">

SWAP 게이트는 단순히 작업 공간 큐비트의 $\vert 0\rangle$ 초기화된 상태를 이동시켜 출력되도록 하고, 입력 상태 $\rho$는 아래쪽 큐비트로 이동된 다음 트레이스 아웃됩니다.

대안적으로, 채널의 출력이 위에 남아 있어야 한다고 요구하지 않는다면, 우리는 이 매우 간단한 회로를 우리의 표현으로 취할 수 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/reset-stinespring-2.svg" style="width:60%">

말로 설명하면, 큐비트를 $\vert 0\rangle$ 상태로 리셋하는 것은 큐비트를 쓰레기통에 버리고 새것을 얻는 것과 같습니다.

## 2. Kraus 표현
이제 우리는 행렬 곱셈과 덧셈을 통해 채널의 작용을 표현하는 편리한 공식적 방법을 제공하는 *Kraus 표현*에 대해 논의할 것입니다. 특히, Kraus 표현은 다음 형식의 채널 $\Phi$에 대한 사양입니다.
<p>$$\Phi(\rho) = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger}$$</p>

여기서 $A_0,\ldots,A_{N-1}$는 모두 동일한 차원을 갖는 행렬입니다. 그들의 열은 입력 시스템 $\mathsf{X}$의 고전적 상태에 해당하며, 그들의 행은 그것이 $\mathsf{X}$이든 다른 시스템 $\mathsf{Y}$이든 출력 시스템의 고전적 상태에 해당합니다. $\Phi$가 유효한 채널이 되기 위해서는 이 행렬들이 다음 조건을 만족해야 합니다.
<p>$$\sum_{k = 0}^{N-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$$</p>

이 조건은 $\Phi$가 트레이스를 보존한다는 조건과 동등합니다. 채널에 요구되는 다른 속성 — 완전 양의성 — 은 켤레 변환의 합으로서 $\Phi$에 대한 방정식의 일반적인 형태에서 따릅니다.

때로는 행렬 $A_0,\ldots,A_{N-1}$의 이름을 다른 방식으로 지정하는 것이 편리합니다. 예를 들어, $1$부터 번호를 매기거나, 첨자로 숫자 대신 임의의 고전적 상태 집합 $\Gamma$의 상태를 사용할 수 있습니다.

<p>$$\Phi(\rho) = \sum_{a\in\Gamma} A_a \rho A_a^{\dagger} \quad \text{where} \quad \sum_{a\in\Gamma} A_a^{\dagger} A_a = \mathbb{I}$$</p>

*Kraus 행렬*이라고 불리는 이 행렬들을 명명하는 이러한 다른 방식들은 모두 일반적이며 다양한 상황에서 편리할 수 있지만 — 우리는 단순함을 위해 이 레슨에서는 $A_0,\ldots,A_{N-1}$라는 이름을 고수할 것입니다.

숫자 $N$은 임의의 양의 정수일 수 있지만, 너무 클 필요는 없습니다. 만약 입력 시스템 $\mathsf{X}$가 $n$개의 고전적 상태를 가지고 출력 시스템 $\mathsf{Y}$가 $m$개의 고전적 상태를 가진다면, $\mathsf{X}$에서 $\mathsf{Y}$로 가는 모든 주어진 채널은 $N$이 최대 곱 $nm$인 Kraus 표현을 항상 가질 것입니다.

### 2.1 완전 역위상 채널
우리는 $A_0 = \vert 0\rangle\langle 0\vert$와 $A_1 = \vert 1\rangle\langle 1\vert$을 취함으로써 완전 역위상 채널의 Kraus 표현을 얻습니다.

<p>$$\begin{aligned} \sum_{k = 0}^1 A_k \rho A_k^{\dagger} & = \vert 0\rangle\langle 0 \vert \rho \vert 0\rangle\langle 0 \vert + \vert 1\rangle\langle 1 \vert \rho \vert 1\rangle\langle 1 \vert\\ & = \langle 0 \vert \rho \vert 0\rangle \, \vert 0\rangle\langle 0 \vert + \langle 1 \vert \rho \vert 1\rangle \, \vert 1\rangle\langle 1 \vert \\[2mm] & = \begin{pmatrix} \langle 0 \vert \rho \vert 0 \rangle & 0 \\[1mm] 0 & \langle 1 \vert \rho \vert 1 \rangle \end{pmatrix} \end{aligned}$$</p>

이 행렬들은 요구되는 조건을 만족합니다.

<p>$$\sum_{k = 0}^1 A_k^{\dagger} A_k = \vert 0\rangle\langle 0\vert 0\rangle\langle 0\vert + \vert 1\rangle\langle 1\vert 1\rangle\langle 1\vert = \vert 0\rangle\langle 0\vert + \vert 1\rangle\langle 1\vert = \mathbb{I}$$</p>

대안적으로 우리는 $A_0 = \frac{1}{\sqrt{2}}\mathbb{I}$와 $A_1 = \frac{1}{\sqrt{2}}\sigma_z$를 취할 수 있으며, 따라서
<p>$$\sum_{k = 0}^1 A_k \rho A_k^{\dagger} = \frac{1}{2} \rho + \frac{1}{2} \sigma_z \rho \sigma_z = \Delta(\rho)$$</p>

이것은 이전에 계산된 바와 같습니다. 이번에는 요구되는 조건이 다음과 같이 검증될 수 있습니다.

<p>$$\sum_{k = 0}^1 A_k^{\dagger} A_k = \frac{1}{2} \mathbb{I} + \frac{1}{2} \sigma_z^2 = \frac{1}{2} \mathbb{I} + \frac{1}{2} \mathbb{I} = \mathbb{I}$$</p>

### 2.2 큐비트 리셋 채널

우리는 $A_0 = \vert 0\rangle\langle 0\vert$와 $A_1 = \vert 0\rangle\langle 1\vert$를 취함으로써 큐비트 리셋 채널의 Kraus 표현을 얻습니다.

<p>$$\begin{aligned} \sum_{k = 0}^1 A_k \rho A_k^{\dagger} & = \vert 0\rangle\langle 0 \vert \rho \vert 0\rangle\langle 0 \vert + \vert 0\rangle\langle 1 \vert \rho \vert 1\rangle\langle 0 \vert\\ & = \langle 0 \vert \rho \vert 0\rangle \, \vert 0\rangle\langle 0 \vert + \langle 1 \vert \rho \vert 1\rangle \, \vert 0\rangle\langle 0 \vert\\[2mm] & = \operatorname{Tr}(\rho) \vert 0\rangle \langle 0 \vert \end{aligned}$$</p>

이 행렬들은 요구되는 조건을 만족합니다.
<p>$$\sum_{k = 0}^1 A_k^{\dagger} A_k = \vert 0\rangle\langle 0\vert 0\rangle\langle 0\vert + \vert 1\rangle\langle 0\vert 0\rangle\langle 1\vert = \vert 0\rangle\langle 0\vert + \vert 1\rangle\langle 1\vert = \mathbb{I}$$</p>

### 2.3 완전 탈분극 채널
완전 탈분극 채널에 대한 Kraus 표현을 얻는 한 가지 방법은 Kraus 행렬 $A_0,\ldots,A_3$를 다음과 같이 선택하는 것입니다.

<p>$$A_0 = \frac{\vert 0\rangle\langle 0\vert}{\sqrt{2}} \quad A_1 = \frac{\vert 0\rangle\langle 1\vert}{\sqrt{2}} \quad A_2 = \frac{\vert 1\rangle\langle 0\vert}{\sqrt{2}} \quad A_3 = \frac{\vert 1\rangle\langle 1\vert}{\sqrt{2}}$$</p>

임의의 큐비트 밀도 행렬 $\rho$에 대해 우리는 다음을 가집니다.

<p>$$\begin{aligned} \sum_{k = 0}^3 A_k \rho A_k^{\dagger} & = \frac{1}{2} \bigl(\vert 0\rangle\langle 0\vert \rho \vert 0\rangle\langle 0\vert + \vert 0\rangle\langle 1\vert \rho \vert 1\rangle\langle 0\vert + \vert 1\rangle\langle 0\vert \rho \vert 0\rangle\langle 1\vert + \vert 1\rangle\langle 1\vert \rho \vert 1\rangle\langle 1\vert\bigr)\\ & = \operatorname{Tr}(\rho) \frac{\mathbb{I}}{2}\\[1mm] & = \Omega(\rho). \end{aligned}$$</p>

대안적인 Kraus 표현은 다음과 같이 Kraus 행렬을 선택함으로써 얻어집니다.

<p>$$A_0 = \frac{\mathbb{I}}{2} \quad A_1 = \frac{\sigma_x}{2} \quad A_2 = \frac{\sigma_y}{2} \quad A_3 = \frac{\sigma_z}{2}$$</p>

이 Kraus 행렬들이 실제로 완전 탈분극 채널을 나타낸다는 것을 검증하기 위해, 먼저 임의의 $2\times 2$ 행렬을 파울리 행렬로 켤레 변환하는 것이 다음과 같이 작동한다는 것을 관찰해 봅시다.

<p>$$\begin{aligned} \sigma_x \begin{pmatrix} \alpha_{0,0} & \alpha_{0,1}\\[1mm] \alpha_{1,0} & \alpha_{1,1} \end{pmatrix} \sigma_x & = \begin{pmatrix} \alpha_{1,1} & \alpha_{1,0}\\[1mm] \alpha_{0,1} & \alpha_{0,0} \end{pmatrix}\\[5mm] \sigma_y \begin{pmatrix} \alpha_{0,0} & \alpha_{0,1}\\[1mm] \alpha_{1,0} & \alpha_{1,1} \end{pmatrix} \sigma_y & = \begin{pmatrix} \alpha_{1,1} & -\alpha_{1,0}\\[1mm] -\alpha_{0,1} & \alpha_{0,0} \end{pmatrix}\\[5mm] \sigma_z \begin{pmatrix} \alpha_{0,0} & \alpha_{0,1}\\[1mm] \alpha_{1,0} & \alpha_{1,1} \end{pmatrix} \sigma_z & = \begin{pmatrix} \alpha_{0,0} & -\alpha_{0,1}\\[1mm] -\alpha_{1,0} & \alpha_{1,1} \end{pmatrix} \end{aligned}$$</p>

이것은 우리의 Kraus 표현의 정확성을 검증할 수 있게 합니다.

<p>$$\begin{aligned} \sum_{k = 0}^3 A_k \rho A_k^{\dagger} & = \frac{\rho + \sigma_x \rho \sigma_x + \sigma_y \rho \sigma_y + \sigma_z \rho \sigma_z}{4} \\ & = \frac{1}{4} \begin{pmatrix} \langle 0\vert\rho\vert 0\rangle + \langle 1\vert\rho\vert 1\rangle + \langle 1\vert\rho\vert 1\rangle + \langle 0\vert\rho\vert 0\rangle & \langle 0\vert\rho\vert 1\rangle + \langle 1\vert\rho\vert 0\rangle - \langle 1\vert\rho\vert 0\rangle - \langle 0\vert\rho\vert 1\rangle \\[2mm] \langle 1\vert\rho\vert 0\rangle + \langle 0\vert\rho\vert 1\rangle - \langle 0\vert\rho\vert 1\rangle - \langle 1\vert\rho\vert 0\rangle & \langle 1\vert\rho\vert 1\rangle + \langle 0\vert\rho\vert 0\rangle + \langle 0\vert\rho\vert 0\rangle + \langle 1\vert\rho\vert 1\rangle \end{pmatrix} \\[4mm] & = \operatorname{Tr}(\rho) \frac{\mathbb{I}}{2} \end{aligned}$$</p>

이 Kraus 표현은 중요한 아이디어를 표현하는데, 이는 큐비트의 상태가 균일하게 무작위로 선택된 네 개의 파울리 행렬(항등 행렬 포함) 중 하나를 적용함으로써 완전히 무작위화될 수 있다는 것입니다. 따라서, 완전 탈분극 채널은 파울리 채널의 또 다른 예입니다.

완전 탈분극 채널 $\Omega$에 대해 세 개 이하의 Kraus 행렬을 갖는 Kraus 표현을 찾는 것은 불가능합니다. 이 채널에는 최소 네 개가 필요합니다.

### 2.4 유니터리 채널

만약 우리가 시스템 $\mathsf{X}$에 대한 연산을 나타내는 유니터리 행렬 $U$를 가지고 있다면, 우리는 이 유니터리 연산의 작용을 채널로 표현할 수 있습니다.
<p>$$\Phi(\rho) = U \rho U^{\dagger}$$</p>

이 표현은 우리가 우연히 단 하나의 Kraus 행렬 $A_0 = U$를 갖는 채널 $\Phi$의 유효한 Kraus 표현입니다. 이 경우, 요구되는 조건
<p>$$\sum_{k = 0}^{N-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$$</p>

훨씬 더 간단한 형태인 $U^{\dagger} U = \mathbb{I}_{\mathsf{X}}$를 취하며, $U$가 유니터리이기 때문에 이것이 사실임을 알고 있습니다.

## 3. Choi 표현
이제 채널이 기술될 수 있는 세 번째 방식, 즉 *Choi 표현*에 대해 논의할 것입니다. 이것은 각 채널이 *Choi 행렬*로 알려진 단일 행렬로 표현되는 방식으로 작동합니다. 입력 시스템이 $n$개의 고전적 상태를 가지고 출력 시스템이 $m$개의 고전적 상태를 가진다면, 채널의 Choi 행렬은 $nm$개의 행과 $nm$개의 열을 가질 것입니다.

Choi 행렬은 채널의 *충실한(faithful)* 표현을 제공하는데, 이는 두 채널이 동일한 Choi 행렬을 갖는 경우에만 그리고 그 경우에만 동일하다는 것을 의미합니다. 이것이 중요한 한 가지 이유는 두 가지 다른 설명이 동일한 채널에 해당하는지 아니면 다른 채널에 해당하는지를 결정하는 방법을 제공하기 때문입니다. 즉, 우리는 단순히 Choi 행렬을 계산하고 비교하여 같은지 확인합니다. 대조적으로, 우리가 보았듯이 Stinespring 및 Kraus 표현은 이러한 방식으로 고유하지 않습니다.

Choi 행렬은 채널의 다양한 수학적 속성을 밝히는 다른 측면에서도 유용합니다.

### 3.1 정의
$\Phi$를 시스템 $\mathsf{X}$에서 시스템 $\mathsf{Y}$로 가는 채널이라고 하고, 입력 시스템 $\mathsf{X}$의 고전적 상태 집합이 $\Sigma$라고 가정합시다. $J(\Phi)$로 표기되는 $\Phi$의 Choi 표현은 다음 방정식으로 정의됩니다.
<p>$$J(\Phi) = \sum_{a,b\in\Sigma} \vert a\rangle\langle b \vert \otimes \Phi\bigl( \vert a\rangle\langle b \vert\bigr)$$</p>

만약 우리가 양의 정수 $n$에 대해 $\Sigma = \{0,\ldots, n-1\}$이라고 가정한다면, 우리는 대안적으로 $J(\Phi)$를 블록 행렬로 표현할 수 있습니다.
<p>$$J(\Phi) = \begin{pmatrix} \Phi\bigl(\vert 0\rangle\langle 0\vert\bigr) & \Phi\bigl(\vert 0\rangle\langle 1\vert\bigr) & \cdots & \Phi\bigl(\vert 0\rangle\langle n-1\vert\bigr) \\[1mm] \Phi\bigl(\vert 1\rangle\langle 0\vert\bigr) & \Phi\bigl(\vert 1\rangle\langle 1\vert\bigr) & \cdots & \Phi\bigl(\vert 1\rangle\langle n-1\vert\bigr) \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] \Phi\bigl(\vert n-1\rangle\langle 0\vert\bigr) & \Phi\bigl(\vert n-1\rangle\langle 1\vert\bigr) & \cdots & \Phi\bigl(\vert n-1\rangle\langle n-1\vert\bigr) \end{pmatrix}$$</p>

즉, 블록 행렬로서, 채널의 Choi 행렬은 입력 시스템의 고전적 상태의 각 쌍 $(a,b)$에 대해 하나의 블록 $\Phi(\vert a\rangle\langle b\vert)$를 가지며, 블록들은 자연스러운 방식으로 배열됩니다.

집합 $\{\vert a\rangle\langle b\vert \,:\, 0\leq a,b < n\}$은 모든 $n\times n$ 행렬 공간의 기저를 형성한다는 점에 주목하십시오. $\Phi$가 선형이기 때문에, 그것의 작용은 블록들의 선형 조합을 취함으로써 Choi 행렬로부터 복구될 수 있습니다.

### 3.2 채널의 Choi 상태
채널의 Choi 행렬에 대해 생각하는 또 다른 방법은 $n = \vert\Sigma\vert$로 나누면 그것이 밀도 행렬이라는 것입니다. 단순화를 위해 $\Sigma = \{0,\ldots,n-1\}$인 상황에 초점을 맞추고, 얽힌 상태 $\vert \psi \rangle$에 함께 있는 $\mathsf{X}$의 동일한 복사본 두 개가 있다고 상상해 봅시다.
<p>$$\vert \psi \rangle = \frac{1}{\sqrt{n}} \sum_{a = 0}^{n-1} \vert a \rangle \otimes \vert a \rangle$$</p>

밀도 행렬로서 이 상태는 다음과 같습니다.
<p>$$\vert \psi \rangle \langle \psi \vert = \frac{1}{n} \sum_{a,b = 0}^{n-1} \vert a\rangle\langle b \vert \otimes \vert a\rangle\langle b \vert$$</p>

만약 우리가 오른쪽 $\mathsf{X}$의 복사본에 $\Phi$를 적용한다면, 우리는 $n$으로 나눈 Choi 행렬을 얻습니다.

<p>$$(\operatorname{Id}\otimes \,\Phi) \bigl(\vert \psi \rangle \langle \psi \vert\bigr) = \frac{1}{n} \sum_{a,b = 0}^{n-1} \vert a\rangle\langle b \vert \otimes \Phi\bigl(\vert a\rangle\langle b \vert\bigr) = \frac{J(\Phi)}{n}$$</p>

말로 설명하면, 정규화 인자 $1/n$를 제외하고, $\Phi$의 Choi 행렬은 다음 그림이 묘사하는 것처럼 *최대 얽힘* 상태의 입력 시스템 쌍의 절반에 $\Phi$를 평가하여 얻는 밀도 행렬입니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/general-formulation-of-quantum-information/quantum-channels/Choi-state.svg" style="width:60%">

특히 이것은 채널의 Choi 행렬이 항상 양의 반정부호여야 함을 의미한다는 점에 주목하십시오.

또한 채널 $\Phi$가 오른쪽/위쪽 시스템에만 적용되기 때문에, 왼쪽/아래쪽 시스템의 축소 상태에 영향을 미칠 수 없다는 것을 알 수 있습니다. 당면한 경우, 그 상태는 완전히 혼합된 상태 $\mathbb{I}_{\mathsf{X}}/n$이며, 따라서

<p>$$\operatorname{Tr}_{\mathsf{Y}} \biggl(\frac{J(\Phi)}{n}\biggr) = \frac{\mathbb{I}_{\mathsf{X}}}{n}$$</p>

양변에서 분모 $n$을 제거하면 $$\operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) = \mathbb{I}_{\mathsf{X}}$$가 산출됩니다.

우리는 대안적으로 채널이 항상 트레이스를 보존해야 한다는 사실을 사용하여 이와 동일한 결론을 도출할 수 있으며, 따라서
<p>$$\begin{aligned} \operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) & = \sum_{a,b\in\Sigma} \operatorname{Tr}\bigl(\Phi( \vert a\rangle\langle b \vert)\bigr) \, \vert a\rangle\langle b \vert \\ & = \sum_{a,b\in\Sigma} \operatorname{Tr}\bigl(\vert a\rangle\langle b \vert\bigr) \, \vert a\rangle\langle b \vert \\ & = \sum_{a\in\Sigma} \vert a\rangle\langle a \vert \\ & = \mathbb{I}_{\mathsf{X}}. \end{aligned}$$</p>

요약하자면, 임의의 채널 $\Phi$에 대한 Choi 표현 $J(\Phi)$는 양의 반정부호여야 하며 다음을 만족해야 합니다.
<p>$$\operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) = \mathbb{I}_{\mathsf{X}}$$</p>
레슨이 끝날 때까지 알게 되겠지만, 이 두 조건은 필요할 뿐만 아니라 충분하기도 합니다. 즉, 이 요구 사항을 만족하는 행렬에서 행렬로의 모든 선형 매핑 $\Phi$는 실제로 채널이어야 합니다.

### 3.3 완전 역위상 채널

완전 역위상 채널 $\Delta$의 Choi 표현은 다음과 같습니다.
<p>$$\begin{aligned} J(\Delta) & = \sum_{a,b = 0}^{1} \vert a\rangle\langle b \vert \otimes \Delta\bigl(\vert a\rangle\langle b \vert\bigr) \\ & = \sum_{a = 0}^{1} \vert a\rangle\langle a \vert \otimes \vert a\rangle\langle a \vert \\[4mm] & = \begin{pmatrix} 1 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 1 \end{pmatrix}. \end{aligned}$$</p>

### 3.4 완전 탈분극 채널

완전 탈분극 채널의 Choi 표현은 다음과 같습니다.
<p>$$\begin{aligned} J(\Omega) & = \sum_{a,b = 0}^{1} \vert a\rangle\langle b \vert \otimes \Omega\bigl(\vert a\rangle\langle b \vert\bigr)\\ & = \sum_{a = 0}^{1} \vert a\rangle\langle a \vert \otimes \frac{1}{2} \mathbb{I} \\[4mm] & = \frac{1}{2} \mathbb{I} \otimes \mathbb{I}\\[3mm] & = \begin{pmatrix} \frac{1}{2} & 0 & 0 & 0\\[1mm] 0 & \frac{1}{2} & 0 & 0\\[1mm] 0 & 0 & \frac{1}{2} & 0\\[1mm] 0 & 0 & 0 & \frac{1}{2} \end{pmatrix}. \end{aligned}$$</p>

### 3.5 큐비트 리셋 채널
큐비트 리셋 채널 $\Phi$의 Choi 표현은 다음과 같습니다.
<p>$$\begin{aligned} J(\Lambda) & = \sum_{a,b = 0}^{1} \vert a\rangle\langle b \vert \otimes \Lambda\bigl(\vert a\rangle\langle b \vert\bigr)\\ & = \sum_{a = 0}^{1} \vert a\rangle\langle a \vert \otimes \vert 0\rangle \langle 0\vert\\[4mm] & = \mathbb{I} \otimes \vert 0\rangle \langle 0\vert\\[3mm] & = \begin{pmatrix} 1 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 1 & 0\\[1mm] 0 & 0 & 0 & 0 \end{pmatrix}. \end{aligned}$$</p>

### 3.6 항등 채널
큐비트 항등 채널 $\operatorname{Id}$의 Choi 표현은 다음과 같습니다.
<p>$$\begin{aligned} J(\operatorname{Id}) & = \sum_{a,b = 0}^{1} \vert a\rangle\langle b \vert \otimes \operatorname{Id}\bigl(\vert a\rangle\langle b \vert\bigr) \\ & = \sum_{a,b = 0}^{1} \vert a \rangle\langle b \vert \otimes \vert a\rangle \langle b \vert \\ & = \begin{pmatrix} 1 & 0 & 0 & 1\\[1mm] 0 & 0 & 0 & 0\\[1mm] 0 & 0 & 0 & 0\\[1mm] 1 & 0 & 0 & 1 \end{pmatrix}. \end{aligned}$$</p>

특히 $J(\operatorname{Id})$가 항등 행렬이 아니라는 점에 주목하십시오. Choi 표현은 행렬이 선형 매핑을 나타내는 일반적인 방식처럼 채널의 작용을 직접적으로 기술하지는 않습니다.

--- 

# 표현의 동등성 (Equivalence of the representations)

우리는 이제 채널을 수학적 용어로 나타내는 세 가지 다른 방법, 즉 **스타인스프링 표현(Stinespring representations)**, **크라우스 표현(Kraus representations)**, **초이 표현(Choi representations)**에 대해 논의했습니다. 우리는 또한 채널의 정의를 가지고 있는데, 이는 채널이 복합 시스템의 일부에만 적용될 때에도 항상 밀도 행렬을 밀도 행렬로 변환하는 선형 매핑이라고 명시합니다. 이 수업의 나머지는 이 세 가지 표현이 동등하며 정의를 정확하게 포착한다는 수학적 증명에 할애됩니다.

## 1. 증명 개요 (Overview of the proof)

우리의 목표는 네 가지 진술들의 집합의 동등성을 확립하는 것이며, 우리는 그것들을 정확하게 작성하는 것으로 시작할 것입니다. 네 가지 진술 모두 이 수업 전체에서 사용된 것과 동일한 규칙을 따릅니다. 즉, $\Phi$는 정방 행렬에서 정방 행렬로의 선형 매핑이며, 입력 행렬의 행과 열은 시스템 $\mathsf{X}$ (입력 시스템)의 고전적 상태와 대응되고, 출력 행렬의 행과 열은 시스템 $\mathsf{Y}$ (출력 시스템)의 고전적 상태와 대응됩니다.

1. $\Phi$는 $\mathsf{X}$에서 $\mathsf{Y}$로의 채널입니다. 즉, $\Phi$는 더 큰 복합 시스템의 한 부분에 작용할 때에도 항상 밀도 행렬을 밀도 행렬로 변환합니다.
2. **초이 행렬** $J(\Phi)$는 양의 반정부호(positive semidefinite)이며 $\operatorname{Tr}_{\mathsf{Y}}(J(\Phi)) = \mathbb{I}_{\mathsf{X}}$ 조건을 만족합니다.
3. $\Phi$에 대한 **크라우스 표현**이 존재합니다. 즉, 모든 입력 $\rho$에 대해 $\Phi(\rho) = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger}$ 식이 참이 되도록 하는 행렬 $A_0,\ldots,A_{N-1}$이 존재하며, 이 행렬들은 $\sum_{k = 0}^{N-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$ 조건을 만족합니다.
4. $\Phi$에 대한 **스타인스프링 표현**이 존재합니다. 즉, 쌍 $(\mathsf{W},\mathsf{X})$와 $(\mathsf{G},\mathsf{Y})$가 동일한 수의 고전적 상태를 갖는 시스템 $\mathsf{W}$와 $\mathsf{G}$가 존재하며, $(\mathsf{W},\mathsf{X})$에서 $(\mathsf{G},\mathsf{Y})$로의 유니터리 연산을 나타내는 유니터리 행렬 $U$가 존재하여 $\Phi(\rho) = \operatorname{Tr}_{\mathsf{G}}\bigl( U (\vert 0\rangle\langle 0 \vert \otimes \rho) U^{\dagger} \bigr)$를 만족합니다.

증명은 함의(implication)의 순환이 증명되는 방식으로 작동합니다. 목록의 첫 번째 진술은 두 번째 진술을 함의하고, 두 번째는 세 번째를, 세 번째는 네 번째를, 그리고 네 번째 진술은 첫 번째를 함의합니다. 이는 임의의 진술에서 다른 진술로 추이적으로(transitively) 따라갈 수 있기 때문에 $\Phi$의 주어진 선택에 대해 네 가지 진술 모두가 동등함(즉, 모두 참이거나 모두 거짓임)을 확립합니다. 

이는 일련의 진술이 동등함을 증명할 때 흔히 사용되는 전략이며, 이러한 맥락에서 유용한 방법은 증명하기 가장 쉬운 방식으로 함의를 설정하는 것입니다. 이 경우가 바로 그러하며, 사실 우리는 이미 네 가지 함의 중 두 가지를 접했습니다.

## 2. 채널에서 초이 행렬로 (Channels to Choi matrices)

위에 나열된 진술들을 번호로 참조하면, 증명해야 할 첫 번째 함의는 **1 $\Rightarrow$ 2**입니다. 이 함의는 채널의 초이 상태(Choi state)의 맥락에서 이미 논의되었습니다. 여기서는 수학적 세부 사항을 요약합니다. 입력 시스템 $\mathsf{X}$의 고전적 상태 집합이 $\Sigma$라고 가정하고 $n = \vert\Sigma\vert$라고 둡시다. $\Phi$가 $\mathsf{X}$의 두 복사본 중 두 번째에 다음과 같은 상태로 함께 적용되는 상황을 고려하십시오.

<p>$$
\vert \psi \rangle = \frac{1}{\sqrt{n}} \sum_{a \in \Sigma} \vert a \rangle \otimes \vert a \rangle,$$</p>

이는 밀도 행렬로 다음과 같이 주어집니다.

<p>$$
\vert \psi \rangle \langle \psi \vert = \frac{1}{n} \sum_{a,b \in \Sigma} \vert a\rangle\langle b \vert \otimes \vert a\rangle\langle b \vert.$$</p>

그 결과는 다음과 같이 작성될 수 있습니다.

<p>$$
(\operatorname{Id}\otimes \,\Phi) \bigl(\vert \psi \rangle \langle \psi \vert\bigr) = \frac{1}{n} \sum_{a,b = 0}^{n-1} \vert a\rangle\langle b \vert \otimes \Phi\bigl(\vert a\rangle\langle b \vert\bigr) = \frac{J(\Phi)}{n},$$</p>

그리고 $\Phi$가 채널이라는 가정에 의해 이것은 밀도 행렬이어야 합니다. 모든 밀도 행렬과 마찬가지로 이것은 **양의 반정부호**여야 하며, 양의 실수로 양의 반정부호 행렬을 곱하면 또 다른 양의 반정부호 행렬이 생성되므로, $J(\Phi) \geq 0$ 입니다.

더욱이, $\Phi$가 채널이라는 가정하에, 그것은 **대각합(trace)을 보존**해야 합니다. 따라서:

<p>$$
\begin{aligned} \operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) & = \sum_{a,b\in\Sigma} \operatorname{Tr}\bigl(\Phi( \vert a\rangle\langle b \vert)\bigr) \, \vert a\rangle\langle b \vert\\ & = \sum_{a,b\in\Sigma} \operatorname{Tr}\bigl(\vert a\rangle\langle b \vert\bigr) \, \vert a\rangle\langle b \vert\\ & = \sum_{a\in\Sigma} \vert a\rangle\langle a \vert\\ & = \mathbb{I}_{\mathsf{X}}. \end{aligned}$$</p>

## 3. 초이에서 크라우스 표현으로 (Choi to Kraus representations)

다시 목록의 번호로 진술을 참조하면, 두 번째 함의는 **2 $\Rightarrow$ 3**입니다. 분명히 말하자면, 우리는 다른 진술들을 무시하고 있으며, 특히 $\Phi$가 채널이라는 가정을 할 수 없습니다. 우리가 다뤄야 할 전부는 $\Phi$가 선형 매핑이고 그 초이 표현 $J(\Phi)$가 $J(\Phi) \geq 0$ 및 $$\operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) = \mathbb{I}_{\mathsf{X}}$$를 만족한다는 것입니다.

그러나 이것이 $\Phi$가 
<p> $$\sum_{k = 0}^{N-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$$</p> 
조건을 만족하는 크라우스 표현 

<p>$$\Phi(\rho) = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger}$$</p>
을 가진다고 결론 내리는 데 필요한 전부입니다. 우리는 $J(\Phi)$가 양의 반정부호라는 **결정적으로 중요한 가정**으로 시작합니다. 이는 다음과 같은 형식으로 표현하는 것이 가능함을 의미합니다.

<p>$$
J(\Phi) = \sum_{k = 0}^{N-1} \vert \psi_k \rangle \langle \psi_k \vert \tag{1}$$</p>

일부 벡터 $\vert\psi_0\rangle,\ldots,\vert\psi_{N-1}\rangle$를 선택하는 방식으로. 일반적으로 이를 수행하는 여러 가지 방법이 있을 것이며, 사실 이것은 $\Phi$에 대한 크라우스 표현을 선택하는 데 있어서의 자유도를 직접적으로 반영합니다. 이러한 표현을 얻는 한 가지 방법은 먼저 스펙트럼 정리(spectral theorem)를 사용하여 다음과 같이 작성하는 것입니다.

<p>$$
J(\Phi) = \sum_{k = 0}^{N-1} \lambda_k \vert \gamma_k \rangle \langle \gamma_k \vert,$$</p>

여기서 $\lambda_0,\ldots,\lambda_{N-1}$는 $J(\Phi)$의 고유값이며 (이는 $J(\Phi)$가 양의 반정부호이므로 필연적으로 음이 아닌 실수입니다), $\vert\gamma_0\rangle,\ldots,\vert\gamma_{N-1}\rangle$는 고유값 $\lambda_0,\ldots,\lambda_{N-1}$에 해당하는 단위 고유 벡터입니다.

고유값 선택에는 자유가 없지만 (순서가 지정되는 방식을 제외하고), 고유 벡터 선택에는 자유가 있으며, 특히 다중도가 1보다 큰 고유값이 있을 때 더욱 그렇습니다. 따라서 이것은 $J(\Phi)$의 고유한 표현이 아닙니다. 우리는 단지 그러한 표현 중 하나를 가지고 있다고 가정하는 것뿐입니다. 그럼에도 불구하고, 고유값은 음이 아닌 실수이므로, 음이 아닌 제곱근을 가지며, 따라서 우리는 다음을 선택할 수 있습니다.

<p>$$
\vert\psi_k\rangle = \sqrt{\lambda_k} \vert \gamma_k\rangle$$</p>

$k = 0 , \ldots , N − 1$ 각각에 대해 (1) 형식의 표현을 얻습니다. 

그러나 표현 (1)이 반드시 이러한 방식으로 스펙트럼 분해에서 비롯될 필요는 없으며, 특히 벡터 $\vert\psi_0\rangle,\ldots,\vert\psi_{N-1}\rangle$는 일반적으로 직교할 필요가 없습니다. 주목할 만한 점은, 우리가 원한다면 이 벡터들을 직교하도록 선택할 수 있으며, 더욱이 우리는 $N$이 $nm$보다 클 필요가 없다는 것입니다 (여기서 $n$과 $m$은 각각 $\mathsf{X}$와 $\mathsf{Y}$의 고전적 상태 수를 나타냅니다).

다음으로, 벡터 $\vert\psi_0\rangle,\ldots,\vert\psi_{N-1}\rangle$ 각각은 다음과 같이 더 분해될 수 있으며:

<p>$$
\vert\psi_k\rangle = \sum_{a\in\Sigma} \vert a\rangle \otimes \vert \phi_{k,a}\rangle,$$</p>

여기서 벡터 $\{\vert \phi_{k,a}\rangle\}$는 $\mathsf{Y}$의 고전적 상태에 해당하는 항목을 가지며 다음 방정식으로 명시적으로 결정될 수 있습니다.

<p>$$
\vert \phi_{k,a}\rangle = \bigl( \langle a \vert \otimes \mathbb{I}_{\mathsf{Y}}\bigr) \vert \psi_k\rangle$$</p>

$a \in \Sigma$ 및 $k = 0 , \ldots , N − 1$ 각각에 대해. $\vert\psi_0\rangle,\ldots,\vert\psi_{N-1}\rangle$가 반드시 단위 벡터일 필요는 없지만, 이것은 쌍 $(\mathsf{X},\mathsf{Y})$의 양자 상태 벡터가 주어졌을 때 시스템 $\mathsf{X}$에 표준 기저 측정(standard basis measurement)이 수행된다면 무슨 일이 일어날지 분석하는 데 사용할 것과 동일한 과정입니다.

그리고 이제 우리는 이 증명 부분이 작동하도록 만드는 핵심 기술에 도달합니다. 우리는 다음 방정식에 따라 크라우스 행렬 $A_0,\ldots,A_{N-1}$을 정의합니다.

<p>$$
A_k = \sum_{a\in\Sigma} \vert \phi_{k,a}\rangle\langle a \vert$$</p>

우리는 이 공식을 순전히 상징적으로 생각할 수 있습니다. $\vert a \rangle$는 효과적으로 뒤집혀 $\langle a \vert$를 형성하고 오른쪽으로 이동하여 행렬을 형성합니다. 증명을 확인하는 목적으로는 이 공식만 있으면 됩니다.

그러나 벡터 $\vert\psi_k\rangle$와 행렬 $A_k$ 사이에는 간단하고 직관적인 관계가 있습니다. 즉, $A_k$를 **벡터화(vectorizing)**하면 $\vert\psi_k\rangle$를 얻는다는 것입니다. $A_k$를 벡터화한다는 것은 벡터를 형성하기 위해 열들을 위에서부터 아래로 차례로 쌓는 것(가장 왼쪽 열이 위에서 시작하여 가장 오른쪽 열이 아래로 가는 방식)을 의미합니다. 예를 들어, $\mathsf{X}$와 $\mathsf{Y}$가 모두 큐비트이고, $k$의 어떤 선택에 대해 우리가 다음을 가진다면:

<p>$$
\begin{aligned} \vert\psi_k\rangle & = \alpha_{00} \vert 0\rangle \otimes \vert 0\rangle + \alpha_{01} \vert 0\rangle \otimes \vert 1\rangle + \alpha_{10} \vert 1\rangle \otimes \vert 0\rangle + \alpha_{11} \vert 1\rangle \otimes \vert 1\rangle\\[2mm] & = \begin{pmatrix} \alpha_{00} \\[1mm] \alpha_{01} \\[1mm] \alpha_{10} \\[1mm] \alpha_{11} \end{pmatrix}, \end{aligned}$$</p>

그러면:

<p>$$
\begin{aligned} A_k & = \alpha_{00} \vert 0\rangle\langle 0\vert + \alpha_{01} \vert 1\rangle\langle 0\vert + \alpha_{10} \vert 0\rangle\langle 1\vert + \alpha_{11} \vert 1\rangle\langle 1\vert\\[2mm] & = \begin{pmatrix} \alpha_{00} & \alpha_{10}\\[1mm] \alpha_{01} & \alpha_{11} \end{pmatrix}. \end{aligned}$$</p>

(주의: 때때로 행렬의 벡터화는 행렬의 **행**을 전치(transpose)하여 서로 쌓아 열 벡터를 형성하는 약간 다른 방식으로 정의되기도 합니다.) 

먼저 우리는 이 크라우스 행렬의 선택이 매핑 $\Phi$를 올바르게 설명하는지 확인하고, 그 후에 필요한 다른 조건을 확인할 것입니다. 혼동을 피하기 위해, 새로운 매핑 $\Psi$를 다음과 같이 정의해 봅시다.

<p>$$
\Psi(\rho) = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger}$$</p>

따라서 우리의 목표는 $\Psi = \Phi$임을 확인하는 것입니다. 

이를 수행하는 방법은 이 매핑들의 초이 표현을 비교하는 것입니다. 초이 표현은 충실하므로 (faithful), $J(\Phi) = J(\Psi)$일 때 그리고 그 때에만 $\Psi = \Phi$입니다. 이 시점에서 우리는 텐서 곱의 쌍선형성(bilinearity)과 함께 다음 표현을 사용하여 $J(\Psi)$를 간단히 계산할 수 있습니다.

<p>$$
\vert\psi_k\rangle = \sum_{a\in\Sigma} \vert a\rangle \otimes \vert \phi_{k,a}\rangle \quad\text{and}\quad A_k = \sum_{a\in\Sigma} \vert \phi_{k,a}\rangle\langle a \vert$$</p>

<p>$$
\begin{aligned} J(\Psi) & = \sum_{a,b\in\Sigma} \vert a\rangle \langle b \vert \otimes \sum_{k = 0}^{N-1} A_k \vert a\rangle \langle b \vert A_k^{\dagger}\\[2mm] & = \sum_{a,b\in\Sigma} \vert a\rangle \langle b \vert \otimes \sum_{k = 0}^{N-1} \vert \phi_{k,a} \rangle \langle \phi_{k,b} \vert \\[2mm] & = \sum_{k = 0}^{N-1} \biggl(\sum_{a\in\Sigma} \vert a\rangle \otimes \vert \phi_{k,a} \rangle\biggr) \biggl(\sum_{b\in\Sigma} \langle b\vert \otimes \langle \phi_{k,b} \vert\biggr)\\[2mm] & = \sum_{k = 0}^{N-1} \vert \psi_k \rangle \langle \psi_k \vert \\[2mm] & = J(\Phi) \end{aligned}$$</p>

따라서 우리의 크라우스 행렬은 $\Phi$를 올바르게 설명합니다. 


이제 $A_0,\ldots,A_{N-1}$에 대한 필수 조건을 확인해야 하는데, 이는 가정 $\operatorname{Tr}_{\mathsf{Y}}(J(\Phi)) = \mathbb{I}_{\mathsf{X}}$ (아직 사용하지 않은 가정)과 동등하다는 것이 밝혀집니다. 우리가 보여줄 관계는 다음과 같습니다.

<p>$$
\Biggl( \sum_{k = 0}^{N-1} A_k^{\dagger} A_k \Biggr)^{T} = \operatorname{Tr}_{\mathsf{Y}}(J(\Phi)) \tag{2}$$</p>

(여기서 우리는 왼쪽의 **행렬 전치(matrix transpose)**를 참조하고 있습니다).

왼쪽부터 시작하여, 우리는 먼저 다음을 관찰할 수 있습니다.

<p>$$
\begin{aligned} \Biggl(\sum_{k = 0}^{N-1} A_k^{\dagger} A_k\Biggr)^T & = \Biggl(\sum_{k = 0}^{N-1} \sum_{a,b\in\Sigma} \vert b \rangle \langle \phi_{k,b} \vert \phi_{k,a} \rangle \langle a \vert\Biggr)^T\\ & = \sum_{k = 0}^{N-1} \sum_{a,b\in\Sigma} \langle \phi_{k,b} \vert \phi_{k,a} \rangle \vert a \rangle \langle b \vert. \end{aligned}$$</p>

마지막 등식은 전치가 선형이고 $\vert b \rangle \langle a \vert$를 $\vert a \rangle \langle b \vert$로 매핑한다는 사실에서 비롯됩니다. 우리 방정식의 오른쪽으로 이동하면:

<p>$$
J(\Phi) = \sum_{k = 0}^{N-1} \vert \psi_k\rangle\langle\psi_k \vert = \sum_{k = 0}^{N-1} \sum_{a,b\in\Sigma} \vert a\rangle \langle b \vert \otimes \vert\phi_{k,a}\rangle\langle \phi_{k,b} \vert$$</p>

따라서:

<p>$$
\begin{aligned} \operatorname{Tr}_{\mathsf{Y}}(J(\Phi)) & = \sum_{k = 0}^{N-1} \sum_{a,b\in\Sigma} \operatorname{Tr}\bigl(\vert\phi_{k,a}\rangle\langle \phi_{k,b} \vert \bigr)\, \vert a\rangle \langle b \vert\\ & = \sum_{k = 0}^{N-1} \sum_{a,b\in\Sigma} \langle \phi_{k,b} \vert \phi_{k,a} \rangle \vert a \rangle \langle b \vert. \end{aligned}$$</p>

우리는 동일한 결과를 얻었으며, 따라서 방정식 (2)가 확인되었습니다. 가정 $\operatorname{Tr}_{\mathsf{Y}} (J(\Phi)) = \mathbb{I}_{\mathsf{X}}$에 의해 다음과 같이 성립하며:

<p>$$
\Biggl(\sum_{k = 0}^{N-1} A_k^{\dagger} A_k\Biggr)^T = \mathbb{I}_{\mathsf{X}}$$</p>

따라서 항등 행렬(identity matrix)은 그 자체의 전치이므로, 요구되는 조건은 참입니다.

<p>$$
\sum_{k = 0}^{N-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$$</p>

## 4. 크라우스에서 스타인스프링 표현으로 (Kraus to Stinespring representations)

이제 우리는 
<p>$$\sum_{k = 0}^{N-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$$ </p>
인 매핑 
<p>$$\Phi(\rho) = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger}$$</p> 의 크라우스 표현을 가지고 있다고 가정합니다. 우리의 목표는 $\Phi$에 대한 스타인스프링 표현을 찾는 것입니다.

우리가 먼저 하고 싶은 것은 가비지 시스템(garbage system) $\mathsf{G}$를 고전적 상태 집합이 $\{0,\ldots,N-1\}$이 되도록 선택하는 것입니다. 그러나 $(\mathsf{W},\mathsf{X})$와 $(\mathsf{G},\mathsf{Y})$가 같은 크기를 갖기 위해서는, $n$이 $mN$을 나누어야 하며, 이로 인해 $d = mN/n$에 대해 $\mathsf{W}$가 $\{0,\ldots,d-1\}$의 고전적 상태를 갖도록 취할 수 있습니다.

$n$, $m$, 그리고 $N$의 임의의 선택에 대해, $mN/n$이 정수(integer)가 아닐 수 있으므로, 우리는 실제로 $\mathsf{G}$의 고전적 상태 집합이 $\{0,\ldots,N-1\}$이 되도록 자유롭게 선택할 수 없습니다. 그러나 원하는 만큼의 추가적인 $k$ 값에 대해 $A_k = 0$을 선택함으로써 크라우스 표현에서 $N$을 임의로 증가시킬 수 있습니다. 

따라서 만약 우리가 $mN/n$이 정수라고 묵시적으로 가정한다면 (이는 $N$이 $m/\operatorname{gcd}(n,m)$의 배수라는 것과 동등합니다), 우리는 $\mathsf{G}$의 고전적 상태 집합이 $\{0,\ldots,N-1\}$이 되도록 자유롭게 취할 수 있습니다. 특히, $N = nm$인 경우, $\mathsf{W}$가 $m^2$개의 고전적 상태를 갖도록 취할 수 있습니다.

이제 $U$를 선택해야 하며, 우리는 다음 패턴과 일치시켜 이를 수행할 것입니다.

<p>$$
U = \begin{pmatrix} A_{0} & \fbox{?} & \cdots & \fbox{?} \\[1mm] A_{1} & \fbox{?} & \cdots & \fbox{?} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] A_{N-1} & \fbox{?} & \cdots & \fbox{?} \end{pmatrix}
$$</p>

분명히 하자면, 이 패턴은 블록 행렬을 나타내기 위한 것이며, 여기서 각 블록( $A_{0},\ldots,A_{N-1}$ 및 물음표로 표시된 블록 포함)은 $m$개의 행과 $n$개의 열을 가집니다. 블록은 $N$개의 행을 가지며, 이는 블록의 열이 $d = mN/n$개임을 의미합니다.

더 공식적인 용어로 표현하면, 우리는 $U$를 다음과 같이 정의할 것입니다.

<p>$$
\begin{aligned} U & = \sum_{k=0}^{N-1} \sum_{j=0}^{d-1} \vert k \rangle \langle j \vert \otimes M_{k,j} \\[4mm] & = \begin{pmatrix} M_{0,0} & M_{0,1} & \cdots & M_{0,d-1} \\[1mm] M_{1,0} & M_{1,1} & \cdots & M_{1,d-1} \\[1mm] \vdots & \vdots & \ddots & \vdots\\[1mm] M_{N-1,0} & M_{N-1,1} & \cdots & M_{N-1,d-1} \end{pmatrix} \end{aligned}
$$</p>

여기서 각 행렬 $M_{k,j}$는 $m$개의 행과 $n$개의 열을 가지며, 특히 우리는 $k = 0 , \ldots , N − 1$에 대해 $M_{k,0} = A_k$를 취할 것입니다. 

이것은 유니터리 행렬이어야 하며, 물음표가 붙은 블록, 또는 이와 동등하게 $j > 0$인 $M_{k,j}$는 이를 염두에 두고 선택되어야 합니다. 하지만 $U$가 유니터리하게 되는 것을 허용하는 것을 제외하고, 물음표가 붙은 블록들은 증명과는 아무런 관련이 없을 것입니다.

$U$가 유니터리하다는 우려를 잠시 접어두고, 우리의 스타인스프링 표현에 대해 $\mathsf{X}$의 입력 상태 $\rho$가 주어졌을 때 $\mathsf{Y}$의 출력 상태를 설명하는 
<p>$$\operatorname{Tr}_{\mathsf{G}} \bigl( U (\vert 0\rangle \langle 0 \vert_{\mathsf{W}} \otimes \rho)U^{\dagger}\bigr)$$</p> 표현에 초점을 맞춥시다. 우리는 대안적으로 다음과 같이 쓸 수 있습니다.

<p>$$
U(\vert 0\rangle\langle 0 \vert \otimes \rho)U^{\dagger} = U(\vert 0\rangle\otimes\mathbb{I}_{\mathsf{W}}) \rho (\langle 0\vert \otimes \mathbb{I}_{\mathsf{W}}) U^{\dagger},$$</p>

그리고 $U$의 선택으로부터 다음을 알 수 있습니다.

<p>$$
U(\vert 0\rangle\otimes\mathbb{I}_{\mathsf{W}}) = \sum_{k = 0}^{N-1} \vert k\rangle \otimes A_k.$$</p>

따라서 우리는 다음을 발견합니다.

<p>$$
U(\vert 0\rangle\langle 0 \vert \otimes \rho)U^{\dagger} = \sum_{j,k = 0}^{N-1} \vert k\rangle\langle j\vert \otimes A_k \rho A_j^{\dagger},$$</p>

따라서:

<p>$$
\begin{aligned} \operatorname{Tr}_{\mathsf{G}} \bigl( U (\vert 0\rangle \langle 0 \vert_{\mathsf{W}} \otimes \rho) U^{\dagger}\bigr) & = \sum_{j,k = 0}^{N-1} \operatorname{Tr}\bigl(\vert k\rangle\langle j\vert\bigr) \, A_k \rho A_j^{\dagger} \\ & = \sum_{k = 0}^{N-1} A_k \rho A_k^{\dagger} \\ & = \Phi(\rho). \end{aligned}
$$</p>

따라서 우리는 매핑 $\Phi$에 대한 정확한 표현을 가지고 있으며, $U$를 유니터리로 선택할 수 있는지 확인하는 것이 남았습니다. 

위 패턴에 따라 선택된 $U$의 첫 번째 $n$개 열을 고려해 봅시다. 이 열들만 취하면 다음과 같은 블록 행렬을 얻습니다.

<p>$$
\begin{pmatrix} A_0\\[1mm] A_1\\[1mm] \vdots\\[1mm] A_{N-1} \end{pmatrix}.
$$</p>

$\mathsf{X}$의 각 고전적 상태에 대해 하나씩, $n$개의 열이 있으며, 벡터로서 이 열들을 각 $a \in \Sigma$에 대해 $\vert \gamma_a \rangle$라고 명명합시다. 다음은 위에 있는 블록 행렬 표현과 일치시킬 수 있는 이 벡터들에 대한 공식입니다.

<p>$$
\vert \gamma_a\rangle = \sum_{k = 0}^{N-1} \vert k\rangle \otimes A_k \vert a \rangle$$</p>

이제 이 벡터들 중 임의의 두 벡터 사이의 내적을 계산해 봅시다. 즉, $a , b \in \Sigma$의 임의의 선택에 해당하는 벡터들입니다.

<p>$$
\langle \gamma_a \vert \gamma_b \rangle = \sum_{j,k = 0}^{N-1} \langle k \vert j \rangle \, \langle a \vert A_k^{\dagger} A_j \vert b\rangle = \langle a \vert \Biggl( \sum_{k = 0}^{N-1} A_k^{\dagger} A_k \Biggr) \vert b\rangle$$</p>

가정에 의해
<p>$$\sum_{k = 0}^{m-1} A_k^{\dagger} A_k = \mathbb{I}_{\mathsf{X}}$$</p> , 우리는 $n$개의 열 벡터 $\{\vert\gamma_a\rangle\,:\,a\in\Sigma\}$가 정규 직교 집합(orthonormal set)을 형성한다고 결론 내립니다:

모든 $a, b \in \Sigma$에 대해.
<p>$$
\langle \gamma_a \vert \gamma_b \rangle = \begin{cases} 1 & a = b\\ 0 & a\neq b \end{cases}$$</p>


이것은 $U$의 나머지 열들을 채워서 유니터리 행렬이 되도록 하는 것이 **가능함**을 의미합니다. 특히, **그램-슈미트 직교화 과정(Gram-Schmidt orthogonalization process)**을 사용하여 나머지 열들을 선택할 수 있습니다. 이와 유사한 작업이 상태 판별 문제(state discrimination problem)의 맥락에서 "Basics of quantum information"의 *Quantum circuits* 수업에서 수행되었습니다.

## 5. 스타인스프링 표현에서 정의로 돌아가기 (Stinespring representations back to the definition)

최종 함의는 **4 $\Rightarrow$ 1**입니다. 즉, 우리는 시스템 쌍 $(\mathsf{W},\mathsf{X})$를 쌍 $(\mathsf{G},\mathsf{Y})$로 변환하는 유니터리 연산이 있다고 가정하며, 우리의 목표는 다음 매핑이:

<p>$$
\Phi(\rho) = \operatorname{Tr}_{\mathsf{G}} \bigl( U (\vert 0\rangle \langle 0 \vert_{\mathsf{W}} \otimes \rho)U^{\dagger}\bigr)$$</p>

유효한 채널이라고 결론 내리는 것입니다. 그 형태로부터 $\Phi$가 선형이라는 것은 분명하며, 항상 밀도 행렬을 밀도 행렬로 변환하는지 확인하는 것이 남아 있습니다. 이것은 매우 간단하며 우리는 이미 핵심 사항을 논의했습니다. 

특히, 복합 시스템 $(\mathsf{Z},\mathsf{X})$의 밀도 행렬 $\sigma$로 시작하여, 추가 작업 공간 시스템 $\mathsf{W}$를 추가하면, 우리는 확실히 밀도 행렬을 갖게 될 것입니다. 편의를 위해 시스템 $(\mathsf{W},\mathsf{Z},\mathsf{X})$의 순서를 재배열하면 이 상태를 다음과 같이 쓸 수 있습니다.

<p>$$
\vert 0\rangle\langle 0\vert_{\mathsf{W}} \otimes \sigma.$$</p>

그런 다음 우리는 유니터리 연산 $U$를 적용하며, 이미 논의했듯이 이것은 유효한 채널이므로, 밀도 행렬을 밀도 행렬로 매핑합니다. 마지막으로, 밀도 행렬의 **부분 대각합(partial trace)**은 또 다른 밀도 행렬입니다. 이를 다르게 표현하는 방법은 다음 각각이 유효한 채널임을 먼저 관찰하는 것입니다.

1. 초기화된 작업 공간 시스템 도입.
2. 유니터리 연산 수행.
3. 시스템의 대각합 제거 (Tracing out a system).

그리고 마지막으로, 채널의 임의의 합성(composition)은 또 다른 채널입니다. 이는 정의로부터 즉각적이지만, 그 자체로 주목할 가치가 있는 사실이기도 합니다. 이것으로 최종 함의의 증명이 완료되었으며, 따라서 우리는 섹션 시작 부분에 나열된 네 가지 진술의 동등성을 확립했습니다.