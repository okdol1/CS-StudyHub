# React

- [React?](#react-1)
- [React.memo vs useMemo](#reactmemo-vs-usememo)
- [useMemo vs useCallback](#usememo-vs-usecallback)
- [Redux](#redux)

## React?
- Facebook에서 제공한 프론트엔드 라이브러리
- SPA(Single Page Application) 개발에 주로 쓰인다
- 사용자와의 상호작용을 쉽게 구현 할 수 있어 많이 사용됨
- 2022년 12월 기준 v.18.2.0 까지 나왔음

### 1 React의 특징
- One way Data Flow(단방향 데이터 플로우)
- Component 기반 구조
- Virtual DOM
- Props and State
- JSX

### 1.1 Compoenent란?
- 리액트로 만들어진 앱을 이루는 최소한의 단위
- 기존의 웹 프레임워크는 MVC방식으로 분리하여 관리하여 각 요소의 의존성이 높아 재활용이 어렵다는 단점이 있었다. 반면 컴포넌트는 MVC의 뷰를 독립적으로 구성하여 재사용을 할 수 있고 이를 통해 새로운 컴포넌트를 쉽게 만들 수 있다.

### 1.2 JSX란?
- JavaScript 코드에 있는 HTML 문법
- React 내에 HTML을 표현할 때 JSX를 사용한다
- Babel을 통해 자바스크립트로 변환된다 -> 자바스크립트 코드를 HTML처럼 표현할 수 있다

### 1.3 Props and State?
- props와 state 모두 리액트 컴포넌트에서 다루는 데이터로
- 그 중 props는 부모 컴포넌트에서 받아온 데이터이다. 리액트는 부모에서 자식으로만 데이터가 흐르는 **단방향 형식**으로, 이미 상속된 props는 수정이 불가능하다.
- 반면 state는 해당 컴포넌트 내부에서 선언되기때문에 **수정이 가능**하다는 차이점이 있다. setState를 사용하여 state값을 업데이트 해주면 자동으로 리렌더링되지만 주의할 점은 setState의 **비동기적 특성**을 기억하고 사용 해야한다. (setState가 있을 경우, 리액트는 다른 state변경까지 한꺼번에 통합해서 리액트 자신이 판단하기에 가장 적절한 시기에 돔을 리렌더링한다. 해결하기 위해 콜백 혹은 useEffect를 사용한다)

### 1.4 CRA란?
- 리액트를 사용하려면 webpack과 babel을 세팅해줘야 하는데, CRA를 사용하면 이런 설정들을 대신 해줌
- CRA를 사용하면 복잡하게 설정할 필요 없이 React 개발 환경을 셋업 할 수 있다

CRA에는 3가지 라이브러리가 포함되어 있음

- react → 리엑트 코어
- react-dom → 렌더링 담당
- react-scripts → CRA 라이브러리

## React.memo vs useMemo

### 1 공통점
- React.memo와 useMemo 모두 props가 변하지 않으면(이전 props와 동일하면) 인자로 넘긴 함수는 재실행되지 않고, 이전의 메모이즈된 결과를 반환한다는 점에서 공통점이 있다.

### 2 차이점
- React.memo는 HOC, useMemo는 hook이다.
- React.memo는 HOC이기 때문에 클래스형 컴포넌트, 함수형 컴포넌트 모두 사용 가능하지만, useMemo는 hook이기 때문에 오직 함수형 컴포넌트 안에서만 사용 가능하다.

## useMemo vs useCallback

### 1 공통점
- 둘다 **메모이제이션 훅**으로 연산 된 값을 자료구조에 저장 후 재사용시 계산을 반복하지않고 꺼내서 사용가능하게 해준다.
- 메모이제이션 훅은 리액트의 불필요한 랜더링(예: 자식 컴포넌트의 상태가 변경되지않아도 랜더링 해버리는 경우)을 방지하여 퍼포먼스 최적화에 사용된다.

### 2 차이점
- 둘의 차이점은 useCallback은 전달된 함수 그 자체를 캐싱하지만, useMemo는 전달된 함수가 실행되고 반환된 결과를 캐싱한다는 것이다.(=useMemo는 메모이제이션 된 값을 반환하고 useCallback은 메모이제이션 된 콜백을 반환한다.)

## Redux

### 1 Redux란?
redux는 state를 편리하게 관리하기 위한 상태관리 라이브러리입니다.

### 2 Redux 사용법
redux는 하나의 커다란 store라는 state가 존재하는데 여기서 전체 state를 총괄합니다. 이 state는 직접 변경할 수 없고 순수함수인 reducer로 변경하는 것이 가능합니다.
리듀소는 type과 payload를 속성으로 갖는 단순 객체인 action 이벤트가 발생했을 때 작동하고 action 이벤트는 dispatch라는 함수에 단순 객체인 action을 넣어 발생시킬 수 있습니다.