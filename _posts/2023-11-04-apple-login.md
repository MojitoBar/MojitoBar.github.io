---
title: 애플 로그인 동작 방식 총 정리
author: mojitobar
date: 2023-11-04 00:21:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, Apple Login]
comments: true
---

## 기본적인 애플 로그인 동작 방식

애플 로그인은 사용자가 앱에 로그인할 때 사용자의 신원을 확인하는 데 사용됩니다. 이를 통해 사용자는 앱에 로그인할 때마다 새로운 계정을 만들거나 비밀번호를 입력할 필요 없이, 애플 ID를 사용하여 로그인할 수 있습니다.

아래 내용은 순서대로 애플 로그인이 어떻게 동작하는 지를 설명합니다.

### 1. 사용자 인터페이스 제공

사용자는 앱에서 'Apple로 로그인' 버튼을 클릭합니다. 이는 iOS 또는 macOS 앱에 내장된 UI 요소입니다.

### 2. 인증 요청

앱은 사용자가 버튼을 클릭하면 `ASAuthorizationAppleIDProvider`를 사용하여 애플 ID 인증을 요청합니다. 이 요청은 사용자가 애플 ID와 비밀번호를 입력하여 로그인하는 것으로 시작합니다. 또한 페이스 ID나 터치 ID를 사용할 수도 있습니다.

### 3. 사용자 동의

사용자가 성공적으로 인증을 마치면, 애플은 사용자에게 필요한 정보(예: 이름, 이메일)를 공유할 것인지 묻습니다. 사용자는 이 정보를 제공하거나 거부할 수 있으며, 심지어 고유한 무작위 이메일 주소를 생성하여 앱과 공유할 수도 있습니다.

### 4. 애플 서버로부터의 응답

사용자가 동의하면 애플 서버는 애플 로그인 요청을 처리하고, 앱에 `identity token`과 `authorization code`를 포함한 응답을 보냅니다. `identity token`은 사용자의 신원 정보를 암호화한 것이며, `authorization code`는 앱의 서버가 애플의 서버와 통신할 때 사용합니다.

### 5. 서버로 정보 전송

앱은 받은 `authorization code`를 자체 서버로 전송합니다. 서버는 이 코드를 사용하여 애플의 검증 서버에 요청을 보내 사용자의 신원을 확인합니다. (nonce 검사도 이 때 이루어짐.)

### 6. 애플의 검증 서버와 통신

앱의 서버는 `authorization code`와 함께 애플의 검증 서버에 요청을 보냅니다. 검증 서버는 코드의 유효성을 확인하고, 앱 서버에 `identity token`, `access token`, 그리고 `refresh token`을 반환합니다.

### 7. 신원 확인 및 세션 생성

앱 서버는 `identity token`을 해독하여 사용자의 신원을 확인합니다. 확인이 완료되면 사용자에 대한 세션을 생성하고, 필요한 경우 추가 사용자 정보(예: 이름, 이메일)를 데이터베이스에 저장합니다.

### 8. 사용자 경험

사용자는 이제 앱 서버가 생성한 세션을 통해 앱에서 로그인 상태로 설정됩니다. 앱은 사용자에게 로그인한 상태의 사용자 인터페이스(UI)를 보여주거나 다른 기능에 접근할 수 있게 됩니다.

### 9. 세션 유지

`access token`과 `refresh token`은 사용자가 앱에 로그인 상태를 유지할 수 있도록 합니다. `access token`이 만료되었을 때 `refresh token`을 사용하여 새로운 `access token`을 얻을 수 있습니다.

## QnA

### 4번에서 애플의 인증을 이미 받았다. 그런데 왜 5번에서 다시 애플의 검증 서버에 요청을 보내는 걸까?

4번 단계에서 애플은 사용자가 로그인을 완료하면 클라이언트 앱(사용자의 기기에 설치된 앱)에 `identity token`과 `authorization code`를 보냅니다.

1. Identity Token: 사용자의 애플 ID에 대한 암호화된 정보가 포함되어 있으며, 클라이언트 앱은 이 토큰을 사용하여 사용자가 애플 ID로 성공적으로 인증했음을 알 수 있습니다.
2. Authorization Code: 이 코드는 서버 측 검증을 위한 것입니다. 클라이언트 앱 자체는 이 코드를 사용하지 않고, 대신 앱의 백엔드 서버로 전송합니다.

5번 단계에서 앱의 서버는 `authorization code`를 받고, 이를 애플의 검증 서버로 보냅니다. 이는 두 가지 주요 목적을 위한 것입니다:

1. 보안 강화: 클라이언트 측(즉, 사용자의 기기)에서 받은 `identity token`과 `authorization code`는 변조될 수 있습니다. 서버 측 검증을 통해 앱의 서버는 이러한 정보가 실제로 애플로부터 온 것임을 확인합니다. 이 과정은 또한 Man-In-The-Middle 공격을 방지하는 데 도움이 됩니다.
2. 서버 토큰 획득: `authorization code`를 검증하면 애플의 서버는 `access token`과 `refresh token`을 앱의 서버로 보냅니다. 이 토큰들은 사용자가 앱에 계속 로그인 상태를 유지할 수 있도록 하며, 특히 `refresh token`은 `access token`이 만료된 후 새로운 `access token`을 획득하는 데 사용됩니다.

### 클라이언트 측에서 `identity token`과 `authorization code` 가 변조되는 시나리오는 뭘까?

클라이언트 측에서 받은 `identity token`과 `authorization code`가 변조될 수 있는 예시를 살펴보겠습니다:

1. 중간자 공격(Man-In-The-Middle Attack): 악의적인 사용자가 네트워크 트래픽을 가로채어 `identity token`과 `authorization code`를 획득하고, 변조된 정보로 앱 서버에 접근을 시도할 수 있습니다.
2. 리플레이 공격(Replay Attack): 악의적인 사용자가 유효한 데이터를 가로채어, 나중에 이를 재사용하여 서버에 요청을 보내는 경우, 서버는 이를 유효한 요청으로 착각하고 처리할 수 있습니다.
3. 클라이언트 측 보안 취약성: 사용자의 기기가 해킹당하거나 악성 소프트웨어에 감염되어, 인증 정보가 탈취되고 변조될 수 있습니다.

서버 측에서 추가적인 검증을 하는 것은 이런 종류의 보안 위협에 대응하기 위함입니다. 서버 측 검증을 통해 클라이언트에서 받은 정보의 진위를 애플의 검증 서버를 통해 다시 확인함으로써, 보안을 한층 더 강화할 수 있습니다.

### 왜 첫 번째 인증에 토큰값을 같이 보내지 않을까?

애플이 `identity token`과 `authorization code`를 클라이언트에 전송한 후, `access token`과 `refresh token`을 바로 보내지 않고 서버 측 검증을 요구하는 이유는 다음과 같습니다:

1. 분리된 책임: 클라이언트 앱은 사용자 인터페이스와 사용자 경험을 관리하는 책임을 가지며, 서버는 데이터와 인증을 관리하는 책임을 가집니다. `access token`과 `refresh token`은 서버에서 사용되기 때문에, 서버 측에서 직접 요청하여 획득하는 것이 적절합니다.
2. 보안 강화: `access token`과 `refresh token`은 앱의 백엔드와 애플 서버 간의 통신을 위한 것이며, 이러한 토큰을 클라이언트 측으로 직접 전송하는 것은 보안상의 위험을 증가시킬 수 있습니다. 이 토큰들은 애플 서비스와의 통신에 사용되므로, 서버를 통해서만 다루어져야 합니다.
3. 토큰의 유효성: 서버 측에서 `authorization code`를 사용하여 애플에 요청함으로써, 애플은 해당 코드가 실제로 사용되었음을 확인하고, 해당 세션에 대한 `access token`과 `refresh token`을 발급합니다. 이렇게 함으로써, 토큰 발급이 실제 인증된 요청에 대해서만 이루어지도록 보장합니다.

이 절차는 OAuth 2.0 프로토콜의 일부로, 클라이언트 앱과 서버 간의 역할을 분명히 하고, 토큰을 안전하게 관리하며, 서비스 제공자와 사용자 양쪽의 보안을 강화하기 위해 설계되었습니다.

### access token과 refresh token 두 개를 사용하는 이유

1. Access Token: 일반적으로 클라이언트 사이드에서 직접 사용됩니다. OAuth 2.0 인증 흐름에서 클라이언트는 사용자를 대신해 API에 요청을 보낼 때 이 토큰을 사용합니다. `access token`은 API 서버가 요청을 인증할 때 사용하므로 클라이언트에 전송되고 저장되어야 합니다.
2. Refresh Token: 이 토큰은 보통 보다 긴 생명 주기를 가지며 `access token`이 만료됐을 때 새로운 `access token`을 요청하기 위해 사용됩니다. 일반적인 베스트 프랙티스는 `refresh token`을 클라이언트가 아닌, 서버에서 관리하도록 하는 것입니다. 이는 만약 클라이언트가 침해당할 경우, 공격자가 장기적으로 사용자의 인증을 유지할 수 있는 수단을 갖게 되는 것을 방지하기 위함입니다.

특히, `refresh token`이 탈취되면, 공격자는 사용자의 인증 세션을 장기간 유지할 수 있으므로, 이를 클라이언트에 저장하는 것은 큰 보안 리스크를 수반합니다. 따라서 이를 보다 안전하게 관리하기 위해 서버 사이드에 `refresh token`을 저장하고, 필요할 때마다 서버를 통해 `access token`을 갱신하는 방식을 선호합니다.

결론적으로, `access token`은 클라이언트로 전송될 수 있으나, `refresh token`은 서버에 저장하는 것이 좋습니다. 그리고 이러한 토큰들은 HTTPS와 같은 안전한 프로토콜을 통해 전송되어야 합니다.

### Firebase를 이용해서 애플 로그인을 구현하면 ?

Firebase를 사용하여 애플 로그인을 구현할 때, Firebase 인증 시스템이 여러 저수준의 인증 세부 사항을 처리해 줍니다. 이럴 경우 다음과 같은 부분이 Firebase로 대체됩니다:

1. **클라이언트 인증 처리**: 사용자가 애플 ID로 로그인하면, 클라이언트 측 라이브러리(예: Firebase Auth SDK)가 애플에서 제공하는 인증 정보(예: authorization code, identity token)를 취득합니다.
2. **서버와의 통신**: 클라이언트가 취득한 인증 정보는 Firebase 인증 시스템에 전달되며, Firebase는 이 정보를 사용해 자체적으로 애플과 통신하여 사용자 인증을 처리합니다.
3. **애플 인증 서버와의 상호작용**: Firebase 서버는 authorization code와 identity token을 이용해 애플의 인증 서버와 통신하여 필요한 검증과 토큰 교환을 수행합니다.
4. **세션 관리 및 토큰 갱신**: Firebase 인증은 자동으로 세션을 관리하고, 필요할 경우 refresh token을 사용하여 access token을 갱신합니다. 사용자는 이 세부사항에 대해 걱정할 필요가 없습니다.
5. **사용자 정보 관리**: Firebase 인증은 사용자 정보를 Firebase 인증 데이터베이스에 저장하며, 개발자는 Firebase 제공 API를 통해 사용자 정보에 접근할 수 있습니다.
6. **보안 및 규칙 설정**: Firebase는 사용자의 로그인 상태를 바탕으로 Firebase 데이터베이스 및 스토리지에 대한 접근을 관리할 수 있는 보안 규칙을 설정할 수 있게 해 줍니다.

이 과정에서 Firebase는 대부분의 백엔드 작업을 대신 처리해주기 때문에, 애플 로그인을 구현하기 위해 복잡한 서버사이드 로직을 작성할 필요가 없습니다. 개발자는 사용자 인증을 위한 로직을 클라이언트 사이드에서 간단히 처리하고, 나머지는 Firebase에 맡기면 됩니다.

## 랜덤 문자열 nonce을 생성하는 이유는?

로그인 요청마다 임의의 문자열인 '[nonce](https://firebase.google.com/docs/auth/ios/apple?hl=ko&authuser=0&_gl=1*s9k3xz*_ga*NDgxODM1MzAyLjE2OTgyMDk0NDA.*_ga_CW55HF8NVT*MTY5ODk4NDExNS40LjEuMTY5ODk4NDY5OS43LjAuMA)'가 생성되며, 이 nonce는 앱의 인증 요청에 대한 응답으로 ID 토큰이 명시적으로 부여되었는지 확인하는 데 사용됩니다. 재전송 공격을 방지하려면 이 단계가 필요합니다.

Firebase를 사용하여 Apple 로그인을 구현하는 맥락에서 nonce 또는 무작위로 생성된 문자열은 중요한 보안 용도로 사용됩니다. 작동 방식은 다음과 같습니다

1. **nonce 생성**: 사용자가 Apple로 로그인을 시도하면 애플리케이션은 nonce라고 하는 고유한 무작위 문자열을 생성합니다. 그런 다음 이 nonce는 Apple 인증 프로세스에 전달됩니다.
2. **인증에 사용**: Apple의 인증 서버는 사용자의 ID 토큰을 생성할 때 이 nonce를 사용합니다. nonce는 토큰에 내장되어 특정 로그인 요청에 고유하게 연결됩니다.
3. **확인**: 애플리케이션이 Apple로부터 ID 토큰을 받으면 토큰에 처음에 전송한 것과 동일한 nonce가 포함되어 있는지 확인할 수 있습니다. 이 단계는 보안을 위해 매우 중요합니다.
4. **보안 목적**: nonce의 주요 목적은 리플레이 공격을 방지하는 것입니다. 리플레이 공격에서 공격자는 ID 토큰을 가로채서 이를 사용하여 사용자 계정에 무단으로 액세스하려고 시도할 수 있습니다. 그러나 nonce는 일회성 문자열이므로 이미 사용된 nonce를 사용하는 토큰은 유효하지 않은 것으로 쉽게 식별할 수 있습니다.
5. **고유성 보장**: nonce는 고유하면서도 예측할 수 없는 것이 중요합니다. 이는 각 인증 세션이 고유하고 위조할 수 없음을 보장합니다.

요약하면, Firebase를 통한 Apple 로그인에서 nonce는 인증 프로세스가 안전하고 ID 토큰이 실제로 사용자가 시작한 특정 로그인 세션에 대한 것임을 보장하는 보안 조치 역할을 합니다.

### 여기서 이야기하는 재전송 공격(replay attack)이 뭘까?

온라인 뱅킹 시스템과 관련된 간단한 예를 살펴보겠습니다.

1. **초기 거래**: 온라인 뱅킹 계좌에 로그인하여 거래를 시작합니다(예: 친구에게 100만원 이체).

   이 거래에는 인증 정보(토큰 또는 세션 ID 등)와 거래 세부정보가 포함된 데이터 패킷을 인터넷을 통해 전송하는 작업이 포함됩니다.

2. **공격자에 의한 가로채기**: 네트워크 트래픽을 모니터링하던 공격자가 이 데이터 패킷을 가로채는 것입니다.

   공격자는 나의 인증 토큰 사본과 100만원 거래 세부 정보를 갖게 되었습니다.

3. **공격자에 의한 재생**: 나중에 공격자는 가로채는 데이터를 사용하여 동일한 거래 요청을 은행 서버에 보냅니다.

데이터 패킷에는 유효한 인증 토큰이 있으므로 은행 서버는 그것이 내가 보낸 합법적인 요청이라고 생각하고 친구에게 추가로 100만원 이체를 처리합니다.

4. **결과**: 이 재생 공격으로 인해 나는 한 번만 승인했음에도 불구하고 동일한 거래에 대해 나의 계정에서 두 번 인출됩니다.

다시 로그인으로 돌아오면,
애플로 보내는 요청 4번(애플 서버로부터 응답)에서 identity token을 보낼때 nonce를 포함해서 전송합니다.

```swift
func startSignInWithAppleFlow() {
    let nonce = randomNonceString()
    currentNonce = nonce
    let appleIDProvider = ASAuthorizationAppleIDProvider()
    let request = appleIDProvider.createRequest()
    request.requestedScopes = [.fullName, .email]
    request.nonce = sha256(nonce)
    // 여기!!
    let authorizationController = ASAuthorizationController(authorizationRequests: [request])
    authorizationController.delegate = self
    authorizationController.presentationContextProvider = self
    authorizationController.performRequests()
}
```

그리고 다시 6번(애플의 검증 서버와 통신)에서 엑세스 토큰을 요청할 때 identity token과 nonce를 같이 보냅니다.

```swift
let credential = OAuthProvider.appleCredential(withIDToken: idTokenString, rawNonce: nonce, fullName: appleIDCredential.fullName)
```

이 때 서버(여기선 firebase)는 nonce와 identity token의 nonce랑 같은지 확인합니다. nonce는 일회용이기 때문에 다음에는 해당 nonce로 인증받을 수 없습니다.

이렇게 재전송 공격을 방지할 수 있습니다.
