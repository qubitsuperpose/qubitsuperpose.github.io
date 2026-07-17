---
title: 15차시 5:Quantum Chemistry with VQE 5
layout: single
classes: wide
categories:
  - Chemistry with VQE
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 5. Bringing it all together with Qiskit Runtime

## 1. 요약

Victoria Lipinska가 지금까지 배운 내용에 대한 최종 요약을 제공합니다.

<iframe width="640" height="360" src="https://video.ibm.com/embed/recorded/132414925" scrolling="no" allowfullscreen webkitallowfullscreen frameborder="0" style="border: 0 none transparent;"></iframe>

### 참고 문헌

위 영상에서 다음 문서들이 참고 문헌으로 인용되었습니다.

* [Quantum Chemistry in the Age of Quantum Computing, Cao, et al.](https://arxiv.org/pdf/1812.09976.pdf)
* [Quantum computational chemistry, McArdle, et al.](https://arxiv.org/pdf/1808.10402.pdf)

## 2. Qiskit 패턴을 이용한 VQE

VQE 계산에 필요한 모든 구성 요소를 갖추었습니다:

* 해밀토니안(Hamiltonian)
* 앤사츠(Ansatz)
* 고전 최적화기(Classical optimizer)

이제 이들을 Qiskit 패턴 프레임워크 안에서 하나로 결합하기만 하면 됩니다.

## 1단계: 고전적 입력을 양자 문제로 매핑하기

앞서 언급했듯이, 여기서는 관심 대상인 해밀토니안이 이미 적절한 형식으로 생성되어 있다고 가정합니다. 이에 관한 질문이 있다면 [해밀토니안 구성에 대한 강의](https://quantum.cloud.ibm.com/learning/courses/quantum-chem-with-vqe/hamiltonian-construction)를 참고하시기 바랍니다. 아래 코드 블록은 이전 강의에서 설명한 구성 요소들을 설정합니다. 여기서는 H2를 모델링하기로 선택했는데, 이는 그 해밀토니안이 충분히 간결하여 직접 작성할 수 있기 때문입니다.

```python
# General imports
import numpy as np
from qiskit.quantum_info import SparsePauliOp

# Hamiltonian obtained from a previous lesson

H = SparsePauliOp(
    [
        "IIII",
        "IIIZ",
        "IZII",
        "IIZI",
        "ZIII",
        "IZIZ",
        "IIZZ",
        "ZIIZ",
        "IZZI",
        "ZZII",
        "ZIZI",
        "YYYY",
        "XXYY",
        "YYXX",
        "XXXX",
    ],
    coeffs=[
        -0.09820182 + 0.0j,
        -0.1740751 + 0.0j,
        -0.1740751 + 0.0j,
        0.2242933 + 0.0j,
        0.2242933 + 0.0j,
        0.16891402 + 0.0j,
        0.1210099 + 0.0j,
        0.16631441 + 0.0j,
        0.16631441 + 0.0j,
        0.1210099 + 0.0j,
        0.17504456 + 0.0j,
        0.04530451 + 0.0j,
        0.04530451 + 0.0j,
        0.04530451 + 0.0j,
        0.04530451 + 0.0j,
    ],
)

nuclear_repulsion = 0.7199689944489797
```

efficient_su2 회로와 최적화 도구인 COBYLA를 선택하여 시작합니다.


```python
# Pre-defined ansatz circuit
from qiskit.circuit.library import efficient_su2

# SciPy minimizer routine
from scipy.optimize import minimize

# Plotting functions

# Random initial state and efficient_su2 ansatz
ansatz = efficient_su2(H.num_qubits, su2_gates=["rx"], entanglement="linear", reps=1)
x0 = 2 * np.pi * np.random.random(ansatz.num_parameters)
print(ansatz.decompose().depth())
ansatz.decompose().draw("mpl")
```

```
5
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fground-state%2Fextracted-outputs%2F28a60667-38f9-46b8-9633-47ceb8e1378f-1.avif&w=1080&q=75)

이제 우리는 비용 함수(cost function)를 구성합니다. 이는 분명히 해밀토니안(Hamiltonian)과 관련이 있지만, 해밀토니안은 연산자(operator)이고 우리는 Estimator를 사용하여 그 연산자의 기댓값을 반환하는 함수를 원한다는 점에서 차이가 있습니다. 물론, 이는 ansatz와 변분 매개변수(variational parameters)를 사용하여 이루어지므로, 이 모든 것이 인자(arguments)로 나타납니다. 아래에서는 실제 하드웨어 또는 시뮬레이터에서 사용하기 위해 약간 다른 버전들을 정의합니다.


```python
def cost_func(params, ansatz, H, estimator):
    pub = (ansatz, [H], [params])
    result = estimator.run(pubs=[pub]).result()
    energy = result[0].data.evs[0]
    return energy


# def cost_func_sim(params, ansatz, H, estimator):
#    energy = estimator.run(ansatz, H, parameter_values=params).result().values[0]
#    return energy
```

## 단계 2: 양자 실행을 위해 문제를 최적화합니다.

우리는 사용하는 하드웨어에서 코드가 가능한 한 효율적으로 실행되기를 원합니다. 따라서 최적화 단계를 시작하기 위해 백엔드를 선택해야 합니다. 아래 코드는 사용 가능한 백엔드 중 현재 가장 한가한 백엔드를 선택합니다.


```python
# To run on hardware, select the backend with the fewest number of jobs in the queue
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService(channel="ibm_quantum_platform")
backend = service.least_busy(operational=True, simulator=False)
backend.name
```

실제 백엔드에서 실행하기 위해 회로를 최적화하는 것은 매우 중요하면서도 다양한 내용을 포함하는 주제입니다. 그러나 이는 VQE에만 국한된 내용은 아닙니다. 여기서는 우선 두 가지 중요한 용어만 살펴보겠습니다.

* **[optimization_level](/docs/api/qiskit/transpiler#preset-pass-managers)**: 이는 회로가 선택한 백엔드의 레이아웃에 얼마나 잘 맞도록 조정되었는지를 나타냅니다. 가장 낮은 최적화 수준은 회로를 장치에서 실행하는 데 필요한 최소한의 작업만 수행합니다. 즉, 회로의 큐비트를 장치의 큐비트에 매핑하고, 모든 2-큐비트 연산을 수행할 수 있도록 스왑 게이트를 추가합니다. 가장 높은 최적화 수준은 훨씬 더 지능적으로 동작하며, 전체 게이트 수를 줄이기 위해 다양한 기법을 활용합니다. 다중 큐비트 게이트는 오류율이 높고 큐비트는 시간이 지남에 따라 결잃음(decoherence*이 발생하므로, 회로가 짧을수록 더 나은 결과를 얻을 가능성이 높습니다.
* **[Dynamical Decoupling](/docs/api/qiskit/qiskit.transpiler.passes.PadDynamicalDecoupling)**: 유휴(idling) 상태의 큐비트에 일련의 게이트를 적용할 수 있습니다. 이를 통해 환경과의 원치 않는 일부 상호작용을 상쇄할 수 있습니다.

회로 최적화에 대한 자세한 내용은 연결된 문서를 참고하십시오. 아래 코드는 `qiskit.transpiler`의 사전 설정된 패스 매니저(preset pass managers)를 사용하여 패스 매니저를 생성합니다.


```python
from qiskit.transpiler import PassManager
from qiskit.transpiler.preset_passmanagers import generate_preset_pass_manager
from qiskit.transpiler.passes import (
    ALAPScheduleAnalysis,
    PadDynamicalDecoupling,
    ConstrainedReschedule,
)
from qiskit.circuit.library import XGate

target = backend.target
pm = generate_preset_pass_manager(target=target, optimization_level=3)
pm.scheduling = PassManager(
    [
        ALAPScheduleAnalysis(target=target),
        ConstrainedReschedule(
            acquire_alignment=target.acquire_alignment,
            pulse_alignment=target.pulse_alignment,
            target=target,
        ),
        PadDynamicalDecoupling(
            target=target,
            dd_sequence=[XGate(), XGate()],
            pulse_alignment=target.pulse_alignment,
        ),
    ]
)


# Use the pass manager and draw the resulting circuit
ansatz_isa = pm.run(ansatz)
ansatz_isa.draw(output="mpl", idle_wires=False, style="iqp")
```

![](https://quantum.cloud.ibm.com/assets-docs-learning/_next/image?url=%2Flearning%2Fimages%2Fcourses%2Fquantum-chem-with-vqe%2Fground-state%2Fextracted-outputs%2Fa5bcdebe-a21c-4dfd-88ba-b6f6b517cd34-0.avif&w=3840&q=75)

마찬가지로 장치 배치 특성을 해밀턴ian에도 적용해야 합니다.

```python
hamiltonian_isa = H.apply_layout(ansatz_isa.layout)
hamiltonian_isa
```

```
SparsePauliOp(['IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZIIIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZIIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZIZIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZZIIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZIIZII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZZIII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZZII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIZIZII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIYYYYII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIYYXXII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIXXYYII', 'IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIXXXXII'],
              coeffs=[-0.09820182+0.j, -0.1740751 +0.j, -0.1740751 +0.j,  0.2242933 +0.j,
  0.2242933 +0.j,  0.16891402+0.j,  0.1210099 +0.j,  0.16631441+0.j,
  0.16631441+0.j,  0.1210099 +0.j,  0.17504456+0.j,  0.04530451+0.j,
  0.04530451+0.j,  0.04530451+0.j,  0.04530451+0.j])
```

## 3단계: Qiskit Primitives를 사용하여 실행하기

선택한 하드웨어에서 실행하기 전에, 간단한 디버깅과 경우에 따라 오류 추정을 위해 시뮬레이터를 사용하는 것이 좋습니다. 이러한 이유로 먼저 시뮬레이터에서 VQE를 실행하는 방법을 간단히 살펴보겠습니다. 그러나 **어떠한 고전 컴퓨터, 시뮬레이터 또는 GPU도** 고도로 얽힌 127큐비트 양자 컴퓨터의 전체 기능을 정확하게 시뮬레이션할 수 없다는 점을 반드시 유념해야 합니다. 현재의 양자 유용성(quantum utility) 시대에서 시뮬레이터의 활용은 제한적입니다.

앞서 살펴본 것처럼, 변분 회로의 매개변수를 하나 선택할 때마다 기댓값(expectation value)을 계산해야 합니다(이는 최소화해야 하는 값이기 때문입니다). 이미 짐작했을 수도 있듯이, 이를 수행하는 가장 효율적인 방법은 Qiskit primitive인 Estimator를 사용하는 것입니다. 먼저 로컬 시뮬레이터를 사용할 것이며, 이를 위해서는 BackendEstimator라고 하는 Estimator의 로컬 버전을 사용해야 합니다.

최적화에 사용한 실제 백엔드를 그대로 유지한 상태에서, 해당 장치의 노이즈 동작 모델을 가져와 원하는 로컬 시뮬레이터와 함께 사용할 수 있습니다. 여기에서는 `aer_simulator_statevector`를 사용하겠습니다.

```python
# We will start by using a local simulator
from qiskit_aer import AerSimulator

# Import Estimator, this time from Qiskit (we will import from Qiskit Runtime for real QPUs)
from qiskit.primitives import BackendEstimatorV2

# generate a simulator that mimics the real quantum system
backend_sim = AerSimulator.from_backend(backend)
estimator = BackendEstimatorV2(backend=backend_sim)
```

이제 VQE를 구현할 차례입니다. 이후 사용할 실제 백엔드를 기반으로 하는 `BackendEstimator`와 선택한 해밀토니안(Hamiltonian), ansatz, 고전적 옵티마이저(classical optimizer)를 사용하여 비용 함수를 최소화합니다. 여기서는 최대 반복 횟수를 비교적 작은 값으로 설정했다는 점에 유의하십시오. 이는 단지 디버깅을 위해 시뮬레이터를 사용하고 있기 때문입니다. VQE의 최적화 단계는 수렴하기까지 수백 번의 반복이 필요한 경우가 많습니다.


```python
res = minimize(
    cost_func,
    x0,
    args=(ansatz_isa, hamiltonian_isa, estimator),
    method="cobyla",
    options={"maxiter": 10, "disp": True},
)

print(getattr(res, "fun") - nuclear_repulsion)
print(res)
```

```
Return from COBYLA because the objective function has been evaluated MAXFUN times.
Number of function values = 10   Least value of F = -0.11556938907226563
The corresponding X is:
[4.11796514 4.52126324 0.69570423 4.12781503 6.55507846 1.80713073
 0.9645473  6.23812214]

-0.8355383835212453
 message: Return from COBYLA because the objective function has been evaluated MAXFUN times.
 success: False
  status: 3
     fun: -0.11556938907226563
       x: [ 4.118e+00  4.521e+00  6.957e-01  4.128e+00  6.555e+00
            1.807e+00  9.645e-01  6.238e+00]
    nfev: 10
   maxcv: 0.0
```

이 코드는 수렴하지는 않았지만 예상대로 올바르게 평가되었습니다. 이제 실제 하드웨어에서 계산을 실행하고 결과를 논의하겠습니다. 실제 백엔드를 사용하기 위해 Qiskit 런타임 추정기를 사용할 것입니다. 이 코드는 Qiskit 런타임 세션 내에서 실행해야 하며, 일반적으로 해당 세션에 대한 옵션을 지정해야 합니다.

```python
from qiskit_ibm_runtime import QiskitRuntimeService, Session
from qiskit_ibm_runtime import EstimatorV2 as Estimator
from qiskit_ibm_runtime.options import EstimatorOptions
```

세션을 사용하면 작업이 시작될 때 한 번만 대기열에 대기하게 되므로, 이후에는 기존 최적화 알고리즘의 반복 실행이 대기열에 추가되지 않습니다. 세션 내에서 복원력과 최적화 수준을 설정할 수 있습니다. 이러한 도구들은 VQE에서 매우 중요하므로, 각 도구에 대한 간략한 설명과 VQE에서의 중요성, 그리고 자세한 내용을 확인할 수 있는 링크를 제공합니다.

* 런타임 세션: VQE는 본질적으로 반복적인 방식으로 작동하며, 기존 최적화 알고리즘은 각 실행마다 새로운 변분 매개변수를 선택하여 새로운 게이트를 사용합니다. 세션을 사용하지 않으면 각 실행 회로 사이에 추가적인 대기 시간이 발생할 수 있습니다. VQE 계산을 세션 내에 캡슐화하면 작업 시작 전 한 번의 초기 대기만 발생하고, 변분 단계 사이에는 추가적인 대기 시간이 발생하지 않습니다. 이 전략은 이전 강의 예제에서도 사용되었지만, 기하 구조가 변하는 경우 더욱 중요한 역할을 할 수 있습니다. 세션에 대한 자세한 내용은 [실행 모드 문서](/docs/guides/execution-modes)를 참조하십시오.

* 런타임 세션: VQE는 본질적으로 반복적인 방식으로 작동하며, 기존 최적화 알고리즘은 각 실행마다 새로운 변분 매개변수를 선택하여 새로운 게이트를 사용합니다. * Estimator의 내장 최적화: Estimator에는 계산 최적화를 위한 내장 옵션이 있습니다. Estimator를 포함한 많은 컨텍스트에서 설정값은 0과 1로 제한되며, 0은 최적화 없음, 1(기본값)은 선택한 하드웨어에 맞춰 회로를 일부 최적화함을 나타냅니다. 다른 컨텍스트에서는 0, 1, 2 또는 3의 설정값을 사용할 수 있습니다. 다양한 설정에서 사용되는 특정 방법에 대한 자세한 내용은 [문서](/docs/api/qiskit-ibm-runtime/options-estimator-options)를 참조하십시오. 여기서는 최적화를 0으로 설정하고 'skip_transpilation = true'를 사용합니다. 이는 위의 최적화 섹션에서 패스 관리자를 사용하여 이미 회로를 트랜스파일링했기 때문입니다.
* Estimator의 내장 복원력: 최적화와 마찬가지로 Estimator에는 오류 완화에 대한 다양한 접근 방식에 해당하는 오류 복원력 설정이 내장되어 있습니다. 복원력 수준 설정에 대한 자세한 내용은 [문서](/docs/guides/error-mitigation-and-suppression-techniques)를 참조하십시오.

오류 완화는 VQE 계산의 수렴에 미묘한 영향을 미친다는 점에 유의해야 합니다. 기존 최적화 알고리즘은 에너지를 최소화하는 매개변수를 찾기 위해 매개변수 공간을 탐색합니다. 최적 매개변수에서 매우 멀리 떨어져 있을 때는 오류가 있더라도 기존 최적화 알고리즘이 급격한 기울기를 감지할 수 있습니다. 그러나 계산이 수렴하고 최적값에 가까워질수록 기울기는 작아지고 오류에 의해 쉽게 가려집니다. 오류 완화를 어느 정도까지 적용할 것인지, 그리고 수렴 과정의 어느 시점에서 적용할 것인지는 특정 사용 사례에 따라 결정해야 합니다.

이번 첫 번째 하드웨어 실행에서는 비교적 빠른 실행을 위해 복원력을 0으로 설정했습니다. 하지만 실제 응용 프로그램에서는 오류 완화를 사용하는 것이 좋습니다. 아래 셀에는 두 가지 옵션 세트가 있습니다. (1) 런타임 세션에 대한 옵션(여기서는 "session_options"로 명명됨)과 (2) 기존 최적화 프로그램에 대한 옵션(여기서는 간단히 "options"라고 함)입니다.


```python
estimator_options = EstimatorOptions(resilience_level=0, default_shots=2000)
with Session(backend=backend) as session:
    estimator = Estimator(mode=session, options=estimator_options)

    res = minimize(
        cost_func,
        x0,
        args=(ansatz_isa, hamiltonian_isa, estimator),
        method="cobyla",
        options={"maxiter": 10, "disp": True},
    )
```

```
Return from COBYLA because the objective function has been evaluated MAXFUN times.
Number of function values = 10   Least value of F = -0.11691688904
The corresponding X is:
[5.11796514 5.52126324 0.69570423 5.12781503 6.55507846 1.80713073
 1.9645473  6.23812214]
```

IBM Quantum® 플랫폼의 [워크로드](https://quantum.cloud.ibm.com/workloads)에서 작업 진행 상황을 확인할 수 있습니다.

```python
print(getattr(res, "fun") - nuclear_repulsion)
print(res)
```

```
-0.8368858834889796
 message: Return from COBYLA because the objective function has been evaluated MAXFUN times.
 success: False
  status: 3
     fun: -0.11691688904
       x: [ 5.118e+00  5.521e+00  6.957e-01  5.128e+00  6.555e+00
            1.807e+00  1.965e+00  6.238e+00]
    nfev: 10
   maxcv: 0.0
```

## 4단계: 후처리를 수행하고, 결과를 고전적 형식으로 반환합니다.

잠시 시간을 내어 이러한 출력 결과를 이해해 봅시다. "**fun**" 출력은 비용 함수에 대해 우리가 얻은 **최솟값**입니다(반드시 마지막으로 계산된 *은 아닙니다). 이것은 양의 핵 반발 에너지를 포함한 총에너지이며, 이것이 우리가 `electron_energy`도 정의한 이유입니다.

위의 경우에는 함수 평가의 최대 횟수를 초과했다는 메시지가 있으며, 함수 평가 횟수(**nfev**)는 10이었습니다. 이는 단순히 최적화의 수렴을 위한 다른 기준들이 충족되지 않았음을 의미합니다. 다시 말해, 우리가 바닥 상태 에너지를 찾았다고 판단할 이유는 없습니다. 이것이 **success**가 "False"인 의미이기도 합니다.

마지막으로 **x**가 있습니다. 이것은 변분 매개변수 벡터입니다. 이러한 매개변수들은 비용 함수(에너지 기댓값)의 최솟값을 얻은 계산에 사용된 값들입니다. 이 여덟 개의 값은 ansatz에서 가변적인 회전 각도를 갖는 게이트들의 여덟 개 회전 각도에 대응합니다.

축하합니다! 여러분은 IBM Quantum QPU에서 VQE 계산을 실행했습니다!

다음 강의에서는 이 작업 흐름을 조정하여 해밀토니안에 변수를 포함하는 방법을 살펴보겠습니다. 양자 화학 문제의 맥락에서는, 이는 분자의 형태나 결합 위치를 결정하기 위해 분자 기하구조를 변화시키는 것을 의미할 수 있습니다.

