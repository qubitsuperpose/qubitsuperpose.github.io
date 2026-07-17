---
title: 16차시 3:Variational Algorithms 3
layout: single
classes: wide
categories:
  - Variational Algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 3. Ansätze와 변분 형태

모든 변분 알고리즘의 핵심에는 상태들 간의 차이를 분석하는 핵심 아이디어가 있으며, 이 상태들은 매개변수나 변수의 집합으로부터 어떤 잘 정의된 매핑(예: 연속적, 미분 가능)을 통해 편리하게 연결된다는 점이 있다 — 이름이 그렇게 붙은 이유이다.

먼저, 우리는 직접 매개변수화된 회로를 어떻게 구성하는지 살펴볼 것이다. 우리는 이 회로들을 사용하여 변분 알고리즘이 탐색할 매개변수화된 상태들의 집합을 나타내는 변분 형태(매개변수화된 게이트 층이 일정 횟수 반복되며, 알고리즘 동안 게이트의 매개변수들이 비용 함수를 최소화하도록 최적화된다)를 정의할 것이다. 그리고 나서, 우리는 이 변분 형태를 참조 상태에 적용하여 ansatz(참조 연산자와 변분 형태의 조합으로, 우리가 탐색하고 있는 탐색 공간을 설명)를 구성할 것이다.

우리는 또한 이 탐색 공간을 탐색하면서 속도와 정확성 사이의 절충을 어떻게 할 것인지 살펴볼 것이다.

![Ansatz 논의의 핵심 구성 요소들을 보여주는 다이어그램으로, 경험적 ansätze와 문제 특정 ansätze를 포함한다.](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/ansaetze-and-variational-forms/ansatz-workflow.svg)

## 1. 매개변수화된 양자 회로

변분 알고리듬은 유한한 $k$개의 매개변수 집합 $\vec{\theta} = (\theta^0, \ldots, \theta^{k-1})$에 의존하는 일련의 양자 상태 $\|\psi(\vec{\theta})\rangle$를 탐색하고 비교함으로써 작동한다. 이러한 상태들은 조정 가능한 매개변수로 정의된 게이트를 사용하는 매개변수화된 양자 회로를 통해 준비될 수 있다. 아직 특정 각도를 바인딩하지 않고도 이 매개변수화된 회로를 생성하는 것이 가능하다:


```python
from qiskit.circuit import QuantumCircuit, Parameter

theta = Parameter("θ")

qc = QuantumCircuit(3)
qc.rx(theta, 0)
qc.cx(0, 1)
qc.x(2)

qc.draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F4c564fab-ed65-4949-b27f-658b0f1ed621-0.avif&w=640&q=75)

```python
from math import pi

angle_list = [pi / 3, pi / 2]
circuits = [qc.assign_parameters({theta: angle}) for angle in angle_list]

for circuit in circuits:
    display(circuit.draw("mpl"))
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F4096d161-2916-472b-8abc-d4a6b78955cf-0.avif&w=640&q=75)

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F4096d161-2916-472b-8abc-d4a6b78955cf-1.avif&w=640&q=75)


## 2. 변분 형태와 Ansatz

참조 상태 $\|\rho\rangle$에서 목표 상태 $\|\psi(\vec\theta)\rangle$로 반복적으로 최적화하기 위해서는, 변분 알고리즘이 탐색할 매개변수화된 상태들의 집합을 나타내는 변분 형태 $U_V(\vec{\theta})$를 정의해야 한다:

<p>
$$
\begin{aligned}
|0\rangle \xrightarrow{U_R} U_R|0\rangle
& = |\rho\rangle \xrightarrow{U_V(\vec{\theta})} U_A(\vec{\theta})|0\rangle \\[1mm]
& = U_V(\vec{\theta})U_R|0\rangle \\[1mm]
& = U_V(\vec{\theta})|\rho\rangle \\[1mm]
& = |\psi(\vec{\theta})\rangle \\[1mm]
\end{aligned}
$$
</p>

매개변수화된 상태는 매개변수에 의존하지 않는 참조 상태 $\|\rho\rangle$와 항상 매개변수에 의존하는 변분 형태 $U_V(\vec{\theta})$ 모두에 의존함을 유의하라. 이 두 반쪽의 조합을 ansatz라고 부른다: $U_A(\vec\theta) := U_V(\vec\theta)U_R$.

변분 알고리즘이 탐색할 매개변수화된 상태들의 집합을 나타내기 위해 ansatz를 구성하면서, 우리는 하나의 중요한 문제를 인식하게 된다: 차원성(dimensionality). $n$-큐비트 시스템(즉, 힐베르트 공간)은 구성 공간에서 방대한 수의 서로 다른 양자 상태를 갖는다. 이를 완전히 탐색하기 위해서는 다루기 어려울 정도로 많은 매개변수가 필요할 것이다. 정량적으로, 그 차원성은 $D = 2^{2n}$이다. 설상가상으로, 탐색 알고리즘 및 이와 유사한 알고리즘의 실행 시간 복잡도는 이 차원성에 대해 지수적으로 증가하며, 이 현상은 문헌에서 종종 차원의 저주(curse of dimensionality)라고 불린다.

이러한 문제를 극복하기 위해, 가장 관련성 높은 상태들만 탐색하도록 변분 형태에 몇 가지 합리적인 제약을 가하는 것이 일반적인 관행이다. 효율적인 절단형(truncated) ansatz를 찾는 것은 활발한 연구 분야이지만, 여기서는 두 가지 일반적인 설계를 다루겠다.


## 3. 휴리스틱 ansatz와 트레이드오프

특정 문제에 대해 차원을 제한하는 데 도움이 될 만한 정보가 전혀 없다면, $2^{2n}$개보다 적은 매개변수를 가진 임의의 매개변수화 회로(parameterized circuits) 계열을 시도해 볼 수 있다. 그러나 다음과 같은 트레이드오프를 고려해야 한다:

* **속도**: 탐색 공간을 줄이면 알고리즘의 실행 속도를 높일 수 있다.
* **정확도**: 공간을 축소하면 문제의 실제 해를 제외할 위험이 있어, 최적이 아닌 해(suboptimal solutions)를 얻을 수 있다.
* **노이즈**: 더 깊은 회로는 노이즈의 영향을 받으므로, ansatz의 연결성(connectivity), 게이트, 그리고 게이트 충실도(gate fidelity)를 실험적으로 조정해야 한다.

품질(또는 심지어 해결 가능성)과 속도 사이에는 근본적인 트레이드오프가 있다: 매개변수가 많을수록 정확한 결과를 찾을 가능성은 높아지지만, 알고리즘을 실행하는 데 걸리는 시간은 더 길어진다.

### 3.1 N-local 회로

휴리스틱 ansaetze(안작)의 가장 널리 사용되는 예 중 하나는 N-local 회로인데, 이는 몇 가지 이유 때문이다:

* **효율적인 구현**: N-local 안작은 일반적으로 단순한 지역 게이트로 구성되어 있어, 소수의 물리 큐비트를 사용하여 양자 컴퓨터에서 효율적으로 구현할 수 있다. 이는 양자 회로를 구성하고 최적화하기 더 쉽게 만든다.
* **중요한 상관관계 포착**: N-local 안작은 소수의 게이트만으로도 양자 시스템의 큐비트 간 중요한 상관관계를 포착할 수 있다. 이는 지역 게이트가 인접한 큐비트에 작용하여 이들 사이에 얽힘을 만들 수 있기 때문이며, 이는 복잡한 양자 시스템을 시뮬레이션하는 데 중요할 수 있다.

이러한 회로는 회전 및 얽힘 레이어로 구성되며, 이들은 다음과 같이 하나 이상의 횟수만큼 번갈아 반복된다:

* 각 레이어는 최대 크기 $N$의 게이트로 형성되며, $N$은 큐비트의 수보다 작아야 한다.
* 회전 레이어의 경우, 게이트는 서로 위에 쌓인다. `RX`나 `CRZ`와 같은 표준 회전 연산을 사용할 수 있다.
* 얽힘 레이어의 경우, 얽힘 전략과 함께 `Toffoli` 게이트나 `CX`와 같은 게이트를 사용할 수 있다.
* 두 종류의 레이어 모두 매개변수화될 수도 있고 아닐 수도 있지만, 적어도 하나의 레이어는 매개변수를 포함해야 한다. 그렇지 않으면, 적어도 하나의 매개변수가 없으면 어떠한 변화도 없을 것이다!
* 선택적으로, 회로의 끝에 추가 회전 레이어가 추가될 수 있다.

예를 들어, `RX`및 `CRZ` 게이트로 형성된 회전 블록, $\[0,1,2\]$, $\[0,2,3\]$, $\[4,2,1\]$ 및 $\[3,1,0\]$ 큐비트에 작용하는 `Toffoli` 게이트로 형성된 얽힘 블록, 그리고 각 레이어를 $2$회 반복하는 5큐비트 `NLocal` 회로를 만들어 보자.

```python
from qiskit.circuit.library import NLocal, CCXGate, CRZGate, RXGate
from qiskit.circuit import Parameter

theta = Parameter("θ")
ansatz = NLocal(
    num_qubits=5,
    rotation_blocks=[RXGate(theta), CRZGate(theta)],
    entanglement_blocks=CCXGate(),
    entanglement=[[0, 1, 2], [0, 2, 3], [4, 2, 1], [3, 1, 0]],
    reps=2,
    insert_barriers=True,
)
ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2Fc28d7b53-fbd7-42f5-a619-5a6b0b44e9fc-0.avif&w=3840&q=75)


위 예시에서 가장 큰 게이트는 세 개의 큐비트에 작용하는 Toffoli 게이트이므로, 이 회로는 $3$-local입니다. 가장 일반적으로 사용되는 $N$-local 회로의 유형은 단일 큐비트 회전 게이트와 $2$-큐비트 얽힘 게이트로 구성된 $2$-local 회로입니다.

Qiskit의 `TwoLocal` 클래스를 사용하여 $2$-local 회로를 만들어 보겠습니다. 구문은 `NLocal`과 동일하지만 몇 가지 차이점이 있습니다. 예를 들어, `RX`, `RZ`, `CNOT` 등 대부분의 게이트는 게이트를 임포트하거나 `Parameter` 인스턴스를 생성하지 않고도 문자열로 전달할 수 있습니다.

```python
from qiskit.circuit.library import TwoLocal

ansatz = TwoLocal(
    num_qubits=5,
    rotation_blocks=["rx", "rz"],
    entanglement_blocks="cx",
    entanglement="linear",
    reps=2,
    insert_barriers=True,
)
ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2Fc48521ee-db12-449e-af9f-7d8ec0fa7890-0.avif&w=3840&q=75)

이 경우, 각 큐비트가 다음 큐비트와 얽히는 선형 얽힘 분포를 사용했습니다. 다른 전략에 대해 알아보려면 `TwoLocal` 문서를 참조하십시오.

### 3.2 Efficient SU2

`efficient_su2` 는 SU(2)를 포괄하는 단일 큐비트 연산 레이어와 `CX` 얽힘으로 구성된 하드웨어 효율적 회로입니다. 이는 변분 양자 알고리즘을 위한 시험 파동 함수를 준비하거나 기계 학습을 위한 분류 회로로 사용할 수 있는 휴리스틱 패턴입니다.

```python
from qiskit.circuit.library import efficient_su2

ansatz = efficient_su2(4, su2_gates=["rx", "y"], entanglement="linear", reps=1)
ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F5abc6f15-d7f5-466a-9659-78a11a2c5270-0.avif&w=1200&q=75)



## 4. 문제 특화 ansätze

휴리스틱 및 하드웨어 효율적인 ansätze가 문제를 단순한 방식으로 해결하는 데 도움을 주는 반면, 문제에 대한 특화된 지식을 활용하여 회로 탐색 공간을 특정 유형으로 제한할 수 있다. 이를 통해 탐색 과정에서 정확도를 잃지 않으면서 속도를 향상시킬 수 있다.


### 4.1 최적화

max-cut 문제에서는 그래프의 노드를 서로 다른 그룹에 속한 노드 사이의 간선 수를 최대화하는 방식으로 분할하고자 한다. 아래 그래프에 대해 원하는 max-cut 분할은 명확하다: 왼쪽의 0번 노드를 오른쪽의 나머지 노드들로부터 컷으로 분리해야 한다.

```python
import rustworkx as rx
from rustworkx.visualization import mpl_draw

n = 4
G = rx.PyGraph()
G.add_nodes_from(range(n))
# The edge syntax is (start, end, weight)
edges = [(0, 1, 1.0), (0, 2, 1.0), (0, 3, 1.0), (1, 2, 1.0), (2, 3, 1.0)]
G.add_edges_from(edges)

mpl_draw(
    G, pos=rx.shell_layout(G), with_labels=True, edge_labels=str, node_color="#1192E8"
)
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2Fef9a5e1d-f70a-4f59-9112-2d8f71b253ba-0.avif&w=1920&q=75)

QAOA 알고리즘을 max-cut 문제에 활용하기 위해서는, 연산자의 최소 기대값이 두 다른 그룹에 속한 노드 사이의 최대 간선 수에 해당하도록 비용을 인코딩하는 Pauli 해밀토니안이 필요하다.

이 간단한 예제에서, 연산자는 간선으로 연결된 노드에 Z 연산자가 있는 항들의 선형 결합이다(0번째 큐비트가 가장 오른쪽에 있음을 기억하라): $ZZII + IZZI + ZIIZ + IZIZ + IIZZ$. 연산자가 구성되면, Qiskit 회로 라이브러리의 `QAOAAnsatz` 회로를 사용하여 QAOA 알고리즘을 위한 ansatz를 쉽게 구축할 수 있다.

```python
# Pre-defined ansatz circuit, operator class and visualization tools
from qiskit.circuit.library import QAOAAnsatz
from qiskit.quantum_info import SparsePauliOp

# Problem to Hamiltonian operator
hamiltonian = SparsePauliOp.from_list(
    [("ZZII", 1), ("IZZI", 1), ("ZIIZ", 1), ("IZIZ", 1), ("IIZZ", 1)]
)
# QAOA ansatz circuit
ansatz = QAOAAnsatz(hamiltonian, reps=2)
# Draw
ansatz.decompose(reps=3).draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F69599589-689e-4e0f-8135-937aedd9430d-0.avif&w=3840&q=75)

위 이미지는 이해를 돕기 위해 기본 게이트를 사용하여 표현한 것입니다. 하지만 `reps` 인수를 변경하거나 `decompose` 메서드를 사용하지 않고 회로를 그리면 여러 단계로 분해하여 표현할 수 있습니다. 예를 들어, 다음 그림은 기본값인 `reps=1`을 사용한 QAOA 구조를 직접 보여줍니다.

```python
ansatz.decompose(reps=2).draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F9724a36b-aae6-4d98-aad2-1090f0e1a941-0.avif&w=3840&q=75)


### 4.2 양자 기계 학습

기계 학습에서 흔한 응용은 데이터를 두 개 이상의 범주로 분류하는 것이다. 이는 데이터점을 고전적 특징 벡터를 양자 힐베르트 공간에 사상하는 특징 사상으로 인코딩하는 것을 포함한다. 고전적으로 시뮬레이션하기 어려운 매개변수화된 양자 회로에 기반한 양자 특징 사상을 구성하는 것은 고전적 기계 학습 방법에 대한 잠재적 이점을 얻기 위한 중요한 단계이며, 현재 활발히 연구되고 있는 분야이다.

`zz_feature_map`은 매개변수화된 회로를 생성하는 데 사용될 수 있다. 특징 사상에 데이터점($x$)을 전달하고, 가중치를 매개변수로 전달할 별도의 변분 형태($\theta$)를 전달할 수 있다.

```python
from qiskit.circuit.library import zz_feature_map, TwoLocal

data = [0.1, 0.2]

zz_feature_map_reference = zz_feature_map(feature_dimension=2, reps=2)
zz_feature_map_reference = zz_feature_map_reference.assign_parameters(data)

variation_form = TwoLocal(2, ["ry", "rz"], "cz", reps=2)
vqc_ansatz = zz_feature_map_reference.compose(variation_form)
vqc_ansatz.decompose().draw("mpl")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fvariational-algorithm-design%2Fansaetze-and-variational-forms%2Fextracted-outputs%2F9d3a813b-40cf-4399-b912-714893987c30-0.avif&w=3840&q=75)


## 5. 요약

이 강의를 통해 변분 형식으로 탐색 공간을 정의하는 방법을 배웠습니다:

* 게이트가 조정 가능한 매개변수로 정의된 *매개변수화된* 양자 회로로 상태를 준비
* 속도와 정확도의 트레이드오프를 고려하는 ansatz를 구성하는 방법
* 휴리스틱 ansatz
* 문제 특화 ansatz

우리의 고수준 변분 워크로드는 다음과 같습니다:

![참조 상태를 준비하는 하나의 유니터리와 ansatz를 준비하는 다른 유니터리를 보여주는 회로 다이어그램](https://quantum.cloud.ibm.com/learning/images/courses/variational-algorithm-design/ansaetze-and-variational-forms/ansatz-circuit.svg)

각 변분 매개변수 $\vec\theta$에 대해 다른 양자 상태가 생성됩니다. 최적의 매개변수를 찾기 위해, ansatz의 매개변수를 반복적으로 업데이트할 문제 특화 *비용 함수*를 정의해야 합니다.