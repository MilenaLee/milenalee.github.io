---
layout: post
title: "CPU, 메모리, 스레드 개념 정리"
subtitle: '애플리케이션 성능 최적화를 위한 CPU, 메모리, 스레드의 핵심 개념 이해'
author: "Mihyun"
header-style: text
tags:
  - cpu
  - memory
  - thread
  - system
  - resource management
  - java
---

## CPU
### cpu core
- 데이터를 처리하는 단위
- 하나의 코어가 많을수록 동시에 처리할 수 있는 작업량이 늘어나며, 멀티코어 CPU는 여러 작업을 병렬로 처리하는 데 유리함
- 일반적으로 CPU 사용률이 80~90% 이상 지속되면 높은 편이라고 판단함. CPU가 과부하되면 응답 지연이 발생할 수 있음. 특히, 복잡한 계산이나 병렬 처리를 많이 수행하는 서비스라면 CPU가 충분히 처리할 수 있는지 고려 필요. 

### CPU-0
- 일반적으로 첫 번째 CPU 코어를 의미
- CPU-0이 주로 기본 작업을 담당하는 코어로 설정되는 경우가 많아 CPU-0을 대표로 보는 경우도 있음

### 모니터링 지표
- CPU 사용률
   - 전체 사용률뿐 아니라 각 코어의 사용률도 체크 필요
- 로드 애버리지 (Load Average)
   - 서버가 얼마나 바쁜지 나타내며, CPU 코어 수에 비해 로드가 과도하면 병목이 발생할 수 있음
   - `uptime`이나 `top` 명령어를 사용해 로드 애버리지(Load Average)를 확인 가능
      - `load averages: 1.79 2.26 3.22`: 1분, 5분, 15분 동안의 평균 부하
   - 로드 애버리지 수치가 CPU 코어 수보다 높으면 과부하 상태로 본다. 예를 들어, 4코어 CPU에서 로드가 4.0을 넘으면 과부하로 간주 


### 관련 장애
- CPU 스로틀링
   - CPU가 과열되거나 과도하게 사용될 때 성능을 강제로 낮춰 열을 줄이기 위해 속도를 제한하는 것을 말한다.
   - 과도한 CPU 사용은 CPU 스로틀링을 유발해 서비스가 느려지거나 응답이 지연될 수 있음
   - `dmesg` 명령어에서 CPU 스로틀링 관련 메시지를 확인해서 발생 여부를 확인할 수 있다

### 자원 조정 방법
- 서버 스케일링(확장)
- 스레드 풀 조정
- 작업 스케줄링 최적화 등

### 추가 공부
- '컴퓨터 아키텍처'와 같은 기초 자료에서 CPU와 코어 개념 찾아보기
- '운영체제' 책의 CPU 스케줄링 부분 참고하기

## 메모리 (RAM)
- 애플리케이션이 실행 중 필요한 데이터를 임시로 저장하는 공간
- 메모리가 충분하지 않으면 애플리케이션이 느려지거나 멈출 수 있다

### 모니터링 지표
- 메모리 사용률
   - 애플리케이션이 차지하는 메모리와 시스템 전체의 사용률을 주기적으로 확인
- GC(Garbage Collection) 시간
   - 특히 Java 등에서는 GC 시간 증가가 메모리 사용 문제를 암시할 수 있음
   - `jstat` 명령어로 GC 횟수와 시간 등의 지표를 확인하거나, 애플리케이션 실행 시 GC 로그 옵션(-Xlog:gc*)을 추가하여 상세 로그를 기록할 수 있다
   - Young GC는 자주, 빠르게 발생하고, Full GC는 드물고 짧게 발생하도록 조정하는 것이 이상적
      - Young GC는 초당 1회 미만, 1초 미만의 시간에 완료되는 것이 좋음
      - Full GC는 10분에 1회 이하, 1초 이하의 시간이 소요되는 것이 좋음

### 관련 장애
- 메모리 누수나 메모리 사용률 급증에 주의
   - 메모리 누수로 인해 메모리가 계속 증가할 수 있음
   - 메모리가 꽉 차면서 `Out of Memory (OOM)` 에러 발생할 수 있음
- 메모리 누수 확인 방법
   - Java의 경우, `jmap` 명령어로 힙 덤프를 생성한 뒤 `JVisualVM`, `Eclipse MAT`와 같은 툴을 사용하여 힙 덤프(heap dump)를 분석하고, 사용된 객체 중 메모리에서 해제되지 않고 남아있는 객체를 추적할 수 있다
   - 

### 자원 조정 방법
- 메모리 확장(스케일업)
- 캐시 관리와 같은 최적화 기법
- 리소스 관리 최적화와 객체 참조 관리로 메모리 누수 방지

### 추가 공부
- '운영체제' 책의 메모리 관리 챕터
- '컴퓨터 시스템 개론' 같은 자료에서 메모리 구조와 메모리 관리 방식 찾아보기


## 스레드 (Thread)
- 프로세스 내에서 실행되는 작은 작업 단위
- 스레드 수를 적절하게 관리하지 않으면 과부하가 발생하거나, 스레드가 너무 많이 생성되어 오히려 시스템 성능이 저하될 수 있음

### 모니터링 지표
- 스레드 수 및 상태
- 큐 대기 시간
   - 스레드 풀이 사용되는 작업 대기 시간을 체크해 적절한 수준인지 확인
    - 프로메테우스 지표 수집 혹은 Java 애플리케이션의 경우 `jstack` 명령어를 통해 스레드 상태를 확인할 수 있음. 특정 라이브러리에서는 스레드풀의 큐 대기 시간도 측정 가능

### 관련 장애
- 스레드 풀 고갈
- 스레드 락 등

### 자원 조정 방법
- 대기 시간이 길거나 스레드 풀이 지속적으로 포화 상태라면 **스레드 풀 사이즈 조정**이나 **작업 부하 분산**을 고려해 CPU와 메모리를 효과적으로 사용할 수 있도록 조정

### 스레드풀
- 다수의 스레드를 미리 만들어 놓고 필요할 때 가져와서 사용, 작업이 끝나면 반납하는 방식. 자원을 효율적으로 사용할 수 있음

### 추가 공부
- '자바'나 '파이썬' 같은 언어의 멀티스레딩 예제 코드
- '운영체제' 책의 스레드 관리와 동기화 챕터
