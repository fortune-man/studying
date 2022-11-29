# Reference 
- [우아한 테크 세미나](https://www.youtube.com/watch?v=dJ5C4qRqAgA&t=1785s)

---
### 개요


>'설계', '의존성', '변경'은 가장 많이 사용되는 단어입니다.
읽어보고 출발하시죠.

- 주제 : 의존성을 통해 설계 진화시키기

- 설계의 핵심 -> '의존성을 어떻게 관리하느냐'

- 설계란 ? -> 코드를 어떻게 배치할 것인지에 대한 의사결정

- 어디에 어떤 코드를 둬야할까 -> '변경에 의한 초점'

- 변경의 핵심 -> 의존성

- Dependency(의존성)이란? 
->__변경에 의한 영향을 받을 수 있는 가능성__

---


### 클래스 의존성

![class_dependency](https://velog.velcdn.com/images/urtimeislimited/post/2829d699-6a38-439d-8fe9-70b7755d1b3a/image.png)

Assosiation 연관관계
A클래스에 B로 갈 수 있는 경로를 가지고 있는 경우 (코드 상 객체 참조)
```java
class A {
	private B b;
}
```


Dependency 의존관계
파라미터, 리턴 타입에 해당 타입이 나오거나 메서드 내부에서 해당 타입 인스턴스를 생성하는 경우
(협력 시점에 일시적으로 관계 맺고 👋👋)
```java
class A {
	public B method(B b) {
		return new B();
	}
}
```

Inheritance 상속 관계
B의 구현을 A가 계승 -> B의 변경이 일어날 때 A도 함께 변경
```java
class A extends B {

}
```

Realization 실체화 관계
```java
class A implements B {

}
```


### 패키지 의존성

![package_dependency](https://velog.velcdn.com/images/urtimeislimited/post/025355fe-657d-4d44-a1d5-a82928e6c762/image.png)

A 클래스 코드에 import B 패키지가 있다
-> 패키지 A가 패키지 B에 의존을 해요
-> 패키지 B에 있는 클래스가 바뀔 때  A 클래스도 변경

### 의존성 관리에 좋은 규칙
정답은 아니라고 하심. 하지만 새겨 듣자.

#### 양방향 의존성을 피하라


```java
class A {
	private B b;

	public void setA(B b) {
		this.b = b;
		this.b.setA(this);
	}
}

class B {

	private A a;

	public void setA(A a) {
		this.a = a;
	}
}

```

>B가 바뀔 때 A도 변경,
  동시에 A가 바뀔 때 B도 변경되기 때문



가급적이면 양방향 관계를 피하고 단방향으로 바꿔야 한다.

단방향
```java
class A {
	private B b;

	public void setA(B b) {
		this.b = b;
	}
}

class B {

}
```


#### 다중성이 적은 방향을 선택하라

![2](https://velog.velcdn.com/images/urtimeislimited/post/7d284125-8203-4692-b158-f4f0d7ec91a5/image.png)


```java
class A {
	private Collection<B> bs;
}
class B{

}
```
A에서 B의 컬렉션을 인스턴스 변수로 잡거나, 컬렉션에 대해의존성을 가지게 하는 것 보다는 반대 방향의 의존성을 가지도록 하는 것이 훨씬 좋다.

이를 유지하기 위해서 다양한 이슈가 발생한다. 성능이슈, 객체들의 관계를 유지하기 위한 노력들이 필요하므로

__가급적이면 다중성이 적은 방향으로 객체를 설계한다.__



```java
class A{

}

class B {
	private A a;
}
```

A가 B의 컬렉션을 가지는 것보다 B가 A의 단방향 참조를 가지는게 가장 좋다.


#### 의존성이 필요없다면 제거하라

제일 좋은 건 의존성이 불필요하다면 제거해버리는 것

```java
class A {
	private B b;
}

class B {

}
```

```java
class A {

}
class B {

}
```
```java
class A {
	private B b;
}

class B {

}
```

```java
class A {

}
class B {

}
```

#### 패키지 사이클 의존성


패키지 사이 양방향(또는 n방향)의존성 사이클이 생기지 않도록 해라.
-> __변경__이 동시에 일어나기 때문!

![2](https://velog.velcdn.com/images/urtimeislimited/post/068fc2ed-6c20-45bf-9a6b-14f5da322ec5/image.png)

> __설계의 원칙은 무조건 '변경'__
내가 배치하는 코드가 어떻게 바뀔 것인지에 대해 포커스를 맞추면 된다고 하심.

---
