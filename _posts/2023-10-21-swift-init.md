---
title: init(coder:)와 init(frame:) 파해치기
author: mojitobar
date: 2023-10-21 22:15:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, init, init(coder:), init(frame:)]
comments: true
---

## 개요

UIView를 상속받아 커스텀 뷰을 그리다보면 init(coder:)와 init(frame:)을 만나게 되는데요.

이 두 생성자가 어떤 역할을 하는 지, 그리고 왜 init(coder:) 생성자가 필수인지에 대해 알아보았어요.

## init(frame:)과 init(coder:)

init(frame:)과 init(coder:)는 iOS에서 UIView를 상속받는 클래스를 커스터마이징할 때 자주 마주치게 되는 두 개의 초기화 메서드에요. 이 두 메서드는 UIView 및 해당 서브클래스들의 생성 시 사용되며 각각 다른 상황에서 호출돼요.

### init(frame:)

이 초기화 메서드는 프로그래밍 방식으로 뷰를 생성할 때 호출돼요.

- 예를 들어, `let myView = CustomView(frame: CGRect(x: 0, y: 0, width: 100, height: 100))`와 같은 방식으로 뷰를 생성하면 이 메서드가 호출돼요.

- frame 파라미터를 통해 뷰의 위치와 크기를 설정해요.

- 프로그래밍적으로 생성된 뷰에 대한 초기 설정이나 추가적인 구성이 필요한 경우, 이 메서드 내에서 해당 작업을 수행하면 돼요.

### init(coder:)

이 초기화 메서드는 Interface Builder(IB)에서 뷰 또는 뷰 컨트롤러를 로드할 때 호출돼요. 주로 스토리보드나 XIB 파일을 사용하는 경우에 해당돼요.

- NSCoder 객체는 뷰나 뷰 컨트롤러의 상태를 압축 또는 아카이빙하는 데 사용돼요.

- 만약 사용자 정의 뷰를 스토리보드에서 사용하려면 init(coder:)를 오버라이드해야 해요.

- 스토리보드로부터 뷰가 로드될 때 특정한 설정이나 구성이 필요하다면 이 메서드 내에서 해당 작업을 수행하면 돼요.

이렇듯 init(code:) 생성자는 IB에서 로드할 때 호출되는 생성자에요.

---

| 그럼 뷰를 코드로만 작성할거라면 init(coder:)를 호출될 일이 없을 것 같은데 작성 안 해도 되는거 아닌가요?

결론부터 이야기 하자면 그렇지가 않아요.

## NSCoding

일단 위에서 잠깐 나온 “**NSCoder 객체는 뷰나 뷰 컨트롤러의 상태를 아카이빙하는 데 사용된다**”는 부분을 자세하게 이해할 필요가 있어요. init(coder:) 초기화 메소드는 스토리보드나 Xib 파일로 뷰를 생성할 때 호출되는 초기화라고 했는데요, 이 때의 파일들은 XML 형태로 저장되어 있어요.

이러한 형태를 화면에 그리기 위해서는 언아카이빙(역직렬화)가 필요해요. 그리고 그러한 역할을 하는것이 바로 NSCoding 프로토콜이에요. UIView는 NSCoding 프로토콜을 채택하고 있어요.

![image](https://github.com/f-lab-edu/pins/assets/16567811/093e9801-cdcc-42fc-9cf4-2054c275da5c)

NSCoding 프로토콜은 다음과 같이 구현되어 있어요.

```swift
@protocol NSCoding
- (void)encodeWithCoder:(NSCoder \*)coder;
- (nullable instancetype)initWithCoder:(NSCoder \*)coder; // NS_DESIGNATED_INITIALIZER
@end
```

@protocol NSCoding의 정의에서, **`NS_DESIGNATED_INITIALIZER`** 매크로가 붙어 있는 `initWithCoder:` 메서드가 `init(coder:)`의 Objective-C 버전이에요. 이 NS*DESIGNATED*`INITIALIZER는 이 초기화 메서드가 디자인된 초기화 메서드임을 나타내죠.

Objective-C에서 `NS_DESIGNATED_INITIALIZER는` 서브클래스가 슈퍼클래스의 해당 초기화 메서드를 오버라이드할 때, 그 초기화 메서드 내에서 슈퍼클래스의 동일한 초기화 메서드를 호출해야 함을 나타내요.

따라서, `NS_DESIGNATED_INITIALIZER` 매크로가 붙어 있는 `initWithCoder:` 메서드가 바로 `init(coder:)`가 필수로 구현되어야 한다는 것을 명시하는 부분입니다. Objective-C의 NSCoding 프로토콜을 Swift에서 채택하고 준수할 때, 이러한 디자인 초기화 메서드의 의미로 인해 `init(coder:)`는 required로 표시되어 구현되어야 합니다.

`init(coder:)` 는 필수로 구현되어야 하는 메소드이고 UIView를 상속받으면 반드시 작성되어야 합니다.

## 그런데 왜 init? 일까?

init?(coder:)에서 ?는 이 초기화 메서드가 실패 가능한 초기화 메서드(failable initializer)임을 나타내요. 이는 초기화 과정에서 어떤 문제가 발생할 경우 초기화를 실패시키고 nil을 반환할 수 있음을 의미하죠.

왜 실패 가능한 초기화 메서드가 필요한지를 이해하려면 NSCoding 프로토콜과 관련된 아카이브 및 언아카이브 과정을 고려해야 해요.

언아카이브 (즉, init(coder:)를 호출하는 과정) 중에는 여러 가지 문제가 발생할 수 있어요. 예를 들면,

1. 아카이브된 데이터가 손상되었을 수 있어요.

2. 아카이브 데이터의 형식이 기대한 것과 다를 수 있어요.

3. 필요한 키 또는 값이 NSCoder 객체 내에 없을 수 있어요.

등등 다른 다양한 이유로 인한 디코딩 오류로 초기화 과정에서 문제가 발생하면 안전하게 초기화를 실패할 수 있기 떄문에 nil을 반환하는 것이에요. 이렇게 함으로써 개발자는 초기화의 실패를 적절하게 처리할 수 있게 돼요.

## 마무리

번외로 IB에서 객체를 로드할 때 라이프사이클에 `awakeFromNib`라는 메소드가 있어요.

호출 시점: Interface Builder의 아카이브(스토리보드나 XIB)에서 객체가 완전히 로드(모든 객체에 대한 아웃랫과 액션 연결) 되고 초기화된 후에 호출돼요.

목적: Interface Builder에서 설정한 속성들이 객체에 적용된 후에 추가적인 설정이나 커스터마이징을 수행하기 위한 용도로 주로 사용돼요.

스토리보드/XIB 외부에서의 호출: `awakeFromNib`는 Interface Builder에서 생성된 객체만을 대상으로 호출돼요. 코드를 사용하여 프로그래밍 방식으로 생성된 객체에는 호출되지 않아요.

호출 순서: `awakeFromNib`는 `init(coder:)` 초기화 메서드가 호출된 후에 호출돼요. 따라서 객체의 기본 초기화가 먼저 이루어진 후 추가적인 설정을 `awakeFromNib`에서 수행할 수 있어요.

이 메서드는 NSObject에 정의되어 있어요. 그리고 UIView는 다음과 같은 계층으로 NSObject를 상속받고 있죠.

```
NSObject
↘
UIResponder
↘
UIView
```

따라서 커스텀 뷰를 IB에서 로드하게 되면 `init(coder:)` 초기화 메서드가 실행되고 이후에 `awakeFromNib` 메서드가 호출돼요.
