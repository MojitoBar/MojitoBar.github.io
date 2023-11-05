---
title: Designing for iOS - 번역
author: mojitobar
date: 2023-11-05 17:21:00 +0900
categories: [iOS, Swift, HIG]
tags: [iOS, Swift, HIG, Designing for iOS, Human Interface Guidelines]
comments: true
image:
  path: /assets/img/platforms-iOS-intro~dark2x.png
  alt: A stylized representation of an iPhone frame shown on top of a grid. The image is overlaid with rectangular and circular grid lines and is tinted green to subtly reflect the green in the original six-color Apple logo.
---

## Designing for iOS

> People depend on their iPhone to help them stay connected, play games, view media, accomplish tasks, and track personal data in any location and while on the go.

사람들은 iPhone을 사용하여 연결 상태를 유지하고, 게임을 하고, 미디어를 보고, 작업을 수행하고, 이동 중에도 개인 데이터를 추적하는 데 의존합니다.

> As you begin designing your app or game for iOS, start by understanding the following fundamental device characteristics and patterns that distinguish the iOS experience. Using these characteristics and patterns to inform your design decisions can help you provide an app or game that iPhone users appreciate.

iOS용 앱이나 게임을 디자인하기 시작할 때, iOS 경험을 구별하는 다음과 같은 기본 장치 특성과 패턴을 이해하는 것으로 시작하십시오. 이러한 특성과 패턴을 디자인 결정에 반영하면 iPhone 사용자들이 감사하게 여길 앱이나 게임을 제공하는 데 도움이 될 수 있습니다.

> Display. iPhone has a medium-size, high-resolution display.

**디스플레이**. iPhone은 중간 크기의 고해상도 디스플레이를 가지고 있습니다.

> Ergonomics. People generally hold their iPhone in one or both hands as they interact with it, switching between landscape and portrait orientations as needed. While people are interacting with the device, their viewing distance tends to be no more than a foot or two.

**인체공학**. 사람들은 일반적으로 iPhone을 한 손 또는 양손으로 들고 상호 작용하며, 필요에 따라 가로 및 세로 방향을 전환합니다. 사람들이 장치와 상호 작용하는 동안, 그들의 시야 거리는 대개 한 두 피트를 넘지 않습니다.

> Inputs. Multi-Touch gestures, onscreen keyboards, and voice control let people perform actions and accomplish meaningful tasks while they’re on the go. In addition, people often want apps to use their personal data and input from the device’s gyroscope and accelerometer, and they may also want to participate in spatial interactions.

**입력**. 멀티터치 [제스처](https://developer.apple.com/design/human-interface-guidelines/touchscreen-gestures), [화면 키보드](https://developer.apple.com/design/human-interface-guidelines/onscreen-keyboards), [음성](https://developer.apple.com/design/human-interface-guidelines/siri) 제어를 통해 사람들은 이동 중에 작업을 수행하고 의미 있는 작업을 완료할 수 있습니다. 또한, 사람들은 종종 앱이 [개인 데이터](https://developer.apple.com/design/human-interface-guidelines/accessing-private-data)와 장치의 [자이로스코프 및 가속도계](https://developer.apple.com/design/human-interface-guidelines/gyro-and-accelerometer)에서 입력을 사용하기를 원하며, [공간 상호 작용](https://developer.apple.com/design/human-interface-guidelines/spatial-interactions)에 참여하고자 할 수도 있습니다.

> App interactions. Sometimes, people spend just a minute or two checking on event or social media updates, tracking data, or sending messages. At other times, people can spend an hour or more browsing the web, playing games, or enjoying media. People typically have multiple apps open at the same time, and they appreciate switching frequently among them.

**앱 상호 작용**. 때때로 사람들은 이벤트나 소셜 미디어 업데이트를 확인하거나 데이터를 추적하거나 메시지를 보내는 데 1 ~ 2분을 소비합니다. 다른 때에는 사람들이 웹을 탐색하거나 게임을 하거나 미디어를 즐기는 데 한 시간 이상을 소비할 수도 있습니다. 사람들은 일반적으로 동시에 여러 앱을 열어 두고 자주 전환하는 것을 선호합니다.

> System features. iOS provides several features that help people interact with the system and their apps in familiar, consistent ways.

**시스템 기능**. iOS는 사람들이 시스템과 앱과 친숙하고 일관된 방식으로 상호 작용하는 데 도움이 되는 여러 기능을 제공합니다.

- [Widgets](https://developer.apple.com/design/human-interface-guidelines/widgets)

- [Home Screen quick actions](https://developer.apple.com/design/human-interface-guidelines/home-screen-quick-actions)

- [Spotlight](https://developer.apple.com/design/human-interface-guidelines/searching)

- [Shortcuts](https://developer.apple.com/design/human-interface-guidelines/siri#Shortcuts-and-suggestions)

- [Activity views](https://developer.apple.com/design/human-interface-guidelines/activity-views)

## Best practices

> Great iPhone experiences integrate the platform and device capabilities that people value most. To help your design feel at home in iOS, prioritize the following ways to incorporate these features and capabilities.

훌륭한 iPhone 경험은 사람들이 가장 중요하게 여기는 플랫폼 및 장치 기능을 통합합니다. iOS에서 자연스럽게 느껴지도록 디자인을 돕기 위해, 다음과 같은 방법으로 이러한 기능과 기능을 통합하는 것을 우선시하십시오.

> Help people concentrate on primary tasks and content by limiting the number of onscreen controls while making secondary details and actions discoverable with minimal interaction.

- 화면상의 컨트롤 수를 제한하여 사람들이 주요 작업과 콘텐츠에 집중할 수 있도록 하면서, 최소한의 상호 작용으로 보조 세부 정보 및 작업을 발견할 수 있도록 합니다.

> Adapt seamlessly to appearance changes — like device orientation, Dark Mode, and Dynamic Type — letting people choose the configurations that work best for them.

- 장치 방향, 다크 모드, 다이내믹 타입과 같은 외관 변화에 매끄럽게 적응하여 사람들이 가장 잘 맞는 구성을 선택할 수 있도록 합니다.

> Support interactions that accommodate the way people usually hold their device. For example, it tends to be easier and more comfortable for people to reach a control when it’s located in the middle or bottom area of the display, so it’s especially important let people swipe to navigate back or initiate actions in a list row.

- 사람들이 일반적으로 장치를 잡는 방식을 수용하는 상호 작용을 지원합니다. 예를 들어, 컨트롤이 디스플레이의 중간이나 하단 영역에 위치할 때 사람들이 도달하기가 더 쉽고 편안하므로, 목록 행에서 뒤로 스와이프하거나 작업을 시작할 수 있도록 하는 것이 특히 중요합니다.

> With people’s permission, integrate information available through platform capabilities in ways that enhance the experience without asking people to enter data. For example, you might accept payments, provide security through biometric authentication, or offer features that use the device’s location.

사람들의 허락을 받아 플랫폼 기능을 통해 제공되는 정보를 경험을 향상시키는 방식으로 통합합니다. 예를 들어, 결제를 수락하거나 생체 인증을 통해 보안을 제공하거나 장치의 위치를 사용하는 기능을 제공할 수 있습니다.

## Resources

### Related

[Apple Design Resources](https://developer.apple.com/design/resources/#ios-apps)

### Developer documentation

[Planning your iOS app](https://developer.apple.com/ios/planning/)

### Videos

[What's new in UIKit](https://developer.apple.com/videos/play/wwdc2021/10059)

[What's New in iOS Design](https://developer.apple.com/videos/play/wwdc2019/808)
