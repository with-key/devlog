# useRef

`useRef`는 말단 컴포넌트에 `ref={ref}`로 꽂을 때, 함수 형태로도 꽂을 수 있다. `ref={node => node}` 이때 node는 꽂힌 컴포넌트이며, 이 함수는 화면의 렌더링이 끝나면 실행된다. 그래서 node 값을 콘솔에 찍어보면 최초값이 `<input/>` 이다. 그리고 해당 컴포넌트가 사라지면 (unmount) null이 된다.

보통 `input`에 `focusing`을 하기 위해 `useEffect` 안에서 if를 통해 컴포넌트가 존재할 때 focus를 꽂는 방식을 많이 사용하는데, 이 패턴으로 구현하면 `useEffect`와 기타 종속값들이 필요가 없다.

```ts
import React from "react";

const Form = React.forwardRef((props, ref) => {
  const [show, setShow] = React.useState(false);

  return (
    <form>
      <button type="button" onClick={() => setShow((pre) => !pre)}>
        show
      </button>
      {show && (
        <input
          ref={(node) => {
            console.log(node);
            return node?.focus();
          }}
        />
      )}
    </form>
  );
});

Form.displayName = "Form";

export default function App() {
  const ref = React.useRef<HTMLFormElement>(null);

  // React.useEffect(() => {
  //   ref.current?.focus();
  // }, []);

  return <Form ref={ref} />;
}
```
