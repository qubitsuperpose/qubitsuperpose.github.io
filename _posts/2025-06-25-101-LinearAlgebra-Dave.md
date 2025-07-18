---
title: 2차시 1:Linear Algebra(Dave)
layout: single
classes: wide
categories:
  - Linear Algebra
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

소스의 내용을 선형대수학에 입문하는 사람도 이해할 수 있게 쉽게 정리해줘

## 1. 선형대수학은 무엇인가요?
- 출처: [Introduction to Linear Algebra: Systems of Linear Equations](https://www.youtube.com/watch?v=csgNflj69-Y&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=1)

### 1.1 선형대수학, 왜 배울까요?

선형대수학은 **선형 방정식**을 다루는 학문입니다. 쉽게 말해, 우리가 일상생활에서 마주하는 다양한 문제를 수학적으로 **연립 선형 방정식**으로 만들고, 이를 효율적으로 해결하는 방법을 배우는 것이죠. 우유와 오렌지 주스를 사는 간단한 계산부터 복잡한 과학, 공학, 경제 문제까지, 이 학문은 우리가 세상을 이해하고 문제를 해결하는 데 필수적인 도구들을 제공합니다. 앞으로 **행렬**이라는 중요한 도구를 많이 사용하게 될 거예요.

### 1.2 선형 방정식이란 무엇인가요?

선형 방정식은 $y = mx + b$처럼 가장 기본적인 형태의 방정식입니다. 과학, 공학, 경제 등 여러 분야에서 복잡하지 않으면서도 다양한 실제 관계를 효과적으로 설명할 수 있기 때문에 매우 중요하게 다뤄집니다.

일반적으로 선형 방정식은 $a_1X_1 + a_2X_2 + ... + a_nX_n = B$와 같은 형태로 표현됩니다. 여기서:
* $a$는 **계수** (숫자)입니다.
* $X$는 **변수** (예: $x, y, z$ 또는 $x_1, x_2, x_3$)입니다.

**가장 중요한 특징:**
* 변수에 **지수**($x^2, y^3$ 등)가 붙지 않습니다.
* **근호, 삼각 함수** 같은 복잡한 함수가 포함되지 않습니다.
* 변수들이 서로 **곱해지거나 나누어지지 않습니다**. 각 항은 단순히 '숫자 곱하기 변수' 형태입니다.

### 1.3 연립 선형 방정식은 무엇이고, 왜 복잡해질까요?

**연립 선형 방정식**은 두 개 이상의 선형 방정식이 함께 있는 것을 말합니다. 예를 들어, 우유와 오렌지 주스를 살 때 '가진 돈'과 '들 수 있는 무게'라는 두 가지 제한이 있다면, 이는 두 개의 선형 방정식으로 이루어진 연립 방정식이 됩니다.

우리의 목표는 이 모든 방정식을 **동시에 만족시키는 변수들의 값(해)**을 찾는 것입니다. 방정식 하나하나는 간단하지만, 변수나 방정식의 개수가 많아질수록 시스템 전체가 매우 복잡해질 수 있습니다.

### 1.4 연립 선형 방정식의 해는 무엇을 의미할까요? (기하학적 해석)

연립 선형 방정식의 해는 우리가 기하학적으로 어떻게 시각화하느냐에 따라 의미가 달라집니다.

1. 변수가 두 개인 경우 (예: $x, y$)
각 선형 방정식은 좌표 평면에서 **직선**을 나타냅니다. 연립 방정식의 해는 **두 직선이 만나는 교점**이 됩니다.
* **유일한 해**: 두 직선이 한 점에서 만날 때 (딱 하나의 해).
* **해가 없는 경우**: 두 직선이 평행해서 만나지 않을 때.
* **무수히 많은 해**: 두 방정식이 사실상 같은 직선을 나타낼 때 (모든 점이 해).

2. 변수가 세 개인 경우 (예: $x, y, z$)
각 선형 방정식은 3차원 공간에서 **평면**을 나타냅니다. 해는 **세 평면이 만나는 교점이나 교선**이 됩니다.
* **유일한 해**: 세 평면이 한 점에서 만날 때.
* **무수히 많은 해**:
    * 세 평면이 한 선을 따라 만날 때.
    * 세 방정식이 모두 같은 평면을 나타낼 때.
* **해가 없는 경우**: 세 평면이 평행하거나 다른 방식으로 만나지 않을 때.

3. 변수가 네 개 이상인 경우
이 경우에는 더 이상 **기하학적으로 그림을 그리기 어렵습니다**. 그래서 선형대수학에서 배우는 다른 개념과 도구들을 활용해 문제를 해결해야 합니다.

### 1.5 시스템의 일관성 (Consistency)

* **일관성 있는 시스템 (Consistent system)**: 적어도 하나의 해(유일한 해 또는 무수히 많은 해)를 가지는 시스템. 즉, 답이 있는 경우를 말합니다.
* **모순된 시스템 (Inconsistent system)**: 해가 없는 시스템. 즉, 답이 없는 경우를 말합니다.

선형대수학은 이처럼 다양한 연립 선형 방정식을 효율적으로 풀기 위한 강력한 수학적 도구와 개념을 제공하는 학문입니다. 앞으로 이러한 시스템들을 해결하는 여러 가지 방법을 배우게 될 것입니다.

## 2. 행렬(Matrix)이란 무엇일까요?
- 출처: [Understanding Matrices and Matrix Notation](https://www.youtube.com/watch?v=y6bVhgmy2rw&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=2)


### 2.1 행렬이란 무엇인가요?

행렬은 **숫자들을 가로줄(행)과 세로줄(열)로 묶어 괄호 안에 배열한 것**입니다. 복잡한 선형 방정식 시스템을 더 쉽고 간결하게 표현하기 위해 사용됩니다.

### 2.2 방정식 시스템을 행렬로 바꾸는 방법

예를 들어, 다음과 같은 방정식이 있다고 해봅시다.

```
2x + 3y + 4z = 10
5x + 6y + 7z = 20
8x + 9y + 1z = 30
```

여기서 중요한 것은 변수(x, y, z) 앞의 숫자들, 즉 **계수**입니다.

1.  **계수 행렬 만들기**:

      * 각 방정식의 계수들을 순서대로 한 줄(행)로 만듭니다.
      * 첫 번째 방정식: (2, 3, 4) $\\rightarrow$ 첫 번째 행
      * 두 번째 방정식: (5, 6, 7) $\\rightarrow$ 두 번째 행
      * 세 번째 방정식: (8, 9, 1) $\\rightarrow$ 세 번째 행
      * 이렇게 만들어진 행렬을 **계수 행렬**이라고 합니다.
$$\begin{pmatrix}2 & 3 & 4 \\5 & 6 & 7 \\8 & 9 & 1\end{pmatrix}$$

2.  **첨가 행렬 만들기**:

      * 방정식의 등호 오른쪽에 있는 숫자들(10, 20, 30)도 행렬에 넣어야 합니다.
      * 이 숫자들을 계수 행렬의 **가장 오른쪽에 새로운 열로 추가**합니다.
      * 이렇게 계수와 상수항을 모두 포함한 행렬을 **첨가 행렬**이라고 부릅니다. 첨가 행렬에서 계수 부분과 상수항 부분을 구분하기 위해 점선을 넣기도 합니다.
    
$ $ $$\begin{pmatrix}
    2 & 3 & 4 & | & 10 \\
    5 & 6 & 7 & | & 20 \\
    8 & 9 & 1 & | & 30
\end{pmatrix}$$ 



### 2.3 행렬을 만들기 전 꼭 지켜야 할 규칙

행렬을 정확하게 만들려면 몇 가지 중요한 준비 단계가 필요합니다.

  * **모든 방정식의 형태가 같아야 합니다.** 만약 변수가 등호 오른쪽에 있다면 왼쪽으로 옮겨서 $Ax + By = C$와 같은 형태로 만들어야 합니다.
  * **각 방정식에서 변수의 순서가 항상 같아야 합니다.** (예: 모든 방정식이 $x, y, z$ 순서여야 합니다.)
  * **어떤 변수가 방정식에 없다면, 해당 변수의 계수를 $0$으로 써서 포함시켜야 합니다.** (예: $2x + 4z = 10$ 이라면 $2x + 0y + 4z = 10$으로 변경)

### 2.4 행렬의 크기 (Dimensions)

  * 만약 방정식이 M개이고 변수가 N개인 시스템이라면:
      * **계수 행렬**은 **M행 N열**이 됩니다. (M $\\times$ N)
      * **첨가 행렬**은 상수항 열이 하나 더 추가되므로 **M행 (N+1)열**이 됩니다. (M $\\times$ (N+1))

### 2.5 왜 행렬을 사용할까요?

행렬을 사용하면 **변수(x, y, z 등)를 일일이 쓸 필요 없이 숫자들(계수와 상수항)만으로 방정식 시스템을 간결하게 표현**할 수 있습니다. 변수의 이름은 중요하지 않기 때문에, 행렬은 복잡한 선형 방정식을 체계적으로 다룰 수 있는 강력한 도구입니다.

이제 행렬을 만들었으니, 이 행렬을 가지고 방정식을 푸는 방법을 배울 수 있습니다.

## 3. 행렬의 조작
-  출처: [Manipulating Matrices: Elementary Row Operations and Gauss-Jordan Elimination](https://www.youtube.com/watch?v=T2Gtt8WygiU&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=3)

### 3.1 **행렬을 사용하는 이유: 선형 방정식 풀기**

선형대수학에서 **행렬(Matrix)**은 여러 개의 선형 방정식을 한데 모아놓은 것입니다. 예를 들어, '4x + 6y = -2' 와 '5x - 3y = 8' 이라는 두 개의 방정식이 있다면, 이를 '첨가 행렬(Augmented matrix)'이라는 형태로 표현할 수 있습니다. 이 행렬은 숫자들로 이루어진 표와 같으며, 이 표를 조작하여 방정식의 해(즉, x, y, z와 같은 미지수의 값)를 찾는 것이 목표입니다. 행렬을 사용하는 주된 이유는 방정식을 더 쉽게 풀고, 해가 없는지, 하나만 있는지, 아니면 무수히 많은지 파악하기 위함입니다.

### 3.2 **기본 행 연산 (Elementary Row Operations)**

방정식 시스템의 해를 변경하지 않으면서 행렬을 조작하는 특정 규칙들이 있는데, 이를 **기본 행 연산**이라고 합니다. 이 연산들은 우리가 일반 대수학에서 방정식을 풀 때 사용했던 방법들과 본질적으로 같습니다.

다음 세 가지 주요 기본 행 연산이 있습니다:

*   **한 행을 0이 아닌 수로 곱하거나 나누기**:
    *   어떤 행이든 **0이 아닌** 원하는 숫자로 곱하거나 나눌 수 있습니다.
    *   예를 들어, 어떤 방정식 '4x + 6y = -2'를 모두 2로 나누면 '2x + 3y = -1'이 되는데, 이는 본질적으로 같은 방정식이며 해에 영향을 주지 않습니다.
    *   행렬에서도 마찬가지로 해당 행의 모든 숫자를 똑같이 조작하는 것입니다.
*   **두 행을 서로 더하거나 빼기**:
    *   시스템 내의 어떤 두 행이든 서로 더하거나 빼서 새로운 행을 만들 수 있습니다.
    *   또한, 한 행의 특정 배수를 다른 행에 더하거나 빼서 새로운 행을 만들 수도 있습니다.
    *   이것은 대수학에서 두 방정식을 더하거나 빼서 새로운 방정식을 만드는 것과 동일합니다. 이 방법을 사용하면 종종 미지수 중 하나를 없애는(즉, 계수를 0으로 만드는) 데 도움이 되어 방정식을 더 쉽게 만들 수 있습니다.
*   **두 행의 위치를 서로 바꾸기**:
    *   두 행의 순서를 서로 바꾸는 것도 가능합니다.
    *   이 역시 방정식 시스템의 해에 아무런 영향을 주지 않습니다.

이러한 기본 행 연산들은 **선형 방정식 시스템의 해를 변경하지 않으면서** 행렬을 원하는 형태로 변형할 수 있게 해줍니다.

### 3.3 **가우스-조르단 소거법 (Gauss-Jordan Elimination)**

**가우스-조르단 소거법**은 위에서 설명한 기본 행 연산을 사용하여 행렬을 특정 목표 형태(즉, **기약 행 사다리꼴**)로 변환하는 기술입니다. 이 방법을 통해 최종적으로 미지수의 값을 직접 얻을 수 있습니다.

**가우스-조르단 소거법의 목표**:
이 방법의 핵심은 각 행이 다음의 조건을 만족하도록 만드는 것입니다:
*   어떤 한 열에는 '1'이 있고,
*   나머지 열(가장 오른쪽 열 제외)에는 '0'이 있게 하여,
*   궁극적으로 각 행의 가장 오른쪽 열에서 해당 변수의 값을 바로 읽을 수 있도록 하는 것입니다.

예를 들어, '1 0 0 \| -9'와 같은 행을 얻었다면, 이는 '1x + 0y + 0z = -9'를 의미하므로, x의 값이 -9라는 것을 바로 알 수 있습니다. '0 1 0 \| 5'라면 y가 5라는 것을 의미하고, '0 0 1 \| 0'이라면 z가 0이라는 것을 의미합니다.

이러한 목표를 달성하기 위한 구체적인 형태를 **기약 행 사다리꼴(Reduced Row-Echelon Form)**이라고 부릅니다.

### 3.4 **기약 행 사다리꼴의 특징**:
행렬이 기약 행 사다리꼴이 되려면 다음 세 가지 조건을 만족해야 합니다:
1.  **선행 1 (Leading One)**: 0이 아닌 숫자를 포함하는 모든 행은 그 행의 첫 번째 0이 아닌 숫자로 '1'을 가져야 합니다. 이 '1'을 '선행 1'이라고 합니다.
2.  **열의 0**: 선행 1이 있는 열의 다른 모든 원소는 '0'이어야 합니다.
3.  **선행 1의 위치**: 위쪽에 있는 행의 선행 1은 그 아래 행의 선행 1보다 왼쪽에 있어야 합니다.

행렬이 이 형태에 도달하면 가우스-조르단 소거법이 완료된 것입니다.

### 3.5 **해의 개수 파악하기: 랭크 (Rank)**

가우스-조르단 소거법이 완료되어 행렬이 기약 행 사다리꼴이 되면, 시스템에 해가 몇 개 있는지 알 수 있습니다. 이를 위해 **랭크(Rank)**라는 개념을 사용합니다. **랭크**는 기약 행 사다리꼴에서 **선행 1의 개수**를 의미합니다.

랭크를 통해 해의 개수를 판단하는 방법은 다음과 같습니다:

*   **유일한 해 (Unique Solution)**:
    *   **랭크**가 **계수 행렬(coefficient matrix)**의 열(column)의 수와 같으면, 시스템은 **최대 하나의 해**를 가집니다. 일반적으로 이 경우 유일한 해가 존재합니다.
    *   계수 행렬은 첨가 행렬에서 가장 오른쪽 상수 열을 제외한 부분입니다.
*   **무수히 많은 해 (Infinitely Many Solutions) 또는 해 없음 (No Solution)**:
    *   **랭크**가 **계수 행렬**의 열의 수보다 작으면, 시스템은 **무수히 많은 해**를 가지거나 **해가 없습니다**.
    *   **특히 해가 없는 경우**: 만약 **계수 행렬의 랭크**가 **첨가 행렬의 랭크**보다 작으면, 시스템에는 해가 없습니다. (예를 들어, '0 0 0 \| 5'와 같은 형태의 행이 생기는 경우로, 이는 '0=5'라는 불가능한 상황을 의미합니다.)

결론적으로, 기본 행 연산을 통해 행렬을 조작하고 가우스-조르단 소거법을 적용하는 것은 대수학에서 방정식을 조작하는 것과 다르지 않지만, 훨씬 체계적이고 효율적으로 선형 방정식의 해를 찾을 수 있게 해줍니다.

## 4.행렬의 유형과 연산
- 출처: [Types of Matrices and Matrix Addition](https://www.youtube.com/watch?v=biyQzy2yRUA&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=4)

수학에서 **행렬**은 숫자를 사각형 형태로 배열한 것을 말하며, 흔히 **표**와 같다고 생각하면 이해하기 쉽습니다. 주로 연립방정식의 계수나 상수를 나타낼 때 사용해요.

### 4.1 행렬의 주요 용어

* **행(Rows)과 열(Columns)**: 
    *   행렬의 **가로줄은 행**, **세로줄은 열**이라고 부릅니다.
* **정방 행렬(Square Matrix)**: 
    *   **행의 개수와 열의 개수가 같은 행렬**을 정방 행렬이라고 합니다. 예를 들어, 2x2나 3x3 행렬이 여기에 해당해요.
* **주 대각선(Main Diagonal)**: 
    *   정방 행렬에서 **왼쪽 위부터 오른쪽 아래로 이어지는 대각선**에 있는 숫자들을 말합니다.
* **대각 행렬(Diagonal Matrix)**: 
    *   **주 대각선 상의 숫자를 제외한 모든 숫자가 0인 행렬**입니다.
* **단위 행렬(Identity Matrix)**: 
    *   대각 행렬 중에서도 **주 대각선에 있는 숫자가 모두 1이고 나머지는 모두 0인 정방 행렬**을 단위 행렬이라고 합니다. 이 행렬은 곱셈에서 숫자 '1'과 같은 역할을 해요.
* **상삼각 행렬(Upper Triangular Matrix)**: 
    *   정방 행렬에서 **주 대각선 아래의 모든 숫자가 0인 행렬**입니다.
* **하삼각 행렬(Lower Triangular Matrix)**: 
    *   정방 행렬에서 **주 대각선 위의 모든 숫자가 0인 행렬**입니다.
* **벡터(Vector)**:
    * **하나의 열로만 이루어진 행렬**을 **열 벡터(Column Vector)**라고 합니다.
    * **하나의 행으로만 이루어진 행렬**을 **행 벡터(Row Vector)**라고 합니다.
    벡터는 선형 방정식의 해를 나타낼 때 자주 사용됩니다.

### 4.2 행렬의 간단한 연산

행렬로 할 수 있는 기본적인 계산들을 알아볼게요.

* **스칼라 곱셈(Scalar Multiplication)**: 행렬에 **특정 숫자(스칼라)**를 곱하는 연산입니다. 이 숫자를 **행렬 안의 모든 숫자에 각각 곱해주면 됩니다**.
    * 예시: $$2 \times \begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix} = \begin{bmatrix} 2 \times 1 & 2 \times 2 \\ 2 \times 3 & 2 \times 4 \end{bmatrix}$$ = $$\begin{bmatrix} 2 & 4 \\ 6 & 8 \end{bmatrix}$$

* **행렬 덧셈(Matrix Addition)**: 두 행렬을 더하는 연산입니다.
    * **규칙**: 두 행렬의 **크기(행과 열의 개수)가 같아야 합니다.** 크기가 같은 두 행렬을 더할 때는 **같은 위치에 있는 숫자들끼리 더해주면 됩니다**.
    * **결과**: 새로운 행렬은 원래 행렬들과 동일한 크기를 가집니다.
    * 예시: $$\begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix} + \begin{bmatrix} 7 & 3 \\ 5 & 3 \end{bmatrix} = \begin{bmatrix} 1+7 & 2+3 \\ 3+5 & 4+3 \end{bmatrix} = \begin{bmatrix} 8 & 5 \\ 8 & 7 \end{bmatrix}$$
    * **교환법칙 성립**: 숫자의 덧셈처럼 행렬 덧셈도 $A + B = B + A$가 성립합니다.

* **행렬 뺄셈(Matrix Subtraction)**: 두 행렬을 빼는 연산입니다.
    * **규칙**: 덧셈과 마찬가지로 **두 행렬의 크기가 같아야 합니다.** 같은 위치에 있는 숫자들끼리 빼주면 된다.
    * **교환법칙 불성립**: 숫자의 뺄셈처럼 행렬 뺄셈도 $A - B \neq B - A$입니다.

## 5. 선형대수학: 행렬 곱셈, 쉽게 이해하기
- 출처: [Matrix Multiplication and Associated Properties](https://www.youtube.com/watch?v=P5GJJ02OG08&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=5)

### **5.1 행렬 곱셈의 기본 생각**

* **단순한 곱셈이 아니에요!**
    행렬 덧셈이나 뺄셈처럼 같은 위치에 있는 숫자끼리 단순히 곱하는 것이 아닙니다. 행렬 곱셈은 특별한 규칙, 즉 '알고리즘'에 따라 계산됩니다.

* **크기(차원)가 중요해요!**
    두 행렬을 곱하려면 특정한 크기 조건이 맞아야 합니다. 행렬 $A$와 행렬 $B$를 $A \times B$ 순서로 곱할 때, **행렬 $A$의 '열(세로 줄)' 개수**와 **행렬 $B$의 '행(가로 줄)' 개수**가 반드시 같아야 합니다. 이 조건은 실제 계산 방식을 보면 더 쉽게 이해할 수 있습니다.

### **5.2 행렬 곱셈, 이렇게 계산해요!**

* **결과 행렬의 각 칸 채우기:**
    곱셈의 결과로 만들어지는 새로운 행렬(이것을 '곱 행렬'이라고 부릅니다)의 각 칸에 들어갈 숫자를 찾아야 합니다. 이때, 첫 번째 행렬의 **'가로 줄(행)'**과 두 번째 행렬의 **'세로 줄(열)'**을 사용합니다.

* **각 칸의 숫자 계산 방법:**
    새로운 행렬의 특정 칸에 들어갈 숫자를 계산하려면, 다음 과정을 따릅니다:

    1.  **두 줄을 선택합니다:**
        첫 번째 행렬에서는 한 개의 **'행(가로 줄)'**을 고르고, 두 번째 행렬에서는 한 개의 **'열(세로 줄)'**을 고릅니다.

    2.  **짝지어 곱하고 더합니다:**
        선택한 행과 열의 **'대응하는 원소들'**을 각각 곱합니다. 예를 들어, 행의 첫 번째 숫자와 열의 첫 번째 숫자를 곱하고, 행의 두 번째 숫자와 열의 두 번째 숫자를 곱하는 식입니다. 이렇게 곱한 결과들을 모두 **더합니다.** 이 합이 바로 곱 행렬의 한 칸에 들어갈 숫자가 됩니다.

### 5.3 **예시를 통해 더 쉽게 이해하기:**

만약 첫 번째 행렬의 i번째 행과 두 번째 행렬의 j번째 열을 선택했다면,

첫 번째 행렬의 i번째 행: $[a_{i1}, a_{i2}, \dots, a_{in}]$
두 번째 행렬의 j번째 열: $$\begin{bmatrix} b_{1j} \\ b_{2j} \\ \vdots \\ b_{nj} \end{bmatrix}$$

이때, 곱 행렬의 (i, j) 위치의 원소는 다음과 같이 계산됩니다:

$a_{i1} \times b_{1j} + a_{i2} \times b_{2j} + \dots + a_{in} \times b_{nj}$

이처럼 행렬 곱셈은 단순히 숫자를 곱하는 것이 아니라, 정해진 규칙에 따라 행과 열의 원소들을 짝지어 곱하고 더하는 독특한 방식입니다.

## 6. 행렬식 구하는 방법
- 출처: [Evaluating the Determinant of a Matrix](https://www.youtube.com/watch?v=CcbyMH3Noow&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=6)

행렬식(Determinant)은 **정방 행렬(square matrix)**, 즉 가로와 세로의 크기가 같은 행렬(예: 2x2, 3x3)에만 적용되는 **특별한 숫자 값**입니다. 이 값은 행렬의 중요한 특징을 나타냅니다.


### 6.1 2x2 행렬의 행렬식 구하기
가장 간단한 형태인 2x2 행렬$$\begin{pmatrix}a & b  \\ c & d \end{pmatrix}$$의 행렬식은 다음과 같이 계산합니다.

* `ad - bc`
* 이것은 **왼쪽 위 숫자와 오른쪽 아래 숫자를 곱한 값**에서 **오른쪽 위 숫자와 왼쪽 아래 숫자를 곱한 값**을 빼는 것입니다.

**예시:**

만약 행렬이 $$\begin{pmatrix}2 & 1 \\ -6 & 4 \end{pmatrix}$$ 라면,
행렬식은 `(2 * 4) - (1 * -6) = 8 - (-6) = 14` 입니다.

### 6.2 3x3 행렬의 행렬식 구하기
3x3 행렬의 행렬식은 여러 개의 2x2 행렬식을 조합하여 구합니다.

* **첫 번째 행의 각 숫자를 기준**으로 계산합니다.
* 각 숫자에 해당하는 **행과 열을 제외한 나머지 숫자들로 2x2 행렬**을 만듭니다. 이 2x2 행렬의 행렬식(이를 '소행렬식'이라고 합니다)을 구합니다.
* 계산할 때 **부호를 번갈아 적용**해야 합니다. (첫 번째 숫자에는 +, 두 번째 숫자에는 -, 세 번째 숫자에는 +)

**예시:**

만약 행렬이 $$\begin{pmatrix}a1 & a2 & a3 \\ b1 & b2 & b3  \\ c1 & c2 & c3 \end{pmatrix}$$ 라면,
행렬식은 `a1 * (b2*c3 - b3*c2) - a2 * (b1*c3 - b3*c1) + a3 * (b1*c2 - b2*c1)` 입니다.

**구체적인 예시:**

행렬이 $$\begin{pmatrix}1 & 2 & -1 \\ 3 & 0 & 1 \\ -5 & 4 & 2 \end{pmatrix}$$ 라면,

1.  **첫 번째 숫자 `1`:** `1`이 있는 행과 열을 제외하면 `0 1` / `4 2` 가 남습니다.
    * 이 2x2 행렬식: `(0 * 2) - (1 * 4) = -4`
2.  **두 번째 숫자 `2`:** `2`가 있는 행과 열을 제외하면 `3 1` / `-5 2` 가 남습니다.
    * 이 2x2 행렬식: `(3 * 2) - (1 * -5) = 6 - (-5) = 11`
3.  **세 번째 숫자 `-1`:** `-1`이 있는 행과 열을 제외하면 `3 0` / `-5 4` 가 남습니다.
    * 이 2x2 행렬식: `(3 * 4) - (0 * -5) = 12 - 0 = 12`

최종 계산은 `(1 * -4) - (2 * 11) + (-1 * 12) = -4 - 22 - 12 = -38` 입니다.

### 6.3 더 큰 행렬(예: 4x4)의 행렬식 구하기
행렬의 크기가 커져도 기본적인 계산 원리는 같습니다.

* 4x4 행렬의 경우, 첫 번째 행의 각 숫자를 기준으로 해당 행과 열을 제외하면 **3x3 행렬**이 남습니다.
* 각 3x3 행렬의 행렬식을 구한 뒤, 위에서 설명한 대로 **`+ - + -` 부호 규칙**을 적용하여 더하고 뺍니다.
* 이처럼 큰 행렬의 행렬식을 구하려면 여러 번의 계산 단계를 거쳐야 하므로, **정확하게 계산하고 과정을 체계적으로 정리하는 것이 중요**합니다.

## 7. 벡터의 외적(Cross Product)
- 출처: [The Vector Cross Product](https://www.youtube.com/watch?v=gPnWm-IXoAY&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=7)

벡터의 외적은 두 벡터를 가지고 **새로운 벡터를 만들어내는 계산 방법**입니다. 우리가 흔히 아는 곱셈과는 다르게, 결과가 '숫자'가 아닌 '또 다른 벡터'가 된다는 점이 중요합니다.

### **7.1 외적이 뭐예요?**

* 두 벡터를 곱해서 **새로운 벡터**를 만들어냅니다.
* 숫자(스칼라)를 만들어내는 내적(Dot Product)과는 다릅니다.
* '×' 기호로 표시합니다. (예: A × B)

### **7.2 외적은 어떻게 계산하나요?**

* 두 벡터 A와 B가 있을 때, 이들의 외적 (A × B)은 마치 **3x3 행렬의 복잡한 계산**처럼 구할 수 있습니다.
* 첫 줄에는 방향을 나타내는 I, J, K를 쓰고, 그 밑에 벡터 A와 B의 각 성분들을 채워 넣어 계산합니다.
* **예시:** 벡터 A (1, 3, 4)와 B (2, 7, -5)를 외적하면 **-43I + 13J + K**라는 새로운 벡터가 나옵니다.

### **7.3 외적 계산 방법**
외적은 **행렬식**을 활용해 계산됩니다. 두 벡터 $\mathbf{A} = (A_x, A_y, A_z) $ 와 $ \mathbf{B} = (B_x, B_y, B_z) $가 주어졌을 때, 외적 $\mathbf{A} \times \mathbf{B}$는 다음과 같이 구합니다:

1. **행렬식 표현**:
   외적은 3x3 행렬의 행렬식을 이용해 계산합니다. 행렬은 다음과 같이 구성됩니다:
   $$
   \begin{vmatrix}
   \mathbf{i} & \mathbf{j} & \mathbf{k} \\
   A_x & A_y & A_z \\
   B_x & B_y & B_z \\
   \end{vmatrix}
   $$
   여기서 $ \mathbf{i}, \mathbf{j}, \mathbf{k} $는 각각 $ x, y, z $ 축 방향의 단위 벡터.

2. **계산 공식**:
   행렬식을 전개하면 외적은 다음과 같은 벡터로 표현됩니다:
   $
   \mathbf{A} \times \mathbf{B} = \left( A_y B_z - A_z B_y \right) \mathbf{i} - \left( A_x B_z - A_z B_x \right) \mathbf{j} + \left( A_x B_y - A_y B_x \right) \mathbf{k}
   $

### **7.4 예시 계산:**
 $ \mathbf{A} = (1, 3, 4) $, $ \mathbf{B} = (2, 7, -5) $
주어진 예시를 통해 외적을 계산해 보겠습니다.

1. **행렬 구성**:
   $$
   \begin{vmatrix}
   \mathbf{i} & \mathbf{j} & \mathbf{k} \\
   1 & 3 & 4 \\
   2 & 7 & -5 \\
   \end{vmatrix}
   $$

2. **행렬식 전개**:
   - $ \mathbf{i} $ 성분: $ (A_y B_z - A_z B_y) = (3 \cdot (-5) - 4 \cdot 7) = (-15 - 28) = -43 $
   - $ \mathbf{j} $ 성분: $ -\left( A_x B_z - A_z B_x \right) = -\left( 1 \cdot (-5) - 4 \cdot 2 \right) = -(-5 - 8) = 13 $
   - $ \mathbf{k} $ 성분: $ (A_x B_y - A_y B_x) = (1 \cdot 7 - 3 \cdot 2) = (7 - 6) = 1 $

3. **결과**:
   $$
   \mathbf{A} \times \mathbf{B} = -43\mathbf{i} + 13\mathbf{j} + 1\mathbf{k}
   $$
   즉, 외적 결과는 벡터 $ (-43, 13, 1) $입니다.

4. **추가 설명**
- **외적의 성질**:
  - 결과 벡터는 $ \mathbf{A} $와 $ \mathbf{B} $ 모두에 수직입니다.
  - 외적의 크기 $ \mathbf{A} \times \mathbf{B} $는 두 벡터가 이루는 평행사변형의 넓이를 나타냅니다.
  - 외적은 **교환법칙이 성립하지 않습니다**: $ \mathbf{A} \times \mathbf{B} = -\mathbf{B} \times \mathbf{A} $.

- **확인**:
  결과 벡터 $ (-43, 13, 1) $가 $ \mathbf{A} $와 $ \mathbf{B} $에 수직인지 확인하려면 내적(inner product)을 계산합니다:
  - $ (-43, 13, 1) \cdot (1, 3, 4) = (-43 \cdot 1) + (13 \cdot 3) + (1 \cdot 4) = -43 + 39 + 4 = 0 $
  - $ (-43, 13, 1) \cdot (2, 7, -5) = (-43 \cdot 2) + (13 \cdot 7) + (1 \cdot (-5)) = -86 + 91 - 5 = 0 $
  두 내적이 0이므로, 결과 벡터는 $ \mathbf{A} $와 $ \mathbf{B} $ 모두에 수직입니다.

### **7.5 외적 결과 벡터는 어디를 향하나요?**

* 외적의 결과로 나온 벡터는 항상 **원래 두 벡터 모두에 대해 90도로 수직**입니다.
* 이것은 원래 두 벡터가 만드는 평면에 대해 수직이라는 의미입니다.
* 방향은 **'오른손 법칙'**으로 쉽게 알 수 있습니다. 오른손을 펴서 첫 번째 벡터(예: B)에 대고 손가락을 두 번째 벡터(예: A) 방향으로 구부리면, 엄지손가락이 가리키는 곳이 바로 외적 벡터의 방향입니다.

### **7.6 외적 결과 벡터의 크기는 얼마인가요?**

* 외적 벡터 (A × B)의 크기는 **\|A\| × \|B\| × sinθ** 로 계산합니다. (여기서 θ는 두 벡터 사이의 각도입니다.)
* 이 크기는 또한 **두 벡터 A와 B가 이루는 평행사변형의 넓이**와 같습니다.

### **7.7 외적의 특별한 경우**

* **자기 자신과의 외적:** 어떤 벡터든 자기 자신과 외적하면 항상 **0**이 됩니다. (A × A = 0)
* **평행한 벡터의 외적:** 서로 평행한 두 벡터를 외적하면 항상 **0**이 됩니다. 이는 두 벡터 사이의 각도가 0도이거나 180도일 때 sin값이 0이 되기 때문입니다. 또한, 평행한 벡터는 넓이가 0인 평행사변형을 만든다고 생각할 수 있습니다.

### **7.8 외적의 수학적 성질**

* **순서 바꾸면 부호 바뀜 (비교환적):** A × B는 B × A와 같지 않고, 대신 **A × B = - (B × A)** 입니다.
* **세 개 이상은 순서대로 계산해야 함 (비결합적):** (A × B) × C 와 A × (B × C)는 일반적으로 다릅니다.
* **분배 법칙은 성립:** A × (B + C) = (A × B) + (A × C)

### **7.9 외적은 어디에 쓰이나요?**

* 외적은 **물리학**에서 힘, 토크, 자기장 등 다양한 개념을 설명할 때 아주 중요하게 사용됩니다.

## 8. 역행렬, 대체 뭔가요?
- 출처: [Inverse Matrices and Their Properties](https://www.youtube.com/watch?v=kWorj5BBy9k&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=8)

역행렬은 일반적인 숫자에서 '역수' 개념과 비슷하지만, 행렬에는 나눗셈이 없기 때문에 조금 다르게 접근해야 해요. 어떤 행렬 $A$의 역행렬은 $A^{-1}$로 표시하는데, 이 둘을 곱하면 특별한 행렬인 **항등 행렬($I$)**이 됩니다. 항등 행렬은 마치 숫자 '1'처럼 어떤 행렬에 곱해도 그 행렬이 그대로 나오게 하는 역할을 해요.

* $A \times A^{-1} = I$
* $A^{-1} \times A = I$

### 8.1 2x2 행렬의 역행렬은 어떻게 구하나요?

가장 간단한 2x2 행렬 $$\begin{pmatrix} a & b \\ c & d \end{pmatrix}$$의 역행렬은 다음 세 단계를 거쳐 쉽게 구할 수 있어요.

1.  **결정자(Determinant) 계산**: $ad - bc$를 계산합니다. 이 값이 나중에 '나누는' 역할을 해요.
2.  **행렬 변형**: 원래 행렬에서 $a$와 $d$의 위치를 바꾸고, $b$와 $c$의 부호를 반대로 만듭니다. $$\begin{pmatrix} d & -b \\ -c & a \end{pmatrix}$$
3.  **결정자의 역수 곱하기**: 2단계에서 만든 행렬에 1단계에서 구한 결정자의 역수($\frac{1}{ad-bc}$)를 곱하면 끝!

**예시**: 행렬 $$\begin{pmatrix} 4 & 3 \\ 3 & 2 \end{pmatrix}$$의 역행렬을 구해볼까요?

1.  **결정자**: $(4 \times 2) - (3 \times 3) = 8 - 9 = -1$
2.  **행렬 변형**: $$\begin{pmatrix} 2 & -3 \\ -3 & 4 \end{pmatrix}$$
3.  **역행렬**: $$\frac{1}{-1} \times \begin{pmatrix} 2 & -3 \\ -3 & 4 \end{pmatrix} = \begin{pmatrix} -2 & 3 \\ 3 & -4 \end{pmatrix}$$

이렇게 구한 역행렬을 원래 행렬과 곱하면 항등 행렬이 나온답니다!

### 8.2 역행렬은 어디에 쓰이나요?

역행렬은 **행렬 방정식**을 풀 때 유용하게 사용됩니다. 행렬에는 나눗셈 개념이 없기 때문에, $X \times A = B$ 같은 방정식을 풀 때는 양변에 $A$의 역행렬을 곱해서 $X$를 구할 수 있어요.

* $X \times A \times A^{-1} = B \times A^{-1}$
* $X \times I = B \times A^{-1}$
* $X = B \times A^{-1}$

**중요한 점**: 행렬 곱셈은 순서가 중요해요. $A \times X = B$라면 $A^{-1}$를 왼쪽에 곱해야 $X = A^{-1} \times B$가 됩니다. $(B \times A)^{-1}$의 역행렬은 $A^{-1} \times B^{-1}$ 순서로 적용됩니다.

### 8.3 역행렬이 없는 행렬도 있나요? (특이 행렬)

네, 모든 행렬이 역행렬을 가지는 것은 아니에요. 행렬의 **결정자가 0인 경우**에는 역행렬을 구할 수 없습니다. 왜냐하면 0으로 나누는 것은 수학적으로 불가능하기 때문이죠. 이렇게 결정자가 0인 행렬을 **특이 행렬(Singular Matrix)**이라고 부릅니다.

### 8.4 3x3 이상의 행렬 역행렬은 어떻게 구하나요?

2x2 행렬과 달리 3x3 이상의 행렬은 역행렬을 구하는 과정이 훨씬 복잡해요. 네 단계를 거쳐야 하는데, 간단히 설명하면 이렇습니다.

1.  **소행렬식 행렬 구하기**: 각 원소를 제외한 나머지 원소들로 2x2 행렬의 결정자를 계산해서 새로운 행렬을 만들어요.
2.  **여인자 행렬 구하기**: 1단계에서 만든 행렬에 체스판처럼 부호(+, -, +, -, +, -, ...)를 붙여서 원소의 부호를 조정합니다.
3.  **딸림 행렬 구하기**: 2단계에서 만든 행렬의 행과 열을 바꿉니다 (전치).
4.  **역행렬 계산**: 마지막으로 원래 3x3 행렬의 결정자를 계산한 다음, 3단계에서 만든 딸림 행렬의 각 원소를 이 결정자 값으로 나눕니다.

이 과정은 손으로 계산하기에는 매우 번거롭고 시간이 많이 걸리기 때문에, 보통 **행렬 계산기**를 사용해서 구하는 것이 일반적입니다. 4x4 이상의 행렬은 훨씬 더 복잡해져서 더욱 그렇답니다.

## 9. 크라머 공식이란?
- 출처: [Solving Systems Using Cramer's Rule](https://www.youtube.com/watch?v=RdLo-9jh2EM&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=9)

### **크라머 공식이란?**
크라머 공식은 여러 개의 방정식을 풀 때, 숫자들을 행렬(표 같은 형태)로 정리해서 각 변수의 값을 찾는 방법이에요. 행렬의 "행렬식(determinant)"이라는 특별한 값을 계산해서 답을 구합니다. 이 방법은 다른 방법(예: 대입법이나 소거법)보다 복잡한 방정식 시스템에서도 체계적으로 답을 찾을 수 있어요.

### 9.1 **어떻게 사용하는 걸까?**
크라머 공식을 사용하려면 아래 단계를 따라가면 됩니다. 예를 들어, 두 개의 방정식이 있다고 해볼게요:
$$
2x + 3y = 8
$$
<br>
$$
4x + y = 7
$$

1. **계수 행렬 만들기**
- 방정식에서 변수(x, y) 앞에 붙은 숫자(계수)만 모아서 행렬을 만듭니다.
- 위 방정식에서는:
  - x의 계수: 2와 4
  - y의 계수: 3과 1
- 계수 행렬은 이렇게 생겨요:$$
  \begin{bmatrix}
  2 & 3 \\
  4 & 1
  \end{bmatrix}$$
- 이 행렬의 **행렬식**을 계산합니다. 행렬식이란 행렬을 하나의 숫자로 바꿔주는 방법이에요. 2x2 행렬의 행렬식은:
  $(첫 번째 대각선 곱) - (두 번째 대각선 곱) = (2 \times 1) - (3 \times 4) = 2 - 12 = -10$  행렬식은 -10 이에요.

2. 각 변수를 위한 행렬 만들기
- x를 구하려면, 계수 행렬의 x 계수 열(첫 번째 열)을 방정식의 오른쪽 값(상수항, 즉 8과 7)으로  변경:
  $$
  \begin{bmatrix}
  8 & 3 \\
  7 & 1
  \end{bmatrix}
  $$
- 이 행렬의 행렬식을 계산해요:
  $$
  (8 \times 1) - (3 \times 7) = 8 - 21 = -13
$$
- y를 구하려면, 계수 행렬의 y 계수 열(두 번째 열)을 상수항으로 바꿉니다:
  $$
  \begin{bmatrix}
  2 & 8 \\
  4 & 7
  \end{bmatrix}
  $$
- 이 행렬의 행렬식은:
  $$
  (2 \times 7) - (8 \times 4) = 14 - 32 = -18
  $$
3. **변수 값 계산하기**
- x는 x를 위한 행렬의 행렬식을 계수 행렬의 행렬식으로 나눕니다:
  \[
  x = \frac{-13}{-10} = 1.3
  \]
- y는 y를 위한 행렬의 행렬식을 계수 행렬의 행렬식으로 나눕니다:
  \[
  y = \frac{-18}{-10} = 1.8
  \]
- 그러니까 해는 x = 1.3, y = 1.8이에요.

### 9.2 **더 복잡한 경우 (3x3 행렬)**
만약 방정식이 세 개이고 변수도 세 개(x, y, z)라면, 같은 방식으로 하면 됩니다. 예를 들어:
\[
x + y + z = 6
\]
\[
2x - y + 3z = 9
\]
\[
-x + 2y - z = 1
\]
- 계수 행렬은:
  $$
  \begin{bmatrix}
  1 & 1 & 1 \\
  2 & -1 & 3 \\
  -1 & 2 & -1
  \end{bmatrix}
  $$
- 이 행렬의 행렬식을 계산하고(좀 복잡하지만, 예시에서는 12라고 가정), 각 변수(x, y, z)를 구하기 위해 열을 상수항(6, 9, 1)으로 바꿔 새로운 행렬식을 구한 뒤 나눠줍니다.
- 결과적으로 x = 2, y = 1, z = -4 같은 식으로 답을 찾을 수 있어요.

### 9.3 **크라머 공식의 장점과 한계**
- **장점**: 복잡한 방정식 시스템(예: 변수 10개!)에서도 같은 방식으로 풀 수 있어요. 단계가 명확해서 컴퓨터로 계산하기도 좋아요.
- **한계**: 계수 행렬의 행렬식이 0이면 나눌 수 없어서 이 방법은 못 써요. 이런 경우는 방정식이 해가 없거나 무수히 많은 경우예요.


## 10. 벡터 공간
- 출처: [Understanding Vector Spaces](https://www.youtube.com/watch?v=EP2ghkO0lSk&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=10)

벡터 공간(Vector Space)은 수학에서 벡터와 행렬을 다룰 때 사용하는 개념으로, 쉽게 말해 **특정한 규칙을 따르는 숫자나 객체의 집합**이라고 생각하면 됩니다. 이 규칙들은 벡터 공간이 잘 작동하도록 도와주는 기본 원칙이에요. 입문자도 이해할 수 있게 간단히 설명해볼게요!


### **10.1 벡터 공간이란?**
벡터 공간은 **벡터(숫자 리스트 같은 것)**들이 모여 있는 집합이에요. 이 벡터들은 서로 더하거나, 숫자(스칼라)로 곱할 수 있는데, 그 결과도 여전히 그 집합 안에 있어야 해요. 이 집합이 벡터 공간이 되려면 몇 가지 중요한 규칙을 따라야 합니다.

### **10.2 벡터 공간의 필수 규칙**
벡터 공간이 되려면 아래와 같은 규칙들이 필요해요. 이 규칙들은 벡터들이 서로 잘 어울리도록 만드는 "게임 규칙" 같은 거예요.

1. **덧셈 규칙**
- **순서 바꿔도 OK**: 
  -   두 벡터 `a`와 `b`를 더할 때, `a + b`나 `b + a`나 결과가 똑같아요. (예: 2 + 3 = 3 + 2)
- **묶는 순서 바꿔도 OK**: 
  -   세 벡터 `a`, `b`, `c`를 더할 때, `(a + b) + c`나 `a + (b + c)`나 결과가 같아요. (예: (2 + 3) + 4 = 2 + (3 + 4))

2. **영 벡터**
- 벡터 공간에는 **영 벡터(0)**가 있어야 해요. 이 영 벡터는 어떤 벡터 `a`와 더해도 `a`를 그대로 유지해요. (예: `a + 0 = a`)

3. **반대 벡터**
- 모든 벡터 `a`에는 반대 벡터 `-a`가 있어서, 둘을 더하면 영 벡터가 나와요. (예: `a + (-a) = 0`)

4. **스칼라 곱셈 규칙**
- **스칼라 분배**: 
  - 숫자(스칼라) `c`를 두 벡터 `a`와 `b`의 합에 곱하면, 각각 곱한 결과를 더한 것과 같아요. (예: `c(a + b) = ca + cb`)
- **스칼라 덧셈 분배**: 
  - 두 숫자 `c`와 `d`를 벡터 `a`에 곱할 때, `(c + d)a = ca + da`가 성립해요.
- **곱셈 순서 상관없음**: 
  - 두 숫자 `c`와 `d`를 벡터 `a`에 곱할 때, `c(da) = (cd)a`로 결과가 같아요.
- **1을 곱하면 그대로**: 
  - 숫자 1을 벡터 `a`에 곱하면 `a`가 그대로 나와요. (예: `1a = a`)

### **10.3 폐쇄성: 벡터 공간의 핵심**
벡터 공간에서 가장 중요한 규칙은 **폐쇄성**이에요. 이건 마치 "집합 안에서만 놀아야 한다"는 규칙이에요.
* **덧셈 폐쇄성**: 
    *   집합 안의 두 벡터 `a`와 `b`를 더하면, 그 결과도 반드시 그 집합 안에 있어야 해요.
* **스칼라 곱셈 폐쇄성**: 
    *   집합 안의 벡터 `a`에 숫자를 곱해도, 그 결과가 집합 안에 있어야 해요.

이 폐쇄성이 없으면 그 집합은 벡터 공간이 될 수 없어요.

### **10.4 벡터 공간의 예시**
벡터 공간이 어떻게 생겼는지 몇 가지 예를 통해 알아볼게요.

- **실수 집합 (R)**: 
    *   모든 실수(1, 2.5, -7 등)를 모은 집합이에요. 두 실수를 더하거나, 실수에 숫자를 곱해도 결과는 여전히 실수라서 벡터 공간이에요.
- **3차원 벡터 (R³)**: 
    *   `[1, 2, 3]` 같은 3개의 숫자로 이루어진 벡터 집합이에요. 두 벡터를 더하거나 숫자를 곱해도 결과는 3개의 숫자로 된 벡터라서 벡터 공간이에요.
- **행렬 (R^(m x n))**: 
    *   같은 크기의 행렬들을 모은 집합이에요. 행렬을 더하거나 숫자를 곱해도 크기가 변하지 않으므로 벡터 공간이에요.
- **선형 다항식 (ax + b)**: 
    *   `2x + 3` 같은 함수들의 집합이에요. 이런 함수들을 더하거나 숫자를 곱해도 여전히 같은 형태의 함수가 나오므로 벡터 공간이에요.

### **10.5 벡터 공간이 아닌 경우**
어떤 집합은 벡터 공간이 될 수 없어요. 예를 들어:
- 두 번째 값이 항상 2인 벡터 집합 `{[x, 2]}`를 생각해봐요.
  - 예: `[3, 2]`와 `[5, 2]`를 더하면 `[3+5, 2+2] = [8, 4]`가 나와요.
  - 하지만 `[8, 4]`는 두 번째 값이 2가 아니므로 이 집합에 속하지 않아요.
  - 이 집합은 덧셈에 대해 **폐쇄적이지 않으므로** 벡터 공간이 아니에요.

### **10.6 요약**
- 벡터 공간은 벡터(숫자 리스트, 행렬, 함수 등)를 모은 집합이에요.
- 덧셈과 스칼라 곱셈에 대해 특정 규칙(교환, 결합, 영 벡터, 역 벡터 등)을 따라야 해요.
- 가장 중요한 건 **폐쇄성**: 더하거나 곱해도 결과가 집합 안에 있어야 해요.
- 실수, 벡터, 행렬, 선형 함수 등은 벡터 공간의 좋은 예이고, 폐쇄성이 깨지면 벡터 공간이 될 수 없어요.


## 11. 부분 공간(Subspace)이란?
- 출처: [Subspaces and Span](https://www.youtube.com/watch?v=tM4TDL9Hj8U&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=11)

### **11.1 부분 공간(Subspace)이란?**
부분 공간은 **벡터 공간**이라는 커다란 집 안에서, 그 자체로도 작은 집 역할을 할 수 있는 특별한 방 같은 거예요. 벡터 공간은 수학에서 벡터(화살표 같은 것)들이 모여 있는 공간인데, 부분 공간은 그 공간 안에서 **특정 조건을 만족하는 벡터들만 모인 작은 공간**이에요. 이 작은 공간도 벡터 공간의 규칙을 따라야 해요.

#### **11.2 어떤 조건?**
부분 공간이 되려면 두 가지 규칙을 지켜야 해요:
1. **스칼라 곱셈에 닫혀 있어야 한다**: 
*   어떤 벡터를 숫자(스칼라)로 곱해도 그 결과가 여전히 그 작은 공간 안에 있어야 해요.
2. **벡터 덧셈에 닫혀 있어야 한다**: 
*   그 작은 공간 안에 있는 두 벡터를 더해도 결과가 그 공간 안에 있어야 해요.

3. **예시로 이해하기**
- **예를 들어**, 3차원 공간(R3, 즉 x, y, z 좌표로 이루어진 공간)을 생각해봐요. 이 공간에서 `(x, 0, -x)` 형태의 벡터들만 모은 집합 S를 살펴볼게요. 예: `(3, 0, -3)`, `(5, 0, -5)` 같은 벡터들요.
- **스칼라 곱셈 확인**: `(3, 0, -3)`에 숫자 2를 곱하면 `(6, 0, -6)`이 되죠. 이건 여전히 `(x, 0, -x)` 형태니까 S 안에 있어요. 
- **벡터 덧셈 확인**: `(3, 0, -3)`과 `(5, 0, -5)`를 더하면 `(8, 0, -8)`이 되죠. 이것도 `(x, 0, -x)` 형태니까 S 안에 있어요. 

그래서 S는 부분 공간이에요!

### **11.3 선형 결합(Linear Combination)이란?**
선형 결합은 벡터들을 가지고 **레고 블록을 쌓듯이 조합하는 방법**이에요. 여러 벡터(V1, V2, ...)에 각각 숫자(스칼라, A1, A2, ...)를 곱해서 더하면 새로운 벡터를 만들 수 있어요. 이 과정을 선형 결합이라고 불러요.

예: 벡터 `(1, 0)`와 `(0, 1)`이 있다면, 선형 결합은 `3*(1, 0) + 2*(0, 1) = (3, 2)` 같은 식으로 계산.

### **11.4. 생성(Span)이란?**
생성은 **특정 벡터들로 만들 수 있는 모든 가능한 벡터들의 모음**이에요. 위에서 말한 선형 결합을 이용해서 특정 벡터들로 만들 수 있는 모든 결과물을 모은 거죠.

1. **예시로 이해하기**
3차원 공간에서 세 벡터 `(2, 1, -1)`, `(0, 2, 2)`, `(-1, -1, -1)`이 있다고 해봐요. 이 벡터들의 선형 결합, 즉 `A*(2, 1, -1) + B*(0, 2, 2) + C*(-1, -1, -1)`로 만들 수 있는 모든 벡터를 모으면 그게 바로 **생성**이에요. 계산해보면 이 생성은 `(2A-C, A+2B-C, -A+2B-C)` 형태의 벡터들로 나타나요.

2. **중요한 점**
- 이 생성은 항상 부분 공간이에요. 왜냐? 선형 결합으로 만든 벡터들은 스칼라 곱셈과 벡터 덧셈에 대해 닫혀 있거든요!
- 이 생성은 그 벡터들을 포함하는 **가장 작은 부분 공간**이에요. 즉, 이 벡터들을 포함하는 어떤 부분 공간이든 이 생성을 포함해야 해요.
