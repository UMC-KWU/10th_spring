- 클라우드 컴퓨팅이란?
    
    **정의**
    
    클라우드 컴퓨팅(Cloud Computing)은 인터넷을 통해 서버, 스토리지, 데이터베이스, 네트워크, 소프트웨어 등의 IT 자원을 필요할 때 제공받아 사용하는 방식이다.
    
    기존에는 직접 서버를 구매하고 구축해야 했지만, 클라우드에서는 필요한 만큼만 사용하고 사용량에 따라 비용을 지불한다.
    
    **특징**
    
    - 온디맨드(On-Demand): 필요한 순간에 바로 서버 생성 가능
    - 탄력성(Elasticity): 트래픽 증가 시 자동으로 서버 확장 가능
    - 종량제(Pay-As-You-Go): 사용한 만큼만 비용 지불
    - 어디서든 접근 가능: 인터넷만 있으면 사용 가능
    
    **장점**
    
    - 초기 서버 구매 비용 절감
    - 빠른 서비스 구축 가능
    - 자동 백업 및 관리 지원
    - 확장성이 뛰어남
    
    **단점**
    
    - 인터넷 연결 필수
    - 장기적으로 비용 증가 가능
    - 클라우드 제공 업체 의존성 발생
    
    **한 줄 요약**
    
    인터넷을 통해 IT 자원을 빌려 쓰는 서비스
    
- AWS? GCP?
    
    ### **AWS**
    
    아마존이 제공하는 세계 최대 규모의 클라우드 플랫폼
    
    **대표 서비스**
    
    - EC2 (가상 서버)
    - S3 (파일 저장소)
    - RDS (데이터베이스)
    - Lambda (서버리스)
    
    **장점**
    
    - 시장 점유율 1위
    - 서비스 종류 매우 많음
    - 기업 사용 사례 풍부
    
    **단점**
    
    - 서비스가 많아 학습 난이도 높음
    - 비용 구조 복잡
    
    ### GCP
    
    구글이 제공하는 클라우드 플랫폼
    
    **대표 서비스**
    
    - Compute Engine
    - Cloud Storage
    - Cloud SQL
    - BigQuery
    
    **장점**
    
    - 데이터 분석 및 AI 강점
    - UI가 직관적
    - Kubernetes 지원 우수
    
    **단점**
    
    - AWS보다 사용 사례 적음
    - 서비스 종류 상대적으로 적음
    
    ### AWS vs GCP 비교
    
    | 항목 | AWS | GCP |
    | --- | --- | --- |
    | 시장 점유율 | 1위 | 3위 |
    | 서비스 수 | 매우 많음 | 비교적 적음 |
    | AI/데이터 분석 | 강함 | 매우 강함 |
    | 학습 난이도 | 높음 | 비교적 쉬움 |
    | 기업 사용 | 매우 많음 | 많음 |
- 환경변수 처리 방법과 왜 환경변수로 민감 정보를 가려야 하는가?
    
    ### 환경변수란?
    
    프로그램 외부에서 설정하는 값이다.
    
    예시
    
    ```
    DB_URL
    DB_USER
    DB_PASSWORD
    JWT_SECRET
    GOOGLE_CLIENT_SECRET
    ```
    
    ### Spring Boot 예시
    
    application.yml
    
    ```
    spring:
      datasource:
        url: ${DB_URL}
        username: ${DB_USER}
        password: ${DB_PW}
    ```
    
    실제 값
    
    ```
    DB_URL=jdbc:mysql://localhost:3306/umc10th1
    DB_USER=root
    DB_PW=1234
    ```
    
    ---
    
    ### 왜 사용해야 하는가?
    
    - **보안:** 비밀번호를 GitHub에 올리는 실수 방지
    - **환경 분리:** 개발/테스트/운영 환경마다 다른 값 사용 가능
    - **유지보수:** 코드 수정 없이 설정 변경 가능
    
    ### 잘못된 예
    
    ```
    Stringpassword="1234";
    ```
    
    ### 올바른 예
    
    ```
    Stringpassword=System.getenv("DB_PW");
    ```
    
    민감정보를 코드에 직접 작성하지 않고 외부에서 주입하는 방식
    
- yml 환경 분리 방법
    
    ### yml 환경 분리란?
    
    Spring Boot 프로젝트를 개발하다 보면 개발 환경(Local), 테스트 환경(Test), 운영 환경(Prod)에서 서로 다른 설정값을 사용해야 한다.
    
    예를 들어 개발 환경에서는 로컬 DB를 사용하지만 운영 환경에서는 실제 서비스용 DB를 사용한다. 만약 하나의 application.yml만 사용한다면 환경이 바뀔 때마다 직접 수정해야 하므로 실수가 발생할 수 있다.
    
    이를 해결하기 위해 Spring Boot에서는 Profile 기능을 제공하며, 환경별로 yml 파일을 분리하여 관리할 수 있다.
    
    ### 사용 방법
    
    기본 설정 파일
    
    **application.yml**
    
    ```
    spring:
      profiles:
        active: local
    ```
    
    현재 사용할 환경을 지정한다.
    
    ---
    
    개발 환경 설정
    
    **application-local.yml**
    
    ```
    spring:
      datasource:
        url: jdbc:mysql://localhost:3306/local_db
    ```
    
    ---
    
    운영 환경 설정
    
    **application-prod.yml**
    
    ```
    spring:
      datasource:
        url: jdbc:mysql://prod-db:3306/prod_db
    ```
    
    ---
    
    운영 환경으로 실행
    
    ```
    java-jar app.jar--spring.profiles.active=prod
    ```
    
    그러면 Spring Boot는 자동으로 application-prod.yml을 읽어 실행한다.
    
    ### 장점
    
    - **환경별 설정 분리:** 개발용 DB와 운영용 DB를 구분할 수 있다.
    - **보안 강화:** 운영 환경의 민감한 정보가 개발 환경에 노출되지 않는다.
    - **유지보수 편리:** 환경이 변경되어도 코드를 수정하지 않아도 된다.
    
    ### 정리
    
    yml 환경 분리는 Spring Profile을 이용하여 환경별 설정 파일을 분리하는 방법이다. 이를 통해 개발, 테스트, 운영 환경을 독립적으로 관리할 수 있으며 보안성과 유지보수성을 높일 수 있다.
    
- Docker와 .jar vs Docker 이미지
    
    ## .jar 파일이란?
    
    jar(Java Archive) 파일은 Java 프로그램을 실행할 수 있도록 압축한 실행 파일이다.
    
    Spring Boot 프로젝트를 빌드하면 다음과 같은 jar 파일이 생성된다.
    
    ```
    build/libs/app.jar
    ```
    
    실행 방법
    
    ```
    java-jar app.jar
    ```
    
    ---
    
    ### jar 파일의 문제점
    
    jar 파일만 있다고 해서 어디서든 실행되는 것은 아니다.
    
    실행하려면 해당 서버에
    
    - Java 설치
    - JDK 버전 일치
    - 환경 변수 설정
    - 라이브러리 의존성
    
    등이 모두 준비되어 있어야 한다.
    
    예를 들어 개발 PC에서는 잘 동작하지만 다른 서버에서는 Java 버전 차이로 실행되지 않을 수 있다.
    
    ---
    
    ## Docker 이미지란?
    
    Docker 이미지는 애플리케이션뿐만 아니라 실행에 필요한 환경까지 함께 포함한 패키지이다.
    
    즉,
    
    ```
    Spring Boot App
    +
    JDK
    +
    라이브러리
    +
    설정 파일
    ```
    
    을 하나로 묶어 놓은 것이다.
    
    ---
    
    ### Docker 이미지 생성 과정
    
    ```
    소스코드
       ↓
    Gradle Build
       ↓
    app.jar 생성
       ↓
    Dockerfile 작성
       ↓
    Docker Build
       ↓
    Docker Image 생성
    ```
    
    ---
    
    ### 실행 과정
    
    ```
    docker run my-app
    ```
    
    Docker가 이미지를 기반으로 컨테이너를 생성하여 실행한다.
    
    ---
    
    ## jar와 Docker 이미지 비교
    
    | 항목 | jar 파일 | Docker 이미지 |
    | --- | --- | --- |
    | 애플리케이션 포함 | O | O |
    | 실행 환경 포함 | X | O |
    | Java 설치 필요 | O | X |
    | 환경 차이 발생 가능 | O | X |
    | 배포 편의성 | 보통 | 매우 높음 |
    | 재현성 | 낮음 | 높음 |
    
    ---
    
    ## Docker를 사용하는 이유
    
    기존 jar 방식은 서버마다 환경이 다르기 때문에 실행 오류가 자주 발생한다.
    
    예를 들어
    
    ```
    개발자 PC → 정상 실행
    서버 → Java 버전 불일치 오류
    ```
    
    가 발생할 수 있다.
    
    반면 Docker는 애플리케이션과 실행 환경을 함께 제공하기 때문에
    
    ```
    개발자 PC → 정상 실행
    테스트 서버 → 정상 실행
    운영 서버 → 정상 실행
    ```
    
    과 같이 동일한 결과를 보장할 수 있다.
    
    ---
    
    ## 정리
    
    jar 파일은 Java 애플리케이션만 포함하는 실행 파일이며, Docker 이미지는 애플리케이션과 실행 환경을 함께 포함하는 패키지이다. 따라서 Docker를 사용하면 서버 환경에 관계없이 동일하게 실행할 수 있어 배포와 운영이 훨씬 편리해진다.