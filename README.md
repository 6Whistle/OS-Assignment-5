# 운영체제 Assignment 5

### 이름 : 이준휘

### 학번 : 2018202046

### 교수 : 최상호 교수님

### 강의 시간 : 금 1, 2


## 1. Introduction

```
해당 과제는 I/O Zone을 이용하여 Linux I/O scheduler의 성능을 측정한다. ㅁ측정하는
scheduler는 noop, deadline, CFQ로 3 가지이며, scheduler 별 성능 결과 표 및 그래프를 작
성한다. iozone의 테스트 연산은 4 가지로 write/re-write, read/re_read, random-read/write,
이외의 다른 연산 하나를 수행하여 검사한다. record size를 8KB ~ 16MB까지 변화시켜가
며, thread는 1 개, File size는 1GB, Buffer cache를 거치지 않고 연산을 수행하여 결과를 엑
셀을 통해 확인한다.
```
## 2. Conclusion & Analysis

## A. noop

다음 명령을 통해 I/O zone을 설치하는 모습이다. 해당 package는 다양한 test를 이용하여 I/O
의 성능을 측정할 수 있는 도구다.


해당 파일은 Makefile로 각 record size에 해당하는 iozone을 5 번 test하기 위해 만들어졌다.
sched 변수에 현재 수행하는 스케줄러명을 입력하며 record_size에 검사할 파일명을 입력한다.
또한 file_size에는 파일 크기를 입력한다.

명령으로 다음을 5 회 반복한다.
우선 rm -rf ~/iozone_test를 통해 process의 파일을 지운다. 그 후 sync를 통해 file system
buffer를 flush한다. 그 후에는 echo 3 | ~ 명령을 통해 pagecache, dentries, and inode를 수행한
다. 그 후 iozone 명령을 수행한다. - R 옵션을 통해 Excel report를 생성하며 - i 0 1 2 8 옵션을
통해 실행할 test를 지정한다. 0 은 write/re-write, 1은 read/re-read, 2은 random-read/write, 마지
막으로 **8 은 필자가 사용한 random-mix test로 전체적인 성능을 평가하기 위하여 해당 test를
사용하였다.** - I 옵션을 사용하여 cache를 거치지 않으며, - r 옵션을 통해 변수로 넣은 record size
로 크기를 지정하고 - s 옵션은 파일 크기로 1GB로 설정한다. - t 1 옵션을 사용하여 thread를 1
개 사용하며, - F 옵션을 통해 process가 생성할 파일의 이름를 지정한다. 마지막으로 - b 옵션을
사용하여 binary file의 path를 지정한다. 파일명은 각각 1~5까지의 숫자로 부여한다.


Echo noop | sudo tee ... 명령을 통해 현재 사용하고 있는 I/O scheduler를 noop으로 변경하였
다. noop scheduler는 인접한 요청 병합만 수행하고 그 외에 아무 작업을 하지 않는 방법을 사
용하며, Requeset FIFO의 Queue만을 유지한다. 이는 Random access하는 device를 위한 스케줄
러로 탐색에 대한 부담이 없는, 즉 정렬이 필요가 없다. 이는 플래시 메모리에서 주로 사용하는
방법이다.

다음 결과는 make를 통해 각 record size 별로 5 번식 수행하여 파일로 만든 모습이다. 위와 같
이 정상적으로 파일이 만들어졌다.


다음은 파일의 내용을 가져온 것이다. 각 파일에서 Initial write/Rewrite는 0 번 test, Read/Re-
read는 1 번 test, Random read/write는 2 번 test, 마지막으로 Mixed workload는 8 번 test의 결과
를 의미한다.



위의 Excel 파일을 하나로 정리하여 평균을 보았을 때 다음과 같은 결과를 보였다.

다음 그래프는 위의 표를 보기 쉽게 정리한 것이다. 일반적으로 write 동작이 read 동작에 비해
Kbytes/sec가 더 낮게 나오는 것을 확인할 수 있으며 이 차이는 record size가 커질수록 더 심
해진다. 또한 record size가 커질수록 성능이 더 좋아지다 8 MB를 기점으로 이보다 더 클 경우
읽기 성능의 경우 소폭 하락하는 경향을 확인할 수 있다. 또한 noop 스케줄러의 장점이기도
한 random read/write의 성능이 일반적인 read/write와 유사하게 나오거나 더 좋게 나오는 것을
확인할 수 있다.

## B. Deadline

```
0
```
```
500000
```
```
1000000
```
```
1500000
```
```
2000000
```
```
2500000
```
```
3000000
```
```
Initial write Read
Random read Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
8k8k8k8k8k8k8k16k16k16k16k16k16k16k32k32k32k32k32k32k32k64k64k64k64k64k64k64k128k128k128k128k128k128k128k256k256k256k256k256k256k256k512k512k512k512k512k512k512k8m8m8m8m8m8m8m16m16m16m16m16m16m16m
```
## noop

```
계열 6
```

이전과 같은 방법으로 이번에는 deadline으로 scheduler를 변경하였다. deadline scheduler는 4
개의 Queue를 사용하는 scheduler다. 각각 elevator’s read/write, FIFO read/write로 이루어져 있
다. Deadline이 지난 요청이 없을 경우에는 정렬된(elevator) Queue에서 요청을 꺼내 처리한다.
즉 이는 모든 request가 마감 시간을 가짐을 의미하며, 마감시간은 읽기 요청의 경우 500ms,
쓰기 요청의 경우 5 s의 값을 가진다. 이를 통해 starvation을 방지하며, 읽기 요청의 deadline이
짧기 때문에 이는 읽기 우선 정책으로 분류된다.


이후 Makefile에서 sched를 deadline로 하여 이름을 설정하고 record size를 8k에서 16 m까지
바꾸어가며 5 번씩 명령을 수행하는 메크로로 바꾸었다.

(^)
해당 make 명령을 각 record_size에 대하여 수행하였고 위와 같은 excel 파일을 얻었다. 또한
내용이 정상적으로 작성되어 있는 것을 확인할 수 있었다.



위의 Excel 파일을 하나로 정리하여 평균을 보았을 때 다음과 같은 결과를 보였다.

다음 그래프는 위의 표를 보기 쉽게 정리한 것이다. 일반적으로 write 동작이 read 동작에 비해
Kbytes/sec가 더 낮게 나오는 것을 확인할 수 있으며 이 차이는 record size가 커질수록 더 심
해진다. 또한 record size가 커질수록 성능이 더 좋아지다 8 MB를 기점으로 이보다 더 클 경우
읽기 성능이 소폭 하락하는 경향을 확인할 수 있다. 또한 대부분의 경우에서 random
read/write의 성능이 일반적인 random read/write 성능을 이기지 못하는 것을 확인할 수 있다.
Mixed workload의 경우 read의 성능과 유사하게 나오는 것을 추가적으로 확인할 수 있다.

### C. CFQ

```
0
```
```
500000
```
```
1000000
```
```
1500000
```
```
2000000
```
```
2500000
```
```
3000000
```
```
Initial write Read
Random read Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
8k8k8k8k8k8k8k16k16k16k16k16k16k16k32k32k32k32k32k32k32k64k64k64k64k64k64k64k128k128k128k128k128k128k128k256k256k256k256k256k256k256k512k512k512k512k512k512k512k8m8m8m8m8m8m8m16m16m16m16m16m16m16m
```
## deadline

```
계열 6
```

현재 I/O zone은 CFQ로 설정되어 있다. 이는 linux의 기본 scheduler로 입출력을 요청하는 모든
프로세스들에 대해 디스크 I/O 대역폭을 공평하게 할당하는 것을 보장하는 기법이다. 이는 I/O
를 요청한 프로세스 별로 Queue를 할당하며 각 프로세스에 관한 큐는 섹터 순으로 정렬되어
있다. 내부에서 큐는 RR 방식으로 4 개씩 처리하며, 처리 크기는 설정이 가능하다.

이후 Makefile에서 sched를 cfq로 하여 이름을 설정하고 record size를 8k에서 16 m까지 바꾸어
가며 5 번씩 명령을 수행하는 메크로로 바꾸었다.


다음은 make를 통해 각 record에 대해 메크로를 수행한 결과로 cfq 파일이 정상적으로 생성되
었며 결과 또한 0, 1, 2, 8번 test가 정상적으로 출력된 모습을 확인할 수 있다.



위의 Excel 파일을 하나로 정리하여 평균을 보았을 때 다음과 같은 결과를 보였다.

다음 그래프는 위의 표를 보기 쉽게 정리한 것이다. 일반적으로 write 동작이 read 동작에 비해
Kbytes/sec가 더 낮게 나오는 것을 확인할 수 있으며 이 차이는 record size가 커질수록 더 심
해진다. 또한 record size가 커질수록 성능이 더 좋아지다 8 MB를 기점으로 이보다 더 클 경우
읽기 성능이 소폭 하락하는 경향을 확인할 수 있다. 또한 대부분의 경우에서 random
read/write의 성능이 일반적인 random read/write 성능과 유사하게 나오는 것을 확인할 수 있었
다. 해당 알고리즘은 내부적으로 각 프로세스의 queue에서 RR 알고리즘에 따라 수행하기에 프
로세스가 많아질 때 해당 알고리즘이 어떻게 변하는지 더욱 세밀하게 확인이 가능할 것으로 예
상된다.

## 3. Consideration

```
해당 과제를 통해 I/O scheduler가 동작하는 원리를 이해하고, 어떠한 scheduler가 존재하
는지 알 수 있었다. 또한 이러한 각각의 scheduler에서 어떠한 장단점이 있는지를 알 수
있었다. 특히 linux에서 기본적으로 제공하는 scheduler에서 noop, deadline, CFQ의 성능
을 직접 비교해볼 수 있었다. 또한 record size가 커짐에 따라 성능이 증가하지만, 일정
크기 이상이 될 경우 read 성능이 보통 감소한다는 것을 확인할 수 있었다. 마지막으로
기본적으로 write 동작이 read 동작보다 느리다는 것을 볼 수 있었던 과제였다.
```
## 4. Reference

```
0
```
```
500000
```
```
1000000
```
```
1500000
```
```
2000000
```
```
2500000
```
```
3000000
```
```
Initial write Read
Random read Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
```
```
Rewrite  Re-read
 Mixed workload
```
```
Initial write   Read
Random read   Random write
8k8k8k8k8k8k8k16k16k16k16k16k16k16k32k32k32k32k32k32k32k64k64k64k64k64k64k64k128k128k128k128k128k128k128k256k256k256k256k256k256k256k512k512k512k512k512k512k512k8m8m8m8m8m8m8m16m16m16m16m16m16m16m
```
## CFQ

```
계열 6
```

#### - 강의자료만을 참고


