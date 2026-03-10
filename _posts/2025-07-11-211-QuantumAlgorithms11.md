---
title: 6차시 11:Fundamentals of quantum algorithms(Unstructured search)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# Grover's algorithm


---

## 1.비정형 검색(Unstructured search)

### 1.1 요약
Grover의 알고리즘이 해결하는 문제에 대한 설명으로 시작하겠습니다. 늘 그렇듯이, 이 논의 전체에서 **이진 문자열**을 나타내도록 하겠습니다. 길이가 $n$인 이진 문자열에서 비트로 가는 함수 $f$라고 가정해 봅시다. 

<p>$$
f:\Sigma^n \rightarrow \Sigma
$$
</p>

우리는 이 함수를 효율적으로 계산할 수 있다고 가정하지만, 그 외에는 임의의 함수이며 우리의 필요에 맞는 특별한 구조나 특정 구현에 의존할 수 없습니다.

Grover의 알고리즘이 하는 일은 $f(x) = 1$에 대해 문자열 $x$를 검색하는 것입니다. 우리는 이와 같은 문자열을 검색 문제의 ***솔루션(solutions)*** 이라고 부를 것입니다. 솔루션이 여러 개 있는 경우, 그중 하나라도 올바른 출력으로 간주되며, 솔루션이 없는 경우, 올바른 답변은 솔루션이 없음을 보고해야 합니다. 

이 작업은 $f$가 쉽게 만들 수 있는 특정 구조에 의존할 수 없기 때문에 ***비정형 검색(unstructured search)*** 문제로 설명됩니다. 우리는 정렬된 목록이나 검색을 용이하게 하기 위해 특별히 설계된 일부 데이터 구조 내에서 검색하는 것이 아니라, 본질적으로 **건초 더미에서 바늘을 찾는 것**과 같습니다.

직관적으로 말하면, 우리는 $f$를 계산하는 매우 복잡한 부울 회로를 가지고 있으며, 우리가 선택하면 선택된 입력 문자열에 대해 이 회로를 쉽게 실행할 수 있다고 상상할 수 있습니다. 그러나 회로가 너무 복잡하기 때문에 (선택된 입력 문자열에 대해 회로를 평가할 수 있는 능력을 넘어서) 회로를 검사하여 이해하는 것은 불가능합니다. 이 검색 작업을 고전적으로 수행하는 한 가지 방법은 단순히 모든 문자열 $x$를 반복하고, 각각에 대해 $f$를 평가하여 솔루션인지 확인하는 것입니다. 이제부터, 다음처럼 작성하겠습니다.

<p>$$
N = 2^n
$$
</p>

편의를 위해 $N=2^n$라고 쓰겠습니다. $\Sigma^n$에 $N$개의 문자열이 있으므로, 이들 모두를 반복하려면 $f$의 $N$회 평가가 필요합니다. 우리가 선택한 입력에 대해 $f$를 평가하는 것으로 제한된다는 가정 하에 작동한다면, 이것은 성공을 보장하고자 할 때 결정론적 알고리즘으로 할 수 있는 최선입니다. 확률적 알고리즘으로는 $f$에 대한 입력 문자열을 무작위로 선택하여 시간을 절약할 수 있기를 바라지만, 이 방법이 높은 확률로 성공하기를 원한다면 여전히 $O(N)$의 $f$ 평가가 필요할 것입니다.

Grover의 알고리즘은 단지 $O(\sqrt{N})$의 $f$ 평가만으로 이 검색 문제를 높은 확률로 해결합니다. 명확히 하자면, 이러한 함수 평가는 **Deutsch의 알고리즘, Deutsch-Jozsa 알고리즘, Simon의 알고리즘을 포함하여** ***양자 질의 알고리즘(Quantum query algorithms)*** 레슨에서 논의된 질의 알고리즘과 유사하게 ***중첩 상태에서(in superposition)*** 발생해야 합니다. 이러한 알고리즘들과 달리, Grover의 알고리즘은 **반복적인 접근 방식**을 취합니다. 즉, 입력 문자열의 중첩 상태에서 $f$를 평가하고, 이러한 평가를 **간섭 패턴을 생성하는 효과**를 갖는 다른 연산들과 섞어서 (솔루션이 존재할 경우) $O(\sqrt{N})$ 반복 후에 높은 확률로 솔루션으로 이어지게 합니다.

### 1.2 공식적인 문제 진술

우리는 Grover의 알고리즘이 해결하는 문제를 계산의 **질의 모델**을 사용하여 공식화할 것입니다. 즉, 우리는 일반적인 방식으로 정의된 질의 게이트 $U_f$를 통해 함수 $f$에 접근할 수 있다고 가정할 것입니다. 

모든 $x \in \Sigma^n$와 $a \in \Sigma$에 대해 $U_f \bigl( \vert a\rangle \vert x\rangle \bigr) = \vert a \oplus f(x) \rangle \vert x \rangle$가 성립합니다. 

이것은 표준 기저 상태에 대한 $U_f$의 작용이며, 일반적인 작용은 선형성에 의해 결정됩니다.

***양자 알고리즘 기초(Quantum algorithmic foundations)*** 레슨에서 논의되었듯이, $f$를 계산하기 위한 부울 회로가 있다면, 우리는 그 부울 회로 설명을 $U_f$를 구현하는 양자 회로로 변환할 수 있습니다 (계산을 $\vert 0\rangle$ 상태에서 시작하고 끝내는 일정 수의 작업 공간 큐비트를 사용하여). 따라서, 우리는 Grover의 알고리즘이 해결하는 문제를 공식화하기 위해 질의 모델을 사용하고 있지만, 이 모델에 국한되지 않습니다. 우리는 부울 회로를 가지고 있는 모든 함수 $f$에 대해 Grover의 알고리즘을 실행할 수 있습니다.

여기에 문제에 대한 정확한 진술이 있습니다. 이 문제는 솔루션, 즉 $f$가 $1$로 평가되게 하는 문자열 $x$를 검색하고 있기 때문에 ***검색(Search)*** 이라고 명명됩니다.

>**검색(Search)** 
*   입력: 함수 $f$ 
*   출력: $f(x) = 1$을 만족하는 문자열 $x$ 또는 그러한 문자열 $x$가 존재하지 않는 경우 "솔루션 없음"

이 문제는 ***약속 문제(promise problem)*** 가 아니라는 점에 주목하십시오. 함수 $f$는 임의적입니다. 그러나 단 하나의 솔루션이 존재한다고 보장되는 문제의 다음 약속 변형을 고려하는 것이 도움이 될 것입니다. 이 문제는 ***양자 질의 알고리즘*** 레슨에서 약속 문제의 예시로 나타났습니다.

>**고유 검색(Unique search)**
*   입력: $f$ 형태의 함수
*   약속: $f(z) = 1$인 문자열 $z$가 정확히 하나 존재하며, 모든 문자열 $x \neq z$에 대해서는 $f(x) = 0$이다
*   출력: 문자열 $z$

또한, 같은 레슨에서 언급된 ***Or*** 문제는 ***검색(Search)***과 밀접하게 관련되어 있다는 점에 주목하십시오. 그 문제의 목표는 실제로 솔루션을 찾는 것이 아니라, 솔루션이 존재하는지 여부를 단순히 결정하는 것입니다.

--- 

## 2. **그로버 알고리즘에 대한 설명**

이 섹션에서는 그로버 알고리즘을 설명할 것입니다. 우리는 **위상 질의 게이트(phase query gates)** 와 이를 구축하는 방법을 논의하는 것부터 시작하여, 그로버 알고리즘 자체에 대한 설명으로 이어질 것입니다. 마지막으로, 이 알고리즘이 검색에 자연스럽게 적용되는 방식에 대해 간략하게 논의할 것입니다.

### 2.1 **위상 질의 게이트**

그로버 알고리즘은 **위상 질의 게이트**로 알려진 연산을 사용합니다. 이전에 설명된 일반적인 방식으로 주어진 함수 $f$에 대해 정의된 일반적인 질의 게이트 $U_f$와 대조적으로, 함수 $f$에 대한 위상 질의 게이트는 다음과 같이 정의됩니다.

<p>
$$
Z_f \vert x\rangle = (-1)^{f(x)} \vert x\rangle
$$
</p>

모든 문자열 $x$에 대해. 연산 $Z_f$는 이 다이어그램이 시사하듯이 하나의 질의 게이트 $U_f$를 사용하여 구현될 수 있습니다. 

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/grover-algorithm/Z_f.svg" style="width:60%">

이 구현은 **위상 킥백(phase kickback) 현상**을 활용하며, $\vert -\rangle$ 상태로 초기화된 하나의 워크스페이스 큐비트를 사용할 수 있도록 요구합니다. 이 큐비트는 구현이 완료된 후에도 $\vert -\rangle$ 상태를 유지하며, (예를 들어, 후속 $Z_f$ 게이트를 구현하기 위해) 재사용되거나 단순히 버려질 수 있습니다.

연산 $Z_f$ 외에도, 우리는 $n$-비트 OR 함수에 대한 위상 질의 게이트도 사용할 것인데, 이는 각 문자열 $x$에 대해 다음과 같이 정의됩니다. 

<p>$$
\mathrm{OR}(x) =
\begin{cases}
  0 & x = 0^n\\[0.5mm]
  1 & x \neq 0^n
\end{cases}
$$
</p>

명시적으로, $n$-비트 OR 함수에 대한 위상 질의 게이트는 다음과 같이 작동합니다.

<p>$$
Z_{\mathrm{OR}} \vert x\rangle
= \begin{cases}
  \vert x\rangle & x = 0^n \\[0.5mm]
- \vert x\rangle & x \neq 0^n.
\end{cases}
$$</p>

분명히 하자면, 이것은 $Z_{\mathrm{OR}}$이 표준 기저 상태에서 작동하는 방식입니다. 임의의 상태에 대한 그것의 동작은 이 표현으로부터 선형성에 의해 결정됩니다. 

연산 $Z_{\mathrm{OR}}$은 OR 함수에 대한 불리언 회로로 시작하여, *양자 알고리즘 기초(Quantum algorithmic foundations)* 레슨에 설명된 절차를 사용하여 $U_{\mathrm{OR}}$ 연산(즉, $n$-비트 OR 함수에 대한 표준 질의 게이트)을 구성하고, 마지막으로 위에서 설명한 위상 킥백 현상을 사용하여 $Z_{\mathrm{OR}}$ 연산을 구성함으로써 양자 회로로 구현될 수 있습니다. 연산 $Z_{\mathrm{OR}}$은 함수 $f$에 의존하지 않으며, 따라서 질의 게이트가 없는 양자 회로로 구현될 수 있다는 점에 주목하십시오.

### 2.2 **알고리즘 설명**

이제 두 가지 연산 $Z_f$와 $Z_{\mathrm{OR}}$을 확보했으므로, 그로버 알고리즘을 설명할 수 있습니다.

이 알고리즘은 숫자 $t$를 참조하는데, 이는 알고리즘이 수행하는 **반복 횟수**(따라서 함수 $f$에 대해 필요한 **질의 횟수**)입니다. 우리가 설명하는 방식으로는 이 숫자 $t$가 그로버 알고리즘에 의해 지정되지 않으며, 레슨 후반부에서 이를 선택하는 방법을 논의할 것입니다.

>**그로버 알고리즘**
1. **$n$ 큐비트 레지스터 $Q$를 모든 비트가 0인 상태로 초기화한 다음, $Q$의 각 큐비트에 하다마드(Hadamard) 연산을 적용합니다.**
2. **유니타리 연산 $G$를 레지스터 $Q$에 $t$번 적용합니다.**
3. **$Q$의 큐비트를 표준 기저 측정에 따라 측정하고 결과 문자열을 출력합니다.**

2단계에서 반복되는 연산 $G$는 이 레슨의 나머지 부분에서 **그로버 연산**이라고 불릴 것입니다. 다음은 $n=7$일 때의 그로버 연산에 대한 양자 회로 표현입니다. 

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/grover-algorithm/Grover_operation.svg" style="width:60%">

이 다이어그램에서, $Z_f$ 연산은 $Z_{\mathrm{OR}}$보다 더 크게 묘사되는데, 이는 비공식적인 시각적 단서로서 $Z_f$가 더 비용이 많이 드는 연산일 가능성이 있음을 시사합니다. 특히, 우리가 질의 모델 내에서 작업할 때, $Z_f$는 하나의 질의를 필요로 하는 반면, $Z_{\mathrm{OR}}$은 질의를 필요로 하지 않습니다. 만약 우리가 대신 함수 $f$에 대한 불리언 회로를 가지고 있고, 이를 $Z_f$에 대한 양자 회로로 변환한다면, 결과 양자 회로가 $Z_{\mathrm{OR}}$에 대한 회로보다 더 크고 복잡할 것이라고 합리적으로 예상할 수 있습니다.

다음은 $n = 7$이고 $t = 3$일 때 전체 알고리즘에 대한 양자 회로 다이어그램입니다. 더 큰 $t$ 값의 경우, 측정 직전에 그로버 연산의 추가 인스턴스를 삽입하기만 하면 됩니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/grover-algorithm/Grover_circuit.svg" style="width:80%">

### 2.3 **검색에의 적용**

그로버 알고리즘은 다음과 같이 **검색(Search)** 문제에 적용될 수 있습니다.

*   2단계에서 숫자 $t$를 선택합니다. (이는 레슨 후반부에서 논의됩니다.)
*   $t$에 대해 우리가 선택한 것을 사용하여 함수 $f$에 대해 그로버 알고리즘을 실행하여 문자열 $x$를 얻습니다.
* 문자열 $x$에 대해 함수 $f$를 질의하여 유효한 해인지 확인합니다.
    *   **만약 $f(x)=1$이라면, 우리는 해를 찾은 것이므로 멈추고 $x$를 출력할 수 있습니다.**
    *   **그렇지 않고, 만약 $f(x)=0$이라면, 우리는 절차를 다시 실행하거나(가능하다면 $t$에 대해 다른 선택을 사용하여), 포기하고 "해가 없음"을 출력하기로 결정할 수 있습니다.**

그로버 알고리즘이 어떻게 작동하는지 분석한 후, $t = O(\sqrt{N})$로 설정함으로써 우리는 높은 확률로 검색 문제에 대한 해를 얻는다는 것을 알게 될 것입니다 (만약 해가 존재한다면).

--- 

## 3. 분석

이제 Grover 알고리즘이 어떻게 작동하는지 이해하기 위해 이를 분석할 것입니다. 우리는 Grover 연산 **$G$**가 특정 상태에 어떻게 작용하는지를 계산하는 **기호적** 분석이라고 설명할 수 있는 것부터 시작한 다음, 이 기호적 분석을 알고리즘이 어떻게 작동하는지를 시각화하는 데 도움이 되는 **기하학적** 그림과 연결할 것입니다.

### 3.1 해와 비-해
두 문자열 집합을 정의하는 것부터 시작하겠습니다.

<p>$$
\begin{aligned}
  A_0 &= \bigl\{ x\in\Sigma^n : f(x) = 0\bigr\} \\
  A_1 &= \bigl\{ x\in\Sigma^n : f(x) = 1\bigr\}
\end{aligned}
$$
</p>

집합 **$A_1$**는 우리의 탐색 문제의 모든 해를 포함하는 반면, **$A_0$**는 해가 아닌 문자열(편의상 **비-해**라고 부를 수 있음)을 포함합니다. 이 두 집합은 **$A_0 \cap A_1 = \varnothing$**와 **$A_0 \cup A_1 = \Sigma^n$**를 만족하며, 이는 이것이 **$\Sigma^n$**의 **이분할**임을 의미합니다.

다음으로, 해 집합과 비-해 집합에 대한 균일 중첩을 나타내는 두 개의 단위 벡터를 정의할 것입니다.

<p>$$
\begin{aligned}
  \vert A_0\rangle &= \frac{1}{\sqrt{\vert A_0\vert}} \sum_{x\in A_0} \vert x\rangle \\
  \vert A_1\rangle &= \frac{1}{\sqrt{\vert A_1\vert}} \sum_{x\in A_1} \vert x\rangle
\end{aligned}
$$</p>

형식적으로 말하면, 이 벡터들 각각은 해당 집합이 비어 있지 않을 때만 정의되지만, 이후에는 **$A_0$**도 **$A_1$**도 모두 비어 있지 않은 경우에 초점을 맞출 것입니다. **$A_0 = \varnothing$**인 경우와 **$A_1 = \varnothing$**인 경우는 쉽게 별도로 다룰 수 있으며, 나중에 그렇게 할 것입니다.

덧붙여 말하자면, 여기서 사용되는 표기법은 일반적입니다. 유한하고 비어 있지 않은 집합 $S$가 있을 때마다, 우리는 그 집합의 요소들에 대해 균일한 양자 상태 벡터를 나타내기 위해 **$\|S\rangle$**를 쓸 수 있습니다.

또한 모든 $n$-비트 문자열에 대한 **균일한** 양자 상태를 **$\|u\rangle$**라고 정의합시다.

<p>$$
\vert u\rangle = \frac{1}{\sqrt{N}} \sum_{x\in\Sigma^n} \vert x\rangle.
$$</p>

다음에 주목하십시오.

$\|u\rangle = \sqrt{\frac{\|A_0 \vert}{N}} \|A_0\rangle + \sqrt{\frac{\|A_1 \vert}{N}} \|A_1\rangle$.

또한 **$\|u\rangle = H^{\otimes n} \|0^n \rangle$**를 가지므로, **$\|u\rangle$**는 Grover 알고리즘의 1단계 초기화 후 레지스터 $Q$의 상태를 나타냅니다.

이는 2단계에서 연산 **$G$**의 반복이 발생하기 직전에, $Q$의 상태가 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 의해 확장되는 2차원 벡터 공간에 포함되며, 더욱이 이 벡터들의 계수는 실수임을 의미합니다. 우리가 보게 되겠지만, 2단계에서 연산 **$G$**의 반복 횟수에 관계없이, $Q$의 상태는 항상 이러한 속성을 가질 것입니다 — 즉, 상태는 **$\|A_0\rangle$**와 **$\|A_1\rangle$**의 실수 선형 조합이 될 것입니다.

### 3.2 Grover 연산에 대한 관찰

이제 우리는 Grover 연산 **$G = H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n} Z_f$**으로 관심을 돌릴 것이며, 이에 대한 흥미로운 관찰로 시작할 것입니다.

잠시 동안 함수 **$f$**를 NOT 함수와의 합성으로 대체했다고 상상해 봅시다 — 다시 말해, **$f$**의 출력 비트를 뒤집어 얻는 함수입니다. 우리는 이 새로운 함수를 **$g$**라고 부르며, 몇 가지 대안적인 방식으로 기호를 사용하여 이를 표현할 수 있습니다.

<p>$$
g(x) = \neg f(x) = 1 \oplus f(x) = 1 - f(x) =
\begin{cases}
1 & f(x) = 0\\[1mm]
0 & f(x) = 1
\end{cases}
$$</p>

다음에 주목하십시오. \
모든 문자열 **$x \in \Sigma^n$**에 대해 **$(-1)^{g(x)} = (-1)^{1 \oplus f(x)} = - (-1)^{f(x)}$**이며, 
따라서 \
**$Z_g = - Z_f$**입니다.

이것은 우리가 함수 **$f$**를 함수 **$g$**로 대체한다고 해도 Grover 알고리즘이 다르게 작동하지 않는다는 것을 의미합니다 — 왜냐하면 두 경우에서 알고리즘으로부터 얻는 상태는 전역 위상(global phase)을 제외하고는 필연적으로 동일하기 때문입니다.

이것은 문제가 아닙니다! 직관적으로 말하면, 이 알고리즘은 어떤 문자열이 해이고 어떤 문자열이 비-해인지 신경 쓰지 않습니다 — 올바르게 작동하기 위해서는 해와 비-해를 **구별**할 수만 있으면 됩니다.

### 3.3 Grover 연산의 작용

이제 양자 상태 벡터 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 대한 **$G$**의 작용을 고려해 봅시다.

먼저, 연산 **$Z_f$**이 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 대해 매우 단순하게 작용한다는 것을 관찰해 봅시다.

<p>$$
\begin{aligned}
Z_f \vert A_0\rangle & = \vert A_0\rangle \\[1mm]
Z_f \vert A_1\rangle & = -\vert A_1\rangle
\end{aligned}
$$
</p>

둘째로, 우리는 연산 **$H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n}$**를 가지고 있습니다. 연산 **$Z_{\mathrm{OR}}$**는 모든 문자열 **$x \in \Sigma^n$**에 대해 다음과 같이 정의되며,

<p>$$
Z_{\mathrm{OR}} \vert x\rangle
= \begin{cases}
\vert x\rangle & x = 0^n \\[2mm]
-\vert x\rangle & x \neq 0^n,
\end{cases}
$$
</p>

이 연산을 표현하는 편리한 대안적인 방법은 다음과 같습니다.

<p>$$
Z_{\mathrm{OR}} = 2 \vert 0^n \rangle \langle 0^n \vert - \mathbb{I}.
$$
</p>

이 표현이 **$Z_{\mathrm{OR}}$**의 정의와 일치하는지 확인하는 간단한 방법은 표준 기저 상태에 대한 그 작용을 평가하는 것입니다.

연산 **$H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n}$**는 따라서 다음과 같이 작성될 수 있습니다.

<p>$$
H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n} = 2 H^{\otimes n} \vert 0^n \rangle \langle 0^n \vert H^{\otimes n} - \mathbb{I} = 2 \vert u \rangle \langle u \vert - \mathbb{I},
$$
</p>

이는 우리가 위에서 모든 $n$-비트 문자열에 대한 균일 중첩에 사용했던 것과 동일한 표기법인 **$\|u\rangle$**를 사용한 것입니다.

이제 우리는 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 대한 **$G$**의 작용을 계산하는 데 필요한 것을 갖추었습니다. 먼저 **$\|A_0\rangle$**에 대한 **$G$**의 작용을 계산해 봅시다.

<p>$$
\begin{aligned}
  G \vert A_0 \rangle
  & = \bigl( 2 \vert u\rangle \langle u \vert - \mathbb{I}\bigr) Z_f \vert A_0\rangle \\
  & = \bigl( 2 \vert u\rangle \langle u \vert - \mathbb{I}\bigr) \vert A_0\rangle \\
  & = 2 \sqrt{\frac{\vert A_0\vert}{N}} \vert u\rangle -\vert A_0 \rangle\\
  & = 2 \sqrt{\frac{\vert A_0\vert}{N}} \biggl(
  \sqrt{\frac{\vert A_0\vert}{N}} \vert A_0\rangle + \sqrt{\frac{\vert A_1\vert}{N}} \vert A_1\rangle\biggr)
  -\vert A_0 \rangle \\
  & = \biggl( \frac{2\vert A_0\vert}{N} - 1\biggr) \vert A_0 \rangle
  + \frac{2 \sqrt{\vert A_0\vert \cdot \vert A_1\vert}}{N} \vert A_1 \rangle \\
  & = \frac{\vert A_0\vert - \vert A_1\vert}{N} \vert A_0 \rangle
  + \frac{2 \sqrt{\vert A_0\vert \cdot \vert A_1\vert}}{N} \vert A_1 \rangle
\end{aligned}
$$
</p>

그리고 둘째로, **$\|A_1\rangle$**에 대한 **$G$**의 작용을 계산해 봅시다.

<p>$$
\begin{aligned}
  G \vert A_1 \rangle
  & = \bigl( 2 \vert u\rangle \langle u \vert - \mathbb{I} \bigr) Z_f \vert A_1\rangle \\
  & = - \bigl( 2 \vert u\rangle \langle u \vert - \mathbb{I} \bigr) \vert A_1\rangle \\
  & = - 2 \sqrt{\frac{\vert A_1\vert}{N}} \vert u\rangle + \vert A_1 \rangle \\
  & = - 2 \sqrt{\frac{\vert A_1\vert}{N}} \biggl(\sqrt{\frac{\vert A_0\vert}{N}} \vert A_0\rangle
      + \sqrt{\frac{\vert A_1\vert}{N}} \vert A_1\rangle\biggr) + \vert A_1 \rangle \\
  & = - \frac{2 \sqrt{\vert A_1\vert \cdot \vert A_0\vert}}{N} \vert A_0 \rangle
      + \biggl( 1 - \frac{2\vert A_1\vert}{N} \biggr) \vert A_1 \rangle \\
  & = - \frac{2 \sqrt{\vert A_1\vert \cdot \vert A_0\vert}}{N} \vert A_0 \rangle
      + \frac{\vert A_0\vert - \vert A_1\vert}{N} \vert A_1 \rangle
\end{aligned}
$$
</p>

두 경우 모두 우리는 다음과 같은 방정식 사용하고 있습니다. 
<p>
$$ \langle u | A_0\rangle = \sqrt{\frac{|A_0 \vert}{N}} \qquad\text{and}\qquad \langle u | A_1\rangle = \sqrt{\frac{|A_1 \vert}{N}} $$
</p>
함께 

<p>$$
\vert u\rangle
= \sqrt{\frac{\vert A_0 \vert}{N}} \vert A_0\rangle
+ \sqrt{\frac{\vert A_1 \vert}{N}} \vert A_1\rangle
$$
</p>

요약하자면, 우리는 다음을 얻습니다.
<p>$$
$$\begin{aligned}
  G \vert A_0 \rangle
  & = \frac{\vert A_0\vert - \vert A_1\vert}{N} \vert A_0 \rangle
  + \frac{2 \sqrt{\vert A_0\vert \cdot \vert A_1\vert}}{N} \vert A_1 \rangle\\[2mm]
  G \vert A_1 \rangle
  & = - \frac{2 \sqrt{\vert A_1\vert \cdot \vert A_0\vert}}{N} \vert A_0 \rangle
      + \frac{\vert A_0\vert - \vert A_1\vert}{N} \vert A_1 \rangle.
\end{aligned}
$$
</p>

이미 언급했듯이, 2단계 바로 직전의 레지스터 $Q$의 상태는 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 의해 확장되는 2차원 공간에 포함되며, 우리는 방금 **$G$**가 이 공간의 모든 벡터를 동일한 공간의 다른 벡터로 매핑한다는 것을 확인했습니다. 이는 분석을 위해 우리가 이 부분 공간에만 집중할 수 있음을 의미합니다.

이 2차원 공간 내에서 무슨 일이 일어나고 있는지 더 잘 이해하기 위해, 이 공간에서의 **$G$**의 작용을 행렬로 표현해 봅시다.

<p>$$
M = \begin{pmatrix}
  \frac{\vert A_0\vert - \vert A_1\vert}{N} & -\frac{2 \sqrt{\vert A_1\vert \cdot \vert A_0\vert}}{N} \\[2mm]
  \frac{2 \sqrt{\vert A_0\vert \cdot \vert A_1\vert}}{N} & \frac{\vert A_0\vert - \vert A_1\vert}{N}
\end{pmatrix},
$$
</p>

이 행렬 **$M$**은 첫 번째와 두 번째 행/열이 각각 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 해당합니다. 이 시리즈에서 지금까지 우리는 항상 행렬의 행과 열을 시스템의 고전적 상태와 연결했지만, 행렬은 여기서처럼 다른 기저에 대한 선형 매핑의 작용을 설명하는 데에도 사용될 수 있습니다.

언뜻 보기에는 전혀 명확하지 않지만, 행렬 **$M$**는 더 단순해 보이는 행렬을 **제곱**하여 얻는 것입니다.

<p>$$
\begin{pmatrix}
  \sqrt{\frac{\vert A_0\vert}{N}} & - \sqrt{\frac{\vert A_1\vert}{N}} \\[2mm]
  \sqrt{\frac{\vert A_1\vert}{N}} & \sqrt{\frac{\vert A_0\vert}{N}}
\end{pmatrix}^2
=
\begin{pmatrix}
  \frac{\vert A_0\vert - \vert A_1\vert}{N} & -\frac{2 \sqrt{\vert A_1\vert \cdot \vert A_0\vert}}{N} \\[2mm]
  \frac{2 \sqrt{\vert A_0\vert \cdot \vert A_1\vert}}{N} & \frac{\vert A_0\vert - \vert A_1\vert}{N}
\end{pmatrix} = M
$$
</p>

이 행렬은 

<p>$$
\begin{pmatrix}
  \sqrt{\frac{\vert A_0\vert}{N}} & - \sqrt{\frac{\vert A_1\vert}{N}} \\[2mm]
  \sqrt{\frac{\vert A_1\vert}{N}} & \sqrt{\frac{\vert A_0\vert}{N}}
\end{pmatrix}
$$
</p>

**회전 행렬**이며, 우리는 이것을 다음과 같이 대안적으로 표현할 수 있습니다.

**$\theta = \sin^{-1}\biggl(\sqrt{\frac{\|A_1\vert}{N}}\biggr)$**에 대해.

<p>$$
\begin{pmatrix}
  \sqrt{\frac{\vert A_0\vert}{N}} & - \sqrt{\frac{\vert A_1\vert}{N}} \\[2mm]
  \sqrt{\frac{\vert A_1\vert}{N}} & \sqrt{\frac{\vert A_0\vert}{N}}
\end{pmatrix}
=
\begin{pmatrix}
  \cos(\theta) & -\sin(\theta) \\[2mm]
  \sin(\theta) & \cos(\theta)
\end{pmatrix}
$$
</p>



이 각 **$\theta$**는 이어지는 분석에서 매우 중요한 역할을 할 것이므로, 우리가 이것을 처음 보았을 때 그 중요성을 강조할 가치가 있습니다.


이 행렬의 표현에 비추어 볼 때, 우리는 다음을 관찰합니다.

<p>$$M = \begin{pmatrix}
  \cos(\theta) & -\sin(\theta) \\[2mm]
  \sin(\theta) & \cos(\theta)
\end{pmatrix}^2
= \begin{pmatrix}
  \cos(2\theta) & -\sin(2\theta) \\[2mm]
  \sin(2\theta) & \cos(2\theta)
\end{pmatrix}.$$
</p>

이는 각 **$\theta$** 만큼 두 번 회전하는 것이 각 **$2\theta$** 만큼 회전하는 것과 동일하기 때문입니다. 이것을 보는 또 다른 방법은 대안적인 표현 **$\theta = \cos^{-1}\biggl(\sqrt{\frac{\|A_0\vert}{N}}\biggr)$**과 함께 삼각법의 **배각** 공식도 활용하는 것입니다.

<p>$$
\begin{aligned}
\cos(2\theta) & = \cos^2(\theta) - \sin^2(\theta)\\[1mm]
\sin(2\theta) & = 2 \sin(\theta)\cos(\theta).
\end{aligned}
$$
</p>

요약하자면, 2단계 시작 시 레지스터 $Q$의 상태는 

<p>$$
\vert u\rangle
= \sqrt{\frac{\vert A_0\vert}{N}} \vert A_0\rangle
+ \sqrt{\frac{\vert A_1\vert}{N}} \vert A_1\rangle
= \cos(\theta) \vert A_0\rangle + \sin(\theta) \vert A_1\rangle,
$$
</p>, 

이 상태에 **$G$**를 적용하는 효과는 **$\|A_0\rangle$**와 **$\|A_1\rangle$**에 의해 확장되는 공간 내에서 이를 **$2\theta$**만큼 회전시키는 것입니다. 예를 들어, 우리는 다음을 가집니다.

<p>$$
\begin{aligned}
G \vert u \rangle &= \cos(3\theta) \vert A_0\rangle + \sin(3\theta) \vert A_1\rangle\\[1mm]
G^2 \vert u \rangle &= \cos(5\theta) \vert A_0\rangle + \sin(5\theta) \vert A_1\rangle\\[1mm]
G^3 \vert u \rangle &= \cos(7\theta) \vert A_0\rangle + \sin(7\theta) \vert A_1\rangle
\end{aligned}
$$
</p>

그리고 일반적으로 

<p>$$
G^t \vert u \rangle
= \cos\bigl((2t + 1)\theta\bigr) \vert A_0\rangle
+ \sin\bigl((2t + 1)\theta\bigr) \vert A_1\rangle.
$$
</p>


### 3.4 기하학적 그림

이제 방금 거친 분석을 기하학적 그림과 연결해 봅시다. 핵심 아이디어는 연산 **$G$**가 두 개의 **반사**인 **$Z_f$**와 **$H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n}$**의 곱이라는 것입니다. 그리고 두 번의 반사를 수행하는 순 효과는 **회전**을 수행하는 것입니다.

**$Z_f$**부터 시작해 봅시다. 이전에 이미 관찰했듯이, 우리는 다음을 가집니다.

<p>$$
\begin{aligned}
Z_f \vert A_0\rangle & = \vert A_0\rangle \\[1mm]
Z_f \vert A_1\rangle & = -\vert A_1\rangle.
\end{aligned}
$$</p>

**$\|A_0\rangle$**와 **$\|A_1\rangle$**에 의해 확장되는 2차원 벡터 공간 내에서, 이것은 **$\|A_0\rangle$**에 평행한 선에 대한 **반사**이며, 우리는 이 선을 $L_1$라고 부를 것입니다. 여기에 우리가 **$\|A_0\rangle$**와 **$\|A_1\rangle$**의 실수 선형 조합이라고 가정하는 가상의 단위 벡터 **$\|\psi\rangle$**에 대한 이 반사의 작용을 보여주는 그림이 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/grover-algorithm/reflection1.svg" style="width:60%">

둘째로, 우리는 연산 **$H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n}$**를 가지고 있는데, 이는 이미 **$H^{\otimes n} Z_{\mathrm{OR}} H^{\otimes n} = 2 \|u \rangle \langle u \| - \mathbb{I}$**로 작성될 수 있음을 보았습니다.

이것 또한 반사이며, 이번에는 벡터 **$\|u\rangle$**에 평행한 선 $L_2$에 대한 반사입니다. 여기에 단위 벡터 **$\|\psi\rangle$**에 대한 이 반사의 작용을 묘사하는 그림이 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/fundamentals-of-quantum-algorithms/grover-algorithm/reflection2.svg" style="width:60%">

우리가 이 두 반사를 합성할 때, 이 그림이 보여주듯이, 우리는 반사선 사이의 각도의 두 배만큼의 회전을 얻습니다. 이것은 기하학적인 용어로, Grover 연산의 효과가 **$\|A_0\rangle$**와 **$\|A_1\rangle$**의 선형 조합을 **$2\theta$**의 각만큼 회전시키는 이유를 설명합니다.



## 4. 반복 횟수 선택

우리는 그로버 알고리즘에서 레지스터 Q의 상태 벡터가 초기화 단계가 수행된 후 $\vert A_0\rangle$와 $\vert A_1\rangle$에 의해 생성되는 2차원 부분 공간에 남아 있다는 것을 확인했습니다. 

**목표는 $A_1$에 속하는 원소 x를 찾는 것이며,** 우리가 상태 $\vert A_1\rangle$을 얻을 수 있다면 이 목표가 달성될 것입니다. 왜냐하면 이 상태를 측정하면 $A_1$
에 속하는 측정 결과 x를 얻을 것이 보장되기 때문입니다. 2단계에서 t번의 반복 후 Q의 상태가 다음과 같다는 점을 고려하면, 

<p>$$
G^t \vert u \rangle
= \cos\bigl((2t + 1)\theta\bigr) \vert A_0\rangle
+ \sin\bigl((2t + 1)\theta\bigr) \vert A_1\rangle,
$$
</p>

우리는 t를 다음과 같이 선택해야 합니다.

<p>$$
\langle A_1 \vert G^t \vert u \rangle =
\sin((2t + 1)\theta)
$$
</p>

측정으로부터 $A_1$
에 속하는 x를 얻을 확률을 최대화하기 위해 절댓값으로 1에 최대한 가깝도록 해야 합니다. 어떤 각도 $\theta \in (0, 2\pi)$에 대해서도 t가 증가함에 따라 $\sin((2t + 1)\theta)$ 값은 *진동*하지만, 반드시 주기적인 것은 아닙니다. 같은 값을 두 번 얻을 것이라는 보장은 없습니다.

당연히, 측정으로부터 $A_1$
에 속하는 원소 x를 얻을 확률을 높이는 것 외에도, t를 가능한 한 작게 선택하고 싶습니다. 왜냐하면 G 연산을 t번 적용하려면 함수 f에 t번의 쿼리가 필요하기 때문입니다. **우리는 $\sin((2t + 1)\theta)$를 절댓값으로 1에 가깝게 만들려고 하기 때문에,** 이를 위한 자연스러운 방법은 t를 다음과 같이 선택하는 것입니다.

<p>$$
(2t + 1) \theta \approx \frac{\pi}{2}.
$$
</p>

t에 대해 풀면 다음과 같습니다. 

<P>$$
t \approx \frac{\pi}{4\theta} - \frac{1}{2}.
$$</p>

물론, t는 정수여야 하므로 이 값에 정확히 도달할 수 없을 수도 있습니다. 그러나 우리가 할 수 있는 것은 이 값에 가장 가까운 정수를 취하는 것이며, 그것은 다음과 같습니다.

<p>$$
t = \Bigl\lfloor \frac{\pi}{4\theta} \Bigr\rfloor.
$$</p>

**이것이 그로버 알고리즘에 권장되는 반복 횟수입니다.** 분석을 진행하면서, 이 정수가 목표 값에 얼마나 가까운지가 알고리즘의 성능에 자연스럽게 영향을 미친다는 것을 알게 될 것입니다.

(덧붙여 말하자면, 목표 값 $\pi/(4\theta) - 1/2$가 두 정수의 정확히 중간에 있는 경우, t에 대한 이 표현식은 올림하여 얻는 값입니다. 대신 내림을 할 수도 있는데, 이는 쿼리 횟수가 하나 줄어든다는 의미이므로 합리적입니다. 하지만 이것은 이 학습의 목적상 부차적이며 중요하지 않습니다.) 

각도 $\theta$의 값이 다음 공식으로 주어진다는 것을 상기하면 

<p>$$
\theta = \sin^{-1}\biggl(\sqrt{\frac{\vert A_1\vert}{N}}\biggr),
$$</p>

권장 반복 횟수 t가 $A_1$ 에 있는 문자열의 수에 의존한다는 것을 알 수 있습니다. **이것은 우리가 얼마나 많은 해답을 가지고 있는지 알지 못할 경우 난제를 제시하며,** 이에 대해서는 나중에 논의할 것입니다.

### 4.1 고유 해답 검색

먼저, f(x) = 1인 단 하나의 문자열 x가 있는 상황에 초점을 맞추겠습니다. 이것을 다르게 표현하면, 우리는 *고유 해답 검색* 문제의 한 사례를 고려하고 있는 것입니다. 이 경우 우리는 다음을 가집니다.

<p>$$
\theta = \sin^{-1}\biggl( \sqrt{\frac{1}{N}} \biggr),
$$</p>

이는 N이 커질 때 다음과 같이 편리하게 근사될 수 있습니다. 

<p>$$
\theta = \sin^{-1}\biggl( \sqrt{\frac{1}{N}} \biggr) \approx \sqrt{\frac{1}{N}}
$$</p>

만약 우리가 $\theta = 1/\sqrt{N}$를 표현식에 

<p>$$
t = \Bigl\lfloor \frac{\pi}{4\theta} \Bigr\rfloor
$$</p>

대입하면, 다음을 얻습니다. 

<p>$$
t = \Bigl\lfloor \frac{\pi}{4}\sqrt{N} \Bigr\rfloor.
$$</p>

t는 G 연산이 수행되는 횟수일 뿐만 아니라 알고리즘에 의해 요구되는 함수 f에 대한 쿼리 횟수라는 것을 상기하면, **우리는 $O(\sqrt{N})$ 쿼리를 요구하는 알고리즘을 얻는 방향으로 나아가고 있다는 것을 알 수 있습니다.**

이제 권장되는 t의 선택이 얼마나 잘 작동하는지 조사할 것입니다. 최종 측정이 고유 해답을 가져올 확률은 다음과 같이 명시적으로 표현될 수 있습니다. 

<p>$$
p(N,1) = \sin^2 \bigl( (2t + 1) \theta \bigr).
$$</p>

첫 번째 인수인 N은 우리가 검색하는 항목의 수를 나타내고, 이 경우 1인 두 번째 인수는 해답의 수를 나타냅니다. 조금 뒤에 우리는 해답이 여러 개인 경우에 대해 동일한 표기법을 더 일반적으로 사용할 것입니다.

다음은 $N = 2^n$의 증가하는 값에 대한 성공 확률 표입니다.

<p>$$
\begin{array}{ll}
N & p(N,1)\\ \hline
2	& 0.5000000000\\
4	& 1.0000000000\\
8	& 0.9453125000\\
16	& 0.9613189697\\
32	& 0.9991823155\\
64	& 0.9965856808\\
128	& 0.9956198657\\
256	& 0.9999470421\\
512	& 0.9994480262\\
1024 &	 0.9994612447\\
2048 &	 0.9999968478\\
4096 &	 0.9999453461\\
8192 &	 0.9999157752\\
16384 &	 0.9999997811\\
32768 &	 0.9999868295\\
65536 &	 0.9999882596
\end{array}
$$</p>

이러한 확률이 엄격하게 증가하지 않는다는 점에 주목하십시오. 특히, **N = 4일 때 흥미로운 변칙이 있는데, 이때 우리는 해답을 확실하게 얻습니다.** 그러나 일반적으로 모든 N에 대해 $p(N, 1) \geq 1 - 1/N$이 성립함을 증명할 수 있으며, 따라서 위의 값이 시사하듯이 N이 커질 때 성공 확률은 1로 수렴합니다. 이것은 좋은 일입니다!

하지만 $p(N, 1) \geq 1/2$와 같은 약한 경계조차도 그로버 알고리즘의 유용성을 확립한다는 점에 주목하십시오. 이 절차를 실행하여 얻는 측정 결과 x가 무엇이든 간에, 우리는 f에 대한 단일 쿼리를 사용하여 f(x) = 1인지 항상 확인할 수 있습니다. 그리고 이 절차를 한 번 실행하여 f(x) = 1인 고유 문자열 x를 얻는 데 실패할 확률이 최대 $1/2$이라면, 이 절차를 m번 독립적으로 실행한 후에는 이 고유 문자열 x를 얻는 데 실패할 확률이 최대 $2^{-m}$이 됩니다. 즉, f에 대한 $O(m\sqrt{N})$ 쿼리를 사용하여, 우리는 고유 해답 x를 $1 - 2^{-m}$ 이상의 확률로 얻게 될 것입니다. 더 나은 경계인 $p(N, 1) \geq 1 - 1/N$을 사용하면 이 방법을 사용하여 $A_1$에 속하는 x를 찾을 확률이 실제로는 $1 - N^{-m}$ 이상임을 알 수 있습니다.

### 4.2 다중 해답

$A_1$의 원소 수가 달라짐에 따라 각도 $\theta$도 달라지며, 이는 알고리즘의 성공 확률에 상당한 영향을 미칠 수 있습니다. 간결함을 위해, 해답의 수를 나타내기 위해 $s = \|A_1\|$로 표기하고, 이전과 마찬가지로 $s \geq 1$이라고 가정하겠습니다. 

동기 부여를 위한 예시로서, 위에서 고려했던 단일 해답이 아닌 $s = 4$개의 해답이 있다고 가정해 봅시다. 이것은 다음을 의미합니다.

<p>$$
\theta = \sin^{-1}\biggl( \sqrt{\frac{4}{N}} \biggr),
$$</p>

이는 N이 클 때 $\|A_1\| = 1$ 사례에서 가졌던 각도의 약 두 배입니다. 우리가 더 나은 방법을 알지 못하고, 고유 해답 설정에서와 동일한 t 값을 선택했다고 가정해 봅시다. 

<p>$$
t = \Biggl\lfloor \frac{\pi}{4\sin^{-1}\bigl(1/\sqrt{N}\bigr)}\Biggr\rfloor.
$$</p>

다음 확률 표에서 알 수 있듯이 그 영향은 치명적일 것입니다.

<p>$$
\begin{array}{ll}
N & \text{Success probability}\\ \hline
4	& 1.0000000000\\
8	& 0.5000000000\\
16	& 0.2500000000\\
32	& 0.0122070313\\
64	& 0.0203807689\\
128	& 0.0144530758\\
256	& 0.0000705058\\
512	& 0.0019310741\\
1024	& 0.0023009083\\
2048	& 0.0000077506\\
4096	& 0.0002301502\\
8192	& 0.0003439882\\
16384	& 0.0000007053\\
32768	& 0.0000533810\\
65536	& 0.0000472907
\end{array}
$$</p>

이번에는 N이 무한대로 갈 때 성공 확률이 0으로 수렴합니다. 이는 고유 해답이 있었을 때보다 효과적으로 두 배 더 빠르게 회전하고 있기 때문에 발생하며, 따라서 우리는 목표 $\|\boldsymbol{A}_1\rangle$를 지나쳐 $-\|A_0\rangle$ 근처에 도달하게 됩니다. 

하지만 대신 권장되는 t의 선택을 사용한다면, 그것은 다음과 같습니다.
<p>
$$
t = \Bigl\lfloor \frac{\pi}{4\theta}\Bigr\rfloor
$$
</p>

<p>$$
\theta = \sin^{-1}\biggl( \sqrt{\frac{s}{N}} \biggr),
$$</p>

그러면 성능은 더 좋을 것입니다. 더 정확히 말하면, 이 t의 선택을 사용하면 높은 확률로 성공하게 됩니다.

<p>$$
\begin{array}{ll}
N & p(N,4)\\ \hline
4	 & 1.0000000000\\
8	 & 0.5000000000\\
16	 & 1.0000000000\\
32	 & 0.9453125000\\
64	 & 0.9613189697\\
128	 & 0.9991823155\\
256	 & 0.9965856808\\
512	 & 0.9956198657\\
1024	& 0.9999470421\\
2048	& 0.9994480262\\
4096	& 0.9994612447\\
8192	& 0.9999968478\\
16384	& 0.9999453461\\
32768	& 0.9999157752\\
65536	& 0.9999997811
\end{array}
$$</p>

이전에 주장된 것을 일반화하여, 다음이 증명될 수 있습니다. 

<p>$$
p(N,s) \geq 1 - \frac{s}{N},
$$</p>

여기서 우리는 이전에 제안된 표기법을 사용하고 있습니다. $p(N, s)$는 총 N개의 가능성 중 s개의 해답이 있을 때 그로버 알고리즘을 t번 반복하여 해답을 발견할 확률을 나타냅니다. 

성공 확률에 대한 이 $1 - s/N$ 하한은 해답이 더 많을수록 더 나쁜 하한을 의미한다는 점에서 약간 특이하지만, s가 N보다 상당히 작다는 가정 하에 우리는 그럼에도 불구하고 성공 확률이 상당히 높다고 결론 내립니다. 이전과 마찬가지로, $p(N, s)$가 상당히 크다는 사실만으로도 알고리즘의 유용성이 입증됩니다.

또한 다음이 성립합니다. 

<p>$$
p(N,s) \geq \frac{s}{N}.
$$</p>

이 하한은 균일하게 무작위로 선택된 문자열 $x \in \Sigma^n$이 해답일 확률을 나타냅니다. 따라서 그로버 알고리즘은 항상 무작위 추측만큼은 잘 작동합니다. (사실, $t = 0$일 때, 그로버 알고리즘은 *곧* 무작위 추측입니다.) 

이제 반복 횟수(따라서 쿼리 횟수)를 살펴보겠습니다.
<p>$$
t = \Bigl\lfloor \frac{\pi}{4\theta}\Bigr\rfloor,
$$</p>

for 
<p>$$
\theta = \sin^{-1}\biggl(\sqrt{\frac{s}{N}}\biggr).
$$</p>


모든 $\alpha \in [0,1]$에 대해 $\sin^{-1}(\alpha)\geq \alpha$가 성립하며, 따라서.

<p>$$
\theta = \sin^{-1}\left(\sqrt{\frac{s}{N}}\right) \geq \sqrt{\frac{s}{N}}.
$$</p>

이것은 다음을 의미한다.

<p>$$
t \leq \frac{\pi}{4\theta} \leq \frac{\pi}{4}\sqrt{\frac{N}{s}}.
$$</p>

**이는 s가 증가함에 따라 쿼리 횟수의 절약으로 이어집니다.** 특히, 필요한 쿼리 횟수는 다음과 같습니다.

<p>$$
O\biggl(\sqrt{\frac{N}{s}}\biggr).
$$</p>


### 4.3 알 수 없는 해답의 수

만약 해답의 수 $s = \|A_1\|$가 *알려지지 않은* 경우라면, 이 상황에서는 t의 선택에 필요한 s에 대한 지식이 없기 때문에 다른 접근 방식이 필요합니다. 사실 여러 가지 접근 방식이 있습니다. 

하나의 간단한 접근 방식은 다음 범위 내에서 

<p>$$
t \in \Bigl\{ 1,\ldots,\bigl\lfloor\pi\sqrt{N}/4\bigr\rfloor \Bigr\}
$$</p>

*균일하게 무작위로* 선택하는 것입니다. 

이러한 방식으로 t를 선택하면 항상 해답을 (존재한다고 가정할 때) 40% 이상의 확률로 찾을 수 있지만, 이는 명확하지 않으며 여기서는 포함하지 않을 분석이 필요합니다. 그러나 특히 기하학적 그림에 대해 생각할 때 이는 말이 됩니다. 이처럼 Q의 상태를 무작위 횟수만큼 회전시키는 것은 $\|A_0\rangle$와 $\|A_1\rangle$에 의해 생성되는 공간에서 무작위 단위 벡터를 선택하는 것과 다르지 않으며, 이 경우 $\|A_1\rangle$의 계수가 상당히 클 가능성이 있습니다. 이 절차를 반복하고 이전에 설명한 것과 동일한 방식으로 결과를 확인함으로써, 해답을 찾을 확률을 1에 매우 가깝게 만들 수 있습니다.

해답의 수 s가 알려지지 않은 경우에도 $O(\sqrt{N/s})$ 쿼리를 사용하여 해답이 존재할 때 해답을 찾는 개선된 방법이 있으며, $s = 0$일 때 해답이 없다는 것을 판단하기 위해 $O(\sqrt{N})$ 쿼리가 필요합니다. 

기본 아이디어는 T의 증가하는 값에 대해 집합 $\{1, \ldots, T\}$에서 t를 균일하게 무작위로 반복적으로 선택하는 것입니다. 특히, 우리는 $T = 1$에서 시작하여 T를 기하급수적으로 증가시킬 수 있으며, 해답이 발견되는 즉시 프로세스를 항상 종료하고, 해답이 없을 때 쿼리를 낭비하지 않도록 T에 상한을 둡니다. 이 프로세스는 더 많은 해답이 존재할 때 더 적은 쿼리가 필요하다는 사실을 활용합니다. 그러나 T의 증가 속도와 각 반복에 대한 성공 확률의 균형을 맞추기 위해서는 약간의 주의가 필요합니다. (예를 들어, 분석 결과 $T \leftarrow \lceil 5T/4 \rceil$로 설정하는 것은 작동합니다. 그러나 T를 두 배로 늘리는 것은 그렇지 않습니다. 이는 증가 속도가 너무 빠르다는 것이 밝혀졌습니다.)

### 4.4 자명한 사례

우리가 방금 거쳐온 분석 전반에 걸쳐, 우리는 해답의 수가 0이 아니라고 가정했습니다. 실제로, 다음 벡터들을 언급함으로써, 

<p>$$
\begin{aligned}
  \vert A_0\rangle &= \frac{1}{\sqrt{\vert A_0\vert}} \sum_{x\in A_0} \vert x\rangle \\
  \vert A_1\rangle &= \frac{1}{\sqrt{\vert A_1\vert}} \sum_{x\in A_1} \vert x\rangle
\end{aligned}
$$
</p>

우리는 $A_0$와 $A_1$가 둘 다 비어 있지 않다고 암묵적으로 가정했습니다.여기서 우리는 이 집합들 중 하나가 비어 있을 때 어떤 일이 발생하는지 간략하게 고려할 것입니다. 

분석에 신경 쓰기 전에, 명확한 것을 관찰해 봅시다. **만약 모든 문자열 $x \in \Sigma^n$이 해답이라면, 측정할 때 우리는 해답을 볼 것입니다. 그리고 해답이 전혀 없다면, 우리는 해답을 보지 못할 것입니다.** 어떤 의미에서는 이보다 더 깊이 들어갈 필요는 없습니다. 

하지만 우리는 이러한 자명한 사례들에 대한 수학적 사실을 신속하게 확인할 수 있습니다. $A_0$와 $A_1$ 중 하나가 비어 있는 상황은 f가 상수일 때 발생합니다. $A_1$은 모든 $x \in \Sigma^n$에 대해 $f(x) = 0$일 때 비어 있고, $A_0$는 모든 $x \in \Sigma^n$에 대해 $f(x) = 1$일 때 비어 있습니다. 이것은 다음을 의미합니다.

<p>$$
Z_f \vert u\rangle = \pm \vert u\rangle,
$$</p>

<p>$$
\begin{aligned}
  G \vert u \rangle
  & = \bigl( 2 \vert u\rangle \langle u \vert - \mathbb{I}\bigr) Z_f\vert u\rangle \\
  & = \pm \bigl( 2 \vert u\rangle \langle u \vert - \mathbb{I}\bigr) \vert u\rangle \\
  & = \pm \vert u\rangle.
\end{aligned}
$$</p>

따라서, 이러한 경우에 우리가 수행하는 반복 횟수 t와 상관없이, 측정은 항상 균일하게 무작위인 문자열 $x \in \Sigma^n$을 드러낼 것입니다.


## 5. 결론

쿼리 모델 내에서 그로버 알고리즘은 **점근적으로 최적**입니다.
이것이 의미하는 바는 *검색* 문제, 또는 특히 *유일 검색* 문제를 해결하기 위해 최악의 경우에 점근적으로 $O(\sqrt{N})$보다 적은 쿼리를 사용하는 쿼리 알고리즘을 만들어내는 것이 불가능하다는 것입니다.
이것은 여러 방식으로 엄밀하게 증명된 사실입니다.

흥미롭게도, 이는 그로버 알고리즘이 발견되기 전부터 알려져 있었습니다 — 그로버 알고리즘은 이미 알려진 하한(lower bound)과 일치했던 것입니다.

그로버 알고리즘은 또한 광범위하게 적용 가능한데, 이는 그것이 제공하는 제곱근 속도 향상을 다양한 상황에서 얻을 수 있다는 의미입니다.
예를 들어, 때로는 그로버 알고리즘을 다른 알고리즘과 함께 사용하여 개선을 얻는 것이 가능합니다.

그로버 알고리즘은 또한 다른 양자 알고리즘 내부에서 서브루틴으로 매우 흔하게 사용되어 속도 향상을 얻습니다.

마지막으로, 그로버 알고리즘에서 사용된 기법, 즉 두 개의 반사를 합성하고 반복하여 양자 상태 벡터를 회전시키는 방법은 일반화될 수 있습니다.
한 예로 **진폭 증폭(amplitude amplification)**이라는 기법이 있는데, 여기서 그로버 알고리즘과 유사한 과정을 다른 양자 알고리즘에 적용하여 고전적으로 가능한 것보다 이차적으로 더 빠르게 성공 확률을 높일 수 있습니다.
진폭 증폭은 양자 알고리즘에서 광범위한 응용을 가지고 있습니다.

따라서 그로버 알고리즘이 가까운 시일 내에 검색을 위한 실용적인 양자 우위로 이어지지는 않을 수 있지만, 이것은 근본적으로 중요한 양자 알고리즘이며, 양자 알고리즘에서 많은 응용을 찾는 보다 일반적인 기법을 대표합니다.

