# Reference
> 🙇🏻‍♂️ 스스로 다시 보려고 기록하는 내용입니다

-[한국방송통신대학교](https://www.knou.ac.kr/knou/index.do?epTicket=ST-916435-Gtok0rF7k3emwse1uu6koP06Mqt6Qcxwlau-13)

---

## 데이터베이스 언어의 필요

![](https://velog.velcdn.com/images/urtimeislimited/post/9589a3d6-2293-4511-9093-f3d9b43f5390/image.png)


- 데이터베이스 시스템이 하는 일: 데이터 관리와 데이터 사용에서 발생할 수 있는 문제를 원천적으로 차단하기 위해 분리하고 중지하는 것
- DBMS에게 명령

---

### SQL의 개요

- Structured Query Language는 관계대수에 기초하여 RDBMS의 데이터 관리를 위해 설계된 언어
- 특징
	- 선언헝(비절차적)
	- 필요한 데이터만 기술
	- 인간의 언어와 매우 유사
	- 간단 명료

---

### SQL의 구성

1. 데이터정의언어 (DDL)

- 데이터베이스내의 객체를 생성 및 삭제하고 그 구조를 조작하는 명령어의 집합
- 데이터가 준수해야하는 제약조건을 기술
- CREATE, ALTER, DROP문 등

2. 데이터 조작언어 (DML)

- DDL에 의해 정의된 테이블에 데이터를 조작하는 명령어의 집합
- 데이터에 대한 CRUD 명령을 포함
- INSERT, UPDATE, DELETE, SELECT

---

### 데이터 정의 언어

1. 데이터베이스 객체를 생성, 삭제 또는 구조를 수정하는 명령어의 집합
2. 데이터베이스 객체의 종류

- 데이터 저장: 테이블, 인덱스, 뷰
- 데이터 조작: 트리거, 프로시저, 함수 등

3. 데이터 정의 명령어의 종류

- CREATE: 객체 생성
- ALTER: 객체 변경
- DROP: 객체 삭제

---

### 데이터 정의 언어의 구분 형식

![](https://velog.velcdn.com/images/urtimeislimited/post/f6b19549-edfd-422f-a6ee-b4fcd106f355/image.png)


- 명령어 + 객체로 명령 전달


---

### 스키마 정의

1. 스키마 === 데이터베이스
2. 한 조직의 데이터베이스 시스템의 운영이 필요한 테이블, 인덱스, 뷰 등의 데이터베이스 객체의 집합
3. 스키마 관리방법

- Forward Engineer
- SQL 에디터
- 내비게이터패널

---

### 스키마 관리 구문 형식

1. 스키마 생성

- CREATE SCHEMA 스키마명

2. 스키마 삭제

- DROP SCHEMA 스키마명
- ALTER SCHEMA는 거의 사용하지 않고, DROP후 다시 생성

---

### 테이블 정의

- 새로운 2차원 형태 테이블 생성
![](https://velog.velcdn.com/images/urtimeislimited/post/743072ea-db07-4196-b306-5e6e65276095/image.png)
![](https://velog.velcdn.com/images/urtimeislimited/post/1f087f7a-1ee9-44af-bedb-751a57b447be/image.png)


---


### 데이터 타입의 개념

1. 도메인을 결정 ( 컬럼이 가질 수 있는 값의 범위 )
2. 프로그래밍 언어에서의 변수를 생성하는 데이터 타입의 사용목적과 방법이 매우 유사
3. 기본 데이터 타입

![](https://velog.velcdn.com/images/urtimeislimited/post/ce92a072-a16a-4cb0-816e-7b162f9c7fb8/image.png)


---

### 정수 데이터 타입
![](https://velog.velcdn.com/images/urtimeislimited/post/ae4559dc-8952-4694-b8a4-a9249f00ec46/image.png)


-> 공간 효율성

---

### 실수 데이터 타입
![](https://velog.velcdn.com/images/urtimeislimited/post/07b17ca1-9078-4e0a-9613-0755a8a93982/image.png)

---

### 날짜 및 시간 데이터 타입

![](https://velog.velcdn.com/images/urtimeislimited/post/3dcc9f73-8f4d-4b20-b3b8-c9dfe3f45dc5/image.png)

---

### 문자 데이터 타입

![](https://velog.velcdn.com/images/urtimeislimited/post/629f3f43-0639-4286-a183-98fcce61e93f/image.png)

- CHAR는 공간 낭비가 존재
- VARCHAR는 검색 속도 저하, 관리의 어려움 존재
	- 4000자가 max
    ![](https://velog.velcdn.com/images/urtimeislimited/post/3cdad0d4-ef3f-46e1-9762-f227de4ba94a/image.png)


---

### 테이블 수정

1. 생성된 테이블에 컬럼을 추가, 수정 (이름, 데이터 타입, 제약조건) 또는 삭제하는 명령
2. 컬럼 삭제 또는 컬럼의 데이터 타입 수정시 데이터에 대한 소실이 발생할 수 있음
3. 테이블 수정 방법

- SQL 구문
- 내비게이터 패널
![](https://velog.velcdn.com/images/urtimeislimited/post/5be982f9-33e3-4d75-be8f-1edb9471bc94/image.png)


---

### 테이블 수정 구문 형식

1. 새로운 컬럼을 추가, 삭제 및 수정하는 등의 테이블 구조로 변경
![](https://velog.velcdn.com/images/urtimeislimited/post/8c7746ee-476e-4488-8313-b43cc1e7addd/image.png)


---

### 테이블 삭제

1. 존재라는 테이블을 스키마에서 삭제
2. 삭제할 테이블의 모든 데이터가 소실, 복구 불가능
3. 테이블 삭제 방법

- SQL 구문
- 내비게이터 패널
![](https://velog.velcdn.com/images/urtimeislimited/post/38d5d06f-9580-4ab7-bf24-f26c74e0c837/image.png)

---

### 제약조건

1. 테이블과 테이블에 존재하는 데이터를 보다 무결하게 관리 목적
2. 테이블 조작시 정의된 제약조건 만족여부 지속적 검사
3. 다양한 제약 조건 지원

---

### 제약조건의 종류

![](https://velog.velcdn.com/images/urtimeislimited/post/211359c2-794d-4d25-909c-a814bd9562b9/image.png)
![](https://velog.velcdn.com/images/urtimeislimited/post/89f0a628-14f9-46a2-a872-5b1dc5d8aea9/image.png)
