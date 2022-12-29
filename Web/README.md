# Web

- [CORS](#cors)

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