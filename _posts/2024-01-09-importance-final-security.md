---
title: 보안을 위한 final 키워드의 중요성
author: mojitobar
date: 2024-01-09 18:22:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, final, security]
comments: true
---

# 들어가기에 앞서

해당 글은 [The importance of 'final' for security](https://blog.encoded.life/the-importance-of-final-for-security) 글을 번역한 글입니다.

흥미로운 내용이 포함되어 있는 것 같아서 번역하게 되었습니다.

# 보안을 위한 final 키워드의 중요성

Swift에서 class나 actor을 만들 때, 만약 어떤 함수도 override하지 않기를 의도한다면 `final` 키워드를 사용해야 합니다.

왜 이것이 중요한지 여기 예시를 보겠습니다.

```swift
class Hello {
    func world() -> String {
        "Hello, world!"
    }
}

class Hack {
    func notSoFast() -> String {
        "Gotcha"
    }
}

final class SafeHello {
    func world() -> String {
        "Hello, world!, obviously!"
    }
}

let hello = Hello()
object_setClass(hello, Hack.self)
hello.world() // "Gotcha"

let safeHello = SafeHello()
object_setClass(safeHello, Hack.self)
safeHello.world() // "Hello, world!, obviously!"
```

여기서 사용된 [`object_setClass`](https://developer.apple.com/documentation/objectivec/1418905-object_setclass)는 런타임에 객체의 클래스를 변경하는 함수입니다.

공격자가 함수 이름을 맞출 필요조차 없었다는 점에 주목하세요.

스위프트는 final 클래스가 아닌 클래스에 대해 동적 조회 테이블을 사용하여 함수의 구현이 있는 위치를 식별합니다.

`world()`는 Hello 클래스의 첫 번째 함수이고 `notSoFast()`는 Hack 클래스의 첫 번째 함수입니다.

만약 hello 인스턴스가 Hack으로 변경되고 `world()` 함수를 호출하면 동적 조회 테이블은 Hack 클래스의 첫 번째 함수를 찾아 `world()` 대신 `notSoFast()`를 실행하게 됩니다.

final 키워드를 사용하면 스위프트는 `world()`의 구현을 직접 참조하고 동적 테이블 조회를 건너뜁니다.

즉, 공격자로부터 코드를 더 안전하게 보호할 수 있습니다.

# Whole Module Optimization

사실 이 트릭은 스위프트 Whole Module Optimization에 의해 대부분 방지됩니다.

라이브러리와 애플리케이션의 빌드 설정이 올바르다면 `final` 키워드의 역할은 대부분 다른 개발자가 클래스를 확장하지 못하도록 하는 것입니다.

왜냐하면 스위프트 컴파일러는 Whole Module Optimization 빌드 중에 non-open 클래스에 대해 자동으로 final 키워드를 추가하기 때문입니다. [1) swift forums](https://forums.swift.org/t/final-optimization-recommendations/18835) [2) samwize's blog](https://samwize.com/2023/12/15/should-you-add-final-to-all-your-swift-classes/#:~:text=To%20optimize%20runtime%20This%20optimization,Need%20NOT%20add%20for%20private)
