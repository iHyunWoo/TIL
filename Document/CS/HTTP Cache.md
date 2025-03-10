# HTTP Cache

## HTTP Cache 종류
1. Private Cache(Browser Cache)
	- 개인 사용자 브라우저 내에 구현된 캐시
	- 사용자가 방문한 웹사이트의 리소스를 저장하고 재방문 시 빠르게 제공
2. Shared Cache
	- 여러 사용자가 접근할 수 있는 캐시(CDN 또는 공용 프록시 서버)
	- 다수의 사용자에게 공통된 리소스를 효율적으로 제공
3. Proxy Cache
	- ISP 서버 또는 네트워크 프록시 서버에 구현된 캐시
4. Managed Cache
	- 애플리케이션 서버 또는 캐시 서버
	- 특정 서비스의 유저를 대상으로 한 캐시

## Cache-Control
- HTTP Cache-Control 헤더는 웹 서버와 브라우저 간에 캐시 동작을 제어하기 위해 사용
1. no-store
	- 특정 자원에 대해 캐싱 금지
	- 실시간 메시지, SNS등 최신 상태를 항상 유지해야하는 경우
2. max-age=초
	- n초 동안 캐시
	- 일반적인 게시물 등
3. must-revalidate
	- 캐시된 자원이 만료되었을 때, 서버에 재검증 하도록 지시
	- 재검증 요청에 실패했다면 504 Gateway Timeout 에러
4. public
	- 프록시 서버같은 공개 캐시에 저장 가능
5. private
	- 브라우저 같은 비공개 캐시에만 저장 가능
6. no-cache
	- 모든 캐시를 사용하기 전 서버에 검증 요청
	- 캐시된 데이터를 활용하면서 동시에 데이터의 최신성과 정확성 보장

## If-(Un)Modified-Since
1. If-Modified-Since
	- 클라이언트가 가진 캐시가 최신인지 확인하기 위한 헤더
	- 클라이언트가 가진 캐시의 마지막 수정 날짜를 서버에 전달
	- 서버의 리소스가 그 이후에 변경 -> 리소스 반환(200 OK)
	- 서버의 리소스가 그 이후에 변하지 않았음 -> 304 Not Modified
2. If-Unmodified-Since
	- 리소스,가 특정 시점 이후 변경되지 않았다면 요청을 수행하기 위한 헤더
	- 해당 날자 이후로 리소스가 변경 안됐다면 요청 수행
	- 해당 날짜 이후 리소스가 변경되었다면, 412 Precondition Failed 반환
	- 데이터 무결성 보장하기 위함

## 캐시 무효화 및 갱신 전략
3. Cache Busting
	- 리소스 URL에 버전 정보 혹은 난수를 추가하여 변경 시 새로운 요청을 강제
	- ex) style.css?v=2.0
4. SWR(Stale-Whild-Revalidate)
	- 사용자가 페이지에 접속하면, 우선 캐시된 콘텐츠를 보여줌
	- 페이지가 로드된 후, 백그라운드에서 서버에게 최신 데이터를 요청
	- 새로운 데이터가 수신되면 콘텐츠 업데이트
	- ex) Cache-Control: max-age=1, stale-whild-revalidate=60
		- ~1초(신선): 최대 1초 동안은 캐시된 응답을 재검증 없이 바로 반환
		- 1~60초(오래되었지만 반환 후, 재검증): SWR전략
		- 60초~: 캐시된 데이터를 더 이상 사용하지 않음