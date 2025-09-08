---
layout: post
title: SwiftUI NavigatoinRouter 파헤치기
description: SwiftUI NavigationRouter
featuredImage:
excerpt: "SwiftUI : NavigationRotuer"
writer: JEONG Eui chan
categories:
  - Development
  - Navigation
tags:
  - SwiftLibrary
  - SocialLogin
toc: true
toc_sticky: true
data: 2025-07-04
last_modified_at: 2025-07-04
image:
  path: /assets/img/Navigation/NavigatoinRouter.png
featured: true
hidden: false
---

# 목차
- [목차](#목차)
  - [NavigationRoutable](#navigationroutable)
  - [NavigationDestination](#navigationdestination)
  - [NavigationRoutingView](#navigationroutingview)

<br>
앱을 개발하다 보면 화면 전환은 가장 기본적이다
특히 프로젝트가 커지고, 화면의 종류가 많아질수록 단순한 push와 present 호출만으로는 복잡한 흐름을 관리하기 어려워진다. `로그인 후 홈으로 이동`, `특정 조건에서만 화면 전환` 같은 요구사항이 쌓이다 보면 코드 곳곳에 네비게이션 로직이 흩어지고, 유지 보수성이 떨어지게 된다

<br>
이때 유용한 접근법이 **<font color="#ff0000">네비게이션 라우터</font>** 입니다. 네비게이션 라웉는 화면 이동 로직을 한곳에 모아 관리하면서 화면 전환의 흐름을 더 직관적으로 확장 가능하게 설계할 수 있도록 도와준다. 단순히 `다음 화면으로 가기` 를 넘어서<font color="#ff0000"> 앱의 전체적인 이동 경로를 설계 하는 중심축</font>이 된다.


## NavigationRoutable
> 네비게이션 라우터는 복잡한 화면 로직을 각 뷰에 흩뿌리지 않고, 한 곳에서 일관되게 관리하려는 목적이다. NavigationRouter는 화면 이동의 "규칙"과 "흐름"을 캡슐화해 뷰는 "무엇을 보여줄지"에만 집중하게 만든다

```swift
protocol NavigationRoutable {
    var destination: [NavigationDestination] { get set }
    func push(to view: NavigationDestination)
    func pop()
    func popToRootView()
}

@Observable
class NavigationRouter: NavigationRoutable {
    var destination: [NavigationDestination] = []
    
    func push(to view: NavigationDestination) {
        destination.append(view)
    }
    
    func pop() {
        _ = destination.popLast()
    }
    
    func popToRootView() {
        destination.removeAll()
    }
}

```

* `NavigationDestination`
	* 앱이 이동할 목직지를 열거형으로 모델링한다. 어떤 화면으로 이동할지 타입으로 안전하게 표현할 수 있다

* `destination: [NavigationRouter]`
	* 스택경로, SwiftUI의 NavigationRouter가 바인딩하는 path와 1:1로 매칭되어, 스택 조작으로 화면이 이동한다.
* `push/pop/popToRootView`
	* 스택 조작을 메서드로 감싸 일관된 진입점을 제공한다. 화면에서 직접 배열을 만지지 않고, 항상 라우터를 거치게 하여 규칙을 중앙화한다.

## NavigationDestination
> Navigation Stack에서 특정 데이터 타입에 따라 화면 전환을 정의한다.
> 앱 전체에서 발생할 수 있는 네비게이션 목적지를 한곳에 모아 타입 안전하게 관리한다.

```swift
enum NavigationDestination: Hashable {
    ...
    case auth(Auth)
    case profile(Profile)
    case diagnostic(Diagnostic)
}
```


각 case는 세부 카테고리를 나타내고, 실제 화면 이동은 내부 서브 enum을 통해 세분화한다.

그렇게 하는 이유는 단순하다.

우리가 앱을 만들면 회원가입/프로필/진단/추천 문의 등 서로 다른 기능 도메인으로 나뉘게 된다.
그렇기 때문에 모든 `목적지를 NavigationDestination에 직접 case로 나열하면, 수십 ~ 수백 개의 case가 생겨서 가독성.유지보수성이 떨어진다.`

그래서 기능 단위로 서브 enum을 만들어 그룹화하여 어느 기능 도메인과 관련 있는지 바로 알 수 있도록 한다.

```swift
enum NaivgatoinDestination: Hashable {
	enum Auth: Hashable {
	           /// 회원 가입 이동
	           case signUp(socialType: SocialLoginType, signup: SignUpData)
	           /// 회원 탈퇴 이동
	           case deleteAccount
	       }
	       
	       /// 애완 프로필 생성
	       enum Profile: Hashable {
	           /// 홈 화면 펫 프로필 수정 이동
	           case editPetProfile(image: String, petInfo: PetInfo)
	       }
	       
	       /// 진단 탭
	       enum Diagnostic: Hashable {
	           /// 일지 생성 이동
	           case makeJournalist
	 }
}
```

이 NavigationDestination라는 열거형 값을 받아서 그 목적지에 맞는 실제 화면을 연결해주는 "라우팅 전담 뷰"를 만들어서 사용해야 한다.

## NavigationRoutingView
> NavigationDestination은 목적지 정의만 담당한다. NavigationRoutingView는 목적지를 화면으로 변환만 담당한다.

앱 전체 화면 이동은 NavigationDestination이라는 열거형으로 정의되며, 이 값은 어떤 화면으로 이동해야 하는가? 라는 추상적인 목적지를 표현한다
NavigationDestination은 데이터 기반의 경로 정의자, NavigationRoutingView는 UI 렌더링 담당자라고 할 수 있다.

```swift
struct NavigationRoutingView: View {
    @State var destination: NavigationDestination
    
    var body: some View {
        switch destination {
        case .auth(let auth):        authView(auth)
        case .profile(let profile):  profileView(profile)
        case .diagnostic(let d):     diagnosticView(d)
        case .recommend(let r):      recommendView(r)
        case .tips(let tips):        tipsView(tips)
        case .myPage(let myPage):    myPageView(myPage)
        case .appInfo(let appInfo):  appInfoView(appInfo)
        case .inquire(let inquire):  inquireView(inquire)
        }
    }
}

extension NavigationRoutingView: View {
	@ViewBuilder
	 func authView(_ route: NavigationDestination.Auth) -> some View {
	     switch route {
	     case .signUp(let socialType, let signUpRequest):
	         SignUpView(socialType: socialType, signup: signUpRequest, container: container, appFlowViewModel: appFlowViewModel)
	     case .deleteAccount:
	         DeleteAccountView(container: container, appFlowViewModel: appFlowViewModel)
	     }
	 }
 }
```