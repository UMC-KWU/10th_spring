- 빌더패턴이란?

  : 복잡한 객체를 생성하는 방법 중 하나로, 객체의 생성 코드와 객체의 사용코드를 분리하여 코드의 가독성과 유지 보수성을 향상시키는 패턴

  **빌더 패턴이 필요한 이유**

  객체를 생성할 때, 필요한 매개변수를 모두 포함하는 생성자를 사용하게 되는데, 이 생성자의 매개변수가 많아질 경우 문제가 발생.

  ⇒ 매개변수의 순서를 혼동하기 쉽고, 일부 매개변수에 대해 null 값 또는 기본값을 할당해야 할 때, 그 의미를 파악하기 어렵게 만들 수 있음.

  **문제 상황 (빌더 패턴이 없을 때)**

    ```java
    // 생성자에 파라미터가 너무 많아 무엇이 무엇인지 알기 어려움
    User user = new User("김철수", 25, "서울", "010-1234-5678", null, true, false);
    ```

  **빌더 패턴 적용 후**

    ```java
    // 훨씬 읽기 쉽고, 필요한 값만 설정 가능
    User user = new User.Builder()
    .name("김철수")
    .age(25)
    .address("서울")
    .phone("010-1234-5678")
    .isActive(true)
    .build();
    ```

  ## 빌더 패턴의 구현 방법

    - 빌더 클래스를 선언하고, 생성할 객체의 속성에 대한 setter 메서드를 구현.
    - 이 메서드들은 빌더 객체 자신을 반환하므로 메서드 체이닝이 가능. 또한 build 메서드를 통해 최종적인 객체를 생성.

    ```java
    public class User {
    		// 필수값
    		private final String name;
    		private final int age;
    		// 선택값
    		private final String address;
    		private final String phone;
    		
    		private User(Builder builder) {
    		    this.name = builder.name;
    		    this.age = builder.age;
    		    this.address = builder.address;
    		    this.phone = builder.phone;
    		}
    
    		public static class Builder {
    		    private String name;  // 필수
    		    private int age;      // 필수
    		    private String address = "";  // 선택 (기본값)
    		    private String phone = "";    // 선택 (기본값)
    		
    		    public Builder name(String name) {
    		        this.name = name;
    		        return this;  // 메서드 체이닝을 위해 자기 자신 반환
    		    }
    		
    		    public Builder age(int age) {
    		        this.age = age;
    		        return this;
    		    }
    		
    		    public Builder address(String address) {
    		        this.address = address;
    		        return this;
    		    }
    		
    		    public Builder phone(String phone) {
    		        this.phone = phone;
    		        return this;
    		    }
    		
    		    public User build() {
    		        return new User(this);
    		    }
    		}
    }
    ```

  **Lombok 라이브러리의 @Builder 어노테이션을 사용하여 빌더 패턴을 간단하게 구현 가능.**

    ```java
    @Builder
    public class User {
    		private final String name;
    		private final int age;
    		private final String address;
    		private final String phone;
    }
    ```

  **Record + @Builder로 변환**

    ```java
    @Builder
    public record User(
    		String name,
    		int age,
    		String address,
    		String phone
    ) {}
    ```

  **사용법은 동일**

    ```java
    User user = User.builder()
    		.name("김철수")
    		.age(25)
    		.address("서울")
    		.build();
    ```

- record vs static class


    |  | `record` | `static class` |
    | --- | --- | --- |
    | **목적** | 순수 데이터 저장 | 범용 |
    | **불변성** | 기본 불변 | 직접 설정 필요 |
    | **상속** | 불가 | 가능 |
    | **boilerplate** | 자동 생성 | 직접 작성 |
    | **로직 포함** | 가능하나 비권장 | 자유롭게 가능 |
    
    ***보일러 플레이트 코드**
    
    : 최소한의 변경(인자, 혹은 결과 타입)으로 여러 곳에서 재사용 되면 반복적으로 비슷한 형태를 가지고 있는 코드
    
    ⇒ getter, setter, equals, hashCode, toString 등이 여기에 해당
    
    **Record** 
    
    ```java
    public record User(String name, int age) {}
    ```
    
    위 한 줄이 아래를 **자동 생성.**
    
    ```java
    public final class User {
    		private final String name;
    		private final int age;
    		// 생성자
    		public User(String name, int age) { ... }
    		
    		// getter (get 없이 필드명 그대로)
    		public String name() { ... }
    		public int age() { ... }
    		
    		// equals, hashCode, toString 자동 생성
    		@Override public boolean equals(Object o) { ... }
    		@Override public int hashCode() { ... }
    		@Override public String toString() { ... }
    }
    ```
    
    **Static Class - 직접 다 써야 함**
    
    ```java
    public static class User {
    		private final String name;
    		private final int age;
    		
    		public User(String name, int age) {
    		    this.name = name;
    		    this.age = age;
    		}
    		
    		public String getName() { return name; }  // get 붙음
    		public int getAge() { return age; }
    		
    		// equals, hashCode, toString 직접 작성 or Lombok 필요
    }
    ```

- 제네릭이란?

  : **타입을 매개변수처럼 사용**하는 기능.
  ⇒ 코드를 작성할 때 타입을 고정하지 않고, **나중에 사용할 때 타입을 지정**할 수 있음.

  **제네릭의 사용 이유**

    1. **잘못된 타입을 사용하는 것을 방지.**
    2. **외부에서 타입이 지정되기 때문에 관리가 편함.**
    3. **클래스의 재사용성 향상.**

    ```java
    // T는 타입 매개변수 (아무 이름이나 가능, 관례상 T 사용)
    public class Box<T> {
    		private T value;
    		
    		public void set(T value) { this.value = value; }
    		public T get() { return value; }
    
    }
    
    // 사용
    Box<String> strBox = new Box<>();
    strBox.set("안녕");
    String str = strBox.get();  // 캐스팅 필요 없음
    
    Box<Integer> intBox = new Box<>();
    intBox.set(123);
    Integer num = intBox.get();
    ```

  **타입 매개변수 관례**

  | 기호 | 의미 |
      | --- | --- |
  | `T` | Type (일반 타입) |
  | `E` | Element (컬렉션 요소) |
  | `K` | Key |
  | `V` | Value |
  | `N` | Number |

- @RestControllerAdvice이란?

  : **전역 예외 처리**를 담당하는 어노테이션.

  **전역 예외 처리**: **모든 컨트롤러에서 발생하는 예외를 하나의 클래스에서 처리함**으로써 코드가 일관되게 관리되고 중복 코드가 줄어든다.

    ```java
    @RestControllerAdvice
    public class GlobalExceptionHandler {
    
    		// 특정 예외 처리
    		@ExceptionHandler(UserNotFoundException.class)
    		public ApiResponse<?> handleUserNotFound(UserNotFoundException e) {
    		    return ApiResponse.onFailure(e.getCode(), e.getMessage());
    		}
    		
    		// 유효성 검사 예외 처리
    		@ExceptionHandler(MethodArgumentNotValidException.class)
    		public ApiResponse<?> handleValidation(MethodArgumentNotValidException e) {
    		    return ApiResponse.onFailure("VALID400", e.getMessage());
    		}
    		
    		// 그 외 모든 예외 처리
    		@ExceptionHandler(Exception.class)
    		public ApiResponse<?> handleException(Exception e) {
    		    return ApiResponse.onFailure("SERVER500", "서버 오류입니다.");
    		}
    }
    ```

  ### 구성 요소

  | 어노테이션 | 역할 |
      | --- | --- |
  | `@RestControllerAdvice` | 전역 예외처리 클래스 선언 |
  | `@ExceptionHandler` | 특정 예외 처리 메서드 지정 |

- Optional이란?

  : null을 안전하게 다루기 위한 wrapper 클래스.
  ⇒ NullPointerException(**NPE**)을 방지.

    ```java
    User user = userRepository.findById(id); // null일 수도 있음
    System.out.println(user.getName()); // NPE 발생 가능
    ```

  **Optional 사용 후**

    ```java
    Optional<User> optionalUser = userRepository.findById(id);
    
    optionalUser.ifPresent(user ->
    		System.out.println(user.getName()) // null이 아닐 때만 실행
    );
    ```

  **써야 할 때**

    - 값이 null일 수 있는 반환값
    - Repository의 단건 조회 (findById 등)

  **쓰지 말아야 할 때**

    - 메서드 파라미터로 사용
    - 컬렉션 반환 (빈 List [] 반환이 나음)
    - 단순히 null 체크용 (if문이 더 명확)