# MoleLaw 검색 실험 커밋별 검토

검토일: 2026-10-23. 대상은 개인 포크 `ranpia/MoleLaw_backend`의 `experiment/legal-embedding-evaluation`, 범위는 `64c34d5..bd2ae9440f3f2d8253034c73b6e5e972e5091a3c`의 비병합 커밋 24개다. 로컬 검토 경로 `.tools/MoleLaw_review`에서 각 커밋의 변경과 보존된 보고서·스크립트를 읽었다. 서버·모델·Qdrant를 실행하거나 실험을 재실행하지 않았다. 아래 결과는 커밋에 보존된 실행 기록이며 현재 DB 상태를 조회한 결과가 아니다.

2025년 팀 프로젝트 기여와 구분되는 2026년 개인 재구축의 실험 이력이다. 사용자 확인에 따르면 팀 개발에서는 RAG를 주로 담당하고 FinalAnswer는 공동 개발했다. 커밋 작성자가 같다는 이유로 과거 팀 기능 전체를 단독 개발했다고 확대하지 않는다. 아래 판단은 문서에 명시된 선정 기준과 후속 결정에 한정하며 당시 감정·동기는 추정하지 않았다.

## 판단 흐름

단일 임베딩 구현에 머물렀던 과제를 모델·청크·질의 조건 비교로 확장했다. 사전 개발용 Recall@5 기준으로 KURE 300자를 선정했으나, 공식 원문을 보며 만든 확대 평가의 높은 점수가 일상 질문 성능을 보장하지 않는다는 한계를 기록했다. 기존 자료를 읽지 않은 역할이 작성한 질문으로 다시 확인했을 때 필요한 근거의 회수율이 낮아 서비스 적용 확정을 보류했다.

이후 임계값을 낮추는 조정은 검색 순위를 바꾸지 않고 범위 밖 수용도 늘린다는 결과를 확인했다. 질문을 법률 쟁점 중심으로 변환하고 청크 크기를 바꾸어 일부 회수율을 높였지만, 필요한 근거를 전부 확보한 질문 수가 적다는 미해결 문제를 남겼다. 상위 50개 후보를 보아도 누락된 근거가 있어 재정렬만으로 해결된다고 판단하지 않았고, Multi-Query·BM25·RRF를 요소별로 비교하는 후속 계획을 작성했다. 이것이 확인 가능한 관찰 → 판단 기준 → 선택 → 결과·미해결의 흐름이다.

## 커밋별 문제·선택·결과·한계

각 해시 링크는 해당 커밋의 diff로 연결된다. 파일을 옮긴 `2178f06` 이후의 최신 근거 경로는 아래 별도 목록에 정리했다.

| 커밋 | 관찰한 문제·판단 기준 | 구현·선택 | 확인된 결과 | 한계·후속 판단 |
| --- | --- | --- | --- | --- |
| [082e389](https://github.com/ranpia/MoleLaw_backend/commit/082e389) | 로컬 전수 계산으로 만든 벡터를 실제 검색 저장소에서도 확인할 필요 | 모델·코퍼스 해시별 Qdrant 실험 컬렉션, 고정 ID upsert, 원문 payload, 조문 그룹·법령 필터, 소유권·스키마·건수 검사 구현 | Gemma 700자 6,031벡터 적재 기록. 문서 벡터 3개로 왕복·전수 점수·필터 검증 | 3개 표본 검증이며 전체 질문 품질·반복 지연 측정 전 단계. 서비스 컬렉션 전환 아님 |
| [f035590](https://github.com/ranpia/MoleLaw_backend/commit/f035590) | 문서 벡터 표본만으로 실제 질문 검색을 확인하기 어려움 | Verify에 질문 임베딩 검색 경로 추가, BGE 적재·검색 및 Gemma 재적재 확인 | 두 모델 각각 6,031개, 합계 12,062벡터. Gemma 재적재 건수 유지 | 당시 전체 질문 벤치마크는 아직 미수행 |
| [1811b59](https://github.com/ranpia/MoleLaw_backend/commit/1811b59) | Qdrant의 검색 품질과 시간 차이를 같은 입력으로 비교할 필요 | Gemma 원본·정제 220개 질문 변형의 동일 벡터로 local/exact/ann 비교. 검색 시간 3회 반복·순서 교대·워밍업 제외 | 전수 조문 집합 일치 100%, ANN 겹침 원본 99.73%·정제 99.55%; Recall@5 유지 | ANN 설정이 항상 HNSW 실행을 뜻하지 않음. 시간은 임베딩·정제·답변 생성 제외, 동시 부하 아님 |
| [994d4f7](https://github.com/ranpia/MoleLaw_backend/commit/994d4f7) | Gemma 한 모델의 저장소 검증을 BGE에도 적용 | BGE 전체 질문 검색과 반복 시간 추가, 비교 집계 확장 | 두 모델 440개 질문 변형·2,640시간 표본. 전수 점수 허용오차 내 일치, ANN Recall@5 변화 없음 | 상위 10개 집합·순위·정답 여부는 서로 다름. 일부 nDCG@10 변화도 기록 |
| [11ad549](https://github.com/ranpia/MoleLaw_backend/commit/11ad549) | 같은 질문 정제 조건에서 KURE도 비교할 필요 | KURE 700자 동일 벡터·고정 정제 질문 재사용, Qdrant 검증과 런타임 분기 확장 | KURE 검증용 Recall@5 76.48%→81.12%; 세 모델 660변형·3,960시간 표본 | BGE보다 Recall은 소폭 높으나 전체 MRR은 낮음. 지표별 장점이 달라 단일 우열 단정 안 함 |
| [d644fac](https://github.com/ranpia/MoleLaw_backend/commit/d644fac) | 정제 질문에 유리한 청크 크기가 원본 질문과 다를 수 있음 | KURE 300·500자 추가, 원문·질문·정답·분할 identity 확인, 크기별 컬렉션·출력 분리, 순차 실행 | KURE 정제 개발 Recall@5 300자 75.17%, 500·700자 73.50% | 검증용은 500·700자가 높음. 사전 개발 기준과 검증 결과를 구분 |
| [d6f4f5b](https://github.com/ranpia/MoleLaw_backend/commit/d6f4f5b) | KURE의 청크 경향을 다른 모델로 일반화할 수 없음 | Gemma 300·500자 정제·Qdrant 결과 추가 | 정제 개발 Recall@5는 세 크기 모두 72%; 검증용 500자 80.81% | 검증용 최고값으로 반복 선택하지 않음. 청크 확대가 항상 개선된다는 결과 아님 |
| [41efc32](https://github.com/ranpia/MoleLaw_backend/commit/41efc32) | 세 모델의 청크·입력 조건을 빠짐없이 비교해야 함 | BGE 300·500자 추가, 9구성 통합 집계 | 110질문×원본/정제×9구성=1,980실행 조합; 검색 시간 11,880표본, 문서 63,486포인트 | 독립 질문이 1,980개라는 뜻 아님. 같은 질문의 반복 조건이며 자동 정제 API 검증 아님 |
| [ef2da94](https://github.com/ranpia/MoleLaw_backend/commit/ef2da94) | 긴 비교표에서 선택 기준과 크기별 차이 파악이 어려움 | 개발/검증 그래프 축을 0~100%로 통일하고 개발 Recall@5로 정렬 | 지표·청크별 차이를 확인하는 요약·그래프 생성 | 새 추론이나 성능 개선 커밋 아님 |
| [1255f63](https://github.com/ranpia/MoleLaw_backend/commit/1255f63) | 비교군 확장과 검색 기준 모델 선정 필요 | Qwen3-Embedding-0.6B 런타임·공식 revision·입력 형식 검증 및 3크기 추가. 사전 정제 개발 Recall@5 기준 KURE 1024차원·300자·중복64 선정 | 4모델×3크기=12구성. KURE 개발 75.17%·검증 80.52%. Qwen500 검증 82.17%여도 사전 선정 기준 유지 | 일반적 최고 모델 아님. KURE300 청크 수는 500자보다 약28% 많음. 실제 서비스 모델·색인 전환 안 함 |
| [180c7ee](https://github.com/ranpia/MoleLaw_backend/commit/180c7ee) | 선정 조건이 새 법령 범위에서도 유지되는지 확인 필요 | 30개 주 법령·6,527원문 단위·10,999청크, 총270질문 입력 고정. 신규 Recall·Complete·기존 회수율 유지·정제 비퇴행 네 기준 사전 설정 | 신규 양성120·음성40을 기존110질문과 분리한 검증 준비 | 입력 준비 커밋이며 결과 아님. 질문은 공식 원문을 본 어시스턴트 작성, 무작위·블라인드 표본 아님 |
| [ffaa4d2](https://github.com/ranpia/MoleLaw_backend/commit/ffaa4d2) | 사전 고정 확대 검증의 통과 여부 확인 | 원본·정제540개 결과, 법령·단일/다중 근거별 집계 저장 | 신규 정제 Recall@5 99.17%·Complete@5 98.33%, 기존 검증 정제80.52%; 네 기준 통과 | 99% 일반 사용자 검색 성능 아님. 신규 음성11/40이 점수 기준 통과. serviceReady=false |
| [8a4c123](https://github.com/ranpia/MoleLaw_backend/commit/8a4c123) | 모델 비교·확대 검증·서비스 완료 상태가 혼동될 수 있음 | 최종 요약에 평가 범위·선정·한계·실제 LLM 후속 검증을 통일 | 기존110질문 비교와 신규 포함270질문 평가를 별도로 읽게 정리 | 문서 정리이며 신규 성능 결과 아님. 당시 선정은 이후 독립 평가 판단과 함께 읽어야 함 |
| [6efbabf](https://github.com/ranpia/MoleLaw_backend/commit/6efbabf) | 원문을 보고 만든 질문의 편향을 추가 확인할 필요 | 기존 원문·정답·결과를 읽지 않은 질문 작성 역할, 별도 정제·정답 검수 역할. 100질문 및 해시 고정 | 코퍼스내36·추가자료필요44·범위밖15·사실부족5로 분류. 정답5문항 수정 후 실행 준비 | 같은 계열 LLM 역할 분리이며 실사용자·법률전문가 독립 평가를 대체하지 않음 |
| [4413496](https://github.com/ranpia/MoleLaw_backend/commit/4413496) | 선정 구성이 새 질문에서도 충분한 근거를 찾는지 확인 | 기존 모델·청크·정답을 고정해 원본/정제200검색, 실패·대체근거 사례 기록 | 코퍼스내36문항 정제 Recall@5 16.44%, Complete@5 3/36. 서비스 적용 확정 보류, KURE는 기존 비교 기준선으로 보존 | 앞선99.17%와 질문셋이 달라 성능 하락폭으로 빼지 않음. 정답 목록 외 관련 근거 누락 가능성 |
| [64c98fc](https://github.com/ranpia/MoleLaw_backend/commit/64c98fc) | 임계값 조정이 낮은 수용률을 해결하는지 검토 | 저장 점수만으로 임계값 후보·상충관계 분석, 해당 질문셋을 튜닝 자료로 전환 | 정제 약0.586에서 범위내16/36·그외13/64, 약0.535에서26/36·43/64 수용 | 임계값을 낮춰도 순위·Recall은 불변. 운영 미적용, 후속2178f06에서 후보 비활성화 |
| [0ca0384](https://github.com/ranpia/MoleLaw_backend/commit/0ca0384) | 문장 다듬기만으로 법률 검색 개념을 충분히 드러내지 못한 문제 검토 | 원본만 읽는 작성자와 의미 검수자 분리, 법률 쟁점 중심 단일 질의100개 고정. 사실·결론 단정 위험4건 수정 | 모델·문서·정답을 유지해 입력 전략만 바꾸는 실험 준비 | 질문당1질의·1벡터. Multi-Query·sparse 구현 아님. 이미 결과를 보고 설계한 탐색 평가 |
| [d3126a0](https://github.com/ranpia/MoleLaw_backend/commit/d3126a0) | 새 질의가 정답 일부·전체 확보에 주는 영향 비교 | 원본·기존정제·쟁점질의300결과 및 질문별 개선·악화 저장 | 같은36문항 Recall@5 16.44%→28.70%, Hit@5 11→18개, Complete@5 3개 유지 | 9개 개선·1개 악화. 근거 전체 확보는 개선되지 않았고 자동 LLM 변환·답변 정확도 성과 아님 |
| [0be7f14](https://github.com/ranpia/MoleLaw_backend/commit/0be7f14) | 질의 개선 이후 청크 크기만 바꾸는 효과 분리 필요 | 동일6,527원문·질문벡터300개 보존, 500자8,286청크·700자7,396청크 준비, 순차 실행·캐시 재사용 | 고정 manifest·원문 복원·입력 일치 검사와 실행 준비 | 준비 결과이며 새 크기 성능은 다음 커밋에서 확인 |
| [87b64ef](https://github.com/ranpia/MoleLaw_backend/commit/87b64ef) | 청크 확대가 누락 근거를 해결하는지 비교 | 세 입력×세 크기900결과 보존, 질문별 대응 비교 | 쟁점질의 Recall@5 300/500/700자=28.70/31.39/30.56%; Complete=3/4/3문항 | 500·700 차이는 한 문항, 서비스 최적값 확정 안 함. 대부분 질문의 누락은 남음 |
| [7bdc565](https://github.com/ranpia/MoleLaw_backend/commit/7bdc565) | 상위5 순위 문제인지 후보 자체 누락인지 구분 필요 | 기존 질문벡터로 상위50 조회. 이후 Hit@5·Complete@5 공동 우선으로 변경하고 이전 기준은 보존 | KURE500 쟁점질의 Recall@50 56.57%, Hit@50 30/36·Complete@50 11/36 | 후보50에도 필요한 근거가 부족해 재정렬만으로 모두 복구 불가. 과거 사전 기준을 소급 변경하지 않음 |
| [eb5907e](https://github.com/ranpia/MoleLaw_backend/commit/eb5907e) | 후보 누락 원인과 검색 전략 효과를 구분할 필요 | 쟁점dense→원본+쟁점 Multi-Query→쟁점BM25→dense+BM25 RRF 순서·동일 청크·후보 수·결합 규칙 고정 | KURE500 기준선, 경로당50후보·동일가중 RRF 상수60 등 비교 계약 문서화 | 2~4단계는 실행 전 계획. BM25/Hybrid 성능 개선으로 기재 금지 |
| [2178f06](https://github.com/ranpia/MoleLaw_backend/commit/2178f06) | 실험 목적·경로·현재 판단이 흩어지고 다른 모델 후보회수 비교가 필요 | 목적별 experiments 재배치·layout/Paths 해석기, Qdrant 관리 목록·별칭, BGE/Qwen Recall50 실행·집계 코드와 결과 추가, 임계값 후보 비활성화 | BGE/KURE 비교 및 Qwen 최종 report·324질의·16,200순위 자료 존재. Qwen500 쟁점질의 Hit@5 21/36·Complete5/36·Recall5 33.01% | 커밋 제목과 달리 구조정리 외 결과 추가 포함. Qwen 실행중이라는 요약과 완료 원자료가 충돌함. 아래 불일치 기록 참조 |
| [bd2ae94](https://github.com/ranpia/MoleLaw_backend/commit/bd2ae94) | 로컬에만 남은 과거 결과·코퍼스 인벤토리의 재현 근거 보존 필요 | 공유 후보114개를 SHA로 비교해 기존100개 중복을 피하고14개 추가 보존, 매니페스트 작성 | 6,527행 원문 단위 인벤토리·초기 실행 결과·과거 Qdrant 관측 자료 보존 기록 | 새 모델 추론·검색·점수 재산정 아님. 과거 Qdrant 목록을 현재 상태로 쓰지 않음 |

## 최신 원자료와 요약 불일치

`candidate-coverage-v1/README.md`와 `reports/model-comparison.md`는 Qwen의 최종 보고서가 없어 비교에서 제외했다고 적혀 있다. 그러나 같은 최신 리비전에 `reports/qwen3-0.6b/report.json`과 `summary.csv`, `queries.csv`, `top50.csv`가 존재한다. 최종 보고서는 `completedAtUtc=2026-10-20T09:43:27.0786726Z`, `serviceReady=false`를 기록하고 있다. 따라서 “Qwen 실행 미완료”라는 요약을 그대로 최신 사실로 옮기지 않는다. 완료 원자료 존재와 요약 미갱신을 함께 기록한다.

Qwen의 법률 쟁점 질의 원자료는 300/500/700자에서 Hit@5 20/21/21문항, Complete@5 4/5/5문항이다. 500자 Recall@5 33.01%, 700자32.31%다. KURE500의20/36·4/36보다 한 문항씩 높지만 이미 탐색한 작은36문항 표본이므로 새 운영 모델 확정 근거로 쓰지 않는다. 실행 완료 기록의 존재와 실제 재실행 검증은 구분한다.

## 최신 근거 경로

아래는 모두 검토한 리비전에 고정된 링크다. 표의 오래된 커밋은 이동 전 경로를 사용한다.

- [전체 판단 요약](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/experiments/summary.md)
- [모델 선정 및 후속 판단](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/experiments/model-comparison/review/model-selection.md)
- [독립 질문 결과·고정 정답 한계](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/experiments/retrieval-validation/independent-v1/reports/kure-v1/interpretation.md)
- [법률 쟁점 질의 효과·미해결](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/experiments/query-strategies/sentence-transform-v1/reports/kure-v1/interpretation.md)
- [청크 크기 비교](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/experiments/query-strategies/chunk-sizes-v1/reports/kure-v1/interpretation.md)
- [후보50 Qwen 완료 원자료](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/experiments/query-strategies/candidate-coverage-v1/reports/qwen3-0.6b/report.json)
- [Qdrant 실제 검색 비교 코드](https://github.com/ranpia/MoleLaw_backend/blob/bd2ae9440f3f2d8253034c73b6e5e972e5091a3c/evaluation/legal-retrieval/scripts/benchmark-qdrant.ps1)

## 이력서에 사용할 수 있는 사례

### 1. 선정 결과를 다른 질문으로 검토하고 적용을 보류한 경험

> 임베딩 모델 4종과 청크 길이 3종을 같은 질문으로 비교하고, 사전 개발용 검색 회수율 기준으로 KURE를 검색 기준 모델로 선정했습니다. 원문을 보며 작성한 평가의 한계를 확인하기 위해 질문 작성과 정답 검수 역할을 분리한 추가 평가를 진행했습니다. 코퍼스 내 36문항에서 필요한 근거 전체를 확보한 질문이 3개에 그쳐 서비스 적용 확정을 보류하고, 질의 전략과 후보 회수 범위를 후속 과제로 분리했습니다.

추천 이유: 높은 수치만 취하지 않고 평가 설계의 한계와 적용 판단을 연결한다. 99.17%와16.44%를 같은 표본의 성능 하락으로 쓰지 않는다.

### 2. 검색 일부 적중과 근거 전체 확보를 구분한 경험

> 낮은 검색 회수율을 개선하기 위해 같은 모델·원문·정답을 고정하고 질문을 법률 쟁점 중심 질의로 변환했습니다. 36문항의 Recall@5는16.44%에서28.70%로 높아졌지만 필요한 근거를 모두 확보한 질문은3개로 같았습니다. 청크 크기와 상위50개 후보를 추가 비교해, 질문 변환과 재정렬만으로 해결되지 않는 근거 누락을 구분하고 Multi-Query·BM25·결합 검색의 단계별 비교를 계획했습니다.

추천 이유: 개선 수치와 남은 실패를 함께 설명한다. 고정 변환문의 탐색 평가이며 실시간 LLM 자동 정제 기능 구현·서비스 답변 정확도 향상으로 쓰지 않는다.

### 3. 실험 검색을 실제 벡터 저장소에서 검증한 경험

> 오프라인 전수 계산의 결과를 확인하기 위해 동일한 문서·질문 벡터를 Qdrant 실험 컬렉션에 적재하고, 전수·근사 설정의 조문별 검색 결과를 비교했습니다. 모델·코퍼스 해시와 차원·건수를 검증하고 고정 ID로 재적재 시 중복을 방지했습니다. 질문 임베딩 시간을 제외한 검색 지연을 반복 측정하며 서버 처리 시간과 클라이언트 전체 시간을 분리해 기록했습니다.

추천 이유: 구현·실측·성능 해석 범위를 함께 보여 준다. 운영 부하 시험·서비스 Qdrant 전환 완료·무중단 운영으로 확대하지 않는다.
