---
layout: post
title: SwiftUI 텍스트 필드 알아보기
description: SwiftUI 텍스트 필드의 기본적인 사용
featuredImage: 
excerpt: "SwiftUI : KakaoLogin"
writer: JEONG Eui chan
categories:
  - Development
tags:
  - SwiftUI
  - TextField
toc: true
toc_sticky: true
data: 2025-07-07
last_modified_at: 2025-07-07
image:
  path: /assets/img/TextField/TextFieldCover.png
featured: true
hidden: false
---

## 목차
* [🤔 TextField?](#-textfield)
* [😃 TextField 기본 사용](#-textfield-기본-사용)
* [🙂 TextFieldStyle](#-textfieldstyle)
* [🙃 그 외 TextField Modifier](#-그-외-textfield-modifier)
* 

# 🤔 TextField?
---
> https://developer.apple.com/documentation/swiftui/textfiel

Apple 공식 문서에 따르면 TextField의 정의를 아래와 같이 설명되어 있습니다.

> A control that displays an editable text interface
> <font color="#a5a5a5">편집 가능한 텍스트 인터페이스를 표시하는 컨트롤</font>

즉 TextField는 사용자가 텍스트를 입력하거나 수정할 수 있도록 인터페이스를 제공하는 컨트롤입니다. <br>
Text가 단순히 정보를 표시하는 역할이라면, TextField는 정보를 입력받는 역할을 한다는 점에서 차이가 있습니다. <br> 
사용자와의 상호작용이 필요한 폼, 로그인 화면, 검색창 등에서 자주 사용합니다. <br>
## 😃 TextField 기본 사용
---
* TextField를 사용하기 위해서는 TextField 명령어를 사용하면 됩니다.
* TextField는 바인딩된 문자열 변수와 함께 사용되며, 사용자가 입력한 텍스트를 해당 변수에 실시간으로 저장합니다.
```swift
struct ContentView: View {
    @State var text: String = ""
    @State var num: Double = 0.0
    
    var body: some View {
        VStack {
            TextField("필드 기본", text: $text)
            
            TextField("프롬프트", text: $text, prompt: prompt)
            
            TextField(text: $text, label: {
                prompt
            })
            
            TextField("숫자 입력", value: $num, formatter: numberFormatter)
        }
        .padding()
    }
    
    private var prompt: Text {
        Text("placeholder")
            .font(.caption)
            .foregroundStyle(Color.black)
    }
    
    private var numberFormatter: NumberFormatter {
           let formatter = NumberFormatter()
           formatter.numberStyle = .decimal
           formatter.minimumFractionDigits = 0
           formatter.maximumFractionDigits = 2
           return formatter
    }
}
``` 

<div align="center">
<img src="/assets/img/TextField/step1.png" width="600" alt="텍스트 필드1"/>
</div>


SwiftUI에서 TextField는 다양한 초기화 방식을 제공합니다.
#### 기본형
* placeholder와 바인딩된 문자열만 사용합니다.
* 사용자가 입력한 값은 text에 저장합니다.

```swift
TextField("텍스트 필드 기본", text: $text)
```

### prompt
* iOS 15 이상에서 추가된 prompt 파라미터입니다.
* prompte는 ViewBuilder를 통해 동적인 뷰를 넣을 수 있도록 합니다.

```swift
TextField("프롬프트 입력", text: $text, prompt: prompt)
```

### Label
* 레이블을 직접 커스터마이징하는 초기화 방식입니다.
* prompt 뷰를 직접 레이블로 지정할 수 있습니다.

```swift
TextField(text: $text, label: {
    prompt
})
```

### Value와 Formatter
* value와 formatter를 사용하는 초기화 방식입니다.
* 텍스트 입력을 숫자나 날짜 같은 비문자형 값에 바인딩할 때 사용합니다.
* numberFormatter(=문자열) <-> 숫자 변환 처리

```swift
TextField("포매터 숫자 입력", value: $num, formatter: numberFormatter)

private var numberFormatter: NumberFormatter {
           let formatter = NumberFormatter()
           formatter.numberStyle = .decimal
           formatter.minimumFractionDigits = 0
           formatter.maximumFractionDigits = 2
           return formatter
    }
```

<br>

> <font color="#ff0000">prompt와 label의 차이가 무엇일까요?</font>
> 1. prompt는 입력 값이 비어있을 때, 보여주는 시각적 안내 텍스트입니다.
> 2. label은 VoiceOver를 위한 이름이거나, 커스터마이징된 레이블 역할입니다.
> 즉, 시각적 프롬프트와 접근성 라벨을 명확히 구분하는 것이 더 바람직하기 때문에 분리한 것입니다.

`titleKey` 파라미터가 존재합니다. <br>
내부적으로 placeholder로 사용하는 키입니다. <br>
보통 간단한 텍스트 필드에는 이 titleKey 기반 초기화를 사용합니다. <br>
하지만 위와 같이 prompt와 같이 있는 경우에는 접근성 기능에서 titleKey가 읽힙니다.

## 🙂 TextFieldStyle
---
TextField 스타일을 지정하려면 .textFieldStyle를 사용하면 됩니다..!

```swift
TextField("이름을 입력하세요", text: $name)
	.textFieldStyle(RoundedBorderTextFieldStyle())
```

기본적으로 제공하는 textFieldStyle에는 
`.textFieldStyle(.plain)`, `.textFieldStyle(.roundedBorder)` 이 있습니다. <br>
근데 우리는 더 다양한 TextField 스타일을 원하겠죠?!
iOS 15 이후에는 .padding(), .background(), 등을 이용하여 직접 꾸미는 방식도 자주 사용됩니다.

```swift
var body: some View {
        VStack {
            TextField("텍스트 필드 기본", text: $text)
                .textFieldStyle(.plain)
            
            TextField("프롬프트 입력", text: $text, prompt: prompt)
                .textFieldStyle(.roundedBorder)
            
            
            TextField(text: $text, label: {
                prompt
            })
            .padding(5)
            .background(Color.gray)
            .clipShape(RoundedRectangle(cornerRadius: 10))
            
            TextField("포매터 숫자 입력", value: $num, formatter: numberFormatter)
                .border(Color.red)
        }
    }
```

<div align="center">
<img src="/assets/img/TextField/step2.png" width="600" alt="텍스트 필드2"/>
</div>


## 🙃 그 외 TextField Modifier
---
TextField에는 여러가지 수정자들이 있습니다. 입력 동작이나 입력 환경을 제어하는 데 사용합니다.

### 1. KeyboardType
> https://developer.apple.com/documentation/uikit/uikeyboardtype <br>
> 키보드 타입을 설정하는 데 사용합니다.

TextField를 탭하면 키보드는 자동으로 올라오지만,
키보드를 닫는 동작은 키보드 타입에 따라 달라질 수 있습니다.

`.default` 타입에는 return 버튼이 있어 이를 눌러 내릴 수 있지만,
`.numberPad나` `.PhonePad`와 같은 키보드 타입은 Return 키가 없기 때문에
사용자가 키보드를 직접 닫기 어렵습니다.

그럼 이런 상황에서 우리는 어떻게 키보드를 내릴 수 있을까요? <br>
UIApplication을 확장해서 사용자가 화면을 탭했을 때 키보드가 내려가도록
Tap Gesture Recognizer를 윈도우에 추가하면 됩니다..!

```swift
import Foundation
   import SwiftUI
   
   extension UIApplication {
       func hideKeyboard() {
           if let windowScene = UIApplication.shared.connectedScenes.first as? UIWindowScene {
               guard let window = windowScene.windows.first else { return }
               
               let tapGesture = UITapGestureRecognizer(target: window, action: #selector(UIView.endEditing))
               tapGesture.cancelsTouchesInView = false
               tapGesture.delegate = self
               
               window.addGestureRecognizer(tapGesture)
           }
       }
   }
   
   
   extension UIApplication: @retroactive UIGestureRecognizerDelegate {
       public func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldRecognizeSimultaneouslyWith otherGestureRecognizer: UIGestureRecognizer) -> Bool {
           return false
       }
   }
```

<font color="#ff0000">**왜 @restoractive를 사용했을까요?**</font> <br>
@retroactive는 Swift에서 기존 타입을 외부 라이브러리 없이 프로토콜 채택하게 할 때 사용하는 기능입니다. <br>
즉, UIGestureRecognizerDelegate를 채택하게 설정해서 다른 제스처와 동시에 인식되지 않도록 하기 위함입니다!

### 2. submitLabel
> https://developer.apple.com/documentation/swiftui/submitlabel <br>
> 키보드의 return 버튼에 표시될 라벨을 설정합니다.

### 3. onSubmit
> https://developer.apple.com/documentation/assignables/assignabledocumentview/onsubmit(of:_:) <br>
> 사용자가 return 키를 눌렀을 때의 동작을 정의합니다.

```swift
TextField("텍스트 필드 기본", text: $text)
            .onSubmit {
                print("hello")
            }
```

submitLabel과 onSubmit은 단독으로 유용하지만 같이 써야 UX가 좋아집니다..!
submitLabel을 사용해서 사용자가 어떤 동작을 기대해야 하는지 의도를 명확히 전달하고,
onSubmit을 통해 Return 키를 눌렀을 때 실행할 동작을 정의합니다..!

### 4. focused
> https://developer.apple.com/documentation/swiftui/view/focused(_:) <br>
> 텍스트 필드의 포커스 여부를 제어하거나 감지합니다.

기본 사용법은 아래와 같습니다.
```swift
struct LoginView: View {
    @State private var email = ""
    @FocusState private var isEmailFocused: Bool

    var body: some View {
        VStack {
            TextField("이메일", text: $email)
                .focused($isEmailFocused) 

            Button("포커스 주기") {
                isEmailFocused = true 
            }
        }
    }
}
```

포커스를 true/false로 직접 제어하여 focused로 TextField와 연결합니다.

근데 만약 2개 이상의 TextField가 존재하고, 여러 입력 필드 간의 전환을 명확하게 제어하려면 어떻게 할 수 있을까요??

```swift
struct SignUpView: View {
       enum Field {
           case username, password
       }
   
       @State private var username = ""
       @State private var password = ""
       @FocusState private var focusedField: Field?
   
       var body: some View {
           VStack(spacing: 20) {
               TextField("사용자명", text: $username)
                   .focused($focusedField, equals: .username)
                   .submitLabel(.next)
                   .onSubmit {
                       focusedField = .password
                   }
   
               SecureField("비밀번호", text: $password)
                   .focused($focusedField, equals: .password)
                   .submitLabel(.done)
                   .onSubmit {
                       print("회원가입 정보 제출됨")
                   }
           }
           .padding()
       }
   }
```

onSubmitLabel과 onSubmit, focuesd를 잘 활용하면 키보드 흐름과 일치, 사용자 경험의 자연스러움을 가질 수 있습니다!
### 5. textInputAutocapitalization
> https://developer.apple.com/documentation/swiftui/view/textinputautocapitalization(_:) <br>
> 사용자가 TextField에 텍스트를 입력할 때, 어떤 경우에 자동으로 대문자를 적용할지 지정하는 데 사용됩니다..!

<font color="#ff0000">**우리는 언제 사용해야할까요?**</font>
* 아이디, 이메일, 주소, URL 등 대소문자를 구분하거나 대문자가 필요 없는 입력에 사용합니다.

```swift
TextField("사용자명", text: $username)
    .textInputAutocapitalization(.never)
```

사용 가능한 옵션에는 4가지가 있습니다.

* never
	* 자동 대문자 적용 안 함
* words
	* 각 단어의 첫 글자만 대문자로
* sentences
	* 각 문장의 첫 글자만 대문자로
* allCharacters
	* 모든 문자를 대문자로

## 마무리
> SwiftUI의 TextField는 단순한 텍스트 입력 컴포넌트를 넘어 다양한 Modifier를 통해 스타일, 입력 방식, 키보드 제어, 포커스 흐름 등 풍부한 사용자 경험을 제공합니다.
> 세심한 설정 하나하나가 전체 앱의 왼성도를 높이는 핵심 요소가 될 수 있다!!!