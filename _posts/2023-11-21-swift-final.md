---
title: final키워드를 붙이면 생기는 이점
author: mojitobar
date: 2023-11-21 16:49:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, final, OptimizationTips]
comments: true
---

## final 키워드

swift에서 final 키워드는 상속과 오버라이드를 막는다는 특징이 있다고만 알고 있었는데, 이를 통해 성능 상의 이점도 챙길 수 있다는 점을 알게 되었습니다.

이번에는 final 키워드에 대해 좀 더 깊게 알아봤습니다.

## OptimizationTips

관련 자료를 찾아보던 중 스위프트 공식 문서 중 성능 최적화와 관련된 문서를 발견했습니다.

[https://github.com/apple/swift/blob/main/docs/OptimizationTips.rst](https://github.com/apple/swift/blob/main/docs/OptimizationTips.rst#advice-use-private-and-fileprivate-when-declaration-does-not-need-to-be-accessed-outside-of-file)

그 중에서도 final와 관련된 부분은 Reducing Dynamic Dispatch에서 찾아볼 수 있었습니다.

## Methods Dispatch

final 키워드가 성능 최적화에 영향을 미치는 이유에 대해 이해하기 위해서는 먼저 swift의 메소드 디스패치에 대해 이해해야 합니다.

스태틱 디스패치(static dispatch)와 다이내믹 디스패치(dynamic dispatch)입니다.

1. 스태틱 디스패치 (Static Dispatch)
   - 이 방식에서는 메소드 호출이 컴파일 시점에 결정됩니다.
   - 컴파일러는 호출할 정확한 메소드의 주소를 알고 있으며, 이를 인라인(inline)화할 수 있습니다. 인라인화는 해당 함수의 코드를 호출 지점에 직접 삽입하는 것을 의미합니다. 이 과정은 런타임에 함수 호출의 오버헤드를 줄이고 성능을 향상시킵니다.
   - 스위프트의 구조체(structs)와 열거형(enums), 그리고 `final`로 선언된 클래스 메소드는 스태틱 디스패치를 사용합니다.
2. 다이내믹 디스패치 (Dynamic Dispatch):
   - 다이내믹 디스패치는 런타임에 메소드 호출이 결정됩니다.
   - 이는 메소드가 오버라이드될 수 있기 때문에 필요합니다. 컴파일러는 호출해야 할 정확한 메소드를 런타임에 결정하기 위해 추가적인 작업을 수행해야 합니다.
   - 이 과정은 유연성을 제공하지만, 추가적인 오버헤드를 발생시키며 성능에 영향을 줄 수 있습니다.
   - 스위프트의 클래스는 기본적으로 다이내믹 디스패치를 사용합니다.

## 다시 돌아가서

돌아가서 공식 문서를 보면 다음과 같은 내용을 볼 수 있습니다.

> 권고: 선언을 재정의할 필요가 없는 경우 'final'을 사용하세요.
>
> final 키워드는 클래스, 메서드 또는 프로퍼티의 선언을 재정의할 수 없도록 선언을 제한하는 키워드입니다. 이는 컴파일러가 간접 호출 대신 직접 함수 호출을 내보낼 수 있음을 의미합니다.
>
> 예를 들어 다음에서 C.array1과 D.array1은 직접 액세스됩니다. 반대로 D.array2는 vtable을 통해 호출됩니다.
>
> ```swift
> final class C {
>   // No declarations in class 'C' can be overridden.
>   var array1: [Int]
>   func doSomething() { ... }
> }
>
> class D {
>   final var array1: [Int] // 'array1' cannot be overridden by a computed property.
>   var array2: [Int]      // 'array2' *can* be overridden by a computed property.
> }
>
> func usingC(_ c: C) {
>   c.array1[i] = ... // Can directly access C.array without going through dynamic dispatch.
>   c.doSomething()   // Can directly call C.doSomething without going through virtual dispatch.
> }
>
> func usingD(_ d: D) {
>   d.array1[i] = ... // Can directly access D.array1 without going through dynamic dispatch.
>   d.array2[i] = ... // Will access D.array2 through dynamic dispatch.
> }
> ```

여기서 말하는 vtable이 다이나믹 디스패치와 관련이 깊은 부분입니다.

## vtable

`vtable`은 가상 메소드 테이블로, 클래스에 있는 가상 함수(오버라이드 가능한 메소드)의 주소를 저장하는 테이블입니다.

각 클래스 인스턴스는 이 `vtable`에 대한 포인터를 가지고 있어, 해당 클래스의 메소드들이 어디에 있는지 알 수 있습니다.

`vtable` 의 동작 방식은 다음과 같습니다.

- 클래스가 인스턴스화될 때, 해당 클래스와 관련된 `vtable`의 복사본이 생성됩니다.
- 이 테이블은 클래스의 각 가상 함수에 대한 주소를 포함합니다.
- 메소드를 호출할 때, 프로그램은 `vtable`을 참조하여 해당 메소드의 실제 주소를 찾고, 그 메소드를 실행합니다.
- 만약 서브클래스에서 메소드가 오버라이드되면, `vtable`은 오버라이드된 메소드의 주소를 가리키도록 업데이트됩니다.

이렇게 `vtable`을 사용하는 다이내믹 디스패치는 유연성과 확장성을 제공하지만, 추가적인 런타임 오버헤드를 발생시킵니다. 각 메소드 호출에는 `vtable`을 조회하는 과정이 필요하며, 이는 성능 저하의 원인이 될 수 있습니다.

## 결론

`final` 키워드를 사용하면, 해당 클래스나 메소드가 더 이상 상속이나 오버라이드되지 않음을 컴파일러에 알립니다. 이는 컴파일러가 메소드 호출을 스태틱 디스패치로 처리할 수 있게 하여, 인라인화를 가능하게 합니다.

결과적으로, 메소드 호출 과정에서 발생할 수 있는 런타임 오버헤드가 줄어들어, 애플리케이션의 전반적인 성능이 향상됩니다.

반면 오버라이드가 가능한 메서드의 경우 vtable을 통해 해당 메소드의 주소를 찾게 되기 때문에 추가적인 런타임 오버헤드를 발생시킵니다.

따라서 스위프트 공식 문서 (성능 최적화 편)에서도 언급한 것과 같이 재정의 할 필요가 없는 클래스의 경우 final을 붙이는게 성능 상 이점을 챙길 수 있습니다.
