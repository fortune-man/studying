
# Reference 🙇🏻‍♂️

- [Java HashMap은 어떻게 동작하는가?](https://d2.naver.com/helloworld/831311)
- [EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode)
---

- hashCode 재정의 규약, 왜 hashCode를 재정의 해야 하는지, 재정의 방법을 알아봅니다.

### hashCode 재정의 규약
- equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 해당 객체의 `hashCode()` 메서드는 몇 번을 호출해도 일관된 값을 반환해야 합니다.
	- 단, 애플리케이션을 다시 실행시킨다면 값이 달라져도 상관없다고 합니다.
- `equals(Object)` 가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 같은 값을 반환해야합니다.
	- 따라서 equals 비교에 사용하지 않는 필드는 반드시 제외해야 합니다.
- `equals(Object)`가 다르다고 판단해도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없습니다.
	- 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아질 수 있습니다.

### hashCode 재정의를 하지 않는 경우
- 재정의를 하지 않는 경우 Object의 기본 `hashCode()` 메서드를 사용하게 되는데, 논리적으로 같다고 하더라도 다른 값을 반환합니다.
- HashMap이나 HashSet과 같은 hash를 사용하는 컬렉션의 원소로 사용될 때 문제가 발생합니다.
```java
@Test
@DisplayName("hashCode를 재정의하지 않을 경우 논리적으로 같은 값을 가지더라도 다른 값을 반환한다.")
void defaultHashCodeTree() {
	//given
	Map<DefaultHashCodeTree, Integer> map = new HashMap<>();
	DefaultHashCodeTree treeA = new DefaultHashCodeTree(150, "은행나무");
	DefaultHashCodeTree treeB = new DefaultHashCodeTree(150, "은행나무");

	//when
	map.put(treeA, 5000);
	Integer value = map.get(treeB);

	//then
	assertThat(value).isNull();
	assertThat(treeA.hashCode()).isNotEqualTo(treeB.hashCode());
}
```

- DefaultHashCodeTree 클래스는 `equals()` 메서드만 재정의하고 `hashCode()` 메서드를 재정의하지 않는 클래스입니다.
- treeA와 treeB는 논리적으로 같은 값을 가지고 있지만 다른 해시값을 반환합니다.
- 그 결과 `map.get(treeB)` 메서드가 해당 key에 대한 value를 찾지 못했기 때문에 null을 반환합니다.

### hashCode 모든 객체에서 동일한 값을 반환하는 경우

- `hashCode()` 메서드가 모든 객체에 대해서 동일한 해시 값을 반환하는 경우에도 문제가 발생합니다.
```java
@Override
public int hashCode() {
	return 404;
}
```

- 동일한 값을 반환하는 경우 hash를 사용하는 컬렉션을 사용할 때 문제가 발생합니다.
	- 해시테이블에 모든 객체가 하나의 버킷에 담기기 때문에 연결 리스트처럼 동작합니다.
- 결과적으로 평균 수행시간이 O(1)인 해시테이블이 O(N)으로 느려지게 됩니다.

---

### 좋은 hashCode를 작성하는 방법

1. int 변수 result를 선언한 후 값 c로 초기화한다.
- c는 해당 객체의 첫 번째 핵심 필드(`equals()` 메서드 비교에 사용되는 필드)를 2.a의 방식으로 계산한 해시코드입니다.
2. 해당 객체의 나머지 핵심 필드 f 각각에 대해 다음 작업을 수행합니다.
	a. 해당 필드의 해시코드 c를 계산
	- 기본 타입 필드일 경우
		- `Type.hashCode(f)`를 수행합니다. 여기서 Type은 해당 기본 타입의 박싱 클래스입니다.
	- 참조 타입 필드인 경우
		- 해당 필드의 `hashCode()` 메서드를 호출하여 사용합니다.
		- 필드의 값이 `null`일 경우 0을 사용합니다.
	- 배열일 경우
		- 배열 원소 중 핵심원소를 필드처럼 다뤄서 계산 -> c 값 계산 후 result 값 갱신
		- 배열의 핵심 원소가 하나도 없다면 단순한 상수 0을 사용합니다.
		- 모든 원소가 핵심 원소라면 `Arrays.hashCode()` 메서드를 사용합니다.
     b. 계산한 해시코드 값 c로 result를 갱신합니다.
     - `result = 31 * result + c;`
     - 필드를 곱하는 순서에 따라 결과 값이 달라지게 해줍니다.
     - 곱할 숫자를 31로 정해서 사용하는 이유
	     - 31이 홀수이면서 소수(prime)
	     - 짝수이거나 오버플로우가 발생하면 정보를 잃게 되기 때문
	     - 시프트 연산과 뺄셈으로 대체해 최적화 가능 `31 * i` -> `(i << 5) - i`
3. result를 반환합니다.

### hashCode 재정의

#### 전형적인 hashCode 재정의

```java
@RequiredArgsConstrctor
public class Animal {
	private final int AGE;
	private final int HEIGHT;
	private final String SPECIES;
	private final String[] FOODS;

	@Override
	public int hashCode() {
		// 1. int 변수 result 선언 이후 값 c로 초기화
		int result = Integer.hashCode(AGE);

		// 2.a 기본 타입 필드일 경우 -> Type.hashCode(f) 수행
		result = 31 * result + Integer.hashCode(HEIGHT);

		// 2.a 참조 타입 필드일 경우 -> 해당 필드의 hashCode() 메서드 호출하여 사용
		result = 31 * result + SPECIES.hashCode();

		// 2.a 배열일 경우 -> 모든 원소가 핵심 원소라면 Arrays.hashCode() 메서드를 사용
		result = 31 * result + Arrays.hashCode(FOODS);

		// 3. result 반환
		return result;
	}
}
```


#### Objects.hash 활용한 재정의

```java
@Override
public int hashCode() {
	return Objects.hash(AGE, HEIGHT, SPECIES, FOODS);
}
```
- 입력 인수를 받기 위한 배열이 만들어지고, 기본 타입이 있다면 박싱과 언박싱을 거쳐야 합니다.
- 짧고 간단하게 작성할 수 있지만, 속도는 더 느립니다.


#### 지연초기화를 이용한 재정의

```java
private int hashCode;  자동으로 0 초기화

@Override
public int hashCode() {
	int result = hashCode;
	if (result == 0) {
		int result = hashCode;
		if (result == 0) {
			result = Short.hashCode(AGE);
			result = 31 * result + Integer.hashCode(HEIGHT);
			result = 31 * result + SPECIES.hashCode();
			result = 31 * result + Arrays.hashCode(FOODS);
			hashCode = result;
		}
		return result;
	}
}
```

- 해시의 키로 사용되지 않는 경우라면 지연초기화 전략을 사용할 수 있습니다.
- 지연초기화를 사용하려면 스레드 안전성까지 고려해야 합니다. (Item 83)

#### Lombok의 @EqualsAndHashCode를 이용한 재정의

```java
@EqualsAndHashCode(of = {"AGE", "HEIGHT"})
@RequiredArgsConstructor
public class Animal {
	private final int AGE;
	private final int HEIGHT;
	private final String SPECIES;
	private final String[] FOODS;
}
```

- Lombok의 `@EqualsAndHashCode` 애너테이션을 사용하면 간단하게 재정의할 수 있습니다.
- `of()`와 `exclude()` 메서드를 이용해서 특정 필드를 지정하거나 제외시킬 수 있습니다.

---

### 정리

- 성능을 위해 해시코드를 계산할 때 핵심 필드를 생략해서는 안 됩니다.
	- 속도는 빨라져도 해시 품질을 저하시키기 때문
- `equals()` 메서드를 재정의할 때는 `hashCode()` 메서드도 반드시 재정의합시다.
	- 프로그램이 제대로 동작하지 않을 수 있음을 고려합시다.
- 재정의한 hashCode는 Object의 API 문서에 기술된 일반 규약을 따라야 하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 두현해야 합니다.
	- 조금 따분하더라도 AutoValue 프레임워크를 사용하면 멋진 equals와 hashCode를 자동으로 만들어주니 IDE의 도움을 받아봅시다.