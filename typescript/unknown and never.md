### unKnown
값의 타입을 미리 알 수 없을 때 사용한다. 다만, unKnown으로 타이핑된 값을 사용하려면 **반드시 해당 값의 타입이 무엇인지 확인**하고 사용해야 한다. 그리고 타입이 확인된 스코프 안에서는 확인된 타입으로 간주되어 값을 사용할 수 있다.

```ts
let a: unknown;

if (typeof a === 'number'){
  let b = a + 1 // a의 타입은 number이다.
}

if (typeof a === 'string'){
  let b = a.toUpperCase(); // a의 타입은 string이다.
}
```
### never
함수가 절대 return 하지 않을 때 발생하는 타입이다. 즉 정상적인 함수라면 never가 아닌 타입으로 모두 실행되고, 그 외 발생해서는 안되는 결과의 return은 never다.
```ts
function hello(name: string | number){
  if(typeof name === 'string' ){
    return name // type === 'string'
  } else if(typeof name === 'number'){
    return name  // type === 'number'
  } else {
    return name // type === 'never'
  }
}
```
정상적인 코드라면, name이 number 또는 string 일 것이다. 그래서 첫번째, 두번째 조건문에 의해서 모두 실행이 될 것이다. 만약 파라미터가 들어왔을 때 never 타입으로 조건문이 실행된다면, 잘못된 파라미터가 온 것이고 이때 여기서 `new throw Error()` 등을 이용해서 예외 처리를 해주는 것이다.