미션 수행한 깃허브 브랜치 : 
https://github.com/central324/10th_Spring_practice/tree/feat/chapter9


## 1. JWT 토큰 발급 로직 구현

이번 과제에서는 JWT 토큰 기반으로 인증을 처리할 수 있도록 Access Token 발급 로직을 구현했다.

사용자가 로그인에 성공하면 서버는 해당 사용자의 식별 정보를 기반으로 JWT Access Token을 생성하고,  
응답 본문에 토큰을 담아 반환하도록 구성했다.

이 과정을 통해 이후 요청에서는 로그인 상태를 서버 세션에 저장하지 않고,  
클라이언트가 직접 JWT 토큰을 포함해서 인증할 수 있도록 만들었다.

로그인 성공 시 반환되는 응답 예시는 다음과 같은 형태이다.

```json
{
  "accessToken": "JWT_ACCESS_TOKEN"
}
```

JWT를 사용하면 이후 요청마다 토큰을 통해 사용자를 식별할 수 있고,
Spring Security의 인증 흐름과도 자연스럽게 연결할 수 있다.

## 1-2. JWT 생성 유틸 구현
JWT 발급과 검증을 분리해서 처리하기 위해 JWT 관련 유틸 클래스를 작성했다.

이 클래스에서는 다음 기능을 담당하도록 구성했다.

토큰 생성
토큰에서 사용자 정보 추출
토큰 유효성 검증
만료 여부 확인
즉, 로그인 성공 시에는 토큰을 생성하고,
인증이 필요한 요청이 들어왔을 때는 요청 헤더의 토큰을 읽어서 유효한지 검증하는 흐름으로 사용할 수 있도록 했다.

이렇게 JWT 관련 책임을 별도 클래스로 분리해두면 이후 Access Token 재발급이나 Claim 확장 같은 작업도 수월하게 할 수 있다.

## 1-3. JWT 인증 필터 적용
이번 과제의 핵심은 로그인 이후 요청을 JWT 기반으로 인증하는 것이므로,
요청마다 Authorization Header를 확인하는 JWT 인증 필터를 추가했다.

클라이언트가 다음과 같이 요청 헤더에 토큰을 담아서 보내면,

Authorization: Bearer {accessToken}

JWT 필터가 해당 토큰을 추출한 뒤,

토큰이 존재하는지 확인하고
형식이 올바른지 검사하고
유효한 토큰이면
토큰에서 사용자 정보를 꺼내 인증 객체를 생성하고
SecurityContext에 저장
하도록 구현했다.

이를 통해 인증이 필요한 API 요청은 매번 JWT를 통해 사용자 인증 여부를 확인할 수 있게 되었다.

## 1-4. SecurityContext에 인증 정보 저장
JWT 필터에서 토큰 검증이 끝난 뒤에는
해당 사용자를 인증된 사용자로 인식할 수 있도록 SecurityContext에 인증 정보를 저장하도록 구성했다.

이 과정을 거치면 컨트롤러나 서비스 단에서 현재 로그인한 사용자의 정보를 사용할 수 있고,
인증이 필요한 API에서도 별도의 세션 없이 사용자를 식별할 수 있다.

## 2. JWT 기반 인증 테스트
이번 과제에서는 Swagger에서 JWT 토큰을 직접 입력해 인증이 필요한 API를 테스트할 수 있도록 구성했다.

로그인 API를 먼저 호출해 Access Token을 발급받고,
이후 Swagger의 Authorize 기능에 해당 토큰을 입력한 다음
인증이 필요한 API를 호출하는 방식으로 테스트를 진행했다.

이 과정을 통해 단순히 토큰이 발급되는 것만 확인한 것이 아니라,
실제로 발급된 토큰이 이후 요청에서 정상적으로 인증 수단으로 동작하는 것까지 검증할 수 있었다.

## 3. 마이페이지 조회 기능 구현
워크북 형식에 맞춰 인증된 사용자의 정보를 조회할 수 있는 마이페이지 API를 구현했다.

마이페이지는 인증이 필요한 API이므로,
JWT 토큰 없이 요청할 수 없고
반드시 유효한 Access Token이 포함된 상태에서만 접근 가능하도록 구성했다.

토큰이 유효한 경우에는 현재 로그인한 사용자를 식별하여
해당 사용자의 정보를 조회하고 응답으로 반환하도록 구현했다.

### 1. 회원가입 성공화면
<img width="856" height="868" alt="Mission_9_(1)" src="https://github.com/user-attachments/assets/894125e0-5b3b-4aa2-88e5-a1859bff2f25" />


### 2. 로그인 성공 및 JWT 토큰 발급 화면
<img width="814" height="781" alt="Mission_9_(2)" src="https://github.com/user-attachments/assets/f2cbc823-f358-4244-9b21-c7ea7fd8330d" />

로그인 요청 후 Access Token이 정상적으로 반환되는지 확인하는 화면이다.


### 3. 인증 적용 화면
<img width="1070" height="502" alt="Mission_9_(3)" src="https://github.com/user-attachments/assets/ab67b777-747f-49de-aa59-1f82dab916e8" />
발급받은 JWT 토큰을 Swagger에 입력하여 인증이 적용된 상태를 확인한 화면이다.
이 과정을 통해 이후 Private API 요청에 JWT를 포함할 수 있도록 했다.


### 4. 마이페이지 조회 성공 화면
<img width="981" height="691" alt="Mission_9_(4)" src="https://github.com/user-attachments/assets/2dcb8aac-d56b-45e4-803a-9e5048b78a5b" />
인증된 사용자의 마이페이지 조회 결과가 정상적으로 반환되는 것을 확인한 화면이다.
즉, JWT를 통해 식별된 사용자의 정보를 기반으로 마이페이지 기능이 정상 동작함을 확인하였다.


### 5. DB 저장 결과 화면
<img width="975" height="108" alt="Mission_9_(5)" src="https://github.com/user-attachments/assets/153b4156-fae6-4d84-9894-bfb001dd121b" />
회원가입 이후 DB에서 사용자 정보가 실제로 저장됐는지 확인한 화면이다.


