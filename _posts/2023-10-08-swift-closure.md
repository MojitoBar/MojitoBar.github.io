---
title: 스위프트 클로저에 대해서
author: mojitobar
date: 2023-10-08 14:59:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, 클로저, closure, 탈출클로저, escaping, non-escaping]
comments: true
---

## 개요

스위프트에서 일반 클로저와 탈출 클로저에 대한 내용과 더불어 사용시 주의사항에 대해 정리해보려해요.

## 클로저란?

Swift에서 클로저는 간단히 말하면 이름 없는 함수 또는 함수처럼 동작하는 코드 블럭이에요.

클로저는 함수와는 다르게 주변의 변수나 상수를 캡쳐할 수 있어서 그 변수나 상수의 값을 클로저 안에서 사용하거나 수정할 수 있어요.

클로저는 크게 두 가지 유형으로 분류할 수 있는데, escaping 클로저와 non-escaping 클로저에요.

### Escaping 클로저 (Escaping Closure)

escaping 클로저는 함수가 종료된 후에 실행될 수 있는 클로저를 의미해요. 다시 말해, 해당 클로저는 함수의 실행 범위를 탈출 해서 나중에 호출될 수 있어요.
비동기 작업에서는 함수가 끝나고 나서도 클로저가 실행되어야 하는 경우가 많아요. 예를 들면 네트워킹 호출이나 타이머, 애니메이션 완료 후 작업 등에서 자주 볼 수 있죠.

클로저를 변수나 프로퍼티로 저장하려는 경우에도 탈출 클로저를 사용해야 해요.
함수의 매개변수 타입으로 클로저를 사용할 때 @escaping 어노테이션을 사용하면 돼요.

```swift
class ViewModel {
    var completionhandler: (() -> Void)? = nil

    func fetchData(completion: @escaping () -> Void) {
        completionhandler = completion
    }
}
```

### Non-Escaping 클로저 (Non-Escaping Closure)

기본적으로 모든 클로저는 non-escaping에요. 따라서, 특별히 @escaping을 명시하지 않으면 non-escaping 클로저로 간주돼요.

함수의 실행 범위 내에서만 실행되고 함수가 종료되면 사라지는 클로저에요.

```swift
func performOperation(with number: Int, operation: (Int) -> Int) -> Int {
    return operation(number)
}
```

non-escaping 클로저와는 달리, escaping 클로저의 경우 클로저가 함수의 실행 범위를 탈출하기 때문에 캡쳐링하는 변수나 상수의 메모리 관리에 주의해야 해요.

## 클로저 사용시 주의 사항

그 전에 순환 참조에 대해 간단하게 정리하자면, 순환 참조는 두 개 이상의 객체가 서로를 강하게 참조하게 되어 가비지 컬렉션 또는 ARC (Automatic Reference Counting)의 메모리 관리 메커니즘이 객체들을 메모리에서 해제할 수 없게 되는 상황을 말해요.

Swift에서는 ARC를 통해 메모리 관리가 이루어지기 때문에, 순환 참조는 메모리 누수를 초래하게 되죠.

이 때문에 self를 클로저 안에서 사용할 경우 weak 또는 unowned를 사용하여 참조 순환을 방지하는 것이 중요해요.

```swift
class SimpleClass {
var closure: (() -> Void)?
    init() {
        closure = {
            self.doSomething()
        }
    }
    func doSomething() {
        print("Doing something")
    }
    deinit {
        print("SimpleClass is being deinitialized!")
    }
}

func testRetainCycle() {
    var instance: SimpleClass? = SimpleClass()
    instance?.closure?()
    instance = nil
}

testRetainCycle()
```

위 예제를 보면 instance 객체가 nil이 되어도 deinit 이 호출되지 않는 것을 확인할 수 있는데, 이는 SimpleClass 초기화 시 자기자신을 참조하는 클로저가 존재하기 때문이에요.

실제로 instance의 closure 프로퍼티를 nil로 만들고 instance를 nil로 만들면 deinit 이 정상적으로 잘 동작하죠.

하지만 객체 내부의 프로퍼티를 일일이 메모리에서 해제해주는 것은 매우 번거로운 일이에요.
그냥 instance객체가 nil이 되면 알아서 내부 프로퍼티들도 메모리에서 해제되게 할 수 없을까요?

이러한 문제를 해결하는 참조 키워드가 바로 weak, unowned이에요.

## weak

weak 참조는 항상 변수의 타입이 옵셔널이어야 해요. (var reference: ClassType?)
weak로 선언된 참조는 참조하는 객체가 메모리에서 해제될 때 자동으로 nil로 설정돼요.

## unowned

unowned 참조는 옵셔널이 아닌, non-optional 타입을 사용해요. (var reference: ClassType)

unowned로 선언된 참조는 참조하는 객체가 메모리에서 해제되어도 nil로 설정되지 않아요. 이 때문에 참조하려는 객체가 해제된 후에는 런타임 크래시가 발생할 수 있어요.
그러므로 unowned 참조를 사용할 때에는 참조하는 객체의 생명 주기가 현재 객체보다 길거나 같음을 보장해야 해요.

unowned 키워드가 좀 특이했는데, weak 키워드처럼 직접적으로 순환 참조를 방지하기 보단 참조한 객체가 해제되었을 때 접근하려하면 크래시를 발생시켜 좀 더 안전하게 객체를 참조하게 도와주는 역할을 하는 것 같았어요.

보통의 상황에서는 weak을 사용하겠지만, 옵셔널 처리를 피하고 싶거나 두 객체의 생명 주기가 명확할 때 사용해볼법 한 것 같아요.

## 마무리

정리하자면 escaping 클로저 는 함수 밖으로 탈출이 가능한 클로저에요.

(일반 클로저의 경우 실행 범위가 함수 내부) 그리고 크게 두 가지 경우에 클로저에 escaping 키워드를 붙여주어야 해요.

1. 함수 내부에 비동기 코드가 있고 그 안에서 클로저가 실행되어야 할 때
2. 클로저를 다른 변수에 할당해야할 때

왜냐하면 일반 클로저는 함수 내에서, 직접 실행을 위해서만 사용해야 하기 때문이에요.

하지만 escaping 키워드를 붙이게 되면 함수 밖에서 혹은 다른 변수에 할당이 가능해져요.

| 그러면 탈출 클로저가 디폴트로 제공되면 더 좋은 것 아닌가요??

꼭 그렇지는 않아요.

1. 코드의 의도를 명확히 함: 클로저의 탈출성 여부를 지정함으로써 함수의 호출자에게 해당 클로저가 함수의 생명주기를 벗어나서 호출될 수 있는지 여부를 명확하게 알려줘요.
2. 컴파일러 최적화: non-escaping 클로저는 해당 함수의 실행 도중에만 실행되기 때문에 컴파일러는 메모리 할당 및 참조 관련 최적화를 할 수 있어요.
3. 순환 참조 방지: 보통 클로저에서 self나 다른 객체를 참조해야 할 때는 함수 외부에서 실행되어야하기 때문인 경우가 많아요. (탈출 클로저에서 순환 참조가 일어날 경우가 많다는 뜻)

따라서 non-escaping 와 escaping 을 분리하여 이러한 위험성에 대해 경계하기 위함이에요.
위와 같은 이유로 스위프트에서는 클로저의 종류를 2개로 분리해서 지원해요.

관련된 자료를 찾다보니 특히나 탈출 클로저에서 순환 참조의 위험을 강조하는 듯 했어요.

아무래도 실제 어플리케이션을 개발하다보면 훨씬 많은 비율을 차지하기 때문이 아닌가 싶어요.
