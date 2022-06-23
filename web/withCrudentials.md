### withCdudentials

서버와 클라이언트의 Origin이 다른 경우, Cookie의 정보가 Request Header에 들어가지 않는다. 이러한 경우 Request 옵션에서 withCrudentials를 true로 해줘야 정상적으로 request에 쿠키가 포함된다.

```js
axios.get('url', {
  withCrudentials: true
})
```
> 물론 백엔드에서도 