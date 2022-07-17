## Resolver
Resolver는 graphQL에서 사용되는 개념이며, `Controller`와 비슷한 역할을 하는 클래스이다. 코드를 보면 `@Query()`, `@Mutation()` 데코레이터들이 있는데, Rest API의 `@Get()`, `@Post` 와 대칭되는 기능이라고 보면 된다.

그래서 `Resolver`에서는 `Service`를 `DI`하고, `Service`의 로직을 사용하여 클라이언트와 소통할 여러 메서드를 작성한다. 그리고 여기에서 작성된 `API` 메서드들이 `Apollo Server`의 `Doc`에서 보여진다.

### @Query()
```ts
@Resolver() // Resolver란 마치 로직이다.
export class StoreResolver {
  constructor(private readonly storeService: StoreService) {}

  @Query(() => [Store]) // <- Query 요청에 대한 타입을 명시
  getStore(): Promise<Store[]> {
    return []
  }

  // Argumnent는 `@Args('variableName')`를 통해 받을 수 있다.
  @Query(() => [Store]) 
  getStore(@Args('variableName'): variableName: boolean): Promise<Store[]> {
    return [variableName]
  }
}
```

### modeule 설정
```ts
import { Module } from '@nestjs/common';
import { StoreResolver } from './store.resolver';
import { StoreService } from './store.service';

@Module({
  // Resolver와 Service가 providers에 설정된다.
  providers: [StoreResolver, StoreService], 
})
export class StoreModule {}
```

