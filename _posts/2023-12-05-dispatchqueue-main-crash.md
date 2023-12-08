---
title: dispatchqueue.main.sync가 크래시나는 이유
author: mojitobar
date: 2023-12-05 11:53:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, DispatchQueue, sync, crash]
comments: true
---

## 개요

기존에 DispatchQueue.main.sync를 사용하면 크래시가 발생한다는 것을 알고 있었는데, 그 이유가 궁금해서 한 번 정리해봤습니다.

## dispatchqueue.main.sync가 크래시나는 이유

1. **기존 작업 실행 중**

   메인 스레드는 이미 실행 중인 작업(예: UI 업데이트, 이벤트 처리 등)을 처리하고 있습니다.

2. **`DispatchQueue.main.sync` 호출**

   이 시점에서 `task1`을 메인 스레드에 할당하고 `task1`이 완료될 때까지 기다리라는 명령을 내립니다.

3. **락 발생과 교착 상태**
   `DispatchQueue.main.sync`로 인해 메인 스레드는 차단(락)됩니다. (`task1`이 완료될 때 까지)

   하지만 `task1` 이 실행되기 위해서는 이미 실행 중인 작업이 완료되었음을 메인 스레드에 알려야 합니다.

   그런데 메인 스레드는 이미 락 상태이고 `task1`이 완료되기만을 기다리고 있습니다.

   이로 인해 교착 상태가 발생합니다.

4. **작업 실행 불가**

   메인 스레드는 이미 차단된 상태이기 때문에, `task1`을 시작할 수 없습니다.

   또한, 기존 작업도 완료할 수 없게 되어, 프로그램은 진행 불가능한 상태에 빠집니다.

5. **크래시 발생**

   이 상태는 애플리케이션의 반응을 멈추게 하며, 최종적으로는 앱이 크래시될 수 있습니다.

## 그렇다면 기존에 실행 중인 작업이 없다면 main.sync는 크래시가 나지 않을까?

그렇습니다.

```swift
func print() {
    DispatchQueue.global().async {
        DispatchQueue.main.sync {
            print("hello")
        }
    }
}

print()
```

이 코드가 그 예시입니다.

print함수가 호출되고 이후 태스크는 `DispatchQueue.global().async`로 인해 글로벌 큐로 넘어가게 됩니다. 그리고 async이기 때문에 즉시 print 함수가 반환됩니다.

바로 이 시점에는 main 스레드가 print 함수의 리턴을 받았기 때문에 큐가 비어있게 됩니다.

따라서 main.sync를 호출해서 메인 스레드가 락에 걸려도 바로 해당 태스크를 수행할 수 있기 때문에 정상적으로 출력이 되는 것을 확인할 수 있습니다.
