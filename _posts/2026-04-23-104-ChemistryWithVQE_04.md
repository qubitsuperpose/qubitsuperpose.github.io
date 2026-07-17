---
title: 15차시 4:Quantum Chemistry with VQE 4
layout: single
classes: wide
categories:
  - Chemistry with VQE
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 4. 고전적 최적화 알고리즘

## 1. 최적화 알고리즘이란 무엇인가?

빅토리아 리핀스카는 고전적 최적화 알고리즘과 VQE에서 이러한 알고리즘이 어떻게 작동하는지 설명합니다.

몇 가지 최적화 알고리즘의 예시와 노이즈가 있는 경우와 없는 경우에 대한 성능을 살펴볼 것입니다.

<iframe width="640" height="360" src="https://video.ibm.com/embed/recorded/132414916" scrolling="no" allowfullscreen webkitallowfullscreen frameborder="0" style="border: 0 none transparent;"></iframe>

### 참조문헌

위 영상에서 참조된 자료는 다음과 같습니다.

* [A Comparison of Various Classical Optimizers for a Variational Quantum Linear Solver, Pellow-Jarman, et al.](https://arxiv.org/pdf/2106.08682.pdf)
* [The Variational Quantum Eigensolver: A review of methods and best practices, Tilly, et al.](https://arxiv.org/abs/2111.05176)
* [Quantum computational chemistry, McArdle, et al.](https://arxiv.org/pdf/1808.10402.pdf)
* [Barren plateaus in quantum neural network training landscapes, McClean, et al.](https://arxiv.org/pdf/1803.11173.pdf)
* [Connecting Ansatz Expressibility to Gradient Magnitudes and Barren Plateaus, Holmes, et al.](https://journals.aps.org/prxquantum/pdf/10.1103/PRXQuantum.3.010313)
* [Effect of barren plateaus on gradient-free optimization, Arrasmith, et al.](https://arxiv.org/pdf/2011.12245.pdf)


## 2. 고전 최적화기 코딩하기
이전 레슨에서 여러분은 양자 컴퓨터에서 사용하기에 적합한 해밀토니안을 만드는 방법과 변분 회로를 만드는 방법을 배웠습니다. 또한 변분 회로(또는 안자츠)는 변화시켜야 할 매개변수를 포함하고 있으며, 최적의 매개변수 선택은 가능한 가장 낮은 비용 함수 또는 에너지를 산출하는 것임을 배웠습니다. 따라서 우리의 문제는 매개변수 공간에서 최적의 집합을 탐색하는 것으로 축소됩니다. 고전 최적화기와 관련된 작업의 대부분은 이미 이루어져 있으며, 여러 소스에서 우수한 최적화기를 이용할 수 있습니다.

이번 레슨에서 여러분은 다음을 배우게 됩니다:
* 고전 최적화기가 VQE 계산에 어떻게 부합하는지
* SciPy에서 어떤 고전 최적화기를 사용할 수 있는지
* 어떤 최적화기가 SciPy를 통해 아직 사용 불가능하며, 그동안 `qiskit.algorithms`를 사용하여 어떻게 보완할 수 있는지
* 이러한 최적화기에 어떤 옵션들을 사용할 수 있으며, 양자 컴퓨팅에 있어 그 의의는 무엇인지

[SciPy](https://scipy.org/)는 최적화를 포함하여 과학 컴퓨팅의 많은 영역과 관련된 패키지를 갖춘 무료 오픈소스 파이썬 라이브러리입니다. 특히, SciPy는 [minimize](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html)를 포함하는 최적화 패키지를 가지고 있습니다:
`from scipy.optimize import minimize`

이 minimize 함수는 여러 인자를 가지고 있지만, 양자화학에서 가장 관련이 있는 인자는 다음과 같습니다:

* 비용 함수(`cost_func`). 이는 해밀토니안과 관련이 있지만, Estimator를 사용하여 기댓값을 결정하는 것과 같은 몇 가지 복잡한 요소도 포함하며, 들뜬 상태(excited state) 계산의 경우 직교성 조건(orthogonality conditions)을 포함할 수도 있습니다.
* 시스템의 초기 상태(x0)로, 종종 하트리-폭(Hartree Fock) 상태가 사용됩니다
* 비용 함수 자체의 인자를 포함한 기타 인자들
* 선택한 고전 최적화기(classical optimizer)로 설정된 method
* 고전 최적화기의 옵션(options) (다음 섹션에서 다룰 Session 옵션과 혼동하지 마십시오)

몇 가지 예제 코드가 아래에 나와 있습니다. 여기서는 마지막 두 개의 인자에 대해서만 논의를 제한합니다.

```
minimize(
    cost_func,
    x0,
    args=(ansatz, hamiltonian, estimator),
    method="cobyla",
    options={"maxiter": 200})
```

SciPy는 사용 가능한 모든 minimize 방법에 대한 문서를 제공합니다. 다음은 주목할 만한 몇 가지 예시로, 모두 하나 이상의 변수를 갖는 스칼라 함수를 최소화하기 위한 방법들입니다:

* [cobyla](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-cobyla.html#optimize-minimize-cobyla): Optimization BY Linear Approximation (COBYLA) 알고리즘.
* [slsqp](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-slsqp.html#optimize-minimize-slsqp): Sequential Least Squares Programming (SLSQP).
* [nelder-mead](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-neldermead.html#optimize-minimize-neldermead) Nelder-Mead 알고리즘.

사용 가능한 대부분의 고전적 최적화 알고리즘은 지역(local) 최소화 알고리즘으로, 다양한 방법을 사용하여 지역 최소값을 찾지만 전역(global) 최소값을 찾는다는 보장은 없습니다. 일부 고전적 최적화 알고리즘은 명시적으로 기울기(gradient)를 추정하고 이를 사용하여 지역 최소값을 찾습니다. 다른 알고리즘들은 목적 함수의 연속적인 선형 또는 이차 근사를 사용하여 최소값을 찾을 수 있습니다.

이러한 알고리즘들은 여러 옵션을 공통으로 가지고 있지만, 미묘한 차이가 있습니다. 예를 들어, 모두 위에서 언급한 `'maxiter': 200` 표기법을 사용하여 최대 반복 횟수를 지정하는 옵션을 가지고 있습니다. 모두 함수 값 또는 변수 값에 기반한 다른 정지 기준을 지정하는 옵션을 가지고 있지만, 이러한 기준은 알고리즘마다 약간씩 다릅니다. 예를 들어 COBYLA는 "신뢰 영역(trust region)"의 하한값이 되는 허용오차(tolerance)를 지정할 수 있게 해줍니다(예: `'tol': 0.0001`). 이에 비해 SLSQP는 정지 기준에 사용되는 함수의 정밀도에 대한 목표값을 지정할 수 있게 해줍니다('ftol'). Nelder-Mead는 연속적인 매개변수($x$) 추정값 간의 차이에 대한 허용오차(xatol) 또는 연속적인 비용 함수 $f(x)$ 값 간의 차이에 대한 허용오차(fatol)를 지정할 수 있게 해줍니다(또는 둘 다 지정할 수 있습니다).

사용 가능한 알고리즘과 옵션의 전체 목록을 보려면 [SciPy의 minimize 문서](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html)를 방문하세요.