# custom pipe
내장된 pipe외에 커스텀 파이프를 생성할 수 있다. 보통 pipes 폴더를 생성하고 그 안에 모아둔다.

```ts
import { BadRequestException, PipeTransform } from '@nestjs/common';
import { BoardStatus } from '../boards.model';

// 클라이언트로부터 받은 "status" 값이 "private", "public"이 아니면 Error를 응답하도록 validation
export class BoardStatusValidationPipe implements PipeTransform { // implements PipeTransform 해야함
  readonly statusOptions = [BoardStatus.PRIVATE, BoardStatus.PUBLIC];

  // transform(value, metadata){} 가 있어야 함
  transform(value: any) {
    // console.log('value', value); // 처리가 된 인자의 값
    // console.log('metadata', metadata); // 인자에 대한 정보값이다.

    value = value.toUpperCase();

    if (!this.isStatusValid(value)) {
      throw new BadRequestException(`${value} 는 올바른 값이 아닙니다.`);
    }
  
    return value; // return 된 값은 핸들러로 전달된다.
  }

  // validation에 필요한 함수로직
  private isStatusValid(value): boolean {
    const index = this.statusOptions.indexOf(value);
    return index !== -1;
  }
}

```