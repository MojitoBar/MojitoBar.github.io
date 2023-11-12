---
title: 스위프트에서 옵셔널을 처리하는 방법
author: mojitobar
date: 2023-10-13 23:25:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, optional]
comments: true
---

## 개요

스위프트에서는 옵셔널을 처리하는 여러가지 방법을 제공해요.

어떤 방법들이 있고 각각 어떻게 사용하는지, 언제 사용하면 좋을지 간단하게 정리해봤어요.

## 옵셔널 바인딩 (Optional Binding)

```swift
if let unwrappedValue = optionalValue {
    print("Value is \\(unwrappedValue)")
} else {
    print("No value")
}

guard let value = optionalValue else {
    print("No value")
    return
}
```

`if-let`, `guard-let`으로 사용하는 옵셔널 바인딩은 옵셔널에 값이 있는지 확인하고 그 값을 일반 변수나 상수로 사용하고 싶을 때 주로 사용해요. 또는 값이 있거나 없을 때 특정 코드 블럭을 실행하기 위해 사용하기도 해요.

## 옵셔널 체이닝 (Optional Chaining)

```swift
let value = object?.property?.method()
```

객체 내부에 옵셔널 프로퍼티가 있고 그 내부에 옵셔널 프로퍼티가 있을 때, 즉 여러 옵셔널을 연결해서 접근해야 할 때 사용할 수 있어요.

옵셔널 체이닝을 사용하게 되면 여러 옵셔널 중 하나가 `nil`일 경우 이후 프로퍼티에 접근하지 않고 바로 `nil`을 반환하게 돼요.

일일이 옵셔널을 해제하지 않고 한 줄로 처리할 수 있다는 장점이 있어요.

## 강제 언래핑 (Forced Unwrapping)

```swift
let unwrappedValue = optionalValue!
```

`!`를 사용해서 옵셔널을 강제로 해제할 수도 있어요. 옵셔널에 반드시 값이 있을 것이라고 확신할 때 사용하면 불필요한 옵셔널 해제 코드를 생략할 수 있어요.

하지만 잠재적으로 오류가 발생할 수 있는 코드라는 것은 변하지 않아요.

그렇기 때문에 오히려 일부러 오류를 발생하고 싶을 때 사용할 수도 있어요. 예를 들어 개발 도중 특정 부분에서 오류를 발생시켜 문제를 빠르게 파악할 수도 있죠.

하지만 퍼블리싱 환경에서는 최대한 사용하지 않는 것이 좋아요.

## 병합 연산자 (Nil Coalescing Operator)

```swift
let value = optionalValue ?? defaultValue
```

`??`를 사용해서 옵셔널에 값이 없을 경우를 처리할 수 있어요.

옵셔널 변수가 비어있을 경우 기본 값을 제공하면 되는 경우에 다른 언래핑 코드보다 간단하게 처리할 수 있어요.

## 마무리

이렇게 총 4가지의 옵셔널 해제 방법에 대해 알아봤어요.

무조건 좋고 나쁘고가 없이 모두 적절한 상황에서 사용한다면 각자의 장점을 누릴 수 있을 것 같아요.

특히 강제 언래핑의 경우 무조건 사용하지 않는게 좋다라고만 알고 있었는데, 사실 그렇다면 스위프트에서 지원하지 않는게 맞겠죠?

이 방법도 특정한 상황에서 잘 활용하면 유용하게 사용할 수 있다는 점을 알게되었어요.
