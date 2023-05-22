# Reference
> 🙇🏻‍♂️ 스스로 다시 보려고 기록하는 내용입니다

-[한국방송통신대학교](https://www.knou.ac.kr/knou/index.do?epTicket=ST-916435-Gtok0rF7k3emwse1uu6koP06Mqt6Qcxwlau-13)

---

## 데이터 삽입, 수정, 삭제

### INSERT

1. 테이블에 새로운 레코드를 삽입하는 명령문

- 테이블에 새로운 레코드 삽입
- 모든 속성 또는 부분 속성에 대한 속성값 삽입
```
INSERT INTO 테이블명 VALUES(값1, 값..)

INSERT INTO 테이블명(컬럼1, 컬럼2..) VALUES(값1, 값..)
```

- 각 컬럼에 대응하는 값을 삽입 (컬럽1 = 값1)

---

### UPDATE

- 조건을 만족하는 레코드 특정 컬럼값을 수정

```
UPDATE 테이블명 SET 컬럼1=값1(컬럼2=값2..) WHERE 조건
UPDATE 테이블명 SET 컬럼1=수식1(컬럼2=수식2..) WHERE 조건
```

---

### DELETE

1. 조건에 일치하는 레코드 집합을 테이블에서 삭제
```
DELETE FROM 테이블명 (WHERE 조건)
```
- 조건 없으면 테이블 전체 삭제
- 삭제 안될 때 삭제하려는 것의 기본키를 참조하는 테이블이 있는지 확인해야 한다.
- 참조 무결성 제약조건

---

### SAFE UPDATES 모드

1. WHERE 절이 없는 UPDATE/DELETE 문은 테이블의 전체 레코드를 변경 / 삭제
2. 의도하지 않은 데이터 변경 / 삭제 방지 위해 MySQL은 SAFE UPDATES 모드를 지원
3. 기본키가 아닌 컬럼을 대상으로 수정/삭제 조건을 명시할 경우 실행 여부를 결정

```
SET SQL_SATE_UPDATES = 0 / 1
```
0 = false / 1 = true

---

## 데이터 검색 (1)

### SELECT문 구문형식

1. 한개 이상의 테이블에서 주어진 조건에 만족하는 레코드를 출력하는 명령문
2. 관계 대수의 셀렉션, 프로젝션, 조인, 카티션 프로덕트 연산자의 기능을 모두 포함하는 명령문
3. 필수절인 SELECT 절과 부과적인 목적으로 사용할 수 있는 여러 절을 혼합하여 검색 기능을 구체화

---

### SELECT문 각 절의 기능

1. SELECT절: 결과에 포함되는 컬럼을 지정
2. FROM절: 질의를 적용할 테이블을 지정
3. ON/WHERE: 조인 조건/검색할 레코드 조건을 지정
4. GROUP BY: 레코드를 그룹화하기 위한 그룹 조건을 지정
5. HAVING절: GROUP BY 절이 적용된 결과에 대한 조건을 지정
6. ORDER BY절: 검색 결과의 정렬 기준을 지정

---

### 단순질의문

1. 레코드를 제한하지 않고 전체 테이블을 검색하는 SELECT문으로 WHERE절이 없음

```
SELECT 컬럼1, 컬럼2.. 컬럼n FROM 테이블
SELECT * FROM 테이블 -> 전체
SELECT DISTINCT 컬럼 FROM 테이블 -> 중복제거
```

---

### 조건질의문

1. 산술연산식 함수 등을 사용하여 표현한 조건을 WHERE 절에 깃루하여 조건을 만족하는 레코드만 검색하는 SELECT 문

- 산술연산자, 비교연산자, 비교연산자

2. WHERE절은 UPDATE, DELETE 문에서도 동일하게 적용

---

### 산술연산자
![](https://velog.velcdn.com/images/urtimeislimited/post/d71344a3-3c6f-4c1f-9186-adb7c76ac75d/image.png)


---

### 비교연산자
![](https://velog.velcdn.com/images/urtimeislimited/post/a52546dc-50b9-4d5d-a959-7a56c87a7a24/image.png)

---

### 논리연산자
![](https://velog.velcdn.com/images/urtimeislimited/post/b1f4008d-d6bb-4410-9f2c-161e652296ec/image.png)

---

### 데이터 정렬

1. ORDER BY 절을 사용
2. 검색 결과를 특정 컬럼에 대해 오름차순 또는 내림차순으로 정렬
- 오름차순: ASC
- 내림차순: DESC

```
SELECT문 형식.. ORDER BY 컬럼1 ASC/DESC.. 컬럼n
ASC/DESC 
```

---

### 특수연산자
![](https://velog.velcdn.com/images/urtimeislimited/post/4d25163b-9034-429d-aa07-38f044d0f553/image.png)


- 범위 포함여부, 부분 일치여부 등 관계형 데이터베이스에서만 사용되도록 고안된 연산자
- BETWEEN
![](https://velog.velcdn.com/images/urtimeislimited/post/d1643109-8577-451c-8282-aadbf29b8494/image.png)
![](https://velog.velcdn.com/images/urtimeislimited/post/23465e84-2ca7-4a23-94d8-9ab428d3a4a7/image.png)

- 특수연산자 사용할 경우
![](https://velog.velcdn.com/images/urtimeislimited/post/a70e1079-f3bc-4454-a2ba-84fcc3382b93/image.png)

---

### IN
![](https://velog.velcdn.com/images/urtimeislimited/post/fd1ce876-0cfd-4cd9-b1ad-2d8f1100f237/image.png)
![](https://velog.velcdn.com/images/urtimeislimited/post/f3bfe38a-78c6-4cf3-8fe5-56dc63e4a4c6/image.png)

- 특수연산자 사용할 경우
![](https://velog.velcdn.com/images/urtimeislimited/post/6c83aca6-8734-4fc8-b1d4-7ad1f43581b8/image.png)

### LIKE
![](https://velog.velcdn.com/images/urtimeislimited/post/01c5509e-412f-4e72-a317-e52e1c5a626e/image.png)
![](https://velog.velcdn.com/images/urtimeislimited/post/ae15397d-2c51-4086-a021-4d9d5d40655c/image.png)

- 아무것도 조회되지 않음 (완전 일치되어야만 하기 때문에)
- 특수연산자 사용할 경우
![](https://velog.velcdn.com/images/urtimeislimited/post/3a1cd37e-6c0f-4a55-b979-a611a8d54788/image.png)

---

### 함수의 개념

1. 특정 목적을 수행하도록 사전에 정의된 연산 및 기능을 수행한 후 결과값을 반환하는 명령어 집합
2. 사용 DBMS는 검색결과가 사용자에게 여러 형태로 사용되도록 여러 데이터 타입에 대한 다양한 함수를 제공 (MySQL 기준)

- 문자함수
- 숫자함수
- 날짜 및 시간함수

---

### 숫자함수
![](https://velog.velcdn.com/images/urtimeislimited/post/7b9e1346-f4d4-49d1-8061-8c9cdd74a5ec/image.png)

---

### 문자함수
![](https://velog.velcdn.com/images/urtimeislimited/post/3f769aba-ceea-41cd-bbd5-5d1372053a98/image.png)

---

### 날짜함수
![](https://velog.velcdn.com/images/urtimeislimited/post/ce3ae649-6d72-438e-a30d-7b77080e80b3/image.png)

---

### 함수의 사용

![](https://velog.velcdn.com/images/urtimeislimited/post/cc25e320-997a-49a9-9f4f-c0482c504a30/image.png)
![](https://velog.velcdn.com/images/urtimeislimited/post/848020b2-e4f7-4232-9f28-ff949d90fd33/image.png)
