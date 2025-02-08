# Promise와 Async, Await

## 콜백 함수
함수의 매개변수로 전달되어 비동기 작업이 끝났을 때 실행되는 함수
```javascript
function fetchData(url, callback) {
  setTimeout(() => {
    const dummy = {user: 'Kevin', age: 26};
    callback(dummy, null);
  }, 1000);
};

fetchData('https://dummy.url.com', () => {
  fetchData('https://dummy.url2.com', () => {
    fetchData('https://dummy.url2.com', (response, error) => {
      if(error) {
        console.log("Error: ", error);
      } else {
        console.log('Response:', response);
      }
    })
  })
});
```
이런 콜백 함수의 형태는 콜백 함수가 중첩되며 가독성을 떨어뜨리고, 에러처리가 어려워짐.
## Promise
Promise 객체를 이용하여 콜백 함수를 대체하고 비동기 작업의 흐름을 쉽게 제어할 수 있음
```javascript
function fetchData(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const dummy = { user: "Kevin", age: 26 };
      resolve(dummy);
    }, 1000);
  });
}

fetchData("https://dummy.url.com")
  .then(() => fetchData("https://dummy.url2.com"))
  .then(() => fetchData("https://dummy.url3.com"))
  .then(response => {
    console.log("Response:", response);
  })
  .catch(error => {
    console.log("Error:", error);
  });

```
- 프로미스 핸들러
	- .then()
		- 프로미스가 이행되었을 때 실행할 콜백 함수를 등록하고 새로운 프로미스를 반환
	- .catch()
		- 프로미스가 거부(reject)되었을 때 실행할 콜백 함수를 등록하고 새로운 프로미스를 반환
	- .finally()
		- 프로미스가 이행되거나 거부될 때 상관없이 실행할 콜백 함수를 등록하고 새로운 프로미스를 반환
- 모두 프로미스를 반환하여 체이닝이 가능
- 프로미스 static method
	- Promise.resolve() / Promise.reject()
		- 프로미스 생성자로 만들고, 그 안의 콜백 함수의 매개변수로 제공
		- 각각 성공/실패 
	- Promise.all()
		- 여러 개의 Promise를 병렬로 실행하고, 모든 Promise가 성공하면 결과 반환
		- results의 순서는 promise의 순서와 같음
		- 어느 하나라도 프로미스가 거부되면 에러
		```javascript
		const api_1 = fetch("https://jsonplaceholder.typicode.com/users");
		const api_2 = fetch("https://jsonplaceholder.typicode.com/users");
		const api_3 = fetch("https://jsonplaceholder.typicode.com/users");

		const promises = [api_1, api_2, api_3];

		Promise.all(promises)
		    .then((results) => {
		      console.log(results);
		    })
		    .catch((error) => {
		      console.error(error);
		    });
		```
	- Promise.allSettled()
		- .all()처럼 모든 Promise를 병렬로 실행
		- 하지만 성공하든 실패하든 완료하기만 하면 status와 같이 리턴
		```javascript
		const api_1 = Promise.resolve(1);
		const api_2 = Promise.reject("Error!");
		const api_3 = new Promise(resolve => setTimeout(() => resolve(3), 2000));
		
		const promises = [api_1, api_2, api_3];
		
		Promise.allSettled(promises)
		    .then((results) => {
		      console.log(results);
		    })
		/*
		[
		  { status: 'fulfilled', value: 1 },
		  { status: 'rejected', reason: 'Error!' },
		  { status: 'fulfilled', value: 3 }
		]
		*/

		```
	- Promise.any()
		- 첫번째로 이행(fulfilled)한 프로미스만 취급. 나머지는 무시
	- Promise.race()
		- 이행 거부 여부 상관없이 무조건 처리가 첫번째로 끝낸 프로미스 반환

## Async/Await
- ES8에 도입된 비동기 처리를 위한 문법
- 프로미스를 기반으로 함
- async 함수는 Promise를 반환. await은 내부적으로 .then()을 이용해서 실행
```javascript
async function fetchData() { 
	try {
		const result = await someAsyncFunction(); 
		console.log(result); 
	} catch (error) {
		console.log(error);
	}
}
```

## Async/Await 의 주의할 점
- 아래 처럼 연관이 없는 두 데이터에서 await 키워드를 여러번 붙이면, 이전 요청을 대기 함
```javascript
async function fetchData() {
  const user = await getUserData()
  const posts = await getPosts()

  console.log(user, posts);
}

// 올바른 방법
const [user, posts] = await Promise.all([getUserData(), getPosts()])

```
