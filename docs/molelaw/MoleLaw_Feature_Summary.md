
# 📘 MoleLaw 기능 정의 요약

## 🧩 프로젝트 개요

| 항목           | 내용                                                                 |
|----------------|----------------------------------------------------------------------|
| **서비스명**   | MoleLaw (몰루로 묻고 법으로 답하다)                                     |
| **설명**       | 사용자의 법률 질문을 받아 GPT가 관련 법령 및 판례를 검색하여 자동 응답하는 상담형 챗봇 서비스 |
| **DB**         | MySQL                                                                |
| **언어/프레임워크** | Java 17 / Spring Boot 3.5.0                                          |
| **외부 API**   | OpenLaw API (법령/판례), OpenAI API (GPT 응답, 임베딩 벡터 생성)         |

---


## 🧩 의존성 목록 (build.gradle 기준)

### 🔧 Spring Boot Starters
- `spring-boot-starter-data-jpa`: JPA 기반 ORM
- `spring-boot-starter-security`: 로그인, 인증, 권한 관리
- `spring-boot-starter-web`: 전통적인 MVC 웹 지원
- `spring-boot-starter-webflux`: WebClient 기반 비동기 통신 지원
- `spring-boot-starter-oauth2-client`: 소셜 로그인 (Google, Kakao 등)
- `spring-boot-starter-validation`: javax 검증 어노테이션 처리

### 🛠️ 추가 라이브러리
- `me.paulschwarz:spring-dotenv`: .env 환경변수 지원
- `com.fasterxml.jackson.core:jackson-databind`: JSON ↔ Java 객체 매핑

### 📄 Swagger
- `springdoc-openapi-starter-webmvc-ui:2.8.4`: Swagger UI 제공

### 🔐 JWT 관련 (JJWT)
- `jjwt-api:0.11.5`: JWT 생성/파싱/검증
- `jjwt-impl:0.11.5`: JWT 내부 구현체
- `jjwt-jackson:0.11.5`: JWT ↔ JSON 직렬화 지원

### 🧪 테스트 / 개발 도구
- `spring-boot-starter-test`, `spring-security-test`: 통합 테스트
- `spring-boot-devtools`: 개발 환경 자동 재시작
- `junit-platform-launcher`: 테스트 런타임
- `lombok` + `annotationProcessor`: 보일러플레이트 제거

### 🛢️ DB 관련
- `mysql-connector-j:8.0.33`: MySQL 드라이버


### 🔐 인증/보안
- `spring-boot-starter-security`: 인증/인가 처리
- `spring-boot-starter-oauth2-client`: Google/Kakao 소셜 로그인
- JWT 관련:
  - `jjwt-api`: JWT 생성/파싱/검증 API
  - `jjwt-impl`: JWT 내부 동작 구현
  - `jjwt-jackson`: JWT ↔ JSON 변환 (Jackson 연동)
- 쿠키 기반 토큰 전달 (`httpOnly`, `secure`, `SameSite=Lax`)

### 🧪 API 문서화
- `springdoc-openapi-starter-webmvc-ui`: Swagger UI 자동 생성 (`/swagger-ui.html`)

---

## 🧱 핵심 DB 구조 (ERD 요약)

\`\`\`mermaid
erDiagram
  USER ||--o{ CHATROOM : has
  CHATROOM ||--o{ MESSAGE : has
  LAW ||--o{ LAWCHUNK : has
  LAWCHUNK ||--|| LAWEMBEDDING : has
\`\`\`

- **User**: 이메일 + provider로 복합 고유성 보장
- **ChatRoom**: 사용자별 채팅방
- **Message**: 암호화된 메시지 저장 (USER / BOT / INFO)
- **Law / LawChunk / LawEmbedding**: 법령, 조문 단위, 임베딩 벡터 저장

---

## 🔐 로그인 및 인증 흐름

### ✅ 로그인 방식
| 방식     | 설명                     |
|----------|--------------------------|
| Local    | 이메일 + 비밀번호 로그인    |
| Google   | OAuth2 기반 소셜 로그인   |
| Kakao    | OAuth2 기반 소셜 로그인   |

- 로그인/회원가입 시 Access + Refresh 토큰 발급
- **Access Token**: 15분 유효
- **Refresh Token**: 7~30일 유효 (재발급용)

### 🔁 인증 동작 흐름
1. 로그인 성공 시 토큰을 `Set-Cookie`로 전달
2. 클라이언트는 쿠키의 AccessToken으로 인증
3. 만료 시 /reissue 요청으로 재발급 시도
4. Refresh까지 만료되면 재로그인 유도

---

## 🧠 챗봇 응답 처리 흐름

### 1단계: 사용자 질문 수신
- 키워드 추출 (ExtractKeyword)
- 채팅방 생성 + 사용자 메시지 저장

### 2단계: 관련 법령/판례 검색 및 GPT 호출
- **LawSimilarityService**: 유사한 법령 조문 검색 (벡터 유사도 기반)
  - 유사도 부족 시 키워드 기반 fallback 수행
- **CaseSearchService**: 관련 판례 2~5개 검색
- **GptService**: 질문 + 법령 + 판례 기반 GPT 프롬프트 구성 및 응답 생성

### 3단계: 결과 저장 및 응답
- GPT 응답(BOT), infoMarkdown(INFO)을 암호화 후 저장
- 모든 메시지를 클라이언트에 FirstMessageResponse로 반환

---

## 🗃️ 채팅 관련 API 요약

| 경로                        | 설명                             |
|-----------------------------|----------------------------------|
| `GET /api/chat-rooms`       | 사용자의 채팅방 리스트 조회          |
| `GET /api/chat-rooms/{id}`  | 특정 채팅방 메시지 전체 조회         |
| `POST /api/chat-rooms/first-message` | 첫 질문 입력 및 GPT 응답 생성  |
| `POST /api/chat-rooms/followup` | 후속 질문 응답 요청              |
| `DELETE /api/chat-rooms/{id}` | 채팅방 삭제 (메시지 포함)         |

---

## 🚨 공통 예외 처리

- 모든 예외는 `MolelawException`으로 래핑
- `ErrorCode` 열거형 기반 메시지 통일화

| 코드명                    | 상태코드 | 설명                              |
|---------------------------|----------|-----------------------------------|
| INVALID_REQUEST           | 400      | 잘못된 요청                        |
| UNAUTHORIZED              | 401      | 인증 필요                          |
| FORBIDDEN                 | 403      | 권한 없음                          |
| NOT_FOUND                 | 404      | 대상 없음                          |
| GPT_API_FAILURE           | 500      | GPT API 호출 실패                  |
| OPENLAW_API_FAILURE       | 502      | 공공법령 API 호출 실패            |
| USER_NOT_FOUND            | 502      | 유저 조회 실패                     |
| PASSWORD_FAIL             | 400      | 비밀번호 불일치                    |
| TOKEN_FAIL                | 400      | RefreshToken 무효                 |
| DUPLICATED_EMAIL          | 400      | 이메일 중복                        |
| CHATROOM_NOT_FOUND        | 404      | 채팅방 없음                        |
| UNAUTHORIZED_CHATROOM_ACCESS | 403  | 비인가 채팅방 접근                 |

---

## 📄 클래스 다이어그램

- 정의서 내 다이어그램:
  - `./docs/main.png`
  - `./docs/jwt.png`
  - `./docs/userandchat.png`
  - `./docs/lawandgpt.png`
  - `./docs/utill.png`
  - `./docs/dto.png`

---

## 🗃️ 저장 및 관리

- 정의서는 프로젝트에 파일(`README.md`)로 저장
- 기능 변경/추가 시 지속 업데이트
- 법령/판례 본문은 DB에 저장되며, API 호출 응답만 프롬프트에 사용 (캐싱 없음)
