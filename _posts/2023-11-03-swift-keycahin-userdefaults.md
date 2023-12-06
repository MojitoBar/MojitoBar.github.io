---
title: 키체인과 유저디폴트에 대해서 알아보자
author: mojitobar
date: 2023-11-03 12:48:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, Keychain, UserDefaults]
comments: true
---

## 개요

iOS에서 데이터를 저장하는 방법에는 여러가지가 있습니다. 그 중에서도 키체인(Keychain)과 유저 디폴트(User Defaults)는 가장 많이 사용되는 방법이에요.

이번 글에서는 키체인과 유저 디폴트의 차이점에 대해 알아보고, 각각의 특징과 사용 목적에 따라 어떤 상황에 사용하는 것이 좋을 지 알아보겠습니다.

## 키체인 vs 유저디폴트의 차이점

1. 보안성 (Security)

   - 키체인: 키체인은 iOS에서 제공하는 보안 저장소로, 사용자의 비밀번호, 토큰 등 민감한 정보를 암호화하여 안전하게 저장합니다. 앱 간 공유가 가능하며, 기기 보안 수준에 따라 데이터 접근을 제어할 수 있습니다.
   - 유저 디폴트: 유저 디폴트는 키-값 쌍으로 데이터를 저장하는 간단한 방식으로, 주로 사용자의 선호도나 설정 같은 비민감한 정보를 저장하는 데 적합합니다. 보안성은 키체인보다 낮으며, 데이터는 암호화되지 않고 플레인 텍스트로 저장됩니다.

2. 사용 목적 (Purpose)

   - 키체인: 주로 민감한 데이터를 안전하게 보관하는 데 사용됩니다. 예를 들어, 사용자 인증 정보, 암호화된 키, 크레딧 카드 번호 등이 여기에 해당합니다.
   - 유저 디폴트: 애플리케이션의 설정이나 상태 정보 같은 경량 데이터를 저장하는 데 사용됩니다. 예를 들면, 사용자의 언어 설정, 볼륨 설정 등이 있습니다.

3. 저장 데이터의 유형 (Type of Data Stored)

   - 키체인: 민감한 정보, 암호화된 데이터.
   - 유저 디폴트: 일반적인 설정, 사용자의 선호도.

4. 액세스 속도 (Access Speed)

   - 키체인: 암호화 및 보안 처리로 인해 유저 디폴트보다 약간 느릴 수 있습니다.
   - 유저 디폴트: 상대적으로 빠른 액세스 속도를 제공합니다.

5. 용도에 따른 선택 (Choosing Based on Use-Case)
   - 보안이 중요한 정보는 키체인에 저장하고, 애플리케이션 설정이나 사용자 선호 같은 비민감한 정보는 유저 디폴트에 저장하는 것이 좋습니다.

## UserDefaults 예제

```swift
// Saving a value
UserDefaults.standard.set("Hello, World!", forKey: "sampleString")

// Retrieving a value
if let retrievedString = UserDefaults.standard.string(forKey: "sampleString") {
    print(retrievedString) // Outputs: Hello, World!
}
```

## Keychain 예제

```swift
import Security

// Prepare a keychain query
let query: [String: Any] = [
    kSecClass as String: kSecClassGenericPassword,
    kSecAttrAccount as String: "user@example.com",
    kSecValueData as String: "myPassword".data(using: .utf8)!
]

// Add data to keychain
let status = SecItemAdd(query as CFDictionary, nil)

// Check if it was successful
if status == errSecSuccess {
    print("Password added successfully!")
} else {
    print("Error adding password: \(status)")
}
```

## 저장되는 위치의 차이

### 키체인(Keychain)

키체인에 저장된 데이터는 iOS의 보안 시스템에 의해 관리되는 암호화된 데이터베이스에 저장됩니다.

이 데이터베이스의 정확한 위치는 운영 체제에 의해 숨겨져 있으며, 직접 접근할 수 없습니다. 키체인은 보안이 중요한 데이터를 안전하게 보호하기 위해 설계되었습니다.

각 애플리케이션은 독립적인 저장 공간(샌드박스)을 가지며, 필요에 따라 특정 규칙을 따르는 애플리케이션 간에 데이터를 공유할 수 있습니다.

### 유저 디폴트(User Defaults)

유저 디폴트에 저장된 데이터는 애플리케이션의 샌드박스 내부에 위치한 `.plist` 파일(프로퍼티 리스트) 형태로 저장됩니다.

이 파일은 각 애플리케이션의 `Library/Preferences` 폴더 내에 위치합니다.

유저 디폴트는 암호화되지 않으며, 주로 애플리케이션의 설정이나 사용자의 선호도와 같은 비민감한 정보를 저장하는 데 사용됩니다.

## 번외 Keychain 사용법

### save 메서드

```swift
static func save(key: String, data: Data) -> OSStatus {
    let query = [
        kSecClass as String: kSecClassGenericPassword as String,
        kSecAttrAccount as String: key,
        kSecValueData as String: data
    ] as [String: Any]

    SecItemDelete(query as CFDictionary)
    return SecItemAdd(query as CFDictionary, nil)
}
```

- 목적: 이 메서드는 주어진 `key`에 연결된 `data`를 Keychain에 저장합니다.
- query 딕셔너리: Keychain에 데이터를 저장하기 위한 쿼리입니다. 이 딕셔너리는 세 가지 주요 요소를 포함합니다:
  - `kSecClass`: 저장할 데이터의 종류를 나타냅니다. 여기서는 일반적인 비밀번호(`kSecClassGenericPassword`)로 설정합니다.
  - `kSecAttrAccount`: 저장할 데이터와 연결된 키를 나타냅니다. 이는 데이터를 검색할 때 사용됩니다.
  - `kSecValueData`: 실제로 저장될 데이터입니다. `Data` 타입이어야 합니다.
- SecItemDelete: 같은 키로 저장된 이전 항목이 있다면 삭제합니다.
- SecItemAdd: 새로운 데이터 항목을 Keychain에 추가합니다.
- 반환 값: `OSStatus` 타입으로, 작업의 성공 여부를 나타냅니다.

### load 메서드

```swift
static func load(key: String) -> Data? {
    let query = [
        kSecClass as String: kSecClassGenericPassword,
        kSecAttrAccount as String: key,
        kSecReturnData as String: kCFBooleanTrue!,
        kSecMatchLimit as String: kSecMatchLimitOne
    ] as [String: Any]

    var dataTypeRef: AnyObject? = nil
    let status: OSStatus = SecItemCopyMatching(query as CFDictionary, &dataTypeRef)
    if status == noErr {
        return dataTypeRef as? Data
    } else {
        return nil
    }
}
```

- 목적: 이 메서드는 주어진 `key`에 연결된 데이터를 Keychain에서 불러옵니다.
- query 딕셔너리: Keychain에서 데이터를 검색하기 위한 쿼리입니다.
  - `kSecReturnData`: 검색된 데이터를 `Data` 객체로 반환받기 위해 `true`로 설정합니다.
  - `kSecMatchLimit`: 검색 결과의 한계를 나타냅니다. 여기서는 단일 항목만 반환하도록 `kSecMatchLimitOne`을 사용합니다.
- SecItemCopyMatching: 주어진 쿼리에 해당하는 Keychain 항목을 찾습니다.
- 반환 값: 찾은 데이터를 `Data` 타입으로 반환하거나, 실패했을 경우 `nil`을 반환합니다.

## 마무리

이번 글에서는 키체인과 유저 디폴트의 차이점에 대해 알아보았어요.

키체인은 iOS에서 제공하는 보안 저장소로, 사용자의 비밀번호, 토큰 등 민감한 정보를 암호화하여 안전하게 저장합니다.

유저 디폴트는 키-값 쌍으로 데이터를 저장하는 간단한 방식으로, 주로 사용자의 선호도나 설정 같은 비민감한 정보를 저장하는 데 적합합니다.

따라서 보안이 중요한 정보는 키체인에 저장하고, 애플리케이션 설정이나 사용자 선호 같은 비민감한 정보는 유저 디폴트에 저장하는 것이 좋습니다.
