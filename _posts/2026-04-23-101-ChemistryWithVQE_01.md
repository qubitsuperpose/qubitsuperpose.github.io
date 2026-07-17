---
title: 15차시 1:Quantum Chemistry with VQE 1
layout: single
classes: wide
categories:
  - Chemistry with VQE
toc: true # 이 포스트에서 목차를 활성화
toc_sticky: true # 목차를 고정할지 여부 (선택 사항)
---


# 1. 소개

이 과정은 변분 양자 고유값 solver(VQE, variational quantum eigensolver)에 대한 집중 입문 과정이며, 화학 분야에 대한 VQE의 응용도 포함합니다. 이 과정은 양자 컴퓨팅을 화학, 생화학, 또는 생물물리학 문제에 적용하는 데 관심이 있는 양자 컴퓨팅 기술 실무자를 대상으로 합니다. 이 과정은 다음을 포함하여 VQE 계산의 중요한 요소들을 다룹니다:

* 해밀토니안(Hamiltonian)
* 앤자츠(Ansatz)
* 고전 최적화기(Classical optimizer)

이 모든 요소들은 함께 결합되어 Qiskit Runtime Primitives, 주로 Estimator를 사용한 VQE 계산을 구축하는 데 사용됩니다. 프리미티브(primitives)에 대한 정보는 [문서](https://quantum.cloud.ibm.com/docs/guides/primitives)를 참고하세요. VQE 계산을 설정할 때, 우리는 표준 Qiskit 패턴(patterns) 프레임워크를 따를 것입니다:

* 고전적 입력을 양자 문제로 매핑
* 양자 실행을 위한 문제 최적화
* Qiskit Runtime 프리미티브를 사용한 실행
* 후처리, 결과를 고전적 형식으로 반환

프레임워크에 대한 설명은 이 과정 전반에 걸쳐 등장하지만, 예제와 함께 Qiskit 패턴에 대한 완전한 설명을 보려면 [이 문서](https://quantum.cloud.ibm.com/docs/guides/intro-to-patterns)를 참고하세요.

이 과정을 완료하는 데 약 6-10시간을 할애할 것을 권장합니다. 영상 시청과 읽기 자료는 완료하는 데 약 2시간이 걸릴 것입니다. 나머지 시간은 코드 셀을 실행하고 수정하며, 퀴즈 문제를 완료하고, 링크된 문서를 읽는 데 사용됩니다.

IBM Quantum®은 VQE와 관련된 다른 역량 강화(upskilling) 프로그램들도 제공합니다. 만약 이미 VQE의 구성 요소들에 익숙하며 단순히 이를 실습에 적용하고자 한다면, 저희 튜토리얼을 참고하세요: [VQE를 이용한 하이젠베르크 체인(Heisenberg chain)의 바닥상태 에너지 추정](https://quantum.cloud.ibm.com/docs/tutorials/spin-chain-vqe).

## VQE 개요

IBM Quantum Americas의 Quantum Innovation Centers 리드인 Victoria Lipinska 박사가 발표하는 개요 영상을 시청하며 VQE 공부를 시작해봅시다.

VQE가 무엇인지, 무엇을 할 수 있는지, 그리고 왜 다른 양자 컴퓨팅 알고리즘들보다 단기적으로 유용할 가능성이 높은지 배우게 될 것입니다.

<iframe width="640" height="360" src="https://video.ibm.com/embed/recorded/132414895" scrolling="no" allowfullscreen webkitallowfullscreen frameborder="0" style="border: 0 none transparent;"></iframe>

**참고 자료**

다음 문서들이 위 영상에서 참조되었습니다.

* [변분 양자 고유값 해법은 어떤 문제를 해결하는가?](https://pyqml.medium.com/what-problem-does-a-variational-quantum-eigensolver-solve-3b91c47af23b)
* [변분 양자 고유값 해법에 대한 종합 가이드](https://pyqml.medium.com/the-comprehensive-guide-to-the-variational-quantum-eigensolver-ee6775e8279e)
* [VQE로 해결된 문제들의 예시](https://medium.com/qiskit/the-variational-quantum-eigensolver-43f7718c2747)
* [VQE 방법: 간단한 조사와 최근 발전 동향, Fedorov 외](https://materialstheory.springeropen.com/articles/10.1186/s41313-021-00032-6)
