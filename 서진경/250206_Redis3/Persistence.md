# [Redis#3] Back-up

Redis는 In-memory DB이지만, RDB, AOF 총 두 가지 방식으로 백업을 지원함

## RDB

#### 1. 개념
- **특정 시점**을 스냅샷으로 백업하는 방식
    > _[스냅샷(Snapshot)이란?]_
<br>- 순간적으로 메모리에 있는 내용을 떠서 바이너리 파일로 디스크에 옮겨담는 방식
- 일반적으로 dump.rdb 파일로 저장됨
- 비교적 작은 사이즈의 백업 파일 → 서버 시작시 빠른 로딩 속도
- 바이너리 파일이기 때문에 사람이 읽을 수 없음

#### 2. 주의할 점
- Snapshot 추출에 오랜 시간 소요 → 도중 서버 다운시 이후의 모든 데이터 소멸

#### 3. 설정 방법
| 항목 | 옵션 | 설명 |
|--------|--------|--------|
| **save** | 900 1<br> 300 10<br> 60 10000 | - N초 이후 M개 이상의 쓰기 발생시 저장 수행 |
| **stop-writes-on-bgsave-error** | yes | - 스냅샷 진행 도중 쓰기 요청 수행 중단 옵션 |
| **rdbcompression** | yes | - dump.rdb 파일을 LZF로 압축 |
| **rdbchecksum** | yes | - CRC64로 checksum 값 생성 후 dump.rdb 파일에 추가<br> - 데이터 영속성 보장 강화 옵션 (+성능 오버 10% 발생) |
| **dbfilename** | dump.rdb | - 파일명 설정 |
| **dir** | ./ | - 백업 파일 생성 위치 |

#### 4. 설정 예시 (redis.conf)
```ini
# 저장할 RDB 파일명
dbfilename backup.rdb
# 15분 안에 최소 1개 이상의 key가 변경 되었을 때
save 900 1
# 5분 안에 최소 10개 이상의 key가 변경 되었을 때
save 300 10
# 60초 안에 최소 10000개 이상의 key가 변경 되었을 때
save 60 10000
# RDB 저장 실패 시 write 명령 차단 여부
stop-writes-on-bgsave-error no
```

#### 5. 복구 방법
1. Redis의 데이터 경로에 dump.rdb를 넣고 재시작
2. dump.rdb 파일의 시점으로 데이터가 복구됨

## AOF

#### 1. 개념
- 모든 쓰기 명령에 대해 **현재까지의** 로그를 백업하는 방식
- 일반적으로 appendonly.aof 파일로 저장됨
- 큰 사이즈의 백업 파일 → 서버 시작시 느린 로딩 속도
- 텍스트 파일이기 때문에 읽을 수 있으며 수정 또한 가능함
    > _[수정 활용 방식?]_
<br>
flushall 명령 → 모든 데이터 손실 → appendonly.aof 파일에서 flushall 명령어 제거 후 서버 재시작 → 장애 복구 가능

#### 2. Rewrite

- AOF 파일을 최적화하는 방법

    **🤚AOF의 경우 왜 최적화가 필요한가요?**
    1. AOF 방식은 모든 명령을 수행하고 기록하기 때문에 로그 파일의 크기가 무한대로 커질 수 있다는 단점이 존재함
    2. 예를 들어, 하나의 Key에 대해 100회의 쓰기 작업을 진행했다면 dataset에는 최종 값에 해당하는 값만 들어 있지만 AOF에는 100개의 항목이 있고 99개는 현재의 상태를 재구성하는데 필요하지 않음 
-  Rewrite 기능을 수행한다면, 여러 번 기록된 이전 데이터는 사라지고 현재 남아있는 키에 대한 최종 데이터만 남음

#### 3. 설정 방법
| 항목 | 옵션 | 설명 |
|--------|--------|--------|
| **appendonly** | yes | - AOF 사용 여부 |
| **appendfilename** | appendonly.aof | - 파일명 설정 |
| **appendfsync** | always<br> everysec<br>no  | - 디스크와의 동기화 처리 방식 |
| **no-appendfsnyc-on-rewrite** | no | - AOF 수행 중 fsync() 호출 블로킹 여부 |
| **auto-aof-rewrite-percentage** | 100 | - rewrite를 진행할 AOF 파일의 기준 사이즈 |
| **auto-aof-rewrite-min-size** | 64mb | - rewrite가 필요없는 기준 사이즈 |

> _[fsync()란?]_
<br>- AOF 방식에서 새로운 변경 사항은 바로 디스크에 저장되는 것이 아닌, 메모리 버퍼에 저장됨<br>- 이후 fsync() 호출을 통해 이 기록이 실제 디스크로 옮겨짐

#### 4. 설정 예시 (redis.conf)
```ini
# AOF 사용 여부
appendonly yes
# 저장할 AOF 파일명
appendfilename appendonly.aof
# 디스크와 동기화 처리 방식
# - always : AOF 값을 추가할 때마다 fsync를 호출해서 디스크에 쓰기
# - everysec : 매초마다 fsync를 호출해서 디스크에 쓰기
# - no : OS가 실제 sync를 할 때까지 따로 설정하지 않음
appendfsync everysec
```

#### 5. 복구 방법
1. Redis의 데이터 경로에 appendonly.aof를 넣고 재시작
2. AOF 파일에 기록된 명령어들이 _순차적으로 실행되며_ 상태가 복구됨


## 그래서 RDB? AOF?

- AOF 방식을 기본으로 하되, RDB를 섞어서 사용
- RDB는 특정 시점에 종속되며, 영속화 도중 문제가 생긴다면 작업이 중지되어 데이터가 유실될 위험이 너무 크다고 여겨짐


### 마지막으로 ..
---

**🤚Redis Replication과의 차이점?**

- 영속화는 말 그대로 데이터의 손실을 막고 영구적으로, 장기적으로 보존하기 위한 방법
- 복제는 서버가 하나일 경우 발생하는 장애를 대응하기 위해 다른 서버로 데이터를 복제하는 것

➡️ 즉, _Persistence_ 는 데이터의 **영속성**을 _Replication_ 은 데이터의 **가용성** 및 **확장성**을 목적으로 둠