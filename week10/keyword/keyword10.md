
- 클라우드 컴퓨팅이란?

  : 인터넷을 통해 컴퓨터 자원을 빌려 쓰는 서비스.
  ⇒ 직접 컴퓨터나 서버를 구매·관리하는 대신, AWS·Google Cloud·Microsoft Azure 같은 클라우드 제공업체의 인프라를 이용.

  | **서비스 종류** | **설명** | **쉽게 비유하자면?** | **대표적인 예시** |
      | --- | --- | --- | --- |
  | **IaaS**
    (Infra as a Service) | 데이터 센터의 **하드웨어(서버, 스토리지 등) 공간만** 빌려주는 서비스입니다. 운영체제(OS)나 프로그램은 사용자가 직접 설치해야 합니다. | 건물을 지을 **빈 땅(부지)**만 빌리는 것 | AWS (EC2), Google Cloud (GCE) |
  | **PaaS**
    (Platform as a Service) | 개발자가 서비스를 만들고 실행할 수 있도록 **OS, 데이터베이스, 개발 도구까지 갖춰진 환경**을 빌려줍니다. | 인테리어와 가구가 다 갖춰진 **주방을 빌려 요리만 하는 것** | Heroku, Red Hat OpenShift |
  | **SaaS**
    (Software as a Service) | 인터넷을 통해 **완성된 소프트웨어(앱)를 바로 이용**하는 서비스입니다. 소비자가 가장 흔하게 접하는 형태입니다. | 완성된 음식을 제공하는 **식당에 가서 먹기만 하는 것** | Google 드라이브, Dropbox, Netflix, Notion |

  ### 주요 장점

    - **비용 절감** — 초기 하드웨어 구매 비용 없이 사용한 만큼만 지불
    - **확장성** — 트래픽 증가 시 즉시 자원 확장 가능
    - **접근성** — 인터넷만 있으면 어디서든 접근
    - **안정성** — 데이터 자동 백업 및 재해 복구

  ### 배포 방식

    - **퍼블릭 클라우드** — 여러 기업이 공유하는 클라우드 (AWS, Azure 등)
    - **프라이빗 클라우드** — 한 기업이 단독으로 사용하는 클라우드
    - **하이브리드 클라우드** — 퍼블릭 + 프라이빗 혼합 사용

- AWS? GCP?

  AWS, GCP는 대표적인 클라우드 플랫폼.

  ### **AWS (Amazon Web Services)**

  : 전 세계에서 가장 먼저 클라우드 서비스를 시작.

    - 현재 가장 높은 시장 점유율을 차지하고 있음.
    - 컴퓨팅, 스토리지, 네트워킹뿐만 아니라 블록체인, 위성 통신 등 현존하는 거의 모든 종류의 클라우드 서비스를 제공.
    - 다른 수많은 기업들의 사례가 누적되어 있어, 문제가 생겼을 때 구글링이나 AI를 통해 해결책을 찾기 쉬움.
    - 금융·공공기관 채택률 높음.

  대표 서비스: EC2, S3, Lambda, RDS

  ### **GCP (Google Cloud Platform)**

  : 구글이 만든 클라우드.

    - 데이터 분석 및 인공지능, 머신러닝 분야에서 압도적인 성능.
    - 컨테이너 기반(쿠버네티스)의 현대적인 앱을 운영하기에 최적화.
    - 전 세계를 대상으로 하는 서비스에서 네트워크 속도와 안정성 높음.

  대표 서비스: BigQuery, Cloud Run, Vertex AI

  | 상황 | 추천 |
      | --- | --- |
  | 처음 클라우드 배우기 | **AWS** (자료가 압도적으로 많음) |
  | AI/ML, 데이터 분석 중심 | **GCP** |
  | 스타트업·빠른 개발 | **GCP** (가격 유리) |
  | 대기업·안정성 우선 | **AWS** |

  한국에서는 AWS를 많이 이용.

- 환경변수 처리 방법과 왜 환경변수로 민감 정보를 가려야 하는가?

  환경변수: 코드 외부에서 프로그램에 전달하는 **설정값.**

  ### 왜 민감 정보를 환경변수로 가려야 할까?

    1. **소스코드 유출 및 해킹 방지**

  대부분의 프로젝트는 깃허브(GitHub) 같은 버전 관리 시스템을 통해 팀원들과 공유되거나 오픈소스로 공개.

  ⇒ 만약 소스코드 안에 비밀번호를 그대로 적어두었다면, 코드가 깃허브에 올라가는 순간 전 세계 누구나 내 데이터베이스에 접속 가능.

  ⇒ 깃허브에 AWS 액세스 키를 하드코딩해 올렸다가, 몇 분 만에 해커들에게 탈취당해 수천만 원의 거액의 이용료 폭탄을 맞는 사례도 빈번하게 발생.

    1. **환경에 따른 유연한 대처**

  소프트웨어는 보통 개발용(Local/Dev), 테스트용(Test), 실제 서비스용(Production) 환경으로 나뉘어 운영.

  ⇒ 만약 코드를 하드코딩해두면 환경이 바뀔 때마다 소스코드를 일일이 수정해야 하지만, 환경변수를 쓰면 **소스코드는 그대로 두고, 컴퓨터(서버) 설정의 환경변수만 바꾸면 되기 때문에** 매우 안전하고 편리.

  ### **환경변수 처리 방법**

  `.env` 파일 만들기

  : .env파일에 키-값(Key-Value) 형태로 민감 정보를 저장.(공백이 없어야 함)

    ```jsx
    # .env 파일 예시
    DB_HOST=localhost
    DB_USER=root
    DB_PASSWORD=super_secret_password_999
    API_KEY=xyz123abc456
    ```

  `.env` 는 `.gitignore`에 등록.

  : 깃허브에 업로드 되지 않도록 함.

  +`.env.example`

  : `.env` 파일을 `.gitignore`로 숨겨버리면, 나중에 프로젝트를 다운받은 다른 팀원은 **"어떤 환경변수가 필요한지"** 알 수가 없음.

  ⇒ 값은 비워두고 **이름(Key)만 적어둔 안내장**인 `.env.example` 파일을 만들어서 깃허브에 함께 공유.

    ```jsx
    # .env.example 파일 예시
    DB_HOST=
    DB_USER=
    DB_PASSWORD=
    API_KEY=
    ```

  ### 실수로 커밋했다면?

    1. **즉시 해당 키 폐기 후 재발급** (가장 중요)
    2. `git filter-branch` 또는 `BFG Repo Cleaner`로 히스토리 삭제
    3. 강제 푸시 (`git push --force`)

- yml 환경 분리 방법

  ### YAML 환경 분리란?

  개발(dev), 스테이징(staging), 운영(prod) 환경마다 **다른 설정값**을 적용하는 방법.

  ### **방법 1. 파일 하나로 합쳐서 관리하기**

  : 하나의 `application.yml` 파일 안에 `---` (하이픈 3개) 구문 기호를 사용하여 환경별 설정을 나누는 방식.

    ```yaml
    # 1. 공통 설정 및 기본 프로필 지정
    spring:
      profiles:
        active: dev # 아무 설정이 없을 때 기본으로 켤 환경 (개발 환경)
    ---
    # 2. 개발(dev) 환경 설정
    spring:
      config:
        activate:
          on-profile: dev
    server:
      port: 8080
    db:
      url: jdbc:mysql://localhost:3306/dev_db
    
    ---
    # 3. 운영(prod) 환경 설정
    spring:
      config:
        activate:
          on-profile: prod
    server:
      port: 80
    db:
      url: jdbc:mysql://13.125.xx.xx:3306/prod_db
    ```

  파일 개수가 늘어나지 않아 깔끔하다는 장점.

  ### 방법 2. 파일을 여러 개로 쪼개서 관리하기

  : 환경별로 파일 이름을 `application-{프로필}.yml` 형태로 생성하면, Spring Boot가 상황에 맞는 파일을 알아서 찾아 읽음.

  ⇒ 실무에서 많이 쓰는 방식.

    - `application.yml` (기본 및 공통 설정)
    - `application-dev.yml` (개발용 설정)
    - `application-prod.yml` (운영용 설정)

    ```yaml
    // application.yml
    spring:
      profiles:
        active: dev # 기본적으로 dev 프로필을 활성화
      application:
        name: my-awesome-app
    ```

    ```yaml
    // application-dev.yml
    server:
      port: 8080
    spring:
      datasource:
        url: jdbc:mysql://localhost:3306/dev_db
        username: dev_user
    ```

    ```yaml
    // application-prod.yml
    server:
      port: 80
    spring:
      datasource:
        url: jdbc:mysql://prod-database-address:3306/prod_db
        username: prod_user
    ```

  이후, 서버 실행 시 프로필(Profile) 지정. (컴퓨터에게 어떤 서버로 켤지 알려줌.)

    ```bash
    # 개발 환경으로 실행할 때
    java -jar myapp.jar --spring.profiles.active=dev
    
    # 운영 환경으로 실행할 때
    java -jar myapp.jar --spring.profiles.active=prod
    ```

  **IntelliJ(인텔리제이) IDE에서 실행 시**

    1. 우측 상단의 실행 구성(Run Configuration)에서 **Edit Configurations**를 클릭합니다.
    2. **Active profiles** 칸에 `dev` 또는 `prod`를 입력하고 실행합니다.
       *(구버전의 경우 VM options 칸에 `Dspring.profiles.active=prod` 입력)*

  `application-prod.yml` 파일에는 진짜 운영 서버의 DB 비밀번호나 암호화 키가 들어갈 확률이 높음. ⇒ 환경변수 처리.

- Docker와 .jar vs Docker 이미지


### .jar (Java Archive)

: Java 애플리케이션을 **하나의 파일로 패키징**한 것.

### **도커 이미지 (Docker Image)**

: `.jar` 파일뿐만 아니라, 이 `.jar`를 실행하는 데 필요한 **운영체제(Linux 패키지), Java 실행 환경(JRE/JDK), 환경 설정**까지 모두 한곳에 때려 넣고 압축한 것.

### .jar 직접 배포 vs Docker 이미지 비교

| 항목 | .jar 직접 배포 | Docker 이미지 |
| --- | --- | --- |
| **환경 의존성** | 서버에 Java 설치 필요 | 없음 (컨테이너 안에 다 포함) |
| **환경 일관성** | "내 PC엔 되는데?" 발생 | 어디서나 동일하게 실행 |
| **배포 속도** | 빠름 | 이미지 빌드 시간 필요 |
| **스케일링** | 수동 설정 복잡 | 컨테이너 복제로 간단 |
| **롤백** | 이전 jar 보관 필요 | 이미지 태그로 즉시 롤백 |
| **리소스** | 가벼움 | 이미지 용량 존재 |
| **적합한 상황** | 소규모, 단순 배포 | 팀 개발, 클라우드 배포 |

**.jar 직접 배포 흐름**

코드 작성 → 빌드(.jar) → 서버에 복사 → java -jar 실행

**문제점**

- 서버마다 Java 버전 달라질 수 있음
- 개발자 PC와 서버 환경 차이 발생
- 여러 서버에 배포 시 반복 작업

**Docker 이미지 배포 흐름**

코드 작성 → Dockerfile 작성 → 이미지 빌드 → 레지스트리 push → 서버에서 pull → 실행