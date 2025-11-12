---
title: 9차시 1:Hello World! 
layout: single
classes: wide
categories:
  - Quantum Computing
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

# Hello world

이 예제는 두 부분으로 구성되어 있습니다. 먼저 간단한 양자 프로그램을 생성하고 양자 처리 장치(QPU)에서 실행합니다. 실제 양자 연구에는 훨씬 더 강력한 프로그램이 필요하기 때문에, 두 번째 섹션에서는 간단한 프로그램을 유틸리티 수준으로 확장할 것입니다.


## 0. 시작하기 전에

아직 설치하지 않았다면, Install and set up 지침을 따르십시오. 여기에는 **IBM Cloud®를 사용하도록 설정하는** 단계도 포함됩니다.

IBM Quantum® 플랫폼 문서에 있는 코드 예제는 **Jupyter** 노트북을 사용하여 개발되었습니다. 예제를 따라 하려면 Jupyter 노트북을 **로컬에서** 실행하거나 **온라인 환경**에서 실행할 수 있는 환경을 설정하는 것이 좋습니다. 권장되는 추가 시각화 지원(`'qiskit[visualization]'`)을 반드시 설치하십시오. 또한 이 예제의 두 번째 부분에서는 `matplotlib` 패키지가 필요합니다.

양자 컴퓨팅에 대해 전반적으로 배우려면 IBM Quantum Learning의 **양자 정보 기초 과정**을 방문하십시오. IBM®은 양자 컴퓨팅의 책임 있는 개발에 전념하고 있습니다. IBM의 책임 있는 양자에 대해 자세히 알아보고 **책임 있는 양자 컴퓨팅 및 포괄적 기술** 항목에서 책임 있는 양자 원칙을 검토하십시오.


## 1. 간단한 양자 프로그램 생성 및 실행

Qiskit 패턴을 사용하여 양자 프로그램을 작성하는 네 가지 단계는 다음과 같습니다:

1. **문제를 양자 고유 형식으로 매핑**합니다.
2. **회로와 연산자를 최적화**합니다.
3. **양자 프리미티브 함수를 사용하여 실행**합니다.
4. **결과를 분석**합니다.

### 단계 1. 문제를 양자 고유 형식으로 매핑

양자 프로그램에서 **양자 회로**는 양자 명령을 나타내는 고유 형식이며, **연산자**는 측정할 관측량(observables)을 나타냅니다. 회로를 만들 때, 일반적으로 새 `QuantumCircuit`객체를 생성한 다음, 순서대로 명령을 추가합니다.

다음 코드 셀은 두 큐비트가 서로 완전히 얽혀 있는 상태인 **벨 상태(Bell state)**를 생성하는 회로를 만듭니다.

> LSb 0 비트 번호 지정 \
> Qiskit을 포함한 많은 양자 플랫폼은 고전 컴퓨터와 달리 오른쪽에서 왼쪽으로 큐비트/비트의 번호를 매깁니다. 
> Qiskit에서 4개의 큐비트가 있는 회로를 정의하면, 큐비트의 인덱스는 **$q\[3\],q\[2\],q\[1\],q\[0\]$**가 됩니다.

사용 가능한 모든 작업에 대해서는 문서의 `QuantumCircuit`를 참조하십시오.

``` python
from qiskit import QuantumCircuit
from qiskit.quantum_info import SparsePauliOp
from qiskit.transpiler import generate_preset_pass_manager
from qiskit_ibm_runtime import EstimatorV2 as Estimator

# Create a new circuit with two qubits
qc = QuantumCircuit(2)

# Add a Hadamard gate to qubit 0
qc.h(0)

# Perform a controlled-X gate on qubit 1, controlled by qubit 0
qc.cx(0, 1)

# Return a drawing of the circuit using MatPlotLib ("mpl").
# These guides are written by using Jupyter notebooks, which
# display the output of the last line of each cell.
# If you're running this in a script, use `print(qc.draw())` to
# print a text drawing.
qc.draw("mpl")
```

<Image src="https://quantum.cloud.ibm.com/docs/images/tutorials/hello-world/extracted-outputs/930ca3b6-0.svg" alt="Output of the previous code cell" />

<br>

양자 회로를 만들 때 실행 후 **어떤 유형의 데이터를 반환**받을지 고려해야 합니다. Qiskit은 데이터를 반환하는 두 가지 방법을 제공합니다: 측정하려는 큐비트 세트에 대한 **확률 분포**를 얻거나, **관측량의 기댓값**을 얻을 수 있습니다. Qiskit primitives를 사용하여 이 두 가지 방법 중 하나로 회로를 측정하도록 워크로드를 준비하십시오 

이 예제는 **`qiskit.quantum_info`** 서브 모듈을 사용하여 기댓값을 측정하는데, 이는 양자 상태를 변경하는 동작 또는 프로세스를 나타내는 데 사용되는 수학적 객체인 **연산자**를 사용하여 지정됩니다. 다음 코드 셀은 6개의 2큐비트 Pauli 연산자(`IZ`, `IX`, `ZI`, `XI`, `ZZ`, `XX`)를 생성합니다.

``` python
# Set up six different observables.

observables_labels = ["IZ", "IX", "ZI", "XI", "ZZ", "XX"]
observables = [SparsePauliOp(label) for label in observables_labels]
```

> 여기서 `ZZ`와 같은 연산자는 텐서곱 $Z\otimes Z$의 약어입니다. 이는 큐비트 1과 큐비트 0에서 Z를 함께 측정하고 큐비트 1과 큐비트 0 사이의 **상관 관계**에 대한 정보를 얻는 것을 의미합니다. 이러한 기댓값은 일반적으로 $\langle Z_1 Z_0 \rangle$로도 표기됩니다. 상태가 얽혀 있는 경우, $\langle Z_1 Z_0 \rangle$의 측정값은 $\langle I_1 \otimes Z_0 \rangle \langle Z_1 \otimes I_0 \rangle$의 측정값과 달라야 합니다. 위에서 설명한 회로로 생성된 특정 얽힌 상태의 경우, $\langle Z_1 Z_0 \rangle$의 측정값은 1이어야 하고 $\langle I_1 \otimes Z_0 \rangle \langle Z_1 \otimes I_0 \rangle$의 측정값은 0이어야 합니다.


### 단계 2. 회로 및 연산자 최적화

장치에서 회로를 실행할 때, 회로에 포함된 명령어 세트를 최적화하고 **전체 깊이** (대략 명령어의 수)를 최소화하는 것이 중요합니다. 이는 오류와 노이즈의 영향을 줄여 가능한 최상의 결과를 얻도록 보장합니다. 또한, 회로의 명령어는 백엔드 장치의 **Instruction Set Architecture (ISA)**를 준수해야 하며, 장치의 기본 게이트와 큐비트 연결성을 고려해야 합니다.

다음 코드는 작업을 제출할 실제 장치를 인스턴스화하고, 해당 백엔드의 ISA와 일치하도록 회로와 관측량을 변환합니다. 이 작업을 수행하려면 자격 증명(credentials)을 이미 저장했어야 합니다.

``` python
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService()

backend = service.least_busy(simulator=False, operational=True)

# Convert to an ISA circuit and layout-mapped observables.
pm = generate_preset_pass_manager(backend=backend, optimization_level=1)
isa_circuit = pm.run(qc)

isa_circuit.draw("mpl", idle_wires=False)
```

<Image src="https://quantum.cloud.ibm.com/docs/images/tutorials/hello-world/extracted-outputs/9a901271-0.svg" alt="Output of the previous code cell" />

### 단계 3. 양자 프리미티브를 사용한 실행

양자 컴퓨터는 무작위 결과를 생성할 수 있으므로, 일반적으로 회로를 여러 번 실행하여 출력 샘플을 수집합니다. `Estimator` 클래스를 사용하여 관측량의 값을 추정할 수 있습니다. `Estimator`는 두 가지 **프리미티브** 중 하나이며; 다른 하나는 양자 컴퓨터에서 데이터를 얻는 데 사용될 수 있는 `Sampler`입니다. 이 객체들은 **프리미티브 통합 블록(PUB)**을 사용하여 회로, 관측량, 그리고 매개변수(해당하는 경우)의 선택을 실행하는 `run()` 메서드를 가지고 있습니다.


``` python
# Construct the Estimator instance.

estimator = Estimator(mode=backend)
estimator.options.resilience_level = 1
estimator.options.default_shots = 5000

mapped_observables = [
    observable.apply_layout(isa_circuit.layout) for observable in observables
]

# One pub, with one circuit to run against five different observables.
job = estimator.run([(isa_circuit, mapped_observables)])

# Use the job ID to retrieve your job data later
print(f">>> Job ID: {job.job_id()}")
```


> Job ID: d2qkdds65eic73blka9g

작업이 제출된 후, 현재 Python 인스턴스 내에서 작업이 완료될 때까지 기다리거나, `job_id`를 사용하여 나중에 데이터를 검색할 수 있습니다 (자세한 내용은 섹션 on retrieving jobs 참조). 작업이 완료되면 작업의 `result()` 속성을 통해 출력을 검토하십시오.


``` python
# This is the result of the entire submission.  You submitted one Pub,
# so this contains one inner result (and some metadata of its own).
job_result = job.result()

# This is the result from our single pub, which had six observables,
# so contains information on all six.
pub_result = job.result()[0]
```

> Alternative: run the example using a simulator
>
> 양자 프로그램을 실제 장치에서 실행할 때, 워크로드는 실행되기 전에 대기열에서 기다려야 합니다. 시간을 절약하기 위해, 대신 다음 코드를 사용하여 Qiskit Runtime 로컬 테스트 모드와 함께 `fake_provider`에서 이 작은 워크로드를 실행할 수 있습니다. 이는 작은 회로에 대해서만 가능하다는 점에 유의하십시오. 다음 섹션에서 규모를 확장할 때는 실제 장치를 사용해야 합니다.

``` python

# Use the following code instead if you want to run on a simulator:

from qiskit_ibm_runtime.fake_provider import FakeFez
backend = FakeFez()
estimator = Estimator(backend)

# Convert to an ISA circuit and layout-mapped observables.

pm = generate_preset_pass_manager(backend=backend, optimization_level=1)
isa_circuit = pm.run(qc)
mapped_observables = [
    observable.apply_layout(isa_circuit.layout) for observable in observables
]

job = estimator.run([(isa_circuit, mapped_observables)])
result = job.result()

# This is the result of the entire submission.  You submitted one Pub,
# so this contains one inner result (and some metadata of its own).

job_result = job.result()

# This is the result from our single pub, which had five observables,
# so contains information on all five.

pub_result = job.result()[0]
```


### Step 4. Analyze the results {#step-4-analyze-the-results}

분석 단계는 일반적으로 **측정 오류 완화(measurement error mitigation)** 또는 **영점 잡음 외삽(ZNE: zero noise extrapolation)**과 같은 기술을 사용하여 결과를 **후처리**하는 단계입니다. 이러한 결과를 추가 분석을 위해 다른 워크플로에 공급하거나 주요 값 및 데이터의 플롯을 준비할 수 있습니다. 일반적으로 이 단계는 문제에 따라 다릅니다. 이 예제에서는 회로에 대해 측정된 각 기댓값을 플로팅합니다.

Estimator에 지정한 관측량에 대한 **기댓값과 표준 편차**는 작업 결과의 `PubResult.data.evs` 및 `PubResult.data.stds` 속성을 통해 액세스됩니다. Sampler의 결과를 얻으려면 `PubResult.data.meas.get_counts()` 함수를 사용하십시오. 이 함수는 비트 문자열을 키로, 해당 값으로 계수를 포함하는 측정값의 `dict`를 반환합니다. 자세한 내용은 Get started with Sampler 를 참조.

``` python
# Plot the result

from matplotlib import pyplot as plt

values = pub_result.data.evs

errors = pub_result.data.stds

# plotting graph
plt.plot(observables_labels, values, "-o")
plt.xlabel("Observables")
plt.ylabel("Values")
plt.show()
```

<Image src="https://quantum.cloud.ibm.com/docs/images/tutorials/hello-world/extracted-outputs/87143fcc-0.svg" alt="Output of the previous code cell" />

<br>
(플롯 분석) 큐비트 0과 1의 경우, X와 Z의 독립적인 기댓값은 모두 0인 반면, **상관 관계**(`XX` 및 `ZZ`)는 1이라는 점에 주목하십시오. 이는 **양자 얽힘(quantum entanglement)의 특징**입니다.

## 2. 대규모 큐비트로 확장

양자 컴퓨팅에서 **유틸리티 규모의 작업**은 이 분야의 발전에 매우 중요합니다. 이러한 작업은 100개 이상의 큐비트와 1000개 이상의 게이트를 사용할 수 있는 회로를 다루는 등 훨씬 더 큰 규모로 계산을 수행해야 합니다. 이 예제는 **100-큐비트 GHZ 상태**를 생성하고 분석하여 IBM® QPU에서 유틸리티 규모의 작업을 수행하는 방법을 보여줍니다. Qiskit 패턴 워크플로를 사용하며, 각 큐비트에 대한 기댓값 $\langle Z_0 Z_i \rangle$를 측정하는 것으로 끝납니다.

### 단계 1. 문제 매핑

$n$-큐비트 GHZ 상태(본질적으로 확장된 벨 상태)를 준비하는 `QuantumCircuit`를 반환하는 함수를 작성한 다음, 이 함수를 사용하여 100-큐비트 GHZ 상태를 준비하고 측정할 관측량을 수집합니다.

``` python
from qiskit import QuantumCircuit


def get_qc_for_n_qubit_GHZ_state(n: int) -> QuantumCircuit:
    """This function will create a qiskit.QuantumCircuit (qc) for an n-qubit GHZ state.

    Args:
        n (int): Number of qubits in the n-qubit GHZ state

    Returns:
        QuantumCircuit: Quantum circuit that generate the n-qubit GHZ state, assuming all qubits start in the 0 state
    """
    if isinstance(n, int) and n >= 2:
        qc = QuantumCircuit(n)
        qc.h(0)
        for i in range(n - 1):
            qc.cx(i, i + 1)
    else:
        raise Exception("n is not a valid input")
    return qc


# Create a new circuit with two qubits (first argument) and two classical
# bits (second argument)
n = 100
qc = get_qc_for_n_qubit_GHZ_state(n)
```

다음으로, 관심 있는 연산자로 매핑합니다. 이 예제는 큐비트 사이의 `ZZ` 연산자를 사용하여 큐비트가 서로 멀어짐에 따라 동작이 어떻게 되는지 조사합니다. 멀리 떨어진 큐비트 간에 점점 더 부정확해지는 (손상된) 기댓값은 존재하는 노이즈 수준을 나타낼 것입니다.

``` python
from qiskit.quantum_info import SparsePauliOp

# ZZII...II, ZIZI...II, ... , ZIII...IZ
operator_strings = [
    "Z" + "I" * i + "Z" + "I" * (n - 2 - i) for i in range(n - 1)
]
print(operator_strings)
print(len(operator_strings))

operators = [SparsePauliOp(operator) for operator in operator_strings]
```


### 단계 2. 양자 하드웨어 실행을 위한 문제 최적화

다음 코드는 백엔드의 ISA와 일치하도록 회로와 관측량을 변환합니다. 이 작업을 수행하려면 자격 증명을 이미 저장했어야 합니다.

``` python
from qiskit.transpiler import generate_preset_pass_manager
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService()

backend = service.least_busy(
    simulator=False, operational=True, min_num_qubits=100
)
pm = generate_preset_pass_manager(optimization_level=1, backend=backend)

isa_circuit = pm.run(qc)
isa_operators_list = [op.apply_layout(isa_circuit.layout) for op in operators]
```

### 단계 3. 하드웨어에서 실행

작업을 제출하고 **동역학적 디커플링**이라고 불리는 오류 감소 기술을 사용하여 오류 억제를 활성화하십시오. **복원력 수준(resilience level)**은 오류에 대해 구축할 복원력의 정도를 지정합니다. 수준이 높을수록 처리 시간이 길어지는 대신 더 정확한 결과를 생성합니다. 다음 코드에 설정된 옵션에 대한 자세한 설명은 Configure error mitigation for Qiskit Runtime을 참조하십시오.

``` python
from qiskit_ibm_runtime import EstimatorOptions
from qiskit_ibm_runtime import EstimatorV2 as Estimator

options = EstimatorOptions()
options.resilience_level = 1
options.dynamical_decoupling.enable = True
options.dynamical_decoupling.sequence_type = "XY4"

# Create an Estimator object
estimator = Estimator(backend, options=options)
```

``` python
# Submit the circuit to Estimator
job = estimator.run([(isa_circuit, isa_operators_list)])
job_id = job.job_id()
print(job_id)
```
> d2qkqt9olshc73bmb9g0

### 단계 4. 결과 후처리

작업이 완료된 후, 결과를 플로팅하고 이상적인 시뮬레이션에서는 모든 $\langle Z_0 Z_i \rangle$가 1이어야 함에도 불구하고, **$i$가 증가함에 따라 $\langle Z_0 Z_i \rangle$가 감소**한다는 점에 주목하십시오.

``` python
import matplotlib.pyplot as plt
from qiskit_ibm_runtime import QiskitRuntimeService

# data
data = list(range(1, len(operators) + 1))  # Distance between the Z operators
result = job.result()[0]
values = result.data.evs  # Expectation value at each Z operator.
values = [
    v / values[0] for v in values
]  # Normalize the expectation values to evaluate how they decay with distance.

# plotting graph
plt.plot(data, values, marker="o", label="100-qubit GHZ state")
plt.xlabel("Distance between qubits $i$")
plt.ylabel(r"$\langle Z_i Z_0 \rangle / \langle Z_1 Z_0 \rangle $")
plt.legend()
plt.show()
```


<Image src="https://quantum.cloud.ibm.com/docs/images/tutorials/hello-world/extracted-outputs/de91ebd0-0.svg" alt="Output of the previous code cell" />
<br>
이전 플롯은 큐비트 간 거리가 증가함에 따라 **노이즈 존재로 인해 신호가 감쇠**한다는 것을 보여줍니다.

> 추천: 다음 단계
>
- **회로를 구축하는 방법**에 대해 더 자세히 알아보십시오.
- **VQE를 사용한 하이젠베르크 체인의 바닥 상태 에너지 추정** 튜토리얼을 시도해 보십시오.