# 1. 빌더 패턴이란 ?

빌더 패턴(Builder Pattern)은 복잡한 객체의 생성 과정과 표현 방법을 분리하여, 동일한 생성 절차에서 서로 다른 표현 결과를 만들 수 있게 하는 디자인 패턴입니다.

쉽게 말해, 수많은 파라미터가 필요한 객체를 만들 때 이를 한꺼번에 생성자에 넣는 대신, 마치 레고 블록을 하나씩 쌓아 올리듯 단계별로 객체를 구성하는 방식입니다.

---

## 1. 왜 사용하는가?

### 기존 방식의 문제점 (점진적 생성자 패턴)

객체를 생성할 때 인자가 많아지면 어떤 값이 어떤 변수에 들어가는지 헷갈리고, 원하지 않는 매개변수까지 넘겨줘야 하는 불편함이 있습니다.

- `User user = new User("name", 20, "Seoul", "010-...", null, null);`
- 가독성이 매우 떨어짐

### 빌더 패턴의 장점

- **가독성 향상:** 각 인자가 무엇을 의미하는지 코드로 명확히 보입니다.
- **유연성:** 필요한 데이터만 설정할 수 있으며, 데이터의 순서에 구애받지 않습니다.
- **불변성(Immutability) 확보:** 객체 생성이 완료된 후에는 내부 상태를 변경할 수 없도록 설계하기 용이합니다.

---

## 2. 주요 구성 요소

1. Product: 우리가 최종적으로 만들고자 하는 복잡한 객체입니다.
2. Builder: Product의 각 부분을 만드는 추상 인터페이스입니다.
3. ConcreteBuilder: Builder 인터페이스를 구현하며, 실제 부품을 조립하고 결과를 반환합니다.
4. Director (선택 사항): Builder를 사용하여 객체를 생성하는 순서를 정의합니다. (최근에는 Director 없이 클라이언트가 직접 빌더를 호출하는 방식을 더 많이 사용합니다.)

---

## 3. 코드 예시

현업에서는 보통 Lombok 라이브러리의 `@Builder` 어노테이션을 많이 사용합니다.

```java
User user = User.builder()
                .name("토니")
                .age(24)
                .address("서울")
                // 필요한 것만 골라서 세팅 가능
                .build();
```



# 2. record vs static class

## **1. Record**

**Record**는 오직 데이터 전달을 목적으로 하는 데이터 객체(DTO)를 정의하기 위해 도입된 특수한 클래스 형태입니다.

- **주요 특징:** 필드, 생성자, `equals()`, `hashCode()`, `toString()`을 컴파일러가 **자동으로 생성**해 줍니다.
- **불변성 (Immutability):** 모든 필드가 `private final`로 선언되어, 한 번 생성된 데이터는 변경할 수 없습니다.
- **장점:** 상용구 코드가 사라져 코드가 획기적으로 짧아지고, 데이터의 일관성이 보장됩니다.

```java
// 이 한 줄만으로 클래스 선언, 생성자, Getter, toString 등이 모두 자동 구현됩니다.
public record UserDto(String name, int age) { }
```

---

## **2. Static Class**

일반적으로 클래스 내부의 클래스(Nested Class)를 선언할 때 `static` 키워드를 붙인 것을 의미합니다.

- **주요 특징:** 외부 클래스의 인스턴스를 먼저 생성하지 않아도 독립적으로 사용할 수 있는 클래스입니다.
- **주요 용도:** 특정 클래스 안에서만 사용되는 **보조적인 클래스**를 정의할 때 사용하며, 대표적으로 **Builder 패턴**의 구현체로 자주 쓰입니다.
- **상태 관리:** `record`와 달리 내부 필드 값을 자유롭게 변경할 수 있도록 설계가 가능합니다.

```java
public class Outer {
    // 외부 클래스의 생성 없이 바로 접근 가능한 정적 내부 클래스
    public static class Inner {
        private String detail;
        
        // 생성자와 Getter/Setter 등을 직접 구현해야 합니다.
        public Inner(String detail) {
            this.detail = detail;
        }
    }
}
```

---

## **3. Record vs Static Class 핵심 비교**

| **구분** | **Record** | **Static Class** |
| --- | --- | --- |
| **주 목적** | **순수 데이터 전달 (DTO)** | **구조적 그룹화 및 보조 역할** |
| **변경 가능성** | 불변 (Immutable) | 가변적 설계 가능 |
| **구현 편의성** | 컴파일러가 자동 생성 | 개발자가 직접 작성 |


# 3. 제네릭이란 ?

## **1. 제네릭 (Generic) 이란?**

**제네릭**은 클래스나 메서드에서 사용할 **데이터 타입을 외부에서 지정**하는 기법입니다. 객체를 생성하거나 메서드를 호출할 때 타입을 파라미터처럼 넘겨줌으로써, 다양한 타입을 하나의 코드로 처리할 수 있게 해줍니다.

- **주요 특징:** `<T>`와 같은 타입 매개변수를 사용하여, 실제 사용 시점에 구체적인 타입(String, Integer 등)을 결정합니다.
- **사용 이유:** 데이터 타입에 의존하지 않는 범용적인 코드를 작성하기 위해 사용합니다.

---

## **2. 왜 사용하는가? (장점)**

- **컴파일 타임 타입 체크:** 잘못된 타입이 들어오는 것을 컴파일 단계에서 방지하여 런타임 에러를 줄여줍니다.
- **형변환(Casting) 제거:** 데이터를 꺼낼 때마다 일일이 형변환을 할 필요가 없어 코드가 간결해집니다.
- **재사용성 향상:** 하나의 클래스나 메서드로 여러 타입의 데이터를 처리할 수 있어 중복 코드를 방지합니다.

---

## **3. 코드 예시**

```java
// 제네릭 클래스 선언: <T>는 나중에 결정될 타입을 의미합니다.
public class Box<T> {
    private T content;

    public void set(T content) {
        this.content = content;
    }

    public T get() {
        return content;
    }
}

// 실제 사용 시
Box<String> nameBox = new Box<>(); // T를 String으로 지정
nameBox.set("UMC 10th");
String name = nameBox.get(); // 별도의 형변환이 필요 없음
```

---

## **4. 핵심 키워드 정리**

- **타입 파라미터 (Type Parameter):** `<T>` (Type), `<E>` (Element), `<K>` (Key), `<V>` (Value) 등 관례적으로 사용하는 기호입니다.
- **와일드카드 (Wildcard):** `<?>`를 사용하여 타입을 정확히 모를 때나, 상속 관계를 고려한 타입 제한(`<? extends T>`, `<? super T>`)이 필요할 때 사용합니다.
- **타입 소거 (Type Erasure):** 하위 호환성을 위해 컴파일 시점에 제네릭 정보를 제거하고 원시 타입(Object)으로 변환하는 과정을 의미합니다.


# 4. @RestControllerAdvice이란?

**@RestControllerAdvice**는 Spring 프레임워크에서 **전역적으로 예외를 처리**하기 위해 사용하는 어노테이션입니다.

- **역할:** 여러 컨트롤러(@RestController)에서 발생하는 예외를 한곳에서 잡아 처리하는 **중앙 집중식 예외 처리기**입니다.
- **구성:** `@ControllerAdvice`와 `@ResponseBody`가 합쳐진 형태입니다. 즉, 예외 처리 결과를 JSON과 같은 데이터 형태로 직접 반환할 때 사용합니다.

---

## **왜 사용하는가? (장점)**

- **코드 중복 제거:** 모든 컨트롤러마다 `try-catch`문을 넣지 않아도 되어 코드가 깔끔해집니다.
- **관심사의 분리 (Separation of Concerns):** 비즈니스 로직과 예외 처리 로직을 완전히 분리할 수 있습니다.
- **일관된 응답 구조:** 에러 발생 시 사용자에게 항상 동일한 형식의 에러 응답(JSON)을 내려줄 수 있어 프론트엔드와의 협업이 쉬워집니다.

---

## **코드 예시**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    // 특정 예외(ex: IllegalArgumentException)를 잡아 처리합니다.
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgumentException(IllegalArgumentException e) {
        // 에러 메세지와 함께 400 Bad Request 상태 코드를 반환합니다.
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(e.getMessage());
    }

    // 모든 예외를 통칭하여 처리할 수도 있습니다.
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleAllExceptions(Exception e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("서버 내부 오류가 발생했습니다.");
    }
}
```

---

## **핵심 키워드 정리**

- **@ExceptionHandler:** 특정 예외 클래스를 지정하여 해당 예외가 발생했을 때 실행할 메서드를 정의합니다.
- **@ResponseStatus:** HTTP 응답 상태 코드를 설정합니다.
- **ResponseEntity:** 에러 응답 바디와 상태 코드를 더 세밀하게 제어하고 싶을 때 반환 타입으로 사용합니다.


# 5. Optional이란?

## **1. Optional이란?**

- Optional는 '값이 존재할 수도 있고, 없을 수도 있는' 객체를 감싸는 래퍼 클래스(Wrapper Class) 입니다.
- **핵심 목적:** 값이 `null`일 수 있는 상황에서 직접 `null`을 반환하는 대신, Optional 객체에 담아 반환함으로써 **NullPointerException(NPE)** 발생 가능성을 원천적으로 줄이는 것입니다.
- **비유:** 값이 들어있을 수도, 비어있을 수도 있는 **상자**라고 생각하시면 이해가 쉽습니다.

---

## **2. 왜 사용하는가? (장점)**

- **NPE 방지:** `null` 체크를 위해 `if (obj != null)`와 같은 코드를 반복해서 쓰지 않아도 됩니다.
- **가독성 향상:** 메서드의 반환 타입이 `Optional`이라면, 개발자는 "아, 이 메서드는 결과가 없을 수도 있겠구나"라고 명확히 인지하고 대비할 수 있습니다.
- **함수형 프로그래밍:** `map`, `filter`, `ifPresent` 등을 활용하여 간결한 연쇄 호출이 가능해집니다.

---

## **3. 코드 예시**

```java
// 1. Optional 객체 생성
Optional<String> opt = Optional.ofNullable(getName()); // null일 수도 있는 값을 감싸기

// 2. 값이 있을 때만 실행
opt.ifPresent(name -> System.out.println("이름: " + name));

// 3. 값이 없으면 기본값 반환 (orElse)
String finalName = opt.orElse("이름 없음");

// 4. 값이 없으면 예외 던지기 (orElseThrow)
String validName = opt.orElseThrow(() -> new IllegalArgumentException("값이 없습니다."));
```

---

## **4. 핵심 메서드 정리**

- **`Optional.of(value)`:** 값이 확실히 `null`이 아닐 때 사용 (null이면 NPE 발생).
- **`Optional.ofNullable(value)`:** 값의 `null` 여부를 모를 때 사용.
- **`isPresent()`:** 값이 들어있는지 확인 (비어있으면 false).
- **`get()`:** 값을 직접 꺼냄 (비어있으면 예외가 발생하므로 주의해서 사용).
