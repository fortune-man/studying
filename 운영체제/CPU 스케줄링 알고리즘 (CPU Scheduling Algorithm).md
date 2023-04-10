# Reference

- [ Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/OS#스케줄러)

---

## CPU Scheduling Criteria (성능 척도)

### 알고리즘에 대한 성능평가 기준 5가지

- `프로세서 이용률(CPU Utilization)`
	- 시간당 CPU를 사용한 시간의 비율
	- 프로세서를 실행상태로 항상 유지하여 유휴 상태가 되지 않도록 합니다.
	- 가능하면 입출력(I/O) 중심의 작업보다 프로세서 중심의 작업을 실행해야 합니다.
- `처리율(Throughput)`
	- 시간당 처리한 작업의 비율
	- 단위 시간당 완료되는 작업 수가 많도록 짧은 작업을 우선 처리하거나 인터럽트 없이 작업을 실행합니다.
- `반환시간 또는 소요시간(Turnaround Time)`
	- CPU burst time(쓰고 나갈때까지의 시간, 누적되지 않음)
	- 작업이 시스템에 맡겨져서 메인 메모리에 들어가기까지의 시간, 준비 큐에 있는 시간, 실행시간, 입출력 시간 등 작업 제출 후 완료되는 순간까지의 소요시간이 최소화되도록 일괄 처리 작업을 우선 처리합니다.
- `대기시간(Waiting Time)`
	- 대기열에 들어와 CPU를 할당받기까지 기다린 시간
	- 작업의 실행 시간이나 입출력 시간에는 실제적인 영향을 미치지 못하므로 준비 큐에서 기다리는 시간이 최소화되도록 사용자 수를 제한합니다.
- `반응시간 또는 응답시간(Response Time)`
	- 대기열에서 처음으로 CPU를 얻을 때 까지 걸린 시간
	- 반응시간은 의뢰한 시간에서부터 반응이 시작되는 시간까지 간격으로 대화형 시스템에서 중요한 사항입니다. 따라서 대화식 작업을 우선 처리하고 일괄 처리 작업은 대화식 작업의 요구가 없을 때까지 처리합니다

### 주체별 CPU 성능 척도

- 시스템 입장에서의 CPU 스케줄링 성능에서 중요한 것
	- `CPU Utilization`  + `Throughput`

- 사용자 입장에서의 CPU 스케줄링 성능에서 중요한 것
	- `Turnaround Time` + `Waiting Time` + `Response Time`

---

## CPU Scheduling Algorithm

- 다음에 어떤 작업을 CPU에 할당할 것인지를 정하는 알고리즘입니다.
- 스케줄링 대상은 Ready Queue에 있는 프로세스 대상입니다.

> `선점 (preemptive)` : 우선 순위가 높은 작업이 오거나, 해당 작업이 더 우선되어야 한다고 판단되면 해당 작업에게서 CPU를 빼앗을 수 있습니다. <br>
> `비선점 (non-preemptive)` : 일단 CPU를 할당받으면 해당 프로세스가 끝날 때까지 CPU를 빼앗기지 않습니다.

### FCFS (First Come First Served)

- 특징
	- `선입선출`,  `비선점형 스케줄링`,  `비효율적`
- 단점
	- 응답시간이 길어질 수 있습니다
	- Convey Effect( 호위 효과 )
		- Short process behind long process, 하나의 프로세서 중심 프로세스가 프로세스를 떠나기를 기다리는 현상


### SJF(Shortest Job First)

- 특징
	- CPU burst time이 짧은 작업을 우선으로 할당하는 방법
	- 각 프로세스와 다음번 CPU burst time을 가지고 스케줄링에 할당
		- 일종의 우선순위 기법 priority = predicted next CPU burst time
	- 비선점형(SFJ)과 선점형(SRTF) 방식이 있음
	- 최소 평균 대기 시간 보장
- 단점
	- Starvation(기아 현상) 발생 가능성 보유

### SRTF 또는 SRF (Shortest Remaining Time First)

- 특징
	- SJF의 선점형 스케줄링 방식
	- 남은 프로세스의 burst time보다 더 짧은 process가 도착하면 CPU를 빼앗음
	- 프로세스가 새로 들어올 때마다 갱신됨
- 단점
	- Starvation
	- 새로운 프로세스가 들어올 때마다 스케줄링이 변경되므로 CPU 사용 시간(burst time)을 정확히 예측하기 어렵습니다.

### Priority Scheduling

- 특징
	- highest priority를 가진 프로세스에게 CPU를 할당
		- higest priority = smallest inteager
	- `선점형 방식` 에서는 새로 도착한 프로세스의 우선순위가 현재 실행되는 프로세스보다 높으면 CPU를 선점합니다.
	- `비선점형 방식`에서는 더 높은 우선순위의 프로세스가 들어오면 Ready queue의 head 부분에 넣습니다.
- 단점
	- Starvation
	- 무기한 봉쇄(infinite blocking) : 프로세스가 CPU를 사용할 준비가 되었지만 우선순위가 낮을 경우 무기한 대기하는 상태가 되는 것
- 해결책
	- Aging
	-  큐에 남아있었던 시간으로 가중치를 부여해, 해당 문제점을 해결할 수 있습니다.
	- 우선순위가 낮은 프로세스라도 오래 기다리면 우선순위를 높여줍니다.


### RR(Round Robin)

- 특징
	- 현대식 기법으로 시분할 시스템을 위해 설계되었습니다.
	- 선점형 스케줄링 기법
	- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가지고, 할당 시간이 지나면 프로세스는 선점 당하고 Ready queue의 맨 뒤로 가게 됩니다.
	- n개의 프로세스가 Ready queue에 있고 할당시간이 q time unit이라면 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻습니다.
		- 어떠한 프로세스도 q time unit 이상 기다리지 않습니다. 공정한 알고리즘입니다.
	- CPU 사용시간이 랜덤할 때 더욱 효율적입니다.
	- 문맥 교환을 통해 프로세스의 Context를 저장할 수 있기 때문에 가능한 기법입니다.
	- 평균 대기 시간(Average Waiting Time)은 조금 길어질 수 있으나, 응답시간(Response)이 짧아집니다.
- 단점
	- q time unit이 커지면 FCFS와 같아져 비효율적일 수 있습니다. <br>
	- q time unit이 지나치게 작으면, 문맥 교환이 대량으로 발생해 오버헤드가 증가합니다.
