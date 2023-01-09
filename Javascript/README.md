# Javascript

- [실행 컨텍스트](#실행-컨텍스트-execution-context)
- [이벤트 루프](#이벤트-루프)
- [Dom, 가상 Dom](#dom-가상-dom)
- [이벤트 버블링, 이벤트 캡쳐링](#이벤트-버블링-이벤트-캡쳐링)
- [URL 을 입력하고 벌어지는 일](#url-을-입력하고-벌어지는-일)
- [브라우저의 동작 원리](#브라우저의-동작-원리)
- [Promise](#promise)
- [호이스팅](#호이스팅)
- [클로저](#클로저)
- [this](#this)

## 실행 컨텍스트 (Execution Context)

> 실행 컨텍스트를 바르게 이해하면<br> 자바스크립트가 스코프를 기반으로 식별자와 식별자에 바인딩된 값을 관리하는 방식과 호이스팅이 발생하는 이유, 클로저의 동작 방식, 그리고 태스크 큐와 함께 동작하는 이벤트 핸들러와 비동기 처리의 동작 방식을 이해할 수 있다.

### 1 실행 컨텍스트란?

- 코드를 **실행**하는데 필요한 **환경**을 제공하는 객체
- 동일한 환경에 있는 코드들을 실행 시, 필요한 환경 정보를 모아 컨텍스트를 구성하고, 이를 콜 스택에 쌓아 올렸다가, **가장 위에 쌓여있는 컨텍스트와 코드를 실행하는 방식**으로 **전체 코드의 환경과 순서를 보장**합니다.

ECMAScript 사양은 소스코드를 4가지로 구분한다.<br>
소스코드(실행 가능한 코드 excutable code)를 4가지 타입으로 구분하는 이유는 소스코드의 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리 내용이 다르기 때문이다.

소스코드의 타입| 설명
--|--
`global code` (전역 코드)   | 전역에 존재하는 소스코드를 말한다. 전역에 정의된 함수, 클래스 등의 내부 코드는 포함되지 않는다.
`function code` (함수 코드) | 함수 내부에 존재하는 소스코드를 말한다. 함수 내부에 중첩된 함수, 클래스 등의 내부 코드는 포함되지 
`eval code` (eval 코드)     | 빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드를 말한다.
`module code` (모듈 코드)   | 모듈 내부에 존재하는 소스코드를 말한다. 모듈 내부의 함수, 클래스 등의 내부 코드는 포함되지 않는다.

- 전역 코드
  - 전역 코드는 전역 변수를 관리하기 위해 최상위 스코프인 전역 스코프를 생성해야 한다.
  - 그리고 var 키워드로 선언된 전역 변수와 함수 선언문으로 정의된 전역 함수를 전역 객체의 프로퍼티와 메서드로 바인딩하고 참조하기 위해 전역 객체와 연결되어야 한다.
  - 이를 위해 전역 코드가 평가되면 전역 실행 컨텍스트가 생성된다.
- 함수 코드
  - 함수 코드는 지역 스코프를 생성하고 지역 변수, 매개변수, arguments 객체를 관리해야 한다.
  - 그리고 생성한 지역 스코프를 전역 스코프에서 시작하는 스코프 체인의 일원으로 연결해야 한다.
  - 이를 위해 함수 코드가 평가되면 함수 실행 컨텍스트가 생성된다.

### 2 소스코드의 평가와 실행

자바스크립트 엔진은 소스코드를 2개의 과정으로 나누어 처리한다.

1. 소스코드의 평가
1. 소스코드의 실행

### 3 실행 순서와 Execution Stack

엔진이 스크립트 파일을 실행하기 전에 `글로벌 실행 컨텍스트(Global Execution Context)`가 생성되고, 함수를 호출할 때마다 `함수 실행 컨텍스트(Function Excution Context, FEC)`가 생성됩니다.<br>
그렇게 때문에 글로벌 컨텍스트는 한 프로그램에 한 개만 존재하게 됩니다.

> 글로벌 생성 -> 스크립트 파일 생성 -> 함수 호출 -> 함수 생성 -> 함수실행 종료 -> 함수 제거 -> 모든 코드 실행 종료 -> 글로벌 제거

JS의 Excution stack은 LIFO(후입 선출법 last-in, first-out) 구조로 이뤄져 있습니다. 왜냐면 JS는 싱글 스레드이기 때문에 여러 일을 동시에 처리할 수 없기 때문이다.

![실행 순서와 Execution Stack](https://miro.medium.com/max/700/1*dUl6qPEaDJJTXWythQsEtQ.gif)

그래서 맨 위의 컨텍스트가 실행이 완료되어 사라진 뒤에야 그 다음의 컨텍스트가 실행될 수 있습니다.

### 4 실행 컨텍스트의 구조

실행 컨텍스트는 세 가지 구조로 이루어져 있습니다.

1. This Binding
1. Laxical Environment
1. Variable Environment

### 4.1 This Binding

- this는 자신을 불러온 객체를 가르킵니다.
- 실행 컨텍스트 활성화 당시, this가 지정되지 않은 경우(자신을 불러온 객체가 없는 경우) this에는 전역 객체가 저장됩니다.
- 하지만 arrow function을 사용하게 되면, 부모의 객체를 가르키게 됩니다.

### 4.2 Lexical Environment

### 4.2.1 Environment Record

- 식별자들을 연관된 렉시컬 환경 범위 안의 값들 맵핑시켜주는 구조
- `let` 과 `const`으로 선언된 식별자들의 값을 보관하는 곳입니다.

### 4.2.2 Outer Reference

- 부모 렉시컬 환경의 참조값을 갖고 있습니다.
- = JS 엔진이 최근 렉시컬 환경을 찾지 못하면 외부 환경의 내부에 있는 변수를 볼 수 있도록 해줍니다.
- `global context` : outer reference == null
- `function context` : outer reference == 부모의 lexical 환경(글로벌이나 다른 함수 실행 컨텍스트)
- 함수 안에 선언된 변수는 Environment Record에 저장될 뿐만 아니라, argument에 유사배열객체 안에도 저장됩니다.

### 4.3 Variable Environment

- var로 선언된 식별자들의 값을 저장하는데 사용됩니다.
- Lexical Environment와 비슷하지만 최초 실행시의 스냅샷을 유지한다는 점이 다릅니다.
- 실행 컨텍스트 생성 시 Variable Environment에서 해당 정보를 먼저 담은 다음, 이를 그대로 복사해 Lexical Environment를 생성합니다. Variable Environment는 변하지 않은 채로, 복사한 Lexical Environment 값을 사용하게 됩니다.

### 5 컨텍스트의 생성과 실행

실행 컨텍스트는 두 가지 단계를 거칩니다.

1. 생성단계
1. 실행단계

### 5.1 생성단계

- 글로벌의 생성단계에선 window와 this가 생성됩니다.
- Environment Record에서 변수가 선언될 때, default value로 undefined / unitialized를 갖게 됩니다.
- 변수가 var로 선언되면 undefined, 변수가 let이나 const로 선언되면 unitialized의 값을 가지게 되는데 그렇기 때문에 변수 선언 전에 변수가 사용되면 Reference Error가 뜨게 됩니다.
- 그리고 모든 함수 선언은 전체적으로 메모리에 올라갑니다.

### 5.2 실행단계

- 실행단계에 들어오면, JS 엔진은 한줄한줄 코드를 실행합니다.
- 코드를 실행하면서 이미 메모리에 올라와 선언된 변수들을 값을 할당해줍니다.

![컨텍스트의 생성과 실행](https://miro.medium.com/max/700/1*SBP65hdVDW5j0LuVryTiXw.gif)

### 참고

- 모던 JavsScript DeepDive
- [[10분 테코톡] 하루의 실행 컨텍스트](https://www.youtube.com/watch?v=EWfujNzSUmw)
- [실행 컨텍스트](https://velog.io/@deli-ght/%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8#%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%EC%9D%98-%EA%B5%AC%EC%A1%B0)

## 이벤트 루프

JavaScript의 런타임 모델은 코드의 실행, 이벤트의 수집과 처리, 큐에 대기 중인 하위 작업을 처리하는 <b>이벤트 루프</b>에 기반하고 있다.

### 1 런타임 개념

### 시각적 표현
![자바스크립트 런타임 모델 시각적 표현](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop/the_javascript_runtime_environment_example.svg)

### 1.1 스택
함수의 호출들은 '프레임' 스택을 형성합니다.

```javascript
function foo(b) {
  let a = 10
  return a + b + 11
}

function bar(x) {
  let y = 3
  return foo(x * y)
}

const baz = bar(7) // 42를 baz에 할당
```

위 코드의 실행 순서는 다음과 같습니다.

1. bar를 호출할 때, bar의 인수와 지역 변수를 포함하는 첫 번째 프레임이 생성됩니다.
1. bar가 foo를 호출할 때, foo의 인수와 지역 변수를 포함하는 두 번째 프레임이 생성되어 첫 번째 프레임의 위로 푸시됩니다.
1. foo가 반환하면, 맨 위의 프레임 요소를 스택 밖으로 꺼냅니다. (bar 호출 프레임만 남음)
1. bar가 반환하면, 스택이 빕니다.

인수와 지역 변수는 스택 바깥에 저장되므로 바깥 함수가 반환한 후에도 계속 존재할 수 있습니다. 중첩 함수에서 지역 변수에 접근할 수 있는 이유가 이것입니다.

### 1.2 힙
객체는 힙에 할당됩니다. 힙은 단순히 메모리의 큰 (그리고 대부분 구조화되지 않은) 영역을 지칭하는 용어입니다.

### 1.3 큐
JavaScript 런타임은 메시지 큐, 즉 처리할 메시지의 대기열을 사용합니다. 각각의 메시지에는 메시지를 처리하기 위한 함수가 연결돼있습니다.

이벤트 루프의 임의 시점에, 런타임은 대기열에서 가장 오래된 메시지부터 큐에서 꺼내 처리하기 시작합니다. 이를 위해 런타임은 꺼낸 메시지를 매개변수로, 메시지에 연결된 함수를 호출합니다. 다른 함수와 마찬가지로, 호출로 인한 새로운 스택 프레임도 생성됩니다.

함수 처리는 스택이 다시 텅 빌 때까지 계속됩니다. 그 후, 큐에 메시지가 남아있으면 같은 방법으로 처리를 계속 진행합니다.

### 2 이벤트 루프
Event Loop는 Call Stack과 Callback Queue의 상태를 체크하여,<br>
<b>Call Stack이 빈 상태가 되면, Callback Queue의 첫번째 콜백을 Call Stack으로 밀어넣는다.</b>
이러한 반복적인 행동을 <b>틱(tick)</b> 이라 부른다.

> 정리하면,
> - V8 엔진에서 코드가 실행되면, Call Stack에 쌓인다.
> - Stack의 선입후출의 룰에 따라 제일 마지막에 들어온 함수가 먼저 실행되며, Stack에 쌓여진 함수가 모두 실행된다.
>   - 비동기함수가 실행된다면, Web API가 호출된다.
>   - Web API는 비동기함수의 콜백함수를 Callback Queue에 밀어넣는다.
>   - Event Loop는 Call Stack이 빈 상태가 되면 Callback Queue에 있는 첫번째 콜백을 Call Stack으로 이동시킨다.(이러한 반복적인 행동을 틱(tick)이라 한다.)

<b>자바스크립트를 단일 스레드 프로그래밍 언어라 한번에 하나씩 밖에 실행할 수 없다.

그러나 Web API, Callback Queue, Event Loop 덕분에 멀티 스레드 처럼 보여진다.</b>

### 참고
- [이벤트 루프](https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop)
- [Event Loop (이벤트 루프)](https://velog.io/@thms200/Event-Loop-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84)

## Dom, 가상 Dom

### 1 DOM(Document Object Model)

웹 페이지나 웹 앱에 있는 HTML 요소들을 구조적으로 표현한 것이다.<br><br>
DOM은 애플리케이션의 전체 UI를 나타내며 트리 데이터 구조로 표현된다. 여기에는 Web Document에 있는 각 UI 요소에 대한 노드가 포함된다. 웹 개발자가 Javascript를 통해 콘텐츠를 수정할 수 있기 때문에 매우 유용하다. 또한 구조화된 형식으로 되어 있어 특정 대상을 선택할 수 있고, 모든 코드 작업이 훨씬 쉬워지기 때문에 많은 도움이 된다.

### 2 DOM의 문제점

- DOM은 트리 구조로 되어 있어서 이해하기 쉽지만, **노드의 수가 많아질 수록 속도가 느려지고, DOM 업데이트에 잦은 오류를 발생시킬 수 있다.**
- DOM을 업데이트 하는 것은 콘텐츠 변경을 포함할 뿐만이 아니라 훨씬 더 많은 작업들이 요구된다. 또한 CSS를 다시 계산하고 레이아웃을 변경하려면 복잡한 알고리즘이 필요하며 성능에 영향을 미친다.
- 즉, ****가장 큰 단점은 DOM 트리가 수정될 때마다 렌더 트리가 계속해서 실시간으로 갱신된다는 점**이다.

### 3 Virtual DOM(가상 DOM)

가상 DOM이란 DOM을 가볍게 만든 **Javascript 표현**이라고 할 수 있고 주로 React, Vue.js 그리고 Elm에 사용된다. 가상DOM은 실제로 스크린에 랜더링하는 것이 아니기 때문에 DOM을 직접 업데이트하는 것보다 상대적으로 빠르다.<br><br>
가상 DOM은 실제 DOM에서 처리하는 방식이 아닌 Virtual DOM과 메모리에서 미리 처리하고 저장한 후 실제 DOM과 동기화하는 프로그래밍 개념이다. 해당 DOM을 컴포넌트 단위로 쪼개어 HTML 컴포넌트 조립품 처럼 다루는 개념이다.

### 4 DOM과 Virtual DOM의 비교

||DOM|Virtual DOM
--|--|--
업데이트|느리다|빠르다
HTML 업데이트 방식|HTML을 직접적으로 업데이트|HTML을 직접적으로 업데이트 하지 않음
새로운 element 업데이트 방식|새로운 element가 업데이트 된 경우 새로운 DOM 생성|새로운 element가 업데이트 된 경우 새로운 가상 DOM 생성 후 이전 DOM과 비교 후 차이점 DOM만 업데이트
메모리|메모리 낭비가 심함|메모리 낭비가 덜함

> DOM코드 

``` html
<ul class="fruits">
    <li>Apple</li>
    <li>Orange</li>
    <li>Banana</li>
</ul>
```
- `DOM` : html 문서를 실체화하고 다루기 위한 트리 형태의 모델(html 문서라는 주문서를 가지고 브라우저 공장에서 그 설계로 실체화한 것)
- 노드로 구성돼있음

> Virtual DOM식으로 표현된 코드

``` javascript
// Virtual DOM representation
{
  type: "ul",
  props: {
    "class": "fruits"
  },
  children: [
    {
      type: "li",
      props: null,
      children: [
        "Apple"
      ]
    },
    {
      type: "li",
      props: null,
      children: [
        "Orange"
      ]
    },
    {
      type: "li",
      props: null,
      children: [
        "Banana"
      ]
    }
  ]
}
```
- `Virtual DOM` : DOM의 구조와 간결하게 흉내낸 자바스크립트 객체

### 5 Virtual DOM의 Three Steps

1. 데이터가 업데이트 되었을 때, 전체 UI가 Virtual DOM에서 리렌더링 된다.
1. 이전 DOM과 새로운 Virtual DOM의 차이가 계산된다.
1. 계산이 끝난 후, DOM은 변경된 부분만을 변화시킨다.

### 참조

- [DOM은 뭐고 가상 DOM은 뭔가요? (+ Svelte와 React의 차이)](https://www.youtube.com/watch?v=1ojA5mLWts8)
- [Virtual DOM, 가상 돔 이란?](https://doqtqu.tistory.com/316#toc-Virtual%20DOM%EC%9D%98%20Three%20Steps)

## 이벤트 버블링, 이벤트 캡쳐링

### 1 이벤트 흐름

1. Capture Phase
1. Target Phase
1. Bubbling Phase

![이벤트 흐름](https://user-images.githubusercontent.com/76744586/206496256-320a00ec-b8e3-407a-9349-1f58bd20198c.png)

[이미지 출처](https://www.youtube.com/watch?v=7gKtNC3b_S8)

### 2 이벤트 버블링
- 어떤 요소에 대한 이벤트가 발생했을 때, 해당 요소의 최상위 부모까지 이벤트가 전달되어지는 과정이다. HTML 구조상 자식 요소에 발생한 이벤트가 상위 부모요소에 영향을 미치는 것이다.
- 기본 값으로 설정되어 있음

### 3 이벤트 캡쳐링
- 이벤트 버블링과 정반대
- 최상위 부모에서 이벤트 발생 요소까지 이벤트 전달 되는 과정
- 캡처링 단계에서 이벤트를 잡아내려면 addEventListener의 capture 옵션을 true로 설정해야 합니다.

``` javascript
elem.addEventListener(..., {capture: true})
// 아니면, 아래 같이 {capture: true} 대신, true를 써줘도 됩니다.
elem.addEventListener(..., true)
```

capture 옵션은 두 가지 값을 가질 수 있습니다.
- false이면(default 값) 핸들러는 버블링 단계에서 동작합니다.
- true이면 핸들러는 캡처링 단계에서 동작합니다.

### 4 이벤트 버블링이나 캡처링을 차단하는 법
이벤트 버블링이나 캡처링을 차단 하고 싶을때 e.stopPropagation을 호출하면 이벤트 전파를 막고 해당 이벤트만 실행시킬 수 있다.

### 참조

- [김버그 Kimbug - DOM 이벤트 플로우 완벽하게 정리해드립니다.](https://www.youtube.com/watch?v=7gKtNC3b_S8)
- [버블링과 캡처링 - 모던 JavaScript 튜토리얼](https://ko.javascript.info/bubbling-and-capturing)

## URL 을 입력하고 벌어지는 일

### 1 용어 정리

- DNS (Domain Name System Servers)
  - '도메인 이름 시스템 서버'는 URL들의 이름과 IP주소를 저장하고 있는 데이터베이스로, 웹사이트를 위한 주소록이라고 생각하면 된다.
  - 숫자로 된 IP주소(ex. 63.245.217.105) 대신 사용자가 사용하기 편리하도록 주소를 매핑해주는 역할을 한다.
- TCP/IP (Transmission Control Protocol / Internet Protocol)
  - '전송제어규약'과 '인터넷규약'은 데이터가 어떻게 웹을 건너 여행하는지 정의하는 통신 규약이다.
  - 이를 사용하겠다는 것은, IP주소 체계를 따르며 TCP의 특성을 활용해 송신자와 수신자의 논리적 연결을 생성하고 신뢰성을 유지할 수 있도록 하겠다는 의미이다. 즉 송신자가 수신자에게 IP주소를 사용해서 데이터를 전달하고 그 데이터가 제대로 갔는지에 대해 이야기하는 것이다.
- HTTP (Hypertext Transfer Protocol)
  - 클라이언트와 서버가 서로 통신할 수 있게 하기 위한 언어를 정의하는 어플리케이션 규약으로, 쉽게 말해 요청과 응답으로 이루어져있어 "어떤 데이터 주세요"라고 요청하면, "이 데이터 줄게요" 라고 응답하는 것이라고 할 수 있다.
  - 주로 HTML문서를 주고 받는데에 사용된다.

### 2 웹 동작 방식

![웹 동작 방식](https://velog.velcdn.com/images%2Feassy%2Fpost%2F44f24431-5192-477d-b36c-87fc0b10a204%2Fimage.png)

예를 들어 www.google.com을 입력한다고 가정해 보았을 때, 어떻게 동작하는지 순서대로 나열해보자

1. 사용자가 웹브라우저 검색창에 www.google.com 입력
1. 사용자가 입력한 url 주소 중에서 **도메인 네임**을 **DNS 서버**에서 검색한다. 
1. DNS 서버에서 해당 도메인 네임에 해당하는 **IP주소를 찾아** 사용자가 입력한 **URL 정보와 함께 전달**함. 
1. 웹 페이지 URL + IP 주소는 HTTP 프로토콜을 사용하여 **HTTP 요청 메세지를 생성**한다 
1. HTTP 요청 메세지는 **TCP 프로토콜**을 사용하여 **인터넷을 거쳐 해당 IP 주소의 컴퓨터로 전송**된다 
1. 이렇게 도착한 HTTP 요청 메세지는 HTTP 프로토콜을 사용하여 **웹 페이지 URL 정보로 변환**된다. 
1. **웹 서버**는 도착한 웹 페이지 URL 정보에 해당하는 **데이터를 검색**한다 
1. 검색된 웹 페이지 데이터는 또다시 HTTP 프로토콜을 사용하여 **HTTP 응답 메세지를 생성**한다 
1. 이렇게 생성된 HTTP 응답 메세지는 TCP 프로토콜을 사용하여 **인터넷을 거쳐 원래 컴퓨터로 전달**된다. 
1. 도착한 HTTP 응답 메세지는 HTTP 프로토콜을 이용하여 **웹 페이지 데이터로 변환**되고, **웹 브라우저에 의해 출력**되어 사용자가 볼 수 있게 된다.

### 참고

- [[번역] Browser에 www.google.com을 검색하면 어떤 일이 일어날까?](https://devjin-blog.com/what-happen-browser-search/)
- [[면접준비] 프론트엔드 개발자 취업 면접 질문 및 답변 정리(64문)](https://amyhyemi.tistory.com/224)

## 브라우저의 동작 원리

### 1 렌더링 엔진의 동작 과정
1. `DOM, CSSOM 생성` : 가장 첫번째 단계로 서버로부터 받은 HTML, CSS를 다운받는다 <br>→ 단순한 텍스트인 HTML, CSS파일을 Object Model로 만든다. HTML은 DOM으로, CSS는 CSSOM으로 만들어진다. (html이 여기서 파싱된다) <br>DOM Tree와 CSSOM Tree가 만들어진다
1. `Render Tree생성` : DOM Tree와 CSSOM Tree가 만들어졌으면 그 다음으로는 이 둘을 이용하여 Render Tree를 생성한다. <br>렌더트리에는 스타일 정보가 설정되어있고, 실제 화면에 표현되는 노드들로 구성된다.
1. `Layout 단계` : 브라우저의 뷰포트(Viewport) 내에서 각 노드들의 정확한 위치와 크기를 계산한다.<br> 생성된 Render Tree 노드들이 가지고 있는 스타일과 속성에 따라서 브라우저 화면의 어느위치에 어느크기로 출력될지 계산하는 단계이다.(reflow 단계) <br>레이아웃 단계에서 %, vh, vw와 같이 상대적인 위치, 크기 속성은 실제 화면에 그려지는 픽셀 단위로 변환된다.
1. `Paint` : Layout 계산이 완료되면 이제 요소들을 실제 화면을 그리게 된다.(repaint 단계) <br>처리해야하는 스타일이 복잡할수록 paint 단계에 소요되는 시간이 길다. (가령 그라데이션, 그림자 효과 > 단색 배경)

### 2 UI가 업데이트되는 3가지 상황

1. 다시 Layout이 발생하는 경우
  - Javascript -> Style -> Layout -> Paint -> Composite
  - 주로 요소의 크기나 위치가 바뀔 때, 혹은 브라우저 창의 크기가 바뀌었을 때 다시 발생
1. Paint부터 다시 발생되는 경우
  - Javascript -> Style -> Paint -> Composite
  - 주로 배경 이미지나 텍스트 색상, 그림자 등 레이아웃의 수치를 변화시키지 않는 스타일의 변경이 일어났을 때 발생
  - Layout 과정이 발생하지 않기 때문에 성능적으로 이점을 가짐
1. 레이어의 합성만 다시 발생하는 경우
  - Javascript -> Style -> Composite
  - Layout과 Paint을 수행하지 않고 레이어의 합성만 발생하기 때문에 성능상으로 가장 큰 이점을 가짐

### CSS Triggers 사이트
CSS 속성마다 렌더링 엔진별로 UI가 어떻게 업데이트 되는지 알려주는 사이트<br>
https://csstriggers.com/

> Blink 는 크롬과 V8, <br>Gecko는 파이어폭스, <br>WebKit은 iOS 사파리, <br>EdgeHTML은 엣지브라우저 에서 쓰인다.

- 첫번째 칸은 기본값에서 변화될 때,
- 두번째 칸은 주기적으로 업데이트 될 때를 나타낸다.
- 최대한 composite만 일어나는게 좋다.
- Layout이 일어나면 성능 차원에서 좋지 않음.

### 참조

- [[10분 테코톡] 체프의 브라우저 렌더링](https://www.youtube.com/watch?v=sJ14cWjrNis)
- [[면접준비] 프론트엔드 개발자 취업 면접 질문 및 답변 정리(64문)](https://amyhyemi.tistory.com/224)

## Promise

### 1 Promise란?
콜백헬을 개선시키기 위해 등장한 패턴으로 Promise는 향후 언젠가 사용하게 될 값을 생산해내는 객체

### 2 Promise 3가지 상태
fullfiled, pending, rejected 상태가 있습니다.
- fullfiled : 비동기처리가 성공적으로 수행된 상태
- rejected : 비동기처리가 실패로 수행된 상태
- pending : 비동기처리가 아직 수행되지 않은 상태

## 호이스팅

> 호이스팅은 변수를 선언하고 초기화했을 때, 선언 부분이 최상단으로 끌어올려지는 현상을 말한다.

var의 경우 변수를 선언하고 초기화하는 과정이 동시에 일어나서 호이스팅이 발생한다. 

반면 let/const 의 경우 선언과 초기화 단계가 동시에 일어나지 않는다. 실행 시점에서 실제 선언부를 만날 때 초기화가 이뤄진다. 그 사이의 시간을 TDZ(Temporary Dead Zone)이라고 한다. 즉 실행 컨텍스트에 변수가 선언은 되었으나 메모리가 할당되지 않아 ReferenceError가 발생한다.  

함수 호이스팅은 선언문에서 발생한다. 선언된 함수는 상단에서 참조, 호출이 가능하다. 함수 표현식은 결국 변수에 할당하는 모습이라 변수 호이스팅의 사례로 볼 수 있다.

## 클로저

> 클로저란 외부함수가 반환된 후에도 외부함수의 변수 범위 체인에 접근할 수  있는 함수이다.

### 왜 사용하는지?
전역 변수의 사용을 억제하고, 정보를 은닉하기 위해 사용한다.

## this
This는 함수를 호출할 때 결정되는 것이다. 
- `전역범위`에서 사용될 때 this는 전역객체를 가르킨다. 
- `함수`에서 사용될때도 전역객체를 가르킨다. 
- `객체에 속한 메서드`에서 사용될때 그 메서드의 객체(점 앞에 명시된 객체)를 가르킨다. 
- `객체에 속한 메서드의 내부함수`에서 사용될땐 전역객체를 가르킨다. 
- `생성자`에서 사용될때 생성자로 인해 생성된 새로운 객체를 가리킨다.