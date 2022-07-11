# Create
## 공통
### DTO
```ts
```
### PIPE
```ts

```
### Entity
```ts
@Entity()
export class Board extends BaseEntity {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @Column()
  description: string;

  @Column()
  status: BoardStatus;
}
```
- `@Entity()` 데코레이터를 붙인다.
- `BaseEntity`로 부터 extends 한다.
- `@PrimaryGeneratedColumn()`가 필요하다. PrimaryGeneratedColumn는 Table에서 고유한 key의 컬럼이다.
- 



## controller
```ts
@Post()
@UsePipes(ValidationPipe)
createBoard(@Body() createBoard: CreateBoardDto): Promise<Board> {
  return this.boardsService.createBoard(createBoard);
}
```
- `@UsePipes(ValidationPipe)`는 handler level의 pipe validation 이다.
- 