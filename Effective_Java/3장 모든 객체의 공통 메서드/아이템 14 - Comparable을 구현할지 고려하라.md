
- Comparable의 `compareTo()` 메서드를 재정의 한다면, 단순 동치성 비교에 더해 순서까지 비교할 수 있으며 제네릭 합니다.
	- 컬렉션을 더욱 더 효율적으로 사용할 수 있습니다.
- Comparable 구현은 곧 그 클래스의 인스턴스들에는 자연적인 순서(natural order)가 있음을 의미합니다.
- Comparable을 구현하여 이 인터페이스를 활용하는 수많은 제네릭 알고리즘과 컬렉션의 힘을 누릴 수 있습니다.
- 알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면, 해당 인터페이스를 구현하는 것이 좋습니다.

```java
public interface Comparable<T> {
	int compareTo(T t);
}
```

---

### compareTo 재정의 규약

- `compareTo()` 메서드는 객체와 주어진 객체의 순서를 비교한 후 값을 반환합니다.
	- 주어진 객체보다 작으면 음의 정수
	- 주어진 객체와 같으면 0
	- 주어진 객체보다 크다면 양의 정수
- 비교할 수 없는 타입의 객체가 주어지면 `ClassCastExcepion`을 던집니다.
- `compareTo()` 메서드가 비교에 따라 -1, 0, 1을 반환한다는 가정 하에 규약은 다음과 같습니다.
- `x.compareTo(y)`는 `-y.compareTo(x)`와 같아야 합니다.
	- 또한 x.compareTo(y)가 예외를 던진다면, `y.compareTo(x)`도 예외를 던져야 합니다.
- `x.compareTo(y) == 0` 일 경우 `z.compareTo(x)` 와 `z.compareTo(y)` 는 같습니다.
	- 크기가 같은 객체들끼리는 어떤 객체와 비교하더라도 항상 같아야 합니다.
- `x.compareTo(y) == 0` 은 `x.equals(y)`와 결과가 같아야 합니다.
	- 마지막 규약은 `equals()` 메서드와의 일관성으로 적극적으로 추천하고 있습니다.
	- 지키지 않을 경우 해당 클래스의 순서가 `equals()` 메서드와 일관되지 않다고 명시하는 것이 좋습니다.

### 마지막 규약을 지켜야 하는 이유

- 컬렉션들은 `equals()` 메서드의 규약을 따르지만, 정렬된 컬렉션들은 동치성을 비교할 때 `compareTo()` 메서드를 사용합니다.
- 해당 규약을 안지키는 경우 발생하는 문제

```java
@Test  
@DisplayName("BigDecimal 클래스는 compareTo 메서드로 수행한 동치성 테스트의 결과가 equals와 같지 않다")  
void bigDecimal() {  
    // given  
    BigDecimal bigDecimal1 = new BigDecimal("0.0");  
    BigDecimal bigDecimal2 = new BigDecimal("0.00");  
  
    HashSet<BigDecimal> hashSet = new HashSet<>();  
    TreeSet<BigDecimal> treeSet = new TreeSet<>();  
  
    // when  
    hashSet.add(bigDecimal1);  
    hashSet.add(bigDecimal2);  
  
    treeSet.add(bigDecimal1);  
    treeSet.add(bigDecimal2);  
  
    boolean compareToResult = bigDecimal1.compareTo(bigDecimal2) == 0;  
    boolean equalsResult = bigDecimal1.equals(bigDecimal2);  
  
    // then  
    assertThat(compareToResult).isNotEqualTo(equalsResult);  
    assertThat(hashSet.size()).isEqualTo(2);  
    assertThat(treeSet.size()).isEqualTo(1);  
}
```
- 0.0과 0.00의 값을 보유한 각 `BigDemical` 객체
- `equals()` 메서드를 이용해 비교하는 HashSet은 두 객체를 넣었을 때 서로 다른 값이라고 판단하여 2개의 원소를 가지게 됩니다.
- `compareTo()` 메서드를 이용해 비교하는 TreeSet을 사용하면, 두 객체는 같은 값이라고 판단하여 1개의 원소를 가지게 됩니다.
- 마지막 규약을 지킨다면, `compareTo()` 메서드의 순서와 `equals()` 메서드의 결과가 일관되게 되기 때문에 컬렉션이 의도한 동작대로 작동합니다.

---

### compareTo 재정의 방법

#### 비교할 필드가 하나일 경우

- 객체 참조 필드가 하나일 경우 `compare()` 메서드를 재귀적으로 호출하는 것이 좋습니다.
- 기본 타입 필드를 비교할 경우 관계 연산자보단, 자바 7부터 래퍼 클래스가 제공해주는 `compare()` 메서드를 사용하는 것이 좋습니다.
```java
public final class CaseInsensitiveString implemnets Comparable<CaseInsensitiveString> {
	public int compareTo(CaseInsensitiveString cis) {
		return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
	}

	// 생략 ..
}
```

	- [안티패턴] 관계 연산자를 이용하여 `compareTo()` 메서드 작성시 가독성도 좋지 않고 실수의 여지가 있습니다.
```java
public class Member implements Comparable<Cat> {

	private String name;

	@Override
	public int compareTo(Member member) {
		return name.compareTo(member.name);
	}
}
```

- 비교 대상이 문자열이 아닌 다른 데이터 타입인 경우 관계 연산자를 사용할 수 없기 때문입니다.
	- 예) Integer의 경우 a < b 비교 가능하지만, String은 불가능
	- 해당 데이터 타입의 자연 순서를 따르도록 정의되어 있다는 걸 인지하는 것이 중요합니다.

#### 비교할 필드가 여럿일 경우

- 비교 필드가 여럿인 경우 Comparator를 제공하는 비교자 생성 메서드를 사용하는 것이 좋습니다.
- 첫 번째 비교시 타입 추론을 위해 입력 인수의 타입을 지정해줘야 합니다.

```java
private static final Comparator<PhoneNumber> COMPARATOR =
	comparingInt((PhoneNumber phoneNumber) -> phoneNumber.areaCode)
	.thenComparingInt(phoneNumber -> phoneNumber.prefix)
	.thenComparingInt(phoneNumber -> phoneNumber.lineNum);

public int compareTo(PhoneNumber phoneNumber) {
	return COMPARATOR.compare(this, phoneNumber);
}
```

#### [안티패턴]'값의 차'를 기준으로 비교하는 경우
```java
// hashCode 값의 차를 기준으로 하는 비교자
static Comparator<Object> hashCodeOrder = new Comparator<>() {
	public int compare(Object o1, Object o2) {
		return o1.hashCode() - o2.hashCode();
	}
}
```
- 추이성을 위배합니다.
- 정수 오버플로를 일으키거나 필드 간 우선순위에 따른 비교를 할 수 없습니다.
- IEEE 754 부동소수점 계싼 방식에 따른 오류를 낼 수 있다고도 합니다.

#### 대안
```java
// 정적 compare 메서드를 활용한 비교자
static Comparator<Object> hashCodeOrder = new Comparator<>() {
	public int compare(Object o1, Object o2) {
		return Integer.compare(o1.hashCode(), o2.hashCode());
	}
}
```

```java
// 비교자 생성 메서드를 활용한 비교자
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```

---

### 정리

- 순서를 고려해야 하는 값 클래스를 작성하는 경우 꼭 Comparable 인터페이스를 구현합시다.
	- 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야 하기 때문입니다.
- `compareTo()` 메서드에서 필드의 값 비교할 때 <와 > 연산자는 사용하지 말아야 합니다.
	- 대신 박싱된 기본 타입 클래스가 제공하는 정적 `compare()` 메서드 또는 
	- Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용합니다.