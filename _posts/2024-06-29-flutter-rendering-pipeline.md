---
title: Flutter Rendering Pipeline
author: mojitobar
date: 2024-06-29 21:55:00 +0900
categories: [Flutter, Dart]
tags: [Flutter, Dart, Rendering]
comments: true
---

## 개요

이 글에서는 Flutter의 렌더링 파이프라인에 대해 알아보았습니다. 

[flutter-internals](https://www.flutteris.com/blog/en/flutter-internals) 블로그의 내용을 참고하여 작성하였습니다.

## Flutter의 구조와 작동 원리

Flutter는 다음과 같은 세 가지 주요 레이어로 구성되어 있습니다.

1. **Flutter 엔진**: 낮은 수준의 렌더링과 플랫폼 간 인터페이스를 처리합니다. [Skia](https://en.wikipedia.org/wiki/Skia_Graphics_Engine)라는 2D그래픽 라이브러리를 사용하여 그래픽을 그립니다.

2. **Flutter 프레임워크**: Dart 언어로 작성된 고수준의 위젯 및 라이브러리 모음입니다. UI를 구축하기 위한 다양한 위젯들을 제공합니다.

3. **임베더 (Embedder)**: 특정 플랫폼(예: iOS, Android, 웹)에서 Flutter 애플리케이션을 실행하기 위한 코드입니다.

Flutter 프레임워크는 Window라는 추상화 계층을 통해 일련의 API를 노출하여 Flutter 엔진과 간접적으로 통신합니다.

Window를 통해 Flutter 엔진이 Flutter 프레임워크에 알림을 보내는 경우는 다음과 같습니다.

- 디바이스에서 중요한 이벤트가 발생했을 때 (예: 터치, 방향 설정, 메모리 문제)

- 플랫폼 채널이 데이터를 전달할 때

- 또한, **Flutter 엔진이 새로운 프레임을 그릴 준비가 되었을 때**

플러터 프레임워크는 플러터 엔진 프레임 렌더링에 의해 구동됩니다.

예외 케이스(제스처, GPS 이벤트, 비동기 http 응답)을 제외하면 플러터 엔진 프레임 렌더링에 의해 트리거되지 않은 경우엔 플러터 프레임워크는 렌더링을 시작하지 않습니다.


> 플러터 프레임워크는 플러터 엔진 프레임 렌더링에서 요청하지 않은 시각적 변경 사항을 적용하지 않습니다.
{: .prompt-warning }


하지만 제스처 관련 코드나 타이머를 사용하여 애니메이션이 동작하는 경우는 플러터 프레임워크에서 시각적 변경 사항을 반영한 것이 아닌지 궁금할 수 있습니다.

> 시각적인 변화가 일어나기를 원한다면 플러터 엔진에 무언가를 렌더링해야 한다고 알려야 합니다. 그러면 일반적으로 다음 새로 고침 시 플러터 엔진은 플러터 프레임워크에 일부 코드를 실행하고 렌더링 할 새 장면을 제공하도록 요청합니다.
{: .prompt-tip }

이제 가장 큰 궁금증은 플러터 엔진이 렌더링을 기반으로 전체 애플리케이션 동작을 어떻게 관리하는가 하는 것입니다.

## 플러터의 트리

그 플러터 엔진의 흐름과 관련된 세부 사항으로 들어가기 전에 잠깐 플러터의 트리에 대해 짚고 넘어가겠습니다.

플러터에는 크게 3가지 트리가 있습니다.

### 위젯 트리

위젯 트리는 선언적이고 불변한 위젯들의 구조입니다.

위젯은 애플리케이션의 UI를 선언적으로 정의하는 역할을 합니다. 위젯 트리는 코드로 정의된 트리 구조로, 실제 실행 시점에 Flutter 프레임워크에 의해 메모리에 로드됩니다.

### 엘리먼트 트리

엘리먼트 트리는 위젯 트리의 구체적인 구현체로, 각 위젯의 인스턴스를 관리합니다.

엘리먼트 트리는 메모리에 상주하며, 위젯의 상태를 추적하고 필요한 경우 위젯을 재구성합니다. 엘리먼트 트리는 위젯 트리와 1:1 대응 관계를 가집니다.

### 렌더 트리

렌더 트리는 RenderObject로 구성된 트리 구조로, 실제 화면에 그려지는 객체들을 관리합니다.

렌더 트리는 레이아웃과 페인팅을 담당하며, 메모리에서 레이아웃과 페인팅 정보를 유지하고 업데이트합니다.

![image](https://www.flutteris.com/images/blog/internals_3_trees.png)

## 가장 먼저 해야 할 일 - 바인딩 초기화

그리고 바인딩에 대해서도 간단히 짚고 넘어가야합니다.

플러터 애플리케이션을 시작하면 시스템은 `main()` 메서드를 호출하고, 이 메서드는 결국 `runApp(위젯 앱)` 메서드를 호출합니다.

`runApp()` 메서드를 호출하는 동안 플러터 프레임워크는 플러터 프레임워크와 플러터 엔진 사이의 인터페이스를 초기화하는데, 이러한 인터페이스를 바인딩 이라고 합니다.

### 바인딩의 종류

바인딩은 플러터 엔진과 플러터 프레임워크 사이의 일종의 접착제 역할을 합니다.

이러한 바인딩을 통해서만 두 플러터 파트(엔진과 프레임워크) 간에 데이터를 교환할 수 있습니다.

그리고 각 바인딩은 활동 도메인별로 재그룹화된 일련의 특정 작업, 이벤트를 처리하는 책임을 가집니다.

플러터 프레임워크는 8개의 바인딩을 지원하지만, 여기서는 `WidgetsBinding`과 `SchedulerBinding`, `RendererBinding`에 대해 집중적으로 다루겠습니다.

아래는 플러터 프레임워크의 바인딩 목록입니다.

- SchedulerBinding
- GestureBinding
- RendererBinding
- WidgetsBinding
- ServicesBinding
- PaintingBinding
- SemanticsBinding
- TestWidgetsFlutterBinding

### SchedulerBinding

이 바인딩에는 두 가지 주요 책임이 있습니다.

첫 번째는 플러터 엔진에게 이렇게 말하는 것입니다. 

> "안녕! 다음에 바쁘지 않을 때 나를 깨워줘. 그러면 내가 일할 수 있고, 너에게 무엇을 렌더링할지 말하거나 다시 나를 불러야 할지 알려줄게."

두 번째는 이러한 모닝콜을 듣고 반응하는 것입니다.

`SchedulerBinding`은 변경 사항이 레이아웃에 적용되어야 하는 경우, 플러터 엔진에게 이러한 모닝콜을 요청합니다.

### RendererBinding

이 바인딩은 플러터 엔진과 렌더 트리 사이의 접착제입니다. 2가지 주요 책임이 있습니다.

첫 번째는 엔진에서 보내는 이벤트를 수신하여 디바이스 설정을 통해 사용자가 적용한 변경 사항을 알려주는 것입니다.

두 번째는 디스플레이에 적용할 수정 사항을 엔진에 제공하는 것입니다.

화면에 렌더링할 수정 사항을 제공하기 위해 이 바인딩은 `PipelineOwner`를 구동하고 `RenderView`를 초기화합니다.

`PipelineOwner`는 레이아웃과 관련하여 어떤 `RenderObject`가 작업을 수행해야 하는지 알고 이러한 작업을 조정하는 일종의 오케스트레이터입니다.

### WidgetsBinding

이 바인딩은 장치 설정을 통해 사용자가 적용한 변경 사항을 수신하며, 이는 언어(= locale ) 및 의미 체계에 영향을 줍니다.

이 외에도 `WidgetsBinding은` 위젯과 플러터 엔진 사이의 접착제입니다. 2가지 주요 책임이 있습니다.

첫 번째 주요 작업은 위젯 구조 변경 처리를 담당하는 프로세스를 구동하는 것이고, 두 번째는 렌더링을 트리거하는 것입니다.

이제 플러터의 트리에 대해서도 알아보았고, 바인딩에 대해서도 알아보았으니, 플러터 엔진의 흐름에 대해 알아보겠습니다.

## 플러터 엔진의 흐름

플러터에서는 전체 매커니즘이 `Element` 또는 `RenderObject`의 `invalidating`통해 이루어집니다.

`Element`의 `invalidating`는 다양한 방법으로 발생할 수 있습니다.

- `setState()` 메서드를 호출하면 해당 위젯의 `Element`가 `invalidating`됩니다.

- `InheritedWidget`을 통해 상태가 변경되면 해당 위젯의 `Element`가 `invalidating`됩니다.

이렇게 되면 `Element`는 `dirty` 상태가 되어, `build()` 메서드를 호출하여 새로운 위젯 트리를 생성합니다.

`RenderObject`의 `invalidating`은 다음과 같은 방법으로 발생할 수 있습니다.

- 단순히 배경색, 텍스트 색상 등의 속성이 변경되면 해당 `RenderObject`가 `invalidating`됩니다.

이렇게 되면 해당 `RenderObject`가 다시 빌드되거나 다시 그려져야하는 목록에 추가됩니다.

`invalidating`의 유형이 무엇이든, 이런 일이 발생하면 `SchedulerBinding`이 플러터 엔진에게 새 프레임을 예약하도록 요청합니다.

앞서 `SchedulerBinding`이 두 가지 주요 책임이 있다고 말했는데, 그 중 하나는 프레임 재구축과 관련하여 플러터 엔진에서 내보낸 요청을 처리할 준비를 하는 것이었습니다.

아래 다이어그램은 Flutter 애플리케이션에서 프레임을 렌더링하는 과정을 단계별로 보여줍니다. 각 단계는 Flutter의 주요 바인딩 요소를 통해 순차적으로 실행됩니다.

![ondrawframe](https://www.flutteris.com/images/blog/internals_flow.gif)

먼저 외부 이벤트가 발생하거나 애니메이션이 업데이트되면 `SchedulerBinding`이 플러터 엔진에게 새로운 프레임을 예약합니다.

그러면 플러터 엔진은 `onBeginFrame`을 호출하여 프레임을 시작합니다.

이 요청을 플러터 프레임워크가 받아 `Ticker`와 관련된 모든 작업을 업데이트 합니다.

`Ticker`가 업데이트 된 이후에 `Window`는 `SchedulerBinding`의 `onDrawFrame`을 호출하여 렌더링을 요청하고,

다시 `SchedulerBinding`은 `WidgetsBinding`에게 렌더링을 시작하도록 요청합니다.

`WidgetsBinding`은 `Element`의 `build` 메서드를 호출하여 새로운 위젯 트리를 생성하고, 이를 `RenderObject` 트리로 변환합니다.

마지막으로 `Window`는 `RenderObject` 트리를 화면에 렌더링합니다.

이 이미지와 설명을 통해 Flutter 엔진이 프레임을 생성하고 렌더링하는 전체 과정을 쉽게 이해할 수 있습니다.

## 마무리

플러터의 렌더링 파이프라인에 대해 알아보았습니다. 플러터 엔진의 흐름과 위젯, 엘리먼트, 렌더 트리 그리고 바인딩에 대해서 알 수 있는 좋은 기회였습니다.

## 참조

[flutter-internals](https://www.flutteris.com/blog/en/flutter-internals)

[Render Object](https://api.flutter.dev/flutter/rendering/RenderObject-class.html)

[SchedulerBinding](https://api.flutter.dev/flutter/scheduler/SchedulerBinding-mixin.html)

[WidgetsBinding](https://api.flutter.dev/flutter/widgets/WidgetsBinding-mixin.html)

