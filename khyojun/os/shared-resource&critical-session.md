## 공유자원(Shared Resource)

> 공유 자원(shared resource)은 여러 프로세스가 공동으로 이용하는 변수, 메모리, 파일 등을 말한다. 

그런데 우리가 생각해야되는것이 공유된 자원을 사용한다고 하면 여러 개의 프로세스가 동시에 접근하는 경우가 생길 것이다.
그런 상황을 경쟁 상태(race condition)이라고 한다. 동시에 접근을 시도할 때 접근의 타이밍이나 순서 등이 결과값에 영향을 미치는 상황인 것이다.

예시를 보자.

1. 프로세스 A가 공유 자원에 500원을 확인한다.
2. 프로세스 B가 공유 자원의 500원을 확인하고 500원을 추가시킨다.
3. 프로세스 B는 1000원이 되는 것을 확인했다.
4. 프로세스 A가 500원을 추가한다.
5. 프로세스 A가 추가한 후 공유 자원에 있는 돈이 1000원인 줄 알았는데 1500원이 되었다?!

5번과 같은 상황이 일어날 수 있는 것이다. 

## 임계영역(Critical Session)

위와 같은 경쟁 상태에 따른 것이 생겨서 임계 영역이라는 개념이 나왔다.

방금과 같이 둘 이상의 프로세스나 스레드가 공유 자원에 접근할 때 순서등의 이유로 결과가 달라지는 영역을 임계 영역이라고 한다.

그러면 이것을 해결해야 하는데 크게 3가지가 있다. 이것을 다른 말로 동기화 기법이라고도 부른다.
- 뮤텍스
- 세마포어
- 모니터

> 동기화 : 하나의 자원 처리에 대한 처리 권한을 주거나 순서를 조정해주는 것

각각의 방법 모두 상호 배제, 한정 대기, 융통성이라는 조건을 만족한다.

위 3가지 방법의 root-메커니즘은 잠금(lock)이라는 것이다. 예시를 들자면 화장실과 같은데 A라는 사람이 화장실에 들어가면 그 사람이 나오기 전까지는 다른 사람이 들어갈 수 없게 막아놓는 것이랑 비슷하다.(동기화)

> - 상호 배제 : 한 프로세스가 임계 영역에 들어갔을 때 다른 프로세스는 들어갈 수 없다.
> - 한정 대기 : 특정 프로세스가 영원히 임계 영역에 들어가지 못하면 안 된다.
> - 융통성 : 한 프로세스가 다른 프로세스의 일을 방해해서는 안 된다.

### Mutex(상호 배재)

> 프로세스나 스레드가 공유 자원을 lock()을 통해 잠금 설정하고 사용한 후에는 unlock()을 하여 잠금을 해제하는 객체이다.

- 잠금 상태가 되면 다른 프로세스나 스레드는 잠긴 코드 영역에 접근 불가
- 잠금 해제는 그 반대
- 잠금, 잠금 해제 2가지 상태를 가짐
- Boolean 타입의 Lock 변수를 사용한다.
- 공유 자원을 사용중인 스레드가 있을 때, 다른 스레드가 공유자원에 접근한다면 Blocking 후 대기 큐로 보낸다.
- **Lock을 건 스레드만 Lock을 해제할 수 있다.**

### SpinLock

- 기본적으로 뮤텍스와 유사하다.
- Busy-waiting하며 대기 큐를 갖지 않는다.
- Mutex-nonblocking 모델로 볼 수 있다.
- mutex보다 컨텍스트 스위칭 시간이 짧거나, 멀티 코어일 때 사용

### 세마포어(Semaphore)

- 일반화된 뮤텍스이다.
- 간단히 정수 값과 두 가지 함수로 접근을 처리한다.
    - signal() : 다음 프로세스로 순서를 넘겨주는 함수
    - wait() : 자신의 차례가 올 때까지 기다리는 함수
- Lock을 걸지않은 스레드도 signal을 보내 Lock을 해제할 수 있다.
>프로세스간의 시그널(신호, Signal)을 주고받기 위해 사용되는 정수 값, 리소스의 상태를 나타내는 카운터로 세마포어는 다음 세가지 원자적인 연산만을 지원합니다.
> - initialize, decrement, increment
> - initialize : 세마포어 초기화. (음이 아닌 정수값으로 초기화)
> - decrement : 프로세스를 블록시킬 수 있습니다.
> - increment : 블록되었던 프로세스를 깨울 수 있습니다. 이 세마포어를 카운팅 세마포어 또는 범용 세마포어라고 합니다.
세마포어의 값에 따라 운영체제는 프로세스가 즉시 자원을 사용할 지, 자원이 다른 프로세스에 의해 사용 중인걸 알게 될 경우엔 일정 시간을 기다려야 합니다.
프로세스가 자원을 사용하는 동안에는 세마포어 값을 변경함으로서 다른 프로세스들이 기다리게 해야합니다.
> - 프로세스간 메시지를 전송하거나 공유메모리를 통해 특정 데이터를 공유하게 될 경우 공유 자원에 여러 프로세스가 접근하면서 문제가 발생하게 됩니다.
+ 하나의 프로세스만 자원에 접근 가능하도록 설정할 때 세마포어를 사용합니다.

- 대기 하는 방식 2가지
  - sleep wait
  - busy-waiting


>예시
> 
>107호 병실(임계 구역)에 방문객용 의자가 5개(임계구역 안의 자원) 있다고 했을 때, 간호사(세마포어)는 이 병실에 방문자가 5명(Counter 개수)만 들어갈 수 있도록 허용하고 나머지 방문객들은 밖에서 대기하도록 합니다. Counter 개수만큼 임계 구역(병실)에 접근할 수 있습니다.
이 **세마포어 Counter의 개수에 따라 1개의 경우 이진 세마포어(Binary Semaphore)**, **2개 이상의 경우 카운팅 세마포어(Counting Semaphore)라고 불립니다.** Binary Semaphore의 경우 개념적으로 Mutex와 같다고 볼 수 있습니다.


### 모니터(monitor)

> 둘 이상의 스레드나 프로세스가 공유 자원에 안전하게 접근할 수 있도록 공유 자원을 숨기고 해당 접근에 대해 인터페이스만 제공하는 것을 말한다.

위에서 설명한 세마포어나 뮤텍스는 오래된 동기화 도구이다. 그래서 자바에서는 처리할 때 모니터라는 기법을 사용한다고 한다.

모니터의 경우 큐가 2개가 있는데 베타 동기, 조건 동기의 역할을 한다.

- entry queue : critical section에 진입을 기다리는 큐
- waiting queue : 조건이 충족되길 기다리는 큐


- 베타 동기 : 배타동기의 queue는 하나의 스레드만 공유자원에 접근할 수 있게 하는 작용을 하는 공간입니다. 특정 스레드가 공유자원을 사용하는 함수를 사용하고 있으면 다른 스레드는 접근을 할 수 없고 대기해야 합니다.
- 조건 동기 : 조건 동기의 queue는 진입 스레드가 블록되면서 새 스레드가 진입가능하게 하는 공간입니다. 새 스레드는 조건동기로 블록된 스레드를 깨울 수 있습니다. 깨워진 쓰레드는 현재 쓰레드가 나가면 재진입할 수 있습니다.

세마포어를 사용하기 위해서는 임계 구역에 명시적으로 상호 배제 로직을 구현해야한다. 모니터는 이 상호 배제 로직을 추상화하고, 공유 자원 접근에 대한 인터페이스만을 제공한다. 또한 공유 자원도 외부로부터 캡슐화하여 숨긴다. 세마포어에 비해 좀 더 고수준이라고 할 수 있다. 따라서 모니터를 사용하면 상호 배제를 세마포어에 비해 구현하기 쉬우며 실수가 줄어든다. 모니터는 주로 고급 프로그래밍 언어에서 제공하는 방식이다. 자바의 synchronized 키워드도 모니터를 사용하여 구현되었다.

모니터는 모니터큐에 작업을 순차적으로 쌓아두고, 한 번에 한 프로세스/쓰레드만 임계 영역에 접근할 수 있도록 한다. 그리고 한번에 하나의 프로세스만 모니터에서 활동할 수 있도록 보장한다.


- [모니터 관련 설명 영상](https://www.youtube.com/watch?v=Dms1oBmRAlo&list=PLcXyemr8ZeoQOtSUjwaer0VMJSMfa-9G-&index=6&ab_channel=%EC%89%AC%EC%9A%B4%EC%BD%94%EB%93%9C) 꼭 참고하기!


### 출처
- [임계영역](https://about-myeong.tistory.com/34)
- [동기화 방법](https://hudi.blog/race-condition-critical-section-mutual-exclusion/#:~:text=%EB%AA%A8%EB%8B%88%ED%84%B0%EB%8A%94%20%EB%AA%A8%EB%8B%88%ED%84%B0%ED%81%90%EC%97%90,%EA%B8%B0%EB%8A%A5%EC%9D%84%20%EC%A0%9C%EA%B3%B5%ED%95%98%EB%8A%94%20%EA%B2%83%EC%9D%B4%EB%8B%A4.)
- [동기화 방법 테코톡](https://www.youtube.com/watch?v=oazGbhBCOfU&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9C%ED%85%8C%ED%81%AC)