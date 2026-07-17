---
title: 15차시 3:Quantum Chemistry with VQE 3
layout: single
classes: wide
categories:
  - Chemistry with VQE
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 3. The ansatz

빅토리아 리핀스카가 변분 양자 고유값 해법(VQE)의 맥락에서 안자츠가 무엇이며 왜 중요한지 설명하는 영상을 시청하세요.

<iframe width="640" height="360" src="https://video.ibm.com/embed/recorded/132414879" scrolling="no" allowfullscreen webkitallowfullscreen frameborder="0" style="border: 0 none transparent;"></iframe>

### 참조 문헌

위 영상에서 참조된 자료는 다음과 같습니다.

* [The theory of variational hybrid quantum-classical algorithms, McClean, et al.](https://iopscience.iop.org/article/10.1088/1367-2630/18/2/023023/meta)
* [Quantum Chemistry in the Age of Quantum Computing, Cao, et al.](https://arxiv.org/pdf/1812.09976.pdf)
* [Noisy intermediate-scale quantum (NISQ) algorithms, Bharti, et al.](https://arxiv.org/pdf/2101.08448.pdf)
* [The Variational Quantum Eigensolver: A review of methods and best practices, Tilly, et al.](https://arxiv.org/abs/2111.05176)
* [Hardware-efficient Variational Quantum Eigensolver for Small Molecules and Quantum Magnets, Kandala, et al.](https://arxiv.org/pdf/1704.05018.pdf)
* [Quantum computational chemistry, McArdle, et al.](https://arxiv.org/pdf/1808.10402.pdf)


## 1. Ansatz 코드

이전 수업에서 분자의 에너지를 기술하는 **Hamiltonian**을 만들고, 이를 양자 컴퓨터에서 유용한 형식으로 매핑했습니다. VQE는 *변분 회로(variational circuit)*를 사용하여 양자 상태를 준비합니다. 그런 다음 이러한 상태를 사용하여 Hamiltonian의 기댓값(에너지)을 결정합니다. 변분 회로의 매개변수는 계산이 최소 기댓값에 수렴할 때까지 변화됩니다. 양자 화학 맥락에서, 이것은 기저 상태 에너지가 되어야 합니다. 이 수업은 변분 회로, 즉 **ansatz**(독일어로 "접근" 또는 "방법"을 의미하는 단어)에 초점을 맞춥니다.

이 수업에서 배울 내용은 다음과 같습니다.

* 회로 라이브러리에서 사용할 수 있는 미리 만들어진 ansatz 집합
* ansatz의 특징을 지정하거나 수정하는 방법
* 자신만의 ansatz를 구축하는 방법
* 좋은 ansatz와 나쁜 ansatz의 예

Qiskit 회로 라이브러리에는 ansatz로 사용할 수 있는 많은 범주의 회로가 있습니다. 여기서는 두 국소 회로(한 번에 최대 두 개의 큐비트에 작용하는 게이트로 구성된 회로)에 대해서만 논의를 제한하겠습니다. **Efficient SU2**는 일반적으로 사용되는 ansatz입니다.

`efficient_su_2` 회로는 SU(2)(2차 특수 유니타리 군, Pauli 회전 게이트와 유사)에 의해 생성된 단일 큐비트 연산 층과 CX 얽힘(entanglement)으로 구성됩니다. 이것은 VQE나 양자 머신러닝(QML)의 분류 회로와 같은 변분 양자 알고리즘에서 유용할 수 있는 휴리스틱 패턴입니다.

SU(2) 게이트 두 종류, 예를 들어 `rx`와 `y`를 가진 4큐비트 예제 `efficient_su2` 회로부터 시작하겠습니다. 또한 얽힘 방식과 반복 횟수를 지정합니다. 단순히 `.draw()`를 사용하면 상당히 추상적인 표현을 얻게 됩니다. 좀 더 이해하기 쉬운 회로 다이어그램은 `.decompose().draw()`를 사용하여 얻을 수 있으며, 여기서는 `output = "mpl"`을 사용하겠습니다.

```python
from qiskit.circuit.library import efficient_su2

SU2_ansatz = efficient_su2(4, su2_gates=["rx", "y"], entanglement="linear", reps=1)
print(SU2_ansatz.draw())
SU2_ansatz.decompose().draw(output="mpl")
```

```
     ┌──────────┐┌───┐     ┌──────────┐   ┌───┐                     
q_0: ┤ Rx(θ[0]) ├┤ Y ├──■──┤ Rx(θ[4]) ├───┤ Y ├─────────────────────
     ├──────────┤├───┤┌─┴─┐└──────────┘┌──┴───┴───┐   ┌───┐         
q_1: ┤ Rx(θ[1]) ├┤ Y ├┤ X ├─────■──────┤ Rx(θ[5]) ├───┤ Y ├─────────
     ├──────────┤├───┤└───┘   ┌─┴─┐    └──────────┘┌──┴───┴───┐┌───┐
q_2: ┤ Rx(θ[2]) ├┤ Y ├────────┤ X ├─────────■──────┤ Rx(θ[6]) ├┤ Y ├
     ├──────────┤├───┤        └───┘       ┌─┴─┐    ├──────────┤├───┤
q_3: ┤ Rx(θ[3]) ├┤ Y ├────────────────────┤ X ├────┤ Rx(θ[7]) ├┤ Y ├
     └──────────┘└───┘                    └───┘    └──────────┘└───┘
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2F6028e7e8-7f4e-45d1-b08b-c8d4e567c1a3-1.avif&w=1200&q=75)

SU(2) 게이트는 su2_gates = $\[...\]$에서 지정된 순서와 요소들로 시작과 끝에 나타납니다. 얽힘(entanglement) 방식 `linear`는 CX 게이트가 번호가 매겨진 큐비트들을 순서대로 거쳐가면서 0번과 1번, 그다음 1번과 2번을 얽히게 하는 식으로, 회로에서 대각선을 따라 내려가는 것을 의미합니다. 예상하실 수 있듯이, `reps = 2`로 설정하면 단순히 얽힘 층과 마지막 SU(2) 층이 하나씩 추가됩니다. `reps = n`으로 설정하는 것은 `n`개의 얽힘 층에 대응하며, 그 사이사이와 양 끝에 SU(2) 층이 위치합니다.

```python
SU2_ansatz2 = efficient_su2(
    4, su2_gates=["rx", "y", "z"], entanglement="linear", reps=2
)
SU2_ansatz2.decompose().draw(output="mpl")
```

다른 여러 가지 얽힘 방식이 있습니다. 그중에서도 '순환형'과 '전체형' 두 가지를 주목할 만합니다. 순환형 얽힘은 선형 얽힘과 동일하지만, 첫 번째 큐비트와 마지막 큐비트를 연결하는 CX 게이트가 추가됩니다. 전체형 얽힘 방식은 모든 큐비트 쌍 사이에 CX 게이트를 포함합니다. N개의 큐비트로 이루어진 회로의 경우, 이는 $N(N-1)/2$개의 CX 게이트가 되므로 계산 비용이 상당히 많이 들 수 있습니다.

```python
SU2_ansatz3 = efficient_su2(
    4, su2_gates=["rx", "y", "z"], entanglement="circular", reps=1
)
SU2_ansatz3.decompose().draw(output="mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2Fe7eb387b-8766-4f3c-a0cc-f07b4b6d5e85-0.avif&w=2048&q=75)

```python
SU2_ansatz4 = efficient_su2(4, su2_gates=["rx", "y", "z"], entanglement="full", reps=1)
SU2_ansatz4.decompose().draw(output="mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2Fb2cc6b8a-c6e8-456f-ac0c-1f8b33ef964b-0.avif&w=1920&q=75)

`.depth()` 또는 경우에 따라 `.decompose().depth()`를 사용하여 회로 깊이를 모니터링할 수 있습니다.

```python
print(SU2_ansatz4.decompose().depth())
```

```
11
```

efficient_su2의 일반화가 two-local 회로이며, 이는 n-local 회로의 특수한 경우입니다. two-local 회로 또한 SU(2) 블록(또는 회전 블록)과 얽힘 블록을 포함합니다. 여기서는 사용하고자 하는 얽힘 게이트의 종류를 자유롭게 지정할 수 있으며, 예를 들어 CRX 게이트를 사용할 수 있습니다. 이 예시에서는 모든 게이트가 파라미터를 받아들이지만, 반드시 그럴 필요는 없습니다. 예를 들어 Y 회전 게이트와 CX 얽힘 게이트를 사용할 수도 있습니다.

```python
from qiskit.circuit.library import n_local

rotation_blocks = ["ry"]
entanglement_blocks = ["crx"]
two_ansatz = n_local(
    4, rotation_blocks, entanglement_blocks, "linear", insert_barriers=True, reps=2
)
two_ansatz.decompose().draw(output="mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2Ffdb6071a-7f29-4214-960c-93c304e64a6a-0.avif&w=3840&q=75)

마지막으로 이름을 붙여 논의할 ansatz는 Pauli-two-design입니다. 이 회로는 $RY(pi/4)$의 초기 회전을 포함하며, 회전 레이어는 단일 큐비트 파울리 회전으로 구성되고, 이때 축은 X, Y, Z 중 균등하게 무작위로 선택됩니다. 얽힘 레이어는 총 깊이가 2인 쌍별 CZ 게이트로 구성됩니다. 이 pauli_two_design과, 예를 들어 efficient_su2 사이의 얽힘(및 전체 회로) 깊이 차이에 주목하십시오.

```python
from qiskit.circuit.library import pauli_two_design

PtwoD_ansatz = pauli_two_design(5, reps=1, seed=10599, insert_barriers=True)
PtwoD_ansatz.decompose().draw(output="mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2F1cff47db-5ee5-4e58-ac99-cbc2af9801c7-0.avif&w=1920&q=75)

이러한 기성 변분 회로(pre-made variational circuits)는 원하는 수준의 얽힘(entanglement)을 달성하는 측면과 회로 깊이(circuit depth)를 제한하는 측면 모두에서 유용한 휴리스틱(heuristics)입니다. 하지만 여기에 마법 같은 것은 없습니다. 여러분은 자유롭게 자신만의 변분 회로를 만들 수 있습니다. 실제로 시스템의 목표 상태(target state)의 얽힘에 대해 무언가를 알고 있는 경우에는 이것이 유리할 수 있습니다.

자신만의 안자츠(ansatz)를 만들기 위해서는, 단순히 매개변수 벡터(parameter vector)의 원소들의 함수인 게이트(gate)들을 일부 부분집합으로 포함하는 양자 회로를 구성하면 됩니다(아래의 3-큐비트 예제에서는 "theta"가 이에 해당합니다).



```python
from qiskit import QuantumCircuit
from qiskit.circuit import ParameterVector

n = 3

theta = ParameterVector("θ", length=n)
qc = QuantumCircuit(n)
qc.h(0)
qc.h(2)
for i in range(n - 1):
    qc.cx(i, i + 1)
qc.cz(0, n - 1)
qc.barrier()
for i in range(n):
    qc.ry(theta[i], i)
qc.barrier()
qc.cz(0, n - 1)
for i in reversed(range(n - 1)):
    qc.cx(i, i + 1)
qc.h(0)
qc.h(1)
own_ansatz = qc
print(own_ansatz.depth())
qc.draw("mpl")
```

```
9
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2F1e7ad487-fc57-4c76-abb2-6279695fab5f-1.avif&w=1920&q=75)

일반적으로 최적의 안자츠(ansatz)를 선택하는 것은 하나의 예술이다; 최적의 안자츠란 가장 적은 최적화 단계로 목표에 도달하도록 도와주는 안자츠이다. 나쁠 가능성이 높은 안자츠를 식별하는 것은 더 쉽다. 예를 들어, 회로 깊이가 커질수록 오류가 누적되는 경향이 있다. 오류 완화(error mitigation)가 이에 도움이 될 수 있지만, 회로 깊이를 합리적인 수준에서 최대한 낮게 유지하는 것이 좋은 관행이다. 하지만 필요한 얽힘(entanglement)을 생략해서는 안 된다. 목표 상태(target state)가 완전한 얽힘 스킴을 필요로 할 수도 있다. 아래에 상당히 명백한 이유로 좋지 않은 선택일 가능성이 높은 두 가지 예시가 나와 있다. 좋은 안자츠를 선택하는 것은 이후 섹션에서 수렴 테스트(convergence tests)의 맥락에서 다시 다룰 것이다.

이 첫 번째 회로는 마지막 큐비트가 다른 큐비트들과 전혀 얽혀 있지 않기 때문에 좋지 않은 선택일 가능성이 높다. 실제로 마지막 큐비트에 대해서는 계산적으로 의미 있는 작용이 전혀 없다. 십중팔구, 마지막 큐비트는 다른 큐비트들과 얽히게 하거나 계산에서 제거해야 할 것이다.

```python
n = 4

theta = ParameterVector("θ", length=n)
qc = QuantumCircuit(n)
qc.h(0)
qc.h(2)
for i in range(n - 2):
    qc.cx(i, i + 1)
qc.cz(0, n - 2)
qc.barrier()
for i in range(n):
    qc.ry(theta[i], i)
qc.barrier()
qc.cz(0, n - 2)
for i in reversed(range(n - 2)):
    qc.cx(i, i + 1)
qc.h(0)
qc.h(1)
own_ansatz2 = qc
print(own_ansatz2.depth())
qc.draw("mpl")
```

```
9
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2Ff8ef9c33-1.avif&w=1920&q=75)

마지막 회로는 좋은 선택이 아닐 가능성이 높은데, 게이트 깊이가 매우 높고, 얽힘 레이어를 네 번 반복하는 것이 두 번이나 세 번 반복하는 것보다 목표 상태에 실질적으로 더 잘 맞는 결과를 낼 가능성이 낮기 때문이다.

```python
su2_ansatz_long = efficient_su2(
    4, su2_gates=["rx", "y", "z"], entanglement="linear", reps=4
)
print(su2_ansatz_long.decompose().depth())
su2_ansatz_long.decompose().draw(output="mpl")
```

```
24
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fansatz%2Fextracted-outputs%2F73627a8d-1.avif&w=3840&q=75)

이 회로들은 "완성된" 것이 아닌데, 왜냐하면 많은 게이트에 삽입되어야 할 미지의 변수 파라미터들이 여전히 존재하기 때문이다. 이 파라미터들은 연속적인 추측을 통해 선택되며, 비용 함수의 기댓값(화학적 맥락에서는 일반적으로 바닥상태 에너지)을 낮추는 방향으로 파라미터를 갱신해 나간다. 1차원, 혹은 몇 차원 정도에서는 이것이 사소한 일이다. 하지만 위의 회로는 20개의 변분 파라미터를 가지고 있으며, 이는 최소 에너지를 갖는 목표 상태를 찾는다는 것이 20차원 상태를 탐색해야 함을 의미한다(불필요한 회로 게이트를 포함시키지 말아야 할 또 다른 이유이다). 바로 이 지점에서 고전적인 최적화 알고리즘이 등장하게 되며, 이는 다음 강의의 주제이다.
