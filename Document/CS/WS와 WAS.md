# WS와 WAS
## WS
- Web Server
- 정적인 콘텐츠(HTML, CSS, JS, 이미지)를 클라이언트에게 전달
- Nginx, Apache 등

## WAS
- Web Application Server
- 비즈니스 로직을 처리하는 서버로, 동적인 콘텐츠를 생성하여 클라이언트에 제공
- Spring boot, Nodejs, Tomcat 등

## WS와 WAS를 같이 사용하는 이유
1. 기능을 분리하여 서버 부하 방지
	- WAS는 DB 조회나 로직을 처리하느라 바쁘기 때문에, 단순한 정적 컨텐츠는 WS에서 제공함으로 부하 분산
2. 물리적으로 분리하여 보안 강화
3. WS의 Load Balancing 역할
	- 여러 대의 WAS를 연결하고 트래픽 분산 가능
4. 여러 웹 애플리케이션 서비스 가능
	- 예를 들어, 하나의 서버에서 PHP Application, Java Application을 함께 사용하는 경우
![[web-system-architecture.png]]
## Reference
<!-- 사용하지 않는 레퍼런스 종류는 삭제 후 업로드 -->
- [Web Server와 Web Application Server](https://velog.io/@gillog/Web-Server%EC%99%80-Web-Application-Server%EC%9D%98-%EC%B0%A8%EC%9D%B4)