---
title: Swift iOS 개발에서 Concurrency와 Multithreading
author: mojitobar
date: 2024-01-08 23:48:00 +0900
categories: [iOS, Swift, Concurrency, Multithreading]
tags: [iOS, Swift, Concurrency, Multithreading]
comments: true
---

# 들어가기에 앞서

해당 글은 [Concurrency and Multithreading in Swift iOS Development](https://medium.com/@muzammalshahzad494/concurrency-and-multithreading-in-swift-ios-development-1bae6b26acd2) 글을 번역한 글입니다.

추가로 예제 코드를 작성하고, 저만의 생각을 덧붙였습니다.

# 개요

빠르게 변화하는 iOS 개발 환경에서, 매끄러운 유저 경험을 제공하기 위해 반응적이고 성능이 좋은 어플리케이션을 만드는 것은 중요합니다.

개발자가 맞닥뜨리는 도전과제 중 하나는 효과적으로 멀티스레딩과 동시성을 관리하는 것입니다.

애플의 프로그래밍 언어인 Swift에서는, 동시적인 테스크들을 관리하기 위해 강력한 도구들을 제공합니다.

하지만 그것들을 완벽하게 다루는 것은 언어와 모범 사례의 깊은 이해를 필요로 합니다.

# Swift의 Concurrency

Concurrency는 동시에 여러 작업을 수행하는 것을 의미합니다. 그리고 Swift는 그것을 달성하기 위해 몇 가지 메커니즘을 제공합니다.

Grand Central Dispatch(GCD)는 동시에 테스크들을 수행하기 위해 간단하고 효과적인 방법을 제공하는 Swift 동시성의 핵심입니다.

또한 Swift는 Swift 기본 구문을 사용하여 비동기 코드를 더 쉽게 작성할 수 있는 `async`와 `await` 키워드를 도입했습니다.

# Grand Central Dispatch(GCD)

GCD는 동시 작업을 관리하기 위한 강력하고 널리 사용되는 프레임워크입니다.

그것은 복잡한 스레드 관리를 추상화하고, 개발자가 동시에 수행하려는 작업에 집중할 수 있게 해줍니다.

테스크들은 각기 다른 큐에 할당되고, GCD는 스레드와 리소스를 효율적으로 관리합니다.

```swift
DispatchQueue.global().async {
    let result = getImage()
    DispatchQueue.main.async {
        updateUI(result)
    }
}

func getImage() -> String {
    // 시간이 걸리는 API 호출 코드

    return "API 호출 결과"
}

func updateUI(_ result: String) {
    // UI 업데이트 코드
}
```

위의 코드는 백그라운드에서 이미지를 가져오고, 메인 스레드에서 UI를 업데이트하는 간단한 예제입니다.

코드가 실행되고 `global().async` 코드 블럭은 concurrent queue에서 실행됩니다.

그리고 그 안에 있는 `main.async` 코드 블럭은 main queue에서 실행됩니다.

`main.async` 블럭 다음 줄에 코드가 있다면 그것은 concurrent queue에서 실행됩니다.

# Swift의 Async/Await

Swift는 5.5에서 더 편리한 비동기 코드를 작성할 수 있도록 `async`와 `await` 키워드를 도입했습니다.

이 키워드들은 개발자가 더 가독성 있고 콜백과 completion 핸들러의 필요를 줄여 순차적인 비동기 코드를 작성할 수 있게 해줍니다.

```swift
// async/await를 사용한 예시 코드
func fetchData() async throws -> Data {
    let url = URL(string: "https://www.example.com")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return data
}
// 비동기 함수 호출
Task {
    do {
        let data = try await fetchData()
        print(data)
    } catch {
        print(error)
    }
}
```

# 경합 조건 예방과 식별

동시성은 두 개 또는 그 이상의 스래드가 동시에 공유된 데이터에 접근하여 예측할 수 없는 행동으로 이어지는 경합 조건의 위험을 내포합니다.

Swift는 경합 조건의 위험을 줄이기 위해 `@synchronized` 블럭과 `DispatchQueue` barrier와 같은 도구를 제공합니다.

```swift
class SharedResource {
    private var data: [String] = []
    private let dataQueue = DispatchQueue(label: "com.example.sharedResource")

    func addData(_ newData: String) {
        dataQueue.async(flags: .barrier) {
            self.data.append(newData)
        }
    }

    func readData() -> [String] {
        var result: [String] = []

        dataQueue.sync {
            result = self.data
        }

        return result
    }
}
```

위의 코드에서는 쓰기 작업에 `barrier flag`를, 읽기 작업에 `sync`를 사용하여 경합 조건을 방지합니다.

`barrier flag`를 사용하면 해당 작업이 실행되는 동안 다른 모든 작업(읽기, 쓰기)이 대기 상태가 됩니다.

이렇게 하면 쓰기 작업이 완료되기 전에 다른 작업이 실행되는 것을 방지할 수 있습니다.

읽기 작업에서는 공유 자원의 상태를 변경하지 않지만, 일관된 상태의 데이터를 읽어야 합니다.

따라서 `sync`를 이용하면 읽기 작업이 완료될 때까지 다른 쓰기 작업이 시작되지 않습니다.

# 스레드 안전성과 동기화

스레드 안전성을 보장하는 것은 멀티스레드 환경에서 무엇보다 중요한 부분입니다.

스위프트는 다중 스레드의 동시 접근으로부터 공유된 자원을 보호하기 위해 [락](https://developer.apple.com/documentation/foundation/nslock), [세마포어](https://developer.apple.com/documentation/dispatch/dispatchsemaphore), [원자성 연산자](https://github.com/apple/swift-atomics)와 같은 다양한 동기화 도구를 제공합니다.

# Swift 동기 프로그래밍의 모범 사례

1. **동기화를 위해 Serial 큐를 사용하세요.**

   직렬 큐는 한 번에 하나의 작업만 실행되도록 보장하므로, 공유된 자원에 대한 동시 접근을 방지할 수 있습니다.

2. **과도한 전역 Concurrent 큐 사용을 피하세요.**

   전역 concurrent 큐는 편리하지만, 너무 많은 작업을 만들면 성능에 영향을 줄 수 있습니다. 대신 특정 테스크를 위한 private 큐를 만드세요.

3. **동시성 이슈를 위해 철처히 테스트하세요.**

   동시성 버그는 발견하기 힘들 수 있습니다. Xcode의 Thread Sanitizer와 같은 도구로 엄격하게 테스트 하는 것은 잠제적인 이슈를 고치고 식별하기 위해 중요합니다.

4. **비동기 디자인 패턴을 도입하세요.**

   델리게이트, 콜백, Combine 프레임워크와 같은 디자인 패턴을 활용하여 비동기 작업을 더 우아하게 다룰 수 있습니다.

# 도전 과제와 고려 사항

Swift는 동시성을 위한 강력한 도구를 제공하지만, 개발자들은 반드시 잠재적 위험에 대해 인지하고 있어야 합니다. 데드락, 우선순위 역전, 리소스 경합은 멀티스레드 환경에서 일어날 수 있는 문제들입니다.
