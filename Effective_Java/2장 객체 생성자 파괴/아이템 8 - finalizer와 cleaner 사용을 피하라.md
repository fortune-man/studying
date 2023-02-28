

### 자바의 두 가지 객체 소멸자

- 자바는 객체 소멸자로 `finalizer`와 `cleaner`를 지원합니다.
- 자바 9에서는 `finalizer`가 사용 자제(deprecated) 되고 덜 위험한 `cleaner`가 대안으로 소개되었습니다.(별도의 스레드 사용)
- 하지만 여전히 예측할 수 없고, 느리고, 불필요합니다.

---

### finalizer와 cleaner의 단점

#### 1. 수행 시점을 예측할 수 없다.
- 즉시 수행된다는 보장이 없고, 실행까지 얼마가 걸릴지 알 수 없습니다.
- 동작은 GC의 알고리즘에 달렸으며 우선순위도 낮기 때문에 만약 인스턴스를 반환하는 작업을 한다면 자원반환이 안 되어서 `OutOfMemoryError가` 발생할 수도 있습니다.

#### 2. 수행 여부를 확인할 수 없다.
- 수행 여부를 알 수 없기 때문에 상태를 영구적으로 수정하는 작업에 절대로 사용하면 안된다고 합니다.
- 데이터베이스와 같은 공유 자원의 영구 락 해제하는 코드가 담겨 있을 경우, 제대로 작동하지 않아서 시스템 전체가 멈출 수도 있습니다.

#### 3. 예외가 무시된다.
- `finalizer` 동작 중 발생한 예외는 무시되고 동작해야 하는 작업이 남아있어도 바로 종료됩니다.
- `cleaner`의 경우에는 자신의 스레드를 통제하기 때문에 이런 문제가 발생하지 않는다.

#### 4. 성능 문제도 심각하다.
- `try-with-resources`를 사용하여 객체를 생성하고 종료하는 방법보다 수십배 느리다고 합니다.
- 하지만 객체를 반환할 때 안전망 형태로만 사용한다면 약 5배 정도만 느려진다고 합니다.

#### 5. finalizer 공격에 노출되기 때문에 보안에 취약하다.
- 생성자나 직렬화 과정에서 예외가 발생하면 해당 클래스를 상속받은 클래스가 상속받은 `finalize()` 메서드를 이용해서 부가적인 작업을 할 수 있습니다.
	- 정적 필드에 자기 자신의 참조를 할당하여 GC가 수집을 못하게 할 수 있다.
	- 악의적인 코드를 상속받은 `finalize()` 메서드를 통해 실행시킬 수 있다.
	- 이미 빌드 되어서 컴파일된 클래스 파일만 참조할 수 있으면 클래스패스에 추가해서 공격이 가능합니다.
- final 클래스는 하위 클래스를 만들 수 있으니 이 공격에 안전합니다.
- final이 아닌 클래스에서는 아무런 작업도 하지 않는 `finalize()` 메서드를 만들고 final로 선언하여 막으라고 합니다.

---

### finalizer와 cleaner의 대안

#### AutoCloseable 구현

-  클라이언트에서 인스턴스 사용 후 `close()` 메서드를 호출 (일반적으로 예외가 발생해도 제대로 종료되도록 `try-with-resources` 사용, Item 09)
- 각 인스턴스는 자신이 닫혔는지를 추적하는 것이 좋다고 합니다.
	- close() 메서드가 호출되었는지 확인하고, 객체가 닫힌 후에도 호출이 되었다면 `IllegalStateException`을 던짐

#### finalizer와 cleanr의 용도
- 이쯤이면 `cleaner`와 `finlizer`는 대체 어디에 쓰는 물건인지 궁금합니다.

#### 안전망 역할
- 자원의 소유자가 `close()` 메서드를 호출하지 않는 것에 대비한 안전망 역할로 사용할 수 있습니다.
- 자바 라이브러리의 일부 클래스는 안전망 역할의 finalizer를 제공합니다.
	- `FileInputStream`
	- `FileOutputStream`
	- `ThreadPoolExecutor`

#### 네이티브 피어(native peer)와 연결된 객체
- 네이티브 피어란 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 객체를 말합니다.
- 네이티브 피어는 자바 객체가 아니니 가비지 컬렉터는 그 존재를 알지 못합니다.
- 이 경우에도 성능 저하가 우려되거나 자원을 즉시 회수해야 하는 경우 `close()`  메서드를 사용하는 것이 좋다고 합니다.

> 네이티브 메서드(native method)?
> - C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드

#### cleaner 사용
```java
// cleaner를 안전망으로 활용하는 AutoCloseable
public class Room implments AutoCloseable {
	private static final Cleaner CLEANER = Cleaner.create();

	// 청소가 필요한 자원. 절대 Room을 참조해서는 안됨
	private static class State implements Runnable {
		int numJunkPiles;

		State(int numJunkPiles) {
			this.numJunkPiles = numJunkPiles;
		}

		// close 메서드나 cleaner가 호출
		@Override public void run() {
			System.out.println("방 청소");
			numJunkPiles = 0;
		}
	}

	// 빙의 상태. cleanable과 공유
	private final State state;

	// cleanable 객체, 수거 대상이 되면 방을 청소
	private final Cleaner.Cleable cleanable;

	public Room() {
		this.state = new State();
		this.cleanable = CLEANER.register(this, state);
	}

	@Override
	public void close() throws Exception {
		cleanable.clean();
	}

	// Room을 참조해서는 안 된다.
	private static class State implements Runnable {
		@Override
		public void run() {
			System.out.println("청소");
		}
	}
}
```

- State는 수거할 자원을 가질 수 있고, 클리너를 네이티브 피어를 위해 사용할 경우 네이티브 피어를 가리키는포인터를 담은 변수를 가지고 있어야 합니다.
- State는 Runnable을 구현하고, `run()` 메서드는 cleanable에 의해 단 한 번만 호출됩니다.
- 호출되는 두 가지 상황
	- `Room.close` 메서드를 호출 할 경우
	- 가비지 컬렉터가 Room을 회수할 경우(`clean() 메서드` 호출을 예측할 수 없음)
- 위 코드에서 State 클래스는 절대로 Room 인스턴스를 참조하면 안 됩니다.
	- 순환 참조가 생겨 GC가 Room 인스턴스를 회수해갈 기회가 오지 않기 때문입니다.
	- 정적이 아닌 중첩 클래스는 자동으로 바깥 객체의 참조를 갖게 됩니다. (Item 24)
	- 이와 비슷하게 람다 역시 바깥 객체의 참조를 갖기 쉬우니 사용하지 않는 것이 좋다고 합니다.

---

### 정리

>- finalizer와 cleaner는 되도록이면 사용을 피하자.
>- 안전망이나 중요하지 않은 네이티브 자원 회수용으로만 사용하고 이런 경우라도 불확실성과 성능 저하에 주의해야한다.