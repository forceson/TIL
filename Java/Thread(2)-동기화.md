# Thread(2) - 동기화

Created: Jul 16, 2019 11:12 PM

## 쓰레드의 동기화

- 멀티쓰레드의 경우 여러 쓰레드가 같은 프로세스 내의 자원을 공유해서 작업하기 때문에 서로의 작업에 영향을 주게된다.
- 이를 방지하기 위해 한 쓰레드가 특정 작업을 끝마치기 전까지 다른 쓰레드에 의해 방해받지 않도록 하는, 임계 영역(Critical Section)과 잠금(Lock)이 도입되었다.
- 공유 데이터를 사용하는 코드 영역을 임계 영역으로 지정하고, 공유 데이터(객체)가 가지고 있는 lock을 획득한 단 하나의 쓰레드만 이 영역 내의 코드를 수행한다.
- 이후 해당 쓰레드가 임계영역 내 모든 코드를 수행하고 벗어나서 lock을 반납해야만 다른 쓰레드가 반납된 lock을 획득하여 임계 영역 코드를 수행할 수 있다.

→ Thread Synchronization

자바에서는

- synchronization block
- java.util.concurrent.locks
- java.util.concurrent.atomic

이용하여 구현

## synchronized 이용한 동기화 - 임계영역 설정

1. 메서드 전체를 임계영역으로 지정 → 해당 메서드를 가진 객체에 lock을 건다. 메서드 종료시 lock 반환

```Java
    public synchronized void calcSum() {
    	// ...
    }
```

  2. 특정 영역을 임계영역으로 지정 - 지정된 객체에 lock을 건다. 블럭 벗어나면 lock 반환

```Java
    synchronized (객체의 참조변수){
    	//...
    }
```

임계영역은 멀티쓰레드 프로그램의 성능을 좌우하므로 가능하면 블럭을 이용해서 효율적ㅇ니 프로그램을 만들어야한다.

static메서드의 경우 class에 lock을 건다. instance와 다름.

## wait(), notify()

- 특정 쓰레드가 객체의 lock을 가진 상태로 오랜 시간을 보내지 않게 하기 위해, wait(), notify() 고안

wait() - lock을 반납하고 대기. 다른 쓰레드가 해당 객체에 대한 작업 수행

notify() - 작업을 중단했던 여러 쓰레드 중 하나의 쓰레드가 다시 lock을 얻어 작업을 진행

- 재진입(reentrant) - wait → notify
- wait()  호출 시, 실행중이던 쓰레드는 해당 객체의 waiting pool에서 대기. timeout 설정하면 시간 후에 notify() 함
- notify() 호출 시, 해당 객체 waiting pool에 있던 모든 쓰레드 중 임의의 쓰레드에 통지
- notifyAll()은 객체 waiting pool에 기다리고 있는 모든 쓰레드에게 통보
- Object에 정의되어 있다.
- 동기화 블록(synchronized 블록) 내에서만 사용가능하다.
- 보다 효율적인 동기화를 가능하게 한다.
- 문제는 기아 상태, 경쟁 상태 발생

## Lock과 Condition을 이용한 동기화

- From Java 1.5
- ReentrantLock - 재진입이 가능한 lock. 가장 일반적인 배타 lock. 특정 조건에서 lock을 풀고 다시 lock을 얻고 임계영역으로 들어와서 이후의 작업 수행가능.
- ReentrantReadWriteLock - 읽기 lock, 쓰기 lock 제공. 읽기에는 공유적이고 쓰기에는 배타적
- StampedLock - ReentrantReadWriteLock에 낙관적인 읽기 lock기능 추가. lock을 걸거나 해지할 때 스탬프(long 타입의 정수 값) 이용. 읽기 lock이 걸려있으면, 쓰기 lock을 쓰기 위해서는 읽기 lock이 풀릴 때 까지 기다려야 하는데 비해 '낙관적 읽기 lock'은 쓰기 lock에 의해 바로 풀린다. 그래서 낙관적 읽기에 실패하면 읽기 lock을 얻어서 다시 읽어와야 한다. **무조건 읽기 lock을 걸지 않고, 쓰기와 읽기가 충돌할 때만 쓰기가 끝난 후에 읽기 lock을 거는 것이다.**

ReentrantLock()

ReentrantLock(boolean fair) ← 가장 오래 기다린 쓰레드를 찾아 실행. 성능은 낮아짐.

lock(), unlock(), isLocked()

```Java
    lock.lock();
    try {
    	// 임계영역
    } finally {
    	lock.unlock();
    }
```

boolean tryLock()

boolean tryLock(long timeout, TimeUnit unit) throws InterruptException

InterruptException - 지정된 시간동안 기다리는 도중 작업 취소될 수 있도록 코드 작성

다른 쓰레드에 의해 lock 걸려 있으면 lock 얻으려고 대기하지 않는다. 또는 지정된 시간 만큼 대기 lock을 얻으면 true, 아니면 false. 응답성 중요한 경우 사용한다.

### Condition

- 쓰레드를 구분하여 통지한다.
- condition 각각 쓰레드마다 만들어서 각각의 waiting pool에서 대기하도록 만든다.
- 이미 생성된 lock으로부터 newCondition() 호출하여 생성한다.
- wait 대신 await, notify 대신 signal

## volatile

- 멀티코어 프로세서에서 코어마다 별도의 캐시를 가지고 있어 문제 발생 가능. 코어마다 별도의 캐시에 겂을 저장해서 사용하기 때문.
- 코어는 메모리에서 읽어온 값을 캐시에 저장하고 캐시에서 값을 읽어서 작업.
- 다시 같은 값을 읽어올 때 먼저 캐시 히트 후 값이 없을 때만 메모리에서 읽는다.
- 그러다보니 도중에 메모리에 저장된 변수 값이 변경되었는데도 캐시에 저장된 값이 갱신되지 않아 메모리에 저장된 값이 다른 경우가 발생.
- volatile은 코어가 변수 값 읽어올 때 캐시 아닌 메모리에서 읽어오게 한다.
- volatile 붙이는 대신 synchronized 블럭 사용해도 같은 효과. synchronized 블럭은 쓰레드가 블럭 출입 시 캐시 메모리간 동기화를 시킴.

- JVM은 데이터를 4byte 단위로 처리하므로 int와 int보다 작은 타입들을 한 번에 읽거나 쓰는게 가능하다. 하나의 명령어는 더 이상 나눌 수 없는 최소 작업 단위이므로, 작업의 중간에 다른 쓰레드가 끼어들 틈이 없다.
- 그러나 크기가 8byte인 long, double 타입의 변수는 다른 쓰레드가 끼어들 여지가 있다.
- volatile을 쓰면 읽기 쓰기가 원자화된다. 작업을 나눌 수 없다는 이야기
- synchronized 블럭도 일종의 원자화이다. 즉, synchronized 블럭은 여러 문장을 원자화 함으로써 쓰레드의 동기화를 구현한 것이라고 보면 된다.
- volatile은 읽기 쓰기 원자와 일뿐 동기화는 아니라는 것에 주의해야한다.