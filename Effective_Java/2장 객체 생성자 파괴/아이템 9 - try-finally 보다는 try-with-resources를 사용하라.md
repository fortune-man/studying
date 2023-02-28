- 자바 라이브러리에는 `close()` 메서드를 호출해 닫아줘야 하는 자원이 많습니다.
	- 좋은 예 : `InputStream`, `OutputStream`, `java.sql.Connection`
- 자원을 반환하는 부분은 클라이언트 쪽에서 실수를 할 경우가 있기 때문에 예측할 수 없는 성능 문제로도 이어지기도 한다고 합니다.
- 어떻게 하면 안전하고 효율적이며 가독성이 좋응 방법으로 자원을 반환할 수 있을까요?

---

### try-finally

```java
static String readInputStream() throws IOExcepton {

	BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));

	try {
		return bufferedReader.readLine();
	} finally {
		bufferedReader.close();
	}
}
```

- 전통적으로 `try-finally`를 이용해서 자원의 닫힘을 제대로 보장했지만
- 관리해야될 자원이 많아지는 경우 코드가 복잡해진다는 단점이 있습니다.
```java
static void copy(String src, String dst) throws IOException {

	InputStream inputStream = new FileInputStream(src);
	try {
		OutputStream out = new FileOutputStream(dst);
		try {
			byte[] buf = new byte[BUFFER_SIZE];
			int n;
			while ((n = in.read(buf)) >= 0)
				out.write(buf, 0, n);
		} finally {
			out.close();
	} finally {
		in.close();
	}
		
	}
}
```
- 예외가 여러 개 생기는 경우 이후에 발생한 예외 때문에 이전에 발생한 예외에 관한 정보가 남아 문제가 생길 수 있습니다.

#### try-with-resources

```java

// 위 문제를 모두 해결
static String readInputStream() throws IOException {
	try (BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in))) {
		return bufferedReader.readLine();
	}
}
```

- 자바 7 부터 사용 가능하고, 이 구조를 사용하려면 해당 자원이 `AutoCloseable` 인터페이스를 구현해야 합니다.
- 읽기가 수월하고, 문제를 진단하는 경우에도 훨씬 편리합니다.
- 이전에 발생된 예외도 스택 추적 내역에 숨겨졌다(suppreessed)라는 꼬리표를 달고 출력됩니다.


#### try-with-resources를 catch절과 함께
```java
static String firstLineOfFile(String path, String defaultVal) {
	try (BufferedReader bufferedReader = new BufferedReader(
		new FileReader(path))) {
		return bufferedReader.readLine();
		} catch (IOException.e) {
			return defaultVal;
		}	
}
```

- 보통의`try-finally` 구문처럼 `try-with-resources` 에서도 catch 절을 사용할 수 있습니다.
	- `try` 구문을 중첩하지 않고도 다수의 예외를 처리할 수 있다고도 합니다.

---

### 정리

>- 꼭 회수해야 하는 자원을 다룰 때 try-finally 말고
>- 더 효율적이고 가독성이 좋은 `try-with-resources`를 사용하자.
>- 예외는 없다.
>- 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다.
>- `try-finally`로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우가
>- `try-with-resources`로 정확하고 쉽게 자원 회수가 보장된다.
