- Docker compose

  # Docker Compose란?

  Docker Compose는 **여러 개의 컨테이너를 한 번에 실행하고 관리하는 도구**다.

  예를 들어 백엔드 서버만 실행하는 게 아니라,

    ```
    Spring Boot 서버
    MySQL
    Redis
    Nginx
    ```

  이런 것들을 한 파일에 정의하고 한 번에 실행할 수 있다.
    
  ---

  # Docker만 쓸 때

    ```
    docker run mysql
    docker run redis
    docker run spring-app
    ```

  컨테이너마다 명령어를 따로 쳐야 한다.
    
  ---

  # Docker Compose를 쓸 때

    ```
    docker compose up
    ```

  이 명령어 하나로 여러 컨테이너를 동시에 실행할 수 있다.
    
  ---

  # docker-compose.yml 예시

    ```yaml
    services:
      app:
        image: my-spring-app
        ports:
          -"8080:8080"
        environment:
          SPRING_PROFILES_ACTIVE: local
          DB_HOST: db
          DB_USER: root
          DB_PASSWORD: 1234
        depends_on:
          - db
    
      db:
        image: mysql:8.0
        ports:
          -"3306:3306"
        environment:
          MYSQL_ROOT_PASSWORD: 1234
          MYSQL_DATABASE: mydb
    
      redis:
        image: redis:7
        ports:
          -"6379:6379"
    ```

  구조는 이렇게 된다.

    ```
    docker compose
     ├ app 컨테이너
     ├ db 컨테이너
     └ redis 컨테이너
    ```
    
  ---

  # Docker Compose를 쓰는 이유

  ## 1. 여러 컨테이너를 한 번에 실행

  백엔드, DB, Redis를 각각 실행하지 않아도 된다.

    ```
    docker compose up-d
    ```
    
  ---

  ## 2. 실행 환경 공유가 쉬움

  팀원이 프로젝트를 받을 때

    ```
    docker compose up
    ```

  만 하면 같은 개발 환경을 띄울 수 있다.
    
  ---

  ## 3. 컨테이너 간 통신이 쉬움

  Compose 안에서는 서비스 이름으로 접근할 수 있다.

    ```
    DB_HOST: db
    ```

  여기서 `db`는 MySQL 컨테이너 이름이다.

  Spring Boot에서 DB 주소를 이렇게 쓸 수 있다.

    ```yaml
    spring:
      datasource:
        url: jdbc:mysql://db:3306/mydb
    ```
    
  ---

  ## 4. 환경변수 관리 가능

    ```yaml
    environment:
      DB_PASSWORD: ${DB_PASSWORD}
    ```

  또는 `.env` 파일을 연결할 수 있다.

    ```yaml
    env_file:
      - .env
    ```

- 컨테이너 vs VM

  # 컨테이너(Container) vs VM(Virtual Machine)

  둘 다 **하나의 물리 서버에서 여러 개의 독립적인 실행 환경을 만들기 위한 기술**이다.

  차이는

    - VM은 **하드웨어를 가상화**
    - 컨테이너는 **운영체제를 공유**

  한다는 점이다.
    
  ---

  # VM (Virtual Machine)

  VM은 물리 서버 위에 가상의 컴퓨터를 만드는 기술이다.

  구조:

    ```
    물리 서버
    └ Hypervisor
        ├ VM1
        │  ├ Guest OS
        │  └ App
        │
        ├ VM2
        │  ├ Guest OS
        │  └ App
        │
        └ VM3
           ├ Guest OS
           └ App
    ```

  각 VM은 독립적인 운영체제를 가진다.

  예시:

    ```
    VM1 → Ubuntu
    VM2 → CentOS
    VM3 → Windows
    ```

  한 서버에서 서로 다른 OS를 동시에 실행할 수 있다.
    
  ---

  # 컨테이너(Container)

  컨테이너는 OS 커널을 공유한다.

  구조:

    ```
    물리 서버
    └ Host OS
        └ Docker Engine
             ├ Container1
             │   └ App
             │
             ├ Container2
             │   └ App
             │
             └ Container3
                 └ App
    ```

  모든 컨테이너가 Host OS의 커널을 함께 사용한다.

  즉 Guest OS가 없다.
    
  ---

  # 가장 중요한 차이

  ## VM

    ```
    VM1
     └ Ubuntu
    
    VM2
     └ CentOS
    
    VM3
     └ Windows
    ```

  각각 OS를 따로 가지고 있음
    
  ---

  ## 컨테이너

    ```
    Container1
    Container2
    Container3
    ```

  OS를 따로 갖지 않고 Host OS 공유
    
  ---

  # 자원 사용 비교

  ## VM

    ```
    Ubuntu 2GB
    CentOS 2GB
    Windows 4GB
    ```

  OS 자체가 메모리를 사용한다.
    
  ---

  ## 컨테이너

    ```
    App1
    App2
    App3
    ```

  OS를 공유하므로 훨씬 가볍다.
    
  ---

  # 실행 속도

  ## VM

  OS 부팅 필요

    ```
    Boot
    ↓
    OS 시작
    ↓
    App 실행
    ```

  수십 초 ~ 수 분
    
  ---

  ## 컨테이너

  OS 부팅 없음

    ```
    Container Start
    ↓
    App 실행
    ```

  수 초 이내
    
  ---

  # 배포 관점

  ## VM

    ```
    Ubuntu 설치
    Java 설치
    MySQL 설치
    Spring 설치
    ```

  환경 구축 필요
    
  ---

  ## 컨테이너

    ```
    Docker Image
     ↓
    docker run
    ```

  이미지 그대로 실행
    
  ---

  # AWS에서의 예시

  ## EC2

  EC2는 기본적으로 VM이다.

    ```
    AWS
     └ EC2
          └ Ubuntu
               └ Spring Boot
    ```

  실제로는 AWS의 Hypervisor 위에서 동작하는 가상 머신이다.
    
  ---

  ## ECS

    ```
    AWS
     └ ECS
          ├ Container
          ├ Container
          └ Container
    ```

  컨테이너 기반 서비스
    
  ---

  ## EKS

    ```
    AWS
     └ Kubernetes
          └ Container
    ```

  쿠버네티스 기반 컨테이너 관리 서비스
    
  ---

  # 보안 측면

  ## VM

  운영체제까지 완전히 분리

    ```
    VM1 장애
    ↓
    VM2 영향 적음
    ```

  격리 수준이 높음
    
  ---

  ## 컨테이너

  커널 공유

    ```
    Container1
    Container2
    ```

  같은 OS를 사용

  그래도 현대 컨테이너 기술은 충분히 강한 격리를 제공한다.
    
  ---

  # 왜 Docker가 인기인가?

  예전

    ```
    개발자 PC
     → 잘 됨
    
    운영 서버
     → 안 됨
    ```

  환경 차이 발생
    
  ---

  Docker

    ```
    개발 PC
     ↓
    Docker Image
     ↓
    운영 서버
    ```

  동일한 환경 제공

- Sticky Session vs Session Clustering

  # Sticky Session이란?

  **같은 사용자의 요청을 항상 같은 서버로 보내는 방식**이다.

    ```
    사용자 A → 서버 1
    사용자 A → 서버 1
    사용자 A → 서버 1
    
    사용자 B → 서버 2
    사용자 B → 서버 2
    ```

  즉, 로그인 세션이 서버 1 메모리에 있으면 이후 요청도 계속 서버 1로 보내는 방식이다.
    
  ---

  # 왜 필요함?

  서버가 여러 대일 때 세션을 서버 메모리에 저장하면 문제가 생긴다.

    ```
    1번째 요청: 서버 1에서 로그인 성공
    세션 저장: 서버 1 메모리
    
    2번째 요청: 서버 2로 감
    서버 2에는 세션 없음
    → 로그인 안 된 사용자로 판단
    ```

  그래서 로드밸런서가 같은 사용자를 계속 같은 서버로 보내게 한다.
    
  ---

  # Sticky Session 구조

    ```
    사용자
     ↓
    Load Balancer
     ↓
    항상 같은 서버로 전달
    ```

  예시:

    ```
    사용자 A → LB → WAS 1
    사용자 B → LB → WAS 2
    사용자 C → LB → WAS 1
    ```
    
  ---

  # Sticky Session 장점

  ## 1. 구현이 쉬움

  서버 세션 구조를 크게 바꾸지 않아도 된다.

  ## 2. 기존 세션 방식과 잘 맞음

  Spring Session, Redis 같은 외부 저장소 없이도 사용 가능하다.

  ## 3. 성능 부담이 적음

  세션을 매번 외부 저장소에서 조회하지 않아도 된다.
    
  ---

  # Sticky Session 단점

  ## 1. 특정 서버에 부하가 몰릴 수 있음

  사용자 A가 서버 1에 고정되면 계속 서버 1만 사용한다.

    ```
    서버 1: 사용자 많음
    서버 2: 사용자 적음
    ```

  로드밸런싱 효과가 약해질 수 있다.

  ## 2. 서버 장애에 약함

  사용자 A의 세션이 서버 1에 있는데 서버 1이 죽으면 세션도 사라진다.

    ```
    WAS 1 장애
    → 사용자 A 세션 유실
    → 다시 로그인 필요
    ```

  ## 3. 오토스케일링에 불리함

  서버가 늘거나 줄 때 기존 세션 분배가 꼬일 수 있다.
    
  ---

  # Session Clustering이란?

  **여러 서버가 세션 정보를 공유하는 방식**이다.

  즉, 어느 서버로 요청이 가도 같은 세션을 사용할 수 있게 한다.

    ```
    WAS 1
    WAS 2
    WAS 3
     ↓
    공유 세션 저장소
    ```
    
  ---

  # Session Clustering 구조

    ```
    사용자
     ↓
    Load Balancer
     ↓
    WAS 1 / WAS 2 / WAS 3
     ↓
    공유 세션 저장소
    ```

  공유 방식은 여러 가지가 있다.

    ```
    서버 간 세션 복제
    Redis 같은 외부 세션 저장소 사용
    DB 세션 저장
    ```
    
  ---

  # Session Clustering 예시

  사용자 A가 로그인한다.

    ```
    요청 1 → WAS 1
    WAS 1에 세션 생성
    세션 정보가 공유됨
    ```

  다음 요청이 WAS 2로 가도:

    ```
    요청 2 → WAS 2
    WAS 2가 공유 세션에서 세션 조회
    로그인 유지
    ```
    
  ---

  # Session Clustering 장점

  ## 1. 서버 장애에 강함

  WAS 1이 죽어도 세션 정보가 공유 저장소에 있으면 WAS 2가 이어받을 수 있다.

    ```
    WAS 1 장애
    ↓
    WAS 2가 세션 조회
    ↓
    로그인 유지
    ```

  ## 2. 로드밸런싱이 자유로움

  사용자를 특정 서버에 고정하지 않아도 된다.

    ```
    요청 1 → WAS 1
    요청 2 → WAS 2
    요청 3 → WAS 3
    ```

  ## 3. 오토스케일링에 유리함

  서버가 늘어나도 세션 저장소만 공유하면 된다.
    
  ---

  # Session Clustering 단점

  ## 1. 구현이 더 복잡함

  세션 저장소나 복제 설정이 필요하다.

  ## 2. 외부 저장소 비용/부하 발생

  Redis나 DB에 세션을 저장하면 네트워크 호출이 추가된다.

  ## 3. 세션 동기화 문제 가능

  서버 간 세션 복제 방식은 서버 수가 많아질수록 복잡해진다.
    
  ---

  # Sticky Session vs Session Clustering

  | 구분 | Sticky Session | Session Clustering |
      | --- | --- | --- |
  | 핵심 | 같은 사용자를 같은 서버로 보냄 | 여러 서버가 세션을 공유 |
  | 세션 위치 | 각 서버 메모리 | 공유 저장소 또는 서버 간 복제 |
  | 로드밸런싱 | 제한적 | 자유로움 |
  | 장애 대응 | 약함 | 강함 |
  | 구현 난이도 | 쉬움 | 상대적으로 어려움 |
  | 확장성 | 낮음 | 높음 |
  | 서버 죽었을 때 | 세션 유실 가능 | 세션 유지 가능 |
  | 대표 방식 | LB의 세션 고정 | Redis, DB, 세션 복제 |
    
  ---

  # AWS에서 보면

  ## Sticky Session

  AWS ALB에서 Sticky Session을 설정할 수 있다.

    ```
    사용자
     ↓
    ALB
     ↓
    항상 같은 EC2/ECS Task로 전달
    ```

  간단하지만 특정 서버가 죽으면 세션이 사라질 수 있다.
    
  ---

  ## Session Clustering

  Redis를 공유 세션 저장소로 둔다.

  AWS에서는 보통 ElastiCache Redis를 쓴다.

    ```
    사용자
     ↓
    ALB
     ↓
    EC2 1 / EC2 2 / ECS Task
     ↓
    ElastiCache Redis
    ```

  Spring Boot에서는 Spring Session Redis를 사용해서 세션을 Redis에 저장할 수 있다.
    
  ---

  # Spring Boot 기준

  ## Sticky Session

    ```
    세션 저장 위치: WAS 메모리
    로드밸런서: 같은 사용자 요청을 같은 WAS로 보냄
    ```

  간단하지만 서버 장애에 약하다.

  ## Session Clustering

    ```
    세션 저장 위치: Redis
    어느 WAS로 요청이 가도 Redis에서 세션 조회
    ```

  운영 환경에서는 이 방식이 더 안정적이다.
    
  ---

  # JWT와 비교하면?

  JWT 방식은 서버에 세션을 저장하지 않는 stateless 방식이다.

    ```
    사용자 요청
     ↓
    JWT 포함
     ↓
    서버가 토큰 검증
    ```

  서버 메모리에 세션을 저장하지 않으므로 Sticky Session이나 Session Clustering 필요성이 줄어든다.
    
  ---

  # 한 줄 정리

  **Sticky Session은 같은 사용자를 같은 서버에 고정하는 방식**이고, **Session Clustering은 여러 서버가 세션 정보를 공유해서 어느 서버로 가도 로그인 상태를 유지하는 방식**이다.

- blue-green 배포

  # Blue-Green 배포란?

  **현재 운영 중인 서버 환경(Blue)** 과 **새 버전 서버 환경(Green)** 을 따로 두고, 트래픽을 한 번에 새 버전으로 전환하는 배포 방식이다.

    ```
    사용자
     ↓
    로드밸런서 / Nginx
     ↓
    Blue 서버: 현재 운영 버전
    Green 서버: 새 배포 버전
    ```
    
  ---

  # 기본 흐름

  ## 1. Blue 환경 운영 중

    ```
    사용자 → Blue
    ```

  현재 서비스는 Blue에서 돌아가고 있다.
    
  ---

  ## 2. Green 환경에 새 버전 배포

    ```
    Blue: 기존 버전 운영 중
    Green: 새 버전 배포 및 테스트
    ```

  사용자 트래픽은 아직 Blue로 간다.
    
  ---

  ## 3. Green 정상 확인

  Green에서 다음을 확인한다.

    ```
    앱 실행 여부
    DB 연결
    API 정상 응답
    로그 오류
    헬스 체크
    ```
    
  ---

  ## 4. 트래픽 전환

  로드밸런서나 Nginx 설정을 바꿔서 사용자 요청을 Green으로 보낸다.

    ```
    사용자 → Green
    ```
    
  ---

  ## 5. 문제 발생 시 롤백

  Green에 문제가 있으면 다시 Blue로 트래픽을 돌린다.

    ```
    사용자 → Blue
    ```

  기존 Blue 서버를 그대로 남겨뒀기 때문에 롤백이 빠르다.
    
  ---

  # 장점

  ## 1. 무중단 배포 가능

  새 버전을 미리 띄워두고 트래픽만 전환하므로 서비스 중단이 적다.

  ## 2. 롤백이 빠름

  문제가 생기면 기존 Blue로 다시 돌리면 된다.

  ## 3. 배포 전 검증 가능

  Green 환경에서 실제 운영과 비슷하게 테스트한 뒤 트래픽을 받을 수 있다.
    
  ---

  # 단점

  ## 1. 서버 자원이 2배 필요

  Blue와 Green을 동시에 띄워야 한다.

    ```
    Blue 서버
    Green 서버
    ```

  그래서 비용이 더 든다.

  ## 2. DB 변경에 주의 필요

  애플리케이션은 Blue/Green으로 나눌 수 있지만 DB는 보통 하나를 공유한다.

    ```
    Blue app
    Green app
       ↓
    공통 DB
    ```

  그래서 DB 스키마를 바꿀 때는 기존 버전과 새 버전이 모두 동작하도록 조심해야 한다.
    
  ---

  # Docker 기준 예시

    ```
    blue 컨테이너: app:v1
    green 컨테이너: app:v2
    nginx가 둘 중 하나로 요청 전달
    ```

  처음에는:

    ```
    Nginx → blue:8080
    ```

  새 버전 배포 후:

    ```
    Nginx → green:8080
    ```

  문제 생기면:

    ```
    Nginx → blue:8080
    ```
    
  ---

  # AWS 기준 예시

  ## EC2 + Nginx

    ```
    EC2
    ├ app-blue 컨테이너
    ├ app-green 컨테이너
    └ Nginx
    ```

  Nginx 설정으로 Blue/Green을 전환한다.
    
  ---

  ## ALB 사용

    ```
    사용자
     ↓
    ALB
     ├ Target Group Blue
     └ Target Group Green
    ```

  ALB의 Target Group을 바꿔서 트래픽을 전환할 수 있다.
    
  ---

  ## ECS 사용

  ECS에서는 Blue-Green 배포를 CodeDeploy와 연결해서 사용할 수 있다.

    ```
    ECS 서비스
     ↓
    CodeDeploy
     ↓
    Blue Task Set / Green Task Set
    ```

  새 Task Set을 띄우고 헬스 체크 후 트래픽을 전환한다.
    
  ---

  # Rolling 배포와 차이

  ## Rolling 배포

  서버를 하나씩 새 버전으로 교체한다.

    ```
    서버1 v1 → v2
    서버2 v1 → v2
    서버3 v1 → v2
    ```

  장점은 자원이 덜 들지만, 배포 중 v1과 v2가 섞일 수 있다.
    
  ---

  ## Blue-Green 배포

  새 환경을 통째로 띄우고 한 번에 전환한다.

    ```
    Blue v1 → Green v2
    ```

  롤백이 빠르지만 자원이 더 필요하다.
    
  ---

  # 한 줄 정리

  Blue-Green 배포는 **기존 운영 환경(Blue)을 유지한 채 새 버전 환경(Green)을 따로 띄우고, 검증 후 트래픽을 Green으로 전환하는 무중단 배포 방식**이다.

- Gzip 압축 및 캐싱

  # Gzip 압축이란?

  Gzip 압축은 **서버가 응답 데이터를 압축해서 보내는 방식**이다.

    ```
    서버 → HTML/CSS/JS/JSON 압축 → 클라이언트
    ```

  브라우저는 압축된 데이터를 받아서 다시 풀어 사용한다.
    
  ---

  # 왜 쓰는가?

  응답 크기를 줄이기 위해서다.

  예를 들어 JSON 응답이 100KB라면 Gzip 압축 후 20~30KB 정도로 줄어들 수 있다.

    ```
    응답 크기 감소
    → 네트워크 전송량 감소
    → 로딩 속도 개선
    → 서버 트래픽 비용 감소
    ```
    
  ---

  # Gzip 동작 흐름

    ```
    1. 브라우저가 요청
       Accept-Encoding: gzip
    
    2. 서버가 압축 가능 여부 확인
    
    3. 서버가 응답 압축
       Content-Encoding: gzip
    
    4. 브라우저가 압축 해제 후 사용
    ```
    
  ---

  # 주로 압축하는 대상

  Gzip은 텍스트 기반 파일에 효과적이다.

    ```
    HTML
    CSS
    JavaScript
    JSON
    XML
    텍스트 파일
    ```

  이미 압축된 파일에는 효과가 거의 없다.

    ```
    jpg
    png
    mp4
    zip
    pdf
    ```
    
  ---

  # Nginx Gzip 예시

    ```
    gzip on;
    gzip_comp_level 5;
    gzip_min_length 1024;
    gzip_types
      text/plain
      text/css
      application/json
      application/javascript
      application/xml;
    ```

  의미:

    ```
    gzip on → Gzip 압축 활성화
    gzip_comp_level 5 → 압축 레벨
    gzip_min_length 1024 → 1KB 이상만 압축
    gzip_types → 어떤 타입을 압축할지 지정
    ```
    
  ---

  # 캐싱이란?

  캐싱은 **자주 사용하는 데이터를 임시 저장해두고 재사용하는 것**이다.

  매번 서버에서 새로 가져오지 않고, 브라우저나 프록시 서버가 저장해둔 데이터를 사용한다.

    ```
    첫 요청: 서버에서 받아옴
    두 번째 요청: 캐시에서 사용
    ```
    
  ---

  # 캐싱을 쓰는 이유

    ```
    응답 속도 향상
    서버 부하 감소
    네트워크 비용 감소
    반복 요청 감소
    ```
    
  ---

  # 캐싱 종류

  ## 1. 브라우저 캐시

  사용자 브라우저에 파일을 저장한다.

  예:

    ```
    logo.png
    style.css
    app.js
    ```

  한 번 받은 파일을 다시 다운로드하지 않게 한다.
    
  ---

  ## 2. 프록시 / CDN 캐시

  CloudFront 같은 CDN이 서버 대신 응답을 저장한다.

    ```
    사용자
     ↓
    CloudFront 캐시
     ↓
    원본 서버
    ```

  캐시에 있으면 원본 서버까지 가지 않는다.
    
  ---

  ## 3. 서버 내부 캐시

  백엔드 서버가 자주 조회하는 데이터를 Redis 등에 저장한다.

    ```
    DB 조회 결과
    인기 게시글
    토큰 정보
    세션 정보
    ```
    
  ---

  # HTTP 캐시 헤더

  ## Cache-Control

  가장 많이 쓰는 캐시 제어 헤더.

    ```
    Cache-Control: max-age=3600
    ```

  의미:

    ```
    3600초 동안 캐시 사용 가능
    ```
    
  ---

  ## no-cache

    ```
    Cache-Control: no-cache
    ```

  캐시를 저장할 수는 있지만, 사용하기 전에 서버에 변경 여부를 확인한다.
    
  ---

  ## no-store

    ```
    Cache-Control: no-store
    ```

  아예 캐시하지 않는다.

  로그인 정보, 결제 정보 같은 민감 데이터에 사용한다.
    
  ---

  ## ETag

  파일이나 응답의 버전값 같은 역할.

    ```
    ETag: "abc123"
    ```

  브라우저가 다음 요청 때 물어본다.

    ```
    If-None-Match: "abc123"
    ```

  서버가 변경 없다고 판단하면:

    ```
    304 Not Modified
    ```

  본문을 다시 보내지 않는다.
    
  ---

  # 정적 파일 캐싱 예시

    ```
    location /static/ {
        expires 30d;
        add_header Cache-Control "public";
    }
    ```

  의미:

    ```
    /static/ 경로 파일은 30일 동안 캐싱
    ```
    
  ---

  # API 응답 캐싱 주의

  정적 파일은 캐싱해도 안전한 경우가 많다.

  하지만 API 응답은 조심해야 한다.

  예를 들어:

    ```
    내 정보 조회 API
    주문 내역 API
    결제 정보 API
    ```

  이런 건 캐싱하면 다른 사용자 정보 노출 위험이 있다.

  그래서 보통:

    ```
    Cache-Control: no-store
    ```

  를 사용한다.
    
  ---

  # Gzip vs 캐싱 차이

  | 구분 | Gzip 압축 | 캐싱 |
      | --- | --- | --- |
  | 목적 | 응답 크기 줄이기 | 같은 응답 재사용 |
  | 효과 | 전송량 감소 | 요청 수 감소 |
  | 동작 위치 | 서버 → 클라이언트 전송 시 | 브라우저/CDN/서버 |
  | 대상 | HTML, CSS, JS, JSON | 정적 파일, 반복 조회 데이터 |
  | 결과 | 더 작게 보냄 | 다시 안 받아도 됨 |
    
  ---

  # AWS와 연결

  ## EC2 + Nginx

  Nginx에서 Gzip과 캐싱 설정 가능.

    ```
    사용자
     ↓
    Nginx
     ↓
    Spring Boot
    ```

  Nginx가 압축과 정적 파일 캐싱을 처리할 수 있다.
    
  ---

  ## CloudFront

  AWS CloudFront는 CDN 서비스다.

    ```
    사용자
     ↓
    CloudFront
     ↓
    EC2 / S3
    ```

  CloudFront가 캐싱을 해주면 원본 서버 부하가 줄어든다.

  정적 파일, 이미지, 프론트엔드 빌드 파일 배포에 많이 사용한다.
    
  ---

  ## S3 + CloudFront

  React 같은 프론트엔드 정적 파일은 보통 이렇게 둔다.

    ```
    React build 파일
     ↓
    S3 저장
     ↓
    CloudFront 캐싱
     ↓
    사용자에게 빠르게 전달
    ```
    
  ---

  # 실무에서 자주 쓰는 조합

    ```
    Nginx
    - Gzip 압축
    - HTTPS 처리
    - Reverse Proxy
    
    CloudFront
    - 정적 파일 캐싱
    - CDN 전송
    
    Redis
    - 서버 내부 데이터 캐싱
    ```
    
  ---

  # 한 줄 정리

  Gzip 압축은 **응답 데이터를 작게 만들어 전송량을 줄이는 기술**이고, 캐싱은 **이미 받은 데이터를 저장해두고 재사용하여 요청 수와 서버 부하를 줄이는 기술**이다.