# 8장 jsx에서 tsx로

## ReactElement & JSX.Element & ReactNode의 차이는?
- ReactElement 타입은 리액트 컴포넌트를 객체 형태로 저장하기 위한 포맷
- JSX.Element는 ReactElement의 제네릭, props와 타입필드에 대해 any타입을 가지도록 확장됌
- ReactNode는 가장 큰 범위의 형태. ReactElement외에도 boolean, string,number 등의 여러 타입을 포함하고 있다.

## 사용예시?
- JSX.Element는 타입 필드가 any이기에 ReactElement를 활용하면 타입 추론하기가 더 좋음!

## DetailedHTMLProps, ComponentPropsWithoutRef차이
- DetailedHTMLProps:   
 특정 HTML 요소의 모든 속성(기본 HTML 속성 및 React 추가 속성)을 포함하는 타입을 정의하는 데 사용된다. 이를 통해 특정 HTML 요소의 타입 안전성을 보장할 수 있음.
- ComponentPropsWithoutRef:   
ref 속성을 포함하지 않는 컴포넌트의 타입을 정의할 때 사용된다. 이는 주로 forwardRef를 사용하지 않는 컴포넌트에 적합하다.

### 합성이벤트란?
리액트는 브라우저의 기본 이벤트를 감싸는 합성 이벤트를 제공한다. 이는 브라우저 간 일관된 이벤트 객체를 제공하기 위함
```ts
const eventHandler1: GlobalEventHandlers["onchange"] = (e) => {
  e.target; // 일반 Event는 target이 없음
};

const eventHandler2: ChangeEventHandler = (e) => {
  e.target; // 리액트 이벤트(합성 이벤트)는 target이 있음
};
```

### 공변성, 반공변성 => 원한다로 기억할 것!

### Q1. React.FC를 더이상 사용하지 않는 이유? 
<details>
   <summary>정답</summary>
   - 리액트 v18로 넘어오면서 React.FC에서 children이 사라졌기 때문에
   <br/>
   - React.FC 대신 props 타입, 반환타입을 직접 지정해야한다.
</details>

### Q2. ComponentPropsWithoutRef는 어디에 쓰이는가? 
<details>
   <summary>정답</summary>
   - ref 속성을 포함하지 않는 타입을 정의할 때
</details>

#
# 9장 훅

### 훅이 나타나게 된 배경:
- 컴포넌트 간 상태 로직을 재사용하기 어렵다.
- 생명주기 메서드에서 서로 관련 없는 로직들이 얽혀 코드의 복잡성을 증가시키는 문제
- componentDidMount, componentDidUpdate와 같은 생명주기 메서드에서만 상태 업데이트에 대한 사이드 이펙트를 처리할 수 있었다.
- 프로젝트 규모가 커지면, 상태를 스토어에 연결하거나 비슷한 로직을 가진 상태 업데이트 및 사이드 이펙트 처리가 불편했다.

### useLayoutEffect이란?
- useEffect는 componentDidUpdate와 같은 기존 생명주기 함수와는 다르게, 레이아웃 배치와 화면 렌더링이 모두 완료된 후에 실행된다.
- 그에 반해 useLayoutEffect는 화면에 해당 컴포넌트가 그려지기 전에 콜백 함수를 실행함!

```ts
// 예시
useEffect(() => {
  // 매우 긴 시간이 흐른 뒤 아래 setName()을 실행한다고 가정
  setName("배달이");
}, []);
// 비어있는 상태로 오래 유지됌
return <div>{`안녕하세요, ${name}님!`}</div>;

useLayoutEffect(() => {
  // 매우 긴 시간이 흐른 뒤 아래 setName()을 실행한다고 가정
  setName("배달이");
}, []);
// 바로 배달이가 나타남!
return <div>{`안녕하세요, ${name}님!`}</div>;
```

## useRef

```ts
import { RefObject, useRef } from "react";

const Component = () => {
  const ref = useRef<HTMLInputElement>(null);
  return <MyInput ref={ref} />;
};

interface Props {
  ref: RefObject<HTMLInputElement>;
}

const MyInput = ({ ref }: Props) => {
  return <input ref={ref} />;
};

//  RunTime WARNING : MyInput: `ref` is not a prop.
// Trying to access it will result in `undefined` being returned.
// If you need to access the same value within the child component, you should pass it as a different prop.
// (https://reactjs.org/link/special-props)
```
- 에러가 발생, ref 예약어!
```ts
// 다른 이름을 사용하자!
import { forwardRef, useRef } from "react";

const Component = () => {
  const ref = useRef<HTMLInputElement>(null);
  return <MyInput ref={ref} name="인풋이름" />; // 아래 Props에서 정의한 필수 속성 name
};

// 꼭 받을 속성을 추가로 정의하기 위한 타입 정의
interface Props {
  name: string;
}

const MyInput = forwardRef<HTMLInputElement, Props>((props, ref) => {
  // forwardRef의 두 번째 인자에 ref를 넣어 자식 컴포넌트로 ref를 전달
  // forwardRef의 타입 정의로 인해 부모 컴포넌트에서 ref를 어떻게 선언했는지와 관계없이 자식 컴포너트가 해당 ref를 수용할 수 있다.
  return (
    <div>
      <label>{props.name}</label>
      <input ref={ref} />
    </div>
  );
});
```

### Q. useRef로 관리되는 변수는 값을 설정한 후 즉시 조회할 수 있다(O/X) 
<details>
   <summary>정답</summary>
   - useRef로 관리되는 변수는 값을 설정한 후 즉시 조회할 수 있다.
   <br />
   - 리액트 컴포넌트의 상태가 상태 변경 함수를 호출하고 렌더링 이후에 업데이트된 상태를 조회할 수 있는 점과 상반된다.
</details>


## React Hook을 사용하는데 있어서의 규칙 
- 훅은 항상 최상위 레벨에서 호출되어야 한다.
- 조건문, 반복문, 중첩 함수, 클래스 등의 내부에서는 훅을 호출하지 않아야 한다.
- 반환문으로 함수 컴포넌트가 종료되거나, 조건문 또는 변수에 따라 반복문 등으로 훅의 호출 여부가 결정되어서는 안된다.(useState, useEffect가 여러 번 호출되더라고 훅의 상태를 올바르게 유지하기 위함)
- 항상 함수 컴포넌트나 커스텀 훅 등의 리액트 컴포넌트 내에서만 호출되어야 한다.

#
# 10장 상태관리

- 지역 상태: 컴포넌트 내부에서 사용되는 상태, useState 훅을 많이 사용하며 경우에 따라서 useReducer 같은 훅을 사용하기도 함. ex) 체크박스의 체크여부, 폼의 입력 값
- 전역 상태: 앱 전체에서 공유하는 상태, 상태가 변경되면 전역상태를 공유하는 컴포넌트들도 업데이트 된다. 또한, prop drilling 문제를 피하기 위해 지역 상태를 해당 컴포넌트들 사이의 전역 상태로 공유할 수 있다.
- 서버 상태: 사용자 정보, 글 목록 등 외부 서버에 저장해야하는 상태, 지역, 전역 변수와 동일한 방법으로 관리되며 최근 react-query, SWR같은 외부 라이브러리를 사용하여 관리하기도 한다.


### Q. ContextAPI가 있음에도 상태관리 라이브러리를 사용하는 이유는? 
<details>
   <summary>정답</summary>
   - 컨텍스트 프로바이더의 prop로 주입된 값이나 참조가 변경될 때마다 해당 컨텍스트를 구독하고 있는 모든 컴포넌트가 리렌더링되기 때문
   <br />
   - 진짜 그럴까??
</details>

```ts
// Recoil 예제
// 제일 편리함!
import React from 'react';
import {
  RecoilRoot,
  atom,
  selector,
  useRecoilState,
  useRecoilValue,
} from 'recoil';

// 최상단의 App root 컴포넌트에 RecoilRoot를 배치해줍니다.
function App() {
  return (
    <RecoilRoot>
      <CharacterCounter />
    </RecoilRoot>
  );
}


// 여기서부터 CharacterCounter :)

const textState = atom({
  key: 'textState', // unique ID (with respect to other atoms/selectors)
  default: '', // default value (aka initial value)
});

function CharacterCounter() {
  return (
    <div>
      <TextInput />
      <CharacterCount />
    </div>
  );
}

function TextInput() {
  const [text, setText] = useRecoilState(textState);

  const onChange = (event) => {
    setText(event.target.value);
  };

  return (
    <div>
      <input type="text" value={text} onChange={onChange} />
      <br />
      Echo: {text}
    </div>
  );
}
```



# 11장 CSS-in-JS
### 개인적으로 StyledComponent는 불편했다...

# 12장 타입스크립트 프로젝트 관리

### 모노레포
- 버전관리 시스템에서 여러 프로젝트를 하나의 레포지토리로 통합하여 관리하는 소프트웨어 개발 전략

### Q. 모노레포를 사용하는 장점은?
<details>
   <summary>정답</summary>
   - 작은 프로젝트에서도 번들러, 테스트, Lint, CI/CD 스크립트나 도커 등 다양한 설정이 적용되는데, 이런 개발 환경 설정을 통합할 수 있어서 더 효율적인 관리가 가능하다.
</details>

### Q. 모노레포를 사용하는 단점은?
<details>
   <summary>정답</summary>
   - 사이즈가 관리하기 어려울 정도로 커질 수동 있다. 팀의 이해관계, 소유권, 권한관리에 대한 부분이 복잡해질 수 있다.
</details>


# 13장 객체지향
- MVC 구조: 실제 개발중인 프로젝트 이와 같이 분리해보기
