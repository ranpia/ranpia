# MoleLaw 후속 상담·통합 변경 커밋 검토

확인일: 2026-10-23. `.tools/MoleLaw_review`의 Git 객체를 읽어 아래 8개 커밋의 변경과 병합 관계를 검토했다. 인접 프로젝트를 수정하거나 모델·서버·프로젝트 테스트를 실행하지 않았다. 테스트 수치는 해당 커밋의 문서 기록이며 이번 검토의 실행 결과가 아니다. 2026년 개인 재구축을 대상으로 하며 2025년 팀 개발 기여와 구분한다.

## 커밋별 문제·변경·결과·한계

| 커밋 | 문제·제약 | 변경 | 확인한 결과 | 한계 |
| --- | --- | --- | --- | --- |
| `6d08181` | 후속 상담에서 판례를 보강하려면 사용자 동의, 현재 방의 근거, 읽은 원문과 인용의 연결이 필요하다. 무인용 동의 질문도 기존 인용 검증과 충돌할 수 있다. | 요청별 `allowCaseSearch`, 동의 요청·검색·본문 읽기 Tool, 방 근거 조회 API, V5 판례 참조, 동의 표시 저장을 추가했다. 서버가 근거 UUID를 포함한 동의 질문을 구성한다. | `LegalAnswerService`가 동의 여부로 ToolCallback을 제한하고 `FollowUpCaseToolsFactory`도 직접 실행 권한을 검사한다. 읽고 인용한 새 판례와 질문·답변을 한 트랜잭션에 저장한다. 문서는 일반 테스트 390개·실패 0·건너뜀 2, 시뮬레이션 1개·격리 MySQL 2개 성공을 기록한다. | 모델·판례는 시뮬레이션 fixture다. 실제 판례 관련성·법률 답변 품질·동시 턴 직렬화·실제 usage 정산·승인 UI 연결은 미검증 또는 후속 과제다. |
| `05083a7` | 기능 병합 뒤 서비스 파일과 실험 자료의 위치·검증 기준이 혼재했다. | `docs/branch-workflow.md`, 상태 문서, `.gitignore`를 갱신해 서비스 dev와 실험 워크트리를 구분했다. | 문서는 통합 코드 트리가 `6d08181`과 같으며 동기화 후 일반 테스트 390개·실패 0·건너뜀 2를 다시 확인했다고 기록한다. | 문서·자료 관리 변경이다. 새 법률 품질 평가나 모델 추론 성과로 세지 않는다. |
| `0a6a361` | 후속 function Tool과 reasoning 설정의 호환 문제, 긴 최초 수집 중 사용자 삭제, 복수 검색 결과의 합성과 이미 선택한 근거 보존을 다룬다. | 후속 ChatClient reasoning을 `none`으로 지정하고 허용 설정에 추가했다. 최초 저장 직전 사용자를 잠금 조회한다. 검색 배치·정확 조문 필터·색인 법령명 전달·선택 근거 우선 보존을 변경했다. | 설정 변경과 사용자 삭제 시 저장 차단 회귀 테스트 코드가 있다. 다만 이 커밋은 새 유틸리티 `LawArticleQuery`, `LawSearchResultFusion`를 참조하면서 해당 파일은 포함하지 않는다. | 메시지는 `temp`다. reasoning 오류의 근거는 코드 주석이며 이 범위에서 실제 공급자 오류 로그·수정 후 실호출 성공 로그는 확인하지 못했다. 이 커밋 단독 빌드 성공을 주장하지 않는다. |
| `1f43103` | 위 변경과 동일한 목적이며 누락된 검색 유틸리티·실행 구성을 포함한다. | `0a6a361`의 공통 변경에 정확 법령명·조문 추출, 순위 역수 합산 기반 검색 결과 합성, 관련 단위 테스트, 앱 Compose 파일을 추가했다. | 두 temp 커밋의 트리 차이는 추가 5개 파일이다. 원시 유사도는 유지하면서 명시 조문 우선·순위 합성·버전 혼합 거절을 구현했다. | 별개의 두 차례 성능 개선으로 중복 계산하지 않는다. 검색 회수율·답변 품질 개선 수치와 배포 성공은 이 변경만으로 확인되지 않는다. |
| `0eedbb1` | 법령 검색·수집 기능 브랜치를 dev에 통합한다. | PR #8 병합. 첫 부모 대비 질문 계획, 후보 선택·본문 읽기, 근거 판단, AI 검색 비교, Qdrant 관련 변경과 검증 문서 등이 들어왔다. | 부모는 `84a541d`, `2225a12`다. 기능 변경을 dev에 반영한 통합 이력이다. | 병합 제목의 ‘완성’을 운영 검증 또는 법률 품질 완성으로 해석하지 않는다. 후속 판례 기능 자체의 구현 커밋은 `6d08181`이다. |
| `0444a89` | 후속 판례 기능을 법령 기능 브랜치에 통합한다. | PR #9 병합. 부모는 `2225a12`, `6d08181`이다. | 결과 트리는 `6d08181`과 같다. | 후속 상담을 새로 한 번 더 구현한 성과가 아니다. |
| `c50f4fd` | 법령 브랜치에 병합된 후속 상담을 dev에도 반영한다. | 부모 `0eedbb1`, `0444a89`를 병합했다. | 결과 트리는 `6d08181`과 같다. 후속 판례·동의 기능의 dev 통합을 확인한다. | 기능 커밋과 중복 계산하지 않는다. |
| `7962c44` | 공통 변경을 가진 로컬·원격 temp 이력을 통합한다. | 부모 `1f43103`, `0a6a361`을 병합했다. | 첫 부모 `1f43103` 대비 파일 변경이 없다. | 병합 자체의 신규 구현·검증 성과는 없다. 최신 트리의 구성과 개별 커밋의 완결성을 구분한다. |

## 시스템 통합에서 드러난 실패와 대응

### 무인용 동의 문구와 인용 검증의 충돌

`6d08181`의 `LegalAnswerFollowUpTest.consentRequestUsesGroundedServerQuestionWithoutRequiringAnUncitedModelAnswer`는 모델이 동의 요청 Tool을 호출한 뒤 인용 없이 “찾아볼까요?”라고 답하는 상황을 재현한다. `LegalAnswerService`는 동의가 요청됐으면 모델 문구를 `Session.consentMessage()`로 교체하고, 그 이후 동일한 인용 검증을 수행한다. 이 문구에는 현재 방의 법령 근거 UUID가 들어간다. 따라서 인용 검증을 해제하는 대신 동의 안내의 출력 계약을 서버가 구성하도록 변경한 사례다.

이 사례는 코드·회귀 테스트·검증 문서가 뒷받침한다. 실제 사용자 장애의 발생 횟수·운영 영향·유료 모델 재현 결과는 해당 근거에서 확인하지 못했다. 일반 테스트 통과도 모델이 모든 동의 상황을 정확히 판단한다는 증명은 아니다.

### 판례 Tool과 reasoning 옵션의 충돌

`0a6a361`과 `1f43103`의 `AnswerChatClientConfig` 주석은 해당 모델의 Chat Completions가 function Tool과 reasoning effort 조합을 거절한다고 설명한다. 이에 후속 답변 클라이언트만 `ReasoningOptions.create(model, "none", maxTokens)`로 설정하고 공통 옵션 검증에 `none`을 추가했다. 최초 답변 클라이언트의 설정과 구분한 변경이다.

확인 가능한 사실은 **코드 주석에 적힌 호환 문제와 설정 분리**다. 저장소의 이 범위에서 공급자 원시 오류 응답이나 수정 후 실제 판례 Tool 호출 성공 로그를 확인하지 못했으므로 “실서비스 오류를 완전히 해결했다”, “모든 모델에서 Tool과 reasoning은 함께 사용할 수 없다”로 일반화하지 않는다.

## 동의·권한·인용·저장의 연결

- **동의:** `allowCaseSearch`는 해당 요청에만 적용한다. 대화 속 긍정 표현이나 과거 승인을 실행 권한으로 삼지 않는다. 동의 전 검색 Tool을 모델에게 제공하지 않으며 직접 호출도 서버가 거절한다. 기존에 방에 저장된 판례는 새 외부 검색 없이 읽을 수 있다.
- **서버 권한:** 방 소유권을 확인하고 현재 방의 법령 근거 ID로 검색을 제한한다. 검색된 판례 후보 또는 기존 방 판례만 본문을 읽는다. 새 상세 2개, 도구 실행 8회·90초, 본문 누적 추정 12,000토큰 등의 한도가 있다. 이는 실제 사용량 청구 검증과 다르다.
- **인용:** 목록은 답변 근거로 취급하지 않는다. 이번 요청에 제공한 법령과 실제 읽은 판례의 근거 UUID만 허용한다. 판례 원문과 해시를 검증하고 인용한 새 판례만 저장 대상으로 넘긴다. 인용 ID의 유효성 검증은 본문 해석·법적 결론의 정확성을 증명하지 않는다.
- **원자 저장:** 모델 호출·인용 검증 뒤 `ChatTurnService.savePreparedFollowUpTurn()`의 트랜잭션에서 판례 참조와 질문·답변을 저장한다. 실패한 대화와 추가 참조는 남기지 않는 구조지만, 별도로 확보한 공식 원문은 보존될 수 있다. 최초 법령 스냅샷은 덮어쓰지 않는다. 동시 후속 턴의 직렬화까지 완료한 것으로 설명하지 않는다.
- **검증 경계:** Postman 대역 시뮬레이션은 MVC·인증·Advisor·ToolCallback·DB·공식 HTTP 클라이언트의 연결을 확인한 기록이다. 대역의 성공과 실제 법률 품질, 탈옥 방어 성능은 구분한다.

## 근거 위치

원격 저장소의 [후속 기능 커밋](https://github.com/ranpia/MoleLaw_backend/commit/6d08181), [동기화 기록](https://github.com/ranpia/MoleLaw_backend/commit/05083a7), [첫 temp 변경](https://github.com/ranpia/MoleLaw_backend/commit/0a6a361), [유틸리티 포함 temp 변경](https://github.com/ranpia/MoleLaw_backend/commit/1f43103)을 기준으로 대조했다.

- `docs/follow-up-consultation.md`, `docs/validation-status.md`, `docs/branch-workflow.md`
- `chat/config/AnswerChatClientConfig.java`, `common/ai/config/ReasoningOptions.java`
- `chat/service/answer/LegalAnswerService.java`, `FollowUpCaseToolsFactory.java`
- `chat/service/ChatTurnService.java`, `chat/service/evidence/ChatCaseEvidenceService.java`
- `law/service/retrieval/LegalEvidenceCollector.java`, `law/service/tool/LawRagSearchToolService.java`
- `law/util/LawArticleQuery.java`, `LawSearchResultFusion.java`
- `LegalAnswerFollowUpTest`, `FollowUpConsultationSimulationTest`, `RoomEvidenceIntegrationTest`

Java 구현 경로의 접두사는 `src/main/java/com/MoleLaw_backend/`이며 테스트는 `src/test/java/com/MoleLaw_backend/` 아래다.

## 이력서 추천 문장

코드에서 확인되는 선택의 흐름은 ‘새 판례 검색에는 요청별 동의가 필요함 → 모델의 대화 해석 대신 서버가 노출 도구와 실행 권한을 제한함 → 법률 답변과 동의 안내의 목적은 다르지만 기존 인용 계약을 유지해야 함 → 동의 안내를 서버가 구성함 → 검증된 답변과 새 판례 참조만 함께 저장함’이다. 이 구조는 구현으로 확인되지만, 작성 당시 개인의 심리나 다른 대안을 검토한 과정까지 추정하지 않는다. 동의 판단의 UI 연결, 법적 관련성의 실모델 평가, 동시 턴 순서 보장은 별도 과제로 남는다.

1. 후속 상담의 외부 판례 검색은 대화 속 긍정 표현만으로 승인하지 않고, 요청별 동의 값에 따라 서버가 검색 도구 노출과 직접 실행을 제한하도록 설계했습니다. 실제 읽고 인용한 판례만 질문·답변과 함께 저장하도록 연결했습니다.
2. 동의를 묻는 안내와 법률 답변의 목적이 달라 무인용 안내가 기존 인용 검증과 충돌하는 경우를 회귀 테스트로 다뤘습니다. 검증을 해제하는 대신 서버가 방 근거 ID를 포함한 안내를 구성하도록 보완하고, 대역 기반 통합 검증과 실제 법률 답변 품질 검증을 구분했습니다.
