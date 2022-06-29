# pipe
- pipe는 `@Injectable`이 달린 클래스이다.
- data transformation과 data validation을 위해 사용된다.
- pipe는 컨트롤러 경로 처리기에 의해 처리되는 인수에 대해 작동한다.
- Nest는 메소드가 호출되기 직전에 pipe를 삽입하고 pipe는 메소드로 향하는 인수를 수신하고 이에 대해 작동한다.

## data transformation
입력 데이터를 원하는 형식으로 변환 하는 것, 클라이언트에서 온 데이터의 타입이 올바르지 않으면 파이프에서 변환해버린다.

## data validation
클라이언트에서 온 데이터가 유효한 데이터 인지 아닌지 확인하는 과정
 
### validator
- 클라이언트에서 정보가 들어올 때 해당 데이터들의 유효성을 검사해야 한다. 

## class-validator

## 파이프 사용하는 법
1. handler level type: 특정 핸들러에 데코레이터를 붙여서 파이프를 적용하는 방식
2. parameter level type: 핸들러 안에 있는 파라미터를 기준으로 파이프를 적용하는 방식
3. global level type: 클라이언트에서 들어오는 모든 요청에 대해 파이프를 적용한다. (main.ts에서 설정)

## Built-in Pips
커스텀도 가능하지만, 기본적으로 바로 사용할 수 있는 내장 파이브들이 있다.