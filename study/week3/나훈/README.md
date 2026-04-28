# Week 3 - 나훈

> 주제: 네트워크 / HTTP / 통신
> 

---

## 1. IP / DNS

**IP (Internet Protocol)**

- 네트워크 상에서 기기를 식별하기 위한 고유한 주소 체계
- 패킷을 출발지에서 목적지까지 전달하는 역할 (라우팅)
- 비연결성, 비신뢰성 → 패킷 손실/순서 보장은 상위 계층(TCP)에서 처리

**IPv4 vs IPv6**

| 구분 | IPv4 | IPv6 |
| --- | --- | --- |
| 길이 | 32비트 (약 43억 개) | 128비트 (사실상 무한) |
| 표기 | 192.168.0.1 | 2001:0db8:85a3:::8a2e:0370:7334 |
| 등장 배경 | 초기 인터넷 표준 | IPv4 주소 고갈 문제 해결 |

**DNS (Domain Name System)**

- 사람이 읽기 쉬운 도메인([www.google.com](http://www.google.com/))을 IP 주소(142.250.x.x)로 변환해주는 시스템
- 인터넷의 전화번호부 역할
- 도메인을 입력하면 DNS 서버가 해당 도메인의 IP 주소를 반환

**DNS 동작 흐름**

1. 브라우저 캐시 확인
2. OS의 hosts 파일 / DNS 캐시 확인
3. 로컬 DNS 서버(통신사 DNS)에 질의
4. Root → TLD(.com) → Authoritative DNS 순으로 재귀/반복 질의
5. 최종 IP 주소를 응답받아 캐시

> **iOS 연결 포인트**
> 
> - URLSession이 URL을 요청하면 내부적으로 DNS Resolution을 자동 수행
> - 첫 요청 시 DNS 조회 시간이 추가로 들어감 → 캐시 이후엔 빠름
> - 회사 내부망에서 도메인이 안 뚫릴 때 DNS 설정 문제일 수 있음

---

## 2. TCP / UDP

**TCP (Transmission Control Protocol)**

- 연결 지향(Connection-oriented) 프로토콜
- 3-way handshake로 연결 수립 후 데이터 전송, 4-way handshake로 종료
- 데이터 순서 보장, 손실 시 재전송 → 신뢰성 있는 전달
- 흐름 제어, 혼잡 제어 기능 내장

**UDP (User Datagram Protocol)**

- 비연결(Connectionless) 프로토콜
- 데이터그램 단위로 던지기만 함 (받았는지 확인 X)
- 순서 보장 X, 재전송 X → 빠르지만 신뢰성 없음
- 헤더가 작아서 오버헤드가 적음

| 구분 | TCP | UDP |
| --- | --- | --- |
| 연결 방식 | 연결 지향 (handshake) | 비연결 |
| 신뢰성 | O (재전송, 순서 보장) | X |
| 속도 | 상대적으로 느림 | 빠름 |
| 헤더 크기 | 20바이트 이상 | 8바이트 |
| 사용 예 | HTTP, HTTPS, FTP, 메일 | DNS, 영상 스트리밍, 게임, VoIP |

**3-way handshake**

```
클라이언트 → SYN → 서버
클라이언트 ← SYN+ACK ← 서버
클라이언트 → ACK → 서버
→ 연결 수립
```

> **iOS 연결 포인트**
> 
> - 일반적인 REST API 통신은 TCP 기반 HTTP/HTTPS 사용
> - 실시간성이 중요한 영상 통화, 게임 등은 UDP 기반 라이브러리 사용
> - URLSession은 TCP 기반이라 신뢰성 있게 동작
> - 모바일 환경은 네트워크 전환(Wi-Fi ↔ Cellular)이 잦아서 TCP 연결이 끊어질 수 있음 → 재시도 로직 필요

---

## 3. HTTP / HTTPS

**HTTP (HyperText Transfer Protocol)**

- 클라이언트와 서버가 데이터를 주고받기 위한 애플리케이션 계층 프로토콜
- 요청(Request)과 응답(Response) 구조
- **무상태(Stateless)**: 각 요청은 독립적이라 서버가 이전 요청을 기억하지 않음
- 평문 전송 → 중간에서 가로채면 내용이 그대로 노출됨

**HTTPS (HTTP Secure)**

- HTTP에 SSL/TLS 암호화 계층이 추가된 프로토콜
- 데이터를 암호화해서 도청, 변조, 위조 방지
- 인증서를 통해 서버의 신원도 확인 가능

**SSL/TLS Handshake (간단 흐름)**

1. 클라이언트가 서버에 연결 요청 + 지원 가능한 암호 알고리즘 목록 전송
2. 서버가 인증서(공개키 포함) 전송
3. 클라이언트가 인증서 검증 후, 대칭키를 공개키로 암호화해서 서버에 전송
4. 이후 통신은 대칭키로 암호화 → 빠르고 안전

| 구분 | HTTP | HTTPS |
| --- | --- | --- |
| 포트 | 80 | 443 |
| 암호화 | X | O (SSL/TLS) |
| 보안 | 취약 | 안전 |
| 속도 | 약간 빠름 | 약간 느림 (handshake 비용) |

> **iOS 연결 포인트**
> 
> - iOS는 **ATS(App Transport Security)** 정책으로 HTTPS 통신을 강제 (HTTP는 Info.plist 예외 처리 필요)
> - URLSession은 기본적으로 HTTPS 인증서 검증을 자동 수행
> - 자체 서명 인증서나 사내망 서버 사용 시 `URLSessionDelegate`에서 인증 처리 커스터마이징
> - HTTP/2, HTTP/3(QUIC) 도 URLSession이 지원 → 멀티플렉싱으로 성능 향상

---

## 4. 요청과 응답 구조

**HTTP 요청 (Request) 구조**

```
[Start Line]    GET /users/1 HTTP/1.1
[Headers]       Host: api.example.com
                Authorization: Bearer xxx
                Content-Type: application/json
[Empty Line]
[Body]          { "name": "nahoon" }   ← 선택적 (POST, PUT 등)
```

- **Start Line**: 메서드 + 경로 + HTTP 버전
- **Headers**: 메타데이터(인증, Content-Type, Accept 등)
- **Body**: 실제 데이터 (GET은 보통 없음)

**HTTP 응답 (Response) 구조**

```
[Status Line]   HTTP/1.1 200 OK
[Headers]       Content-Type: application/json
                Content-Length: 123
[Empty Line]
[Body]          { "id": 1, "name": "nahoon" }
```

- **Status Line**: HTTP 버전 + 상태 코드 + 상태 메시지
- **Headers**: 응답 메타데이터
- **Body**: 서버가 보내는 실제 데이터

**자주 쓰는 헤더**

| 헤더 | 역할 |
| --- | --- |
| Content-Type | 본문의 데이터 형식 (application/json 등) |
| Authorization | 인증 정보 (Bearer 토큰 등) |
| Accept | 클라이언트가 받을 수 있는 데이터 형식 |
| Cache-Control | 캐시 정책 |
| User-Agent | 클라이언트 정보 |

> **iOS 연결 포인트**
> 
> - `URLRequest`로 요청 구성 → method, headers, httpBody 직접 설정 가능
> - JSON 전송 시 `Content-Type: application/json` 명시 + `JSONEncoder`로 인코딩
> - 응답은 `URLResponse` + `Data` 형태 → `JSONDecoder`로 디코딩
> - `HTTPURLResponse`로 캐스팅하면 statusCode, allHeaderFields 접근 가능

---

## 5. HTTP Method

| 메서드 | 용도 | 멱등성 | 안전성 | Body |
| --- | --- | --- | --- | --- |
| GET | 자원 조회 | O | O | X (보통) |
| POST | 자원 생성 | X | X | O |
| PUT | 자원 전체 수정/대체 | O | X | O |
| PATCH | 자원 부분 수정 | X | X | O |
| DELETE | 자원 삭제 | O | X | X |
| HEAD | 헤더만 조회 (Body 없음) | O | O | X |
| OPTIONS | 지원하는 메서드 조회 | O | O | X |

**용어 정리**

- **안전성(Safe)**: 서버 상태를 변경하지 않음 → GET, HEAD
- **멱등성(Idempotent)**: 같은 요청을 여러 번 해도 결과가 동일 → GET, PUT, DELETE
- POST는 호출할 때마다 새로 생성되므로 멱등하지 않음
- PATCH는 부분 수정이라 구현에 따라 멱등할 수도 있고 아닐 수도 있음

**PUT vs PATCH**

- PUT: 자원 전체를 새 값으로 대체 (없는 필드는 사라질 수 있음)
- PATCH: 명시한 필드만 부분 수정

흔한 오해: "POST가 GET보다 안전하다"
정확한 사실: 전송 중 보안은 HTTPS가 결정하며, 메서드와 무관함

- HTTP라면 GET/POST 둘 다 평문 노출
- HTTPS라면 GET/POST 둘 다 통신 구간 암호화

그럼에도 민감 데이터에 POST를 쓰는 이유:

- GET은 데이터가 URL에 포함됨 → URL은 HTTPS여도 아래 위치에 평문으로 남음
    - 브라우저 히스토리
    - 서버 액세스 로그
    - Referer 헤더 (다른 사이트로 누출)
    - 북마크 / 공유 링크
- POST는 데이터가 Body에 들어가므로 위 노출 경로가 차단됨

결론: 로그인, 결제, 토큰 등 민감 데이터는 `POST + HTTPS` 조합이 표준

**왜 HTTPS여도 URL이 평문으로 남는가?**

HTTPS는 TLS로 **클라이언트 ↔ 서버 통신 구간만** 암호화한다.
양 끝단(브라우저 자체, 서버 자체)에서 처리되는 데이터는 평문이다.

> **iOS 연결 포인트**
> 
> - URLRequest의 `httpMethod` 프로퍼티에 문자열로 메서드 지정 ("GET", "POST" 등)
> - GET 요청은 일반적으로 쿼리 파라미터로 데이터 전달 → URLComponents 활용
> - POST/PUT/PATCH는 httpBody에 인코딩된 데이터 담아서 전송
> - 서버 API 명세에 따라 정확한 메서드 사용 → REST 원칙 준수

---

## 6. 상태 코드

서버가 요청 처리 결과를 알려주는 3자리 숫자 코드

| 범위 | 의미 | 대표 코드 |
| --- | --- | --- |
| 1xx | 정보성 | 100 Continue |
| 2xx | 성공 | 200 OK, 201 Created, 204 No Content |
| 3xx | 리다이렉션 | 301 Moved Permanently, 304 Not Modified |
| 4xx | 클라이언트 오류 | 400, 401, 403, 404, 409, 429 |
| 5xx | 서버 오류 | 500, 502, 503, 504 |

**자주 마주치는 상태 코드**

| 코드 | 의미 | 설명 |
| --- | --- | --- |
| 200 | OK | 요청 성공 |
| 201 | Created | 자원 생성 성공 (POST 후) |
| 204 | No Content | 성공했지만 응답 본문 없음 (DELETE 후 자주 사용) |
| 301 | Moved Permanently | 영구 리다이렉트 |
| 304 | Not Modified | 캐시 사용 (변경 없음) |
| 400 | Bad Request | 잘못된 요청 형식 |
| 401 | Unauthorized | 인증 안 됨 (로그인 필요) |
| 403 | Forbidden | 인증은 됐지만 권한 없음 |
| 404 | Not Found | 자원 없음 |
| 409 | Conflict | 자원 충돌 (중복 생성 등) |
| 429 | Too Many Requests | 요청 횟수 초과 |
| 500 | Internal Server Error | 서버 내부 오류 |
| 502 | Bad Gateway | 게이트웨이 오류 |
| 503 | Service Unavailable | 서버 과부하/점검 중 |

**401 vs 403**

- 401 → 인증 정보가 아예 없거나 유효하지 않음
- 403 → 인증은 됐지만 해당 자원 접근 권한 없음

> **iOS 연결 포인트**
> 
> - `HTTPURLResponse.statusCode`로 상태 코드 확인 후 분기 처리
> - 401 응답 시 토큰 만료 처리 → Refresh Token으로 재발급 후 원래 요청 재시도
> - 5xx는 보통 사용자에게 "잠시 후 다시 시도" 안내, 4xx는 클라이언트 측 문제 수정 필요
> - 상태 코드만 보고 처리하지 말고 응답 Body의 에러 메시지도 함께 활용

---

## 7. REST API

**REST (REpresentational State Transfer)**

- HTTP를 기반으로 자원(Resource)을 명확하게 표현하고 조작하는 아키텍처 스타일
- 자원을 URI로 표현하고, HTTP Method로 행위를 표현

**REST 6대 원칙**

1. **Client-Server**: 클라이언트와 서버 분리
2. **Stateless**: 각 요청은 독립적, 서버는 상태 저장 X
3. **Cacheable**: 응답은 캐시 가능 여부를 명시
4. **Uniform Interface**: 일관된 인터페이스 (URI + Method)
5. **Layered System**: 다단계 구조 (프록시, 게이트웨이 가능)
6. **Code on Demand** (선택): 서버가 클라이언트에 코드 전달 가능

**REST API 설계 원칙**

- URI는 **자원(명사)** 으로 표현 → `/users`, `/posts/1`
- 행위는 **HTTP Method** 로 표현 → `GET /users`, `POST /users`
- 동사 사용 자제 → `/getUsers` X, `GET /users` O
- 계층 구조 표현 → `/users/1/posts`
- 복수형 사용 권장

**RESTful 예시**

| 행위 | 잘못된 예 | 올바른 예 |
| --- | --- | --- |
| 유저 목록 조회 | GET /getUsers | GET /users |
| 유저 1명 조회 | GET /user?id=1 | GET /users/1 |
| 유저 생성 | POST /createUser | POST /users |
| 유저 수정 | POST /users/1/update | PUT /users/1 |
| 유저 삭제 | GET /deleteUser?id=1 | DELETE /users/1 |

> **iOS 연결 포인트**
> 
> - 대부분의 모바일 앱이 백엔드와 REST API로 통신
> - URLSession + Codable 조합으로 REST 통신을 깔끔하게 구현
> - Alamofire, Moya 같은 라이브러리는 REST 통신을 더 편하게 추상화
> - GraphQL이 REST의 over-fetching/under-fetching 문제를 해결하기 위해 등장 → 필요한 데이터만 요청 가능

---

## 8. 쿠키 / 세션 / 토큰

HTTP는 **무상태(Stateless)** 라서 사용자 인증 상태를 유지하려면 별도 메커니즘이 필요

**쿠키 (Cookie)**

- 서버가 클라이언트(브라우저)에 저장하는 작은 데이터 조각
- 매 요청마다 자동으로 헤더(`Cookie`)에 담겨 서버로 전송
- 클라이언트에 저장 → 보안 약함, 위변조 가능

**세션 (Session)**

- 서버가 사용자 정보를 **서버 측**에 저장하고, 식별자(Session ID)만 쿠키로 전달
- 실제 데이터는 서버에 있어서 비교적 안전
- 서버가 모든 세션을 관리해야 해서 메모리 부담, 서버 확장 어려움 (Sticky Session 필요)

**토큰 (Token, 주로 JWT)**

- 인증 정보를 암호화/서명한 문자열을 클라이언트가 보관
- 매 요청마다 헤더(`Authorization: Bearer xxx`)에 담아서 전송
- 서버는 토큰의 서명만 검증 → **Stateless** 인증 가능, 서버 확장 유리

**JWT 구조**

```
xxxxx.yyyyy.zzzzz
↓     ↓     ↓
Header.Payload.Signature
```

- Header: 알고리즘 정보
- Payload: 사용자 정보 (Claims)
- Signature: 서명 (위변조 방지)

| 구분 | 쿠키 | 세션 | 토큰 |
| --- | --- | --- | --- |
| 저장 위치 | 클라이언트 | 서버 | 클라이언트 |
| 서버 부담 | 적음 | 큼 | 적음 |
| 보안 | 약함 | 강함 | 중간 (탈취 시 위험) |
| 확장성 | 좋음 | 나쁨 | 좋음 |
| 사용처 | 웹 위주 | 전통적 웹 | 모바일/SPA/마이크로서비스 |

**Access Token vs Refresh Token**

- **Access Token**: 짧은 수명 (수 분~수 시간), 실제 API 요청 시 사용
- **Refresh Token**: 긴 수명, Access Token 만료 시 재발급 용도로만 사용
- Access Token이 탈취돼도 짧은 수명 덕분에 피해 최소화
- Refresh Token은 안전한 곳에 저장 (모바일은 Keychain)

> **iOS 연결 포인트**
> 
> - 모바일 앱은 쿠키/세션보다 **토큰 기반 인증** 이 일반적
> - Access Token은 메모리/UserDefaults, Refresh Token은 **Keychain** 에 저장 (보안 강도 차이)
> - 401 응답 시 → Refresh Token으로 재발급 → 원래 요청 재시도 (인터셉터 패턴)
> - URLSession에 Custom Authentication Header 자동 추가 시 `URLSessionConfiguration.httpAdditionalHeaders` 또는 인터셉터 활용

---

## 9. 소켓 통신 / WebSocket / iOS 연결

**소켓 통신 (Socket Communication)**

- 네트워크 상에서 두 프로세스가 양방향으로 데이터를 주고받기 위한 끝점(Endpoint)
- IP + Port 조합으로 식별
- HTTP와 달리 **연결을 유지하면서** 실시간 양방향 통신 가능
- TCP 소켓: 신뢰성 있는 연결, UDP 소켓: 빠르지만 신뢰성 X

**HTTP의 한계 (실시간 통신 관점)**

- HTTP는 클라이언트가 요청해야만 서버가 응답하는 단방향 구조
- 서버가 먼저 데이터를 보낼 수 없음 (Push 불가)
- 실시간 채팅, 알림 같은 기능에 부적합 → 폴링(Polling) 같은 우회로 비효율

**WebSocket**

- HTTP 기반으로 시작해서 한 번의 handshake 이후 **양방향 지속 연결** 로 전환
- `ws://` 또는 `wss://` (보안)
- 서버가 클라이언트에게 자유롭게 데이터 푸시 가능
- 채팅, 실시간 주가, 게임 등에 적합

**WebSocket 흐름**

```
1. 클라이언트 → HTTP GET + Upgrade: websocket → 서버
2. 서버 → 101 Switching Protocols → 클라이언트
3. 이후 TCP 연결을 유지한 채 양방향 통신
```

| 구분 | HTTP | WebSocket |
| --- | --- | --- |
| 통신 방향 | 단방향 (요청-응답) | 양방향 |
| 연결 | 요청마다 끊김 (Keep-Alive 옵션 가능) | 유지 |
| 서버 푸시 | 불가능 | 가능 |
| 사용 예 | REST API | 채팅, 실시간 알림, 라이브 데이터 |

[**Socket.io**](http://socket.io/)

- WebSocket 위에 자동 재연결, 룸(Room), 이벤트 기반 API 등을 추가한 라이브러리
- WebSocket 미지원 환경에서는 폴링으로 자동 폴백
- 서버/클라이언트 모두 [socket.io](http://socket.io/) 라이브러리를 써야 함 (순수 WebSocket과 호환 X)

> **iOS 연결 포인트**
> 
> - iOS 13+ 부터 `URLSessionWebSocketTask` 로 WebSocket 기본 지원
> - Starscream, Socket.IO-Client-Swift 같은 서드파티 라이브러리도 자주 사용
> - 채팅, 실시간 알림, 라이브 스트리밍 화면에서 WebSocket 활용
> - 백그라운드 진입 시 WebSocket 연결이 끊길 수 있음 → 포그라운드 복귀 시 재연결 로직 필요
> - 일반 API는 URLSession(HTTP), 실시간 기능은 WebSocket → 한 앱에서 두 방식을 함께 쓰는 경우가 일반적
