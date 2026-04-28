# Week 3 - 은지

> 주제: 네트워크 / HTTP / 통신

# 1️⃣ IP / DNS

## IP (Internet Protocol Address)

> 인터넷에 연결된 각 장치를 식별하기 위한 고유한 주소
> 

모든 컴퓨터, 스마트폰, 서버는 인터넷 통신을 위해 반드시 IP 주소를 가지고 있습니다.

데이터를 주고받을 때 목적지를 정확하게 지정하기 위해 사용됩니다.

- IPv4: 32비트 주소 체계 (ex - 192.168.0.1, 142.250.190.78)
- IPv6: 128비트 주소 체계, 더 많은 주소를 제공하기 위해 등장함

IPv4는 주소 수의 한계로 인해 점차 IPv6로 전환되는 추세입니다.

## DNS (Domain Name System)

> 도메인 이름을 IP 주소로 변환해주는 시스템
> 

사람이 기억하기 쉬운 도메인 이름을 사용하여 인터넷에 접근할 수 있도록 도와줍니다.

일반적으로 DNS는 “인터넷의 전화번호부”라고 표현됩니다.

예를 들어, 사용자가 google.com을 입력하면 실제로는 해당 도메인과 대응되는 142.250.190.78과 같은 IP 주소로 변환되어 접속이 이루어집니다.

## 인터넷에서 웹사이트에 접속하는 과정

<aside>

1. 사용자가 브라우저에 도메인 이름을 입력합니다.
2. 컴퓨터는 DNS 서버에 해당 도메인의 IP 주소를 요청합니다.
3. DNS 서버는 도메인에 대응되는 IP 주소를 반환합니다.
4. 브라우저는 해당 IP 주소를 이용하여 서버에 요청을 보냅니다.
5. 서버는 요청에 대한 응답으로 웹페이지 데이터를 전달합니다.
</aside>

## iOS에서 IP / DNS

우리가 API를 요청할 때에도 IP를 사용합니다.

앱에서 서버에 요청을 보내면, 실제로는 IP 주소를 기반으로 데이터가 전달됩니다.

<aside>

1. 앱에서 URL 요청을 생성합니다.
(예: `https://api.koin.com`)
2. iOS 내부 네트워크 스택(예: URLSession)이 DNS 조회를 수행합니다.
3. DNS 서버로부터 도메인에 대응되는 IP 주소를 전달받습니다.
4. 해당 IP 주소로 서버에 연결(TCP/HTTPS)을 수행합니다.
5. 서버로부터 데이터를 받아 앱에 전달합니다.
</aside>

```swift
let url = URL(string: "https://api.koin.com/dinings")!
let task = URLSession.shared.dataTask(with: url) { data, response, error in
    // 데이터 처리
}
task.resume()
```

이 코드에서 개발자는 도메인(api.koin.com)만 사용합니다.

실제 IP 변환(DNS)는 URLSession과 같은 로직이 자동으로 처리합니다.

---

# 2️⃣ TCP / UDP

## TCP(Transmission Control Protocol)

> 인터넷에서 데이터를 **신뢰성 있게 전달하기 위한 연결 지향 프로토콜**
> 

IP와 함께 사용되며, 데이터의 순서와 정확성을 보장합니다.

### TCP 특징

<aside>

- 연결 지향 방식입니다. (통신 전 연결을 먼저 설정합니다)
- 3-way handshaking으로 연결을 설정하고, 4-way handshaking으로 해제합니다.
- 흐름 제어와 혼잡 제어를 지원합니다.
- 데이터의 순서 보장 및 재전송을 통해 높은 신뢰성을 제공합니다.
- UDP보다 속도가 느립니다.
- 전이중(Full-Duplex), 점대점(Point to Point) 방식입니다.
</aside>

### 동작 과정 (3-way handshake)

![image.png](attachment:661f271b-53dc-4cf3-9b0b-6af99a05285e:image.png)

1. 클라이언트가 SYN 요청을 보냅니다.
2. 서버가 SYN + ACK 응답을 보냅니다.
3. 클라이언트가 ACK를 보내며 연결이 성립됩니다.

### TCP 사용 사례

- 파일 전송
- 웹 통신 (HTTP/HTTPS)
- 로그인, 결제 등 정확성이 중요한 기능

## UDP(User Datagram Protocol)

> 데이터를 **빠르게 전달하기 위한 비연결형 프로토콜**
> 

데이터를 독립적인 단위(데이터그램)로 전송합니다.

### UDP 특징

<aside>

- 비연결형 서비스입니다.
- 별도의 연결 설정 과정이 없습니다.
- 데이터의 순서 및 도착 여부를 보장하지 않습니다.
- 흐름 제어 및 혼잡 제어를 하지 않습니다.
- TCP보다 속도가 빠르고 지연이 적습니다.
</aside>

### 동작 과정

![image.png](attachment:1fecc409-2581-48da-821f-15b5cb94c169:image.png)

- 각 패킷이 독립적으로 전송됩니다.
- 서로 다른 경로로 전달될 수 있습니다.
- 일부 데이터가 손실될 수 있습니다.

### UDP 사용 사례

- 실시간 스트리밍 (영상, 음악)
- 온라인 게임
- 화상 통화

## iOS에서의 TCP / UDP

iOS에서는 개발자가 직접 TCP/UDP를 선택하기도 하지만, 대부분은 고수준 API가 내부적으로 처리합니다.

### TCP 사용

```swift
let url = URL(string: "https://api.example.com")!
URLSession.shared.dataTask(with: url) { data, response, error in
    // TCP 기반 통신
}.resume()
```

- URLSession 기반 네트워크 요청은 TCP를 사용합니다.
- HTTP / HTTPS는 모두 TCP 위에서 동작합니다.

즉, 일반적인 API 통신은 모두 TCP입니다.

### UDP 사용 → 특수 케이스

- 실시간 통신 (영상, 음성)
- 게임 네트워크

iOS에서는 Network framework 또는 BSD Socket을 통해 UDP를 직접 사용할 수 있습니다.

---

# 3️⃣ HTTP / HTTPS

## HTTP (Hyper Text Transfer Protocol)

HTTP는 클라이언트와 서버 간에 데이터를 주고받기 위한 **애플리케이션 계층 프로토콜**입니다.

TCP/IP 위에서 동작하며, 웹에서 가장 기본이 되는 통신 방식입니다.

### HTTP 특징

- 기본적으로 80번 포트를 사용합니다.
- 클라이언트-서버 구조로 동작합니다.
- 요청(Request)과 응답(Response) 구조를 가집니다.
- Stateless(무상태) 프로토콜입니다.

즉, 각 요청은 서로 독립적이며 이전 상태를 기억하지 않습니다.

### HTTP 메시지 구성 요소

![image.png](attachment:6004c230-7848-4b06-ab2e-11b1bd5fa574:image.png)

- Method (GET, POST 등)
- Path (요청 경로)
- Version (HTTP 버전)
- Headers (메타데이터)
- Body (실제 데이터)

HTTP는 데이터를 **암호화하지 않은 평문(Plain Text)**으로 전송합니다.

따라서 중간에서 데이터를 가로채면 내용이 그대로 노출될 수 있습니다.

## HTTPS (Hyper Text Transfer Protocol Secure)

HTTPS는 HTTP에 **보안(암호화)**이 추가된 프로토콜입니다.

데이터를 암호화하여 안전하게 통신할 수 있도록 합니다.

### HTTPS 특징

- 기본적으로 443번 포트를 사용합니다.
- HTTP + 암호화(SSL/TLS) 구조입니다.
- 데이터 도청 및 변조를 방지합니다.

### HTTPS의 연결 과정

<aside>

1. 클라이언트가 서버에 접속을 시도합니다.
2. 서버는 인증서(공개키 포함)를 클라이언트에게 전달합니다.
3. 클라이언트는 인증서의 유효성을 검증합니다.
4. 클라이언트는 세션 키를 생성합니다.
5. 해당 세션 키를 서버의 공개키로 암호화하여 전달합니다.
6. 서버는 개인키로 이를 복호화하여 세션 키를 얻습니다.
7. 이후부터는 동일한 세션 키로 데이터를 암호화하여 통신합니다.
</aside>

공개키: 누구나 접근 가능하며 암호화에 사용됩니다.

개인키: 서버만 가지고 있으며 복호화에 사용됩니다.

세션 키: 실제 데이터 암호화에 사용하는 대칭키입니다.

## iOS에서의 HTTP / HTTPS

iOS에서는 네트워크 통신 시 보안이 매우 중요하게 다뤄집니다.

iOS는 기본적으로 **HTTP를 차단**합니다. → App Transport Security (ATS)

- HTTP만 허용하는 것이 기본 설정입니다.
- HTTP를 사용하려면 별도의 설정이 필요합니다
    - Info.plist

---

# 4️⃣ 요청과 응답 구조

HTTP 통신은 항상 **요청(Request) → 응답(Response)**의 구조로 이루어집니다.

## 요청(Request) 구조

클라이언트(앱, 브라우저)가 서버에 보내는 메시지입니다.

### Start Line (요청 줄)

어떤 요청인지 정의합니다.

```xml
GET /users HTTP/1.1
```

- Method: GET
- Path: /users
- Version: HTTP/1.1

### Headers

요청에 대한 추가 정보입니다.

```xml
Host: api.example.com
Authorization: Bearer token
Content-Type: application/json
```

### Body

서버에 보낼 실제 데이터입니다.

```xml
{
  "name": "jin",
  "age": 25
}
```

주로 POST, PUT, PATCH에서 사용됩니다.

## 응답(Response) 구조

서버가 클라이언트에게 보내는 결과 메시지입니다.

### Status Line

요청 결과를 나타냅니다.

```xml
HTTP/1.1 200 OK
```

- 200: 성공
- 404: 없음
- 500: 서버 오류

### Headers

응답에 대한 메타 정보입니다.

```xml
Content-Type: application/json
Content-Length: 123
```

### Body

실제 응답 데이터입니다.

```json
{
  "id": 1,
  "name": "jin"
}
```

---

# 5️⃣ HTTP Method

HTTP Method는 클라이언트가 서버에 **어떤 동작을 요청하는지**를 나타내는 방식입니다.

주로 REST API 설계에서 중요한 역할을 합니다.

### GET

서버로부터 **데이터를 조회**할 때 사용하는 메서드입니다.

- 데이터를 가져오기만 하며, 서버 상태를 변경하지 않습니다.
- 캐싱이 가능합니다.

### POST

서버에 **데이터를 생성**할 때 사용하는 메서드입니다.

- 요청 데이터를 Body에 담아 전송합니다.
- 서버 상태를 변경합니다.
- 캐싱이 불가능합니다.

## PUT

서버의 데이터를 **전체 수정(덮어쓰기)**할 때 사용하는 메서드입니다.

- 리소스를 완전히 교체합니다.
- 동일 요청을 여러 번 보내도 결과가 같습니다.

## PATCH

서버의 데이터를 **부분 수정**할 때 사용하는 메서드입니다.

- 변경이 필요한 일부 데이터만 수정합니다.

## DELETE

서버의 데이터를 **삭제**할 때 사용하는 메서드입니다.

- 리소스를 제거합니다.
- 여러 번 요청해도 결과는 동일합니다.

---

# 6️⃣ 상태 코드

HTTP 상태 코드는 서버가 클라이언트의 요청을 처리한 **결과를 숫자로 표현한 값**입니다.

응답의 첫 줄(Status Line)에 포함되어 전달됩니다.

| 1xx | 요청 처리 중 |
| --- | --- |
| 2xx | 성공 |
| 3xx | 리다이렉션 |
| 4xx | 클라이언트 오류 |
| 5xx | 서버 오류 |

## 주요 상태 코드

### 200 OK

- 요청이 정상적으로 처리된 경우입니다.
- 가장 일반적인 성공 응답입니다.

### 201 Created

- 요청이 성공적으로 처리되어 **새 리소스가 생성된 경우**입니다.

### 204 No Content

- 요청은 성공했지만 **응답 Body가 없는 경우**입니다.

### 301 Moved Permanently

- 리소스가 영구적으로 이동된 경우입니다.

### 302 Found

- 리소스가 일시적으로 이동된 경우입니다.
- 브라우저는 자동으로 새로운 주소로 이동합니다.

### 400 Bad Request

- 요청 형식이 잘못된 경우입니다.

### 401 Unauthorized

- 인증이 필요하거나 인증이 실패한 경우입니다.

### 403 Forbidden

- 접근 권한이 없는 경우입니다.

### 404 Not Found

- 요청한 리소스를 찾을 수 없는 경우입니다.

### 409 Conflict

- 요청이 서버의 현재 상태와 충돌하는 경우입니다.

### 500 Internal Server Error

- 서버 내부 오류입니다.

### 502 Bad Gateway

- 서버 간 통신 중 잘못된 응답을 받은 경우입니다.

### 503 Service Unavailable

- 서버가 일시적으로 요청을 처리할 수 없는 상태입니다.

---

# 7️⃣ REST API

## REST

REST는 **Representational State Transfer의 약자**로, 자원(Resource)을 기반으로 데이터를 주고받는 아키텍처 스타일입니다.

REST는 다음 3가지 요소로 구성됩니다.

1. URI로 자원을 표현합니다.
2. HTTP Method로 행위를 표현합니다.
3. 자원의 상태를 주고받습니다.

## REST API란

REST API는 REST 원칙을 따르는 API를 의미합니다.

HTTP 프로토콜을 기반으로 자원을 효율적으로 다루도록 설계된 API입니다.

## **RESTful 설계 원칙**

### 1. URI는 명사를 사용합니다

동사가 아닌 **자원 자체를 표현**해야 합니다.

❌ Bad

```
/getUser
/deletePost
```

✅ Good

```
/users
/posts
```

### 2. 행위는 HTTP Method로 표현합니다

| 동작 | Method | 예시 |
| --- | --- | --- |
| 조회 | GET | GET /users |
| 생성 | POST | POST /users |
| 수정 | PUT/PATCH | PATCH /users/1 |
| 삭제 | DELETE | DELETE /users/1 |

### 3. URI는 일관성 있게 작성합니다

- 소문자 사용
- 하이픈(-) 사용
- 마지막 슬래시 사용하지 않음

### 4. 계층 구조를 표현합니다

```
/users/1/posts
```

사용자 1의 게시글을 표현하는 것입니다.

### 5. 파일 확장자를 포함하지 않습니다

```
/photos   (O)
/photos.jpg (X)
```

데이터 형식은 Header로 처리합니다.

---

# 8️⃣ 쿠키 / 세션 / 토큰

서버는 클라이언트를 식별하고 인증하기 위해 쿠키, 세션, 토큰 방식을 사용합니다.

## 쿠키 (Cookie)

![image.png](attachment:729ac237-2e8f-4f32-8a54-5cb17c2fd082:image.png)

쿠키는 **클라이언트(브라우저)에 저장되는 Key-Value 형태의 데이터**입니다.

### 쿠키 동작 방식

<aside>

1. 클라이언트가 서버에 요청을 보냅니다.
2. 서버는 응답 시 `Set-Cookie` 헤더로 데이터를 전달합니다.
3. 브라우저는 쿠키를 저장합니다.
4. 이후 요청마다 `Cookie` 헤더에 자동으로 포함하여 전송합니다.
</aside>

### 쿠키 특징

- 클라이언트에 저장됩니다.
- 자동으로 요청에 포함됩니다.
- 상태 유지(stateless 보완)에 사용됩니다.

### 쿠키 단점

- 보안에 취약합니다. (탈취, 변조 가능)
- 용량 제한이 존재합니다.
- 클라이언트에 저장되므로 신뢰할 수 없습니다.

## 세션 (Session)

세션은 **서버 측에서 사용자 상태를 관리하는 방식**입니다.

### 세션 동작 방식

![image.png](attachment:6b188134-b2fe-4e33-ba97-66fd8e76a199:image.png)

<aside>

1. 사용자가 로그인합니다.
2. 서버는 세션을 생성하고 Session ID를 발급합니다.
3. 해당 ID를 쿠키에 담아 클라이언트에 전달합니다.
4. 클라이언트는 요청마다 Session ID를 전송합니다.
5. 서버는 Session ID로 사용자 정보를 조회하여 인증합니다.
</aside>

### 세션 특징

- 실제 인증 정보는 서버에 저장됩니다.
- 클라이언트는 Session ID만 보유합니다.
- 쿠키보다 보안이 강화된 방식입니다.

### 세션 단점

- 서버 메모리 사용량 증가
- 서버 확장 시 세션 공유 필요 (분산 환경 문제)
- Session ID 탈취 위험

## 토큰 (Token)

토큰은 서버가 클라이언트에게 발급하는 **인증용 문자열**입니다.

대표적으로 JSON Web Token(JWT)이 사용됩니다.

### Token 동작 방식

![image.png](attachment:877990eb-eab4-4d10-be30-a898e33fc4e7:image.png)

<aside>

1. 사용자가 로그인합니다.
2. 서버는 토큰을 발급합니다.
3. 클라이언트는 토큰을 저장합니다.
4. 이후 요청 시 `Authorization` 헤더에 토큰을 포함합니다.
5. 서버는 토큰을 검증하여 인증을 처리합니다.
</aside>

### 토큰 특징

- 서버에 상태를 저장하지 않는 Stateless 방식입니다.
- 확장성이 뛰어납니다.
- 모바일 앱에서 많이 사용됩니다.

### 토큰 단점

- 토큰 탈취 시 위험합니다.
- 토큰 길이가 길어 네트워크 부담이 있습니다.
- 만료/갱신 전략이 필요합니다.

---

# 9️⃣ 소켓 통신 / WebSocket / iOS 연결

## 소켓(Socket) 통신

소켓 통신은 네트워크 상에서 **두 장치 간 데이터를 주고받기 위한 연결 통로**입니다.

IP + Port를 기반으로 프로세스 간 통신을 수행합니다.

TCP 또는 UDP 위에서 동작합니다.

### 소켓 통신 특징

- 클라이언트와 서버가 직접 연결됩니다.
- 양방향 통신이 가능합니다.
- 지속적인 연결 상태를 유지할 수 있습니다.

## HTTP vs 소켓 통신

![image.png](attachment:bd137950-7580-4f39-9aff-ce6a07468b1a:image.png)

| HTTP | 소켓 통신 |
| --- | --- |
|   • 요청 → 응답 후 연결 종료
  • 단방향 구조 (요청 기반)
  • Stateless |   • 연결을 계속 유지
  • 양방향 실시간 통신
  • 서버도 클라이언트에게 직접 데이터 전송 가능 |

## WebSocket

WebSocket은 **HTTP의 단점을 보완하기 위해 만들어진 프로토콜**입니다.

처음에는 HTTP로 연결한 뒤, 이후에는 지속적인 양방향 통신을 수행합니다.

### WebSocket 특징

- 하나의 연결로 지속적인 통신 가능
- Full-Duplex (양방향)
- 실시간 데이터 전송에 적합

### WebSocket 동작 과정

![image.png](attachment:ff5e3a70-91d0-4b1c-bda3-eab570860ffc:image.png)

<aside>

1. 클라이언트가 HTTP로 연결 요청을 보냅니다.
2. `Upgrade: websocket` 헤더를 포함합니다.
3. 서버가 이를 승인하면 WebSocket으로 전환됩니다.
4. 이후 지속적인 양방향 통신이 가능합니다.
</aside>

채팅 앱, 실시간 게임, 주식/코인 시세, 실시간 알림과 같은 경우는 WebSocket / 소켓을 사용합니다.

하지만 일반 API 요청, 로그인 / 회원가입, 데이터 조회와 같은 경우는 HTTP 요청을 사용합니다.
