---
title: weak, unowned의 성능 차이
author: mojitobar
date: 2023-10-10 16:13:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, weak, unowned, 메모리, ARC]
comments: true
---

## 개요

스위프트에서 weak와 unowned의 차이점으로 성능 차이가 많이 언급되는데요,

이번에는 weak와 unowned가 어떻게 동작하는지, 왜 성능 차이가 발생하는지에 대해 알아보려해요.

## 참조 카운트

스위프트는 ARC라는 자동 참조 카운팅을 사용하여 앱의 메모리 사용량을 추적하고 관리해요.

자세한 내용은 [공식 문서](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting/)에 나와있어요.

여기선 조금 더 자세하게 스위프트가 참조 카운트(RefCount)를 어떻게 구현하고 있는지에 대해 다룰거에요.

이 내용은 [스위프트 깃허브 레파지토리](https://github.com/apple/swift/blob/main/stdlib/public/SwiftShims/swift/shims/RefCount.h)에서 확인해볼 수 있어요.

### 참조 카운트 (RefCounts)

각 객체는 세 가지 참조 카운트를 가져요. strong, unowned, weak.

이러한 카운트는 객체 내부에 직접 저장되거나 **side table entry**에 저장돼요.

### 참조 카운트의 동작

- **Strong RC**: 객체에 대한 강한 참조를 계산해요. 이 카운트가 0이 되면 객체는 **deinitialized**되고, unowned 참조 읽기는 오류가 발생하며, weak 참조 읽기는 nil이 돼요.
- **Unowned RC**: 객체에 대한 unowned 참조를 계산해요. 이 카운트가 0이 되면 객체의 할당이 해제돼요.
- **Weak RC**: 객체에 대한 약한 참조를 계산해요. 이 카운트가 0이 되면 **side table entry**가 해제돼요. 그리고 객체의 참조에 따른 라이프 사이클은 아래 그림과 같아요.

![image](https://github.com/MojitoBar/MojitoBar.github.io/assets/16567811/e753729b-a4e2-4c2d-9cda-5b6c533a66d5)

## 객체 참조에 따른 메모리 주기

- **DEINITING 상태**: deinit() 메소드가 호출되고 완료될 때 객체의 strong 참조 카운트가 0이고 unowned 참조가 없다면, 객체는 메모리에서 즉시 해제돼요. 그렇지 않으면 DEINITED 상태로 전환되죠.
- **DEINITED 상태**: 객체의 deinit() 메소드가 이미 호출되었지만 여전히 unowned 참조가 남아 있는 상태에요. 이 상태에서 unowned 참조 카운트가 0이 되면 객체는 메모리에서 해제돼요.
- **FREED 상태**: 객체는 이미 메모리에서 해제되었지만, side table에 대한 약한 참조가 아직 있을 때의 상태에요. 이 상태에서 weak 참조 카운트가 0이 되면 side table 엔트리도 해제돼요. 그리고 Dead 상태가 객체와 side table까지 전부 메모리에서 해제된 상태이죠.

RefCount의 소스를 보면 주석으로 작성된 설명을 볼 수 있어요. 간단하게 정리하자면 이렇습니다.

객체가 처음 초기화될 때 3개의 참조 카운터는 각각 1, 1, 1로 초기화 돼요.
그리고 strong 참조 카운트가 0이되어 deinit이 호출되면 unowned 참조 카운트도 1 감소하죠.

만약 unowned 참조 카운트가 0이 된다면 인스턴스가 메모리에서 해제되는 Freed 상태가 돼요.
weak 참조 카운트는 객체가 메모리에서 해제되면 1 감소하는데, 참조 카운트가 0이되면 side table entry가 메모리에서 해제돼요.

즉, strong -> unowned -> weak 순으로 참조 카운트가 감소하고, 각각의 참조 카운트가 0이 되면 객체는 메모리에서 해제돼요.

잘 이해했는지 모르겠는데 weak 참조 변수는 객체가 아니라 사이드 테이블을 참조하기 때문에 참조하는 객체가 nil이 될 때 한 번에 해제할 수 있는 것 같아요.

결론은 weak의 경우 사이드 테이블이라는 별도의 메모리 구조를 사용하기 때문에 메모리 및 관련 연산 오버헤드가 더 많아요.
따라서 적절한 상황에서 unowned를 사용할 경우 성능 면에서 이점을 챙길 수 있게돼요.

## 마무리

스위프트를 공부하다보면 순환참조와 관련해서 ARC를 학습하게 되는데요, 참조 카운트가 단순히 숫자 하나가 총 3개의 참조 카운트로 이뤄져 있다는 점이 새로웠어요. 그리고 객체의 참조에 따른 상태도 여러개가 있다는 것을 알게 되었어요.

역시 내부를 들여다보는 방식의 공부가 시간은 오래걸려도 재미있긴 한 것 같아요.
