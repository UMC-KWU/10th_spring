미션 수행한 깃허브 브랜치
https://github.com/central324/10th_Spring_practice/tree/feat/chapter8


이번 과제를 진행하면서 다음 내용을 구현했다.

- Spring Security 적용
- 회원가입 API 구현
- 로그인 API 구현
- 비밀번호 BCrypt 암호화 적용
- Public / Private API 분리
- 인증 실패 / 인가 실패 예외 응답 통일

---


### 1. `MemberReqDTO.java`
회원가입과 로그인 요청을 받기 위한 DTO를 작성했다.

회원가입 DTO에는 다음 정보를 받도록 구성했다.

- 이름
- 비밀번호
- 이메일
- 성별
- 생년월일
- 전화번호
- 주소

로그인 DTO에는 폼 로그인에 필요한 다음 정보를 받도록 구성했다.

- 이메일
- 비밀번호

예시 코드는 아래와 같다.

```java
public class MemberReqDTO {

    @Getter
    public static class SignupDTO {
        @NotBlank(message = "이름은 필수입니다.")
        private String name;

        @NotBlank(message = "비밀번호는 필수입니다.")
        private String password;

        @NotBlank(message = "이메일은 필수입니다.")
        @Email(message = "올바른 이메일 형식이어야 합니다.")
        private String email;

        @NotNull(message = "성별은 필수입니다.")
        private Gender gender;

        @NotNull(message = "생년월일은 필수입니다.")
        private LocalDate birth;

        @NotBlank(message = "전화번호는 필수입니다.")
        private String phoneNumber;

        @NotNull(message = "주소는 필수입니다.")
        private AddressType address;
    }

    @Getter
    public static class LoginDTO {
        @NotBlank(message = "이메일은 필수입니다.")
        @Email(message = "올바른 이메일 형식이어야 합니다.")
        private String email;

        @NotBlank(message = "비밀번호는 필수입니다.")
        private String password;
    }
}
```

이 DTO를 통해 회원가입과 로그인에 필요한 요청 데이터를 명확하게 분리할 수 있었다.

---

### 1-2. `SecurityConfig.java`
Spring Security의 핵심 설정 파일이다.

- `PasswordEncoder`를 BCrypt 방식으로 등록
- `AuthenticationManager` Bean 등록
- 회원가입 / 로그인 API는 `permitAll()`
- 그 외 모든 API는 `authenticated()`
- 인증 실패 / 인가 실패 핸들러 등록

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/users/signup", "/api/users/login").permitAll()
    .anyRequest().authenticated()
)
```

이 설정을 통해 회원가입과 로그인은 누구나 접근할 수 있도록 하고, 나머지 API는 로그인한 사용자만 접근하도록 구성했다.

---

### 1-3. `CustomUserDetails.java`
Spring Security에서 인증된 사용자 정보를 다루기 위해 `UserDetails` 구현체를 만들었다.

이 클래스에는 프로젝트의 `Member` 엔티티를 담아두고, 인증 이후 컨트롤러에서  
`@AuthenticationPrincipal`로 현재 로그인한 사용자를 꺼내 쓸 수 있도록 구성했다.

---

### 1-4. `CustomUserDetailsService.java`
로그인 시 이메일을 기준으로 회원을 조회하기 위해 `UserDetailsService`를 구현했다.

```java
@Override
public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
    Member member = memberRepository.findByEmail(email)
            .orElseThrow(() -> new UsernameNotFoundException("사용자를 찾을 수 없습니다."));
    return new CustomUserDetails(member);
}
```

Spring Security가 로그인 과정에서 사용자를 조회할 기준이 필요하기 때문이다. 그 기준을 email로 잡았다.  

---

### 1-5. 회원가입 서비스 로직
회원가입 기능에서는 사용자가 입력한 비밀번호를 그대로 저장하지 않고,  
반드시 `BCryptPasswordEncoder`로 암호화한 뒤 저장하도록 구현했다.

```java
String encodedPassword = passwordEncoder.encode(request.getPassword());
```

```java
Member member = Member.builder()
        .name(request.getName())
        .email(request.getEmail())
        .password(encodedPassword)
        .gender(request.getGender())
        .birth(request.getBirth())
        .phoneNumber(request.getPhoneNumber())
        .address(request.getAddress())
        .build();
```

비밀번호를 평문으로 저장하면 보안상 매우 위험하다.  
BCrypt는 솔트가 포함된 해시 방식이기 때문에 같은 비밀번호라도 다른 결과값으로 저장되어 보다 안전하게 가져갈 수 있다.

---

### 1-6. 로그인 API
로그인 API는 email과 password를 받아 `AuthenticationManager`로 인증하도록 구현했다.

예시 코드는 아래와 같다.

```java
Authentication authentication = authenticationManager.authenticate(
        new UsernamePasswordAuthenticationToken(
                request.getEmail(),
                request.getPassword()
        )
);
```

인증이 성공하면 SecurityContext에 인증 정보를 저장하고, 이후 Private API에 접근할 수 있도록 처리했다.
이를 통해 로그인 성공 후 인증된 사용자 상태를 유지할 수 있다.

---

### 1-7. 인증 / 인가 예외 처리
과제 조건에 맞게 인증 실패와 인가 실패 응답 형식을 통일하기 위해  
`exceptionHandling`을 설정했다.

```java
.exceptionHandling(exception -> exception
    .authenticationEntryPoint(customAuthenticationEntryPoint)
    .accessDeniedHandler(customAccessDeniedHandler)
)
```

이를 통해 다음 두 가지 상황에서 응답 형식을 일정하게 유지할 수 있었다.

- 로그인하지 않고 Private API에 접근한 경우
- 로그인은 했지만 권한이 없는 경우

즉, 단순히 기본 에러 페이지를 반환하는 것이 아니라 프로젝트에서 사용하는 공통 응답 형식으로 처리할 수 있게 되었다.

---

## 2. Public API / Private API 분리

이번 과제에서는 회원가입 API와 로그인 API를 Public API로 두고,  
그 외 API는 모두 Private API로 설정했다.

정리하면 다음과 같다.

- Public API
  - `/api/users/signup`
  - `/api/users/login`

- Private API
  - 그 외 나머지 API 전체

회원가입과 로그인은 인증 하기 전에 접근이 가능하도록 해야하고,
그 외의 기능은 모두 인증된 사용자만 접근이 가능하도록 해야한다.

---

## 3. 실행 결과

1. 회원가입 API 호출이 정상적으로 성공하는지 확인
2. 로그인 API 호출이 정상적으로 성공하는지 확인
3. DB에서 비밀번호가 BCrypt 해시 형태로 저장되는지 확인
4. 로그인하지 않은 상태에서 Private API 접근 시 인증 필요 응답이 오는지 확인
5. 로그인 후 동일한 Private API 접근 시 정상 응답이 오는지 확인


- 회원가입 성공 화면
<img width="832" height="886" alt="스크린샷 2026-05-16 220228" src="https://github.com/user-attachments/assets/6e4e2a8c-1da2-4265-89c9-89bfb7282d60" />


- 로그인 성공 화면
<img width="767" height="825" alt="스크린샷 2026-05-16 220245" src="https://github.com/user-attachments/assets/8a07bcaf-f694-481f-971f-7ff1a9b17fbd" />


- MySQL Workbench에서 BCrypt 해시 비밀번호 저장 확인 화면
<img width="608" height="24" alt="스크린샷 2026-05-17 000130" src="https://github.com/user-attachments/assets/69a2f939-5838-45ea-a352-dadaa46ba710" />


- 로그인 전 Private API 접근 실패 화면
<img width="964" height="859" alt="스크린샷 2026-05-17 000722" src="https://github.com/user-attachments/assets/8d1f4229-0c34-48d1-8885-b45bc5632b6b" />


- 로그인 후 동일 API 접근 성공 화면
<img width="987" height="901" alt="스크린샷 2026-05-17 000838" src="https://github.com/user-attachments/assets/7f6dc658-6867-4f78-8ec6-8a520104c6d4" />
