---
title: 옵셔널 타입 클로저는 왜 escaping이 기본일까?
author: mojitobar
date: 2023-10-09 16:55:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, 클로저, closure, escaping, non-escaping, optional, 옵셔널]
comments: true
---

## 개요

기본 클로저 매개변수는 non-escaping이 기본값이에요.

하지만 옵셔널 타입 클로저 매개변수는 escaping이 기본인데요, 그 이유에 대해 알아보려고해요.

## @escaping

먼저 `@escaping` 키워드의 히스토리에 대해 알아봤어요.

`@escaping` 키워드는 Swift3부터 추가된 키워드에요.

이전에는 클로저가 escaping이 기본값이었으며, `@noescape` 키워드를 이용해 non-escaping 클로저를 선언할 수 있었어요.

하지만 아래와 같은 이유로 Swift3부터는 non-escaping을 기본으로하고, `@escaping` 키워드를 붙이는 식으로 변경되었어요.

1. 기능적 알고리즘의 이점: 순수 스위프트로 작성된 대부분의 함수형 알고리즘은 자연스럽게 non-escaping입에요. 이로 인해 이러한 알고리즘을 작성할 때 필요한 보일러플레이트 코드가 줄어들어요.
2. 컴파일러의 질의 향상: 개발자가 클로저의 escaping에 대해 신경 쓰지 않고 클로저를 escaping 시도할 때, 컴파일러는 이를 감지하고 `@escaping`을 추가하는 것을 제안하는 fixit을 제공할 수 있어요.
3. 언어의 선호도 변경: 최근의 변경사항(예: inout 매개변수에 대한 escaping 클로저의 사용 금지)은 언어가 non-escaping 클로저를 선호하도록 밀어주고 있어요. 또한, non-escaping 클로저는 참조 순환 문제의 한 분류를 제거함으로써 항상 선호되어 왔죠.
4. `@autoclosure(escaping)`의 표준화: `@autoclosure(escaping)`은 `@autoclosure @escaping`으로 더 간단하고 표준화된 형식으로 변환될 수 있어요.

non-escaping을 기본값으로 하면서 `@escaping` 키워드를 추가하자는 자세한 업데이트 내용은 [여기](https://github.com/apple/swift-evolution/blob/main/proposals/0103-make-noescape-default.md)에서 확인할 수 있어요.

그런데 왜 클로저 매개변수를 옵셔널 타입으로 받게되면 클로저는 escaping이 기본값이 될까요?

## 옵셔널

당연할지도 모르지만 이유는 옵셔널로 감쌌기 때문이에요.

클로저의 기본값이 non-escaping이 되는 경우는 클로저 타입이 매개변수로 전달될 때에요. 다른 모든 타입은 escaping이 기본이죠.

옵셔널은 [내부 코드](https://github.com/apple/swift/blob/8b40353e22fdcc75f9bd8c172ee3ce1067f5c810/stdlib/public/core/Optional.swift)를 보면 제네릭으로 구현되어 있음을 볼 수 있어요.

```swift
enum Optional<Wrapped>: ExpressibleByNilLiteral {
case none
case some(Wrapped)

    init(_ some: Wrapped) {
        self = .some(some)
    }

    public init(nilLiteral: ()) {
        self = .none
    }
}
```

즉, 옵셔널 타입 클로저 매개변수는 클로저 타입이 아니라 옵셔널 타입이기 때문에 escaping이 기본으로 동작하게 되는 것이에요.

## 마무리

스위프트는 언어의 업데이트 내용이 공부하기 쉽게 잘 정리되어 있다고 느껴졌어요.

앞으로도 스위프트 업데이트 사항 중 궁금한 점이 있다면 proposals가 큰 도움이 될 것 같아요.

기회가 된다면 스위프트 포럼에 직접 업데이트 내용에 대한 의견도 제시해보려고 해요!
