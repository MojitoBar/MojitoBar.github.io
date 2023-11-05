---
title: protocol, extension에서 stored property사용하는 방법
author: mojitobar
date: 2023-10-12 09:49:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, protocol, extension, stored property]
comments: true
---

## 개요

프로토콜과 익스텐션에서는 저장 프로퍼티를 사용할 수 없고, 계산 프로퍼티를 사용해야해요.

그럼에도 불구하고 저장 프로퍼티를 사용하고 싶다면 어떻게 할 수 있을까요?

이번에는 프로토콜과 익스텐션에서 저장 프로퍼티와 같은 효과를 볼 수 있는 방법에 대해 알아봤어요.

## protocol, extension에서의 stored property(저장 프로퍼티)

먼저 프로토콜과 익스텐션에서는 왜 저장 프로퍼티를 사용할 수 없을까요?

- 프로토콜은 타입의 행위를 정의하는 추상화 도구에요. 이러한 프로토콜이 저장 프로퍼티를 가진다는 것은 프로토콜이 어떤 상태를 가져야 한다는 구체적인 구현 세부 사항을 강요하게 되므로 본래의 목적에 맞지 않아요.

- 익스텐션은 기존 타입에 새로운 기능을 추가하는 도구에요. 만약 저장 프로퍼티를 익스텐션에 추가할 수 있다면 이미 인스턴스화된 객체에 대해 어떻게 그 속성의 메모리가 할당되고 관리되는지에 대한 문제가 발생할 수 있어요.

- 이미 선언되어 있는 Int, String 등의 객체에도 저장 프로퍼티를 추가할 수 있게 되는 등 예측 불가능한 코드가 될 수 있어요.

이러한 이유들로 프로토콜과 익스텐션에서 저장 프로퍼티를 사용할 수 없어요.

그래도 저장 프로퍼티를 사용하고 싶다면 어떻게 해야할까요?

## associated object

[AssociatedObject](https://developer.apple.com/documentation/objectivec/1418509-objc_setassociatedobject)는 오브젝티브-C에서 제공하는 기능이에요.

이를 사용하면 기존 클래스에 동적으로 속성을 추가할 수 있는데, 이 기능을 활용하여 프로토콜과 익스텐션에서 저장된 속성의 효과를 흉내낼 수 있어요.

```swift
private var nameKey: UInt8 = 0

extension PropertyStoring {
    var name: String {
        get {
            return objc_getAssociatedObject(self, &nameKey) as? String ?? "Default Name"
        }
            set(newValue) {
            objc_setAssociatedObject(self, &nameKey, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
    }
}

let myInstance = MyClass()
myInstance.name = "ChatGPT"
print(myInstance.name) // 출력: ChatGPT
```

위 예제 코드를 보면 MyClass 인스턴스에서 name을 저장 프로퍼티처럼 사용하는 것을 확인할 수 있어요.
이처럼 Associated objects를 사용하면 저장 프로퍼티와 같은 동작을 흉내낼 수 있어요. 하지만, 실질적으로는 저장 프로퍼티가 아니에요.

내부적으로 Associated objects는 해당 객체와 연결된 별도의 key-value 저장소에서 값을 유지해요.

그리고 objc_setAssociatedObject 함수의 마지막 매개변수인 policy를 통해 저장되는 값의 [메모리 관리 방식](https://developer.apple.com/documentation/objectivec/1418509-objc_setassociatedobject)을 지정할 수 있어요.

결과적으로 저장 프로퍼티와는 다르게, name의 데이터가 객체의 메모리 레이아웃에 직접적으로 포함되지 않죠.
대신, Objective-C 런타임이 제공하는 별도의 메커니즘을 통해 객체와 연관된 값을 저장하고 검색해요.

## 마무리

위에서 언급한 이유로 저장 프로퍼티를 사용하는 것은 불가능 한데 왜 저장 프로퍼티를 사용하려 하는 걸까요?

왜냐하면 프로토콜이나 익스텐션에 저장 프로퍼티가 있으면 여러모로 편리한 경우가 있기 때문이에요.

예를 들어 UIScrollView 클래스를 확장해서 마지막 y좌표를 저장할 프로퍼티를 만들고, 스크롤 방향 확인하기 또는 UIButton 클래스를 확장해서 토클 상태를 저장할 프로퍼티를 추가하기 등이 그 예시에요.
