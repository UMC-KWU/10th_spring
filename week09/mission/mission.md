미션 수행한 브랜치~

https://github.com/givenhhy012/umc_10th_practice/tree/feature/%239

JWT_SECRET_KEY는 워크북에 나온것처럼 base64 난수 생성을 해주고 그걸 .env파일에 등록했다.

윈도우는 기본적으로 OpenSSL 명령어가 내장되어있지 않아, 아래의 명령어는 사용할 수 없었다.

```bash
openssl rand -base64 64
```

대신 윈도우 파워쉘(PowerShell)에서 아래의 코드를 실행시켜 난수를 생성했다.

```bash
$key = New-Object byte[] 64
[System.Security.Cryptography.RandomNumberGenerator]::Create().GetBytes($key)
$base64Key = [System.Convert]::ToBase64String($key)
$base64Key
```

**UserController**

```java
// 로그인
    @PostMapping("/auth/login")
    public ApiResponse<UserResDTO.Login> login(
            @RequestBody UserReqDTO.Login request
    ) {
        UserResDTO.Login result = userService.logIn(request);
        BaseSuccessCode code = UserSuccessCode.LOGIN_OK;
        return ApiResponse.onSuccess(code, result);
    }
```

리퀘스트 바디로 이메일과 비밀번호를 받아서 로그인을 시도한다.

**UserServiceImpl**

```java
// 로그인 JWT 방식
    public UserResDTO.Login logIn(UserReqDTO.Login request){
        // 이메일로 멤버 조회
        User user = userRepository.findByEmail(request.email())
                .orElseThrow(() -> new UserException(UserErrorCode.LOGIN_FAILED));

        // 비밀번호 검증
        if (!passwordEncoder.matches(request.password(), user.getPassword())) {
            throw new UserException(UserErrorCode.LOGIN_FAILED);
        }

        // AuthMember 생성 후 토큰 발행
        AuthUser authUser = new AuthUser(user);
        String token = jwtUtil.createAccessToken(authUser);
        return new UserResDTO.Login(user.getId(), user.getName(), token);
    }
```

아이디 / 비밀번호 검증 후 토큰을 발행하는 흐름.

아이디가 틀렸을때와 비밀번호가 틀렸을때의 에러코드는 한가지로 통일시켰다.
⇒ 프론트엔드 딴으로 가는 에러코드가 다를 경우
(아이디가 틀림 → 아이디 에러코드 / 비밀번호 틀림 → 비밀번호 에러코드),
해커가 bruteforce로 사용자 계정을 탈취할 수 있다고 하여 에러코드를 하나로 했다.

비밀번호 검증은 passwordEncoder를 이용해 인코딩된 모습끼리 비교.(DB에 인코딩 되어 저장되어있기 때문)

**로그인 실패했을 경우의 화면**

![스크린샷 2026-05-26 185234.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20185234.png)

**로그인 성공했을 경우의 화면**

![스크린샷 2026-05-26 185255.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20185255.png)

토큰이 발급된 모습.

![스크린샷 2026-05-26 185313.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20185313.png)

![스크린샷 2026-05-26 185320.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20185320.png)

![스크린샷 2026-05-26 185443.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20185443.png)

발급된 토큰으로 인증을 거친 후에 리뷰 조회(private API)가 정상적으로 되는 모습.
⇒ 헤더에 토큰을 포함.

회원가입 시 패스워드가 인코딩되어 DB에 저장된 모습.
⇒ 회원가입은 이전 구현에서 변경된 점 없음.

![스크린샷 2026-05-26 210413.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20210413.png)

### 마이페이지

**UserController**

```java
// 마이페이지
    @GetMapping("/users/me")
    public ApiResponse<UserResDTO.GetInfo> getInfo(
            @AuthenticationPrincipal AuthUser user
            ) {
        UserResDTO.GetInfo result = userService.getMyInfo(user);
        BaseSuccessCode code = UserSuccessCode.OK;
        return ApiResponse.onSuccess(code, result);
    }
```

기존에는 userId를 직접 받아서 마이페이지를 조회했었지만, @AuthenticationPrincipal을 이용하는 방식으로 개선.

**UserServiceImpl**

```java
		public UserResDTO.GetInfo getMyInfo(AuthUser user) {
		    // 컨버터를 활용
		    return UserConverter.toGetInfoResponse(user.getUser());
		}
```

AuthUser에서 User객체를 가져와서 전달.

컨버터에 전달되는 것은 변경 전과 동일하게 User객체이기 때문에 **UserConverter** 코드는 변경 없음.

**마이페이지 조회했을 때의 화면**

![스크린샷 2026-05-26 231446.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-26%20231446.png)

JWT 토큰으로 인증을 한 후, 별도의 파라미터나 리퀘스트 바디 없이도 마이페이지가 성공적으로 조회된 모습.

헤더에 토큰이 정상적으로 삽입.


<br>

## **OAuth**

워크북을 따라서 카카오 로그인 기능을 구현했다.

**로그인하는 화면**

![스크린샷 2026-05-27 224511.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-27%20224511.png)

**로그인 성공 후, 토큰 발급**

![스크린샷 2026-05-28 005544.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-28%20005544.png)

**토큰을 이용해 마이페이지 조회**

![스크린샷 2026-05-28 005657.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-28%20005657.png)

**DB에 저장된 모습**

![스크린샷 2026-05-28 010257.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-28%20010257.png)

### **구현 중 발생한 이슈(OAuth)**

![스크린샷 2026-05-27 224913.png](%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202026-05-27%20224913.png)

워크북을 그대로 따라했는데(뭔가를 놓쳤을수도..?) 로그인 시도 시 에러가 발생했다.

아무리 다시 봐도 워크북과 뭐가 다른지 모르겠어서 실패 시의 핸들러를 적용.
상세한 에러 내용을 볼 수 있도록 했다.

**SecurityConfig**

```java
 .oauth2Login(oauth -> oauth
                        // 인증 엔드포인트
                        .authorizationEndpoint(auth -> auth
                                .baseUri("/oauth/authorize")
                        )
                        // 콜백 주소
                        .redirectionEndpoint(redirect -> redirect
                                .baseUri("/oauth/callback/**")
                        )
                        // 인증 완료 후 정보 활용
                        .userInfoEndpoint(userInfo -> userInfo
                                .userService(customOAuthService)
                        )
                        // 성공 시 JWT 토큰 발행할 핸들러
                        .successHandler(oAuthSuccessHandler())
                        // !!이 부분 추가 => 실패 시 에러 메시지 반환
                        .failureHandler((request, response, exception) -> {
                            response.setContentType("application/json;charset=UTF-8");
                            response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
                            response.getWriter().write(
                                    "{\"isSuccess\":false,\"code\":\"AUTH401\",\"message\":\""
                                    + exception.getMessage().replace("\"", "'") + "\"}"
                            );
                        })
                );
```

핸들러 적용 후, 로그인 시도 시 아래와 같은 메시지가 나왔다.

```json
{"isSuccess":false,"code":"AUTH401",
	"message":"[invalid_token_response] An error occurred while attempting to retrieve the OAuth 2.0 Access Token Response: 401 Unauthorized on POST request for 'https://kauth.kakao.com/oauth/token': '{'error':'invalid_client','error_description':'Not exist client_id [null]','error_code':'KOE101'}'"}
```

이 메시지를 AI에게 물어보니 다음과 같은 답변을 줬다.

**카카오의 인증 방식 설정 문제**입니다.

Spring Security 기본값은 `client_secret_basic` (Authorization 헤더로 전송)인데, 카카오는 **POST body로 client_id를 받아야** 합니다. 그래서 카카오 측에서 `client_id [null]`로 인식합니다.

`application.yml`에 `client-authentication-method` 추가가 필요합니다.

**application.yml**

```yaml
  security:
    oauth2:
      client:
        registration:
          kakao:
            client-id: ${KAKAO_REST_API_KEY}
            client-secret: ${KAKAO_REST_API_SECRET}
            client-authentication-method: client_secret_post # 이 부분을 추가.
            authorization-grant-type: authorization_code
            redirect-uri: "http://localhost:8080/oauth/callback/kakao"
            scope:
              - profile_nickname
              - account_email
        provider:
          kakao:
            authorization-uri: "https://kauth.kakao.com/oauth/authorize"
            token-uri: "https://kauth.kakao.com/oauth/token"
            user-info-uri: "https://kapi.kakao.com/v2/user/me"
            userNameAttribute: id

```

그래서 client-authentication-method를 추가해줬고, 그 후 로그인 시도에서 성공할 수 있었다.