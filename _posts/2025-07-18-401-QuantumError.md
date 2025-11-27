---
title: 8차시 1:Foundation Quantum Error Correction(Correcting Quantum Error)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# 반복 부호(Repetition codes)

우리는 *반복 부호(repetition codes)* 에 대한 논의로 이 수업을 시작할 것입니다. 반복 부호는 큐비트에서 발생할 수 있는 모든 유형의 오류로부터 양자 정보를 보호하지는 못하지만, 다음 수업에서 볼 9-큐비트 Shor 부호의 기초를 형성하며, 오류 수정의 기본 사항을 설명하는 데에도 유용합니다.

## 1. 고전적 인코딩 및 디코딩
반복 부호는 오류 수정 부호의 매우 기본적인 예시입니다. 이 아이디어는 각 비트를 단순히 고정된 횟수만큼 반복함으로써 오류로부터 비트를 보호할 수 있다는 것입니다.

특히, 고전적 정보의 맥락에서 시작하여 3-비트 반복 부호를 먼저 고려해 봅시다. 이 부호는 비트를 세 번 반복하여 하나의 비트를 세 개의 비트로 인코딩하므로, 0은 $000$으로 인코딩되고 1은 $111$로 인코딩됩니다.

<p>$$
\begin{aligned} 0 & \mapsto 000\\ 1 & \mapsto 111 \end{aligned}
$$</p>

아무것도 잘못되지 않는다면, 우리는 원래 비트의 두 가지 가능성을 그 인코딩으로부터 명확하게 구별할 수 있습니다. 요점은 오류가 발생하여 세 비트 중 하나가 뒤집혔을 때, 즉 0이 1로 바뀌거나 1이 0으로 바뀌었을 때에도, 두 이진 값 중 어떤 것이 두 번 나타나는지를 결정함으로써 원래 비트가 무엇이었는지 여전히 알아낼 수 있다는 것입니다. 이와 동등하게, 우리는 다수결 값(가장 자주 나타나는 이진 값)을 계산하여 *디코딩*할 수 있습니다.

<p>$$
a b c \mapsto \operatorname{majority}(a,b,c)
$$</p>

물론, 인코딩된 2개 또는 3개의 비트가 뒤집히면 디코딩이 제대로 작동하지 않고 잘못된 비트가 복구되지만, 3개의 비트 중 **최대 1개만 뒤집히면 디코딩은 정확할 것입니다**. 이는 일반적으로 오류 수정 부호의 전형적인 속성입니다: 오류 수정을 허용할 수 있지만, 오류가 너무 많지 않은 경우에만 가능합니다.

### 1.1 이진 대칭 채널(binary symmetric channel)을 위한 잡음 감소
반복 부호를 사용하여 오류 발생 가능성을 줄일 수 있는 상황의 예시로, 우리의 목표가 가상의 수신자에게 단일 비트를 전달하는 것이라고 가정하고, 우리는 소위 *이진 대칭 채널* 을 통해 비트를 전송할 수 있으며, 이 채널은 전송된 각 비트를 독립적으로 어떤 확률 $p$로 뒤집습니다. 즉, **확률 $1-p$로 수신자는 채널을 통해 전송된 비트와 동일한 비트를 받지만, 확률 $p$로 비트가 뒤집히고 수신자는 반대되는 비트 값을 받습니다**.

따라서, 우리가 3-비트 반복 부호를 사용하지 않기로 선택하고 단순히 우리가 생각하는 비트를 채널을 통해 보내면, 수신자는 확률 $p$로 잘못된 비트를 받게 됩니다. 반면에, 우리가 먼저 보내려는 비트를 3-비트 반복 부호를 사용하여 인코딩한 다음, 인코딩된 세 비트 각각을 채널을 통해 보내면, 이들 각각은 확률 $p$로 독립적으로 뒤집힙니다. 이제 뒤집힐 수 있는 비트가 하나가 아니라 세 개이기 때문에 비트-뒤집힘(bit-flip)의 가능성은 더 커지지만, **비트 중 최대 하나만 뒤집힌다면 수신자는 올바르게 디코딩할 것입니다**. 따라서 오류는 전송 중에 두 개 이상의 비트가 뒤집혀야만 디코딩 후에 지속됩니다.

전송 중에 두 비트가 뒤집힐 확률은 $3 p^2(1-p)$이며, 이는 뒤집히지 않는 비트에 대한 세 가지 선택 각각에 대해 $p^2(1-p)$입니다. 반면에 세 비트 모두 뒤집힐 확률은 $p^3$입니다. 따라서 두 개 또는 세 개의 비트-뒤집힘이 발생할 총 확률은 다음과 같습니다.

<p>$$
3 p^2 (1 - p) + p^3 = 3 p^2 - 2 p^3.
$$</p>

$p$ 값이 2분의 1보다 작은 경우, 이는 수신자가 잘못된 비트를 얻게 될 확률의 감소를 초래합니다. 이 경우에도 오류의 가능성은 여전히 존재하지만, 부호는 그 가능성을 *줄여줍니다*. (반면에, $p$ 값이 2분의 1보다 *큰* 경우에는, 이 부호는 실제로 수신자가 잘못된 비트를 얻을 가능성을 *증가시킵니다*).

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/binary-symmetric-error.svg" style="width:60%">

## 2. 큐비트 인코딩
3-비트 반복 부호는 고전적인 오류 수정 부호이지만, 우리가 이것을 *큐비트*를 오류로부터 보호하기 위해 사용하려고 시도하면 어떤 일이 발생하는지 고려해 볼 수 있습니다. 우리가 보게 되겠지만, 이는 실제로 일부 오류를 더 가능성 있게 만들기 때문에 그리 인상적인 양자 오류 수정 부호는 아닙니다. 그러나 이는 Shor 부호로 향하는 첫 번째 단계이며, 교육적인 관점에서 우리에게 잘 활용될 것입니다.

명확히 하자면, 3-비트 반복 부호가 큐비트에 사용된다고 언급할 때, 우리는 *표준 기저 상태*가 세 번 반복되도록 큐비트를 인코딩하는 것을 염두에 두고 있으며, 따라서 단일 큐비트 상태 벡터는 다음과 같이 인코딩됩니다.

<p>$$
\alpha \vert 0\rangle + \beta \vert 1\rangle \mapsto \alpha \vert 000\rangle + \beta \vert 111\rangle
$$</p>

이 인코딩은 두 개의 초기화된 작업 공간 큐비트와 두 개의 제어-NOT 게이트를 사용하는 다음 양자 회로로 쉽게 구현됩니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-encoder.svg" style="width:60%">

특히, 이 인코딩은 주어진 큐비트 상태 벡터가 $\vert\psi\rangle \mapsto \vert\psi\rangle\vert\psi\rangle\vert\psi\rangle$와 같이 양자 상태를 세 번 반복하는 것과 동일하지 않다는 점에 유의하십시오. 이러한 인코딩은 복제 금지 정리(no cloning theorem)에 의해 알 수 없는 양자 상태 $\vert\psi\rangle$에 대해 구현될 수 없습니다.

### 2.1 비트-뒤집힘 오류 (Bit-flip errors)
이제 인코딩이 수행된 후에 오류가 발생한다고 가정해 봅시다. 구체적으로, $X$ 게이트, 즉 비트-뒤집힘(bit-flip)이 큐비트 중 하나에서 발생한다고 가정해 봅시다. 예를 들어, 가운데 큐비트에서 비트-뒤집힘이 발생하면, 세 큐비트의 상태는 이 상태로 변환됩니다.

<p>$$
\alpha \vert 010\rangle + \beta \vert 101\rangle.
$$</p>

물론, 이것이 발생할 수 있는 유일한 종류의 오류는 아니며 — 오류가 완벽한 유니타리 연산의 형태를 취한다는 가정에 의문을 제기하는 것도 합리적입니다. 우리는 이 수업의 마지막 섹션에서 이러한 문제들로 돌아갈 것이며, 지금은 이러한 형태의 오류를 단지 하나의 가능한 유형의 오류로 간주할 수 있습니다 (비록 근본적으로 중요한 오류이기는 하지만).

우리는 위 상태에 대한 수학적 표현으로부터 각 켓(ket) 내부에서 가운데 비트가 다르다는 것을 명확하게 알 수 있습니다. 하지만 우리가 세 개의 큐비트를 가지고 있고 그 상태를 모른다고 가정해 봅시다. 비트-뒤집힘이 발생했을 수 있다고 의심한다면, 비트가 뒤집혔는지 확인하는 한 가지 선택지는 표준 기저 측정(standard basis measurement)을 수행하는 것인데, 이 경우 우리는 각각 확률 $\vert\alpha\vert^2$와 $\vert\beta\vert^2$로 $010$ 또는 $101$을 보게 될 것입니다. 두 경우 모두, 우리의 결론은 가운데 비트가 뒤집혔다는 것이겠지만 — 불행하게도, 우리는 원래의 양자 상태 $\alpha\vert 0\rangle + \beta \vert 1\rangle$를 잃게 될 것입니다. 이것이 우리가 보호하려고 하는 상태이므로, 표준 기저로 측정하는 것은 만족스럽지 않은 선택지입니다.

대신 우리가 할 수 있는 것은 다음 양자 회로를 사용하는 것인데, 인코딩된 상태를 상단 세 큐비트에 공급합니다. 이 회로는 3-큐비트 인코딩의 상위 두 큐비트와 하위 두 큐비트의 표준 기저 상태의 *패리티(parity)* 를 비파괴적으로 측정합니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-error-detection.svg" style="width:60%">

최대 하나의 비트만 뒤집혔다는 가정 하에, 측정 결과를 통해 비트-뒤집힘의 위치(또는 비트-뒤집힘의 부재)를 쉽게 추론할 수 있습니다. 특히, 다음 네 가지 회로 다이어그램이 보여주듯이, 측정 결과 $00$은 비트-뒤집힘이 발생하지 않았음을 나타내며, 다른 세 가지 가능성은 어떤 큐비트에서 비트-뒤집힘이 발생했는지를 나타냅니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-decode-no-error.svg" style="width:70%">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-decode-e0.svg" style="width:70%">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-decode-e1.svg" style="width:70%">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-decode-e2.svg" style="width:70%">

결정적으로, 어떤 경우에도 상위 세 큐비트의 상태는 붕괴되지 않으며, 이는 오류가 발생했을 경우 $X$ 게이트로 동일한 비트-뒤집힘을 다시 적용함으로써 비트-뒤집힘 오류를 수정할 수 있게 합니다. 다음 표는 최대 하나의 비트-뒤집힘으로부터 우리가 얻는 상태, 측정 결과 (오류 수정의 맥락에서 *증후군(syndrome)* 이라고 불림), 그리고 원래 인코딩으로 돌아가는 데 필요한 수정을 요약합니다.

| State | Syndrome | Correction |
| :--- | :--- | :--- |
| $\alpha\vert 000\rangle + \beta \vert 111\rangle$ | $00$ | $\mathbb{I}\otimes\mathbb{I}\otimes\mathbb{I}$ |
| $\alpha\vert 001\rangle + \beta \vert 110\rangle$ | $01$ | $\mathbb{I}\otimes\mathbb{I}\otimes X$ |
| $\alpha\vert 010\rangle + \beta \vert 101\rangle$ | $11$ | $\mathbb{I}\otimes X\otimes\mathbb{I}$ |
| $\alpha\vert 100\rangle + \beta \vert 011\rangle$ | $10$ | $X\otimes\mathbb{I}\otimes\mathbb{I}$ |

다시 한 번 말하지만, 우리는 최대 하나의 비트-뒤집힘이 발생했을 가능성만을 고려하고 있습니다. 두 개나 세 개의 비트-뒤집힘이 발생했다면 이것은 올바르게 작동하지 않을 것이며, 우리는 또한 비트-뒤집힘 외의 다른 가능한 오류들도 고려하지 않았습니다.

### 2.2 위상-뒤집힘 오류 (Phase-flip errors)
양자 환경에서는 비트-뒤집힘 오류만이 우리가 걱정해야 할 유일한 오류가 아닙니다. 예를 들어, 우리는 $Z$ 게이트로 설명되는 *위상-뒤집힘 오류(phase-flip errors)* 에 대해서도 걱정해야 합니다. 비트-뒤집힘 오류와 마찬가지로, 우리는 위상-뒤집힘 오류를 큐비트에 영향을 미칠 수 있는 또 다른 오류 가능성으로 생각할 수 있습니다.

그러나 양자 오류 수정 부호를 위한 소위 *오류의 이산화(discretization of errors)* 에 관한 이 수업의 마지막 섹션에서 보겠지만, 비트-뒤집힘 오류와 위상-뒤집힘 오류에 초점을 맞추는 것은 정당화되는 것으로 판명됩니다. 구체적으로, 비트-뒤집힘 오류, 위상-뒤집힘 오류, 또는 이 두 오류를 동시에 수정할 수 있는 능력은 단일 큐비트에 대한 임의의 양자 오류를 수정할 수 있는 능력을 자동으로 의미합니다.

불행하게도, 3-비트 반복 부호는 위상-뒤집힘으로부터 전혀 보호하지 못합니다. 예를 들어, 큐비트 상태 $\alpha\vert 0\rangle + \beta\vert 1\rangle$가 3-비트 반복 부호를 사용하여 인코딩되었고, 가운데 큐비트에서 위상-뒤집힘 오류가 발생했다고 가정해 봅시다. 이로 인해 다음 상태가 됩니다.

<p>$$
(\mathbb{I} \otimes Z \otimes \mathbb{I}) ( \alpha \vert 000\rangle + \beta \vert 111\rangle) = \alpha \vert 000\rangle - \beta \vert 111\rangle,
$$</p>

이것은 큐비트 상태 $\alpha\vert 0\rangle - \beta\vert 1\rangle$를 인코딩함으로써 얻었을 상태와 **정확히 같습니다**. 실제로, 인코딩의 세 큐비트 중 어느 하나에 위상-뒤집힘 오류가 발생하면 이와 동일한 효과를 가지며, 이는 인코딩 전에 원래 큐비트에서 위상-뒤집힘 오류가 발생한 것과 같습니다. 원래 양자 상태가 알 수 없는 상태라는 가정 하에, 결과 상태가 다른 큐비트 상태에 대한 완벽하게 유효한 인코딩이기 때문에 오류가 발생했음을 감지할 방법이 없습니다. 특히, 상태 $\alpha \vert 000\rangle - \beta \vert 111\rangle$에 대해 이전의 오류 감지 회로를 실행하면 증후군 $00$이 확실히 발생하는데, 이는 오류가 발생하지 않았음을 잘못 시사합니다.

한편, 이제 위상-뒤집힘 오류를 겪을 가능성이 있는 큐비트가 하나가 아니라 세 개가 있습니다. 따라서 위상-뒤집힘 오류가 (비트-뒤집힘이 아닌 위상-뒤집힘을 제외하고는 이진 대칭 채널과 유사하게) 0이 아닌 어떤 확률 $p$로 각 큐비트에서 독립적으로 발생한다고 가정되는 상황에서, 이 부호는 실제로 $p$의 작은 값에 대해 디코딩 후 위상-뒤집힘 오류의 가능성을 증가시킵니다. 더 정확히 말하면, 인코딩의 세 큐비트에서 홀수 개의 위상-뒤집힘 오류가 발생할 때마다 디코딩 후 원래 큐비트에서 위상-뒤집힘 오류를 얻게 되며, 이는 다음 확률로 발생합니다.

<p>$$
3 p (1 - p)^2 + p^3.
$$</p>

이 값은 $0<p<1/2$일 때 $p$보다 크므로, 이 부호는 이 범위의 $p$ 값에 대해 위상-뒤집힘 오류의 확률을 증가시킵니다.

### 2.3 위상-뒤집힘 오류를 위한 수정된 반복 부호
우리는 3-비트 반복 부호가 위상-뒤집힘 오류를 완전히 인지하지 못한다는 것을 관찰했으므로, 이러한 종류의 오류를 처리하는 데는 그리 도움이 되지 않는 것 같습니다. 그러나 우리는 3-비트 반복 부호를 간단한 방식으로 수정하여 위상-뒤집힘 오류를 감지하도록 할 수 있습니다. 이 수정은 부호를 비트-뒤집힘 오류에 대해서는 인지하지 못하게 만들겠지만 — 다음 섹션에서 보겠지만, 우리는 3-비트 반복 부호를 이 수정된 버전과 결합하여 비트-뒤집힘 및 위상-뒤집힘 오류를 모두 수정할 수 있는 Shor 부호를 얻을 수 있습니다.

다음은 위에서 언급된 인코딩 회로의 수정된 버전으로, 이제 위상-뒤집힘 오류를 감지할 수 있을 것입니다. 수정은 매우 간단합니다: 우리는 단순히 두 개의 제어-NOT 게이트를 수행한 후 각 큐비트에 Hadamard 게이트를 적용합니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/repetition-encoder-modified.svg" style="width:70%">

Hadamard 게이트는 $\vert 0\rangle$ 상태를 $\vert + \rangle$ 상태로 변환하고, $\vert 1\rangle$ 상태를 $\vert - \rangle$ 상태로 변환하므로, 최종 효과는 단일 큐비트 상태 $\alpha\vert 0\rangle + \beta \vert 1\rangle$가 다음과 같이 인코딩되는 것입니다.

<p>$$
\alpha \vert {+}\,{+}\,{+} \rangle + \beta \vert {-}\,{-}\,{-} \rangle
$$</p>

여기서 $\vert {+}\,{+}\,{+} \rangle = \vert + \rangle \otimes \vert + \rangle \otimes\vert + \rangle$이고 $\vert {-}\,{-}\,{-} \rangle = \vert - \rangle \otimes \vert - \rangle \otimes\vert - \rangle$입니다.

위상-뒤집힘 오류, 또는 이와 동등하게 $Z$ 게이트는 $\vert + \rangle$ 상태와 $\vert - \rangle$ 상태 사이를 뒤집으므로, 이 인코딩은 위상-뒤집힘 오류를 감지하고(그리고 수정하는 데) 유용할 것입니다. 구체적으로, 이전의 오류 감지 회로는 다음과 같이 수정될 수 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/three-bit-modified-error-detect.svg" style="width:70%">


말로 설명하자면, 우리는 이전의 회로를 가져와서 시작과 끝 모두에서 상위 세 큐비트에 Hadamard 게이트를 단순히 배치합니다. 아이디어는 첫 번째 세 개의 Hadamard 게이트가 $\vert + \rangle$ 및 $\vert - \rangle$ 상태를 $\vert 0\rangle$ 및 $\vert 1\rangle$ 상태로 다시 변환하고, 이전과 동일한 패리티 검사가 수행된 다음, 두 번째 레이어의 Hadamard 게이트가 상태를 $\vert + \rangle$ 및 $\vert - \rangle$ 상태로 다시 변환하여 인코딩을 복구할 수 있도록 하는 것입니다. 나중에 참고하기 위해, 이 위상-뒤집힘 감지 회로가 다음과 같이 단순화될 수 있음을 관찰해 봅시다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/simplified-phase-error-detect.svg" style="width:70%">

다음 네 가지 회로 다이어그램은 인코딩 단계와 오류 감지 단계를 포함한 3-비트 반복 부호의 수정된 버전이 최대 하나의 위상-뒤집힘 오류가 발생할 때 어떻게 작동하는지 설명합니다. 이 동작은 비트-뒤집힘에 대한 일반적인 3-비트 반복 부호와 유사합니다.


<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/phase-error-none.svg" style="width:70%">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/phase-error-0.svg" style="width:70%">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/phase-error-1.svg" style="width:70%">

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/phase-error-2.svg" style="width:70%">


다음은 위 표와 유사한 표이며, 이번에는 최대 하나의 위상-뒤집힘 오류 가능성을 고려합니다.

| State | Syndrome | Correction |
| :--- | :--- | :--- |
| $\alpha\vert {+}\,{+}\,{+} \rangle + \beta \vert {-}\,{-}\,{-}\rangle$ | $00$ | $\mathbb{I}\otimes\mathbb{I}\otimes\mathbb{I}$ |
| $\alpha\vert {+}\,{+}\,{-}\rangle + \beta \vert {-}\,{-}\,{+}\rangle$ | $01$ | $\mathbb{I}\otimes\mathbb{I}\otimes Z$ |
| $\alpha\vert {+}\,{-}\,{+}\rangle + \beta \vert {-}\,{+}\,{-}\rangle$ | $11$ | $\mathbb{I}\otimes Z\otimes\mathbb{I}$ |
| $\alpha\vert {-}\,{+}\,{+} \rangle + \beta \vert {+}\,{-}\,{-}\rangle$ | $10$ | $Z\otimes\mathbb{I}\otimes\mathbb{I}$ |

불행하게도, 이 수정된 버전의 3-비트 반복 부호는 이제 더 이상 비트-뒤집힘 오류를 수정할 수 없습니다. 하지만 모든 것을 잃은 것은 아닙니다. 이전에 제시되었듯이, 우리는 방금 본 두 가지 부호를 결합하여 비트-뒤집힘 및 위상-뒤집힘 오류, 그리고 실제로 단일 큐비트의 임의의 오류를 수정할 수 있는 하나의 부호, 즉 9-큐비트 Shor 부호를 얻을 수 있을 것입니다.

--- 

# **9-큐비트 쇼어 코드**

이제 우리는 9-큐비트 쇼어 코드로 넘어갑니다. 이것은 이전 섹션에서 고려된 두 코드를 결합하여 얻은 양자 오류 정정 코드입니다. 이 두 코드는 단일 비트-플립 오류를 수정할 수 있는 큐비트용 3비트 반복 코드와, 단일 위상-플립 오류를 수정할 수 있는 해당 코드의 수정된 버전입니다.

## 1. 코드 설명
9-큐비트 쇼어 코드는 이전 섹션의 두 코드를 ***연결(concatenating)***하여 얻는 코드입니다. 이는 우리가 먼저 하나의 인코딩을 적용하여 하나의 큐비트를 세 개로 인코딩한 다음, 첫 번째 인코딩에 사용된 세 개의 큐비트 ***각각***에 다른 인코딩을 적용하여, 총 아홉 개의 큐비트를 생성함을 의미합니다.

더 정확히 말하면, 이 특정 경우에서는 두 코드를 어떤 순서로든 적용할 수 있지만, 우리는 먼저 (위상-플립 오류를 감지하는) 수정된 버전의 3비트 반복 코드를 적용하고, 그런 다음 (비트-플립 오류를 감지하는) 원래의 3비트 반복 코드를 사용하여 결과로 나온 세 개의 큐비트 ***각각***을 독립적으로 인코딩하는 선택을 할 것입니다. 여기에 이 인코딩의 회로 다이어그램 표현이 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-encoder.svg" style="width:70%">

그림이 나타내듯이, 우리는 쇼어 코드의 아홉 큐비트를 세 큐비트씩 세 블록으로 그룹화되어 있는 것으로 생각할 것입니다. 여기서 각 블록은 ***두 번째*** 인코딩 단계(일반적인 3비트 반복 코드)로부터 얻어집니다. 여기서 독립적으로 세 번 적용되는 일반적인 3비트 반복 코드는 이 맥락에서 ***내부 코드(inner code)***라고 불리며, ***외부 코드(outer code)***는 첫 번째 인코딩 단계에 사용된 코드, 즉 위상-플립 오류를 감지하는 수정된 버전의 3비트 반복 코드입니다.

우리는 원래 큐비트에 대한 두 표준 기저 상태가 어떻게 인코딩되는지 설명함으로써 코드를 다르게 지정할 수 있습니다.

<p>$$\begin{aligned} \vert 0\rangle & \:\mapsto\: \frac{1}{2\sqrt{2}} (\vert 000\rangle + \vert 111\rangle) \otimes (\vert 000\rangle + \vert 111\rangle) \otimes (\vert 000\rangle + \vert 111\rangle) \\[4mm] \vert 1\rangle & \:\mapsto\: \frac{1}{2\sqrt{2}} (\vert 000\rangle - \vert 111\rangle) \otimes (\vert 000\rangle - \vert 111\rangle) \otimes (\vert 000\rangle - \vert 111\rangle) \end{aligned}$$</p>

이것을 알면, 선형성을 통해 임의의 큐비트 상태 벡터가 어떻게 인코딩되는지 결정할 수 있습니다.

## 2. **비트-플립 및 위상-플립 오류 수정**
### 2.1 **오류와 CNOT 게이트**
$X$ 오류와 $Z$ 오류가 9-큐비트 쇼어 코드 및 다른 코드에서 큐비트 인코딩에 어떻게 영향을 미치는지 분석하기 위해, 이 오류들과 CNOT 게이트 사이의 몇 가지 간단한 관계를 관찰하는 것이 도움이 될 것입니다. 9-큐비트 쇼어 코드를 분석하기 시작할 때, 이것을 위해 잠시 멈추는 것은 합리적인 순간입니다.

다음 회로 다이어그램들은 $X$ 게이트와 CNOT 게이트 사이의 세 가지 기본적인 관계를 보여줍니다. 구체적으로, CNOT 이전에 ***타겟*** 큐비트에 $X$ 게이트를 적용하는 것은 순서를 바꾸고 CNOT를 먼저 수행하는 것과 동등하지만, CNOT 이전에 ***제어*** 큐비트에 $X$ 게이트를 적용하는 것은 CNOT 후에 두 큐비트 모두에 $X$ 게이트를 적용하는 것과 동등합니다. 마지막으로, CNOT 이전에 두 큐비트 모두에 $X$ 게이트를 적용하는 것은 CNOT를 먼저 적용한 다음 제어 큐비트에 $X$ 게이트를 적용하는 것과 동등합니다. 이러한 관계는 필요한 행렬 곱셈을 수행하거나 표준 기저 상태에 대한 회로의 영향을 계산함으로써 확인할 수 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/X-and-CNOT.svg" style="width:60%">

$Z$ 게이트에 대해서도 상황은 비슷하지만, 제어 큐비트와 타겟 큐비트의 역할이 바뀝니다. 특히, 우리는 다음 양자 회로로 묘사된 세 가지 관계를 가집니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Z-and-CNOT.svg" style="width:60%">

### 2.2 **비트-플립 오류 수정**

이제 우리는 9-큐비트 쇼어 코드를 사용하여 오류를 어떻게 감지하고 수정할 수 있는지 고려할 것입니다. 비트-플립 오류부터 시작하며 — 간결함을 위해 이후에는 일반적으로 $X$ 오류라고 부를 것입니다.

$X$ 오류를 감지하고 수정하기 위해, 우리는 단순히 인코딩의 세 블록 각각을 별도로 다룰 수 있습니다. 각 블록은 $X$ 오류로부터 보호하는 3비트 반복 코드를 사용하여 큐비트를 인코딩한 것입니다 — 따라서 이전에 설명된 증후군 측정 및 $X$ 오류 수정을 각 블록에 수행함으로써, 우리는 블록당 최대 하나의 $X$ 오류를 감지하고 수정할 수 있습니다. 특히, 인코딩의 아홉 큐비트에 최대 하나의 $X$ 오류가 있는 경우, 이 오류는 이 절차에 의해 감지되고 수정될 것입니다.

요컨대, 내부 코드가 비트-플립 오류를 수정한다는 사실 때문에, 비트-플립 오류를 수정하는 것은 이 코드에서 간단한 문제입니다.

### 2.3 **위상-플립 오류 수정**
다음으로 우리는 위상-플립 오류, 또는 간결함을 위해 $Z$ 오류를 고려할 것입니다. 이번에는 우리가 무엇을 해야 할지가 그렇게 명확하지 않습니다. 왜냐하면 외부 코드가 $Z$ 오류를 감지하는 코드이지만, 내부 코드가 어떤 식으로든 "방해하고" 있는 것처럼 보이기 때문에, 이러한 오류의 감지 및 수정이 약간 더 어려워지기 때문입니다.

이 다이어그램에 표시된 것과 같이, 쇼어 코드의 9개 큐비트 중 하나에 $Z$ 오류가 발생한다고 가정해 봅시다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-phase-error-1.svg" style="width:60%">

우리는 3비트 반복 코드를 사용할 때 $Z$ 오류가 발생하면 어떤 일이 일어나는지 이미 관찰했습니다 — 이는 인코딩 이전에 $Z$ 오류가 발생하는 것과 동등합니다. 9-큐비트 쇼어 코드의 맥락에서, 이는 블록 내의 세 큐비트 중 어느 하나에서 $Z$ 오류가 발생하면 항상 동일한 효과를 가지며, 이는 내부 코드가 적용되기 전에 해당 큐비트에서 $Z$ 오류가 발생하는 것과 동등하다는 것을 의미합니다.

예를 들어, 위에 있는 회로 다이어그램은 다음 다이어그램과 동등합니다. 이는 위에서 설명한 $Z$ 게이트와 CNOT 게이트 사이의 관계를 사용하여 추론하거나, 단순히 임의의 큐비트 상태 $\|\psi\rangle$에 대해 회로를 평가함으로써 추론할 수 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-phase-error-2.svg" style="width:60%">

이는 $Z$ 오류를 감지하고 수정하기 위한 한 가지 옵션을 제안합니다. 그것은 내부 코드를 ***디코딩(decode)***하는 것입니다. 이렇게 하면 외부 인코딩에 사용된 세 개의 큐비트와 여섯 개의 초기화된 작업 공간 큐비트가 남습니다. 그런 다음 우리는 $Z$ 오류에 대해 외부 코드의 이 세 큐비트를 확인하고, 마지막으로 내부 코드를 사용하여 다시 인코딩하여, 쇼어 코드에서 얻는 9-큐비트 인코딩으로 돌아갈 수 있습니다. 만약 우리가 $Z$ 오류를 감지한다면, 내부 코드로 다시 인코딩하기 전에 그것을 수정하거나, 해당 블록의 큐비트 중 하나에 $Z$ 게이트를 적용하여 다시 인코딩한 후에 수정할 수 있습니다.

여기에 인코딩 회로와 위에서 제안된 오류가 방금 설명된 단계들(실제 수정 단계는 제외)과 함께 포함된 회로 다이어그램이 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-phase-error-detect-1.svg" style="width:60%">

이 특정 예에서, 증후군 측정은 $11, 11, 11$이며, 이는 $Z$ 오류가 가운데 블록의 큐비트 중 하나에서 발생했음을 찾아냅니다.

다시 인코딩하는 단계 이전에 $Z$ 오류를 수정하는 것보다 이후에 수정하는 것의 한 가지 장점은 위에 있는 회로를 단순화할 수 있다는 것입니다. 다음 회로는 동등하지만, 네 개의 CNOT 게이트가 덜 필요합니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-phase-error-detect-2.svg" style="width:60%">

다시 한 번, 증후군은 $Z$ 오류에 의해 어떤 큐비트가 영향을 받았는지 나타내는 것이 아니라, 어떤 블록이 $Z$ 오류를 겪었는지를 나타내며, 블록 내의 어떤 큐비트가 영향을 받았는지와 관계없이 그 효과는 동일합니다. 그런 다음 우리는 영향을 받은 블록의 세 큐비트 중 어느 하나에 $Z$ 게이트를 적용하여 오류를 수정할 수 있습니다.

여담으로, 여기서 우리는 양자 오류 정정 코드의 ***축퇴(degeneracy)***의 한 예를 봅니다. 이 경우, 우리는 특정 오류($Z$ 오류)를 고유하게 식별할 수 없더라도 수정할 수 있습니다.

### 2.4 **동시 비트- 및 위상-플립 오류**

이제 우리는 9-큐비트 쇼어 코드를 사용하여 $X$ 오류와 $Z$ 오류 모두를 어떻게 감지하고 수정할 수 있는지, 특히 최대 하나의 $X$ 오류 또는 최대 하나의 $Z$ 오류를 어떻게 감지하고 수정할 수 있는지 살펴보았습니다. 이제 비트-플립 오류와 위상-플립 오류가 모두 발생한다고 가정해 봅시다. 이는 아마도 같은 큐비트에서 발생할 수 있습니다. 밝혀진 바에 따르면, 이 상황에서는 이미 논의된 내용과 다른 조치를 취할 필요가 없습니다 — **이 코드는 추가 수정 없이 최대 하나의 $X$ 오류와 하나의 $Z$ 오류를 동시에 감지하고 수정할 수 있습니다**.

더 구체적으로 말하면, $X$ 오류는 일반적인 3비트 반복 코드 증후군 측정을 적용하여 감지되며, 이는 세 큐비트로 구성된 세 블록 각각에 대해 별도로 수행됩니다. 그리고 $Z$ 오류는 방금 위에서 설명된 절차를 통해 감지되는데, 이는 내부 코드를 디코딩하고, 위상-플립을 위한 수정된 3비트 반복 코드에 대한 증후군 측정을 수행한 다음, 다시 인코딩하는 것과 동등합니다. 이 두 가지 오류 감지 단계 — 그리고 해당 수정 단계 — 는 서로 완전히 독립적으로 수행될 수 있으며, 사실 어떤 순서로 수행되는지는 중요하지 않습니다.

이것이 왜 그런지 알아보기 위해, 다음 회로 다이어그램에 묘사된 예를 고려해 봅시다. 여기서 $X$ 오류와 $Z$ 오류 모두가 중간 블록의 맨 아래 큐비트에 영향을 미쳤습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-XZ-error.svg" style="width:60%">

먼저 오류의 순서는 중요하지 않다는 것을 관찰해 봅시다. 즉, $X$ 오류와 $Z$ 오류의 위치를 바꾸는 것이 동등한 회로를 생성한다는 의미에서 그렇습니다. 명확히 하자면, $X$와 $Z$는 교환하지 않고, ***반교환(anti-commute)***합니다.

<p>$$XZ = \begin{pmatrix} 0 & 1\\[1mm] 1 & 0 \end{pmatrix} \begin{pmatrix} 1 & 0\\[1mm] 0 & -1 \end{pmatrix} = \begin{pmatrix} 0 & -1\\[1mm] 1 & 0 \end{pmatrix} = - \begin{pmatrix} 1 & 0\\[1mm] 0 & -1 \end{pmatrix} \begin{pmatrix} 0 & 1\\[1mm] 1 & 0 \end{pmatrix} = -ZX.$$</p>

이는 다음 회로가 전역 위상 인자 $-1$을 제외하고는 바로 위의 회로와 동등함을 의미합니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-ZX-error.svg" style="width:60%">

이제 우리는 이전과 마찬가지로 $Z$ 오류를 이동시켜 또 다른 동등한 회로를 얻을 수 있습니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/Shor-ZX-error-2.svg" style="width:60%">

이 시점에서, $X$ 오류를 감지하고 수정하는 절차가 먼저 수행되면, $X$ 오류가 수정될 것이며, 그 후에 $Z$ 오류를 감지하고 수정하는 절차가 수행되어 이전처럼 $Z$ 오류를 제거할 수 있다는 것이 분명합니다.

대안적으로, $Z$ 오류를 감지하고 수정하는 절차를 먼저 수행할 수 있습니다. 이 절차가 하나 이상의 $X$ 오류가 있는 경우에도 예상대로 작동한다는 사실은, 인코딩에 사용된 아홉 큐비트 중 어느 하나에 대한 $X$ 게이트가 $Z$ 오류에 대한 증후군 측정을 위한 우리의 단순화된 회로에 있는 모든 게이트와 교환한다는 사실에서 비롯됩니다. 따라서, 이 증후군 측정은 $Z$ 오류에 의해 어떤 블록이 영향을 받았는지 여전히 정확하게 식별할 것입니다. $X$ 오류가 발생했더라도, $Z$ 오류가 발생한 블록에 $Z$ 게이트를 적용하여 해당 블록의 어떤 큐비트에 대해서든 $Z$ 오류가 수정된다는 사실은, 전역 위상까지 동등한 회로를 제공하는 $X$ 및 $Z$ 게이트의 순서에 관한 위와 동일한 논증에서 비롯됩니다.

따라서, 9-큐비트 쇼어 코드는 이 코드에 사용된 아홉 큐비트 중 어느 하나에 대해 **$X$ 오류, $Z$ 오류, 또는 둘 다를 수정할 수 있습니다**. 사실, 우리는 그 이상의 오류도 수정할 수 있습니다. 여기에는 다중 $X$ 오류(서로 다른 블록에 속하는 한) 또는 다중 $Z$ 오류(최대 하나의 블록이 홀수 개의 $Z$ 오류를 겪는 한)가 포함됩니다 — 그러나 앞으로 이 레슨의 목적을 위해 가장 관련성이 높을 내용은, 우리가 단일 큐비트에서 $X$ 오류, $Z$ 오류, 또는 둘 다를 수정할 수 있다는 것입니다.

## 3. **무작위 오류에 대한 오류 감소**
임의의 양자 오류에 관한 레슨의 마지막 섹션으로 넘어가기 전에, 큐비트에 파울리 행렬로 표현되는 오류가 ***무작위로*** 발생할 때 9-큐비트 쇼어 코드의 성능을 간략하게 고려해 봅시다.

더 구체적으로, 우리는 고전 비트의 이진 대칭 채널과 유사하게, 오류가 큐비트에 ***독립적으로*** 발생하고, 각 큐비트가 확률 $p$로 오류를 겪으며, 서로 다른 큐비트의 오류 사이에 상관관계가 없는 단순한 노이즈 모델을 고려할 것입니다. 우리는 $X, Y, Z$ 오류가 발생할 확률에 서로 다른 값을 할당할 수 있지만, 가능한 한 간단하게 유지하기 위해, 우리는 9-큐비트 쇼어 코드에 대한 최악의 시나리오를 고려할 것입니다. 이는 영향을 받은 각 큐비트에 $Y$ 오류가 발생하는 것입니다. 그런데 $Y$ 오류는 $Y = iXZ$라는 점을 고려할 때, (무관한 전역 위상 인자를 제외하고) 동일한 큐비트에 $X$ 오류와 $Z$ 오류가 모두 발생하는 것과 동등합니다. 이것이 우리가 지금까지 $Y$ 오류를 명백히 무시했던 이유를 설명합니다.

이제, $\mathsf{Q}$가 오류로부터 보호하고 싶은 특정 상태의 큐비트라고 가정하고, 우리는 9-큐비트 쇼어 코드를 사용할 옵션을 고려할 수 있습니다. 자연스럽게 물어볼 질문은 "우리가 이것을 사용해야 하는가?"입니다.
대답은 반드시 "예"는 아닙니다. 노이즈가 너무 많다면, 즉 이 맥락에서 $p$가 너무 크다면, 쇼어 코드를 사용하는 것이 실제로 상황을 악화시킬 수 있습니다 — 마치 3비트 반복 코드가 $p$가 2분의 1보다 클 때 코드를 사용하지 않는 것보다 더 나쁜 것처럼 말입니다. 하지만, $p$가 충분히 작다면, 대답은 "예"이며, 우리는 이 코드를 사용해야 합니다. 왜냐하면 이 코드는 인코딩된 상태가 손상될 가능성을 감소시킬 것이기 때문입니다. 이것이 왜 그런지, 그리고 $p$가 이 코드에 대해 너무 크거나 충분히 작다는 것이 무엇을 의미하는지 알아봅시다.

쇼어 코드는 단일 큐비트에 대한 모든 파울리 오류를 수정하며, 물론 $Y$ 오류도 포함합니다. 하지만 두 개 이상의 $Y$ 오류는 적절하게 수정하지 못합니다. 명확히 하자면, 우리는 이 섹션의 앞부분에서 설명된 $X$ 및 $Z$ 오류 수정을 사용하고 있다고 가정하고 있습니다. (물론, 우리가 $Y$ 오류에 대해서만 걱정하면 된다는 것을 미리 알았다면, 당연히 수정을 다르게 선택했을 것입니다 — 하지만 그것은 노이즈 모델에 대해 기만하는 것이며, 두 개 이상의 큐비트가 오류의 영향을 받을 때마다 이 새로운 수정 선택이 실패하도록 다른 파울리 오류를 선택하여 모델을 항상 변경할 수 있을 것입니다).

따라서, 이 코드는 아홉 큐비트 중 최대 하나만 오류의 영향을 받는 한 $\mathsf{Q}$를 보호하며, 이는 다음 확률로 발생합니다.
<p> $$(1-p)^9 + 9 p (1-p)^8.$$</p>
그렇지 않으면, 다음 확률로,
<p>$$1 - (1-p)^9 - 9 p (1-p)^8,$$</p>

코드는 $\mathsf{Q}$를 보호하는 데 실패합니다.

구체적으로, 이 맥락에서 그것이 의미하는 바는, 전역 위상을 제외하고, 비-항등 파울리 연산이 우리의 큐비트 $\mathsf{Q}$에 (논리 큐비트로서) 적용될 것이라는 것입니다. 즉, 레슨의 앞부분에서 설명된 대로 쇼어 코드에 대해 $X$ 및 $Z$ 오류가 감지되고 수정되면, 우리는 전역 위상을 제외하고, $\mathsf{Q}$의 원래 상태에 적용된 비-항등 파울리 연산의 인코딩과 동등한 상태의 인코딩을 갖게 될 것입니다. 이것을 더 간결하게 말하는 방법은 ***논리적*** 오류가 발생했다는 것입니다. 이것은 $\mathsf{Q}$의 원래 상태에 영향을 미치거나 미치지 않을 수도 있습니다 — 다시 말해, 우리가 아홉 개의 ***물리적 큐비트***로 인코딩한 ***논리적 큐비트***에 영향을 미치거나 미치지 않을 수도 있습니다 — 하지만, 이 분석을 위해 우리는 이 사건을 실패로 간주하고 있습니다.

반면에, 우리가 코드를 사용하는 수고를 하지 않는다면, 우리의 유일한 큐비트는 확률 $p$로 비슷한 운명(비-항등 파울리 연산의 대상이 되는 것)을 겪을 것입니다. 코드는 첫 번째 확률이 두 번째 확률보다 작을 때 도움이 됩니다.

<p>$$1 - (1-p)^9 - 9 p (1-p)^8 < p.$$</p>

다음은 $p$의 매우 작은 값에 대해 코드가 이점을 제공함을 보여주는 그래프이며, 손익분기점(break-even point)은 약 $0.0323$에서 발생합니다.

<img src="https://quantum.cloud.ibm.com/learning/images/courses/foundations-of-quantum-error-correction/correcting-quantum-errors/breakeven-Shor.svg" style="width:60%">

만약 $p$가 이 손익분기점보다 작다면, 코드가 도움이 됩니다. 손익분기점에서는 확률이 동일하므로, 코드를 사용한다면 8개의 큐비트와 함께 우리의 시간을 낭비하는 것일 뿐입니다. 그리고 손익분기점을 넘어서는, 이 코드가 $\mathsf{Q}$에 대한 논리적 오류의 가능성을 ***증가시키고*** 있기 때문에, 우리는 이 코드를 절대 사용해서는 안 됩니다.

3.25% 정도는 고전 정보에 대한 3비트 반복 코드의 유사한 손익분기점인 $50\%$와 비교했을 때, 그다지 좋은 손익분기점으로 보이지 않을 수 있습니다. 이러한 차이는, 주로 양자 정보가 고전 정보보다 더 섬세하고 보호하기 어렵다는 사실 때문입니다. 또한 — 9-큐비트 쇼어 코드가 세계 최초의 양자 오류 정정 코드로서 뛰어난 발견을 나타낸다는 점을 인정하지만 — 실제적인 측면에서는 실제로 아주 좋은 코드가 아니라는 점을 인정해야 합니다.

---

# 오류의 이산화 (Discretization of errors)

지금까지 우리는 9-큐비트 쇼어 코드(Shor code)의 맥락에서 $X$ 오류와 $Z$ 오류를 고려했으며, 이 섹션에서는 임의의 오류를 고려할 것입니다. 우리가 발견할 것은, 그러한 오류를 처리하기 위해 우리가 이미 논의한 것과 다른 어떤 것도 할 필요가 없다는 것입니다. $X$ 오류, $Z$ 오류, 또는 둘 다를 수정할 수 있는 능력은 임의의 오류를 수정할 수 있는 능력을 의미합니다. 이러한 현상을 때때로 **오류의 이산화***라고 부릅니다.

## 1. 유니타리 큐비트 오류 (Unitary qubit errors)

단일 큐비트 **유니타리*** 오류부터 시작하겠습니다. 예를 들어, 이러한 오류는 블로흐 구면(Bloch sphere)의 매우 작은 회전에 해당할 수 있으며, 이는 예를 들어 완벽하지 않은 게이트로 인해 발생하는 오류를 나타낼 수 있습니다. 또는 이는 큐비트에 대한 다른 어떤 유니타리 연산일 수 있으며 반드시 항등 연산에 가까운 것은 아닙니다.

이러한 오류를 수정하는 것이 어려워 보일 수 있습니다. 결국, 이와 같은 가능한 오류는 무한히 많으며, 우리가 어떻게든 각 오류를 정확하게 식별하고 되돌릴 수 있다는 것은 상상하기 어렵습니다. 그러나 비트-플립, 위상-플립, 또는 둘 다를 수정할 수 있는 한, 우리는 이 단원의 앞부분에서 설명된 절차를 사용하여 임의의 단일 큐비트 유니타리 오류를 수정하는 데 성공할 것입니다.

이것이 왜 그런지 알아보기 위해, 우리는 먼저 단일 큐비트의 오류를 나타내는 임의의 $2 \times 2$ 유니타리 행렬 $U$를 네 개의 파울리 행렬(항등 행렬 포함)의 선형 조합으로 표현할 수 있다는 것을 인식합시다.

<p>$$U = \alpha \mathbb{I} + \beta X + \gamma Y + \delta Z$$</p>

우리가 보게 될 것처럼, 오류 감지 회로가 실행될 때, 신드롬 비트를 제공하는 측정은 인코딩의 상태를 확률적으로 네 개의 파울리 행렬 중 하나로 표현되는 오류(또는 오류의 부재)가 발생한 상태로 효과적으로 붕괴시킵니다. ($U$가 유니타리라는 사실로부터 숫자 $\alpha$, $\beta$, $\gamma$, 그리고 $\delta$가 $\vert\alpha\vert^2 + \vert\beta\vert^2 + \vert\gamma\vert^2 + \vert\delta\vert^2 = 1$을 만족해야 한다는 것이 따르며, 실제로 값 $\vert\alpha\vert^2$, $\vert\beta\vert^2$, $\vert\gamma\vert^2$, 그리고 $\vert\delta\vert^2$는 해당 파울리 오류가 발생한 상태로 인코딩된 상태가 붕괴되는 확률입니다).

이것이 어떻게 작동하는지 더 자세히 설명하기 위해, 주어진 큐비트 유니타리 연산이 어떤 큐비트에 작용하는지 나타내기 위해 아래 첨자를 사용하는 것이 편리할 것입니다. 예를 들어, 쇼어 코드에 사용된 9개의 큐비트에 번호를 매기기 위해 Qiskit의 큐비트 번호 지정 규칙 $(\mathsf{Q}_8,\mathsf{Q}_7,\ldots,\mathsf{Q}_0)$을 사용하면, 단일 큐비트에 대한 다양한 유니타리 연산에 대해 다음과 같은 표현을 얻습니다. 여기서 각 경우에 우리는 해당 유니타리 행렬을 다른 모든 큐비트의 항등 행렬과 텐서곱합니다.

<p>$$X_0 = \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes X$$</p>

<p>$$Z_4 = \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes Z \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I}$$</p>

<p>$$U_7 = \mathbb{I} \otimes U \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I} \otimes \mathbb{I}$$</p>

따라서 특히 주어진 큐비트 유니타리 연산 $U$에 대해, 우리는 큐비트 $k$에 적용된 $U$의 작용을 다음 공식으로 지정할 수 있습니다. 이 공식은 각 행렬이 큐비트 $k$에 적용된 연산을 나타낸다는 점을 제외하고는 이전 공식과 유사합니다.

<p>$$U_k = \alpha \mathbb{I}_k + \beta X_k + \gamma Y_k + \delta Z_k$$</p>

이제 $\vert\psi\rangle$가 큐비트 상태의 9-큐비트 인코딩이라고 가정해 봅시다. 오류 $U$가 큐비트 $k$에서 발생하면, 우리는 상태 $U_k \vert\psi\rangle$를 얻습니다. 이는 $\vert\psi\rangle$에 작용하는 파울리 연산의 선형 조합으로 다음과 같이 표현될 수 있습니다.

<p>$$U_k \vert\psi\rangle = \alpha \vert\psi\rangle + \beta X_k\vert\psi\rangle + \gamma Y_k\vert\psi\rangle + \delta Z_k\vert\psi\rangle$$</p>

이 시점에서 $Y = i X Z$로 대입해 봅시다.

<p>$$U_k \vert\psi\rangle = \alpha \vert\psi\rangle + \beta X_k\vert\psi\rangle + i \gamma X_kZ_k\vert\psi\rangle + \delta Z_k\vert\psi\rangle$$</p>

이제 이전에 설명된 오류 감지 및 수정 단계를 고려해 봅시다. 우리는 세 개의 내부 코드 패리티 검사 결과와 외부 코드의 검사 결과를 합쳐서 8비트로 구성된 단일 신드롬으로 생각할 수 있습니다. 이러한 신드롬 비트를 생성하는 실제 표준 기저 측정 직전에 상태는 다음과 같은 형태를 가집니다.

<p>$$\begin{aligned}\alpha\,\vert \mathbb{I} \text{ syndrome}\rangle \otimes \vert\psi\rangle \\ + \beta\,\vert X_k \text{ syndrome}\rangle \otimes X_k\vert\psi\rangle \\ + i \gamma\,\vert X_k Z_k \text{ syndrome}\rangle \otimes X_k Z_k\vert\psi\rangle \\ + \delta\,\vert Z_k \text{ syndrome}\rangle \otimes Z_k\vert\psi\rangle\end{aligned}$$</p>

분명히 말해서, 이 시점에서 우리는 두 개의 시스템을 가지고 있습니다. 왼쪽에 있는 시스템은 신드롬을 얻기 위해 측정할 8개의 큐비트이며, $\vert \mathbb{I} \text{ syndrome}\rangle$, $\vert X_k \text{ syndrome}\rangle$ 등은 해당 오류(또는 비-오류)와 일치하는 8-큐비트 표준 기저 상태를 나타냅니다. 오른쪽에 있는 시스템은 우리가 인코딩에 사용하고 있는 9개의 큐비트입니다.

이 두 시스템은 이제 (일반적으로) 상관관계가 있으며, 이것이 작동하는 핵심입니다. 신드롬을 측정함으로써, 오른쪽에 있는 9개 큐비트의 상태는 측정된 신드롬과 일치하는 파울리 오류가 큐비트 중 하나에 적용된 상태로 효과적으로 붕괴됩니다. 더욱이, 신드롬 자체는 우리가 오류를 되돌리고 원래 인코딩 $\vert\psi\rangle$를 복구할 수 있도록 충분한 정보를 제공합니다.

특히, 신드롬 큐비트가 측정되고 적절한 수정이 이루어지면, 우리는 밀도 행렬로 표현될 수 있는 상태를 얻습니다.

<p>$$\xi \otimes \vert\psi\rangle\langle\psi\vert $$</p>

여기서

<p>$$\begin{aligned}\xi = \vert\alpha\vert^2 & \vert \mathbb{I} \text{ syndrome}\rangle\langle \mathbb{I} \text{ syndrome}\vert \\ &+ \vert\beta\vert^2 \vert X_k \text{ syndrome}\rangle\langle X_k \text{ syndrome}\vert \\ &+ \vert\gamma\vert^2 \vert X_k Z_k \text{ syndrome}\rangle\langle X_k Z_k \text{ syndrome}\vert \\ &+ \vert\delta\vert^2 \vert Z_k \text{ syndrome}\rangle\langle Z_k \text{ syndrome}\vert \end{aligned}$$</p>

결정적으로, 이것은 곱 상태입니다. 우리는 원래의, 손상되지 않은 인코딩을 오른쪽 텐서 인수로 가지고 있으며, 왼쪽에는 임의의 오류 신드롬을 설명하는 밀도 행렬 $\xi$가 있습니다. 오류가 수정되었기 때문에, 우리가 중요하게 여기는 오른쪽 시스템과는 더 이상 어떤 상관관계도 없습니다. 이 시점에서 우리는 신드롬 큐비트를 버리거나 재설정하여 다시 사용할 수 있습니다. 이것이 오류에 의해 생성된 무작위성, 즉 **엔트로피***가 시스템에서 제거되는 방식입니다.

이것은 유니타리 오류의 특수한 경우에 대한 오류의 이산화입니다. 본질적으로, 신드롬을 측정함으로써, 우리는 오류를 파울리 행렬로 설명되는 오류에 효과적으로 **투영***합니다.

언뜻 보기에, 자체적으로는 작고 거의 눈에 띄지 않는 오류일지라도, 이와 같이 임의의 유니타리 오류를 수정할 수 있다는 것은 사실이 되기에는 너무 좋아 보일 수 있습니다. 하지만, 여기서 깨달아야 할 중요한 점은 이것이 **단일*** 큐비트에 대한 유니타리 오류이며, 코드의 설계에 따라 단일 큐비트 연산은 인코딩된 논리 큐비트의 상태를 변경할 수 없다는 것입니다. 이것이 할 수 있는 모든 것은 상태를 유효한 인코딩의 부분 공간 밖으로 이동시키는 것이지만, 그런 다음 오류 감지가 상태를 붕괴시키고 수정이 상태를 시작했던 곳으로 되돌립니다.

## 2. 임의의 큐비트 오류 (Arbitrary qubit errors)

마지막으로, 반드시 유니타리는 아닌 임의의 오류를 고려해 봅시다. 정확히 말하면, 우리는 임의의 큐비트 채널 $\Phi$로 설명되는 오류를 고려할 것입니다. 예를 들어, 이것은 디페이징(dephasing) 또는 디폴라라이징(depolarizing) 채널, 리셋 채널 또는 우리가 이전에 생각해 본 적이 없는 이상한 채널일 수 있습니다.

첫 번째 단계는 $\Phi$의 모든 크라우스 표현(Kraus representation)을 고려하는 것입니다.

<p>$$\Phi(\sigma) = \sum_j A_j \sigma A_j^{\dagger}$$</p>

이것은 큐비트 채널이므로, 각 $A_j$는 $2\times 2$ 행렬이며, 이를 파울리 행렬의 선형 조합으로 표현할 수 있습니다.

<p>$$A_j = \alpha_j \mathbb{I} + \beta_j X + \gamma_j Y + \delta_j Z$$</p>

이를 통해 우리는 선택된 큐비트 $k$에 대한 오류 $\Phi$의 작용을 파울리 행렬의 관점에서 다음과 같이 표현할 수 있습니다.

<p>$$\Phi_k \bigl( \vert\psi\rangle\langle\psi\vert\bigr) = \sum_j (\alpha_j \mathbb{I}_k + \beta_j X_k + \gamma_j Y_k + \delta_j Z_k) \vert\psi\rangle\langle\psi\vert (\alpha_j \mathbb{I}_k + \beta_j X_k + \gamma_j Y_k + \delta_j Z_k)^{\dagger}$$</p>

요컨대, 우리는 모든 크라우스 행렬을 파울리 행렬의 선형 조합으로 단순히 확장했습니다.

이제 오류 신드롬을 계산하고 측정하며, 드러난 오류를 수정하면, 우리는 유니타리 오류의 경우와 유사한 종류의 상태를 얻게 될 것입니다.

<p>$$\xi \otimes \vert\psi\rangle\langle\psi\vert$$</p>

여기서 이번에는 다음과 같습니다.

<p>$$\begin{aligned}\xi = \sum_j \Bigl(\vert\alpha_j\vert^2 & \vert \mathbb{I} \text{ syndrome}\rangle\langle \mathbb{I} \text{ syndrome}\vert \\ &+ \vert\beta_j\vert^2 \vert X_k \text{ syndrome}\rangle\langle X_k \text{ syndrome}\vert \\ &+ \vert\gamma_j\vert^2 \vert X_k Z_k \text{ syndrome}\rangle\langle X_k Z_k \text{ syndrome}\vert \\ &+ \vert\delta_j\vert^2 \vert Z_k \text{ syndrome}\rangle\langle Z_k \text{ syndrome}\vert \Bigr)\end{aligned}$$</p>

세부 사항은 조금 더 복잡하여 여기서는 보여주지 않습니다. 개념적으로 말하자면, 아이디어는 유니타리 경우와 동일합니다.

## 3. 일반화 (Generalization)

오류의 이산화는 다중 큐비트 오류를 감지하고 수정할 수 있는 양자 오류 수정 코드(quantum error-correcting codes)를 포함하여 다른 코드에도 일반화됩니다. 이러한 경우, 다중 큐비트의 오류는 파울리 행렬의 **텐서 곱***으로 표현될 수 있으며, 이에 상응하여 다른 신드롬은 단 하나의 큐비트가 아닌 다중 큐비트에서 수행될 수 있는 파울리 연산 수정을 지정합니다.

다시 말하지만, 신드롬을 측정함으로써 오류는 파울리 행렬의 텐서 곱으로 표현되는 이산적인 가능성 집합으로 효과적으로 투영되거나 붕괴되며, 이러한 파울리 오류를 수정함으로써 우리는 원래의 인코딩된 상태를 복구할 수 있습니다. 한편, 이 과정에서 생성되는 모든 무작위성은 폐기되거나 재설정되는 신드롬 큐비트로 이동되어, 인코딩을 저장하는 시스템에서 이 과정에서 생성된 무작위성을 제거합니다.
