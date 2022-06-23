## CURD 만들기

### create
1. entity 구현
```ts
@InputType({ isAbstract: true })
@ObjectType()
@Entity()
export class Store {
  @Field(() => Number)
  @PrimaryGeneratedColumn()
  id: number;

  @Field(() => String)
  @Column()
  @IsString()
  @Length(5, 10)
  name: string;

  @Field(() => Boolean, { defaultValue: true }) // GQL에서 deault value가 있음을 설정
  @Column({ default: true }) // DB에서의 default Value가 있음을 설정
  @IsOptional() // DTO validation에서 Client에서 값을 필수로 보내지 않아도 되도록 설정해 줌
  @IsBoolean() // DTO validation에서 해당 값이 Boolean임을 검증
  isVegan: boolean; // TypeScript에서 해당 값의 형태가 Boolean임을 확인

  @Field(() => String, {
    defaultValue: '강남',
  })
  @Column()
  @IsString()
  address: string;

  @Field(() => String)
  @Column()
  @IsString()
  ownerName: string;

  @Field(() => String)
  @Column()
  @IsString()
  categoryName: string;
}
```
- InputType을 통해 DTO를 생성할 수 있다. (isAbstract: true)를 통해서 graphQL schema 생성을 방지한다.
- ObjectType을 통해 graphQL schema를 생성한다.
- Entity를 통해 PostgreSQL db에 테이블이 생성된다.

2. DTO 구현
```ts
@InputType()
export class CreateStoreDto extends OmitType(Store, ['id']) {}
```
- MappedType과 Entity를 이용해서 DTO를 생성했다.

3. Service 구현
```ts
@Injectable()
export class StoreService {
  construtor(
    @InjectableRepository(Store) private readonly store: Repository<Store>,
  ){}
    createStore(createStoreDto: CreateStoreDto): Promise<Store> {
    const newStore = this.store.create(createStoreDto);
    return this.store.save(newStore);
}
```
- Repository 패턴을 사용하기 위해 `@InjectableRepository`를 통해 Repository를 주입했다. Repository는 Entity로부터 생성되고, 타이핑은 `Repository<>`을 통해 생성한다.
- `createStore` 로직을 구현한다. `store` Repository를 통해 `create`, `save` 등을 사용 할 수 있다.

4. Resolver 구현
```ts
@Resolver() // Resolver란 마치 로직이다.
export class StoreResolver {
  constructor(private readonly storeService: StoreService) {}

  @Mutation(() => Boolean)
  async createStore(
    @Args('newStoreData') newStoreInfo: CreateStoreDto,
  ): Promise<boolean> {
    try {
      await this.storeService.createStore(newStoreInfo);
      return true;
    } catch (e) {
      console.log(e);
      return false;
    }
  }
}
```
- 클라이언트에서 요청되는 API가 명시되어 있는 Resolver를 구현한다.
- 비즈니스로직은 Service에 구현되어 있으므로, Service를 사용하기 위해 `private readonly storeService: StoreService`를 통해서 inject 한다.
- Rpository를 통해 사용하는 `create`와 같은 메서드는 `Promise`를 반환하는 것을 주의하자. 

5. Client 요청
```graphql
mutation {
  createStore(newStoreData: {
    name: "",
    ownerName:"",
  })
}
```
- 서버의 코드 구현은 모두 끝났으며, 생성한 Mutation을 클라이언트에서 요청한다.
- createStroe는 DTO가 `@InputType`으로 구현되었기 때문에 위와 같이 요청해야 한다.
