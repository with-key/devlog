## DTO와 Entity
Entity와 DTO는 그 형태가 유사하다. Entity는 DB의 테이블 모양이고, DTO는 클라이언트와 서버가 데이터를 주고 받는 양식이기 때문이다. 그래서 보통 Entity에서 Colunm이 추가되면 DTO에서도 같이 추가해줘야 한다. 하지만 그러한 상황이 발생할 때마다 하드 코딩 해주는 건 여간 번거로운 일이 아니다. 그래서 Mapped Type을 이용해서 Entity로부터 DTO를 자동으로 생성하는 것을 구현하고자 한다.

###  Entity로 DTO 생성하기
OmitType을 이용해서 CreateStoreDto를 생성했다.
  ```ts
  @InputType()
  export class CreateStoreDto extends OmitType(Store, ["id"], InputType) {}
  ```
  InputType이라고 명시해주는 이유는 MappedType를 InputType에서 작동하기 때문이다. ObjectType에서는 MappedType이 작동하지 않는다. 그래서 OmitType의 세번째 인자 자리에 변경하고자 하는 타입을 넣어주는 것이다. Store는 ObjectType으로 생성되었지만, OmitType을 사용할 때는 InputType으로 변경해주는 것이다. 세번째 자리에 타입 데코레이터를 명시하지 않으면, child class(Omit으로 생성되는 클래스)는 parent class(Store)와 같은 데코레이터를 사용한다.

  Child class의 type을 변경하는 방식은 두가지이다. 위 방식대로 해도 되고, 아니면 entity에서 아래 처럼 데코레이터를 넣어서도 가능하다.
  ```ts
  @InputType({ isAbstract: true }) // child class type 변경
  @ObjectType() 
  @Entity()
  export class Store {
    @Field(() => Number)
    @PrimaryGeneratedColumn()
    id: number;

    @Field(() => String)
    @Column()
    name: string;

    @Field(() => Boolean)
    @Column()
    isVegan: boolean;

    @Field(() => String)
    @Column()
    address: string;

    @Field(() => String)
    @Column()
    ownerName: string;

    @Field(() => String)
    @Column()
    categoryName: string;
  }
  ```
  `isAbstract: true`라는 설정이 필요한 이유는 `Schema must contain uniquely named types but contains multiple types name "Store"` 라는 에러가 발생하기 때문이다. `CodeFirst` 설정에 따라 Entity가 스키마로 변환이 되는데, 이때 `@ObjectType`에서 한번, `@InputType`에서 한번 변환을 시도한다. 이때 같은 이름으로 스키마 변환을 하려고 하기 때문에 에러가 난다. 이것을 방지하기 위해 `isAbstract: true`로 해주면 이 `@InputType`이 스키마 변환 대상에 포함이 되지 않게 막는다.  그리고 이렇게 생성된 DTO에서도 당연히 class-validator를 이용해서 유효성 검사를 할 수 있다.

  ### Entity의 용도
  이렇게 함으로써 Entity는 총 3개의 형태로 변환이 가능해진다.
  1. @ObjectType으로 graphql Schema로 변환
  2. @InputType으로 DTO로 변환
  3. @Entity로 DB의 테이블로 변환
