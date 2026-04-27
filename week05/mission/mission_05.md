미션 수행한 깃허브 브랜치
https://github.com/central324/10th_Spring_practice/tree/feat/chapter5

## 2주차 API 명세서
<img width="1608" height="766" alt="image" src="https://github.com/user-attachments/assets/5cbfd65f-eb00-4ee4-9bf7-35fb207146a3" />

## Swagger 전체 화면
<img width="1784" height="776" alt="image" src="https://github.com/user-attachments/assets/80e175da-ab51-4e0f-9ae5-9256af8adca0" />

## 1주차 ERD
<img width="1533" height="977" alt="image" src="https://github.com/user-attachments/assets/b5cd6e96-89d4-44f2-91b0-29daf7d3533f" />


## 1. Member

### 구현 내용
Member는 사용자 정보를 관리하는 도메인으로 잡음.
회원에 대한 기본 정보들을 엔티티로 구성하고,
회원 관련 요청이 들어오면 이를 처리할 수 있도록 DTO와 서비스 로직을 작성하였음.

### 요청/응답
Request DTO
- 회원 가입이나 회원 정보 입력 시 필요한 데이터를 받도록 구성
Response DTO
- 회원 조회 또는 회원 생성 후, 필요한 정보만 응답하도록 구성

엔티티를 직접 노출하지 않고
Request → Service → Response 구조로 분리해서
역할을 명확히 나누었음.


## 2. Mission
### 구현 내용
Mission은 사용자가 수행할 수 있는 미션 정보를 관리하는 도메인.
미션의 제목, 내용, 보상 등 필요한 속성들을 기준으로 설계하였고,
미션 생성/조회 등의 흐름을 만들었음.

### 요청/응답
Request DTO
- 미션 생성 시 필요한 값들을 전달받도록 구성
Response DTO
- 미션 조회 결과를 클라이언트가 보기 좋은 형태로 반환

Mission도 Member와 마찬가지로
컨트롤러에서 바로 엔티티를 다루지 않고
DTO를 통해 입력값과 출력값을 분리하였음.


## 3. Review
### 구현 내용
Review는 사용자가 미션이나 특정 대상에 대해 작성하는 리뷰 도메인으로 구현하였음.
리뷰 내용, 별점 또는 관련 식별자 등을 저장할 수 있도록 설계하고,
리뷰 등록 및 조회 흐름을 만들었음.

### 요청/응답
Request DTO
- 리뷰 작성 시 필요한 값들을 받도록 구성
Response DTO
- 작성된 리뷰 정보나 조회 결과를 응답하도록 구성

Review는 다른 도메인과의 연관관계가 있을 수 있기 때문에
필요한 ID 값들을 기반으로 연결되도록 설계하였음.
