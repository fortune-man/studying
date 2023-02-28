
- 싱글턴 : 인스턴스를 오직 하나만 생성할 수 있는 클래스
- 싱글턴이 보증되는 이유가 있다면 무엇일까?

---

### 싱글톤을 만드는 방식


#### 1. public 필드

```java
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }

	public void leaveTheBuilding() { ... }
}
```

- private 생성자는 인스턴스를 초기화 할 때 단 한 번 호출된다.
- 클라이언트 쪽에서 접근할 수 있는 생성자가 없음으로 해당 인스턴스가 전체 시스템에서 단 하나임이 보증된다.

#### public 필드 장점
- 해당 클래스가 싱글턴임이 명백하게 드러나고, 간결하다는 장점이 있다.

#### 2. 정적 팩토리

```java
public class Elvis {
	private static final Elvis INSTANCE = new Elivis();

	private Elvis)() { ... }

	public static Elvis getInstance() { return INSTANCE; }

	public void leaveTheBuilding() { ... }
}
```


- 인스턴스를 미리 생성해둔다.
- 정적 팩토리 메서드를 사용하여 인스턴스를 제공한다.
- getInstance() 메서드는 항상 같은 객체의 참조를 반환한다.


#### 정적 팩토리 장점
1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
	- ex) 스레드 별로 다른 인스턴스를 넘겨줄 수 있다.
2. 정적 팩토리를 제네릭 싱글턴 팩토리로 만들 수 있다.
3. 정적 팩토리 메서드 참조를 공급자(supplier)로 사용할 수 있다.

```java
public class ExSupplier {

	private final Supplier<? extends Static<Singleton> singleton;

	public ExSupplier(Supplier<? extends StaticSingleton> singleton) {
		this.singleton = singleton;
		}
}

ExSupplier exSupplier = new ExSupplier(StaticSingleton::getInstance);
```

#### public 필드 방식과 정적 팩토리 방식의 단점

- 싱클턴 클래스를 직렬화, 역직렬화시 새로운 인스턴스를 생성된다.
	- 새로운 인스턴스 생성을 막으려면 모든 인스턴스 필드를 transient 선언 후 싱글턴 객체를 반환하는 readResolve() 메서드를 제공해야 한다.
- 권한 있는 클라이언트는 reflection의 setAccessible(true)를 사용하면 private 생성자에 접근 가능해지기 때문에 싱글톤을 보장하지 못한다.
	- 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지는 방법으로 해결할 수 있다.

>transient
>- 직렬화 과정에서 제외하고 싶을 때 선언하는 키워드
>- 데이터를 전송하고 싶지 않을 때 사용

>직렬화 (serialization)
>- JVM(Java Virtual Machine 이하 JVM)의 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 바이트 형태로 변환하는 기술

>리플렉션 (reflection)
>- 구체적인 클래스 타입을 알지 못해도, 해당 클래스의 메서드, 타입, 변수에 접근할 수 있도록 해주는 API
>- JVM에서 실행되는 애플리케이션의 런타임 동작을 검사하거나 수정할 수 있는 기능이 필요한 프로그램에서 사용된다.

---

### 바람직한 방법

#### 열거 타입 방식의 싱글턴

```java
public enum Singeton {

	INSTANCE;

	public void leaveTheBuilding() { ... }
}
```

- public 필드 방식과 비슷하다.
- 더 간결하고, 추가 노력 없이 직렬화할 수 있다.
- 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제 2의 인스턴스가 생성되는 것을 막아준다.

>- 대부분 상황에서 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이라고 한다.
>- 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.
>- 열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.

---

> 정리
> - 싱글턴을 생성하거나 보증하고 싶다면
> - 대부분의 상황에서
> - 원소가 단 하나인 열거타입으로
> - 싱글턴을 생성하는 것이 좋다.