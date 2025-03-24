# Swift와 Objective-C의 Bridging Header

Swift로 만든 프로젝트에서 obj-c 코드를 쓰려면 bridging header가 필요하다.   

## Bridging Header 만들기
1. ProjectName-Bridging-Header.h 파일 생성(프로젝트 이름만 바꿔서 이런 형식을 지켜야 함)
2. Project > Target > Build Settings > Objective-C Bridging Header에 파일 AppName-Bridging-Header.h 를 추가
3. 이 파일에 사용할 obj-c의 헤더파일 import
```
#import "RNCConfig.h"
```
