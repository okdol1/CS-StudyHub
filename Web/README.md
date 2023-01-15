# Web

- [CORS](#cors)
- [HTTP Method](#http-method)
- [HTTP 상태 코드](#http-상태-코드)
- [HTTP 헤더](#http-헤더)
- [브라우저 렌더링 원리](#브라우저-렌더링-원리)
- [브라우저 저장소 차이점](#브라우저-저장소-차이점)
- [JSON을 사용하는 이유]()

## CORS

브라우저에서는 보안적인 이유로 cross-origin HTTP 요청들을 제한합니다. 그래서 cross-origin 요청을 하려면 서버의 동의가 필요합니다. 만약 서버가 동의한다면 브라우저에서는 요청을 허락하고, 동의하지 않는다면 브라우저에서 거절합니다.

이러한 허락을 구하고 거절하는 메커니즘을 HTTP-header를 이용해서 가능한데, 이를 CORS(Cross-Origin Resource Sharing)라고 부릅니다.

그래서 브라우저에서 cross-origin 요청을 안전하게 할 수 있도록 하는 메커니즘입니다.

### 1 cross-origin

cross-origin이란 다음 중 한 가지라도 다른 경우를 말합니다.

프로토콜 - http와 https는 프로토콜이 다르다.
도메인 - domain.com과 other-domain.com은 다르다.
포트 번호 - 8080포트와 3000포트는 다르다.

### 2 CORS 정책 위반 이슈

Cross-Origin Resource Sharing 정책을 위반했을때 발생하는 이슈입니다.

브라우저는 도메인 경계를 넘어 요청하는것을 방지합니다. 이때 발생하는 것이 CORS이슈이다. 이를 통해 잠재적 악성 문서를 격리하여 공격 경로를 줄이는데 도움이 된다.

## HTTP Method

클라이언트가 서버에 요청방법을 정의하는 것으로 주어진 리소스에 수행하길 원하는 행동을 나타낸다.

GET : 서버에게 조회할 리소스를 요청한다. (READ, 조회)
POST : 서버에게 본문(body)에 생성할 데이터를 삽입하여 전송한다. (CREATE, 생성)
PUT : 서버에게 본문에 수정할 데이터를 삽입하여 전송한다. (UPDATE, 수정)
DELETE : 서버에게 삭제할 리소스를 요청한다. (DELETE, 삭제)
PATCH : PUT과 비슷하지만 일부만 수정한다는 점에서 다르다.

## HTTP 상태 코드

HTTP 상태 코드는 클라이언트가 보낸 HTTP 요청에 대한 서버의 응답 코드로, 상태 코드에 따라 요청의 성공/실패 여부를 판단한다.

> 1xx : Informational -> 요청이 수신되어 처리중<br>
> 2xx : Success -> 성공을 알리는 상태 코드<br>
> 3xx : Redirection -> 리다이렉션(다른 페이지로 이동)을 알리는 상태 코드<br>
> 4xx : Client Error -> 요청 오류, 요청 자체에 오류가 있는 경우다.<br>
> 5xx : Server Error -> 서버 오류, 요청은 정상이지만 서버에 오류가 생긴 경우다.

### 1 HTTP 상태코드 종류와 의미

100번대는 요청이 수신되어 처리중이라는 뜻인데, 거의 사용하지 않으므로 생략한다.

200번대는 성공의 의미를 뜻하는데 더 자세하게는 아래와 같다.

- 200 OK : 요청 성공
- 201 Created : 요청 성공해서 새로운 리소스가 생성됨
- 202 Accepted : 요청이 접수되었으나 처리가 완료되지 않았음
- 204 No Content : 서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음

300 번대는 리다이렉션에 대한 내용으로, 더 자세하게는 아래와 같다.

- 301 Moved Permanently : 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음
- 302 Found : 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음
- 303 See Other : 리다이렉트시 요청 메서드가 GET으로 변경
- 304 Not Modified : 캐시를 목적으로 사용
- 307 Temporary Redirect : 리다이렉트시 요청 메서드와 본문 유지(요청 메서드를 변경하면 안된다.)
- 308 Permanent Redirect : 리다이렉트시 요청 메서드와 본문 유지(처음 POST를 보내면 리다이렉트도 POST 유지)

400번대는 클라이언트 측에서 오류가 발생했다고 알려준다.

- 400 Bad Request : 클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음
- 401 Unauthorized : 클라이언트가 해당 리소스에 대한 인증이 필요함
- 403 Forbidden : 서버가 요청을 이해했지만 승인을 거부함
- 404 Not Found : 요청 리소스를 찾을 수 없음

500번대는 서버 측에서 오류가 발생했다고 알려주는 것이다. 자세한 사항은 다음과 같다.

- 500 Internal Server Error : 서버 문제로 오류 발생, 애매하면 500 오류
- 503 Service Unavailable : 서비스 이용 불가

## HTTP 헤더

요청/응답 헤더 및 본문 헤더 등 다양한 속성들이 있지만 여기선 주요한 속성들만 명시한다.

### 요청 헤더

- Host : 서버의 도메인 이름과 TCP 포트번호 (표준 포트는 생략 가능)

  - ```
    Host: en.wikipedia.org:8080
    ```

- Content-Type : POST/PUT 메서드를 사용할 때 본문의 타입

  - ```
    Content-Type: application/x-www-form-urlencoded
    ```

- If-Modified-Since : 명시한 날짜 이후로 변경된 리소스만 획득

  - ```
    If-Modified-Since: Sat, 29 Oct 1994 19:43:31 GMT
    ```

- Origin : 요청이 어느 도메인에서 왔는지 명시, 서버의 `Access-Control-*` 속성에 필요

  - ```
    Origin: http://www.example-social-network.com
    ```

- Cookie : 서버의 `Set-Cookie` 로 설정된 쿠키 값

  - ```
    Cookie: $Version=1; Skin=new;
    ```

### 응답 헤더

- Access-Control-\* : CORS를 허용하기 위한 웹사이트 명시

  - ```
    Access-Control-Allow-Origin: *
    ```

- Set-Cookie : 클라이언트에 쿠키 설정

  - ```
    Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1
    ```

- Last-Modified : 요청한 리소스가 마지막으로 변경된 시각

  - ```
    Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT
    ```

- Location : 3xx 상태 코드일 때, 리다이렉션 되는 주소

  - ```
    Location: http://www.w3.org/pub/WWW/People.html
    ```

- Allow : 요청한 리소스에 대해 가능한 메서드들

  - ```
    Allow: GET, HEAD
    ```

## 브라우저 렌더링 원리

1. DOM, CSSOM생성: 가장 첫번째 단계로 서버로부터 받은 HTML, CSS를 다운받는다 → 단순한 텍스트인 HTML, CSS파일을 Object Model로 만든다. HTML은 DOM으로, CSS는 CSSOM으로 만들어진다. (html이 여기서 파싱된다) DOM Tree와 CSSOM Tree가 만들어진다
2. Render Tree생성: DOM Tree와 CSSOM Tree가 만들어졌으면 그 다음으로는 이 둘을 이용하여 Render Tree를 생성한다. 렌더트리에는 스타일 정보가 설정되어있고, 실제 화면에 표현되는 노드들로 구성된다.
3. Layout 단계: 브라우저의 뷰포트(Viewport) 내에서 각 노드들의 정확한 위치와 크기를 계산한다. 생성된 Render Tree 노드들이 가지고 있는 스타일과 속성에 따라서 브라우저 화면의 어느위치에 어느크기로 출력될지 계산하는 단계이다.(reflow 단계) 레이아웃 단계에서 %, vh, vw와 같이 상대적인 위치, 크기 속성은 실제 화면에 그려지는 픽셀 단위로 변환된다.
4. Paint: Layout 계산이 완료되면 이제 요소들을 실제 화면을 그리게 된다.(repaint 단계) 처리해야하는 스타일이 복잡할수록 paint 단계에 소요되는 시간이 길다. (가령 그라데이션, 그림자 효과 > 단색 배경)

## 브라우저 저장소 차이점

key-value형태의 만료기한이있는 쿠키의 단점을 보완해 웹스토리지(로컬과 세션)가 만들어졌다.

로컬 스토리지는 클라이언트의 정보를 영구적으로(자동로그인) 저장하는 반면 세션 스토리지는(비로그인 장바구니) 브라우저를 닫을 경우 정보가 삭제된다.
쿠키는 로컬&세션에 비해 용량이 매우작고, 치명적인 단점에는 암호화가 없어 정보 도난 위험이있다.

## JSON을 사용하는 이유

### 1 JSON이란?

- JavaScript Object Notation이라는 줄임말로 데이터를 저장하거나 전송할 때 많이 사용하는 경량의 데이터 교환 형식이다.
- JSON은 데이터 포맷일 뿐 어떠한 통신 방법이나 프로그래밍 문법이 아니라 단순히 데이터를 표시하는 표현 방법일 뿐이다.

### 2 JSON을 사용하는 이유

- 네트워크를 사용하는 데이터 통신은 데이터를 byte 형태로 전달한다.
- 서버와 클라이언트 또는 애플리케이션 처리할 데이터를 주고받을 때 자료 형식 중 대표적인 것이 XML과 JSON이 있다.
- XML과 JSON은 공식포맷이기 때문에 다른 개발자 사이에 데이터 통신을 가능하도록 한다.

### 3 XML -> JSON으로 많이 사용되는 이유

- JSON은 특정 언어에 종속되지 않은다.
- XML보다 최소한의 용량으로 데이터 전송이 가능하다.
- XML은 HTML과 같은 형태의 태그 구조 형식으로 데이터를 표현하는데 이는 구조가 부족해지며 용량이 커지기 때문이다.
- XML보다 JSON은 구조 정의의 용이성과 가독성이 좋다.
