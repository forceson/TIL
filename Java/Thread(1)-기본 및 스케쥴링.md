# Thread(1) - 기본 및 스케쥴링

Created: Jul 15, 2019 5:48 PM

## 프로세스와 쓰레드

프로세스 - 실행 중인 프로그램. 프로그램을 실행하면 OS로부터 실행에 필요한 자원(메모리)를 할당받아 프로세스가 된다.

쓰레드 - 프로세스의 자원을 이용해서 실제로 작업을 수행한다. 프로세스의 자원을 공유한다는 것이 핵심이다. 해당 용어는 1코어 4쓰레드에서 흔히 쓰이는 쓰레드와 다르다. 1코어 4쓰레드에서의 쓰레드는 물리적인 작업의 단위이고, 현재 언급된 쓰레드는 논리적 쓰레드로 흔히 프로그래밍을 할 때 말하는 일반적인 프로그램의 작업을 수행하는 쓰레드이다.

- 둘 이상의 쓰레드를 가진 프로세스는 멀티쓰레드 프로세스라고 한다.
- 쓰레드가 작업을 수행하는데 개별적인 메모리 공간(콜 스택)을 필요로 하기 때문에 프로세스의 메모리 한계에 따라 생성할 수 있는 쓰레드 수가 결정된다.
- 대부분의 OS는 멀티태스킹을 지원하여 여러 개의 프로세스가 동시에 실행될 수 있다.
- 멀티쓰레딩은 여러 쓰레드가 하나의 프로세스에서 동시에 작업을 수행하는 것이다.

멀티쓰레딩의 장점

- CPU 사용률 증가
- 자원의 효율적인 사용
- 사용자에 대한 응답성 향상
- 작업이 분리되어 코드가 깔끔해진다.

멀티쓰레딩의 단점

- 동기화
- 데드락

## 쓰레드 구현방법

1. Thread 클래스 상속
```Java
    class MyThread extends Thread {
    	public void run() { // ... } // Thread 클래스 run()을 오버라이딩
    }
    
    // ...
    
    thread.start(); // 실행대기 중이다가, 자신의 차례오면 실행
```

 2. Runnable 인터페이스 구현
```Java
    class MyThread implements Runnable {
    	public void run() { // ... } // Runnable 인터페이스의 run()을 구현
    }
    
    // ...
    
    Thread thread = new Thread(runnable);
    thread.start();
```
- Thread 상속 시 다른 클래스의 상속을 못받으니 Runnable 구현하는 방법이 일반적이다.
- Runnable 구현은 재사용성이 높고 코드 일관성 유지하기에 좋아 보다 객체지향적이다.
- 쓰레드를 구현한다는 것은 작업 내용으로 run()의 블록을 채우는 것이다.
- Runnable은 실행시 Thread 클래스 생성자에 넣어서 실행해야한다.

## start()와 run()

main 메서드에서 run()을 호출하는 것은 생성된 쓰레드를 실행시키는 것이 아니라 단순히 클래스에 선언된 메서드를 호출하는 것이다. 따라서 콜스택은 아래와 같이 된다.

| run()   |

| main() |

start()는 새로운 쓰레드가 작업을 실행하는데 필요한 콜스택을 생성하고 다음에 run()을 호출해서, 생성된 콜스택에 run()이 첫번째로 올라가게 한다. 모든 쓰레드는 독립적인 작업 수행을 위해 자신만의 콜스택이 필요하다.

1. start() 호출

| start() |

| main() |

 2. 새로운 쓰레드의 콜스택 생성

| start() |   |BLANK|

| main() |   |BLANK|

 3.  새로운 콜스택에서 run() 호출

| start() |   |BLANK|

| main() |   | run() |

 4. 두 개의 쓰레드에서 작업

| start() |   |BLANK|

| main() |   | run()  |

- 콜스택이 2개이므로 스케쥴러가 정한 순서에 의해 번갈아가며 작업이 실행된다.
- 쓰레드가 2개 이상이면 최상위 메서드라도 대기상태가 될 수 있다.

## 싱글 쓰레드, 멀티 쓰레드

- 싱글 코어에서는 단순 CPU 사용하는 계산작업이라면 멀티쓰레드보다 싱글쓰레드를 사용하는게 낫다. Context Switching (프로세스 또는 쓰레드 간 작업 전환) 때문
- 멀티 쓰레드로 작업했을 때 시간이 더 걸리는 이유

    Context Switching

    한 쓰레드가 화면에 출력하는 동안 다른 쓰레드는 출력이 끝나기를 기다려야 하는데 이 때 대기시간이 발생한다.

- 두 쓰레드가 서로 다른 자원을 사용하는 작업의 경우에는 싱글쓰레드 프로세스보다 멀티쓰레드 프로세스가 더 효율적이다. ex) 사용자가 데이터 입력, 네트워크로 파일 입출력, 프린터로 파일 출력, 외부기기와의 입출력
- 여러 쓰레드가 여러 작업을 동시 진행하면 Concurrent (병행), 하나의 작업을 여러 쓰레드가 나눠서 처리하면 Parallel (병렬)

## 쓰레드 우선순위 (1 ~ 10)

- 쓰레드는 priority라는 멤버변수를 가지고 있는데, 이 우선순위 값에 따라 쓰레드가 얻는 실행시간이 달라진다.

    ex) 파일 전송 기능이 있는 메신저 - 채팅 내용 전송 쓰레드 > 파일 다운로드 처리 쓰레드

- 쓰레드 우선 순위는 쓰레드를 생성한 쓰레드로부터 상속 받는다.

    ex) main Thread 우선순위 = 5 → main 메서드 내에서 생성하는 쓰레드는 우선순위 = 5

- 싱글코어에서는 우선순위가 잘 먹히지만 멀티코어에서는 쓰레드 우선순위 차이가 없다. OS 스케쥴링 방식과 JVM 구현 방식이 다르기 때문이다. 따라서 그저 쓰레드에 높은 우선순위를 주면 더 많은 실행시간, 기회를 갖는다고 기대할 수 없다. 차라리 작업에 우선순위를 두어 PriorityQueue에 저장해두고, 우선순위 높은 작업을 선처리하게 하는게 나을 수 있다.

## 쓰레드 그룹

[ThreadEx9](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx9.java)


- 서로 관련된 쓰레드를 그룹으로 다루기 위한 것이다. 쓰레드 그룹안에 쓰레드 그룹도 가능하다.
- 보안상의 이유로 도입되었고, 자신이 속한 쓰레드 그룹이나 하위 쓰레드 그룹은 변경가능하나, 다른 쓰레드 그룹의 쓰레드는 변경이 불가능하다.
- ThreadGroup 생성자를 통해 생성한다.
- 쓰레드를 쓰레드 그룹에 포함시키려면 Thread 생성자를 이용해야한다.

    Thread(ThreadGroup group, String name)

    Thread(ThreadGroup group, Runnable target)

    외 2개

- 모든 쓰레드는 반드시 쓰레드 그룹에 포함되어 있어야하므로, 쓰레드 그룹을 지정하는 생성자를 사용하지 않은 쓰레드는 기본적으로 자신을 생성한 쓰레드와 같은 쓰레드 그룹에 속하게 된다.
- 자바 어플리케이션이 실행되면, JVM은 main과 system이라는 쓰레드 그룹을 만들고 JVM 운영에 필요한 쓰레드를 생성해서 이 쓰레드 그룹에 포함시킨다.

    ex) main 메서드를 수행하는 main Thread는 main Thread Group에 속한다. GC를 수행하는 FinalizerThread는 system Thread Group에 속한다.

- 우리가 생성하는 모든 쓰레드 그룹은 main Thread Group의 하위 쓰레드 그룹이다.
- Thread의 ThreadGroup 관련 메서드

    ThreadGroup getThreadGroup()

    void unCaughtException(Thread t, Throwable e) - 쓰레드 그룹의 쓰레드가 처리되지 않은 예외에 의해 종료되면, JVM이 호출

## daemon thread

[ThreadEx10](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx10.java)

- 데몬쓰레드는 다른 일반 쓰레드의 작업을 돕는 보조적인 역할을 수행하는 쓰레드이다.

    ex) GC, 워드프로세서의 자동저장, 화면 자동 갱신

- 무한루프와 조건문을 통해 실행 후 대기하고 있다가 특정조건이 만족되고나면 작업을 수행하고 다시 대기한다.
- 작성방법, 실행방법은 일반 쓰레드와 같고 실행전 setDaemon(true)를 호출하면 된다.
- 데몬쓰레드가 생성한 쓰레드는 모두 데몬쓰레드이다.
- 일반쓰레드가 모두 종료되면 데몬쓰레드는 강제적으로 자동종료된다.

## 쓰레드의 실행제어 (스케쥴링)

- 효율적인 멀티쓰레드 프로그램을 만들기 위해서는 보다 정교한 스케쥴링을 통해 프로세스에게 주어진 자원과 시간을 여러 쓰레드가 낭비없이 잘 사용하도록 프로그래밍 해야한다.
- 상태관련 메서드


메서드 | 설명
-------------|------------------------
|static void sleep(long mills, int nanos)|지정된 시간 (1/1000) 동안 쓰레드 일시정지. 지정시간 후, 실행대기(RUNNALBE 상태)|
|void join(long mills, int nanos)|지정된 시간동안 쓰레드 실행, 지정 시간 후 작업종료되면 join()을 호출한 쓰레드로 다시 돌아와 실행계속. 일종의 대기|
|void interrupt()|sleep()이나 join()에 의해 일시정지된 쓰레드를 실행대기 상태로 만든다. 해당 쓰레드에서는 InterruptException이 발생함으로써 일시정지 상태 해제|
|void stop()|쓰레드 즉시 종료|
|void suspend()|쓰레드 일시정지|
|void resume()|suspend()에 의한 일시정지를 실행대기 상태로|
|static void yield()|실행 중에 자신에게 주어진 실행시간을 다른 쓰레드에게 양보하고 자신은 실행대기 상태로|

- 쓰레드 상태

상태 | 설명
------|------------------------
NEW|쓰레드 생성되고 아직 start() 호출 안된 상태
|RUNNABLE|실행 중 또는 실행 가능한 상태|
|BLOCKED|동기화 블럭에 의해 일시정지된 상태(lock이 풀릴때까지 대기)|
|WAITING|쓰레드의 작업이 종료되지는 않았지만 실행가능하지 않은 상태|
|TIMED_WATING|(unrunnable)일시정지 상태. TIME_WAITING은 일시정지 시간 지정된 경우|
|TERMINATED|쓰레드의 작업이 종료된 상태|

![https://lh4.googleusercontent.com/oR0_liUxjMnfgFS-fSuc0x2vCCPLQ0Vdw6w2rBVGloaE_84tRNprqNJEJiyI1unMY8Vpj2CDK9GiQGy03_RmteRz-aM31iIQcZsVZhIH2cLrne_5nY9miXKDmQqEHdY60_WopC0](https://lh4.googleusercontent.com/oR0_liUxjMnfgFS-fSuc0x2vCCPLQ0Vdw6w2rBVGloaE_84tRNprqNJEJiyI1unMY8Vpj2CDK9GiQGy03_RmteRz-aM31iIQcZsVZhIH2cLrne_5nY9miXKDmQqEHdY60_WopC0)

RUNNBALE은 쓰레드 Queue

### sleep() - 일정시간동안 쓰레드를 멈추게 한다.

- sleep()은 항상 현재 실행중인 쓰레드에 대해 작동한다.
- th1.sleep(2000)을 main에서 실행하면 th1이 아닌 main쓰레드에 영향을 준다. 따라서 참조변수보다는 Thread.sleep(2000)으로.
- 지정시간이 다 되거나 interrupt()가 호출되면 InterruptedException 발생 후 실행대기

### interrupt()와 interrupted() - 쓰레드의 작업을 취소한다.

[ThreadEx13](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx13.java)

[ThreadEx14](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx14.java)

- 진행중인 쓰레드의 작업이 끝나기 전에 취소시켜야 할 때 사용한다.

    ex) 큰 파일 다운로드 할 때, 시간이 오래걸리면 포기하고 취소가능해야한다.

- 단지 멈추라고 요청하는 것일 뿐, 쓰레드 강제 종료는 아니다.
- 쓰레드의 interrupted 인스턴스 변수 상태를 바꾸는 것 - interrupt()
- void interrupt() - interrupted 상태를 false에서 true로 변경

    boolean isInterrupted() - 쓰레드의 interrupted 상태 반환

    static boolean interrupted() - 쓰레드 interrupted 상태 반환 후, false로 변경

- 쓰레드가 sleep(), wait(), join()에 의해 '일시정지 상태(WAITING, BLOCKED)' 일 때, 해당 쓰레드에 interrupt()는 InterruptException 발생시키고 '실행 대기 상태(RUNNABLE)'로 바꾼다. 즉, 멈춘 쓰레드를 깨워 실행가능하게 변경한다.

### suspend(), resume(), stop() - Deprecated (Deadlock - suspend, stop)

[ThreadEx15](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx15.java)

- 대체하는 방법은 stopped, suspended를 boolean 인스턴스 변수로 선언하고 활용

### yield() - 다른 쓰레드에게 양보한다

[ThreadEx16](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx16.java)

- yield()와 interrupt()를 적절히 활용하면, 프로그램 응답성을 높이고 보다 효율적인 실행을 가능하게 할 수 있다.

    ex) busy wating(하는일 없이 돌고 있는 작업) 없앨 수 있다.

### join() - 다른 쓰레드의 작업을 기다린다

[ThreadEx17](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx17.java)

[ThreadEx18](https://github.com/forceson/Thread-Study/blob/master/src/com/company/ThreadEx18.java)

- 시간 지정 안하면, 해당 쓰레드가 작업을 모두 마칠 때까지 기다린다.
- interrupt()에 의해 대기상태 벗어날 수 있다.
- 특정 쓰레드에 대해 동작하므로 static은 아니다. sleep()과는 비슷하게 동작한다.
- join()은 자신의 작업 중간에 다른 쓰레드의 작업을 참여(join) 시킨다는 의미로 이름 지어졌다.

TODO : 작성한 코드 레퍼런싱하기, 다음 주제는 동기화
