---
title: 6차시 10:Fundamentals of quantum algorithms(Shore Algorithm)
layout: single
classes: wide
categories:
  - Fundamentals of quantum algorithms
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---
# 쇼어 알고리즘

- 출처: [Shor's algorithm](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/phase-estimation-and-factoring/shor-algorithm)

이제 정수 인수분해 문제에 집중하여 위상 추정을 사용하여 양자 컴퓨터에서 이 문제를 어떻게 효율적으로 해결할 수 있는지 살펴보겠습니다. 우리가 얻을 알고리즘은 쇼어(Shor)의 정수 인수분해 알고리즘입니다. 쇼어는 자신의 알고리즘을 위상 추정의 관점에서 구체적으로 설명하지는 않았지만, 이 알고리즘의 작동 방식을 설명하는 자연스럽고 직관적인 방법입니다.

우선, 차수 찾기 문제(order-finding problem)라는 중간 문제를 논의하고 위상 추정이 이 문제에 어떻게 해를 제공하는지 살펴보겠습니다. 그런 다음, 차수 찾기 문제에 대한 효율적인 해가 어떻게 정수 인수분해 문제에 대한 효율적인 해를 제공하는지 살펴보겠습니다. (이와 같이 한 문제에 대한 해가 다른 문제에 대한 해를 제공할 때, 두 번째 문제가 첫 번째 문제로 환원된다고 합니다. 즉, 이 경우 정수 인수분해를 차수 찾기로 환원하는 것입니다.) 쇼어 알고리즘의 두 번째 부분은 양자 컴퓨팅을 전혀 사용하지 않습니다. 완전히 고전적인 방식입니다. 양자 컴퓨팅은 차수 찾기 문제를 해결하는 데만 필요합니다.

