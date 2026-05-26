미션 수행한 Github 주소: https://github.com/shinhyunwooo/umc10th1/tree/feature/mission08

1. Spring Security 적용 및 회원가입 API 구현
- 구현 목표
Spring Security를 프로젝트에 적용하고, 회원가입 API를 구현했다.
회원가입 시 폼 로그인을 위한 email, password를 추가로 입력받도록 구성했으며, 비밀번호는 평문으로 저장하지 않고 BCrypt 방식으로 암호화하여 저장했다.

- 구현 방식
build.gradle에 Spring Security 의존성을 추가하고, SecurityConfig를 생성하여 보안 설정을 적용했다.
회원가입 요청은 AuthController에서 처리하고, MemberService에서 PasswordEncoder를 사용해 비밀번호를 BCrypt로 암호화한 뒤 DB에 저장하도록 구현했다.

- 구현 흐름
1. 클라이언트가 /api/auth/sign-up으로 회원가입 요청을 보낸다.
2. 요청 Body로 email, password, name 등을 전달받는다.
3. MemberService에서 passwordEncoder.encode()를 사용해 비밀번호를 암호화한다.
4. 암호화된 비밀번호와 회원 정보를 Member 엔티티로 생성한다.
5. MemberRepository를 통해 DB에 저장한다.

- 구현 포인트
Spring Security 의존성을 추가했다.
PasswordEncoder Bean을 등록했다.
BCryptPasswordEncoder를 사용하여 비밀번호를 암호화했다.
DB에 비밀번호가 평문이 아닌 $2a$... 형태의 해시값으로 저장되도록 구현했다.
로그인 처리를 위해 UserDetailsService와 UserDetails 구현체를 추가했다.



2. 회원가입 API Public 설정 및 나머지 API Private 설정
- 구현 목표
회원가입 API는 로그인 없이 접근 가능한 Public API로 설정하고, 그 외 API는 로그인이 필요한 Private API로 설정했다.
또한 인증/인가 실패 시 HTML 로그인 페이지가 아니라 통일된 JSON 응답이 반환되도록 구현했다.

- 구현 방식
SecurityConfig에서 requestMatchers()를 사용해 Public API 경로를 지정했다.
/api/auth/**, Swagger 관련 경로는 permitAll()로 설정하여 로그인 없이 접근 가능하게 했고, 그 외 모든 요청은 authenticated()로 설정하여 로그인한 사용자만 접근할 수 있도록 했다.
인증 실패와 인가 실패 응답을 통일하기 위해 CustomEntryPoint와 CustomAccessDeniedHandler를 구현하고, exceptionHandling()에 등록했다.

- 구현 흐름
1. 사용자가 Public API인 /api/auth/sign-up에 접근한다.
2. 해당 API는 permitAll()로 등록되어 있어 로그인 없이 접근 가능하다.
3. 사용자가 Private API인 /api/reviews/my 등에 로그인 없이 접근한다.
4. Spring Security가 인증되지 않은 사용자임을 감지한다.
5. CustomEntryPoint가 실행되어 통일된 JSON 에러 응답을 반환한다.

- 구현 포인트
회원가입 API를 Public API로 설정했다.
Swagger 경로도 Public으로 열어 테스트 가능하게 했다.
그 외 API는 Private API로 설정했다.
인증되지 않은 사용자가 Private API에 접근하면 401 Unauthorized가 반환되도록 했다.
기본 HTML 로그인 페이지 대신 프로젝트의 ApiResponse 형식에 맞춘 JSON 응답이 반환되도록 했다.