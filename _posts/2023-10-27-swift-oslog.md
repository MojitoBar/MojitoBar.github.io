---
title: os_log를 사용해보자
author: mojitobar
date: 2023-10-27 00:06:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, os_log]
comments: true
---

## 개요

프로젝트를 진행하다 멘토님께서 이러한 리뷰를 남겨주셨는데요.

![image](https://github.com/f-lab-edu/pins/assets/16567811/f563d47f-c960-4921-80cb-638cfb9547f3)

콘솔창에 메시지를 찍기 위해서 print()밖에 사용하지 않았기 때문에 처음 들어보는 os_log에 대해서 정리해봤어요.

## os_log

os_log는 Apple의 로깅 시스템의 일부로, 앱 및 시스템 서비스의 실행 중에 발생하는 정보, 경고 및 오류와 같은 정보를 기록하는 데 사용돼요.

이 로깅 시스템은 고성능이며, 앱의 성능에 크게 영향을 주지 않으면서도 유용한 로그 정보를 캡쳐하는 데 중점을 둬요.

os_log의 주요 특징은 다음과 같아요.

- 카테고리화: 로그를 다양한 카테고리로 분류할 수 있어요. 이를 통해 관련 로그를 빠르게 찾을 수 있죠.

- 효율성: 로그 메시지는 런타임에 평가되기 전까지 문자열 인터폴레이션을 지연시켜요. 이는 불필요한 처리를 방지하며 성능에 미치는 영향을 최소화해요.

- 동적 수준 설정: 로그 수준(예: info, debug, error 등)을 동적으로 조정할 수 있어요. 이를 통해 개발 중에는 상세한 로그를 확인하고, 출시 버전에서는 중요한 로그만 확인할 수 있어요.

- 통합된 로그 저장소: macOS, iOS, watchOS 및 tvOS에서 동일한 로깅 시스템을 사용해요. 따라서 여러 플랫폼에서 일관된 로깅 경험을 제공할 수 있어요.

더 자세한 내용은 공식 문서를 통해 확인해주세요.

[Generating Log Messages from Your Code Apple Developer Documentation](<(https://developer.apple.com/documentation/os/logging/generating_log_messages_from_your_code)>)

[OSLog Apple Developer Documentation](https://developer.apple.com/documentation/os/oslog)

아래는 간단한 사용 예시에요.

```swift
import os.log

let log = OSLog(subsystem: "com.example.myapp", category: "networking")
os_log("Network request sent!", log: log, type: .info)
```

- subsystem: 앱의 전체 기능 또는 부분에 대한 고유한 문자열을 나타내요. 주로 번들 식별자를 사용해요.

- category: 관련성 있는 로그를 그룹화하는 데 사용하는 문자열이에요. 예로 'networking', 'UI', 'database' 등의 카테고리를 생각할 수 있어요.

- type: 로그의 중요도를 나타내는 로그 유형이에요. .default, .info, .debug, .error, .fault 등이 있어요.

좀 더 세부적인 포맷이나 인자를 사용하려면 %@ (문자열), %d (정수) 등의 특수 문자를 사용할 수 있어요. 그리고 개인 정보를 공개하려면 public 키워드를 사용해요.

```swift
os_log("User %@ logged in with ID: %d", log: log, type: .info, "JohnDoe", 123456)
os_log("User %{public}@ logged in with ID: %d", log: log, type: .info, "JohnDoe", 123456) // 이름이 공개됨
```

이렇게 사용하면 앱에서 발생하는 다양한 이벤트를 기록하고, 문제 해결을 위한 진단 정보를 제공하는 데 매우 유용해요.

## 로그 레벨

위에서 로그의 중요도를 나타내는 로그 유형에 대해서 언급했는데요, 각각 어떤 의미인지는 다음과 같아요.

1.  .default

    - 기본 로그 레벨이에요.
    - 일반적인 시스템 작동 중에 정보를 제공하거나 특정 이벤트를 기록하는 데 사용돼요.
    - 시스템 동작에는 영향을 주지 않는 일반적인 정보에 적합해요.

2.  .info

    - 정보 제공용 로그 레벨이에요.
    - .default 보다는 덜 중요하지만, 특정 상황에서 유용한 정보를 제공하는 데 사용돼요.
    - 디버깅이나 추가적인 컨텍스트 제공에 유용해요.
    - 기본적으로 디바이스 로그에서는 보이지 않으며, 개발 중이나 디버깅할 때만 활성화하여 볼 수 있어요.

3.  .debug

    - 디버그용 로그 레벨이에요.
    - 개발 중이나 문제 해결 시에만 사용되는 세부 정보를 제공하는 데 사용돼요.
    - 이 로그 레벨의 메시지는 출시된 앱에서는 캡쳐되지 않아요.
    - 개발자가 코드를 직접 검토하며 발생하는 상황을 이해하는 데 도움을 주는 로그에 적합해요.

4.  .error

    - 오류 로그 레벨이에요.
    - 예상치 않은 문제나 오류가 발생했을 때 사용돼요.
    - 문제의 원인을 파악하고 해결하는 데 필요한 정보를 포함해야 해요.

5.  .fault

    - 시스템 또는 앱의 심각한 오류를 나타내는 로그 레벨이에요.
    - 앱의 불안정성을 야기하거나 끝내 크래시를 유발할 수 있는 잘못된 상태나 오류를 나타낼 때 사용돼요.
    - .error 보다 더 심각한 상황에 적합해요.

## print말고 os_log를 사용해서 로깅을 해야하는 이유

위 os_log의 특징에 덕분에 로깅을 위해서는 print 대신 os_log를 사용하는 것이 장점이 많아요.

다시 한 번 정리한 os_log의 장점이에요.

1. 성능에 따라서 과도한 로그 메시지로 인해 디스크 공간이 빠르게 소모되는 것을 방지해요. print 함수는 표준 출력으로 메시지를 보내는 데에 비교적 더 많은 시스템 리소스를 사용할 수 있어요.

2. os_log는 애플의 로깅 시스템, 즉 Unified Logging System에 직접 통합되어 설계되었어요. 이는 로그 메시지를 효율적으로 저장하고, 필요에 따라 동적으로 메시지의 지속 시간을 조절해요.

3. 개인정보보호 예를 들어, 사용자 이름이나 이메일 주소와 같은 개인 정보는 자동으로 가릴 수 있어요. 이는 %{public}@ 또는 %{private}@ 태그를 사용하여 로그 메시지 내에서 지정할 수 있어요. 이렇게 함으로써 로그에 실수로 개인 정보가 기록되어 외부에 노출되는 것을 방지할 수 있습니다.

4. os_log는 다양한 로그 레벨과 카테고리를 제공해요. 이를 통해 로그의 중요도와 카테고리별로 로그를 분류하고 필터링할 수 있어요.

## 마무리

다른 블로그들을 보면 mac의 콘솔앱을 이용해서 로그를 확인하는 것을 볼 수 있었어요.

xcode 콘솔창에서도 확인할 수 있지만, 더 자세한 정보를 확인하려고 콘솔앱을 이용하는 것 같았는데요, xcode 15로 업데이트되며 OSLog를 xcode로 통합하여 많은 부분이 개선되었어요.

메타데이터의 경우엔 토글 버튼으로 껏다 켰다 할 수 있게 되었고, 로그 타입과 카테고리별로 필터링도 가능해요.

그리고 해당 소스코드로 이동이나 타입에 따라 배경색도 변경되어서 훨씬 사용하기 편해졌다고 볼 수 있어요.

[What’s new in Xcode 15 - WWDC23 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2023/10165/?time=1043)

[Debug with structured logging - WWDC23 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2023/10226)
