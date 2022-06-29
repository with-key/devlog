# NotFoundException
클라이언트에서 조회한 어떤 정보가 없을 때 사용할 수 있는 Next.js built-in Class이다.

서비스 로직에서 아래 코드처럼 분기를 해서 조건에 해당하면 throw 한다. 

```ts
 getBoardById(id: string) {
    const found = this.boards.find((board) => board.id === id);

    if (!found) {
      throw new NotFoundException('board를 찾을 수 없습니다');
    }

    return found;
  }
```

## 연계
만약 사용자가 존재하지 않는 board를 지우려고 request 했다면, getBoardById를 활용해서 Exception 처리를 할 수 있다. 먼저 this.getBoardById를 먼저 처리해주고, filter를 이용해서 삭제 로직을 진행한다.

```ts
 deleteBoard(id: string): boolean {
    const found = this.getBoardById(id);
    this.boards = this.boards.filter((board) => board.id !== found.id);
    return true;
  }
```