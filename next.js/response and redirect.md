### Next Response And Redirect

`middleware`를 사용해서 router에 대한 Response를 설정 할 수 있다. 그리고 `NextResponse.redirect()`를 통해서 redirect 할 수 있다. 클라이언트에서 커스텀 훅을 통해서 페이지를 가드하는 방법도 있지만, 그 방식은 가드가 적용되기전까지 의도치 않게 사용자에게 화면이 보여지는 현상이 있다. 미들웨어를 통한 방법은 클라이언트 코드가 실행되기전에 먼저 실행되기 때문에 더 매끄럽게 처리 할 수 있다. 

API call에 대한 응답을 통해 router를 조작하던 방식과 달리, 인증되지 않은 유저는 처음부터 API call을 보내지 않기 때문에 불필요한 요청을 줄이는 효과도 있다.

```ts
export default function useUser(){
  const { data, error } = useSWR<ProfileResponse>("/api/user/me");
  const router = useRouter();
  
  // API call을 해서 data가 있으면, `/enter`로 이동시킨다.
  useEffect(()=>{
    if(data && !data.ok){
      router.replace("/enter");
    }
  },[data, router]);
  
  return { user: data?.profile, isLoading: !data && !error };
}
```
미들웨어 코드
```typescript
// _middleware.ts
export function middleware(request, event){
  // req.url: 유저가 요청한 url
  // req.cookies: 브라우저에 저장된 쿠키
  
  if(){
     
  }
}
```

`NextResponse`는 클라이언트로 json을 보내거나 rewrite, redirect 등을 할 수 있다. `return NextResponse....` 처럼 반드시 `return`을 같이 붙여줘야 정상적으로 기능이 작동한다.

