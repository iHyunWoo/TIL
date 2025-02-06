# AJAX. 그리고 Fetch와 Axios

## AJAX란?
- Asynchoronous Javascript And Xml의 약자
- JS와 XML을 이용하여 비동기적으로 서버와 데이터를 주고받는 기법을 의미
- 기술 자체가 아닌, 비동기 통신을 위한 개념


## Fetch란?
- 웹 브라우저 API
- 최신 JS에서 AJAX를 구현하는 방법
- 과거에는 XMLHttpRequest를 사용했지만, 보다 더 간결하게 사용 가능

```javascript
fetch("https://test.com") 
	.then((response) => { 
		if (!response.ok) {
			throw new Error(`HTTP 오류! 상태 코드: ${response.status}`); 
		}
		return response.json(); 
	})
	.then((data) => console.log(data)) 
	.catch((error) => console.error("❌ Fetch 에러:", error.message)); 
	
// async/await 사용 
try { 
	const response = await fetch("https://test.com"); 
	if (!response.ok) { 
		throw new Error(`HTTP 오류! 상태 코드: ${response.status}`); 
	} 
	const data = await response.json();
	console.log(data); 
} catch (error) { 
	console.error("❌ Fetch 에러:", error); 
}
```


## Axios란?
- Fetch API보다 더 편리한 기능을 제공하는 라이브러리
- Feach API와 달리 자동으로 HTTP 오류(400, 500 등)을 감지하여 예외를 발생시킴
- 자동으로 Json 파싱
- 인터셉터 지원 등의 편의 기능 제공
```javascript
axios.get("https://test.com") 
	.then(response => console.log(response.data)) 
	.catch(error => console.error("❌ Axios 에러:", error));
	
// async/await 사용 
try {
	const { data } = await axios.get("https://test.com");
	console.log(data); 
} catch (error) { 
	console.error("❌ Axios 에러:", error); 
}
```


## Axios 요청/응답 인터셉터
```javascript
// Axios 인스턴스 생성 
const axiosInstance = axios.create({
	baseURL: "https://test.com",
	timeout: 1000,
}); 

// 요청 인터셉터 
axiosInstance.interceptors.request.use(
	(config) => { 
		// 요청이 전달되기 전에 작업 수행
		return config; 
	}, 
	(error) => { 
		// 요청 오류가 있는 작업 수행
		return Promise.reject(error);
	} 
); 

// 응답 인터셉터 
axiosInstance.interceptors.response.use(
	(response) => { 
		// 2xx 범위의 상태 코드
		return response.data; 
	},
	(error) => { 
		// 2xx 범위
	
		return Promise.reject(error); 
	}
);


```

## Reference
<!-- 사용하지 않는 레퍼런스 종류는 삭제 후 업로드 -->
- [Ajax와 Axios 그리고 fetch](https://velog.io/@kysung95/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-Ajax%EC%99%80-Axios-%EA%B7%B8%EB%A6%AC%EA%B3%A0-fetch)
- [[Axios 공식 문서] 인터셉터](https://axios-http.com/kr/docs/interceptors)