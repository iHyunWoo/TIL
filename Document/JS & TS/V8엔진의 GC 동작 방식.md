# V8엔진의 GC 동작 방식
- 대부분의 브라우저 엔진은 Mark-and-Sweep을 기반으로 GC를 지원.
	- Mark-and-Sweep: Root부터 출발해서 하나하나 객체에 도달 가능한지. 
	  가능하지 않다면 제거(순환참조 방지 가능)
- V8엔진은 Generation 가설을 적용한 알고리즘을 사용
	- Generation: 대부분의 객체는 길게 살지 않고, 몇몇 객체만 오래 살아남음 -> 공간을 2개의 Generation으로 나눠 관리

### New Space의 Minor GC
- New Space(Young Genreration)은 1~8MB크기의 공간
- 기본적으로 New Space는 2개의 Semi space로 나뉨. From-space와 To-space
1. From space의 공간이 가득하면 Scavenge(Minor GC가 실행
2. 다른 곳에서 참조되는 메모리만 To space 옮기고 나머지는 정리
3. 이제 From space와 To space의 역할을 바꿈(From은 To로, To는 From으로)
4. 바꾸고 시간이 지나서 또 From space가 가득차면 Minor GC 발생
5. 이때는 한 번 살아남았던 메모리가 또 살아남는다면 Old Space로 승격됨

### Old Space와  Major GC
- Old space는 공간이 부족하면 Mark-and-sweep방식으로 GC를 실행
- New Space에 비해 공간이 커서 자주 ㅇ리어나지 않음
1. Marking
	- 루트로부터 DFS방식으로 연결된 객체들을 쭉 돌면서 Marking
2. Sweep
	- Marking 과정에서 표시 되지 않은 객체들을 정리
3. Compacting
	- 여기저기 파편화된 메모리를 다시 컴팩트하게 정렬하는 과정

### Orinoco 프로젝트
- GC가 진행되는 동안 Main Thread의 기존 작업이 중지됨(stop-the-world 발생)
- V8엔진은 이런 GC를 최적화하기 위해 Orinoco프로젝트를 통해 GC를 개선함
- 아래와 같은 방식들을 적절히 조합하여 GC 최적화
1. Parallel
	- 메인 쓰레드 혼자 하던 GC 작업을 헬퍼 스레드와 병렬 처리
	- 스레드간 동기화를 처리해야 해서 오버헤드가 생기지만 stop-the-world 시간이 크게 감소
2. Incremental
	- GC 작업을 작은 태스크로 쪼개서 분산 처리
	- 중간에 heap 상태가 변경되어 이전 작업이 무효화 될 수 있으므로 어려운 작업
	- 시간이 더 오래 걸릴 수 있지만, UX는 개선될 수 있음
3. Concurrent
	- 메인 스레드는 더 이상 GC를 하지 않고, 헬퍼 스레드가 수행
	- 중간에 언제든 heap 상태가 변경되어 이전 작업이 무효화 될 수 있으므로 가장 어려운 작업
	- stop-the-world 시간이 전혀 없음
4. Idle-time GC
	- V8엔진은 크롬과 같은 embedder에게 GC를 유발할 수 있는 메커니즘을 제공
	- 크롬에서는 프로그램이 쉬는 idle time을 알아내 해당 시간에 GC 작업
	- 예를 들어 1초에 60프레임을 렌더링하는 크롬은 1프레임을 렌더링 하기 위해 16ms(1/60)이 소모됨
	- 이때 만약 애니메이션 프레임 렌더링 작업이 16ms보다 빨리 끝나면, 크롬은 다음 프레임 작업 전까지 GC를 유발