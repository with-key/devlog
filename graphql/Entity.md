## Entity

graphQL에서 Entity는 두가지 역할을 가지고 있다. 첫번째는 **Schema로의 변환**, 두번째는 **Table 생성을 위한 명세서**의 역할을 한다.

**첫번째 역할을 하기 위해서는 graphQl에서 자동으로 스키마를 빌드하기 위해 사용하는 @ObjectType() 데코레이터**를 사용해야 한다. `nestjs/graphpl`를 사용한다.
**두번째 역할을 하기 위해서는 typeORM에서 지원하는 @Entity() 데코레이터**를 사용해야 한다. `typeorm`를 사용한다.

```ts
@ObjectType() // graphQL
@Entity() // typeORM
export class Store {
  @Field(() => Number)
  @PrimaryGeneratedColumn()
  id: number;

  @Field(_ => String)
  @Column()
  name: string
}
```