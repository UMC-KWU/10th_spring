- Page와 Slice

  ## [**Slice**](https://kjs990114.tistory.com/70#Slice-1)

    ```java
    public interface Slice<T> extends Streamable<T> {
    
        int getNumber();
    
        int getSize();
    
        int getNumberOfElements();
        List<T> getContent();
    
        boolean hasContent();
    
        // ...
    }
    ```

  ## [**Page**](https://kjs990114.tistory.com/70#Page-1)

    ```java
    public interface Page<T> extends Slice<T> {
    
        long getTotalElements();
    
        int getTotalPages();
    
        //...
    }
    ```

  둘 다 **페이징 결과**를 담는 객체지만 제공하는 정보가 다름.

  ⇒ Page 인터페이스는, Slice를 상속받기때문에, Slice에서 쓸수있는 모든 기능을 쓸수있으며, **총 페이지 수 , 총 element수**를 받아올 수 있는 추가적인 기능을 제공.

  Page의 경우, Limit을 활용해 조회를 한 후 , 전체 element개수를 조회하기위해 **추가 쿼리 1회**가 실행된다 . (Select Count(*) from ~)

  Slice의 경우에는, Limit 의 값을 기존 페이지 사이즈 + 1을 함으로써 다음 페이지가 있는지 확인하므로, 1번의 쿼리로 조회가 가능하다.

  ⇒ 데이터의 수가 많을 경우 Page의 성능이 급감.

  Page를 사용하는 이유는 검색 페이지와 같은 페이지 수가 중요한 기능이나, 사용자의 편의를 위한 경우 사용.

  Slice를 사용하는 경우는, 전체 페이지 수, 전체 element수가 필요없는 모바일 환경의 무한스크롤 같은 환경에서 사용.

- Java stream API

  : 데이터 컬렉션을 함수형 스타일로 처리할 수 있게 하는 API.

  ⇒ 데이터를 추상화하고 처리하는 다양한 연산을 수행할 수 있게 해준다. 특히, 데이터 소스에 대한 계산 로직을 고수준에서 간결하게 표현할 수 있어, 코드의 가독성과 유지보수성이 향상된다.

  ### **스트림 API의 특징**

    - **저장소가 없음(No Storage)**: 스트림 자체는 데이터를 저장하지 않는다. 대신, `컬렉션`, `배열`, `I/O 채널` 등의 데이터 소스를 표현하는 데 사용된다.
    - **함수형(Functional in nature)**: 스트림 연산은 주로 람다 표현식을 사용하여 함수형 프로그래밍의 접근 방식을 취한다. 이로 인해 `외부 반복(external iteration)` 대신 `내부 반복 (internal iteration)`을 사용할 수 있다.
    - **지연성 추구(Laziness-seeking)**: 많은 스트림 연산들(`filtering`, `mapping`)은 지연된다는 특징을 갖고 있다. 즉, 실제로 필요할 때까지 계산을 수행하지 않는다.
    - **무한성(Possibly unbounded)**: 스트림은 한정된 데이터뿐만 아니라 무한한 데이터에 대해서도 작업을 수행할 수 있다. 예를 들어, 난수 스트림은 무한하다.
    - **소비 가능성(Consumable)**: 스트림의 요소들은 소비된다. 즉, 한 번 사용된 스트림은 재사용할 수 없다.

  ### 기본 구조

    ```java
    List<Member> members = memberRepository.findAll();
    
    members.stream()          // 1. 스트림 생성
        .filter(...)          // 2. 중간 연산 (가공)
        .map(...)             // 2. 중간 연산 (변환)
        .collect(...)         // 3. 최종 연산 (결과 수집)
    ```

  **중간 연산**: 스트림의 요소들을 처리하고, 변환하는 데 사용되는 연산.

  ⇒ 각 중간 연산은 새로운 스트림을 반환하므로, 연속적으로 체이닝하여 사용할 수 있으며, 이러한 특성으로 인해 코드의 가독성과 유지보수성이 크게 향상.

  **최종 연산**: 스트림의 요소들에 대한 계산을 실행하고 결과를 도출하는 연산.

- 객체 그래프 탐색

  : **연관관계를 따라 객체를 탐색**하는 것.

    ```java
    // 객체 그래프 탐색
    member.getTeam().getName();           // Member → Team
    member.getTeam().getLeague().getName(); // Member → Team → League
    member.getOrders().get(0).getItem();  // Member → Order → Item
    ```

  ⇒ 객체들이 **그래프처럼 연결**되어 있어서 `.`으로 계속 탐색 가능.

  특정 객체에 지연로딩을 설정해놓았다면 해당 객체를 탐색하는 시점에 로딩.
  ⇒ N+1문제 발생 가능(Fetch Join으로 해결)

  **NPE 방지 탐색 방법**

    ```java
    // ❌ NPE 위험
    String name = member.getTeam().getName();
    
    // ✅ Optional로 안전하게
    String name = Optional.ofNullable(member.getTeam())
        .map(Team::getName)
        .orElse("팀 없음");
    ```

  NPE가 발생할 우려가 있다면 Optional을 이용하여 안전하게 탐색 가능.

- @Valid vs @Validated
    1. **@Valid**는 자바 표준 스펙이고 **@Validated**는 스프링에서 제공하는 어노테이션이다.
    2. **@Validated**를 통해 그룹 유효성 검사나 Controller가 아닌 다른 계층에서 유효성 검증 가능.
    3. **@Valid**는 **MethodArgumentNotValidException** 예외를,
       **@Validated**는 **ConstraintViolationException** 예외를 발생시킨다.

  **`@Valid`** 어노테이션은 주로 request body를 검증하는데 많이 사용.
  reqDTO에서 @NotNull 등의 어노테이션을 적어주고, 컨트롤러에서 @Valid 어노테이션으로 적용.

  @Validated는 그룹별 유효성 검사.
  ⇒ 상황마다 다른 검증 규칙을 적용가능.

  ex) 회원 가입할 때는 이름이 필수지만, 회원 정보 수정할 때는 이름이 선택일 수 있음. 이런 상황에서 같은 DTO를 쓰면서도 검증 규칙을 다르게 적용.

  대부분의 경우 **@Valid**로 충분하고, 동일한 DTO를 여러 상황에서 재사용할 때만 **@Validated**가 필요.