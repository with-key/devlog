## InputType and ArgsType
**Dto클래스에서 사용되는 데코레이터이다.** InputType, ArgsType 모두 Query, Mutation에서 argument를 받고자 할 때 사용한다. 하지만 두개의 차이점이 있다.

  ```ts
    // InputType을 사용할 때
    @Mutation(() => Boolean)
    createStore(@Args('newStoreInput') newStoreInfo: CreateStoreDto): boolean {
      return true;
    }

    // ArgsType을 사용할 때
    @Mutation(() => Boolean)
    createStore(@Args() newStoreInfo: CreateStoreDto): boolean {
      return true;
    }
  ```
  둘다 API를 작성할 때, API의 인자에 `@Args()`를 사용하는데, 여기에 인자에 이름을 넣느냐, 넣지 않느냐의 차이가 있다.

  ```graphql
    # inputType
    muataion {
      createStore(newStoreInput: {
        address: "",
        ownerName : ""
      })
    }

    # ArgsType
    muataion {
      createStore(address: "", ownerName: "")
    }
  ```
  클라이언트에서 요청할 때 인자를 채워 넣는 방법이 다르다.