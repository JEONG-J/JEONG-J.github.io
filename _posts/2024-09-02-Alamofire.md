---
layout: post
title: Alamofire 익히기
description: Swift 라이브러리 Alamofire
featuredImage: 
excerpt: "SwiftLibrary : Alamofire"
writer: JEONG Eui chan
categories:
  - Development
tags:
  - SwiftLibrary
  - Alamofire
toc: true
toc_sticky: true
data: 2024-09-02
last_modified_at: 2024-09-02
image:
  path: /assets/img/Alamofire/AlamofireLogo.png
featured: true
hidden: false
---
## 목차

- [🧐 Alamofire란 무엇인가?](#alamofire란-무엇인가)

- [😃 Alamofire의 특징](#alamofire의-특징)

- [📋 Alamofire 네트워킹](#-alamofire-네트워킹)

- [❗️ 추가적인 HTTP 메소드](#️-추가적인-http-메소드)

- [✍️ Request/Response](#️requestresponse)

- [👊 마무리](#마무리) 

## 🧐 Alamofire란 무엇인가?
---
> 깃허브 주소 <br>
> [https://github.com/Alamofire/Alamofire](https://github.com/Alamofire/Alamofire)

> Swift에 존재하는 HTTP 라이브러리로, 이 라이브러리는 다양한 특징의 HTTP method를 지원하고 있다. JSON, XML 그리고 네트워크 통신 상태를 모니터링 한다. 하지만 때때로 OSI 7계층에서의 높은 레벨의 추상화가 필요로 한다.(Moya) 

## 😃 Alamofire의 특징
---

### 간편한 HTTP 요청 생성

Alamofire는 간단한 직관적인 API를 제공하여 Get, Post, Put, Delete 등 HTTP 1.0 이상의 요청을 생성하고 실행할 수 있다.

### 비동기 네트워킹

Alamofire는 비동기 방식으로 네트워크 요청을 처리하며 클로저를 통해 응답과 오류를 처리할 수 있다. 이로 인해 메인 스레드의 블로킹을 피하고 사용자 인터페이스의 응답성을 유지할 수 있다.

### 비동기 네트워킹

URL 인코딩, JSON 인코딩 등 요청 데이터를 쉽게 인코딩할 수 있는 기능을 제공하며, 응답 데이터를 디코딩하는 것도 간편하게 처리할 수 있다.

### 파일 업로드 및 다운로드

대용량 파일 업로드 및 다운로드를 쉽게 구현할 수 있도록 지원한다. 특히 백그라운드 다운로드와 업로드 기능을 제공하여 앱이 백그라운드로 전환되어도 작업을 지속할 수 있다.

### 보안 기능

Alamofire는 SSL 인증서 검증, HTTP 인증 등을 다양한 보안 기능을 지원하여 안전한 네트워크 통신을 구현할 수 있다.

## 📋 Alamofire 네트워킹

### GET

> 서버로부터 데이터를 가져오기 위해 사용한다.

> 서버의 데이터를 조회하는데 사용되며, 서버의 상태나 데이터를 변경하지 않는다. <br>
요청할 때 쿼리 파라미터를 URL에 포함하여 전송할 수 있다. <br>
캐시될 수 있으며, 일반적으로 안전하고 반복적으로 요청해도 서버 상태에 영향을 주지 않는다.

- Get 코드 작성 캡쳐화면

<img width="1822" alt="XcodeGet" src="https://github.com/user-attachments/assets/fc1ea9a3-e70a-436d-a860-7d908fe9970b">

- 실행 화면

<div align="center">
<img width="350" alt="ResultGet" src="https://github.com/user-attachments/assets/cb4e0b4f-57f7-431f-8600-f30cf639ec27" align="center">
</div>

```swift
private func apiGet() {
        AF.request("https://jsonplaceholder.typicode.com/todos/1").responseString { response in
            switch response.result {
            case .success(let response):
                self.apTextLabel.text = response
                print(response)
            case .failure(let error):
                self.apTextLabel.text = "Error: \(error)"
            }
        }
    }
```

### POST

> 서버에 데이터를 전송하여 새로운 리소스를 생성하거나 서버의 상태를 변경하기 위해 사용한다.

> 요청 본문에 데이터를 포함하여 서버로 전송한다. <br>
새로운 리소스를 생성하거나 서버의 상태에 변화를 일으킨다. <br>
일반적으로 캐시되지 않는다.

- POST 코드 작성 캡쳐화면

<img width="1822" alt="XcodePost" src="https://github.com/user-attachments/assets/440af921-bd4b-4bf8-a93c-6c9b9f9da1bb">

- 실행 화면
    
<div align="center">
<img width="350" alt="ResultPost" src="https://github.com/user-attachments/assets/762a5064-a306-45d4-a9e4-82723a28f214">
</div>
    

```swift
 let param: [String: Any] = [
        "title": "fooText",
        "body": "barText",
        "userId": 122
    ]

 private func apiPost() {
        AF.request("https://jsonplaceholder.typicode.com/posts", method: .post, parameters: param, encoding: JSONEncoding.default).responseString { response in
            switch response.result {
            case .success(let response):
                self.apTextLabel.text = response
                print(response)
            case .failure(let error):
                self.apTextLabel.text = "Error: \(error)"
            }
        }
    }
```

### PATCH

> 서버의 기존 리소스의 일부를 수정하기 위해 사용한다.

> 요청 본문에 수정할 데이터만 포함하여 서버로 전송한다.<br>
**리소스 전체가 아닌, 일부 속성만 변경하고 싶을 때 사용한다.(리소스 일부 변경)**<br>
PATCH는 부분 업데이트에 사용, PUT은 전체 리소스를 대체

- PATCH 코드 작성 캡쳐화면

<img width="1822" alt="Xcodepatch" src="https://github.com/user-attachments/assets/9f0e54a0-868c-49ce-bdf6-73be1982ec7e">

- 실행 화면

<div align="center">
<img width="350" alt="ResultPatch" src="https://github.com/user-attachments/assets/055ba403-63f5-47d7-84e6-55ce8cbbc045">
</div>

```swift
let param: [String: Any] = [
        "email": "newemail@example.com"
    ]
    
  private func apiPatch() {
        AF.request("https://jsonplaceholder.typicode.com/users/1", method: .patch, parameters: param, encoding: JSONEncoding.default).responseString { response in
            switch response.result {
            case .success(let response):
                self.apTextLabel.text = response
                print(response)
            case .failure(let error):
                self.apTextLabel.text = "Error: \(error)"
            }
        }
    }
```

### PUT

> 서버에 새로운 리소스를 생성하거나 기존 리소스를 대체하기 위해 사용한다.

> 요청 본문에 리소스의 전체 표현을 포함하여 서버에 전송한다 <br>
요청한 리소스가 존재하지 않으면 새로운 리소스를 생성한다. <br>
**요청한 리소스가 존재하면 해당 리소스를 요청 본문으로 완전히 대체한다**.**(리소스 전체 변경)** <br>
멱등성(idempotent) 특징을 가지고 있다. 그래서 요청을 여러 번 수행해도 결과는 동일하다.

- PUT 코드 작성 캡쳐화면

<img width="1822" alt="XcodePut" src="https://github.com/user-attachments/assets/48d324e3-bbfd-45c2-98c5-9a50671201e8">

- 실행 화면

<div align="center">
<img width="350" alt="ResultPut" src="https://github.com/user-attachments/assets/17586330-9a51-4d9d-b999-2a94991a8992">
</div>

```swift

  let param: [String: Any] = [
        "id": 1,
        "name": "John Doe",
        "username": "johndoe",
        "email": "johndoe@example.com"
    ]

private func apiPut() {
        AF.request("https://jsonplaceholder.typicode.com/users/1", method: .put, parameters: param, encoding: JSONEncoding.default).responseString { response in
            switch response.result {
            case .success(let response):
                self.apTextLabel.text = response
                print(response)
            case .failure(let error):
                self.apTextLabel.text = "Error: \(error)"
            }
        }
    }
```

Alamofire의 기본적인 사용법을 이해하는 것은 네트워킹을 효율적으로 처리하는 데 중요한 첫걸음이다. 이 글에서 우리는 GET, POST, PUT, PATCH와 같은 주요  HTTP 메서드를 사용하여 서버와 데이터를 주고받는 방법을 다루었다. 하지만 이 외에도 다양한 HTTP 메서드가 존재하며, 상황에 맞게 활용할 수 있다. 간략하게 추가적인 HTTP 메서드를 간략히 소개하도록 하겠다.

## ❗️ 추가적인 HTTP 메소드
---

### DELETE

> 서버에 특정 리소스를 삭제할 때 사용한다.

```swift
AF.request("https://jsonplaceholder.typicode.com/posts/1", method: .delete)
    .response { response in
        if response.error == nil {
            print("리소스 삭제 성공")
        } else {
            print("리소스 삭제 실패: \(response.error?.localizedDescription ?? "Unknown error")")
        }
    }

```

### OPTIONS

> 서버에 지원하는 HTTP 메서드의 목록을 확인할 때 사용한다. 주로 서버의 기능을 탐색하는 데 유용하다

```swift
AF.request("https://jsonplaceholder.typicode.com/posts/1", method: .options)
    .response { response in
        if let headers = response.response?.allHeaderFields {
            print("지원되는 메서드: \(headers)")
        }
    }

```

### HEAD

> GET 요청과 유사하지만 응답 본문을 포함하지 않는다. 주로 리소의 메타 데이터를 가져올 때 사용된다.

```swift
AF.request("https://jsonplaceholder.typicode.com/posts/1", method: .head)
    .response { response in
        if let headers = response.response?.allHeaderFields {
            print("헤더 정보: \(headers)")
        }
    }

```

## ✍️ Request/Response
---

위 기초적인 예제를 통해 어떻게 사용되는지 살펴 볼 수 있었다.

그럼 이제 request 내부 파라미터에 대해 설명하고 마무리를 지어보고자 한다.

기본적인 틀 사용방법으로 아래 코드와 같다.

```swift
let url = "https://example.com"

AF.request(url,
		   method: .get,
    	   parameters: nil,
    	   encoding: URLEncoding.default,
    	   headers: ["Content-Type":"application/json", "Accept":"application/json"])
	.validate(statusCode: 200..<300)
    .response { response in
    	
	/** 서버로부터 받은 데이터 활용 */
    switch response.result {
    case .success(let data):
		/** 정상적으로 reponse를 받은 경우 */
	case .failure(let error):
    	/** 그렇지 않은 경우 */
	}
}
```

`url` : 첫 번째 파라미터로 요청을 보낼 URL을 담는다.

`method` : 어떤 request mthod를 사용할지 선택한다. 기본값으로 .get으로 되어있어서 딱히 지정하지 않으면 get으로 요청을 보내게 된다.

`parameters` : POST 메소드와 같이 Request Body를 사용할 때 전달할 값을 담는다. 위 예제 코드에서 봤듯이 파라미터 변수를 만들고 값을 넣어서 Request를 보낸것을 알 수 있다.

`Encoding` : 인코딩 방식을 정한다.

**URLEncoding** 

> 요청 파라미터를 URL의 쿼리 문자열로 인코딩하는 방식이다. 일반적으로 Get 요청에서 사용되며,
파라미터가 URL의 일부로 추가된다.<br>
GET, DELETE와 같은 요청 메소드에서는 URL의 끝에 쿼리 파라미터로 추가된다.
POST, PUT과 같은 요청 메소드에서는 파라미터가  URL의 본문이 아닌 쿼리 파라미터로 추가될 수 있다.

```swift
AF.request("https://example.com",
           method: .get,
           parameters: ["key1": "value1", "key2": "value2"],
           encoding: URLEncoding.default)

```

**JSONEncoding**

> 요청 파라미터를 JSON 형식으로 인코딩하여 HTTP 요청의 본문에 포함시키는 방식이다.<br>
POST, PUT, PATCH 요청에서 주로 사용된다.

```swift
AF.request("https://example.com",
           method: .post,
           parameters: ["key1": "value1", "key2": "value2"],
           encoding: JSONEncoding.default)

```

`headers` : 부가적인 정보를 나타낸다. 송/수신 데이터 타입을 나타낸다. Contents-Type과 Accept를 헤더에 지정할 수 있다.

`validate`: 유효성 검사를 진행한다. 요청 결과에 따라 처리되도록 할 수 있다.

**상태 코드 검증**

기본적으로 200번대 경우를 성공으로 간주한다. 이 상태 코드는 서버가 클라이언트의 요청을 성공적으로 처리했음을 의미한다. 직접 지정하여 범위로 유효하도록 지정할 수 있다.

```swift
AF.request("https://example.com/resource")
  .validate(statusCode: 200..<400)  // 200~399까지의 상태 코드를 성공으로 간주
  .response { response in
      // 처리 로직
  }

```

**콘텐츠 타입 검증**

서버에서 반환된 응답의 콘텐츠 타입이 요청 시 예상한 타입과 일치하는지 검증할 수 있다. 클라이언트가 JSON 데이터를 기대한다면 서버의 응답이 application/json 타입인지  확인한다.

```swift
AF.request("https://example.com/resource")
  .validate(contentType: ["application/json"])  // 응답의 콘텐츠 타입이 JSON인지 검증
  .response { response in
      // 처리 로직
  }

```

`response` : 응답의 성공, 실패에 따라 처리되도록 로직을 지정한다.

## 👊 마무리
---

Alamofire의 주요 기능과 사용법에 대해 살펴 보았다.

네트워킹은 모든 애플리케이션에서 중요한 역할을 한다. 안정적이고 효율적인 네트워킹 코드를 작성하는 것은 사용자의 경험을 향상 시키고, 애플리케이션의 품질을 높이는 데 필수적이다. Alamofire는 이른 실현하는 데 큰 도움이 되는 도구이다.

디자인 패턴을 적용하고, 클린 코드로 작성하여 사용하면 네트워킹 코드의 관리가 더 쉬워지고, 코드의 가독성과 유지 보수성도 향상될 수 있다.
