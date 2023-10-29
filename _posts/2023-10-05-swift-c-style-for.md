---
title: Swift에서 C-스타일 For 문과 증감 연산자가 사라진 배경
author: mojitobar
date: 2023-10-06 21:53:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, C-Style For, 증감 연산자]
comments: true
---

## 개요

Swift의 발전은 몇 가지 핵심 철학에 기반하여 이루어지는데요. Swift3의 업데이트와 함께 주목할 만한 변화 중 하나는 C-스타일의 for 문과 증감 연산자의 제거였어요.

본 글에서는 이러한 변화의 배경과 그 이유에 대해서 알아보겠습니다.

## Swift의 발전 방향

Swift3의 업데이트에 대해 알아보기 전에 Swift 언어가 지향하는 업데이트 방향에 대해 알아보려고 해요.

Swift 언어는 다음과 같은 발전 방향을 지향해요.

- 안전성 (Safety): 옵셔널, 값 타입, 오류 처리, 타입 안정성 등을 통한 안전한 프로그래밍 지향.
- 성능 (Performance): C와 C++ 성능에 근접하거나 뛰어난 성능을 목표로 함.
- 표현력 (Expressivity): 현대적인 문법과 강력한 표준 라이브러리로 명료하고 간결한 코드 작성 지원.
- 자연스러운 문법 (Clarity): 코드의 명료성과 가독성 중시.
- 소스 호환성 (Source Compatibility): 기존 버전과의 코드 호환성을 중요하게 여김.
- 커뮤니티 주도 (Community Driven): 커뮤니티의 참여와 피드백을 중요하게 여기며, Swift Evolution 프로세스를 통한 언어의 발전.
- 이 내용을 인지한 후 업데이트 과정을 살펴보죠.

## C-스타일 For 문과 증감 연산자 제거의 배경

Swift의 업데이트 관련 정보는 Swift-evolution 레포를 통해 자세하게 확인해 볼 수 있어요.

그리고 아래 링크를 통해 확인해보면 C-스타일 For문에 대한 논의 이전에 증감 연산자의 삭제가 먼저 제안된 것을 확인할 수 있어요.

[(증감 연산자-004, c스타일 for문-007)](https://github.com/apple/swift-evolution/tree/main/proposals)

증감 연산자를 제거하고자 한 이유는 다음과 같아요.

- x++는 x+=1보다 코드가 짧지 않으며, 가독성 역시 떨어진다.
- 전위와 후위에 따른 작동 방식의 차이로 인해 혼란과 실수가 쉽게 일어난다.
- 잘 사용되지 않는 연산자이다.
- Swift 개발 초기에 고려 없이 C에서 이월된 레거시 요소라 볼 수 있다.

이렇게 증감 연산자를 제거하자고 제안하고 나니 자연스래 C-스타일 for 문의 제거 논의도 진행되었어요.

가장 많이 증감 연산자를 사용하던 구문이었기 때문이죠.

## C-스타일 For 문 제거 이유

해당 [링크](https://github.com/apple/swift-evolution/blob/main/proposals/0007-remove-c-style-for-loops.md#remove-c-style-for-loops-with-conditions-and-incrementers)를 통해 C-스타일 For문을 제거하고자한 이유를 확인할 수 있어요.

그리고 그 이유는 아래와 같아요.

- 증감연산자가 없어진다면, 그 연산자를 주로 사용하던 C-스타일 for 문의 사용이 어려워진다.
- C-스타일 for문은 C언어의 잔재로 볼 수 있으며, for-in 구문이나 stride 메소드로 대체가 가능하다.
- for-in 구문과 stride가 Swift의 철학에 더 어울린다.
- Apple Swift 코드베이스 검사 결과 이 구문이 거의 사용되지 않았다는 것이 밝혀짐.

C계열의 언어를 먼저 접하신 분들이 처음 Swift를 접하게 된다면, Swift에서 C-스타일 For문을 지원하지 않는다는 점이 불편하게 느껴질 수도 있을거에요. 제가 그랬거든요.

하지만 이런 업데이트를 진행한데는 위와 같이 굉장히 합리적인 이유와 디테일한 근거들이 있어요.
이러한 제안들은 나름 치열한 논의를 바탕으로 진행되었고, Swift3 버전에서 제거되었어요.

## 마무리

Swift의 발전은 안전성, 성능, 표현력, 명료성, 소스 호환성, 그리고 커뮤니티 주도라는 핵심 철학 아래 이루어진다는 것을 직접 확인해 볼 수 있는 시간이었어요.

특히 커뮤니티가 정말 잘 되어있고, 논의 과정과 결론에 대한 정리가 너무 깔끔해서 보기 편했어요.

이러한 내용을 알고 Swift를 공부하니 언어에 더 애정이 생기는 것 같아요.