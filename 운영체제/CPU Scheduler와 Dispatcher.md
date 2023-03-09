
# Reference
- [Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner)
---

## CPU Scheduler와 Dispatcher

#### 프로세스를 스케줄링 하기 위한 큐 (Queue)

프로세스는 실행되는 동안 다양한 스케줄링 큐 사이를 이동하는데, 운영체제는 이러한 스케줄링 큐에서 프로세스들을 선택하기 위해 스케줄러를 사용합니다.

- `Job Queue` : 현재 시스템 내에 있는 모든 프로세스의 집합
- `Ready Queue` : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- `Device Queue` : I / O device의 처리를 기다리는 프로세스의 집합

#### CPU 스케줄러 (Scheduler)

- 스케줄링은 시스템의 목표를 달성할 수 있도록 프로세서(CPU)를 할당하는 일련의 과정입니다.
- 스케줄링이 필요한 경우
	- Running - > Blocked (ex: I / O 요청에 의한 System call) `비선점`
	- Running -> Ready (ex: timer interrupt) `선점`
	- Blocked -> Ready (ex: I / O 완료에 의한 하드에어 interrupt) `선점`
	- Terminate -> `비선점`
		- 비선점 : 자진해서 CPU를 반납하고 문맥 교환
		- 선점 : CPU를 빼앗기고 문맥 교환
- 스케줄러는 Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 결정하는 프로그램입니다.

### 장기 스케줄러 (Long-term scheduler or job scheduler)

- 시작 프로세스 중 어떤 것들을 Ready Queue에 보낼지 조정합니다.
- 메모리에 올라가 있는 프로세스의 수를 제어합니다.
	- degree fo Multiprogramming 제어
- 시분할 방식에는 장기 스케줄러가 없습니다.
	- 모든 작업이 무조건 Ready 상태
- 프로세스의 상태 변화
	- new -> ready (in memory)

### 단기 스케줄러 (Short-term scheduler or CPU scheduler)

- 어떤 프로세스를 다음 번에 running 시킬지 결정합니다.
- 프로세스에 CPU를 할당(scheduler dispatch)하는 문제를 다루는 스케줄러입니다.
- 처리 속도가 ms 단위로 매우 빠릅니다.
- 프로세스의 상태 변화
	- ready -> running -> waiting -> ready

### 중기 스케줄러 (Medium-term scheduler or Swapper)

- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냅니다.
- 프로세스에서 memory를 빼앗는 문제를 다루는 스케줄러 입니다.
- 메모리에 올라가 있는 프로세스의 수를 제어합니다.
	- degree of Multiprogramming 제어
- 프로세스의 상태 변화
	- ready -> suspended
	- suspended 상태에서는 외부적인 이유로 디스크의 swap area로 쫓겨난 것이기 때문에 스스로 ready 상태가 될 수 없습니다.

![s](https://velog.velcdn.com/images%2Fss-won%2Fpost%2Fad1865fe-d8e9-4dfa-910b-2308166ac0ca%2Fimage.png)

---

## Dispatcher

- CPU의 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘깁니다.
- CPU 스케줄러 내부에 포함된 것으로, 단기 스케줄러가 선택한 프로세스에 실질적으로 프로세서를 할당하는 역할을 합니다.
- 프로세스의 레지스터를 적재하고(문맥 교환), 운영체제 모드(Kernel Mode)에서 사용자 상태(User Mode)로 전환시켜주며 프로세스가 다시 시작할 때 사용자 프로그램이 올바른 위치를 찾을 수 있도록 합니다.
	- dispatcher의 처리속도가 빠르면 빠를 수록 좋습니다.
