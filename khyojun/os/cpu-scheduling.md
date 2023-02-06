# CPU-Scheduler

### 스케줄러가 있는 이유!
 > CPU가 놀지 않도록 할려고 CPU에서 실행될 프로세스를 선택해주게 하는 역할!

### 디스패처도 같이 알아보자!
> 스케줄러에 의해 선택된 프로세스가 실행 될 수 있도록 CPU 할당 (EX. Context-Switching, 적절한 곳에 할당 하는 작업)

## 스케줄링의 목적
- CPU를 가능한 바쁘게 만든다.
- CPU 할당이 공정하게 되도록 한다.
- CPU 처리량은 Maximum이어야 한다.
- 최소 처리 시간, 프로세스 실행 완료하는데 걸리는 시간이 가장 짧아야 한다.
- 최소 대기 시간이 있어야 하며 프로세스 ready queue 에서 starvation이 되면 안된다.
- 최소의 응답 시간을 가져야 한다.

## 스케줄링 방식

### Non-preemptive(비선점)

> 프로세스 스스로 CPU 소유권을 포기하는 방식, 강제로 프로세스를 중지하지 않기에 context-switching으로 인한 부하가 적다.

- 신사적이게 일이 다 끝날 때까지 기다리고 그 이후에 실행 될 수 있도록 스케줄링 해주는 방식
- 장점 : 신사적, 협력적(cooperative)
- 단점 : 느린 응답성
- 방식 : FCFS, SJF, 우선순위 등이 있다.


### Preemptive(선점)


> 현대 운영체제가 쓰는 방식으로 지금 사용하고 있는 프로세스를 알고리즘에 의해 중단시켜 버리고 강제로 다른 프로세스에 CPU 소유권을 할당

- 기본적으로 Non-preemptive 방식으로 돌아간다.
- 비신사적으로 일이 다 끝날 때까지 기다리지 않고 자신이 정한 스케줄링 기준에 따라 강제로 넘기는 방식
- 장점 : 적극적, 빠른 응답성
- 단점 : 강제적, 데이터 일관성 문제가 일어남.(이 문제 때문에 mutex, semaphore ~~, 임계영역 등이 나옴)
- 방식 : RR(round-robin), SRF, Priority와 같은 방법이 있다.



> ? convoy effect(호송 효과) : CPU 사용시간이 긴 프로세스 때문에 짧은 프로세스들이 오래 기다리는 현상!
> 
> ex) p1: 30 p2 : 3, p3 : 2 이 작업을 p1,2,3 순서, p3,2,1 순서로 나눌 때 대기시간의 가장 큰 차이가 난다.
> p1,2,3의 경우 (0+33+35)/3 = 약 22ms p3,2,1의 경우 (0+2+5)/3 = 약 2ms 의 시간처럼 대기 시간의 차이가 되게 크게 나타나게 된다!

> ?starvation : 특정 프로세스의 우선순위가 낮아서 원하는 자원을 계속 할당 받지 못하는 상태이다.
> ex) preemptive에서 만약 우선순위가 2인 친구가 있는데 계속해서 중간에 자기보다 높은 친구들이 계속 오면 자신은 계속 할당을 못 받는 현상과 같다.







### FCFS(선착순)
- 먼저 온 순서대로 처리를 하게 된다.
- 길게 수행되는 프로세스 때문에 convoy effect가 일어나게 된다!

### SJF(Shortest Job First)
- 실행 시간이 가장 짧은 프로세스(CPU Burst가 가장 짧은 친구)를 우선으로 실행한다.
- 긴 시간을 가진 프로세스가 실행되지 않는 현상(starvation)이 일어난다.
- 평균 대기 시간이 짧다.
- 실제로 실행시간을 알 수 없어서 과거의 이력들을 토대로 추측하여 사용한다.

### Priority(우선순위)

- 우선순위 순으로 실행한다.
- 기존의 SJF 스케줄링에서 오래된 작업일수록 우선순위를 높이는 방법을 적용하여 실행한다.
- preemptive, non-preemptive 두 가지 관점으로 나눠서 사용하기도 한다.


### Round-Robin

- 동일한 할당 시간을 주고 그 시간 안에 끝나지 않으면 다시 ready queue로 돌아가는 방식
- 할당 시간이 너무 크면 FCFS처럼 되버린다. 짧으면 context-switching이 너무 많이 일어난다.
- 이 알고리즘은 로드밸런서에서 트래픽 분산 알고리즘으로도 쓰인다.


### SRF

- 중간에 더 짧은 작업이 들어오면 수행하던 프로세스를 중지하고 해당 프로세스를 수행하는 알고리즘


### SRTF(Shortest-Remaing-Time-First)

- 남은 CPU Burst가 짧은 프로세스를 실행한다.
- SJF + Preemptive 방식이다.

### Multilevel queue(다단계 큐)
- 우선순위에 따른 준비 큐를 여러 개 사용하고, 큐마다 RR, FCFS등 다른 스케줄링 알고리즘을 적용한다. 
- 큐 간의 프로세스 이동이 안된다.
- 스케줄링 부담이 적지만 유연성이 떨어진다.


### Multilevel Feedback Queue
- 위 다단계 큐의 유연성이 떨어지는 점을 보완해서 나온 알고리즘이다.
- 큐 간의 프로세스 이동이 된다.
- 움직일 수 있는 만큼 오버헤드가 생성이 된다.
- 가장 복잡한 알고리즘이다.




이것 말고도 더 많은 알고리즘들이 있는데 이 사이트를 참고하며 더 공부하기를 추천한다!
### 출처
> - [여러가지 스케줄링](https://www.geeksforgeeks.org/cpu-scheduling-in-operating-systems/)
> - [스케줄링 관련](https://www.youtube.com/watch?v=LgEY4ghpTJI&ab_channel=%EC%89%AC%EC%9A%B4%EC%BD%94%EB%93%9C)