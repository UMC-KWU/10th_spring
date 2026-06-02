미션 수행한 Github 주소: https://github.com/shinhyunwooo/umc10th1/tree/feature/mission09

1. Spring Security 기반 회원가입 API 구현

Spring Security를 적용하여 회원가입 및 로그인 기능을 구현하였다. 회원가입 API는 Public API로 설정하여 인증 없이 접근 가능하도록 구성하였다.

회원가입 요청 시 사용자의 이메일, 비밀번호, 이름, 전화번호 등의 정보를 입력받도록 구현하였다. 사용자의 비밀번호는 보안 강화를 위해 평문으로 저장하지 않고 BCryptPasswordEncoder를 사용하여 암호화 후 데이터베이스에 저장하였다.

이를 통해 DB 내부에 실제 비밀번호가 아닌 암호화된 문자열(Hash)이 저장되도록 구성하였다.

회원가입 성공 후 Swagger에서 정상적으로 사용자 생성 여부를 확인하였으며, IntelliJ Database 기능을 이용해 member 테이블에 데이터가 저장되는 것도 확인하였다.


2. JWT 기반 로그인 구현

로그인 API 역시 Spring Security 인증 구조를 기반으로 구현하였다.

사용자가 이메일과 비밀번호를 입력하면 DB에 저장된 회원 정보를 조회하고, 입력한 비밀번호와 암호화된 비밀번호를 BCryptPasswordEncoder.matches()를 사용하여 비교하도록 구현하였다.

인증이 성공하면 JWT AccessToken을 생성하고 응답값으로 반환하도록 구현하였다.

JWT 토큰에는 사용자 식별 정보(email)를 포함하였으며, 이후 인증이 필요한 API 접근 시 Authorization Header를 통해 전달할 수 있도록 구성하였다.

로그인 성공 시 Swagger 응답에서 AccessToken이 정상 발급되는 것을 확인하였다.


3. Public API / Private API 분리

미션 요구사항에 맞게 Spring Security 설정에서 API 접근 권한을 구분하였다.

Public API:

/api/auth/**
회원가입
로그인

인증 없이 접근 가능

Private API:

/api/v1/**

JWT 인증 필요

인증이 완료된 사용자만 접근 가능하도록 설정하였다.

SecurityConfig 내부에서 permitAll()과 authenticated()를 사용하여 접근 권한을 분리하였다.

4. JWT 인증 필터 적용

JWT 인증을 처리하기 위해 요청마다 토큰을 검사하는 JWT Filter를 구현하였다.

클라이언트가 요청 시 Authorization 헤더에

Bearer {JWT토큰}

형식으로 토큰을 전달하면 다음 과정을 수행한다.

Header에서 JWT 추출
토큰 유효성 검사
사용자 정보 추출
SecurityContext에 인증 정보 저장

토큰 검증이 완료되면 인증된 사용자로 처리되어 Private API 접근이 가능하도록 구현하였다.

5. 마이페이지 API 구현

워크북 형식과 유사하게 마이페이지 조회 API(/api/v1/users/me)를 구현하였다.

로그인 후 발급받은 JWT 토큰을 Swagger Authorize 기능에 입력하여 인증을 수행한 뒤 API를 호출하였다.

정상적인 토큰 전달 시:

{
   "memberId":1,
   "email":"jwt@test.com",
   "name":"JWT테스트",
   "phoneNumber":"01012345678",
   "profileUrl":null,
   "point":0
}

형태로 회원 정보가 조회되는 것을 확인하였다.
![alt text](image.png)
6. 인증 실패 예외 처리 확인

JWT 토큰을 제거한 상태에서 Private API(/api/v1/users/me)를 호출한 결과:

401 Unauthorized
인증되지 않았습니다.

응답이 발생하는 것을 확인하였다.

이를 통해 인증되지 않은 사용자는 Private API 접근이 차단되는 것을 확인하였으며, 인증/인가 실패 응답이 Exception Handling을 통해 통일된 형식으로 처리되는 것을 검증하였다.


7. 정리

Spring Security와 JWT를 이용한 회원가입 및 로그인 기능을 구현하였다. BCrypt를 이용한 비밀번호 암호화, JWT 토큰 발급 및 검증, Public/Private API 분리, 마이페이지 조회 기능을 구현하였다.

또한 Swagger와 Database를 활용하여 실제 데이터 저장 및 인증 과정을 검증하였으며, JWT 기반 인증 흐름이 정상적으로 동작함을 확인하였다.