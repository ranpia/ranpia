# 김정현 | Java·Spring 백엔드 개발자

Java와 Spring Boot로 인증, 데이터 처리, 외부 API 연동을 구현합니다. 팀 프로젝트에서 요구사항 분석과 기능 설계를 맡았고, 현재는 법률 상담 서비스 MoleLaw를 포크해 인증과 RAG 구조를 재구축하고 있습니다.

[이력서](이력서_김정현.md) · [상세 포트폴리오](포트폴리오_김정현.md) · [MoleLaw 개인 재구축](docs/MoleLaw_개인재구축.md) · [Email](mailto:richard.kim146@gmail.com)

삼육대학교 컴퓨터공학과 졸업 · Java·Spring 백엔드 신입

## 주요 기술

| 분야 | 사용 경험 |
| --- | --- |
| 백엔드 | Java, Spring Boot, Spring Security, Spring Data JPA, REST API |
| 데이터·인증 | MySQL, Oracle, Redis, JWT, OAuth2 |
| AI 연동 | Spring AI, OpenAI API, 임베딩·코사인 유사도 검색 |
| 배포·협업 | Docker, GitHub Actions, AWS EC2·RDS, Git |
| 기타 프로젝트 | Python, PyTorch, OpenCV, Selenium, JSP |

## 대표 프로젝트

### MoleLaw | 법률 상담 서비스 → 개인 재구축

**팀 프로젝트 2025.06–07 · 개인 재구축 2026.10–진행 중**

팀장 · 총 4명(프론트엔드 1명, 백엔드 3명) · 요구사항 분석·전체 기능 설계·백엔드 개발

팀 개발의 주 담당은 RAG이며, 답변 생성부인 `FinalAnswer`는 팀원과 같은 화면을 보며 함께 개발·수정했습니다. 인증 등 다른 영역은 팀원 구현을 통합하면서 수정·논의했습니다. 이후 개인 재구축에서 인증과 RAG 구조를 다시 설계하고 있습니다.

- 법령 청크와 질문의 임베딩을 생성하고 코사인 유사도로 검색했습니다. 임베딩 병렬 처리·중복 스킵과 유사도가 낮을 때 법령 수집·재검색하는 경로를 구현했습니다.
- `FinalAnswer`가 검색한 법령 청크를 프롬프트와 참고자료에 반영하도록 개선하고, 답변과 Markdown 참고자료를 분리했습니다.
- 개인 재구축에서는 Spring AI 호출 통합, 법령·판례 내부 Tool과 원문 보존, Flyway 초기 스키마를 구현했습니다.
- Access를 최대 5분 stateless JWT로 전환하고 Redis에 Refresh 해시를 저장해 Lua 기반 회전·재사용 탐지·폐기를 구현했습니다. 기존 Access는 로그아웃 후에도 만료까지 유효할 수 있는 제약을 명시했습니다.

Qdrant 검색·ETL·상담 근거 스냅샷·대화 메모리는 후속 개발 계획입니다.

[팀 원본](https://github.com/24Sahmyook-Team-s1/MoleLaw_backend) · [개인 포크](https://github.com/ranpia/MoleLaw_backend) · [개발 브랜치](https://github.com/ranpia/MoleLaw_backend/tree/dev) · [설계와 구현 범위](docs/MoleLaw_개인재구축.md)

### Molecom | DICOM 의료 영상 관리 서비스

**2025.08–09 · 집중 개발 약 1개월 미만**

팀장 · 총 4명(프론트엔드 1명, 백엔드 3명) · 요구사항 분석·전체 기능 설계·백엔드 개발

- Oracle의 의료 영상 메타데이터와 MySQL의 서비스 데이터를 함께 다루도록 DB별 JPA 설정과 트랜잭션 매니저를 분리했습니다.
- 환자·검사·시리즈·이미지 계층 조회와 SMB/CIFS 저장소의 DICOM 파일을 전달하는 Spring MVC 스트리밍 API를 구현했습니다.
- 사용자 작업과 DICOM 열람 이력을 저장하고, 로그 유형별 조회 및 시간순 통합 조회 API를 구현했습니다.

[저장소](https://github.com/24Sahmyook-Team-s1/Molecom_backend)

### ThinkTrip | AI 여행 일정 서비스

**2025.04–06 · 집중 개발 약 1개월 미만, 이후 추가 개발·유지보수**

백엔드 담당 · 총 2명(프론트엔드 1명, 백엔드 1명)

- Google OAuth2 로그인 구현 후 Kakao 로그인으로 전환하고 JWT 인증을 연동했습니다.
- 여행 계획의 시작일·종료일을 모델링하고, D-Day 계산과 날짜 필수 검증을 보완했습니다.
- 사용자별 호출 횟수·날짜를 관리해 무료 계정의 GPT 사용을 하루 5회로 제한했습니다.

[저장소](https://github.com/Th-nkTrip/thinktrip-back)

## 기타 프로젝트

| 프로젝트 | 본인 커밋 활동 | 주요 내용 | 저장소 |
| --- | --- | --- | --- |
| MolePMS | 2025.02 | JSP 일정·간트 차트 기능, Docker·GitHub Actions 배포 설정 개선 | [GitHub](https://github.com/ranpia/molepmsporject) |
| Fruit Box AI | 2025.03 | OpenCV 보드 인식·Selenium 자동화, CNN OCR·강화학습 실험 | [GitHub](https://github.com/ranpia/applegame) |

기간은 저장소에서 확인한 본인 커밋 활동의 월 범위입니다. 실제 집중 개발 기간과 구분하며, 상세 날짜·리비전·확인 범위는 [프로젝트 활동 기록](docs/프로젝트_활동기록.md)에 정리했습니다.
