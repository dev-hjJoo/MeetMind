---
title: Day-1 Evaluation Specification (v0)
version: 0.1.0
status: draft
created: 2026-04-24
updated: 2026-04-24
owner: ml-eval-lead
---

# Day-1 Evaluation Specification (v0)

> MeetMind의 **첫 번째 평가 체계**를 정의한다.
> 이 스펙은 ADR-0001(모델 스택)의 `proposed → accepted` 승격 조건이기도 하다.
> 스펙 자체의 업데이트는 MINOR 버전업으로 반영한다.

## 1. 목표

1. 모델·파이프라인 변경이 **메트릭으로 직접 검증**되는 상태 확보.
2. PR 머지 기준으로 **회귀 테스트**가 CI에서 작동.
3. ADR · 아키텍처 · 모델 선택 논의의 **기본 언어를 데이터**로.

## 2. 메트릭 (v0 범위)

### 2.1 ASR — Speech-to-Text

| 메트릭 | 정의 | 목표 (한국어 도메인) |
|---|---|---|
| **WER** | Word Error Rate | ≤ 15% (p50), ≤ 20% (p95) |
| **CER** | Character Error Rate (한국어 주요 지표) | ≤ 7% (p50) |
| **RTF** | Real-Time Factor (= 처리 시간 / 오디오 길이) | ≤ 0.15 @ GPU fp16, ≤ 1.0 @ CPU int8 |

### 2.2 Diarization — 화자 분리

| 메트릭 | 정의 | 목표 |
|---|---|---|
| **DER** | Diarization Error Rate (miss + false alarm + confusion) | ≤ 15% |
| **JER** | Jaccard Error Rate | ≤ 20% |
| **Speaker count MAE** | \|predicted − true\| | ≤ 1 (3–8인 회의) |

### 2.3 Alignment — 전사·화자 정합

| 메트릭 | 정의 | 목표 |
|---|---|---|
| **Word-level speaker accuracy** | 올바른 화자로 라벨된 단어 비율 | ≥ 90% |

### 2.4 Summarization — 요약 품질

| 메트릭 | 정의 | 목표 |
|---|---|---|
| **Faithfulness (human)** | 5점 Likert, 2 라벨러 평균 | ≥ 4.0 (p50) |
| **Coverage (human)** | 주요 결정 · 액션 포함률 | ≥ 80% |
| **Hallucination rate** | 원본에 없는 claim 비율 | < 3% |

### 2.5 Action Item Extraction

| 메트릭 | 정의 | 목표 |
|---|---|---|
| **Precision** | 추출 액션 중 실제 액션 비율 | ≥ 0.80 |
| **Recall** | 실제 액션 중 추출된 비율 | ≥ 0.70 |
| **Ownership accuracy** | 담당자 매칭 정확도 | ≥ 0.85 |
| **Deadline accuracy** | 기한 매칭 정확도 (±1일) | ≥ 0.70 |

### 2.6 End-to-End Latency

| 메트릭 | 목표 |
|---|---|
| 1시간 회의 요약까지 p50 | ≤ 60초 |
| 1시간 회의 요약까지 p95 | ≤ 90초 |

## 3. 골든셋 (v0)

### 3.1 구성
- **크기**: 20개 회의, 총 ~10시간
- **언어**: 한국어 14 · 영어 4 · 한/영 혼합 2
- **길이**: 15분 × 5 / 30분 × 8 / 60분 × 5 / 90분 × 2
- **화자 수**: 2인 × 4 / 3–4인 × 10 / 5–8인 × 6
- **도메인**: 제품 회의 10 · 리서치 토론 6 · 임원 리뷰 4

### 3.2 소스
- **Primary**: 내부 자체 녹음 (사전 동의 + PII 처리).
- **Secondary**: 상업 이용 가능한 오픈 데이터 보조 (예: AMI Meeting Corpus, KsponSpeech 일부).
- **라이선스 문서화**: `data/golden/v0/MANIFEST.yml`에 출처 · 라이선스 · 동의 기록 명시.

### 3.3 애너테이션
- **전사**: 2명이 독립 전사 → 3차 리졸버로 골드 확정. 단어 단위 타임스탬프.
- **화자 라벨**: 익명 라벨 (`Speaker_A`, `Speaker_B`, …).
- **요약 · 결정 · 액션**: 2명 라벨러 → 리졸버 합성.
- **라벨러 간 일치도 (Cohen's κ)**: 요약 쿼리 ≥ 0.7 유지.

## 4. 평가 프로토콜

### 4.1 오프라인 회귀 평가
- CI에 `make eval` 타겟. 골든셋 전체 실행 후 리포트 생성.
- 결과 저장: `benchmarks/reports/<commit-sha>.json`.
- **리그레션 가드레일**: 주요 메트릭이 이전 `main` 대비 **5% 이상 악화** 시 PR 차단 (soft — `ml-eval-lead` 승인 시 통과).

### 4.2 휴먼 평가 루프
- 주 1회: 10개 샘플 블라인드 평가 (summarization + action).
- 분기 1회: 전체 20개 재평가.
- 결과: `benchmarks/human/<ISO-week>.md`.

### 4.3 PR 게이팅
- AI/ML 관련 파일 변경 PR은 `make eval` 결과 리포트를 PR 코멘트로 **자동 첨부**.
- 리그레션 가드레일 위반 시 `ml-eval-lead` 라벨 리뷰어 자동 지정.

## 5. ADR-0001 승격 조건

`proposed → accepted` 프로모션은 **다음 모두** 충족 시:

- 골든셋 v0 기준 한국어 **WER ≤ 15%**, **DER ≤ 15%**, **faithfulness ≥ 4.0** 달성.
- 한국어 도메인 **WER p95 ≤ 20%**.
- **RTF ≤ 0.15 (GPU)** 또는 **≤ 1.0 (CPU int8)** 중 하나 이상.
- CI에 회귀 가드레일 green 확인.

## 6. 리포팅

- `benchmarks/dashboard.md` — 최신 결과 스냅샷 + 히스토리 링크.
- Weekly summary → `delivery-manager`가 릴리스 노트에 요약 인용.
- 중대 리그레션은 이슈로 자동 등록.

## 7. Open Questions

- 온프렘 fallback LLM의 평가는 default 경로와 분리할 것인가 통합할 것인가?
- 한국어 고유명사(사명 · 인명 · 기술 용어) 별도 WER 분할 리포팅이 필요한가?
- 상업 이용 가능한 한국어 회의 데이터셋의 법적 검토 — 내부 녹음 의존이 커지면 수집 파이프라인 스펙 필요.
- PR 리그레션 가드레일의 임계값(5%)이 노이즈 대비 적절한지 첫 한 달 모니터링 후 재조정.

## 8. Related

- PRD v0 `docs/03-prd-v0.md` — 성공 지표 섹션 기반.
- ADR-0001 `docs/adr/0001-model-stack.md` — 본 스펙이 승격 게이트.
- 에이전트 팀 `docs/01-agent-team.md` — `ml-eval-lead`, `delivery-manager`.

## Change Log
- **v0.1.0** (2026-04-24): 초안. 메트릭 · 골든셋 · 프로토콜 · ADR-0001 승격 조건 정의.
