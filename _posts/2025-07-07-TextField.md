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

<br>
<div align="center">
<img src="/assets/img/TextField/step1.png" width="600" alt="텍스트 필드1"/>
</div>

<br>

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
> var body: some View {
>            VStack {
>                TextField("텍스트 필드 기본", text: $text)
>                    .textFieldStyle(.plain)
>                
>                TextField("프롬프트 입력", text: $text, prompt: prompt)
>                    .textFieldStyle(.roundedBorder)
>                
>                
>                TextField(text: $text, label: {
>                    prompt
>                })
>                .padding(5)
>                .background(Color.gray)
>                .clipShape(RoundedRectangle(cornerRadius: 10))
>                
>                TextField("포매터 숫자 입력", value: $num, formatter: numberFormatter)
>                    .border(Color.red)
>            }
>            .padding()
>        }
```

<br>
<div align="center">
<img src="/assets/img/TextField/step2.png" width="600" alt="텍스트 필드2"/>
</div>

<br>
