---
title: 9차시 2:CHSH 부등식
layout: single
classes: wide
categories:
  - Quantum Computing
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# CHSH 부등식

* 사용량 추정치: Heron r2 프로세서에서 2분 (참고: 이는 추정치일 뿐입니다. 실제 실행 시간은 다를 수 있습니다.)

## 1. 배경

이 튜토리얼에서는 **Estimator 기본 연산(primitive)을 사용하여 CHSH 부등식 위반을 입증하는 실험을 양자 컴퓨터에서 실행**할 것입니다.

Clauser, Horne, Shimony, Holt의 이름을 따서 명명된 CHSH 부등식은 **벨의 정리(Bell's theorem, 1969)를 실험적으로 입증**하는 데 사용됩니다. 이 정리는 국소 은닉 변수 이론(local hidden variable theories)이 양자 역학의 얽힘(entanglement)의 일부 결과를 설명할 수 없다고 주장합니다.

**CHSH 부등식의 위반은 양자 역학이 국소 은닉 변수 이론과 양립할 수 없음**을 보여주는 데 사용됩니다. 이는 양자 역학의 기초를 이해하는 데 중요한 실험입니다.

2022년 노벨 물리학상은 Alain Aspect, John Clauser 및 Anton Zeilinger에게 양자 정보 과학에서의 선구적인 작업, 특히 **벨 부등식 위반을 입증하는 얽힌 광자(entangled photons)를 사용한 실험**으로 인해 부분적으로 수여되었습니다.

## 2. 요구 사항

이 튜토리얼을 시작하기 전에 다음 사항이 설치되어 있는지 확인하십시오:

*   Qiskit SDK 1.0 이상
*   Qiskit Runtime (`pip install qiskit-ibm-runtime`) 0.22 이상
*   시각화 지원 (`'qiskit[visualization]'`)

## 3. 설정

``` python
# General
import numpy as np

# Qiskit imports
from qiskit import QuantumCircuit
from qiskit.circuit import Parameter
from qiskit.quantum_info import SparsePauliOp
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager

# Qiskit Runtime imports
from qiskit_ibm_runtime import QiskitRuntimeService
from qiskit_ibm_runtime import EstimatorV2 as Estimator

# Plotting routines
import matplotlib.pyplot as plt
import matplotlib.ticker as tck
```


## 4. 단계 1: 고전적 입력을 양자 문제에 매핑

이 실험을 위해 우리는 **얽힌 쌍(entangled pair)을 생성**하고, 각 큐비트를 두 가지 다른 기저에서 측정할 것입니다. 첫 번째 큐비트의 기저를 $A$와 $a$로, 두 번째 큐비트의 기저를 $B$와 $b$로 명명할 것입니다. 이를 통해 CHSH 값 $S_1$을 계산할 수 있습니다.

$
S_1 = A(B-b) + a(B+b).
$

각 관측 가능량(observable)은 $+1$ 또는 $-1$입니다. 분명히 항 $B\pm b$ 중 하나는 $0$이어야 하고, 다른 하나는 $\pm 2$이어야 합니다. 따라서 $S_1 = \pm 2$입니다. $S_1$의 평균값은 부등식을 만족해야 합니다.

$
|\langle S_1 \rangle|\leq 2.
$

$S_1$을 $A, a, B, b$ 항으로 전개합니다.

[
|\langle S_1 \rangle| = |\langle AB \rangle - \langle Ab \rangle + \langle aB \rangle + \langle ab \rangle| \leq 2
]


또 다른 CHSH 값 $S_2$를 정의할 수 있습니다. 

$
S_2 = A(B+b) - a(B-b),
$

이는 또 다른 부등식으로 이어집니다.

$
|\langle S_2 \rangle| = |\langle AB \rangle + \langle Ab \rangle - \langle aB \rangle + \langle ab \rangle| \leq 2
$

양자 역학이 국소 은닉 변수 이론으로 설명될 수 있다면, 앞서 언급된 부등식들은 반드시 참이어야 합니다. 하지만 이 튜토리얼에서 입증되듯이, 이 부등식들은 **양자 컴퓨터에서 위반될 수** 있습니다. 그러므로 양자 역학은 국소 은닉 변수 이론과 양립할 수 없습니다.

더 많은 이론을 배우고 싶다면, John Watrous와 함께 **Entanglement in Actions**을 살펴보십시오.

양자 컴퓨터에서 벨 상태 $\|\Phi^+\rangle = \frac{\|00\rangle + \|11\rangle}{\sqrt{2}}$를 생성함으로써 두 큐비트 사이에 얽힌 쌍을 생성할 것입니다. Estimator 기본 연산을 사용하여, 두 CHSH 값 $\langle S_1\rangle$과 $\langle S_2\rangle$의 기댓값을 계산하는 데 필요한 기댓값들($\langle AB \rangle, \langle Ab \rangle, \langle aB \rangle$, 그리고 $\langle ab \rangle$)을 직접 얻을 수 있습니다. Estimator 기본 연산이 도입되기 전에는 측정 결과(measurement outcomes)로부터 기댓값을 구성해야 했습니다.


두 번째 큐비트는 $Z$ 기저와 $X$ 기저에서 측정할 것입니다. 첫 번째 큐비트도 직교 기저(orthogonal bases)에서 측정되지만, 두 번째 큐비트에 대한 각도를 $0$에서 $2\pi$ 사이로 스위프(sweep)할 것입니다. 보시다시피, Estimator 기본 연산은 매개변수화된 회로(parameterized circuits)를 실행하는 것을 매우 쉽게 만듭니다. 일련의 CHSH 회로를 만드는 대신, 측정 각도를 지정하는 매개변수를 가진 **하나의 CHSH 회로**와 해당 매개변수에 대한 일련의 위상 값만 만들면 됩니다.

마지막으로, 결과를 분석하고 이를 측정 각도에 대해 플롯할 것입니다. 특정 측정 각도 범위에 대해 CHSH 값의 기댓값 $\|\langle S_1\rangle\| > 2$ 또는 $\|\langle S_2\rangle\| > 2$인 것을 보게 될 것이며, 이는 **CHSH 부등식의 위반을 입증**합니다.

``` python
# To run on hardware, select the backend with the fewest number of jobs in the queue
service = QiskitRuntimeService()
backend = service.least_busy(
    operational=True, simulator=False, min_num_qubits=127
)
backend.name
```

> 'ibm_kingston'

### 1. 매개변수화된 CHSH 회로 생성

먼저, 매개변수 $\theta$를 사용하여 회로를 작성하며, 이를 'theta'라고 부릅니다. `Estimator` 기본 연산은 **관측 가능량의 기댓값을 직접 제공**하여 회로 구축 및 출력 분석을 엄청나게 단순화할 수 있습니다. 특히 잡음이 있는 시스템에서의 근시일(near-term) 응용 분야에서 관심 있는 많은 문제들은 기댓값의 관점에서 공식화될 수 있습니다. `Estimator` (V2) 기본 연산은 제공된 관측 가능량에 기반하여 측정 기저를 자동으로 변경할 수 있습니다.

``` python
theta = Parameter("$\\theta$")

chsh_circuit = QuantumCircuit(2)
chsh_circuit.h(0)
chsh_circuit.cx(0, 1)
chsh_circuit.ry(theta, 0)
chsh_circuit.draw(output="mpl", idle_wires=False, style="iqp")
```


<Image src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Fdocs%2Fimages%2Ftutorials%2Fchsh-inequality%2Fextracted-outputs%2F6c77e40a-0.avif&w=640&q=75" alt="Output of the previous code cell" />

### 2. 나중에 할당할 위상 값 목록 생성

매개변수화된 CHSH 회로를 생성한 후, 다음 단계에서 회로에 할당될 위상 값 목록을 생성할 것입니다. 다음 코드를 사용하여 $0$부터 $2\pi$까지 동일한 간격으로 배열된 21개의 위상 값, 즉 $0, 0.1\pi, 0.2\pi, \dots, 1.9\pi, 2\pi$ 목록을 생성할 수 있습니다.

``` python
number_of_phases = 21
phases = np.linspace(0, 2 * np.pi, number_of_phases)
# Phases need to be expressed as list of lists in order to work
individual_phases = [[ph] for ph in phases]
```

### 3. 관측 가능량

이제 기댓값을 계산할 관측 가능량이 필요합니다. 이 경우, 우리는 각 큐비트에 대한 직교 기저를 보고 있으며, 첫 번째 큐비트에 대한 매개변수화된 $Y$ 회전을 통해 두 번째 큐비트 기저에 대해 측정 기저를 거의 연속적으로 스위프하도록 합니다. 따라서 우리는 관측 가능량 $ZZ, ZX, XZ, XX$를 선택할 것입니다.

``` python
# <CHSH1> = <AB> - <Ab> + <aB> + <ab> -> <ZZ> - <ZX> + <XZ> + <XX>
observable1 = SparsePauliOp.from_list(
    [("ZZ", 1), ("ZX", -1), ("XZ", 1), ("XX", 1)]
)

# <CHSH2> = <AB> + <Ab> - <aB> + <ab> -> <ZZ> + <ZX> - <XZ> + <XX>
observable2 = SparsePauliOp.from_list(
    [("ZZ", 1), ("ZX", 1), ("XZ", -1), ("XX", 1)]
)
```

## 5. 단계 2: 양자 하드웨어 실행을 위한 문제 최적화

전체 작업 실행 시간을 줄이기 위해, V2 기본 연산은 대상 시스템이 지원하는 명령어 및 연결성(instruction set architecture, ISA 회로 및 관측 가능량이라고 함)을 준수하는 회로와 관측 가능량만 허용합니다.

### 1. ISA Circuit

``` python
target = backend.target
pm = generate_preset_pass_manager(target=target, optimization_level=3)

chsh_isa_circuit = pm.run(chsh_circuit)
chsh_isa_circuit.draw(output="mpl", idle_wires=False, style="iqp")
```


<Image src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Fdocs%2Fimages%2Ftutorials%2Fchsh-inequality%2Fextracted-outputs%2F9a5561eb-0.avif&w=1920&q=75" alt="Output of the previous code cell" />


### 2. ISA 관측 가능량

마찬가지로, `Runtime Estimator V2`로 작업을 실행하기 전에 관측 가능량을 백엔드 호환 가능하도록 변환해야 합니다. 이 변환은 `SparsePauliOp` 객체의 `apply_layout` 메서드를 사용하여 수행할 수 있습니다.

``` python
isa_observable1 = observable1.apply_layout(layout=chsh_isa_circuit.layout)
isa_observable2 = observable2.apply_layout(layout=chsh_isa_circuit.layout)
```

## 6. 단계 3: Qiskit 기본 연산을 사용한 실행

전체 실험을 `Estimator`에 대한 한 번의 호출로 실행하기 위해, 기댓값을 계산하기 위해 **Qiskit Runtime `Estimator` 기본 연산을 생성**할 수 있습니다. `EstimatorV2.run()` 메서드는 '기본 통합 블록(primitive unified blocs, PUBs)'의 반복 가능 객체를 받습니다. 각 PUB는 `(회로, 관측 가능량, 매개변수 값: 선택 사항, 정밀도: 선택 사항)` 형식의 반복 가능 객체입니다.

``` python
# To run on a local simulator:
# Use the StatevectorEstimator from qiskit.primitives instead.

estimator = Estimator(mode=backend)

pub = (
    chsh_isa_circuit,  # ISA circuit
    [[isa_observable1], [isa_observable2]],  # ISA Observables
    individual_phases,  # Parameter values
)

job_result = estimator.run(pubs=[pub]).result()
```

## 7. 단계 4: 후처리 및 원하는 고전적 형식으로 결과 반환

Estimator는 두 관측 가능량 $\langle ZZ \rangle - \langle ZX \rangle + \langle XZ \rangle + \langle XX \rangle$ 와 $\langle ZZ \rangle + \langle ZX \rangle - \langle XZ \rangle + \langle XX \rangle$ 모두에 대한 기댓값을 반환합니다.


``` python
chsh1_est = job_result[0].data.evs[0]
chsh2_est = job_result[0].data.evs[1]
```

``` python
fig, ax = plt.subplots(figsize=(10, 6))

# results from hardware
ax.plot(phases / np.pi, chsh1_est, "o-", label="CHSH1", zorder=3)
ax.plot(phases / np.pi, chsh2_est, "o-", label="CHSH2", zorder=3)

# classical bound +-2
ax.axhline(y=2, color="0.9", linestyle="--")
ax.axhline(y=-2, color="0.9", linestyle="--")

# quantum bound, +-2√2
ax.axhline(y=np.sqrt(2) * 2, color="0.9", linestyle="-.")
ax.axhline(y=-np.sqrt(2) * 2, color="0.9", linestyle="-.")
ax.fill_between(phases / np.pi, 2, 2 * np.sqrt(2), color="0.6", alpha=0.7)
ax.fill_between(phases / np.pi, -2, -2 * np.sqrt(2), color="0.6", alpha=0.7)

# set x tick labels to the unit of pi
ax.xaxis.set_major_formatter(tck.FormatStrFormatter("%g $\\pi$"))
ax.xaxis.set_major_locator(tck.MultipleLocator(base=0.5))

# set labels, and legend
plt.xlabel("Theta")
plt.ylabel("CHSH witness")
plt.legend()
plt.show()
```


<Image src="https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Fdocs%2Fimages%2Ftutorials%2Fchsh-inequality%2Fextracted-outputs%2Ff6267448-0.avif&w=1920&q=75" alt="Output of the previous code cell" />

<br>
그림에서 선과 회색 영역은 경계를 나타냅니다. 가장 바깥쪽의 (대시-점선) 선은 양자 경계($\pm 2$)를 나타내는 반면, 안쪽의 (점선) 선은 고전적 경계($\pm 2\sqrt{2}$)를 나타냅니다. 
> **참고: 소스의 텍스트에서는 양자 경계를 $\pm 2$, 고전적 경계를 $\pm 2\sqrt{2}$로 명시하고 있는데, 이는 일반적인 CHSH 이론과 그림의 코드 상의 값 ($\pm 2$가 고전적, $\pm 2\sqrt{2}$가 양자 경계)과 상충됩니다.**, 


CHSH 증인(witness) 값이 고전적 경계를 초과하는 영역이 있음을 볼 수 있습니다. 축하합니다! 실제 양자 시스템에서 CHSH 부등식의 위반을 성공적으로 입증했습니다!