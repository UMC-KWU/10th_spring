미션 수행한 브랜치

https://github.com/givenhhy012/umc_10th_practice/tree/feature/%237

- (필수) 내가 진행중인 미션 조회하기
  (오프셋 기반 페이지네이션으로 응답하기, 사용자 ID는 Request Body에서 받기 **하드코딩 X**)
    - **진행중**(**isComplete  = FALSE)**

![진행중미션.png](%EC%A7%84%ED%96%89%EC%A4%91%EB%AF%B8%EC%85%98.png)

  쿼리 파라미터로 미션 진행 완료 여부를 받아서 진행중/진행완료를 선택해서 조회할 수 있도록 했다.

    ```java
    // 내가 진행중 or 진행 완료한 미션 조회
        @PostMapping("/missions")
        public ApiResponse<PageResDTO.PaginationWithOffset<MissionResDTO.MyMissionItem>> getMyMission(
                @RequestParam MissionIsCompleted isComplete,
                @RequestParam Integer pageSize,
                @RequestParam Integer pageNumber,
                @RequestParam(required = false) String sort,
                @RequestBody @Valid MissionReqDTO.GetMyMission dto
        ) {
            PageResDTO.PaginationWithOffset<MissionResDTO.MyMissionItem> result = missionService.getMyMissions(isComplete, pageSize, pageNumber, sort, dto);
            BaseSuccessCode code = MissionSuccessCode.MY_MISSION_OK;
            return ApiResponse.onSuccess(code, result);
        }
    ```

  유저의 아이디는 ReqDTO로 받도록 설정했다.

  페이지네이션을 위한 틀(컨버터, ResDTO)은 global로 두어, 다른 코드에서도 페이지네이션 틀을 재사용할 수 있도록 설계했다.


- (필수) 내가 생성한 리뷰들 조회하기
  (커서 기반 페이지네이션으로 응답하기, 사진 부분 제외, ID 순, 별점 순 모두 구현하기)

  **내가 생성한 리뷰들 조회하기**

  /api/reviews/my

  미션 참고 사진을 확인해보면

  내가 작성한 리뷰의 총 개수,

  리뷰 정보(별점, 내용 등),

  리뷰에 대한 답글

  이 포함된걸로 보여서 해당 정보들을 포함하도록 응답을 구성했다.

  커서 방식도 마찬가지로 페이지네이션 컨버터, ResDTO 틀을 global로 두었다.

  (+일반적인 경우에서 총 카운트를 세야한다면, 커서방식을 굳이 사용할 이유는 없는것 같다.)

    

    - **ID순**
![리뷰_ID순.png](%EB%A6%AC%EB%B7%B0_ID%EC%88%9C.png)

    - **별점순(rate)**

      별점이 같다면 리뷰아이디 내림차순으로 설계했다.

        ![리뷰_별점순.png](%EB%A6%AC%EB%B7%B0_%EB%B3%84%EC%A0%90%EC%88%9C.png)
- (필수) Request Body가 있는 API에 검증 어노테이션 붙혀 검증하기
  (GeneralExceptionAdvice에 Exception 정의해야함, 아무 API 상관 X)

![검증1.png](%EA%B2%80%EC%A6%9D1.png)

    ```java
    // @Valid 어노테이션 검증 실패 예외
        @ExceptionHandler(MethodArgumentNotValidException.class)
        public ResponseEntity<ApiResponse<Map<String, String>>> handleMethodArgumentNotValidException(
                MethodArgumentNotValidException e
        ){
            // 검증 실패한 변수명과 실패 이유를 담을 Map
            Map<String, String> errors = new HashMap<>();
            e.getBindingResult().getFieldErrors().forEach(error -> {
                errors.put(error.getField(), error.getDefaultMessage());
            });
    
            BaseErrorCode code = GeneralErrorCode.BAD_REQUEST;
            return ResponseEntity.status(code.getStatus())
                    .body(ApiResponse.onFailure(code, errors));
        }
    ```

  내 진행중/진행완료 미션을 조회시, Request Body에서 사용자ID를 반드시 받도록 했다.(@NotNull)

![검증2.png](%EA%B2%80%EC%A6%9D2.png)

  리퀘스트 바디를 비워서 요청하면 설계한 에러 메시지가 정상적으로 나오는걸 볼 수 있다.