## Class DI 패턴

부양가족 정보만을 가진 클래스가 있다. 이 model 클래스를 바탕으로 여러 정보로 가공해주는 service 컨트롤러에 의존성을 주입해서 사용하고자 한다.

```ts
export class FamilyModel {
  family = {
    "00": "본인" as const,
    "01": "소득자의 직계존속" as const,
    "02": "배우자의 직계존속" as const,
    "03": "배우자" as const,
  };
}
```
다양한 처리를 할 수 있는 컨트롤러 클래스를 구현하다. constructor의 인자로 클래스 자체를 받는다. 그리고 그 클래스를 컨트롤러 클래스 내부에서 인스턴스화 한다. 이렇게 클래스 자체를 주입하고, 주입된 클래스가 내부에서 인스턴스화 하도록 타입을 구성한다.

```ts
// 컨트롤러 클래스 타입
type FamilyControllerType = {
  new (): FamilyModel; // 이렇게 타입을 설정 할 수 있다.
};

export class FamilyController {
  constructor(private _family: FamilyControllerType) {}

 // DI로 주입된 클래스를 배열로 code와 label key를 가진 배열로 변환해주는 함수
  getFamilies() {
    const family = new this._family().family;
    return Object.entries(family).map((value) => ({
      code: value[0],
      label: value[1],
    }));
  }
}
```
`getFamilies()` 의 결과는 아래와 같다.
```ts
(4) [{…}, {…}, {…}, {…}]

0: {code: '00', label: '본인'}
1: {code: '01', label: '소득자의 직계존속'}
2: {code: '02', label: '배우자의 직계존속'}
3: {code: '03', label: '배우자'}
```