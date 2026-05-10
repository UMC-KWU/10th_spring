미션 수행한 깃허브 브랜치
https://github.com/central324/10th_Spring_practice/tree/feat/chapter7

## **1. 내가 진행중인 미션 조회하기**
- 구현 목표
사용자가 현재 진행중인 미션 목록을 조회할 수 있도록 API를 구현했다.
이때 사용자 ID는 하드코딩하지 않고 Request Body를 통해 전달받도록 처리했다.

- 구현 방식
이 API는 오프셋 기반 페이지네이션으로 구현했다.
Spring Data JPA의 Pageable을 활용해 page, size 값을 기준으로 데이터를 조회하도록 구성했다.

- 구현 흐름
Request Body에서 사용자 ID를 전달받는다.
Controller에서 DTO와 Pageable을 함께 받는다.
Service에서 해당 사용자의 진행중 미션만 조회한다.
Repository에서 Page 또는 Slice 형태가 아닌, 오프셋 기반 조건에 맞게 페이지 단위로 데이터를 조회한다.
조회 결과를 응답 DTO로 변환하여 반환한다.

- 구현 포인트
사용자 ID를 직접 코드에 넣지 않고 요청값으로 받도록 처리했다.
진행 상태에 해당하는 미션만 조회하도록 조건을 걸었다.
페이징 정보가 함께 전달될 수 있도록 페이지 기반 응답 구조를 사용했다.

- 첨부 이미지
<img width="1596" height="860" alt="스크린샷 2026-05-09 215708" src="https://github.com/user-attachments/assets/5ee2be26-1d67-4dc8-8277-1a033c494188" />
<img width="1603" height="768" alt="스크린샷 2026-05-09 215757" src="https://github.com/user-attachments/assets/2964a4a4-50ce-47cc-8861-ca308979c57f" />



## **2. 내가 생성한 리뷰 조회하기**

- 구현 목표
사용자가 작성한 리뷰 목록을 조회할 수 있도록 API를 구현했다. 이때 조건에 맞게 커서 기반 페이지네이션을 적용하고,
정렬 기준은 ID 순과 별점 순 두 가지를 모두 구현했다.

- 구현 방식
이 API는 일반적인 page, size 방식이 아니라
커서 기반 페이지네이션으로 구현했다.

즉, 이전에 조회한 마지막 데이터의 기준값을 커서로 사용해서
그 다음 데이터를 조회하는 방식으로 처리했다.

- 구현 흐름
사용자 ID와 커서 값을 요청으로 받는다.
정렬 기준에 따라 조회 방식을 분기한다.
ID 순 조회
별점 순 조회
마지막으로 조회된 데이터 이후의 리뷰만 가져오도록 조건을 설정한다.
정해진 개수만큼 조회한 뒤 응답 DTO로 반환한다.


- 구현 포인트
커서 기반 페이지네이션을 적용해 불필요한 오프셋 이동 비용을 줄이도록 했다.
정렬 기준을 두 가지로 나누어 각각 조회할 수 있게 구현했다.
커서 기반 조회이므로 “다음 데이터가 있는지” 중심으로 응답을 설계했다.
- 정렬 기준별 구현 내용
1) ID 순 조회
리뷰 ID를 기준으로 오름차순 또는 조건에 맞는 순서로 조회하도록 구현했다.
마지막 리뷰 ID를 커서처럼 사용해서 그 다음 리뷰부터 조회할 수 있도록 처리했다.

2) 별점 순 조회
별점을 기준으로 정렬하도록 구현했다.
동일한 별점이 있을 수 있기 때문에 정렬 안정성을 고려해서
필요한 경우 보조 정렬 기준도 함께 설정하는 방식으로 구현했다.

- 첨부 이미지
<img width="1579" height="803" alt="스크린샷 2026-05-09 215958" src="https://github.com/user-attachments/assets/9b174b59-6972-4794-8c73-ccedc4c7790a" />
<img width="1594" height="833" alt="스크린샷 2026-05-09 220025" src="https://github.com/user-attachments/assets/c5e30198-2f0d-4f19-bb50-fe15862047c3" />

## **3. Request Body 검증 적용하기**

- 구현 목표
Request Body가 있는 API에 대해 검증 어노테이션을 적용하고,
잘못된 요청이 들어왔을 때 예외를 처리할 수 있도록 구현했다.

- 구현 방식
DTO에 검증 어노테이션을 붙이고,
Controller에서는 @Valid를 사용해 요청값이 들어올 때 자동 검증되도록 설정했다.

또한 검증 실패 시 GeneralExceptionAdvice에서
MethodArgumentNotValidException을 처리하도록 구현했다.

- 적용한 방식
DTO 필드에 @NotNull, @NotBlank, @Min 등의 어노테이션 적용
Controller 파라미터에 @Valid 적용
GeneralExceptionAdvice에서 검증 예외 처리

- 구현 포인트
잘못된 요청이 들어와도 서버 에러가 아니라 의도된 예외 응답이 나가도록 처리했다.
검증 실패 메시지를 클라이언트가 확인할 수 있도록 구성했다.
특정 API 하나만이 아니라, Request Body를 사용하는 구조 전반에 적용 가능한 방식으로 구현했다.

- 예외 처리 방식
검증 오류 발생 시 GeneralExceptionAdvice에서
MethodArgumentNotValidException을 받아 응답 형식에 맞게 변환했다.

기존에는 첫 번째 에러만 반환하는 방식으로 작성할 수 있었지만,
필드별 검증 결과를 함께 전달할 수 있도록 수정하여
어떤 값이 잘못되었는지 한 번에 확인할 수 있도록 처리했다.

- 첨부 이미지
<img width="1603" height="742" alt="스크린샷 2026-05-09 220124" src="https://github.com/user-attachments/assets/c8e8156b-3347-4b7d-b896-d045d8768022" />

