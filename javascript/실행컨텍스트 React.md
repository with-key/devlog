# 예시코드
```jsx
const p = new Promise((res, rej) => {
  res("프로미스");
});

function App() {
  const [n, setN] = useState(1);
  console.log("App 내부 맨위", n); // 1

  useEffect(() => {
    console.log(n, "첫번째 이펙트");
    return () => console.log(n, "클린업");
  }, [n]);

  useEffect(() => {
    console.log(n, "두번째 이펙트");
    return () => console.log(n, "클린업");
  }, [n]);

  const f = () => {
    console.log("함수 시작");

    setTimeout(() => {
      console.log(n, "셋타임아웃");
    }, 0);

    setN(n + 1);
    console.log("함수 중간");
    setN(n + 1);
    setN(n + 1);
    setN(n + 1);

    console.log("함수 끝");

    p.then((res) => console.log("프로미스", res));
  };

  console.log("App 내부 맨 아래", n);

  return (
    <button
      onClick={() => {
        console.log("이벤트 핸들러 맨 위", n);
        setN(n + 1);
        console.log("이벤트 핸들러 중간", n);
        f();
        console.log("이벤트 핸들러 맨 아래", n);
      }}
    >
      버튼
    </button>
  );
}
```

1.우선 App 내부에 있는 동기 코드가 모두 실행된다. 그리고 나서 useEffect가 실행된다. useEffect가 여러개가 있다면, 위에서부터 순차적으로 실행된다. 그리고 effect 함수 내에 setTimeout은 useEffect의 순서와 상관없이 가장 나중에 실행된다. 
> useEffect의 effect 함수가 동기코드보다 나중에 실행되는 이유?

> **setTimeout이 가장 마지막에 실행되는 이유?**
> setTimeout은 비동기로 동작한다. 그래서 동기코드가 모두 실행되고 콜스택이 비었을 때, 이벤트루프에 의해 콜스택으로 setTimeout의 콜백함수가 올라오고, 그제서야 실행이 되기때문에 실행순서가 가장 늦다.

2. 이 상태에서 클릭 이벤트 핸들러를 실행시키면, 이벤트 핸들러 내부의 동기코드가 모두 먼저 실행된다. useState의 stter 함수는 호출되면 리렌더링을 하게 되는데, 컴포넌트 리렌더링 작업은 큐로 이동한다. 그래서 바로 화면의 리렌더링이 이루어지지는 않는다. 아무튼 `f()`를 만나면 `f()`로 이동한다. 그리고 `f`함수의 동기 코드가 모두 실행된다. 마찬가지로 `setTimeout`가 호출되면 콜백함수는 큐로, `setter`가 호출되면 컴포넌트의 리렌더링도 큐로 이동한다. `p.then` 도 Promise 비동기 코드 이므로 큐로 이동한다. `f()` 함수 내에서 모든 동기 코드가 실행되었다면, 큐로 이동한 `컴포넌트 리렌더링`, `setTimeout callback`, `Promise.then callback` 이 각각 콜스택으로 올라온다. 이때 가장 먼저 올라오는 것은 `컴포넌트 리렌더링`이다. 그리고나서 화면의 리렌더링이 발생한다. 그러면 App 컴포넌트 자체가 다시 재호출된다.
   > 🤔 리액트 공식문서 :  `setState 함수는 state를 갱신할 때 사용합니다. 새 state 값을 받아 컴포넌트 리렌더링을 큐에 등록합니다.`
   
3. App 컴포넌트가 재호출되므로, App 컴포넌트 내부의 동기코드가 다시 실행되는데, 이때 setter에 의해서 새 값을 받은 state로 코드가 실행된다. 여기서 첫번째 사이클에는 없던 새로운 동작이 있는데, 바로 useEffect의 클린업 함수의 호출이다. 두번째 사이클의 useEffect effect 함수가 실행 되기 직전에 클린업 함수가 실행되고, 이후에 effect 함수가 실행된다. 

4. App 컴포넌트 리렌더링에 의한 동기코드의 실행이 모두 종료되면, 다시 큐에 있던 명령들을 가져온다. 현재 큐에 남은건 `Promise.then callback`과 `setTimeout callback`인데 `setTimeout callback`은 `매크로 테스크큐`에 있기 때문에 `Promise.then callback`이 더 우선순위를 가진다.**(마이크로 테스크큐가 매크로테스크큐보다 우선적으로 콜스택으로 올라감)** 그래서 `Promise.then`이 먼저 콜스택으로 올라와서 실행이 되고, 이후에 `setTimeout callback`이 실행된다. 이때 `setTimeout callback`이 가지고 있는 state 값은 당연히 이전 사이클의 값일 것이다. 호출은 이전 사이클에 됐으나 중간중간 우선순위에 계속 밀려 지금에서야 실행이 됐기 때문이다. 그리고 마지막으로 `useEffect` 안에 있던 `setTimeout callback`이 실행이 되면서 큐와 콜스택이 모두 비게 되면서 모든 실행이 종료된다.

  ```md
  --- 컴포넌트 최초 렌더링 👇 ---
  App.tsx:12 App 내부 맨위 1
  App.tsx:45 App 내부 맨 아래 1
  App.tsx:15 1 '첫번째 이펙트'
  App.tsx:23 1 '두번째 이펙트'
  App.tsx:17 1 '이펙트 안에서 셋타임 아웃'
  --- 이벤트 핸들러 실행 👇 ---
  App.tsx:50 이벤트 핸들러 맨 위 1
  App.tsx:52 이벤트 핸들러 중간 1
  App.tsx:28 함수 시작
  App.tsx:35 함수 중간
  App.tsx:40 함수 끝
  App.tsx:54 이벤트 핸들러 맨 아래 1
  --- 👆 이벤트 핸들러 내 동기코드 모두 실행 ---
  --- 큐에 대기중인 코드 실행 👇 --- 
  --- 보이진 않지만, setter의 컴포넌트 리렌더링이 콜스택에서 실행됨, 그로 인해 App 컴포넌트 재호출 👇 ---
  App.tsx:12 App 내부 맨위 2
  App.tsx:45 App 내부 맨 아래 2
  App.tsx:19 1 '클린업'
  App.tsx:24 1 '클린업'
  App.tsx:15 2 '첫번째 이펙트'
  App.tsx:23 2 '두번째 이펙트'
  --- 👆 두번째 App 컴포넌트 렌더링 후 동기코드 모두 실행 ---
  --- 큐에 남은 비동기실행등 👇 ---
  App.tsx:42 프로미스 프로미스
  App.tsx:31 1 '셋타임아웃'
  App.tsx:17 2 '이펙트 안에서 셋타임 아웃'
  --- 큐, 콜스택 모두 비워짐 👇 ---
  ```