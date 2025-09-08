---
layout: post
title: SwiftUI 커스텀 폰트 적용하기
description: SwiftUI에 커스텀 폰트를 만들고 사용하기
featuredImage:
excerpt: "SwiftUI : CustomFont"
writer: JEONG Eui chan
categories:
  - Development
  - Font
tags:
  - SwiftUI
  - Font
toc: true
toc_sticky: true
data: 2025-07-08
last_modified_at: 2025-07-08
image:
  path: /assets/img/Font/CustomFontCover.png
featured: true
hidden: false
---

# 목차

## 🙂 CustomFont
---
Swift에서 비교적 간단한 설정만으로 자신만의 커스텀 폰트를 프로젝트에 추가합니다. 전역 혹은 개별 컴포넌트에 적용할 수 있다. 그러나 폰트 파일의 추가, Info.plist 설정, 실제 코드 적용까지의 흐름을 제대로 이해하지 않으면 예상치 못한 오류에 직면합니다. <br>
**그럼 커스텀 폰트를 추가하고 쉽게 사용하려면 어떻게 해야할까요??**

## ☺️ 폰트 파일을 프로젝트에 넣기
---
폰트 파일을 Xcode에 추가합니다.

<div align="center">
<img src="/assets/img/Font/step1.png" width="600" alt="폰트1"/>
</div>

## 😁 Info.plist에 폰트 파일 추가하기
---
폰트 파일을 Xcode에 추가했다면, 이제 Info.plist에 <br>
`Fonts provided by application`를 추가해야합니다. <br>
추가 후, 방금 추가한 폰트 파일명을 그대로 복사해서 Item으로 넣어줍니다. 꼭!! 확장자명까지 입력해야합니다!!

<div align="center">
<img src="/assets/img/Font/step2.png" width="600" alt="폰트2"/>
</div>

## ☺️ 폰트 이름 확인하기
---
폰트 파일명을 폰트에 적용하면 안됩니다!! <br>
**왜 그럴까요??**

파일명은 폰트명이 아니에요! 우리는 진짜 사용할 수 있느 폰트명을 알아야합니다!!

```swift
UIFont.familyNames.sorted().forEach { familyName in
	print("*** \(familyName) ***")
	UIFont.fontNames(forFamilyName: familyName).forEach { fontName in
		print("\(fontName)")
	}
	print("---------------------")
}
```

프리뷰 실행 시 폰트 목록을 출력할 수 있도록 하는 코드입니다.
모든 폰트의 FamilyName과 실제 사용할 수 있는 Font Name이 출력됩니다.
아래 사진과 같이 출력되는 Font Name이 실제 코드에서 사용할 수 있는 이름입니다.

<div align="center">
<img src="/assets/img/Font/step3.png" width="600" alt="폰트3"/>
</div>

## 😗 폰트 적용하기
---
Figmad의 디자인 시스템을 참고해 폰트를 미리 설정해두는 것은 UI 일관성과 개발 효율성을 높이는 데 매우 중요합니다! <br>
디자인 시스템에 맞춰 폰트를 사용할 수 있도록 준비하기 위해 아래와 같이 작성을 하면 됩니다.
```swift
extension Font {
    enum Pretend {
        case bold
        case semibold
        
        var value: String {
            switch self {
            case .bold:
                return "PretendardVariable-Bold"
            case .semibold:
                return "PretendardVariable-SemiBold"
            }
        }
    }
    
    static func pretend(type: Pretend, size: CGFloat) -> Font {
        return .custom(type.value, size: size)
    }
    
	/* 예시 */
    static var headLine1: Font {
        return .pretend(type: .bold, size: 12)
    }
    
    static var headLine2: Font {
        return .pretend(type: .semibold, size: 13)
    }
}
```

위에서 확인했던 폰트 네임을 value 값 내부에 작성하고 사용하면 됩니다.

디자인 시스템에 주어진 폰트를 아랫부분의 headLine1 처럼 만들어서 사용하면 됩니다!! <br>
이렇게 함으로, 일관된 스타일과 유지보수에 용이하게 사용할 수 있습니다.

## 마무리
> 피그마의 디자인 시스템을 바탕으로 폰트를 미리 정의하고,
> SwiftUI에서 Font, extension, enum을 활용하여 일관된 스타일을 적용하는 방식은 프로젝트의 완성도, 유지보수성, 가독성을 크게 높여줄 수 있어 아주 효과적입니다!