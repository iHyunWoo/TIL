# XSS와 CSRF
## XSS
- Cross-Site Scripting
- 공격자가 웹 페이지에 악성 스크립트를 삽입해 사용자의 브라우저에서 실행되게 하는 공격
- 종류
	- Stored XSS
		- 스크립트가 서버에 저장되는 형태의 공격
		- 일회성이 아닌 지속적으로 실행될 수 있고, 다른 사용자에게도 노출될 수 있음
		- 예시) 게시글 내에 스크립트를 삽입하여, 다른 사용자가 열람 시 악성 스크립트 실행
	- Reflected XSS
		- 공격자가 입력한 스크립트가 즉시 실행되는 공격
		- 취약점이 존재하는 페이지를 미리 탐색 후 악성 스크립트가 포함된 URL을 공격 대상자에게 노출
	- DOM-based XSS
		- 브라우저에서 DOM을 조작해 악성 스크립트를 실행하는 공격
- 방지 방법
	1. XSS 취약점이 있는 innerHTML 사용 자제
		- textContent, innerText 사용
	2. HttpOnly 쿠키
	3. CSP 사용
		- Content Security Policy
		- 유효한 도메인으로 지정한 도메인의 스크립트만 실행
		```html
		#모든 콘텐츠는 현재 도메인에서만 제공되어야 한다. ( 하위 도메인의 리소스만 사용 )
		Content-Security-Policy: default-src 'self'
		
		#신뢰할 수 있는 특정 도메인과 그것의 모든 하위 도메인의 콘텐츠를 허용한다.
		Content-Security-Policy: default-src 'self' *.domain.com
		
		#이미지는 모든 도메인, 미디어와 스크립트는 특정 도메인만 허용한다.
		Content-Security-Policy: default-src 'self'; img-src *; media-src media1.com media2.com; script-src userscripts.example.com
		
		#기본적으로 HTTPS를 사용하는 특정 도메인만 사용하도록 한다.
		Content-Security-Policy: default-src https://onlinebanking.mybank.com
		```
	4. 특수문자 치환
		- < 를 &lt; 로, >를 &gt;로 치환하는 등 escape 처리

## CSRF
- Cross Site Request Forgery
- 사용자 자신의 의지와는 무관하게 공격자가 의도한 행위를 하는 공격
- 동작 원리
	1. 사용자가 보안이 취약한 서버에 로그인
	2. session ID가 사용자의 브라우저 쿠키에 저장
	3. 공격자는 사용자가 악성 스크립트 페이지를 누르도록 유도(악성 게시글 or 메일 등)
	4. 사용자가 악성 페이지 접근 시 쿠키에 저장된 session ID 탈취
	5. 공격자는 사용자의 세션을 통해 공격
- 방지 방법
	- Referer 및 Origin검증
		- 요청된 페이지의 정보를 확인
		- 조작이 가능하므로 만능이 아님
	- CAPTCHA 등 추가 인증 수단 사용
	- CSRF 토큰 사용
		- 사용자 세션에 임의의 값을 저장하여 모든 요청마다 해당 값을 포함하여 전송하도록 
	- SameSite 쿠키 설정
		- 쿠키가 타 사이트에 전송되지 않도록 설정
		```
		Set-Cookie: session=abc123; SameSite=Strict;
		```