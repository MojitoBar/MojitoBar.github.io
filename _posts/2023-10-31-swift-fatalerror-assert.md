---
title: fatalError와 assert 알아보기
author: mojitobar
date: 2023-10-31 14:24:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, fatalError, assert]
comments: true
---

## fatalError, Assert

`fatalError`와 `assert`는 Swift에서 실행 중에 발생할 수 있는 오류나 예상치 못한 상황을 검사하고 대응할 수 있는 메커니즘이며, 두 기능은 다음과 같은 특징과 용도를 가지고 있습니다

### fatalError

`fatalError`는 프로그램을 즉시 종료시키는 함수입니다.

보통 코드에서 절대 도달해서는 안 되는 위치나 처리할 수 없는 오류 상황에서 사용됩니다.

```swift
func unimplementedMethod() {
    fatalError("This method is not yet implemented.")
}
```

위의 함수는 호출되면 오류 메시지를 출력하고 앱을 종료합니다.

### assert

`assert` 함수는 런타임 중에 특정 조건이 참인지 확인하는데 사용됩니다. 조건이 거짓일 경우, `assert`는 실패 메시지를 출력하고 디버그 빌드에서만 프로그램을 중지시킵니다.

릴리스 빌드에서는 `assert`문이 무시됩니다.

- `assert`는 런타임 중에 코드의 특정 조건이 참인지 확인하기 위해 주로 사용됩니다.
- 디버그 목적으로 사용되며, 개발 중에 발견된 오류를 디버그하기 쉽게 만들어줍니다.

```swift
func divide(_ numerator: Int, by denominator: Int) -> Int {
    assert(denominator != 0, "Denominator should not be zero.")
    return numerator / denominator
}
```

위의 함수는 분모가 0이 아닌지 확인하고, 0일 경우 디버그 빌드에서 앱을 중지시킵니다.

### 차이점

- `fatalError`: 조건과 관계없이 프로그램을 항상 종료시키며, 디버그 및 릴리스 빌드 모두에서 동작합니다.
- `assert`: 조건이 거짓일 때만 프로그램을 종료시키며, 디버그 빌드에서만 동작합니다. 릴리스 빌드에서는 무시됩니다.

두 메커니즘 모두 개발 중에 오류를 빠르게 감지하고 대응하기 위한 도구로 사용됩니다.

## init(coder:)에서는 무엇을 사용하는게 좋은가

`required init?(coder: NSCoder)` 메서드는 인터페이스 빌더 (Interface Builder)와 함께 사용되는 뷰나 뷰 컨트롤러의 초기화에 필요합니다.

만약 개발자가 인터페이스 빌더를 사용하지 않고 코드를 통해서만 해당 뷰를 초기화할 것이라는 확신이 있으면, 이 메서드가 호출되는 것은 개발자의 의도와 다르게 동작하는 것을 의미합니다.

이런 상황에서는 `fatalError()`를 사용하는 것이 더 적절합니다.

왜냐하면 이 메서드가 호출되면 그것은 개발 및 배포 중 모두 예상치 못한 상황이기 때문입니다.

반면, `assert`는 디버그 빌드에서만 동작하기 때문에 릴리스 빌드에서는 감지되지 않습니다.
