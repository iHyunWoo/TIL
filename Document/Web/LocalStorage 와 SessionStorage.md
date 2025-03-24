# LocalStorage 와 SessionStorage
## LocalStorage
- 사용자가 브라우저를 종료해도 직접 지우기 전까지 영구적으로 남아있음
- 동일 도메인 내에서만 접근 가능
> 쿠키 vs LocalStorage
> - 클라이언트에서만 사용한다면 LocalStorage를, 서버와의 상호작용에 필요한 값이라면 쿠키를 사용
```js
localStorage.username = 'kevin';
localStorage.setItem('username', 'kevin');

console.log(localStorage.username);
console.log(localStorage.getItem('username'));

localStorage.removeItem('test');
localStorage.clear();  // 저장된 모든 값을 삭제

// 기본적으로 localStorage는 string만 저장 가능. 객체나 배열을 저장하려면, JSON으로 변환해야함
localStorage.user = JSON.stringify({name: "kevin"});
let user = JSON.parse( localStorage.user );
```
## SessionStorage
- 현재 브라우저 탭에 한정. 탭이 닫히면 데이터가 삭제됨
- 동일 도메인 내에서만 접근 가능하며, 같은 도메인이어도 다른 탭(윈도우)면 접근 불가
```js
// localStorage랑 사용법 유사
sessionStorage.setItem("domain", "naver.com");
sessionStorage.getItem("domain");
sessionStorage.removeItem("domain");
sessionStorage.clear();
```

## Storage Event
- localStorage나 sessionStorage의 데이터가 갱신될 때, 이벤트가 실행됨
- 다만, 같은 Browsing Context에서는 이벤트를 발생시키지 않음
	- Browsing Context란 Document를 표시하는 환경으로, 보통 tab단위지만 iframe, frame등 이 될 수 있음
- 속성
	- **key:** 변경된 데이터의 키
	- **oldValue:** 변경 전의 값 (문자열 형태)
	- **newValue:** 변경 후의 값 (문자열 형태)
	- **url:** 변경을 유발한 페이지의 URL
	- **storageArea:** 변경이 발생한 저장소(localStorage 또는 sessionStorage)

```js
// 다른 탭에서 localStorage 변경을 감지하기 위한 이벤트 리스너 등록
// window.onstorage = event => { 로도 쓸 수 있음
window.addEventListener("storage", function(event) {
  console.log("Key:", event.key);
  console.log("Old Value:", event.oldValue);
  console.log("New Value:", event.newValue);
  console.log("URL:", event.url);
  console.log("Storage Area:", event.storageArea);
});

```