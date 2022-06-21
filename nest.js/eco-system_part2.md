## getStore()가 실행되는 과정 ✅

### `@Resolver`에서 getStore()가 호출됨
```ts
@Resolver() 
export class StoreResolver {
  constructor(private readonly storeService: StoreService) {}
  @Query(() => [Store])
  getStore(): Promise<Store[]> {
    return this.storeService.getAll();
  }
}
```
- `graphQL` `Query`가 클라이언트에서 요청되면, `@Resolver`의 `@Query`에서 해당 함수를 호출한다. 그리고 `getStore()` 함수는 해당 로직을 연결된 `Service`로부터 받는다.
- `Resolver`에서는 `Service`가 inject 되어있다. `constructor(private readonly storeService: StoreService) {}`


### `Inject`된 `Service`로 이동
```ts
@Injectable()
export class StoreService {
  constructor(
    @InjectRepository(Store) private readonly store: Repository<Store>,
  ) {}
  getAll(): Promise<Store[]> {
    return this.store.find();
  }
}
```
- `Service`에서는 `Resolver`로부터 호출된 함수(로직)를 실행한다. 다만, 이 요청은 DB와 커뮤니케이션이 있어야 하는 작업이다. 그래서 Service 내에서 `Repository`를 `Inject`해서 `Repository`를 통해 DB 커뮤니케이션을 하고 있다. (Repository pattern 적용)
- Repository는 Entity로부터 만들어진다. Entity는 DB Table의 모양과 같다. 왜냐하면 Entity로부터 Table이 생성되기 때문이다. `@InjectRepository(Store)`로 부터 Custom Repository가 생성되며, 타입은 `Repository<Store>`이다. 그리고 Service Class내에 inject된다. 그리고 `getAll()` 함수내에서 `this.store`를 통해 사용된다.

### 이 과정을 위한 module 등록
```ts
@Module({
  imports: [TypeOrmModule.forFeature([Store])], 
  providers: [StoreResolver, StoreService], 
})
```
- `imports`에서 `[TypeOrmModule.forFeature([Store])]`를 통해 Repository가 import 되어야하고, `Resolver`와 `Service`가 `providers`로써 등록되어야 한다.