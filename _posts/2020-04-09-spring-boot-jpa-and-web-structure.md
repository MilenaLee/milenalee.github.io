---
layout: post
title: "[Spring Boot] JPA, Spring web structure"
subtitle: '공부한 내용 정리'
author: "Mihyun"
header-style: text
tags:
- spring
- spirng boot
- JPA
- study
---


[스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://book.naver.com/bookdb/book_detail.nhn?bid=15871738)라는 책을 공부하고 있고
[이 레포](https://github.com/MilenaLee/study-springboot2-webservice)에 실습한 내용들을 저장하고있다ㅇ_ㅇ
아래는 3장 공부 정리내용

### JPA
- 자바 표준 ORM(Object Relational Mapping) 로서 객체를 매핑한다
- 인터페이스로서 자바 표준명세서이다. 인터페이스인 JPA를 사용하기 위해서는 구현체가 필요하다.
   - 구현체 예시: Hibernate, Eclipse Link ..
- 스프링에서는 구현체들을 좀 더 쉽게 사용하고자 추상화시킨 Spring Data JPA라는 모듈을 이용하여 JPA 기술을 다룬다.
   - JPA <- Hibernate <- Spring Data JPA
- Spring Data JPA가 등장한 이유 두 가지
   - 1. 구현체 교체의 용이성
   - 2. 저장소 교체의 용이성

### Spring 웹 계층
![Spring 웹 계층 그림설명](https://sehun-kim.github.io/sehun/assets/images/spring-web-app-architecture.png)
1. Web Layer
   - 뷰 템플릿 영역. 외부 요청과 등답에 대한 전반적인 영역
2. Service Layer
   - Controller와 DAO 중간 영역에서 사용. ```@Transactional```이 사용되어야 하는 영역
3. Repository Layer
   - DB와 같이 데이터 저장소 접근하는 영역
4. DTOs
   - Dat Transfer Object. 계층 간에 데이터 교환을 위한 객체.
5. Domain Model
   - 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화시킨 것
   - 비지니스 처리 담당해야 할 곳
​
