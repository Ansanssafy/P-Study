# [Redis#1] Redis란?

- Remote Dictionary Server : 외부의 Key-Value 형태의 서버
- **Database**, **Cache**, Message Broker
- In-memory Data Structure Store

## 특징
- Performance : 메모리에 저장되어 빠른 속도로 접근 가능
- Single Thread : 한 번에 하나의 명령만을 처리
- Data Structure : 다양한 자료 구조 제공
- Persistence : 메모리에 저장된 데이터를 디스크에 영속화 가능

## Data Structure

#### Collection
- String (Java의 Map.Entry)
- List (Java의 LinkedList)
- Set (Java의 HashSet)
- Sorted Set (Java의 TreeSet)
- Hash(Java의 HashMap)

**🤚Java의 HashMap 역시 In-Memory인데, 왜 Redis를 사용?**

Java의 경우

1. 여러 대의 서버를 가질 경우 Consistency 문제 발생
2. Multi-Thread 환경에서 Race Condition 발생

    > _[Race Condition이란?]_
<br>
<br>- 둘 이상의 입력 및 조작 타이밍, 순서 등이 결과값에 영향을 줄 수 있는 상태
<br>- 즉, 공유 자원을 둘 이상의 스레드가 읽거나 쓰며 접근이 어떤 순서에 따라 이루어졌는지에 따라 결과값이 의도와 달라지는 문제
<br>- 더 쉽게 설명하자면 두 개의 스레드가 하나의 자원을 놓고 서로 사용하려고 경쟁하는 상황

Redis의 경우

1. 기본적으로 Single Thread 환경
2. Atomic Critical Section에 대한 동기화 제공 : 특정 작업이나 명령을 수행되는 동안 방해받지 않음을 보장

    > _[Critical Section이란?]_<br> - 프로세스 여러 개가 동시에 접근하면 안되는 영역

3. 서로 다른 Transaction Read/Write를 동기화 : 원치 않는 결과를 막도록 기본적인 구현이 되어 있음

## 활용시 주의할 점
- Single Thread이므로 사용시 시간 복잡도를 고려
- In-Memory 특성상 메모리 파편화, 가상 메모리에 대한 이해 필요