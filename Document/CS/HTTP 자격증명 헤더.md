# HTTP 자격증명 헤더
- 클라이언트와 서버 간의 인증과정에서 사용자의 자격을 확인하기 위해 사용
## 헤더 종류
- Authorization
	- 클라이언트가 서버에 자신을 인증하기 위해 필요한 정보를 제공하는 헤더
	- `Authorization: <type> <credentials>`
	- ex) 기본 인증의 경우 ID와 비밀번호를 콜론으로 구분한 후 Base64로 인코딩하여 `Authorization: Basic dXNlcjpwYXNzd29yZA==` 형태로 전송
- WWW-Authenticate
	- 서버가 클라이언트에게 인증이 필요함을 알리는 헤더. 접근 권한을 요구
	- 클라이언트가 보호된 리소스에 접근을 요청할 때, 서버는 `401 Unauthorized` 응답과 `WWW-Authenticate` 헤더를 함께 전송하여 요구하는 인증 방식을 안내
	- ex) 다이제스트 인증 방식을 요구할 때 Digest realm="example" 형식으로 전송


## 인증방식
- 기본 인증 (Basic Authentication)
- 다이제스트 인증 (Digest Authentication)
- Bearer Token
- OAuth

## 기본 인증 (Basic Authentication)
- 동작 방식
	- 사용자 이름과 비밀번호를 콜론(:)으로 구분하여 base64로 인코딩한 값을 Authorization 헤더에 포함하여 서버로 전송
- 구현이 간단함
- Base64 인코딩은 암호화가 아니라, HTTPS와 같이 안전한 전송 채널을 사용하지 않으면 중간자 공격 등에 취약

## 다이제스트 인증 (Digest Authentication)
- 동작 방식
	- 서버가 클라이언트에 nonce값을 WWW-Authenticate헤더와 함께 전송
	- 클라이언트는 사용자 정보와 받은 nonce값을 조합하여 해시를 생성하여 서버로 전송
	- 서버는 클라이언트로부터 받은 Authorization 헤더 내의 값과 서버에서 독립적으로 해시값을 비교
	  일치하면 접근 허용
	- nonce
		- Number used once
		- 서버에서 임의로 생성된 딱 한 번만 사용되는 숫자
		- 재전송 공격 방지 목적
## Bearer Token
- 주로 JWT나 OAuth 2.0과 같은 토큰 기반 인증 시스템에서 사용
- 클라이언트는 서버로부터 받은 액세스 토큰을 `Authorization` 헤더에 포함하여 리소스에 접근 요청을 보냄

