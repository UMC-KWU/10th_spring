미션 수행한 Github 주소 : https://github.com/shinhyunwooo/umc10th1/tree/feature/mission05

- (필수) 프로젝트 세팅을 마친 상태에서 응답 통일 객체, 에러 핸들링할 객체를 생성하기
(워크북에서 언급 안한 방식으로 생성 시, 그 이유도 함께 기재!)
    
    프로젝트 세팅을 마친 뒤 API 응답 형식을 통일하기 위해 ApiResponse<T> 객체를 생성하였습니다.
    
    응답 구조는 isSuccess, code, message, result로 구성하였으며, 성공 응답은 onSuccess, 실패 응답은 onFailure 메서드를 통해 생성하도록 구현하였습니다.
    
    성공 코드와 에러 코드는 각각 BaseSuccessCode, BaseErrorCode 인터페이스를 기준으로 분리하였습니다.
    
    공통 응답 코드는 GeneralSuccessCode, GeneralErrorCode에서 관리하고, 회원 도메인 관련 응답 코드는 MemberSuccessCode, MemberErrorCode에서 관리하도록 구성하였습니다.
    
    에러 핸들링을 위해 프로젝트 공통 예외 클래스인 ProjectException을 생성하였고, @RestControllerAdvice를 사용하는 GeneralExceptionAdvice에서 예외를 감지하여 통일된 응답 형식으로 반환하도록 구현하였습니다.
    
    이를 통해 정상 응답뿐 아니라 예외 상황에서도 동일한 JSON 응답 구조를 유지할 수 있도록 하였습니다.

- (필수) 2주차 미션으로 진행한 API 명세서를 기반으로 Controller, DTO 제작하기
(Service & Repository는 다음 주차에서 하겠습니다!!)
    
    2주차 미션에서 작성했던 마이페이지 조회 API 명세를 기반으로 회원 정보를 조회하는 Controller와 DTO를 제작하였습니다.
    
    API 경로는 다음과 같이 설정하였습니다.
    
    - POST /api/v1/users/me
    
    요청 DTO는 MemberReqDTO.GetInfo로 작성하였으며, 사용자 식별을 위한 id 값을 받도록 구성하였습니다.
    
    응답 DTO는 MemberResDTO.GetInfo로 작성하였으며, 사용자 이름, 프로필 이미지 URL, 이메일, 전화번호, 포인트 정보를 반환할 수 있도록 구성하였습니다.
    
    또한 DTO는 record를 사용하여 작성하였습니다. record는 불변 객체를 간결하게 표현할 수 있기 때문에 요청과 응답 데이터를 전달하는 DTO에 적합하다고 판단하였습니다.