## mutate의 종류 

###  Bound mutate
SWR 캐시를 mutate 해야 한다. mutate란, 돌연변이, 즉 `변형`을 의미한다. SWR 캐시를 변경하고자 하는 목적은 현재 view 상에서 보여지는 data는 swr의 캐시 data이기 떄문이다. 캐시된 data를 mutate 함으로써 view를 업데이트 하는 것이다.

```ts
const {data, mutate} = useSWR();

const onFavClick = () => {
	mutate({}, true) // 두번쨰 파라미터에는 revalidation 여부다. 
}

```
bound mutate에 첫번째 자리에 들어간는 파라미터 `{ }`는 변경될 새로운 데이터이다. 그리고 두번째 자리는 revalidation 여부이다. revalidation란? 해당 요청을 다시 server로  request 하는 것이다. 

bound mutation 이란, 제한된 변형을 뜻한다. 다시 말해 하나의 `useSWR`에 대한 mutate이다. 다른 개념으로 unbound mutation도 있다. `unbound mutation`란, 직접 요청한 useSWR 뿐만 아니라, 다른 화면에 있는 다른 요청들의 데이터도 변경하는 것이다.

```ts
const onFavClick = () => {
    if (!data) return;
    mutate({ ...data, isLiked: !data?.isLiked }, false); // cache만 변경하여 화면의 UI를 변경
    toggleFav({}); // backend server로 데이터 fetching을 요청
  };
```
### Unbound mutate

unbound mutations 이란, 내가 원하는 곳에서 원하는 것을 mutate 하는 것이라고 말할 수 있다. 예를 들어 내가 상품 페이지에서 user 정보에 대한 cache값을 mutate 할 수 있다는 뜻이다.

```ts
// unbound mutate는 `useSWRConfig()`에서 가져온다.

const { mutate: unboundMutate } = useSWRConfig();

unboundMutate(key, data, revalidateBoolean)
// data: 변경하고자 하는 새로운 데이터
```

```ts
mutate('/api/users/me')
// 만약 인자가 없으면, 단순히 refetch만 한다.
```
