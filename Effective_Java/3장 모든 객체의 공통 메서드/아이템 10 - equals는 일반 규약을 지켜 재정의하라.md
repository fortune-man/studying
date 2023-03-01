
- `Object.equals()` 메서드는 비교하는 두 객체의 주소 값을 비교하여 boolean을 반환합니다.
- `equals()` 메서드를 재정의 할 필요가 없는 경우와 재정의 해야되는 경우, 재정의 시 고려할 점에 대해 알아보겠습니다.

---

## 재정의가 필요 없는 경우

다음 상황 중 하나에 해당된다면 재정의하지 않는 것이 최선이라고 합니다.

### 1. 각각의 인스턴스가 고유할 경우

- 값 표현이 아닌 동작하는 개체를 표현하는 클래스가 이 경우에 해당
	- 예) `java.lang.Thread` 클래스

### 2. 인스턴스의 논리적 동치성(logical equality)을 검사할 일이 없는 경우

- `equals()` 메서드를 재정의해서 논리적 동치성을 확인할 수 있는 방법을 제공하는 클래스를 만들 수 있습니다.
- 클래스를 정의할 때 논리적 동치성이 필요없다고 생각한다면, 기본 `equals()` 메서드로 충분합니다.

### 3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 적합한 경우

- `Collections` 프레임워크의 구현체들은 Abstract 클래스가 존재하고 이미 재정의된 `equals()`를 상속받아 사용합니다.
```java
public boolean equals(Object o) {
	if(o == this)
		return true;
	if(!(o instanceof List))
		return false;

	ListIterator<E> e1 = listIterator();
	ListIterator<?> e2 = ((List<?> o).listIterator();

	while (e1.hasNext() && e2.hasNext()) {
		E o1 = e1.next();
		Object o2 = e2.next();
		if (!(o1==null ? o2==null : o1.equals(o2)))
			return false;
	}
	return !(e1.hasNext() || e2.hasNext());
}
```

- ArrayList가 사용하는 `AbstractList.equals(Object o)`

### 4. 클래스가 private 이거나 package-private이고 equals를 호출할 일이 없는 경우

- 의도하지 않은 `equals()` 메서드 호출을 막고싶으면 다음과 같이 구현하면 된다고 합니다.
```java
@Override
public boolean equals(Object obj) {
	throw new AssertionError(); // 호출 금지
}
```

### 5. 값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 경우

- 같은 값을 생성하지 않도록 설정한 클래스의 경우 `Object.equals()`가 논리적 동치성까지 확인해준다고 합니다.

---

## 재정의가 필요한 경우

`그렇다면 equals를 재정의해야 할 때는 언제일까?`

### 객체 식별성이 아닌 논리적 동치성을 확인해야 하는 경우
- 상위 클래스의 `equals()` 메서드가 논리적 동치성을 비교하도록 재정의 되지 않았을 경우
- `Integer`, `String` 처럼 값을 표현하는 클래스들이 여기 해당된다고 합니다.
- 논리적 동치성을 확인할 수 있도록 재정의 한다면 값을 비교하거나, `Map` 의 키와 `Set`의 원소로 사용할 수 있게 됩니다.

> 객체 식별성(object identity)
> - 같은 상태를 가지고 있어도 구별할 수 있는지의 여부

#### 재정의시 지켜야 하는 규약
- `Object.equals()` 메서드의 명세에는 동치관계를 구현하며 다음을 만족한다.
- 규약을 어기면 큰일난다!
	- 규약을 어긴 객체를 사용하는 다른 객체의 동작을 예측할 수 없을 것이며
	- 프로그램이 이상하게 동작하거나 종료될 것이고
	- 원인이 되는 코드를 찾기도 굉장히 어려울 것이다.

#### 1. 반사성(reflexivity)
- `null`이 아닌 모든 참조 값 x에 대해, `x.equals(x)`는 참이다.
- 객체는 자기 자신과 같아야 한다.
- 반사성을 만족하지 못 하는 클래스가 있다면, 해당 클래스의 인스턴스를 컬렉션에 넣은 후 `contains()` 메서드를 호출하여 인스턴스의 존재 유무를 확인할 때 해당 인스턴스가 없다고 나올 것이다.

#### 2. 대칭성(symmetry)
- `null`이 아닌 모든 참조 값 x,y에 대해 `x.equals(y)`의 결과와 `y.equals(x)`의 결과가 동일하다.
- 두 객체가 있을 때 서로의 동치 여부에 대해 같은 결과를 반환해야 한다.

#### 3. 추이성(transitivity)
- `null`이 아닌 모든 참조 값 x, y, z에 대해 `x.equals(y)`가 참이고 `y.equals(z)`가 참이면 `x.equals(z)`도 참이다.
- `equals()` 메서드가 동작할 때 신뢰할 수 없는 자원이 사용되면 안 된다.

#### 4. 일관성(consistency)
- `null`이 아닌 모든 참조 값 x, y에 대해 `x.equals(y)`를 반복해서 호출하면 항상 같은 값을 반환해야 한다.
- `equals()` 메서드가 동작할 때 신뢰할 수 없는 자원이 사용되면 안 된다.

#### 5. null-아님
- null이 아닌 모든 참조 값 x에 대해, `x.equals(null)`은 거짓이다.

---

## equals 메서드 구현 방법

### 1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
- 단순 최적화 용으로, 자기 자신이면 참을 반환합니다.

### 2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
- 올바른 타입이 아니면 거짓을 반환합니다.

### 3. 입력을 올바른 타입으로 형변환 한다.
- instanceof으로 확인을 했기 때문에, 형변환은 무조건 성공합니다.

### 4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 검사한다.
- 모든 필드가 일치하면 참을, 하나라도 다르면 거짓을 반환합니다.
- 기본 필드 타입은 == 연산자, 참조 타입의 필드는 `equals()` 메서드, float과 double 필드는 정적 메서드인 `compare()` 메서드로 비교합니다.

```java
public class Tree {

	private int age;
	private double height;

	@Override
	public boolean equals(Object o) {

		// 1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인
		if (this == o) { return true; }
	
		// 2. instanceof 연산자로 입력이 올바른 타입인지 확인
		if (!(o instanceof Tree)) { return false; }
	
		// 3. 입력을 올바른 타입으로 형변환 
		Tree tree = (Tree) o;
	
		// 4, 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 검사
		if (age != tree.age) { return false; }
	
		return Double.compare(tree.height, height) == 0;
	}
}
```
- 1 ~ 4구현 방법을 적용한 `equals()` 메서드

---

### 재정의 시 고려해야 할 부분

- 비교하는 순서에 따라 성능을 좌우하는 경우도 있기 때문에, 다를 가능성이 크거나 비교하는 비용이 싼 필드를 우선적으로 비교하는 것이 좋다고 합니다.
- 구현 후 규약을 만족하는지 확인하는 단위 테스트 작성하는 것이 좋습니다.
- Object 외의 타입을 매개변수로 받는 `equals()` 메서드는 선언하지 않는 것이 좋다고 합니다.
	- 실수로 다중 정의를 했더라도 `@Override` 애너테이션을 일관되게 사용하면 실수를 예방할 수 있습니다.
- 

### 정리

- 꼭 필요한 경우가 아니라면 `equals()` 메서드를 재정의하지 않는 것이 좋습니다.
	- 많은 경우에 Object의 equals가 원하는 비교를 정확히 수행해준다고 합니다.
- 재정의를 해야하는 경우엔 그 클래스의 핵심 필드를 모두 빠짐 없이, 다섯 가지 규약을  확실히 지켜가며 비교해야 합니다.
- 필요한 경우 [AutoValue 프레임워크](https://www.baeldung.com/introduction-to-autovalue), [Lombok의 `EqualsAndHashCode`](https://kwonnam.pe.kr/wiki/java/lombok/pitfall), IDE 코드 제너레이션을 사용하여 재정의하면 실수를 줄일 수 있다고 합니다.