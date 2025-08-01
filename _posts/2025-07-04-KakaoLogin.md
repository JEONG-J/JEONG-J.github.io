---
layout: post
title: SwiftUI 카카오톡 로그인
description: SwiftUI에서 카카오톡 소셜 로그인을 위한 Xcode에 연결
featuredImage: 
excerpt: "SwiftLibrary : KakaoLogin"
writer: JEONG Eui chan
categories:
  - Development
tags:
  - SwiftLibrary
  - SocialLogin
toc: true
toc_sticky: true
data: 2025-07-04
last_modified_at: 2025-07-04
image:
  path: /assets/img/Kakao/kakaoCover.png
featured: true
hidden: false
---

## 목차

- [😚 카카오 앱 등록하기](#-카카오-앱-등록하기)
- [😄 초기 설정](#-초기-설정)
- [😚 설치 방법](#-설치-방법)
- [😃 프로젝트 설치](#-프로젝트-설치)
- [😆 초기화](#-초기화)
- [🥹 KakaoLogin 호출](#-kakaologin-호출)

<br>

모바일 앱에서 `소셜 로그인` 기능은 사용자 경험을 높이는 중요한 요소 중 하나입니다. 특히 국내 사용자들에게 익숙한 `카카오톡 로그인`은 회원가입 절차를 간소화하고 접근성을 높이는데 유용하죠!

카카오 로그인 기능을 사용하기 위해 Xcode에 어떻게 적용하고 사용할 수 있는지 단계별로 정리합니다!

<br>

## 😚 카카오 앱 등록하기
---
```
https://developers.kakao.com/console/app
```

먼저 해당 사이트를 통해 앱을 등록해야 합니다.
앱 아이콘, 회사명 등 빈칸의 내용을 전부 채워야합니다!

전부 다 채웠으면 앱 등록 완성!

<div align="center">
<img src="/assets/img/Kakao/step1.png" width="600" alt="카카오 설정1"/>
</div>

<br>

##  😄 초기 설정
---
로그인 활성화, 번들 아이디 등록 등 필요 사항들에 대해 초기 설정을 다음과 같이 진행합니다.

<br>

<div style="display: flex; gap: 10px;">
<img width="400" alt="step1" src="/assets/img/Kakao/step2.png" />
<img width="400" alt="step2" src="/assets/img/Kakao/step3.png" />
<img width="400" alt="step3" src="/assets/img/Kakao/step4.png" />
</div>

<br>

> 네이티브 앱키를 꼭 확인 해야합니다.! <br>
<img width="600" alt="step3" src="/assets/img/Kakao/step8.png" />

## 😚 설치 방법
---
### SPM으로 설치 진행하기
```
https://github.com/kakao/kakao-ios-sdk
```
Xcode 상단의 [File > Add Package]를 통해 아래 URL을 입력하여 SDK를 설치합니다.
아래 사진과 같이 카카오 계정 로그인에 필요한 모듈을 선택해서 설치합니다.
<div>
<img width="600" alt="step1" src="/assets/img/Kakao/step5.png" />
</div>

<br>

## 😃 프로젝트 설치
---
### Info.plist 설정

**Queried URL Schemes**를 추가하여 해당 키의 아이템 값으로 **kakaokompassauth**를 추가해줍니다.
<div>
<img width="600" alt="step1" src="/assets/img/Kakao/step6.png" />
</div>

### URL Schemes 설정

[Target] -> [Info] -> [URL Types]에 **URL Scheme** 를 추가합니다.  <br>
**URL Scheme** 값을 작성할 때 kakao{네이티브 앱 키}를 넣어서 작성하면 됩니다. <br>
네이티브 앱 키는 초기 설정을 부분에서 확인할 수 있어요!

<Br>

> 왜 **URL Schemes**를 작성할까요?? <br>
> * 사용자가 카카오톡 앱을 통해 로그인 인증을 하면 인증 결과를 전달하기 위해 **다시 원래 앱으로 돌아와야 합니다.**
> * 이때 URL Scheme은 카카오가 우리 앱을 다시 열기 위한 고유한 식별자 역할을 합니다.
> * 즉, 인증 완료 후 돌아갈 주소를 등록해 두는 것과 같습니다.

<br>

## 😆 초기화
---
App.swift의 초기화 함수 init()에서 Kakao SDK 초기화 함수를 작성해야 합니다.
```swift
import SwiftUI
import KakaoSDKCommon
import KakaoSDKAuth

@main
struct TestKakaoApp: App {
    
    init() {
        KakaoSDK.initSDK(appKey: "네이티브 앱 키")
    }
    var body: some Scene {
        WindowGroup {
            ContentView()
                .onOpenURL(perform: { url in
                    if (AuthApi.isKakaoTalkLoginUrl(url)) {
                        _ = AuthController.handleOpenUrl(url: url)
                    }
                })
        }
    }
}
```

### 왜 .onOpenURL을 작성할까??

> 애플 공식문서에 아래와 같이 작성되어 있습니다. <br>
> Registers a handler to invoke in response to a URL that your app receives.

뷰가 속한 Scene 또는 Window에 대한 URL을 받았을 때 호출할 Handler를 등록하는 함수입니다.

즉, 다시 설명하면
1. 카카오톡 앱이 URL Scheme을 통해 우리 앱으로 복귀합니다. <br>
2. 이때 우리 앱이 해당 **URL을 받아서 로그인 처리**를 이어가야 합니다. <br>
3. 이 URL을 처리하기 위해 openURL이 필요합니다.

해당 URL을 처리해서 카카오 로그인 인증 결과를 이어서 처리하기 위해 <font color="#ff0000">AuthController.handleOpenURL(url:)</font>을 작성합니다.

## 🥹 KakaoLogin 호출

카카오 로그인을 하기 위해 사용자는 카카오톡 또는 웹 브라우저를 통해 인증을 진행하게 됩니다.
로그인이 완료되면 <font color="#ff0000">액세스 토큰</font>을 통해 사용자 정보를 받아올 수 있습니다!

우리는 어떻게 받아올까요??

### 카카오 웹 로그인

```swift
@MainActor
    private func loginWithKakaoWeb() async throws -> OAuthToken {
        try await withCheckedThrowingContinuation { continuation in
            UserApi.shared.loginWithKakaoAccount { token, error in
                if let error = error {
                    continuation.resume(throwing: error)
                } else if let token = token {
                    continuation.resume(returning: token)
                } else {
                    continuation.resume(throwing: KakaoLoginError.failedToLoginWithKakaoWeb)
                }
            }
        }
    }
```

`withCheckedThrowingContinuation`라는 문법을 왜 사용할까요??
<font color="#ff0000">비동기 함수 async/await으로 기존의 콜백 기반 코드를 변환</font>할 때 사용되는 고급 도구입니다.
즉, 기존의 completion handler(콜백) 기반 코드를 async await 함수로 변환하기 위해 사용합니다.
비동기 작업에서 오류를 던질 수 있도록 throws 처리하기 위해 사용합니다.
<br>
카카오 계정으로 웹 로그인을 시도하고, 
성공하면 OAuthToken을 반환하고,
실패하면 throw로 에러를 던집니다.

### 카카오 앱 로그인

```swift
@MainActor
    private func loginWithKakaoApp() async throws -> OAuthToken {
        try await withCheckedThrowingContinuation { continuation in
            UserApi.shared.loginWithKakaoTalk { token, error in
                if let error = error {
                    continuation.resume(throwing: error)
                } else if let token = token {
                    continuation.resume(returning: token)
                } else {
                    continuation.resume(throwing: KakaoLoginError.failedToLoginWithKakaoApp)
                }
            }
        }
    }
```

카카오 앱 로그인 또한, 카카오 웹 로그인과 마찬가지로 같은 로직입니다.

### 유저 정보 획득

```swift
@MainActor
    private func getUserInfo(token: OAuthToken) async throws -> KakaoUser {
        try await withCheckedThrowingContinuation { continuation in
            UserApi.shared.me { user, error in
                if let error = error {
                    continuation.resume(throwing: KakaoLoginError.unknown(error))
                } else if let user = user {
                    let kakaoUser = KakaoUser(
                        nickname: user.kakaoAccount?.profile?.nickname ?? "닉네임 정보 없음",
                        accessToken: token.accessToken
                    )
                    continuation.resume(returning: kakaoUser)
                } else {
                    continuation.resume(throwing: KakaoLoginError.failedToFetchUserInfo)
                }
            }
        }
    }
```

앱/웹을 수단으로 카카오 로그인 후 전달받은 OAuthToken을 기반으로 카카오의 사용자 정보를 요청합니다. <br>
성공 시 KakaoUser 객체로 모델값을 저장합니다. <br>
하지만 실패 시, throw로 에러를 발생시킵니다.

> 위 세 가지 코드를 사용하여 실제로 사용할 수 있도록 구성하면 아래와 같습니다.

```swift
@MainActor
    public func login() async throws -> KakaoUser {
        let token: OAuthToken
        
        if UserApi.isKakaoTalkLoginAvailable() {
            do {
                token = try await loginWithKakaoApp()
            } catch {
                throw KakaoLoginError.failedToLoginWithKakaoApp
            }
        } else {
            do {
                token = try await loginWithKakaoWeb()
            } catch {
                throw KakaoLoginError.failedToLoginWithKakaoWeb
            }
        }
        
        return try await getUserInfo(token: token)
    }
```

이렇게 작성하여 앱/웹으로 로그인하여 사용자 정보를 받아올 수 있습니다.
앱 내에서 사용자 정보를 활용하는 데이터가 있는 경우, <font color="#ff0000">getUserInfo를</font> 통해 사용자 정보를 가져올 수 있습니다.

## 마무리
> 카카오 로그인을 통해 사용자 인증을 보다 쉽게 구현할 수 있으며, Swift Concurrency와 함께 사용할 경우 코드의 가독성과 유지보수성도 높일 수 있습니다.