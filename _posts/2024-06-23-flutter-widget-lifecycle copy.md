---
title: Flutter widget lifecycle
author: mojitobar
date: 2024-06-23 18:42:00 +0900
categories: [Flutter, Dart]
tags: [Flutter, Dart, Widget, Lifecycle]
comments: true
---

## 개요

이 글에서는 Flutter가 어떻게 위젯 상태를 관리하고, 업데이트하며, 효율적으로 렌더링 하는지에 대해 알아보았습니다.

## 위젯의 생명주기

### 플러터 위젯의 종류

플러터 위젯은 크게 두 가지 종류로 나뉩니다.

하나는 상태가 변하는 `StatefulWidget`, 나머지 하나는 상태가 변하지 않는 `StatelessWidget입니다`.

### State란 ?

State는 Flutter 애플리케이션에서 위젯의 데이터 또는 UI가 동적으로 변경될 수 있는 속성을 의미합니다.

이는 위젯의 모양이나 동작을 변화시킬 수 있는 모든 정보를 포함합니다.

앞서 이야기한 `StatefulWidget와` `StatelessWidget의` 생명 주기가 다른 이유가 바로 이 상태 관리에 있습니다.

### StatefulWidget의 생명주기

먼저 StatefulWidget의 생명 주기입니다.

<img alt="다이어그램" src="https://github.com/MojitoBar/MojitoBar.github.io/assets/16567811/229b6043-748b-40be-a7a6-6f472a261d21" width=600 />

이해를 돕기 위해 먼저 다이어그램을 보면서 정리해보겠습니다.

#### createState()

`createState()` 메서드는 `StatefulWidget`이 위젯 트리에 삽입될 때 호출됩니다. 

이 메서드는 위젯의 상태를 관리하는 State 객체를 생성합니다. State 객체는 위젯의 모든 변경 가능한 상태를 보유합니다.


`StatefulWidget`은 동일한 위젯이라도 트리의 여러 위치에 삽입될 경우, 각각의 위치마다 독립적인 State 객체를 생성하여 각기 다른 상태를 유지할 수 있습니다.

이를 통해 동일한 위젯이라도 여러 위치에서 다른 상태를 가지도록 할 수 있습니다.

State 객체는 위젯의 변경 가능한 상태를 나타냅니다. 예를 들어, 카운터 값, 사용자 입력 값, 애니메이션 상태 등과 같은 동적으로 변경될 수 있는 데이터를 관리합니다.

State 객체가 생성되고 나서 위젯 트리에 삽입되면, Flutter 프레임워크는 해당 State 객체의 `mounted` 프로퍼티를 `true`로 설정합니다.

`mounted`가 `true`일 때, State 객체는 `BuildContext`와 연결되어 있으며, 이는 위젯 트리의 특정 위치를 참조합니다.

이 상태에서는 위젯이 화면에 표시되고 상호작용할 준비가 된 상태입니다.

#### initState()

`initState()` 메서드는 State 객체가 처음 생성되고, 위젯이 트리에 삽입된 직후 호출됩니다. 이 메서드는 각 State 객체마다 정확히 한 번 호출됩니다. 주로 변수를 초기화하고 데이터 소스를 구독하는 데 사용됩니다.

`initState()`를 `override`하여 `context`에 의존하는 초기화 또는 위젯에 의존하는 초기화를 수행할 수 있습니다. 이 메서드 내에서 초기화를 수행하면 State 객체가 처음 빌드될 때 필요한 설정을 할 수 있습니다.

아래는 위젯의 속성과 컨텍스트에 의존하는 초기화 예시입니다.

```dart
class MyStatefulWidget extends StatefulWidget {
  final String title;

  MyStatefulWidget({required this.title});

  @override
  _MyStatefulWidgetState createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  late String _title;
  late ThemeData _themeData;

  @override
  void initState() {
    super.initState();
    // 위젯의 속성 값에 기반한 초기화
    _title = widget.title;

    // 컨텍스트에 기반한 초기화
    _themeData = Theme.of(context);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(_title),
      ),
      body: Center(
        child: Text(
          'Current theme primary color: ${_themeData.primaryColor}',
        ),
      ),
    );
  }
}
```

#### didChangeDependencies()

프레임워크는 `initState()` 직후에 이 메서드를 호출하거나, State 객체의 종속성이 변경될 때 호출합니다. 

State 객체가 특정 `InheritedWidget`의 값을 참조하고 있을 때, 해당 `InheritedWidget`이 변경되면 Flutter 프레임워크는 `didChangeDependencies()` 메서드를 호출하여 State 객체에 알립니다.

`InheritedWidget`은 Flutter 애플리케이션에서 상위 위젯의 데이터를 하위 위젯과 공유하기 위해 사용됩니다. 예를 들어, `Theme`, `MediaQuery` 등이 `InheritedWidget`의 일종입니다.

아래는 `Theme`이 변경됨에 따라 하위 위젯의 `didChangeDependencies()`에서 테마를 바꾸는 예시입니다.

```dart
class MyApp extends StatefulWidget {
  const MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  bool _isDarkTheme = false;

  void _toggleTheme() {
    setState(() {
      _isDarkTheme = !_isDarkTheme;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: _isDarkTheme ? ThemeData.dark() : ThemeData.light(),
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Theme Change Example'),
        ),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text('Current theme is ${_isDarkTheme ? 'Dark' : 'Light'}'),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: _toggleTheme,
                child: const Text('Toggle Theme'),
              ),
              const MyStatefulWidget(),
            ],
          ),
        ),
      ),
    );
  }
}

class MyStatefulWidget extends StatefulWidget {
  const MyStatefulWidget({super.key});

  @override
  State<MyStatefulWidget> createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  late String _themeColor;

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    final ThemeData theme = Theme.of(context);
    _themeColor = theme.primaryColor.toString();
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Text(
        'Current theme primary color: $_themeColor',
        style: TextStyle(color: Theme.of(context).primaryColor),
      ),
    );
  }
}
```

#### build()

`build()` 메서드는 필수이며 Flutter 프레임워크는 여러 가지 상황에서 이 메서드를 호출합니다. 이 메서드는 위젯 트리를 생성하고 UI를 렌더링하는 데 중요한 역할을 합니다.

이 메서드는 다음과 같은 상황에서 호출됩니다.

- `initState()`를 호출한 후

- `didUpdateWidget()`을 호출한 후

- `setState()` 호출을 받은 후

- 이 State 객체의 종속성이 변경된 후 (`didChangeDependencies()` 호출 후)

- `deactivate()`를 호출한 후 다른 위치에서 State 객체를 트리에 다시 삽입한 후

`build()` 메서드는 현재 위젯의 하위 트리를 반환하며, 프레임워크는 이 반환된 위젯을 사용해 기존 트리를 업데이트합니다.

위젯 트리의 변경 사항은 `Widget.canUpdate` 메서드를 통해 판단되며, 새로 반환된 위젯이 기존 위젯의 루트를 업데이트할 수 있는지 여부를 결정합니다.

`Widget.canUpdate`의 동작 원리는 다음과 같습니다.

1. 키 비교 (Key Comparison)
    
    - 먼저, 기존 위젯과 새 위젯의 키를 비교합니다. 키가 동일하면 두 위젯은 같은 위치에서 서로를 대체할 수 있다고 판단합니다.

    - 키가 다르다면, 두 위젯은 서로 다른 것으로 간주되어 기존 위젯을 제거하고 새 위젯을 삽입합니다.

2. 타입 비교 (Type Comparison)
    
    - 키가 동일한 경우, 두 위젯의 타입을 비교합니다. 타입이 동일하면 새 위젯이 기존 위젯을 업데이트할 수 있습니다.

    - 타입이 다르다면, 두 위젯은 다른 것으로 간주되어 기존 위젯을 제거하고 새 위젯을 삽입합니다.

#### didUpdateWidget()

`didUpdateWidget()` 메서드는 부모 위젯의 구성이 변경되어 현재 위젯을 다시 빌드해야 할 때 호출됩니다.

부모 위젯이 재빌드되면, 자식 위젯도 재빌드 되어야 할 수 있습니다.

이 과정에서 동일한 타입과 키를 가진 새 위젯이 같은 위치에 표시될 수 있습니다. (속성 값이나 상태가 변경된 경우)

프레임워크는 새 위젯을 현재 State 객체의 widget 프로퍼티에 할당하여 State 객체가 새 위젯과 연관되도록 합니다.

그런 다음, 기존 위젯을 인수로 하여 `didUpdateWidget()` 메서드를 호출합니다.

이 메서드를 통해 State 객체는 이전 위젯과 새 위젯 간의 변경 사항을 감지하고, 필요한 업데이트를 수행할 수 있습니다.

아래는 부모 위젯이 재빌드될 때 하위 위젯의 재사용을 보여줍니다.

만약 key값이 같다면 `didUpdateWidget()`가 호출되지만 key값이 같지 않다면 `didUpdateWidget()`가 호출되지 않습니다.

```dart
class ParentWidget extends StatefulWidget {
  const ParentWidget({super.key});

  @override
  State<ParentWidget> createState() => _ParentWidgetState();
}

class _ParentWidgetState extends State<ParentWidget> {
  bool _toggle = true;

  void _toggleChild() {
    setState(() {
      _toggle = !_toggle;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ElevatedButton(
          onPressed: _toggleChild,
          child: const Text('Toggle Child'),
        ),
        if (_toggle)
          const ChildWidget(
            key: ValueKey('child1'),
          )
        else
          const ChildWidget(
            key: ValueKey('child2'),
          ),
      ],
    );
  }
}

class ChildWidget extends StatefulWidget {
  const ChildWidget({super.key});

  @override
  State<ChildWidget> createState() => _ChildWidgetState();
}

class _ChildWidgetState extends State<ChildWidget> {
  @override
  void didUpdateWidget(covariant ChildWidget oldWidget) {
    super.didUpdateWidget(oldWidget);
    print("updated widget with key ${widget.key}");
  }

  @override
  Widget build(BuildContext context) {
    return const Text('I am a Child Widget');
  }
}
```

> 상위 위젯에서 재빌드 해야하는 하위 위젯을 검사할 때, `didUpdateWidget`를 먼저 호출합니다. <br>
> 그리고 속성이나 상태값이 변경되었는지를 확인하고 변경되었다고 판단되면 `build`를 호출합니다.

#### setState()

`setState()` 메서드는 위젯의 내부 상태가 변경되어 업데이트가 필요하다는 것을 프레임워크에 알리는 역할을 합니다. 

상태를 수정할 때마다 이 메서드를 사용하여 위젯의 UI를 다시 빌드해야 합니다.

`setState()`에 전달된 콜백 함수는 즉시 동기적으로 호출됩니다. 이 콜백 함수는 상태를 즉시 업데이트하며, 비동기 작업을 수행하지 않습니다. 

따라서 `setState()`를 사용하여 상태를 변경할 때는 항상 동기적으로 호출해야 합니다.

비동기 반환이 불가하다는 점도 중요합니다. 콜백 함수는 `Future`를 반환해서는 안 되며, 즉 `async` 함수가 될 수 없습니다.

비동기 함수는 호출 즉시 완료되지 않으므로, 상태가 언제 실제로 설정되는지 명확하지 않으며, 이는 상태 관리와 UI 업데이트에 혼란을 초래할 수 있습니다.

#### deactivate()

`deactivate()` 메서드는 State 객체가 위젯 트리에서 제거될 때 프레임워크에 의해 호출됩니다.

이 시점에서 리소스를 해제하거나 상태를 정리할 수 있습니다.

`deactivate()`는 `GlobalKey`를 사용하는 경우, State 객체가 트리의 다른 위치에 재삽입될 수 있는 가능성을 염두에 둡니다.

`GlobalKey`는 위젯의 위치가 변경될 때 상태를 유지할 수 있도록 도와줍니다. 따라서, State 객체는 트리의 한 위치에서 다른 위치로 이동할 수 있으며, 이 과정에서 `deactivate()`가 호출됩니다.

아래는 `GlobalKey`를 사용하여 `deactivate()` 호출하는 예시입니다. (위젯이 다른 위치에 재삽입 되는 예시)

```dart
import 'package:flutter/material.dart';

class DeactivateWidget extends StatefulWidget {
  const DeactivateWidget({super.key});

  @override
  State<DeactivateWidget> createState() => _DeactivateWidgetState();
}

class _DeactivateWidgetState extends State<DeactivateWidget> {
  int counter = 0;

  void incrementCounter() {
    setState(() {
      counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: incrementCounter,
      child: Text('Counter: $counter'),
    );
  }
}

class DeactivateParentWidget extends StatefulWidget {
  const DeactivateParentWidget({super.key});

  @override
  State<DeactivateParentWidget> createState() => _DeactivateParentWidgetState();
}

class _DeactivateParentWidgetState extends State<DeactivateParentWidget> {
  final GlobalKey globalKey = GlobalKey();
  bool toggle = true;

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        ElevatedButton(
          onPressed: () {
            setState(() {
              toggle = !toggle;
            });
          },
          child: const Text('Toggle Position'),
        ),
        toggle ? DeactivateWidget(key: globalKey) : Container(),
        !toggle ? DeactivateWidget(key: globalKey) : Container(),
      ],
    );
  }
}
```

#### dispose()

`dispose()` 메서드는 State 객체가 위젯 트리에서 영구적으로 제거될 때 호출됩니다. 

이 메서드는 주로 리소스를 해제하고, 네트워크 요청을 취소하거나, 타이머를 중지하는 등 필요한 정리 작업을 수행하는 데 사용됩니다.

`dispose()`가 호출된 후에는 해당 State 객체를 다시 사용할 수 없으며, `mounted` 속성은 `false`로 설정됩니다. 

따라서 `dispose()`가 호출된 시점에서는 `setState()`를 호출할 수 없습니다. 이미 상태 객체가 제거된 상태이기 때문입니다.

중요한 점은 `dispose()` 메서드가 호출된 후 해당 State 객체는 다시 트리에 추가될 수 없다는 것입니다.

이는 `dispose()` 메서드가 상태 객체의 생명 주기에서 마지막으로 호출되는 메서드임을 의미합니다.

### StatelessWidget의 생명주기

`StatelessWidget`의 생명주기는 매우 간단합니다.

위젯의 생성자가 호출되고, `build()` 메서드를 호출하는 것이 끝입니다.

따라서 정적인 UI 구성 요소, 단순한 텍스트, 아이콘, 이미지 등 변경되지 않는 UI 요소에 사용됩니다.

`Row`, `Column`, `Container`와 같은 레이아웃 위젯도 대부분 `StatelessWidget`입니다.

## 마무리

이렇게 위젯의 생명주기에 대해 정리해봤습니다.

마지막으로 이해하기 쉽게 몇 가지 생명주기 순서 예시를 정리하고 마치겠습니다.

### 몇 가지 생명주기 순서 예시

#### 위젯이 처음 생성되는 경우

`createState()` -> `initState()` -> `didChangeDependencies()` -> `build()`

#### 부모 위젯의 구성이 변경되는 경우

`didUpdateWidget()` -> `build()`

#### 위젯의 상태가 변경되는 경우

`setState()` -> `build()`

#### 위젯이 트리에서 제거되었다가 다시 삽입되는 경우

`deactivate()` -> `activate()` -> `build()`

#### 위젯이 트리에서 영구적으로 제거되는 경우

`deactivate()` -> `dispose()`

## 참조

[the-journey-of-a-widget-understanding-the-lifecycle-in-flutter-3plp](https://dev.to/pranjal-barnwal/the-journey-of-a-widget-understanding-the-lifecycle-in-flutter-3plp)


[Flutter-LifeCycle생명주기](https://velog.io/@realryankim/Flutter-LifeCycle생명주기)

[responding-to-widget-lifecycle-events](https://docs.flutter.dev/ui#responding-to-widget-lifecycle-events)

[State-class](https://api.flutter.dev/flutter/widgets/State-class.html)

[widgets-library](https://api.flutter.dev/flutter/widgets/widgets-library.html)