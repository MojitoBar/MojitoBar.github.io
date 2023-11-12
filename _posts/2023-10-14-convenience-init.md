---
title: convenience init에 대하여
author: mojitobar
date: 2023-10-14 22:52:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, convenience init]
comments: true
---

## 개요

Swift에서 **convenience** 초기화 메서드는 특정 클래스에 대한 보조 초기화 메서드를 제공하는 데 사용되는 메서드에요.

이번에는 `convenience init`이 무엇인지, 그리고 일반적인 init이 있는데 왜 `convenience init`을 사용하는 지에 대해 알아봤어요.

## convenience init

`convenience init`은 보조(편의) 초기화라고 불리는 일종의 초기화 키워드에요.

그리고 `convenience init`은 항상 클래스 내의 주 초기화 메서드를 호출하여 모든 속성이 적절하게 초기화되도록 해야해요.

```swift
class Person {
var name: String
var age: Int

    // 주 초기화 메서드
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    // 편의 초기화 메서드
    convenience init(name: String) {
        self.init(name: name, age: 0) // 주 초기화 메서드를 호출
    }

}

let child = Person(name: "John") // age는 편의 초기화 메서드에 의해 0으로 설정됩니다.
```

위 예제를 보면 같이 보조 초기화 메서드에서 주 초기화 메서드를 호출하고 있는 걸 알 수 있어요.
덕분에 보조 초기화만으로 `child`를 선언해도 `age`가 자동으로 설정되는 것이죠.

그럼 일반적인 `init`만으로도 충분히 구현할 수 있는데 왜 보조 초기화가 필요할까요?

## 보조 초기화가 필요한 이유

보조 초기화는 다양한 프로퍼티를 가진 복잡한 객체에서 장점이 증가해요.

만약 해당 객체에 일부 프로퍼티 값만 변경하거나 기본 값을 설정하는 등의 새로운 초기화가 필요하다면 어떨까요?
나머지 프로퍼티는 기존의 초기화 코드를 똑같이 작성하고 일부만 변경하는 방식으로 작성한다면 분명 중복되는 코드가 작성될 거에요.

그리고 이러한 역할의 초기화가 여러개 필요하다면 더 많은 코드가 중복으로 작성되고 가독성을 떨어트릴 수 있어요.

이때 보조 초기화를 활용하면 더 적은 코드로 쉽게 객체를 생성할 수 있어요.

```swift
class Car {
var brand: String
var model: String
var year: Int
var color: String

    // 주 초기화 메서드
    init(brand: String, model: String, year: Int, color: String) {
        self.brand = brand
        self.model = model
        self.year = year
        self.color = color
    }

    // 보조 초기화: 브랜드와 모델만 설정하고 나머지는 기본값으로 초기화
    convenience init(brand: String, model: String) {
        self.init(brand: brand, model: model, year: 2022, color: "Black")
    }

    // 보조 초기화: 브랜드, 모델, 색상만 설정하고 연도는 기본값으로 초기화
    convenience init(brand: String, model: String, color: String) {
        self.init(brand: brand, model: model, year: 2022, color: color)
    }

}

let car1 = Car(brand: "Toyota", model: "Corolla")
// year는 2022, color는 "Black"으로 설정됩니다.

let car2 = Car(brand: "Honda", model: "Civic", color: "Red")
// year는 2022로 설정됩니다.
```

이런 식으로 보조 초기화를 활용하면 중복되는 코드를 줄이면서 다양한 초기화 방식을 제공할 수 있어요.

## 마무리

Swift에서 객체의 초기화는 매우 중요한 과정입에요. `convenience init`는 객체 초기화를 더욱 편리하게 만들어주며, 코드의 중복성을 줄여 개발자에게 보다 간결하고 명확한 코드를 제공하죠. 앞서 살펴본 예제들처럼 다양한 상황에서 보조 초기화를 사용하면 객체 생성 과정을 간단하게 만들 수 있어요.

하지만 항상 주의해야 할 점은, `convenience init`을 사용하더라도 모든 프로퍼티가 적절하게 초기화되어야 한다는 점이에요. 그렇기 때문에 항상 보조 초기화의 끝에는 주 초기화(Designated initializer)가 있어야해요.
