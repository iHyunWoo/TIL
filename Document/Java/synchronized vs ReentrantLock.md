# synchronized vs ReentrantLock


## synchronized
synchronized는 메소드나 코드 블록에 대해 하나의 스레드만 접근할 수 있도록 보장함
```java
// 메소드
public synchronized void increment() {
	count++;
}

// 코드 블록
private int count = 0;
private final Object lock = new Object();

public void increment() {
	synchronized (lock) {
		count++;
	}
}
```

## ReentrantLock
ReentrantLock은 `java.util.concurrent.locks` 패키지에서 제공하는 클래스로, synchronized보다 더 세밀한 락 제어가 가능
- synchronized는 임계 영역 내에서만 락이 걸림
- 반면 ReentrantLock는 lock()과 unlock()으로 락을 명시적으로 지정해서 여러 메서드에 나눠서 작성 가능(물론 굉장히 위험!!!)
- 또한, 공정한 Lock으로 가장 오랫동안 스레드에게 lock을 제공한다.(기아 상태 방지)
- Condition으로 특정 객체만을 깨우도록 할 수 있음
```java
private final ReentrantLock lock = new ReentrantLock();  // 불공정한 락
private final ReentrantLock fairLock = new ReentrantLock(true);  // 공정한 락
lock.lock();
// 임계영역
lock.unlock();

// lock으로 condition 생성
private Condition forStudent = lock.newCondition();          // 학생 스레드 Condition
private Condition forTeacher = lock.newCondition();      // 선생님 스레드 Condition
```