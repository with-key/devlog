# 실행 컨텍스트 
- 코드를 실행하는데 필요한 배경이 되는 조건 / 환경
- 동일한 조건, 환경을 지니는 코드 뭉치 (= 함수)
- js는 오직 함수에 의해서만 실행컨텍스트를 분리할 수 있다. 즉, 함수를 실행할 때 필요한 조건, 환경정보를 담은 객체이다.
- 코드 실행에 관여하는 스택을 콜스택이라고 한다.

## 환경정보 종류
- VariableEnvironment
  - 식별자 정보 수집
  - 컨텍스트 내부 코드를 실행하는 동안에 변화가 생겨도 변경되지 않는다.
  - environmentRecord(snap shot)
  - outerEnvironmentReference(snap shot)
- LexicalEnvironment
  - 각 식별자의 데이터 추적
  - 컨텍스트 내부 코드를 실행하는 동안에 변화가 생기면 변경된다.
  - environmentRecord
  - outerEnvironmentReference
- ThisBinding

### 참고사항
 1. `VariableEnvironment`와 `LexicalEnvironment`의 차이는 값의 변화가 실시간으로 반영되느냐 그렇지 않느냐의 차이만 있다.
 2. `LexicalEnvironment` 위주로 살펴보아도 된다.

### LexicalEnvironment
> 어휘적/사전적 환경
1. 내부식별자, 외부정보 등이 담겨 있다.
2. 실행컨텍스트를 구성하는 환경 정보들을 모아 사전처럼 구성한 객체
3. `environmentRecord` = 현재 컨텍스트 내부의 식별자 정보 
4. `outerEnvironmentReference` = 외부 환경을 참조하는 정보

### environmentRecord
현재 컨텍스트의 식별자 정보들을 수집해서 environmentRecord에 담는 과정 = 호이스팅

```js
console.log(a()) // function a(){..}
console.log(b()) // undefined
console.log(c()) // undefined

function a(){
  return 1
}

var b = function dd(){
  return 2
}

c = function (){
  return 3
}

// 호이스팅이 일어나면, 
function a(){
  return 1
}

var b;
var c;

// environmentRecord 가 생성된다.
/**
 * {
 *  function a(){..},
 *  b: undefined,
 *  c: undefined 
 * }
 */

```
### outerEnvironmentReference
현재 문맥과 관련 있는 외부 환경 정보

```txt
inner
- LexicalEnvironment
  - environmentRecord
  - outerEnvironmentReference
outter
- LexicalEnvironment
  - environmentRecord
  - outerEnvironmentReferenceA
```
> `inner`의 `outerEnvironmentReference`는 outter의 `LexicalEnvironment`이다.

- 스코프체인은 outerEnvironmentReference에 의해서 만들어진다. outerEnvironmentReference에 의해서 외부로는 나갈 수 있는데, 안으로는 들어갈 수 없다. 이것이 곧 스코프체인이다.
  어떤 값이 inner에 없다면, outerEnvironmentReference를 통해서 외부 환경에서 변수를 찾으려고하고 또 없다면, 해당 outter에서 outerEnvironmentReference를 통해서 또 외부 환경을 참조하려는 과정, 스코프체인이다. 그리고 가장 먼저 찾은 변수를 사용한다. 