# @Unique(['columnName'])

- Entity에서 특정 컬럼의 값을 고유값으로 저장되게 할 때 사용하는 데코레이터이다.
- `@Unique(['name'])`으로 지정하면, name은 중복된 값이 db에 저장될 수 없다.
  - 만약 중복된 값이 Request로 들어오면, 500 error을 반환한다. 왜냐하면 이 에러는 Nest의 에러인데, 이것을 catch로 잡아주지 않으면 이 에러가 컨트롤러 레벨로 가서 500 에러를 반환해버리기 때문이다. 이 에러가 처음 발생하는 레포짓토리 레이어의 `save` 부분쪽에서 catch로 잡아주고, 분기처리한다.
```ts
async createUser(authCredentialDto: AuthCredentialDto): Promise<void> {
    const { username, password } = authCredentialDto;
    const user = this.create({ username, password });

    try {
      await this.save(user);
    } catch (error) {
      console.log(error);
    }
  }
```