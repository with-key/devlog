## Nest Layer 개념 정리 ✨

### Provider
  ```ts
  @Module({
    imports: [TypeOrmModule.forFeature([Store])],
    providers: [StoreResolver, StoreService], // << 이거
  })
  export class StoreModule {}
  ```
- Provider는 nest의 기본 개념이다. 대부분의 기본 nest 클래스는 서비스, 레포짓토리, 팩토리, 헬퍼 등이 Provider에 포함된다.
- Provider들을 다른 클래스에 주입하면서 서로 다양한 관계를 만들 수 있다. 가령, `Controller` 에서는 많은 클래스들을 필요로 한다. 예를 들어 Service가 필요한다고 했을 때, 이것을 `Controller` 내부에서 모두 구현 할 수 없다. 어딘가에 따로 정의가 되고 이것을 `Controller` 에서 사용 할 수 있도록 연결 시켜야 한다. 이때 서비스 클래스를 별도의 파일에서 구현하고, 그것을 `Controller`에 주입하여 연결시킨다. 
- `xxx.module.ts` 안에서 `providers`에 여러 `providers` 를 넣어 사용할 설정을 한다.

### Entity
- DB의 테이블로 변환이 되는 클래스(객체)다. 또한 이것이 Repository가 되기도 한다. `graphQL`에서는 schema 생성을 위한 `@ObjectType`과 모양기 같기 때문에 동시에 같은 역할을 하기도 한다.

### Controller
-  컨트롤러란, 들어오는 요청을 처리하고 클라이언트에 응답을 하는 역할을 하는 클래스(객체)다.
- Controller에서 별도의 로직수행을 하고자 한다면 Service Layer를 생성하고, 이 Service를 연결해서 사용하고자 한다면, `Controller class`에서 `dependency injection`을 해줘야 한다.
  ```ts
  // dependency injection

  @Controller('store')
  export class StoreController {
    storeService: StoreService;
    construtor(storeService: StoreService){
      this.storeService = storeService;
    }
  }

  // 또는 

  @Controller('store')
  export class StoreController {
    construtor(private storeService: StoreService) {}
  }
  ```



### Repository
- Repository는 Entity 객체와 함께 작동하며, Entity 찾기, 삽입, 업데이트, 삭제 등을 처리 한다.
- 원래 `Service`에서 하던 DB 관련 작업을 별도로 분리해서 처리하는 객체라고 보면 된다. 그리고 `Repository`를 별도로 생성해서 구성하는 것을 `Repository pattern` 이라고 한다.
- 생성한 `Repository`를 다른 곳에서도 사용하기 위해서는 module에서 import해줘야 한다.


### Service
- 소프트웨어 개발내의 공통 개념이다. 딱히 Nest.js, Js에서만 사용하는 개념이 아니다.
- **`@Injectable` 라는 데코레이터로 감싸져서 모듈에 제공되며, 이 서비스 인스턴스는 앱 전체에서 사용될 수 있다.** `@Injectable`이 `Service`를 다른 컴포넌트에서도 사용 할 수 있게 만들어준다.
  ```ts
  @Injectable()
  export class StoreService {
    getStore(): string {
      return 'hello store';
    }
  }
  ```
- 서비스는 컨트롤러에서 데이터의 유효성 체크를 하거나 데이터베이스에 아이템을 생성하는 등의 작업을 하는 부분을 처리한다. 
- 서비스는 `Controller`에서 처리하기 복잡한 것들을 처리하기 위한 역할을 하기 위해 존재한다. 보통 DB와의 커뮤니케이션이 이루어진다.
- Service를 다양한 컴포넌트에서 사용하고자 할때는 module.ts 의 Provider에 등록이 되어야 한다.


### DTO
```ts
@ArgsType()
export class CreateStoreDto {
  @Field(() => String)
  @IsString()
  @Length(5, 10)
  name: string;

  @Field(() => Boolean)
  @IsBoolean()
  isVegan: boolean;

  @Field(() => String)
  @IsString()
  address: string;

  @Field(() => String)
  @IsString()
  ownerName: string;
}
```
- 계층 간 데이터 교환을 위한 객체이다.
- DB 에서 데이터를 얻어 Service나 Controller 등으로 보낼 때 사용하는 객체를 말한다. 
- interface나 class를 이용해서 정의될 수 있으나, Nest에서는 class를 이용할 것을 추천하고 있다. **따라서 DTO자체가 Request parameter의 타입이 된다.**
  ```ts
  @Mutation(() => Boolean)
    createStore(@Args() newStoreInfo: CreateStoreDto): boolean {
      console.log(newStoreInfo);
      return true;
    }
  ```
- DTO를 사용하는 이유는 데이터의 유효성을 체크하는데 효율적이고, 더 안정적인 코드로 만들어주며 그 자체가 데이터의 타입이 되기도 한다. 보통 `class-validator`를 이용해서 유효성 검증을 한다.

