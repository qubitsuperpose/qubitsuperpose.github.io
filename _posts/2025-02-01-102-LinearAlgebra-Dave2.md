---
title: 1차시 2:Linear Algebra(Dave2)
layout: single
classes: wide
categories:
  - Linear Algebra
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---

## 12. 선형 독립과 선형 종속, 왜 중요할까요?
- 출처:[Linear Independence](https://www.youtube.com/watch?v=9kDpbZCK62Y&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=12)

선형 독립과 선형 종속은 어렵게 들릴 수 있지만, 쉽게 말해 **"주어진 정보 중에서 쓸모없는 중복을 없애고 가장 핵심적인 정보만 남기는 방법"**이라고 생각할 수 있습니다. 예를 들어, 어떤 물건을 표현할 때 "빨간색이고 크다"고 말했는데, "빨간색이고 크며, 눈에 잘 띈다"고 말하는 건 '눈에 잘 띈다'는 말이 이미 '빨간색이고 크다'는 정보에 포함될 수 있다면 중복이겠죠? 선형 독립/종속은 이 중복을 가려내는 개념입니다.

### 12.1 선형 종속 (Linearly Dependent): "겹치는 정보가 있어요!"

**선형 종속**은 한 벡터(정보 덩어리)가 다른 벡터들의 조합으로 표현될 수 있을 때를 말합니다. 쉽게 말해, **새로운 정보가 아니라 이미 있는 정보들을 섞어서 만들 수 있는 정보**라는 뜻이죠.

* **예시:** 벡터 C가 벡터 A와 벡터 B를 섞어서 (예: C = A + 2B) 만들 수 있다면, C는 A와 B에 종속적입니다. C는 없어도 A와 B만으로 충분히 표현할 수 있으니 "불필요한 정보"가 될 수 있습니다.

### 12.2 선형 독립 (Linearly Independent): "모두 다른 독자적인 정보예요!"

**선형 독립**은 어떤 벡터도 다른 벡터들의 조합으로 표현될 수 없을 때를 말합니다. 즉, **모든 벡터가 고유한 정보를 가지고 있어서 그 어떤 벡터도 다른 벡터들로는 만들어낼 수 없다**는 의미입니다. 이렇게 선형 독립인 벡터들만 모아야 가장 효율적으로 정보를 표현할 수 있습니다.

### 12.3 선형 독립/종속은 어떻게 판단하나요?

몇 가지 방법으로 벡터들이 선형 독립인지 선형 종속인지 확인할 수 있습니다.

1. 스칼라 방정식 풀기: 가장 기본 \
주어진 벡터들 ($V_1, V_2, ..., V_n$)에 각각 숫자들($C_1, C_2, ..., C_n$)을 곱해서 모두 더했을 때 0이 되는 방정식을 세웁니다.
$$C_1V_1 + C_2V_2 + ... + C_nV_n = 0$$
* **선형 독립:** 이 방정식을 만족하는 숫자들이 **모두 0일 때만** 가능하다면, 이 벡터들은 **선형 독립**입니다. 즉, 어떤 벡터도 다른 벡터들의 조합으로 0을 만들 수 없다는 뜻이죠.
* **선형 종속:** 만약 숫자들 중에 **0이 아닌 것이 하나라도 있는데도** 이 방정식이 성립한다면, 이 벡터들은 **선형 종속**입니다. 0이 아닌 숫자가 있다는 건 그 벡터를 다른 벡터들로 표현할 수 있다는 의미.

2. 연립방정식으로 생각하고 풀기 \
위의 스칼라 방정식을 푸는 것은 사실 미지수(숫자 $C_1$부터 $C_n$)가 있는 연립방정식을 푸는 것과 같습니다.
* **간단한 경우:** 대입하거나 소거하는 방법으로 풀 수 있습니다.
* **복잡한 경우:** 벡터들을 숫자로 나열해서 **행렬**이라는 것을 만들고, 이 행렬을 간단하게 만드는 "**행 소거(Row Reduction)**"라는 작업을 통해 답을 찾습니다.
    * **자유 변수(Free Variable)의 등장:** 만약 연립방정식을 풀었을 때, 어떤 미지수($C_i$)가 특정 값으로 정해지지 않고 아무 값이나 가질 수 있게 된다면(이것을 **자유 변수**라고 합니다), 그 벡터들은 **선형 종속**입니다. '자유롭다'는 것은 곧 그 벡터가 다른 벡터들의 조합으로 표현될 수 있다는 의미이기 때문이죠. 모든 $C_i$가 오직 0으로만 결정되어야 선형 독립입니다.

3. 행렬식(Determinant) 이용하기: 특별한 경우에만 \
이 방법은 **정방 행렬(가로줄과 세로줄의 개수가 같은 행렬)**일 때만 사용할 수 있습니다.
* 벡터들로 행렬을 만들었을 때, 그 **행렬식이 0이면** 벡터들은 **선형 종속**입니다.
* **행렬식이 0이 아니면** 벡터들은 **선형 독립**입니다.

### 12.4 벡터뿐만 아니라 다른 곳에도 적용될 수 있어요!
*   선형 독립/종속의 개념은 단순히 화살표 같은 **벡터**에만 적용되는 것이 아닙니다. **다항식**이나 **함수**처럼 다른 형태의 "정보 덩어리"에도 똑같이 적용할 수 있습니다. 예를 들어, 다항식의 경우 각 항의 계수들을 연립방정식으로 만들어 풀어서 선형 독립인지 종속인지 판단할 수 있습니다.

### 12.5 왜 이런 추상적인 개념을 배우는 걸까요?
*   선형 독립과 같은 개념들은 처음엔 좀 추상적으로 느껴질 수 있습니다. 하지만 이 개념을 이해하면 복잡해 보이는 함수, 벡터, 행렬 등 다양한 수학적 대상을 **하나의 통일된 도구**로 분석할 수 있게 됩니다. "이 정보 덩어리가 다른 정보 덩어리들로 표현될 수 있는가?"라는 질문에 답할 수 있게 해주어, 복잡한 문제들을 간단하고 효율적으로 해결하는 데 아주 유용하게 사용됩니다. 이는 고급 수학과 과학 분야에서 매우 중요한 기본기입니다.

## 13. 기저와 차원
- 출처: [Basis and Dimension](https://www.youtube.com/watch?v=4C9GKyfUQkc&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=13)

선형대수학의 **기저(Basis)**와 **차원(Dimension)**은 언뜻 복잡해 보일 수 있지만, 쉽게 말하면 벡터 공간을 이해하는 데 아주 중요한 기준점이라고 생각할 수 있어요.

### **13.1 기저(Basis)**: 벡터 공간의 기본 건축 블록

*   우리가 어떤 건물을 짓는다고 상상해볼게요. 이 건물을 짓기 위해서는 벽돌이나 나무 같은 기본적인 건축 재료들이 필요하죠? 이 재료들이 바로 벡터 공간에서 **기저**와 같은 역할을 합니다.

기저는 다음 두 가지 조건을 만족하는 벡터들의 모음이에요.

1.  **독립성 (선형 독립):** 
    *   이 건축 재료들이 서로 겹치거나 불필요하게 많지 않다는 뜻이에요. 예를 들어, 이미 벽돌로 만들 수 있는 것을 또 다른 '특별한 벽돌'이라고 부르지 않는 거죠. 수학적으로는, 기저 벡터들을 적절히 늘리거나 줄여서 더한 결과가 0이 되려면, 각각의 벡터에 곱하는 숫자(스칼라)들이 모두 0이어야만 한다는 의미.
2.  **생성력 (Span):** 
    *   이 건축 재료들만으로도 우리가 만들고 싶은 건물의 모든 부분을 만들 수 있다는 뜻이에요. 벡터 공간 안의 어떤 벡터라도 기저 벡터들을 적절히 늘리거나 줄여서 더하는 방식으로 표현할 수 있어야 한다.

### **13.2 기저를 확인하는 방법 (예시)**

* **3차원 공간 (R3)의 예시:**
    *   우리가 사는 3차원 공간을 생각해볼까요? 이 공간의 가장 기본적인 방향은 동-서, 남-북, 위-아래 세 방향이죠. 수학에서는 이 방향들을 각각 벡터 $(1,0,0)$, $(0,1,0)$, $(0,0,1)$로 표현해요.
    * **생성 확인:** 이 세 방향 벡터들을 조합하면 3차원 공간의 어떤 위치(어떤 벡터)라도 나타낼 수 있습니다. (예: 동쪽으로 2칸, 북쪽으로 3칸, 위로 1칸)
    * **독립성 확인:** 이 세 방향은 서로 완전히 독립적이죠. 동쪽 방향을 아무리 늘리거나 줄여도 북쪽 방향이 되거나 위쪽 방향이 될 수 없어요.
    * 이 두 조건을 만족하므로, 이 세 벡터는 **3차원 공간의 기저**가 됩니다.

* **2x2 행렬 공간 (R2x2)의 예시:**
    *   조금 더 복잡하게 2x2 크기의 행렬들로 이루어진 공간을 생각해보세요. 이 공간에서도 특정한 네 개의 행렬이 기저가 될 수 있습니다. 이것도 위와 똑같이 '생성'과 '독립' 두 가지 조건을 만족하는지 확인하면 돼요. 계산 과정은 조금 복잡하지만, 결국 이 네 행렬만으로 다른 어떤 2x2 행렬도 만들 수 있고, 이들 서로는 독립적임을 증명하는 과정입니다.

### **13.3 차원(Dimension)**: 공간의 크기

**차원**은 간단히 말해, **기저를 이루는 벡터의 개수**를 의미합니다.

* 3차원 공간(R3)의 기저가 3개의 벡터로 이루어져 있었죠? 그래서 **R3의 차원은 3**입니다.
* 2x2 행렬 공간(R2x2)의 기저가 4개의 행렬로 이루어져 있었다면, **R2x2의 차원은 4**입니다.
*  3x3 행렬 공간의 기저를 이루는 벡터(여기서는 행렬)의 개수가 9개이므로, 3x3 행렬 공간의 차원은 9.

이때 중요한 특징은, 어떤 벡터 공간이든 그 **차원은 항상 고정되어 있다**는 거예요. 즉, 3차원 공간의 기저를 어떤 벡터들로 선택하든, 항상 3개의 벡터가 필요하다는 뜻이죠.

*   **결론적으로, 기저는 벡터 공간을 '만들 수 있는' 최소한의 독립적인 벡터들의 모음이고, 차원은 그 기저를 이루는 벡터들의 '개수'를 통해 공간의 크기를 알려주는 개념입니다.**

이 개념들은 선형 대수학에서 벡터 공간의 구조를 이해하고 분석하는 데 매우 중요한 도구가 됩니다. 혹시 더 궁금한 점이 있으신가요?

## 14. 기저 변경
- 출처:[Change of Basis](https://www.youtube.com/watch?v=HZa1RwFHgwU&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=14)

###  **14.1 기저(Basis)가 뭔가요?**

**기저**는 벡터 공간에 있는 모든 벡터를 표현할 수 있는 **기본적인 벡터들의 묶음**이에요. 마치 색을 표현할 때 빨강, 파랑, 노랑 세 가지 기본색만 있으면 어떤 색이든 만들 수 있는 것과 비슷해요.

가장 흔하게 접하는 2차원 공간(XY 평면)을 예로 들어볼게요.
* XY 평면에서 기본 기저는 **X 방향 단위 벡터 I (1, 0)**와 **Y 방향 단위 벡터 J (0, 1)**입니다.
* 어떤 벡터 **V = (2, 3)**이 있다면, 이 벡터는 **2 * I + 3 * J** 로 나타낼 수 있어요. 여기서 **2**와 **3**이 바로 기본 기저에서의 **좌표**가 됩니다.

### **14.2 새로운 기저와 좌표 변경**

우리는 기본 기저 말고도 다른 벡터들을 새로운 기저로 사용할 수 있어요. 예를 들어, **u1 = (1, 2)**와 **u2 = (3, 3)**도 2차원 공간의 새로운 기저가 될 수 있습니다.

* 원래 **V = (2, 3)**이라는 벡터는 여전히 **V = (2, 3)**이지만, 이 벡터를 새로운 기저 **u1**과 **u2**를 사용해서도 표현할 수 있어요.
* 예를 들어, **V = 1 \* u1 - (1/3) \* u2**와 같이 표현될 수 있습니다. 이때, **1**과 **-1/3**이 새로운 기저에서의 **좌표**가 되는 거죠.

**중요한 점은 기저를 바꾸더라도 원래의 벡터 자체는 변하지 않는다는 거예요!** 단지 그 벡터를 표현하는 방식(좌표)이 달라지는 것뿐입니다.

### **14.3 전이 행렬(Transition Matrix)이란?**

기본 기저에서의 좌표와 새로운 기저에서의 좌표 사이의 관계를 찾아주는 도구가 바로 **전이 행렬**입니다.

* 벡터 **V**를 기본 기저의 좌표 **V**와 새로운 기저의 좌표 **V'**로 표현했을 때, 이 둘 사이에는 **V = U \* V'** 라는 간단한 행렬 방정식 관계가 성립해요.
* 여기서 **U**가 바로 **전이 행렬**입니다. 이 행렬은 기본 기저에서의 좌표와 새로운 기저에서의 좌표를 연결.
* **전이 행렬 U**는 새로운 기저 벡터들을 열(세로 줄)로 세워서 만들 수 있어요. 만약 새로운 기저가 **u1**과 **u2**라면, **$U = [u1 u2]$** 가 됩니다.

### **14.4 새로운 기저에서의 좌표 찾기 (V' 구하기)**

만약 우리가 기본 기저에서의 벡터 **V**를 알고, 새로운 기저에서의 좌표 **V'**를 찾고 싶다면 어떻게 해야 할까요?

* 위의 공식 **V = U \* V'** 에서 **V'**를 찾아내기 위해 **전이 행렬 U의 역행렬(U_inverse)**을 사용해야 합니다.
* **$V' = U^{-1} \cdot V$** 라는 공식을 사용하면 새로운 기저에서의 좌표를 구할 수 있어요.

**예시:**
벡터 **V = (2, 3)**을 기저 **u1 = (1, 2)**, **u2 = (3, 3)**으로 표현해볼게요.

1.  **전이 행렬 U**는 **$$\begin{pmatrix}1 & 3 \\ 2 & 3\end{pmatrix}$$** 이 됩니다.
2.  **U의 역행렬**을 계산합니다.
    * 2x2 행렬의 역행렬은 대각선 원소는 서로 바꾸고, 나머지 원소는 부호를 바꾼 다음, 원래 행렬의 행렬식(determinant)으로 나누면 됩니다.
    * **U**의 행렬식은 $(1 * 3) - (3 * 2) = 3 - 6 = -3$ 입니다.
    * 따라서 **$U^{-1}$**는 **(1/-3) * $$\begin{pmatrix} 3 & -3 \\ -2 & 1\end{pmatrix}$$** 가 됩니다.
3.  이제 **$V' = U^{-1} \* V$**를 계산합니다.
    * **$$V' = \begin{pmatrix}-1 & 1 \\ 2/3 & -1/3 \end{pmatrix}  \begin{pmatrix}2 \\ 3 \end{pmatrix}$$**
    * **$$V' = \begin{bmatrix}(-1*2) + (1*3) \$2/3*2) + (-1/3*3)\end{bmatrix}$$**
    * **$$V' = \begin{bmatrix}-2 + 3 \\ 4/3 - 1\end{bmatrix}$$**
    * **$$V' = \begin{bmatrix}1 \\ 1/3\end{bmatrix}$$**

따라서, **V = (2, 3)**은 새로운 기저 **u1, u2**에서 **(1, 1/3)**이라는 좌표를 갖게 됩니다. 이는 **V = 1 \* u1 + (1/3) \* u2** 와 같다는 의미예요.

### **14.5 두 비표준 기저 간의 전환**

만약 기본 기저가 아닌, **서로 다른 두 개의 새로운 기저(예: U 기저와 W 기저)** 사이에서 좌표를 전환하고 싶다면 어떻게 할까요?

* 이때는 **기본 기저를 '중간 다리'**로 사용합니다.
* 벡터 **V**를 **U 기저**와 **W 기저**로 각각 표현하면 다음과 같아요:
    * **V = U \* Vu** (여기서 Vu는 U 기저에서의 V 좌표)
    * **V = W \* Vw** (여기서 Vw는 W 기저에서의 V 좌표)
* 이 두 식을 같다고 놓으면 **U \* Vu = W \* Vw** 가 됩니다.
* **W 기저에서 U 기저로** 바꾸고 싶다면: **Vu = U_inverse \* W \* Vw**
* **U 기저에서 W 기저로** 바꾸고 싶다면: **Vw = W_inverse \* U \* Vu**


### **14.6 선형 변환과의 관계**

**기저 변경(Change of Basis)**은 사실 **선형 변환(Linear Transformation)**의 특별한 한 종류라고 생각할 수 있습니다. 즉, 벡터의 위치를 그대로 둔 채, 벡터를 표현하는 '좌표계'만 바꿔주는 변환인 거죠.

## 15. 벡터공간에서의 선형 변환
- 출처: [Linear Transformations on Vector Spaces](https://www.youtube.com/watch?v=is1cg5yhdds&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=15)

### 15.1 **선형 변환이란?**
선형 변환은 **벡터를 다른 벡터로 바꾸는 함수**입니다. 마치 숫자를 입력해서 새로운 숫자를 얻는 함수(예: $ f(x) = 2x $)처럼, 벡터 공간에서 벡터를 입력하면 새로운 벡터를 출력합니다. 예를 들어:
- 2차원 벡터 (x, y)를 입력하면 3차원 벡터 (a, b, c)로 바뀔 수 있습니다.
- 또는 같은 차원의 벡터로 바뀔 수도 있습니다 (예: 2차원 → 2차원).

수학적으로, 선형 변환은 **L: V → W**로 표현됩니다:
- **V**: 입력 벡터가 속한 벡터 공간 (예: $$\mathbb{R}^2$$, 2차원 벡터 공간).
- **W**: 출력 벡터가 속한 벡터 공간 (예: $$\mathbb{R}^3$$, 3차원 벡터 공간).

### 15.2 **선형 변환의 두 가지 규칙**
선형 변환이 되려면 다음 두 가지 규칙을 반드시 만족해야 합니다. 이 규칙들은 벡터를 변환할 때 "일정한 패턴"을 유지하게 해줍니다.

1. **스칼라 곱셈 규칙**:  
   벡터에 숫자(스칼라)를 곱한 뒤 변환한 결과는, 벡터를 먼저 변환한 뒤 스칼라를 곱한 결과와 같아야 합니다.  
   수식:  
   \[
   L(c \mathbf{V}) = c L(\mathbf{V})
   \]
   - $\mathbf{V}$: 벡터, 
   - $c$: 스칼라(숫자).

2. **벡터 덧셈 규칙**:  
   두 벡터를 더한 뒤 변환한 결과는, 각 벡터를 따로 변환한 결과를 더한 것과 같아야 합니다.  
   수식:  
   \[
   L(\mathbf{V} + \mathbf{W}) = L(\mathbf{V}) + L(\mathbf{W})
   \]

이 두 규칙은 변환 과정이 "비틀리거나 왜곡되지 않고" 일관되게 작동해야 함을 의미합니다.

### 15.3 **예시: 선형 변환 확인**
영상에서 다룬 예시를 통해 선형 변환을 확인해보겠습니다.  
변환 $$ L: \mathbb{R}^2 \to \mathbb{R}^3 $$는 벡터 $\mathbf{V}$ = $$\begin{bmatrix} V_1 \\ V_2 \end{bmatrix}$$를 다음과 같이 바꿉니다:


$L(\mathbf{V})$ = $$\begin{bmatrix} V_2 \\ V_1 + V_2 \\ V_1 - V_2 \end{bmatrix}$$

#### (1) **스칼라 곱셈 규칙 확인**
- 벡터 $\mathbf{V}$ = $$\begin{bmatrix} V_1 \\ V_2 \end{bmatrix}$$에 스칼라 $c$를 곱하면:  
  $c \mathbf{V}$ = $$\begin{bmatrix} c V_1 \\ c V_2 \end{bmatrix}$$
  
- 이를 변환:  
  $$
  L(c \mathbf{V}) = L\left( \begin{bmatrix} c V_1 \\ c V_2 \end{bmatrix} \right) = \begin{bmatrix} c V_2 \\ c V_1 + c V_2 \\ c V_1 - c V_2 \end{bmatrix}
  $$
- 한편, 원래 벡터를 먼저 변환한 뒤 스칼라를 곱하면:  
  $$
  L(\mathbf{V}) = \begin{bmatrix} V_2 \\ V_1 + V_2 \\ V_1 - V_2 \end{bmatrix}, \quad c L(\mathbf{V}) = c \begin{bmatrix} V_2 \\ V_1 + V_2 \\ V_1 - V_2 \end{bmatrix} = \begin{bmatrix} c V_2 \\ c (V_1 + V_2) \\ c (V_1 - V_2) \end{bmatrix}
  $$
- 두 결과가 같으므로, **스칼라 곱셈 규칙** 만족!

#### (2) **벡터 덧셈 규칙 확인**
- 두 벡터 $$\mathbf{V} = \begin{bmatrix} V_1 \\ V_2 \end{bmatrix}$$, $$\mathbf{W} = \begin{bmatrix} W_1 \\ W_2 \end{bmatrix}$$를 더하면:  
  $$
  \mathbf{V} + \mathbf{W} = \begin{bmatrix} V_1 + W_1 \\ V_2 + W_2 \end{bmatrix}
  $$
- 이를 변환:  
  $$
  L(\mathbf{V} + \mathbf{W}) = L\left( \begin{bmatrix} V_1 + W_1 \\ V_2 + W_2 \end{bmatrix} \right) = \begin{bmatrix} V_2 + W_2 \\ (V_1 + W_1) + (V_2 + W_2) \\ (V_1 + W_1) - (V_2 + W_2) \end{bmatrix}
  $$
- 한편, 각 벡터를 따로 변환한 뒤 더하면:  
  $$
  L(\mathbf{V}) = \begin{bmatrix} V_2 \\ V_1 + V_2 \\ V_1 - V_2 \end{bmatrix}, \quad L(\mathbf{W}) = \begin{bmatrix} W_2 \\ W_1 + W_2 \\ W_1 - W_2 \end{bmatrix}
  $$
  $$
  L(\mathbf{V}) + L(\mathbf{W}) = \begin{bmatrix} V_2 \\ V_1 + V_2 \\ V_1 - V_2 \end{bmatrix} + \begin{bmatrix} W_2 \\ W_1 + W_2 \\ W_1 - W_2 \end{bmatrix} = \begin{bmatrix} V_2 + W_2 \\ (V_1 + V_2) + (W_1 + W_2) \\ (V_1 - V_2) + (W_1 - W_2) \end{bmatrix}
  $$
- 두 결과가 같으므로, **벡터 덧셈 규칙** 만족!

따라서, 이 변환은 **선형 변환**입니다.

### 15.4 **행렬로 선형 변환 표현하기**
선형 변환은 **행렬 곱셈**으로 나타낼 수 있습니다. $\mathbb{R}^n \to \mathbb{R}^m$으로의 선형 변환은 $m \times n$ 행렬 $\mathbf{A}$를 사용해 $\mathbf{A} \mathbf{V}$로 표현됩니다.

위 예시에서 행렬 $\mathbf{A}$를 찾으려면:
- $\mathbb{R}^2$의 표준 기저 벡터 $$\begin{bmatrix} 1 \\ 0 \end{bmatrix}$$, $$\begin{bmatrix} 0 \\ 1 \end{bmatrix}$$를 변환한 결과를 행렬 $\mathbf{A}$의 열로 사용합니다.

1. 첫 번째 기저 벡터 변환:  
   $$
   L\left( \begin{bmatrix} 1 \\ 0 \end{bmatrix} \right) = \begin{bmatrix} 0 \\ 1 + 0 \\ 1 - 0 \end{bmatrix} = \begin{bmatrix} 0 \\ 1 \\ 1 \end{bmatrix}
   $$

2. 두 번째 기저 벡터 변환:  
   $$
   L\left( \begin{bmatrix} 0 \\ 1 \end{bmatrix} \right) = \begin{bmatrix} 1 \\ 0 + 1 \\ 0 - 1 \end{bmatrix} = \begin{bmatrix} 1 \\ 1 \\ -1 \end{bmatrix}
   $$

3. 이 결과들을 열로 가지는 행렬 $\mathbf{A}$:  
   $$
   \mathbf{A} = \begin{bmatrix} 0 & 1 \\ 1 & 1 \\ 1 & -1 \end{bmatrix}
   $$

4. 확인: 임의의 벡터 $$\mathbf{V} = \begin{bmatrix} V_1 \\ V_2 \end{bmatrix}$$에 대해 $\mathbf{A} \mathbf{V}$를 계산:  
   $$
   \mathbf{A} \mathbf{V} = \begin{bmatrix} 0 & 1 \\ 1 & 1 \\ 1 & -1 \end{bmatrix} \begin{bmatrix} V_1 \\ V_2 \end{bmatrix} = \begin{bmatrix} 0 \cdot V_1 + 1 \cdot V_2 \\ 1 \cdot V_1 + 1 \cdot V_2 \\ 1 \cdot V_1 + (-1) \cdot V_2 \end{bmatrix} = \begin{bmatrix} V_2 \\ V_1 + V_2 \\ V_1 - V_2 \end{bmatrix}
   $$
   이는 원래 변환 $L(\mathbf{V})$와 일치합니다!


### 15.5 **선형 변환의 실제 사용**
선형 변환은 현실에서 벡터를 늘리거나 줄이거나, 회전하거나, 반사시키는 등의 작업에 사용됩니다. 예를 들어:
- 이미지 회전(2D → 2D 변환).
- 3D 모델링에서 객체 이동이나 크기 조정.
- 데이터 분석에서 좌표 변환.

### 15.6 요약
- **선형 변환**: 벡터를 다른 벡터로 바꾸는 함수로, 두 가지 규칙(스칼라 곱셈, 벡터 덧셈)을 만족해야 함.
- **행렬 표현**: 선형 변환은 행렬 $\mathbf{A}$를 곱하는 형태로 나타낼 수 있음. 예시에서는 $$\begin{bmatrix} 0 & 1 \\ 1 & 1 \\ 1 & -1 \end{bmatrix}$$.
- **핵심 아이디어**: 선형 변환은 벡터를 "일관된 방식"으로 변환하며, 행렬을 사용해 간단히 계산 가능.

## 16. 선형 변환의 이미지와 커널 쉽게 이해하기
- 출처: [Image and Kernel](https://www.youtube.com/watch?v=vyYrvhbDhW4&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=16)

선형 변환은 벡터 공간(예: $\mathbb{R}^3$)에 있는 벡터들을 특정 규칙에 따라 다른 벡터 공간(예: $\mathbb{R}^2$)으로 바꾸는 함수입니다. 이 과정에서 **이미지(Image)**와 **커널(Kernel)**이라는 두 가지 중요한 개념이 등장합니다. 이를 쉽게 풀어서 설명하겠습니다.

### 16.1 이미지(Image)란?

1. 정의
- **이미지**는 선형 변환 $L: V \to W$가 벡터 공간 $V$의 벡터들을 $W$로 변환했을 때, 결과로 나오는 $W$의 벡터들 집합입니다.
- 비유: $V$라는 방에서 플래시를 켜서 $W$라는 벽에 빛을 비춘다고 생각하세요. 빛이 비춘 부분(결과 벡터들)이 바로 **이미지**입니다.
- 특히, $V$ 전체를 변환했을 때 나오는 결과는 $L$의 **범위(Range)**라고 부릅니다.

2. 예시
- 변환: $L(\mathbf{V})$ = $$\begin{bmatrix} v_1 \\ v_2 - v_3 \end{bmatrix}$$, 여기서 $\mathbf{V}$ = $$\begin{bmatrix} v_1 \\ v_2 \\ v_3 \end{bmatrix}$$는 $\mathbb{R}^3$의 벡터이고, 결과는 $\mathbb{R}^2$의 벡터입니다.
- 특정 부분 공간 $s$: $\mathbf{V}$ = $$\begin{bmatrix} C \\ 2C \\ 0 \end{bmatrix}$$ (첫 번째 요소는 $C$, 두 번째는 $2C$, 세 번째는 0).
- 이 벡터를 $L$에 적용:
  $$
  L\left(\begin{bmatrix} C \\ 2C \\ 0 \end{bmatrix}\right) = \begin{bmatrix} C \\ 2C - 0 \end{bmatrix} = \begin{bmatrix} C \\ 2C \end{bmatrix}
  $$
- 결과: 이 부분 공간 $s$의 이미지는 $$\begin{bmatrix} C \\ 2C \end{bmatrix}$$ 형태의 $\mathbb{R}^2$ 벡터들입니다. 즉, 두 번째 요소가 첫 번째 요소의 두 배인 벡터들(예: $$\begin{bmatrix} 1 \\ 2 \end{bmatrix}, \begin{bmatrix} 3 \\ 6 \end{bmatrix}$$)이 이미지입니다.

3. 특징
- 이미지는 항상 $W$의 **부분 공간**입니다. 즉, $W$ 안에 포함된, 특정 규칙을 따르는 벡터들로 이루어진 집합입니다.

### 16.2 커널(Kernel)이란?

1. 정의
- **커널**은 선형 변환 $L$을 적용했을 때 $W$의 영 벡터(예: $\mathbb{R}^2$에서는 $$\begin{bmatrix} 0 \\ 0 \end{bmatrix}$$)로 변환되는 $V$의 벡터들 집합.
- 비유: $V$에서 플래시를 켰을 때 벽($W$)에 아무 빛도 비추지 않는(결과가 0) 벡터들을 찾는 것과 같다.
- 수학적으로: $L(\mathbf{V}) = \mathbf{0}_W$를 만족하는 $\mathbf{V}$들의 집합.

2. 예시
- 같은 변환: $$L(\mathbf{V}) = \begin{bmatrix} v_1 \\ v_2 - v_3 \end{bmatrix}$$
- 커널을 찾으려면 $$L(\mathbf{V}) = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$를 만족하는 $\mathbf{V}$를 구합니다:
  $$
  \begin{bmatrix} v_1 \\ v_2 - v_3 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}
  $$
- 방정식:
  - $v_1 = 0$
  - $v_2 - v_3 = 0 \implies v_2 = v_3$
- 결과: $$\mathbf{V} = \begin{bmatrix} 0 \\ v_2 \\ v_2 \end{bmatrix} = \begin{bmatrix} 0 \\ C \\ C \end{bmatrix}$$ (예: $$\begin{bmatrix} 0 \\ 1 \\ 1 \end{bmatrix}, \begin{bmatrix} 0 \\ 2 \\ 2 \end{bmatrix}$$).
- 이 벡터들을 $L$에 적용하면:
  $$
  L\left(\begin{bmatrix} 0 \\ C \\ C \end{bmatrix}\right) = \begin{bmatrix} 0 \\ C - C \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}
  $$
- 즉, 커널은 $$\begin{bmatrix} 0 \\ C \\ C \end{bmatrix}$$ 형태의 $\mathbb{R}^3$ 벡터들입니다.

3. 특징
- 커널은 항상 $V$의 **부분 공간**입니다. 즉, $V$ 안에 포함된, 특정 조건을 만족하는 벡터들로 이루어진 집합입니다.

### 16.3 요약: 이미지와 커널의 의미
- **이미지**: 변환 $L$이 $V$의 벡터들을 $W$로 보냈을 때 도달하는 모든 결과 벡터들의 집합. ($W$의 부분 공간)
- **커널**: 변환 $L$을 적용했을 때 $W$의 영 벡터($\begin{bmatrix} 0 \\ 0 \end{bmatrix}$)로 가는 $V$의 벡터들. ($V$의 부분 공간)
- 비유: 플래시($L$)를 켜서 벽($W$)에 비춘 빛이 이미지이고, 빛이 전혀 비추지 않는(결과가 0인) 벡터들이 커널입니다.

### 16.4 왜 중요한가?
- **이미지**는 선형 변환이 얼마나 많은 $W$ 공간을 "덮는지" 보여줍니다.
- **커널**은 선형 변환이 어떤 벡터들을 "0으로 만드는지" 보여줍니다.
- 이 두 개념은 선형 변환이 벡터 공간을 어떻게 변화시키는지 이해하는 데 핵심적인 역할을 합니다.

## 17. 직교성과 정규직교성 쉽게 이해하기
- 출처: [Orthogonality and Orthonormality](https://www.youtube.com/watch?v=6nqMegdbxik&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=17)


### 17.1 벡터의 직교성: 서로 수직인 벡터
- **직교란?**: 두 벡터가 서로 **90도 각도**를 이루면 직교한다고 합니다. 즉, 두 벡터가 "수직"인 관계입니다.
- **확인 방법**: 두 벡터의 **내적**(dot product)이 0이면 직교입니다.
  - 내적은 벡터의 각 성분을 곱한 뒤 더한 값입니다.
  - 예: 벡터 $$\mathbf{A} = \begin{bmatrix} 4 \\ 2 \\ -1 \end{bmatrix}, \mathbf{B} = \begin{bmatrix} 1 \\ -3 \\ -2 \end{bmatrix}$$의 내적은:
    \[
    \mathbf{A} \cdot \mathbf{B} = (4 \cdot 1) + (2 \cdot -3) + (-1 \cdot -2) = 4 - 6 + 2 = 0
    \]
    → 내적이 0이므로 $\mathbf{A}$와 $\mathbf{B}$는 직교입니다.

### 17.2 벡터의 정규직교성: 길이 1인 직교 벡터
- **정규직교란?**: 벡터들이 **서로 직교**하면서 **길이가 1**인 경우를 정규직교(orthonormal)라고 합니다.
- **길이 계산**: 벡터의 길이는 자신과의 내적의 제곱근입니다.
  - 예: 벡터 $$\mathbf{A} = \begin{bmatrix} 4 \\ 2 \\ -1 \end{bmatrix}$$의 길이는:
    \[
    \sqrt{\mathbf{A} \cdot \mathbf{A}} = \sqrt{4^2 + 2^2 + (-1)^2} = \sqrt{16 + 4 + 1} = \sqrt{21}
    \]
  - 벡터 $$\mathbf{B} = \begin{bmatrix} 1 \\ -3 \\ -2 \end{bmatrix}$$의 길이는:
    \[
    \sqrt{\mathbf{B} \cdot \mathbf{B}} = \sqrt{1^2 + (-3)^2 + (-2)^2} = \sqrt{1 + 9 + 4} = \sqrt{14}
    \]
- **정규화**: 벡터를 길이로 나누면 길이가 1인 **단위 벡터**가 됩니다.
  - 예: $\mathbf{A}$를 정규화하면:
    $$
    \frac{\mathbf{A}}{\sqrt{21}} = \begin{bmatrix} \frac{4}{\sqrt{21}} \\ \frac{2}{\sqrt{21}} \\ \frac{-1}{\sqrt{21}} \end{bmatrix}
    $$
  - $\mathbf{B}$도 마찬가지로 정규화하면 길이가 1이 됩니다.
- 정규화된 $\mathbf{A}$와 $\mathbf{B}$는 여전히 직교이므로 **정규직교** 집합을 이룹니다.

### 17.3 부분 공간의 직교성: 공간끼리 수직
- **부분 공간이란?**: 3차원 공간(R³) 안에 있는 더 작은 공간(예: 선이나 평면)입니다.
- **직교 조건**: 한 부분 공간의 모든 벡터가 다른 부분 공간의 모든 벡터와 직교하면 두 부분 공간은 직교합니다.
- **예시**:
  - 부분 공간 $\mathbf{A}$: $$\begin{bmatrix} a_1 \\ 0 \\ 0 \end{bmatrix}$$ (x축 위의 벡터).
  - 부분 공간 $\mathbf{B}$: $$\begin{bmatrix} 0 \\ b_2 \\ b_3 \end{bmatrix}$$ (yz평면 위의 벡터).
  - 내적 계산:
    $$
    \begin{bmatrix} a_1 \\ 0 \\ 0 \end{bmatrix} \cdot \begin{bmatrix} 0 \\ b_2 \\ b_3 \end{bmatrix} = (a_1 \cdot 0) + (0 \cdot b_2) + (0 \cdot b_3) = 0
    $$
    → 항상 내적이 0이므로 두 부분 공간은 직교합니다.

### 17.4 정사각 행렬의 직교성: 열이 정규직교인 행렬
- **정의**: 정사각 행렬(예: 2x2 또는 3x3)의 **열 벡터들이 정규직교**라면 그 행렬은 **직교 행렬**입니다.
- **예시**: 행렬 $$\mathbf{M} = \begin{bmatrix} \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \end{bmatrix}$$
  - 열 벡터: $$\mathbf{v}_1 = \begin{bmatrix} \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} \end{bmatrix}, \mathbf{v}_2 = \begin{bmatrix} \frac{1}{\sqrt{2}} \\ -\frac{1}{\sqrt{2}} \end{bmatrix}$$
  - **직교성 확인**:
    \[
    \mathbf{v}_1 \cdot \mathbf{v}_2 = \left( \frac{1}{\sqrt{2}} \cdot \frac{1}{\sqrt{2}} \right) + \left( \frac{1}{\sqrt{2}} \cdot -\frac{1}{\sqrt{2}} \right) = \frac{1}{2} - \frac{1}{2} = 0
    \]
    → 직교합니다.
  - **길이 확인**:
    \[
    \text{길이 of } \mathbf{v}_1 = \sqrt{\left( \frac{1}{\sqrt{2}} \right)^2 + \left( \frac{1}{\sqrt{2}} \right)^2} = \sqrt{\frac{1}{2} + \frac{1}{2}} = \sqrt{1} = 1
    \]
    \[
    \text{길이 of } \mathbf{v}_2 = \sqrt{\left( \frac{1}{\sqrt{2}} \right)^2 + \left( -\frac{1}{\sqrt{2}} \right)^2} = \sqrt{\frac{1}{2} + \frac{1}{2}} = \sqrt{1} = 1
    \]
    → 길이가 1이므로 정규직교입니다.
- **직교 행렬의 장점**: 직교 행렬의 역행렬은 단순히 **전치 행렬**(행과 열을 바꾼 행렬)입니다.
  - 예: $$\mathbf{M}의 전치 행렬은 \mathbf{M}^T = \begin{bmatrix} \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \end{bmatrix}$$
  - $\mathbf{M}^T$가 $\mathbf{M}^{-1}$과 같음을 확인하려면 $\mathbf{M} \cdot \mathbf{M}^T = \mathbf{I}$ (단위 행렬)임을 확인하면 됩니다:
    $$
    \mathbf{M} \cdot \mathbf{M}^T = \begin{bmatrix} \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \end{bmatrix} \begin{bmatrix} \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \end{bmatrix}
    = \begin{bmatrix} \frac{1}{2} + \frac{1}{2} & \frac{1}{2} - \frac{1}{2} \\ \frac{1}{2} - \frac{1}{2} & \frac{1}{2} + \frac{1}{2} \end{bmatrix}
    = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}
    $$
    → 단위 행렬이므로 $\mathbf{M}$은 직교 행렬입니다.


### 17.5 함수의 직교성: 함수도 직교할 수 있다
- **함수 내적**: 두 함수 $f(x)$와 $g(x)$의 내적은 특정 구간 $[a, b]$에서 $f(x) \cdot g(x)$를 적분한 값입니다:
  \[
  \text{내적} = \int_a^b f(x) g(x) \, dx
  \]
- **직교 조건**: 이 적분값이 0이면 두 함수는 직교합니다.
- **예시**:
  - 함수 $f(x) = x$, $g(x) = 1$에 대해 구간 $[-1, 1]$에서 내적:
    $$
    \int_{-1}^1 x \cdot 1 \, dx = \int_{-1}^1 x \, dx = \left[ \frac{x^2}{2} \right]_{-1}^1 = \frac{1^2}{2} - \frac{(-1)^2}{2} = \frac{1}{2} - \frac{1}{2} = 0
    $$
    → 내적이 0이므로 직교합니다.
  - 하지만 구간 $[0, 1]$에서는:
    $$
    \int_0^1 x \cdot 1 \, dx = \int_0^1 x \, dx = \left[ \frac{x^2}{2} \right]_0^1 = \frac{1}{2} - 0 = \frac{1}{2}
    $$
    → 내적이 0이 아니므로 직교하지 않습니다.
- **가중 함수**: 때로는 $f(x) \cdot g(x) \cdot W(x)$ (여기서 $W(x)$는 가중 함수)를 적분하여 내적을 정의하기도 합니다.
- **왜 중요?**: 함수의 직교성은 신호 처리나 물리학에서 복잡한 시스템을 간단한 요소로 나누는 데 사용됩니다.

### 17.6 요약: 직교성과 정규직교성이란?
- **직교성**: 벡터, 부분 공간, 행렬, 함수가 서로 "수직"인 관계. 내적이 0인지를 통해 확인.
- **정규직교성**: 직교하면서 길이(또는 크기)가 1인 경우.
- **왜 중요?**: 직교성은 수학, 물리학, 공학에서 복잡한 문제를 간단히 풀 수 있게 해줍니다. 특히 직교 행렬은 계산이 쉬워지고, 함수 직교성은 데이터를 효율적으로 분석하는 데 도움을 줍니다.


## 18. 그램-슈미트 과정(Gram-Schmidt Process)
- 출처: [The Gram-Schmidt Process](https://www.youtube.com/watch?v=zHbfZWZJTGc&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=18)

### 18.1 **그램-슈미트 과정이란?**
- **목적**: 임의의 벡터 집합(예: $\mathbf{v}_1, \mathbf{v}_2, \mathbf{v}_3$)을 받아서, 서로 직교하는 벡터들($\mathbf{u}_1, \mathbf{u}_2, \mathbf{u}_3$)로 변환합니다. 필요하면 길이를 1로 만들어 정규직교 기저를 만듭니다.
- **왜 중요할까?**: 직교 기저는 계산이 간단하고, 수학/물리/컴퓨터 과학에서 많이 사용됩니다(예: 데이터 분석, 신호 처리 등).

### 18.2 **그램-슈미트 과정 단계**
주어진 벡터 집합 $\{\mathbf{v}_1, \mathbf{v}_2, \dots, \mathbf{v}_n\}$을 직교 기저 $\{\mathbf{u}_1, \mathbf{u}_2, \dots, \mathbf{u}_n\}$로 바꾸는 과정은 다음과 같습니다. 여기서 $\cdot$는 내적(벡터의 점곱)을 의미합니다.

1. **첫 번째 벡터**
- 첫 번째 직교 벡터 $\mathbf{u}_1$은 그냥 $\mathbf{v}_1$을 사용합니다:
  \[
  \mathbf{u}_1 = \mathbf{v}_1
  \]

2. **두 번째 벡터**
- $\mathbf{v}_2$에서 $\mathbf{u}_1$ 방향의 성분을 빼서, $\mathbf{u}_1$과 수직인 $\mathbf{u}_2$를 만듭니다:
  \[
  \mathbf{u}_2 = \mathbf{v}_2 - \frac{\mathbf{v}_2 \cdot \mathbf{u}_1}{\mathbf{u}_1 \cdot \mathbf{u}_1} \mathbf{u}_1
  \]
  - $\mathbf{v}_2 \cdot \mathbf{u}_1$: $\mathbf{v}_2$와 $\mathbf{u}_1$의 내적.
  - $\mathbf{u}_1 \cdot \mathbf{u}_1$: $\mathbf{u}_1$의 길이의 제곱.

3. **세 번째 벡터 (그리고 이후 벡터들)**
- $\mathbf{v}_3$에서 $\mathbf{u}_1$, $\mathbf{u}_2$ 방향의 성분을 빼서 $\mathbf{u}_3$를 만듭니다:
  \[
  \mathbf{u}_3 = \mathbf{v}_3 - \frac{\mathbf{v}_3 \cdot \mathbf{u}_1}{\mathbf{u}_1 \cdot \mathbf{u}_1} \mathbf{u}_1 - \frac{\mathbf{v}_3 \cdot \mathbf{u}_2}{\mathbf{u}_2 \cdot \mathbf{u}_2} \mathbf{u}_2
  \]
- 일반적으로, $k$번째 벡터는:
  $$
  \mathbf{u}_k = \mathbf{v}_k - \sum\limits_{i=1}^{k-1} \frac{\mathbf{v}_k \cdot \mathbf{u}_i}{\mathbf{u}_i \cdot \mathbf{u}_i} \mathbf{u}_i
  $$

### 18.3 **예시: 3차원 공간에서 직교 기저 만들기**
주어진 벡터들: $$\mathbf{v}_1 = \begin{bmatrix} 1 \\ -1 \\ 1 \end{bmatrix}, \mathbf{v}_2 = \begin{bmatrix} 1 \\ 0 \\ 1 \end{bmatrix}, \mathbf{v}_3 = \begin{bmatrix} 1 \\ 1 \\ 2 \end{bmatrix}$$

1. **$\mathbf{u}_1$ 계산:**
$$
\mathbf{u}_1 = \mathbf{v}_1 = \begin{bmatrix} 1 \\ -1 \\ 1 \end{bmatrix}
$$

2. **$\mathbf{u}_2$ 계산**
- 내적 계산:
  \[
  \mathbf{v}_2 \cdot \mathbf{u}_1 = (1 \cdot 1) + (0 \cdot -1) + (1 \cdot 1) = 1 + 0 + 1 = 2
  \]
  \[
  \mathbf{u}_1 \cdot \mathbf{u}_1 = (1 \cdot 1) + (-1 \cdot -1) + (1 \cdot 1) = 1 + 1 + 1 = 3
  \]
- 투영 계수: $\frac{\mathbf{v}_2 \cdot \mathbf{u}_1}{\mathbf{u}_1 \cdot \mathbf{u}_1} = \frac{2}{3}$
- $\mathbf{u}_2$ 계산:
  $$
  \mathbf{u}_2 = \mathbf{v}_2 - \frac{2}{3} \mathbf{u}_1 = \begin{bmatrix} 1 \\ 0 \\ 1 \end{bmatrix} - \frac{2}{3} \begin{bmatrix} 1 \\ -1 \\ 1 \end{bmatrix}
  $$
  $$
  = \begin{bmatrix} 1 \\ 0 \\ 1 \end{bmatrix} - \begin{bmatrix} \frac{2}{3} \\ -\frac{2}{3} \\ \frac{2}{3} \end{bmatrix} = \begin{bmatrix} 1 - \frac{2}{3} \\ 0 + \frac{2}{3} \\ 1 - \frac{2}{3} \end{bmatrix} = \begin{bmatrix} \frac{1}{3} \\ \frac{2}{3} \\ \frac{1}{3} \end{bmatrix}
  $$

3. **$\mathbf{u}_3$ 계산**
- 내적 계산:
  \[
  \mathbf{v}_3 \cdot \mathbf{u}_1 = (1 \cdot 1) + (1 \cdot -1) + (2 \cdot 1) = 1 - 1 + 2 = 2
  \]
  \[
  \mathbf{u}_1 \cdot \mathbf{u}_1 = 3 \quad (\text{이전에 계산})
  \]
  \[
  \mathbf{v}_3 \cdot \mathbf{u}_2 = \left(1 \cdot \frac{1}{3}\right) + \left(1 \cdot \frac{2}{3}\right) + \left(2 \cdot \frac{1}{3}\right) = \frac{1}{3} + \frac{2}{3} + \frac{2}{3} = \frac{5}{3}
  \]
  \[
  \mathbf{u}_2 \cdot \mathbf{u}_2 = \left(\frac{1}{3} \cdot \frac{1}{3}\right) + \left(\frac{2}{3} \cdot \frac{2}{3}\right) + \left(\frac{1}{3} \cdot \frac{1}{3}\right) = \frac{1}{9} + \frac{4}{9} + \frac{1}{9} = \frac{6}{9} = \frac{2}{3}
  \]
- 투영 계수:
  \[
  \frac{\mathbf{v}_3 \cdot \mathbf{u}_1}{\mathbf{u}_1 \cdot \mathbf{u}_1} = \frac{2}{3}, \quad \frac{\mathbf{v}_3 \cdot \mathbf{u}_2}{\mathbf{u}_2 \cdot \mathbf{u}_2} = \frac{\frac{5}{3}}{\frac{2}{3}} = \frac{5}{3} \* \frac{3}{2} = \frac{5}{2}
  \]
- $\mathbf{u}_3$ 계산:
  \[
  \mathbf{u}_3 = \mathbf{v}_3 - \frac{2}{3} \mathbf{u}_1 - \frac{5}{2} \mathbf{u}_2
  \]
  $$
  = \begin{bmatrix} 1 \\ 1 \\ 2 \end{bmatrix} - \frac{2}{3} \begin{bmatrix} 1 \\ -1 \\ 1 \end{bmatrix} - \frac{5}{2} \begin{bmatrix} \frac{1}{3} \\ \frac{2}{3} \\ \frac{1}{3} \end{bmatrix}
  $$
  $$
  = \begin{bmatrix} 1 \\ 1 \\ 2 \end{bmatrix} - \begin{bmatrix} \frac{2}{3} \\ -\frac{2}{3} \\ \frac{2}{3} \end{bmatrix} - \begin{bmatrix} \frac{5}{2} \cdot \frac{1}{3} \\ \frac{5}{2} \cdot \frac{2}{3} \\ \frac{5}{2} \cdot \frac{1}{3} \end{bmatrix} = \begin{bmatrix} 1 \\ 1 \\ 2 \end{bmatrix} - \begin{bmatrix} \frac{2}{3} \\ -\frac{2}{3} \\ \frac{2}{3} \end{bmatrix} - \begin{bmatrix} \frac{5}{6} \\ \frac{5}{3} \\ \frac{5}{6} \end{bmatrix}
  $$
  $$
  = \begin{bmatrix} 1 - \frac{2}{3} - \frac{5}{6} \\ 1 + \frac{2}{3} - \frac{5}{3} \\ 2 - \frac{2}{3} - \frac{5}{6} \end{bmatrix} = \begin{bmatrix} \frac{6}{6} - \frac{4}{6} - \frac{5}{6} \\ \frac{6}{6} + \frac{4}{6} - \frac{10}{6} \\ \frac{12}{6} - \frac{4}{6} - \frac{5}{6} \end{bmatrix} = \begin{bmatrix} -\frac{3}{6} \\ 0 \\ \frac{3}{6} \end{bmatrix} = \begin{bmatrix} -\frac{1}{2} \\ 0 \\ \frac{1}{2} \end{bmatrix}
  $$

4. **결과: 직교 기저**
$$
\mathbf{u}_1 = \begin{bmatrix} 1 \\ -1 \\ 1 \end{bmatrix}, \quad \mathbf{u}_2 = \begin{bmatrix} \frac{1}{3} \\ \frac{2}{3} \\ \frac{1}{3} \end{bmatrix}, \quad \mathbf{u}_3 = \begin{bmatrix} -\frac{1}{2} \\ 0 \\ \frac{1}{2} \end{bmatrix}
$$

### 18.4 **정규직교 기저로 변환**
직교 기저를 정규직교 기저로 만들려면, 각 벡터를 길이로 나누어 길이를 1로 만듭니다. 벡터의 길이는 $\sqrt{\mathbf{u} \cdot \mathbf{u}}$입니다.

1. **$\mathbf{u}_1$ 정규화**
\[
\mathbf{u}_1 \cdot \mathbf{u}_1 = 1^2 + (-1)^2 + 1^2 = 3, \quad \text{길이} = \sqrt{3}
\]
$$
\text{정규화된 } \mathbf{u}_1 = \frac{\mathbf{u}_1}{\sqrt{3}} = \begin{bmatrix} \frac{1}{\sqrt{3}} \\ -\frac{1}{\sqrt{3}} \\ \frac{1}{\sqrt{3}} \end{bmatrix}
$$

2. **$\mathbf{u}_2$ 정규화**
\[
\mathbf{u}_2 \cdot \mathbf{u}_2 = \left(\frac{1}{3}\right)^2 + \left(\frac{2}{3}\right)^2 + \left(\frac{1}{3}\right)^2 = \frac{1}{9} + \frac{4}{9} + \frac{1}{9} = \frac{6}{9} = \frac{2}{3}, \quad \text{길이} = \sqrt{\frac{2}{3}}
\]
$$
\text{정규화된 } \mathbf{u}_2 = \frac{\mathbf{u}_2}{\sqrt{\frac{2}{3}}} = \sqrt{\frac{3}{2}} \cdot \mathbf{u}_2 = \begin{bmatrix} \frac{1}{3} \cdot \sqrt{\frac{3}{2}} \\ \frac{2}{3} \cdot \sqrt{\frac{3}{2}} \\ \frac{1}{3} \cdot \sqrt{\frac{3}{2}} \end{bmatrix} = \begin{bmatrix} \frac{\sqrt{6}}{6} \\ \frac{2\sqrt{6}}{6} \\ \frac{\sqrt{6}}{6} \end{bmatrix} = \begin{bmatrix} \frac{1}{\sqrt{6}} \\ \frac{2}{\sqrt{6}} \\ \frac{1}{\sqrt{6}} \end{bmatrix}
$$

3. **$\mathbf{u}_3$ 정규화**
\[
\mathbf{u}_3 \cdot \mathbf{u}_3 = \left(-\frac{1}{2}\right)^2 + 0^2 + \left(\frac{1}{2}\right)^2 = \frac{1}{4} + \frac{1}{4} = \frac{1}{2}, \quad \text{길이} = \sqrt{\frac{1}{2}}
\]
$$
\text{정규화된 } \mathbf{u}_3 = \frac{\mathbf{u}_3}{\sqrt{\frac{1}{2}}} = \sqrt{2} \cdot \mathbf{u}_3 = \begin{bmatrix} -\frac{1}{2} \cdot \sqrt{2} \\ 0 \\ \frac{1}{2} \cdot \sqrt{2} \end{bmatrix} = \begin{bmatrix} -\frac{\sqrt{2}}{2} \\ 0 \\ \frac{\sqrt{2}}{2} \end{bmatrix}
$$

4. **결과: 정규직교 기저**
$$
\begin{bmatrix} \frac{1}{\sqrt{3}} \\ -\frac{1}{\sqrt{3}} \\ \frac{1}{\sqrt{3}} \end{bmatrix}, \quad \begin{bmatrix} \frac{1}{\sqrt{6}} \\ \frac{2}{\sqrt{6}} \\ \frac{1}{\sqrt{6}} \end{bmatrix}, \quad \begin{bmatrix} -\frac{\sqrt{2}}{2} \\ 0 \\ \frac{\sqrt{2}}{2} \end{bmatrix}
$$

### 18.5 **왜 유용할까?**
- **직교 벡터**: 서로 수직이라 계산이 간단합니다(예: 투영 계산, 좌표 변환).
- **정규직교 기저**: 길이가 1이라 더 간단하고, 함수 공간(예: 신호 처리)이나 데이터 분석에서도 사용됩니다.
- **응용**: 컴퓨터 그래픽, 머신 러닝, 물리 시뮬레이션 등에서 중요합니다.

### 18.6 **핵심 요약**
1. 그램-슈미트 과정은 벡터들을 직교 벡터로 바꾸는 방법입니다.
2. 각 단계에서, 새로운 벡터에서 이전 직교 벡터들의 성분을 빼줍니다.
3. 정규직교 기저를 원하면, 직교 벡터를 길이로 나누어 단위 벡터로 만듭니다.
4. 위 예시처럼, 행렬과 내적을 사용해 계산하며, 결과는 항상 직교(수직)인 벡터들입니다.

## 19.  고유값과 고유 벡터란?
- 출처: [Finding Eigenvalues and Eigenvectors](https://www.youtube.com/watch?v=TQvxWaQnrqI&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=19)

### 19.1 고유값과 고유 벡터란?
- **고유 벡터(Eigenvector)**: 정사각 행렬 $\mathbf{A}$에 특정 벡터 $\mathbf{x}$를 곱했을 때, 그 결과가 원래 벡터 $\mathbf{x}$에 어떤 숫자(스칼라)를 곱한 것과 같은 벡터인 경우, 그 벡터 $\mathbf{x}$를 고유 벡터라고 합니다. 수식으로 나타내면:
  \[
  \mathbf{A} \mathbf{x} = \lambda \mathbf{x}
  \]
  여기서 $\lambda$는 **고유값(Eigenvalue)**이라고 부르는 숫자입니다.
- **쉽게 말해**: 행렬 $\mathbf{A}$가 고유 벡터 $\mathbf{x}$를 "방향은 그대로 두고 크기만 $\lambda$배로 바꾸는" 역할을 합니다.
- **중요한 점**:
  - 고유 벡터는 $\mathbf{x} = \mathbf{0}$ (영 벡터)이 아니어야 합니다.
  - 고유값은 행렬의 크기(예: $n \times n$ 행렬이면 최대 $n$개)만큼 있을 수 있습니다.
  - 이 개념은 물리학(진동, 운동), 공학, 데이터 분석 등에서 많이 사용됩니다.

### 19.2 고유값 찾는 방법
고유값 $\lambda$를 찾으려면 다음 단계를 따릅니다.

1. 기본 방정식
고유 벡터 방정식 $\mathbf{A} \mathbf{x} = \lambda \mathbf{x}$를 변형하면:
\[
(\mathbf{A} - \lambda \mathbf{I}) \mathbf{x} = \mathbf{0}
\]
여기서 $\mathbf{I}$는 항등 행렬(대각선이 1이고 나머지가 0인 행렬)입니다.

2. 비자명해 조건
고유 벡터 $\mathbf{x}$가 영 벡터($\mathbf{0}$)가 아니어야 하므로, 행렬 $\mathbf{A} - \lambda \mathbf{I}$의 역행렬이 존재하지 않아야 합니다. 이는 행렬식(determinant)이 0이라는 뜻입니다:
\[
\det(\mathbf{A} - \lambda \mathbf{I}) = 0
\]
이 방정식을 **특성 방정식(Characteristic Equation)**이라고 하며, 이 방정식을 풀면 고유값 $\lambda$를 얻는다.

3. 예시: 행렬 $$\mathbf{A} = \begin{bmatrix} 1 & 1 \\ 4 & 1 \end{bmatrix}$$
* $\mathbf{A} - \lambda \mathbf{I}$를 계산:
   $$
   \mathbf{A} - \lambda \mathbf{I} = \begin{bmatrix} 1 & 1 \\ 4 & 1 \end{bmatrix} - \lambda \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} = \begin{bmatrix} 1 - \lambda & 1 \\ 4 & 1 - \lambda \end{bmatrix}
   $$

* 행렬식 계산:
   \[
   \det(\mathbf{A} - \lambda \mathbf{I}) = (1 - \lambda)(1 - \lambda) - (1 \cdot 4) = (1 - \lambda)^2 - 4
   \]
   \[
   = 1 - 2\lambda + \lambda^2 - 4 = \lambda^2 - 2\lambda - 3
   \]

* 특성 방정식 풀기:
   \[
   \lambda^2 - 2\lambda - 3 = 0
   \]
   인수분해: $(\lambda - 3)(\lambda + 1) = 0$
   \[
   \lambda = 3 \quad \text{또는} \quad \lambda = -1
   \]
   **고유값**: $\lambda = 3$, $\lambda = -1$.

### 19.3 고유 벡터 찾는 방법
각 고유값 $\lambda$에 대해 고유 벡터 $\mathbf{x}$를 찾으려면 다음 단계를 따릅니다.

1. 고유값을 대입
고유값 $\lambda$를 $\mathbf{A} - \lambda \mathbf{I}$에 대입하여 방정식 $(\mathbf{A} - \lambda \mathbf{I}) \mathbf{x} = \mathbf{0}$를 풉니다.

2. 행렬을 행 사다리꼴로 변환
행 연산을 통해 $\mathbf{A} - \lambda \mathbf{I}$를 단순화하고, $\mathbf{x}$를 구합니다.

3. 고유 벡터의 형태
해는 고유 벡터의 "형태"를 나타냅니다. 고유 벡터는 스칼라 배수(예: $c \mathbf{x}$)도 모두 고유 벡터이므로, 대표적인 벡터를 선택합니다.

4. 예시: 행렬 $$\mathbf{A} = \begin{bmatrix} 1 & 1 \\ 4 & 1 \end{bmatrix}$$
- **고유값 $\lambda = 3$**:
  1. $\mathbf{A} - 3\mathbf{I}$:
     $$
     \mathbf{A} - 3\mathbf{I} = \begin{bmatrix} 1 - 3 & 1 \\ 4 & 1 - 3 \end{bmatrix} = \begin{bmatrix} -2 & 1 \\ 4 & -2 \end{bmatrix}
     $$

  2. 방정식 $(\mathbf{A} - 3\mathbf{I}) \mathbf{x} = \mathbf{0}$:
     $$
     \begin{bmatrix} -2 & 1 \\ 4 & -2 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}
     $$

  3. 행 연산:
     - 두 번째 행에 첫 번째 행의 2배를 더함:
       $$
       \begin{bmatrix} -2 & 1 \\ 4 + 2(-2) & -2 + 2(1) \end{bmatrix} = \begin{bmatrix} -2 & 1 \\ 0 & 0 \end{bmatrix}
       $$
     - 첫 번째 행의 방정식: $-2x_1 + x_2 = 0 \implies x_2 = 2x_1$.

  4. 고유 벡터:
     $$
     \mathbf{x} = \begin{bmatrix} x_1 \\ 2x_1 \end{bmatrix} = x_1 \begin{bmatrix} 1 \\ 2 \end{bmatrix}
     $$
     $x_1 = 1$로 선택하면:
     $$
     \mathbf{x} = \begin{bmatrix} 1 \\ 2 \end{bmatrix}
     $$

- **고유값 $\lambda = -1$**:
  1. $\mathbf{A} - (-1)\mathbf{I}$:
     $$
     \mathbf{A} - (-1)\mathbf{I} = \begin{bmatrix} 1 - (-1) & 1 \\ 4 & 1 - (-1) \end{bmatrix} = \begin{bmatrix} 2 & 1 \\ 4 & 2 \end{bmatrix}
     $$

  2. 방정식 $(\mathbf{A} + \mathbf{I}) \mathbf{x} = \mathbf{0}$:
     $$
     \begin{bmatrix} 2 & 1 \\ 4 & 2 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}
     $$

  3. 행 연산:
     - 두 번째 행에서 첫 번째 행의 2배를 뺌:
       $$
       \begin{bmatrix} 2 & 1 \\ 4 - 2(2) & 2 - 2(1) \end{bmatrix} = \begin{bmatrix} 2 & 1 \\ 0 & 0 \end{bmatrix}
       $$
     - 첫 번째 행의 방정식: $2x_1 + x_2 = 0 \implies x_2 = -2x_1$.

  4. 고유 벡터:
     $$
     \mathbf{x} = \begin{bmatrix} x_1 \\ -2x_1 \end{bmatrix} = x_1 \begin{bmatrix} 1 \\ -2 \end{bmatrix}
     $$
     $x_1 = 1$로 선택하면:
     $$
     \mathbf{x} = \begin{bmatrix} 1 \\ -2 \end{bmatrix}
     $$

### 19.4 요약
- **고유값**: $\lambda = 3$, $\lambda = -1$.
- **고유 벡터**:
  - $\lambda = 3$에 대해: $$\begin{bmatrix} 1 \\ 2 \end{bmatrix}$$ (또는 그 스칼라 배수).
  - $\lambda = -1$에 대해: $$\begin{bmatrix} 1 \\ -2 \end{bmatrix}$$ (또는 그 스칼라 배수).

### 19.5 왜 중요한가?
고유값과 고유 벡터는 행렬이 데이터를 어떻게 변환하는지 이해하는 데 도움을 줍니다. 예를 들어:
- 진동 시스템에서 특정 주파수를 찾거나,
- 데이터 분석에서 주요 패턴을 추출하거나,
- 물리학에서 시스템의 안정성을 분석할 때 사용됩니다.


## 20. 행렬 대각화(Diagonalization)
- 출처: [Diagonalization](https://www.youtube.com/watch?v=WTLl03D4TNA&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=20)

행렬 대각화는 복잡한 행렬을 더 단순한 형태로 바꾸는 방법입니다. 이를 통해 계산이 쉬워지고, 컴퓨터로 처리하기도 편해집니다. 입문자를 위해 간단히 정리했습니다.

### 20.1 대각화란?
- **행렬 A**를 **X D X⁻¹** 형태로 표현하는 과정입니다.
  - **D**: 대각 행렬 (대각선에만 값이 있고, 나머지는 0인 행렬)
  - **X**: 고유벡터로 만든 행렬
  - **X⁻¹**: X의 역행렬
- 이 과정을 통해 **A = X D X⁻¹** 또는 **X⁻¹ A X = D**로 나타낼 수 있습니다.
$$
A = X \begin{bmatrix}
\lambda_1 & 0 \\
0 & \lambda_2
\end{bmatrix} X^{-1}
$$

### 20.2 대각화의 핵심 구성 요소
1. **대각 행렬 D**:
   - A의 **고유값(λ)**들이 대각선에 배치됩니다.
   - 예: 고유값이 λ₁, λ₂라면,
     $$
     D = \begin{bmatrix}
     \lambda_1 & 0 \\
     0 & \lambda_2
     \end{bmatrix}
     $$
2. **행렬 X**:
   - A의 **고유벡터**들로 열을 구성합니다.
   - 고유값 순서에 맞춰 고유벡터를 배치해야 합니다.
   - 예: λ₁의 고유벡터가 첫 번째 열, λ₂의 고유벡터가 두 번째 열.
3. **X⁻¹**:
   - X의 역행렬입니다.

### 20.3 대각화가 가능한 조건
- 행렬 A가 **고유한 고유값**을 가지거나,
- **중복된 고유값**이 있더라도, 그에 대응하는 **고유벡터들이 선형 독립**이어야 합니다.

### 20.4 대각화 과정 (2x2 행렬 예시)
예시 행렬:  
$$
A = \begin{bmatrix}
-3 & -4 \\
5 & 6
\end{bmatrix}
$$

1. 고유값 찾기
- 방정식 **det(A - λI) = 0**를 풉니다.
- 계산 결과: 고유값은 **λ₁ = 1**, **λ₂ = 2**.

2. 고유벡터 찾기
- 각 고유값에 대해 **(A - λI)x = 0**를 풀어 고유벡터를 구합니다.
  - **λ₁ = 1**: 고유벡터 = $$\begin{bmatrix}-1 \\ 1\end{bmatrix}$$
  - **λ₂ = 2**: 고유벡터 = $$\begin{bmatrix}-4/5 \\ 1 \end{bmatrix}$$

3. 대각 행렬 D 구성
- 고유값을 대각선에 배치:
  $$
  D = \begin{bmatrix}
  1 & 0 \\
  0 & 2
  \end{bmatrix}
  $$

4. 행렬 X 구성
- 고유벡터를 열로 배치 (고유값 순서에 맞춤):
  $$
  X = \begin{bmatrix}
  -1 & -4/5 \\
  1 & 1
  \end{bmatrix}
  $$

5. X⁻¹ 계산
- X의 역행렬을 구합니다:
  $$
  X^{-1} = \begin{bmatrix}
  -5 & -4 \\
  5 & 5
  \end{bmatrix}
  $$

6. 검증
- **A = X D X⁻¹** 계산:
  $$
  A = \begin{bmatrix}
  -1 & -4/5 \\
  1 & 1
  \end{bmatrix}
  \begin{bmatrix}
  1 & 0 \\
  0 & 2
  \end{bmatrix}
  \begin{bmatrix}
  -5 & -4 \\
  5 & 5
  \end{bmatrix}
  $$
- 결과: 원래 행렬 A = $$\begin{bmatrix}-3 & -4 \\ 5 & 6 \end{bmatrix}$$가 나옵니다.

### 20.5 대각화의 장점
- 행렬 계산이 **단순화**됩니다.
- 컴퓨터로 처리할 때 **효율적**입니다.
- 행렬의 거듭제곱(예: A¹⁰) 계산이 쉬워집니다:
  \[
  A^n = X D^n X^{-1}
  \]
  (D는 대각 행렬이므로 $D^n$은 대각선 값의 n제곱으로 쉽게 계산됩니다.)


## 21. 복소수 행렬이란?
- 출처: [Complex, Hermitian, and Unitary Matrices](https://www.youtube.com/watch?v=DUuTx2nbizM&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=21)


### 21.1 복소수 행렬이란?
- **복소수 행렬**은 행렬의 원소가 **복소수**로 이루어진 행렬입니다.
- **복소수**는 실수 부분과 허수 부분으로 구성됩니다. 예: $ 2 + 3i $ (여기서 $ i $는 $ \sqrt{-1} $).
- 복소수 행렬은 **실수 부분**과 **허수 부분**으로 나눌 수 있습니다.
  - 예: 행렬 $$ \begin{bmatrix} 2+3i & i & 6-4i \\ 7 & 2-3i & -i \end{bmatrix} $$는
    - 실수 부분: $$ \begin{bmatrix} 2 & 0 & 6 \\ 7 & 2 & 0 \end{bmatrix} $$
    - 허수 부분: $$ i \cdot \begin{bmatrix} 3 & 1 & -4 \\ 0 & -3 & -1 \end{bmatrix} $$

### 21.2 복소수 켤레(Complex Conjugate)
- 복소수의 켤레는 허수 부분의 부호를 바꾼 것입니다. 예: $ 2 + 3i $의 켤레는 $ 2 - 3i $.
- 행렬의 켤레는 각 원소의 허수 부분 부호를 바꾼 행렬입니다.
  - 예: $$ \begin{bmatrix} 2+3i & i & 6-4i \\ 7 & 2-3i & -i \end{bmatrix} $$의 켤레는
$$ \begin{bmatrix} 2-3i & -i & 6+4i \\ 7 & 2+3i & i \end{bmatrix} $$

### 21.3 켤레 전치(Conjugate Transpose, Hermitian Transpose)
- **켤레 전치**는 1) 행렬의 켤레를 구하고, 2) 그 결과의 행과 열을 바꾼(전치) 행렬입니다.
- 표기: 행렬 $ M $의 켤레 전치는 $ M^H $ 또는 $ M^\dagger $.
- 왜 중요할까? 켤레 전치를 하면 행렬 곱셈이 가능해집니다(행렬 크기 때문에).
  - 예: $$ \begin{bmatrix} 2+3i & i & 6-4i \\ 7 & 2-3i & -i \end{bmatrix} $$의 켤레 전치는
    1. 켤레: $$ \begin{bmatrix} 2-3i & -i & 6+4i \\ 7 & 2+3i & i \end{bmatrix} $$
    2. 전치: $$ \begin{bmatrix} 2-3i & 7 \\ -i & 2+3i \\ 6+4i & i \end{bmatrix} $$

### 21.4 헤르미트 행렬(Hermitian Matrix)
- **헤르미트 행렬**은 정방 행렬(행과 열의 수가 같은 행렬)로, **자신의 켤레 전치와 동일**한 행렬입니다. 즉, $ M^H = M $.
- 예: $$ \begin{bmatrix} 2 & 1-i \\ 1+i & 3 \end{bmatrix} $$
  1. 켤레: $$ \begin{bmatrix} 2 & 1+i \\ 1-i & 3 \end{bmatrix} $$
  2. 전치: $$ \begin{bmatrix} 2 & 1-i \\ 1+i & 3 \end{bmatrix} $$ (원래 행렬과 동일 → 헤르미트 행렬)

### 21.5 유니타리 행렬(Unitary Matrix)
- **유니타리 행렬**은 열 벡터들이 정규 직교(orthonormal)를 이루는 복소수 정방 행렬입니다.
- 특징: 유니타리 행렬 $ U $의 켤레 전치 $ U^H $는 역행렬 $ U^{-1} $과 같습니다. 즉, $ U^H U = I $ (항등 행렬).
- 예: $$ U = \begin{bmatrix} \frac{i}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} & -\frac{i}{\sqrt{2}} \end{bmatrix} $$
  1. 켤레: $$ \begin{bmatrix} -\frac{i}{\sqrt{2}} & -\frac{1}{\sqrt{2}} \\ \frac{1}{\sqrt{2}} & \frac{i}{\sqrt{2}} \end{bmatrix} $$
  2. 전치: $$ \begin{bmatrix} -\frac{i}{\sqrt{2}} & \frac{1}{\sqrt{2}} \\ -\frac{1}{\sqrt{2}} & \frac{i}{\sqrt{2}} \end{bmatrix} $$
  3. $ U^H U $를 계산하면 $$ \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} $$ (항등 행렬) → 유니타리 행렬 확인!


## 22. 행렬 분해(Matrix Decomposition)
- 출처: [Further Matrix Decompositions: LU, Cholesky, QR, and SVD](https://www.youtube.com/watch?v=wHAJzemKQW4&list=PLybg94GvOJ9En46TNCXL2n6SiqRc_iMB8&index=22)

### 22.1 **LU 분해 (LU Decomposition)**  
**쉽게 말해**: 행렬 $ A $를 두 개의 삼각행렬 $ L $ (하삼각)과 $ U $ (상삼각)로 나누는 방법입니다.  
- **하삼각행렬 $ L $**: 주 대각선 아래만 숫자가 있고, 위는 모두 0인 행렬. 대각선은 1로 고정.  
  $$
  L = \begin{bmatrix}
  1 & 0 & \cdots & 0 \\
  l_{21} & 1 & \cdots & 0 \\
  \vdots & \vdots & \ddots & \vdots \\
  l_{n1} & l_{n2} & \cdots & 1
  \end{bmatrix}
  $$  
- **상삼각행렬 $ U $**: 주 대각선 위만 숫자가 있고, 아래는 모두 0인 행렬.  
  $$
  U = \begin{bmatrix}
  u_{11} & u_{12} & \cdots & u_{1n} \\
  0 & u_{22} & \cdots & u_{2n} \\
  \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & \cdots & u_{nn}
  \end{bmatrix}
  $$  
- **어떻게 구하나요?**  
  1. **가우스 소거법**으로 행렬 $ A $를 상삼각행렬 $ U $로 만듭니다.  
  2. 소거 과정에서 사용한 계수를 반대로 활용해 $ L $을 만듭니다.  
  3. 결과: $ A = L \cdot U $.  
- **활용 예시**:  
  - $ Ax = B $ 같은 방정식을 풀 때 유용합니다.  
  - 직접 역행렬을 구하는 대신, $ L $과 $ U $를 이용해 두 단계로 나눠 계산:  
    1. $ LC = B $를 풀어 $ C $를 구함.  
    2. $ Ux = C $를 풀어 $ x $를 구함.  
  - 삼각행렬은 계산이 쉬워 효율적입니다.

LU분해 예제:
* **문제**: Ax = B를 풀기

$A =$ $$\begin{bmatrix} 2 & 4 \\ 1 & 3 \end{bmatrix}, \quad B = \begin{bmatrix} 10 \\ 7 \end{bmatrix}$$

* **LU 분해**:
$A = LU =$ $$\begin{bmatrix} 1 & 0 \\ 0.5 & 1 \end{bmatrix} \begin{bmatrix} 2 & 4 \\ 0 & 1 \end{bmatrix}$$

* **1단계**: Ly = B 풀기
$$\begin{bmatrix} 1 & 0 \\ 0.5 & 1 \end{bmatrix} \begin{bmatrix} y_1 \\ y_2 \end{bmatrix} = \begin{bmatrix} 10 \\ 7 \end{bmatrix}$$

  - 첫 번째 식: $y_1 = 10$
  - 두 번째 식: $0.5(10) + y_2 = 7$ → $y_2 = 2$

따라서 $y =$ $$\begin{bmatrix} 10 \\ 2 \end{bmatrix}$$

* **2단계**: Ux = y 풀기
$$\begin{bmatrix} 2 & 4 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 10 \\ 2 \end{bmatrix}$$

  - 두 번째 식: $x_2 = 2$
  - 첫 번째 식: $2x_1 + 4(2) = 10$ → $x_1 = 1$

**답**: $x =$ $$\begin{bmatrix} 1 \\ 2 \end{bmatrix}$$


### 22.2 **콜레스키 분해 (Cholesky Decomposition)**  
**쉽게 말해**: LU 분해의 특수한 경우로, 대칭 행렬에 적용됩니다.  
- **조건**: 행렬 $ A $가 대칭이고 양의 정부호(positive definite)여야 합니다.  
  즉, $ A = A^T $ (대칭)이고 모든 고유값이 양수.  
- **어떻게 나누나요?**  
  - $ A = L \cdot L^T $, 여기서 $ L $은 하삼각행렬이고 $ L^T $는 그 전치 행렬(상삼각).  
  $$
  A = \begin{bmatrix}
  a_{11} & a_{12} & \cdots \\
  a_{12} & a_{22} & \cdots \\
  \vdots & \vdots & \ddots
  \end{bmatrix}
  = L \cdot L^T
  $$  
- **활용 예시**:  
  - 행렬의 “제곱근”처럼 사용됩니다.  
  - 통계나 최적화 문제에서 계산 효율성을 높이는 데 유용합니다.


간단한 2x2 예제를 보여드리겠습니다.

**문제**: 대칭 양정부호 행렬 A를 콜레스키 분해

$A =$  $$\begin{bmatrix} 4 & 2 \\ 2 & 5 \end{bmatrix}$$

**콜레스키 분해**: $A = LL^T$ (L은 하삼각행렬)

$L =$ $$\begin{bmatrix} l_{11} & 0 \\ l_{21} & l_{22} \end{bmatrix}$$

**계산 과정**:

$LL^T =$ $$\begin{bmatrix} l_{11} & 0 \\ l_{21} & l_{22} \end{bmatrix} \begin{bmatrix} l_{11} & l_{21} \\ 0 & l_{22} \end{bmatrix} = \begin{bmatrix} l_{11}^2 & l_{11}l_{21} \\ l_{11}l_{21} & l_{21}^2 + l_{22}^2 \end{bmatrix}$$

A와 비교하면:

1. $l_{11}^2 = 4$ → $l_{11} = 2$
2. $l_{11}l_{21} = 2$ → $2 \cdot l_{21} = 2$ → $l_{21} = 1$
3. $l_{21}^2 + l_{22}^2 = 5$ → $1 + l_{22}^2 = 5$ → $l_{22} = 2$

**결과**:
$L =$ $$\begin{bmatrix} 2 & 0 \\ 1 & 2 \end{bmatrix}$$

**검증**:
$LL^T =$ $$\begin{bmatrix} 2 & 0 \\ 1 & 2 \end{bmatrix} \begin{bmatrix} 2 & 1 \\ 0 & 2 \end{bmatrix} = \begin{bmatrix} 4 & 2 \\ 2 & 5 \end{bmatrix} = A$$

**특징**: LU분해와 달리 **하나의 삼각행렬**만 구하면 되고, **대칭 행렬**에만 사용 가능합니다!

### 22.3 **QR 분해 (QR Decomposition)**  
**쉽게 말해**: 행렬 $ A $를 정규 직교 행렬 $ Q $와 상삼각행렬 $ R $로 나누는 방법입니다.  
- **정규 직교 행렬 $ Q $**: 열 벡터들이 서로 직교하고 크기가 1인 행렬.  
  \[
  Q^T \cdot Q = I \quad (\text{단위 행렬})
  \]  
- **상삼각행렬 $ R $**: 위와 동일.  
  $$
  A = Q \cdot R = \begin{bmatrix}
  | & | & \cdots & | \\
  q_1 & q_2 & \cdots & q_n \\
  | & | & \cdots & |
  \end{bmatrix}
  \cdot
  \begin{bmatrix}
  r_{11} & r_{12} & \cdots & r_{1n} \\
  0 & r_{22} & \cdots & r_{2n} \\
  \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & \cdots & r_{nn}
  \end{bmatrix}
  $$  
- **어떻게 구하나요?**  
  1. **그람-슈미트 과정**으로 $ A $의 열에서 정규 직교 벡터 $ Q $를 만듭니다.  
  2. $ Q $를 이용해 $ R = Q^T \cdot A $를 계산.  
- **활용 예시**:  
  - $ Ax = B $를 풀 때, 특히 방정식이 미지수보다 많을 때 (최소 제곱법).  
  - 예: 데이터에 가장 잘 맞는 직선을 찾을 때.  
  - $ Q $의 역행렬이 전치 행렬이므로 계산이 간단:  
    $ Ax = B \rightarrow Rx = Q^T B $.

**간단한 2x2 예제**:

$A =$ $$\begin{bmatrix} 3 & 1 \\ 4 & 2 \end{bmatrix}$$

**그람-슈미트 과정으로 Q, R 구하기**:

**1단계**: 첫 번째 열 정규화
- $\mathbf{a}_1 =$ $$\begin{bmatrix} 3 \\ 4 \end{bmatrix}$$, 크기 = $\sqrt{9+16} = 5$
- $\mathbf{q}_1 = $ $$\frac{1}{5}\begin{bmatrix} 3 \\ 4 \end{bmatrix} = \begin{bmatrix} 0.6 \\ 0.8 \end{bmatrix}$$

**2단계**: 두 번째 열을 첫 번째에 직교하도록 만들기
- $\mathbf{a}_2 =$  $$\begin{bmatrix} 1 \\ 2 \end{bmatrix}$$
- 투영: $\mathbf{a}_2 \cdot \mathbf{q}_1 = 0.6(1) + 0.8(2) = 2.2$
- 직교 성분: $\mathbf{v}_2 = \mathbf{a}_2 - 2.2\mathbf{q}_1 $= $$\begin{bmatrix} 1 \\ 2 \end{bmatrix} - \begin{bmatrix} 1.32 \\ 1.76 \end{bmatrix} = \begin{bmatrix} -0.32 \\ 0.24 \end{bmatrix}$$
- 크기 = $\sqrt{0.32^2 + 0.24^2} = 0.4$
- $\mathbf{q}_2 =$ $$\begin{bmatrix} -0.8 \\ 0.6 \end{bmatrix}$$

**결과**:
$Q =$ $$\begin{bmatrix} 0.6 & -0.8 \\ 0.8 & 0.6 \end{bmatrix}, \quad R = \begin{bmatrix} 5 & 2.2 \\ 0 & 0.4 \end{bmatrix}$$

**검증**:
$QR =$ $$\begin{bmatrix} 0.6 & -0.8 \\ 0.8 & 0.6 \end{bmatrix} \begin{bmatrix} 5 & 2.2 \\ 0 & 0.4 \end{bmatrix} = \begin{bmatrix} 3 & 1 \\ 4 & 2 \end{bmatrix} = A$$ 


### 22.4 **특이값 분해 (SVD, Singular Value Decomposition)**  
**쉽게 말해**: 모든 행렬에 적용 가능한 강력한 분해 방법으로, 대각화를 일반화한 것.  
- **어떻게 나누나요?**  
  - $ A = Q_1 \cdot \Sigma \cdot Q_2^T $  
    - $ Q_1, Q_2 $: 정규 직교 행렬.  
    - $ \Sigma $: 특이값(singular values)이 내림차순으로 대각선에 있는 대각 행렬.  
  $$A = \begin{bmatrix}
  | & | & \cdots \\
  q_1 & q_2 & \cdots \\
  | & | & \cdots
  \end{bmatrix}$$
  $\cdot$
  $$\begin{bmatrix}
  \sigma_1 & 0 & \cdots & 0 \\
  0 & \sigma_2 & \cdots & 0 \\
  \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & \cdots & 0
  \end{bmatrix}$$
  $\cdot$
  $$\begin{bmatrix}- & q_1^T & - \\- & q_2^T & - \\\vdots & \vdots & \vdots \\- & q_n^T & -\end{bmatrix}$$  
- **어떻게 구하나요?**  
  1. $ A \cdot A^T $와 $ A^T \cdot A $의 고유값을 계산.  
  2. 고유값의 제곱근으로 특이값 $ \sigma_i $를 구하고, $ \Sigma $를 만듦.  
  3. $ Q_1 $: $ A \cdot A^T $의 고유벡터, $ Q_2 $: $ A^T \cdot A $의 고유벡터.  
- **활용 예시**:  
  - 행렬의 구조(열 공간, 행 공간, 영공간 등)를 분석.  
  - 데이터 압축, 이미지 처리, 머신러닝(예: 주성분 분석)에서 사용.  
  - $ \Sigma $의 특이값은 행렬의 중요 정보를 나타냄.
  - 어떤 행렬이든 SVD 가능! (정사각행렬이 아니어도 OK)

**간단한 2x2 예제**:

$A =$ $$\begin{bmatrix} 3 & 0 \\ 4 & 5 \end{bmatrix}$$

**1단계**: $A^T A$ 계산 ($Q_2$를 구하기 위해)
$A^T A =$ $$\begin{bmatrix} 3 & 4 \\ 0 & 5 \end{bmatrix} \begin{bmatrix} 3 & 0 \\ 4 & 5 \end{bmatrix} = \begin{bmatrix} 25 & 20 \\ 20 & 25 \end{bmatrix}$$

고유값: λ₁ = 45, λ₂ = 5

고유벡터 정규화:
$Q_2 =$ $$\begin{bmatrix} 1/\sqrt{2} & 1/\sqrt{2} \\ 1/\sqrt{2} & -1/\sqrt{2} \end{bmatrix}$$

**2단계**: 특이값 구하기
$\sigma_1 = \sqrt{45} = 3\sqrt{5} \approx 6.71, \quad \sigma_2 = \sqrt{5} \approx 2.24$

$\Sigma = $ $$\begin{bmatrix} 6.71 & 0 \\ 0 & 2.24 \end{bmatrix}$$

**3단계**: $Q_1$ 구하기 ($Q_1 = AQ_2 Σ^{-1}$)
$Q_1 = $ $$\begin{bmatrix} 0.45 & -0.89 \\ 0.89 & 0.45 \end{bmatrix}$$

**결과**:
$A =$ $$\begin{bmatrix} 0.45 & -0.89 \\ 0.89 & 0.45 \end{bmatrix} \begin{bmatrix} 6.71 & 0 \\ 0 & 2.24 \end{bmatrix} \begin{bmatrix} 0.71 & 0.71 \\ 0.71 & -0.71 \end{bmatrix}$$

### 22.5 요약표

| 분해 방법 | 분해 형태 | 조건 | 주요 활용 |
|-----------|-----------|------|-----------|
| **LU** | $ A = L \cdot U $ | 정방 행렬 | 방정식 풀이, 효율적 계산 |
| **콜레스키** | $ A = L \cdot L^T $ | 대칭, 양의 정부호 | 최적화, 행렬 제곱근 |
| **QR** | $ A = Q \cdot R $ | 모든 행렬 | 최소 제곱법, 직선 피팅 |
| **SVD** | $ A = Q_1 \cdot \Sigma \cdot Q_2^T $ | 모든 행렬 | 데이터 분석, 압축 |

- 특히 **SVD**는 가장 일반적이고 강력한 도구로, 다양한 분야에서 활용됩니다.  
