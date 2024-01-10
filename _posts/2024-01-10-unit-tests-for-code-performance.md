---
title: 코드 성능을 위한 유닛 테스트 작성법
author: mojitobar
date: 2024-01-10 22:05:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, UnitTest]
comments: true
---

# 들어가기에 앞서

해당 글은 [Unit Testing for Code Performance](https://www.swiftwithvincent.com/blog/how-to-write-unit-tests-for-code-performance) 글을 번역한 글입니다.

최근 프로젝트에 유닛 테스트를 추가하면서, 관련 자료를 찾아보던 중 흥미로운 글을 발견하여 번역하게 되었습니다.

# 코드 성능을 위한 유닛 테스트 작성법

코드 성능을 테스트하기 위해 유닛 테스트를 작성하는 것이 가능하다는 것을 알고 계신가요?

다음 예시 함수를 보겠습니다.

```swift
func costlyFunction() -> Int {
    var result = 0
    for i in 0..<1000000 {
        result += i
    }
    return result
}
```

보시다시피, 이 함수는 실행에 조금 시간이 걸리는 간단한 예제입니다.

그리고 이러한 종류의 코드는 실수로 실행 시간이 늘어나지 않도록 모니터링 하는 것이 중요합니다.

그럼 이제 이 함수가 실행 시간이 변하지 않았음을 체크하는 테스트를 작성해봅시다.

우리는 유닛 테스트 타겟으로 넘어와 `measure()` 함수를 사용해 테스트를 작성할 것입니다.

```swift
import XCTest
@testable import UnitTestExample

class UnitTestExampleTests: XCTestCase {
    func testCostlyFunction() {
        measure {
            let _ = costlyFunction()
        }
    }
}
```

이 함수는 일반적인 테스트 assertions와 다르게 작동합니다.

우리가 처음으로 테스트를 실행하면, `measure()`함수는 클로저 내부의 코드를 10회 실행하고 실행 시간의 평균을 구합니다.

그런 다음 이 결과를 Baseline으로 설정하여 Xcode 프로젝트 설정에 저장할 수 있습니다.

![image](https://github.com/f-lab-edu/pins/assets/16567811/12abd2d6-0afc-421e-a206-240e6bb4531f)

그리고 그 다음부터 테스트를 실행할 때 평균 실행 시간이 허용된 최대 편차 비율보다 더 많이 증가하면 테스트가 실패합니다.

```swift
func costlyFunction() -> Int {
    var result = 0
    for i in 0..<2000000 {
        result += i
    }
    return result
}
```

참고로, 실행 시간만이 `measure()` 함수가 추척할 수 있는 유일한 단위는 아닙니다.

메모리 또는 스토리지 사용량도 모니터링할 수 있습니다.

```swift
import XCTest
@testable import TestPerformance

class UnitTestExampleTests: XCTestCase {
    func testPerformance() throws {
        measure(metrics: [XCTMemoryMetric(), XCTStorageMetric()]) {
            let _ = costlyFunction()
        }
    }
}
```

# 마무리

본문 블로그에서는 테스트가 실패하지만 실제로 테스트를 해본 결과 테스트가 실패하지는 않았습니다.

![image](https://github.com/f-lab-edu/pins/assets/16567811/6d8c9b0c-2f5b-4e04-ad73-7e7826b4c7d2)

훨씬 더 높은 퍼센트에서 테스트가 실패하긴 했으나, 정확한 기준을 찾지는 못했습니다.

그래도 설정한 Baseline에 비해 얼마나 오래 걸리는지는 확인할 수 있었습니다.

테스트가 실패하지 않는 이유에 대해서는 더 알아봐야할 것 같습니다.
