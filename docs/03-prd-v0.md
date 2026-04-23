---
title: PRD v0 — MeetMind
version: 0.1.0
status: draft
created: 2026-04-24
updated: 2026-04-24
owner: product-strategist
---

# MeetMind — PRD v0

> 이 문서는 **discovery 단계 PRD (v0)** 로, 아직 검증되지 않은 **가설(H#)** 을 명시적으로 담는다.
> 각 가설은 `user-researcher` 인터뷰와 `ml-eval-lead`의 초기 평가로 검증한다.
> v0.2 진입 조건: H1–H3 피드백 반영 + ADR-001~003 accepted.

---

## 1. Problem

지식노동자 업무 시간의 상당 부분이 회의와 "회의 뒷처리"(기록, 요약, 할일 추출, 후속 공유)에 쓰인다.
기존 회의 기록 도구는 **기록(transcription)은 강하지만, 이해(understanding)는 약하다**:

- 긴 전사는 다시 읽히지 않는다.
- 요약은 기계적이고, 맥락(이전 회의 · 참여자 의도 · 결정 배경)을 잇지 못한다.
- 후속 "누가, 언제까지, 무엇을"이 구조화되지 않아 사람이 다시 정리해야 한다.
- 한국어 품질 · 온프렘 배포 · 화자 분리 같은 실무 요구가 기존 SaaS에서 충족되지 않는 경우가 많다.

## 2. Target User (Primary Hypothesis)

### H1. 1차 타겟: 제품팀 PM / 리서치 팀 리더
- 주 4–10회 회의, 절반 이상에서 자신이 주재 또는 핵심 참여자
- 회의 뒤 팔로업(결정 요약 · 할일 분배 · 문서 업데이트) 책임 보유
- 한/영 혼용 회의 빈도 높음
- 온프렘 · 프라이버시 민감 (정책 · 계약 이슈)

### 2차 후보
- **H1a. 리서치 중심 조직 (연구소 / AI팀)**: 긴 기술 토론, 과거 회의 참조 빈도 높음 → RAG 수요
- **H1b. 임원 / 리더십**: 다수 회의를 "대신 이해해달라" 수요 — 요약 · 이슈 · 위험 중심

## 3. Jobs-to-be-Done

회의 전 → 중 → 후 맥락별로 분리.

| 시점 | Job | 현재 해결 방식 | MeetMind 기회 |
|---|---|---|---|
| 전 | "지난번 맥락을 빠르게 복기하고 싶다" | 문서 검색 · 수동 정리 | 관련 회의 자동 요약 + 주요 결정 recall |
| 중 | "놓치지 않고 참여하고 싶다 (기록은 AI가)" | 수동 메모 | 실시간 전사 + 화자 라벨 + 자동 강조 |
| 후 | "결정 · 할일 · 이슈를 구조화해 공유하고 싶다" | 수동 정리 | 구조화된 이해 레이어 + 팔로업 답변 |

## 4. Positioning

> **"기록하는 도구"가 아니라 "회의를 이해하는 팀원"**

| 경쟁 | 포지셔닝 차이 |
|---|---|
| Otter / Fireflies | transcription + 기본 요약. MeetMind는 이해 · 추론 · 회의 간 연결. |
| Granola | 품질은 준수하나 클로즈드. MeetMind는 **오픈 모델 기반 프라이버시 · 온프렘 옵션**. |
| ChatGPT / Claude + 수동 기록 | 범용 LLM에는 회의 맥락 정합성(화자 / 시점 / 결정 연결) 부재. |

## 5. v1 Scope (가설)

**In scope**
1. 오디오 입력 (업로드 + 실시간) 양쪽 지원
2. STT(Whisper) + SD(pyannote) 정합 전사
3. 이해 레이어: 요약 · 결정사항 · 액션 아이템 · 이슈 / 블로커
4. 과거 회의 RAG (조직 단위)
5. 대화형 질의 ("누가 뭘 맡았지?")
6. 한국어 · 영어 양방향

**Out of scope (v1)**
- 화상회의 SaaS 자체(카메라/룸)
- 완전 오프라인 모바일
- 자체 태스크 / 캘린더 관리 (외부 연동으로 대체)
- 음성 합성(TTS) / 자동 응답

## 6. Success Metrics

**North Star (가설)**: **AEU (AI-Enriched Utilization)**
= (MeetMind로 처리된 회의 수) / (사용자가 참여한 전체 회의 수)

**Input metrics**
| 지표 | 목표 (v1) |
|---|---|
| 한국어 WER (도메인 세팅) | ≤ 15% |
| DER (Diarization Error Rate) | ≤ 15% |
| 요약 품질 (human judgment 4/5 이상) | p50 이상 |
| 액션아이템 precision / recall | ≥ 0.80 / ≥ 0.70 |
| 회의 후 7일 내 재열람률 | ≥ 40% |
| 산출물 공유율 | ≥ 50% |

**Guardrails**
- 요약 latency p95 ≤ 90초 (1시간 회의)
- 회의당 비용 목표: v0.2에서 확정
- 환각률 (summary faithfulness failure): < 3%

## 7. Risks & Assumptions

| # | 가설 / 위험 | 검증 방법 | 담당 |
|---|---|---|---|
| H1 | 1차 타겟이 PM / 리서치 리더 | 인터뷰 8–10건 + JTBD 일치도 | `user-researcher` |
| H2 | 한국어 Whisper + pyannote 품질 목표 달성 가능 | 30분 × 20개 회의 샘플 평가 | `asr-diarization-engineer`, `ml-eval-lead` |
| H3 | "화자–시점–결정" 정합이 핵심 가치 | 프로토타입 유저 피드백 | `ux-architect`, `reasoning-engineer` |
| H4 | 온프렘 / 프라이버시가 계약 전환 요인 | 타겟 5팀 인터뷰 | `product-strategist` |
| H5 | RAG(과거 회의 연결)가 재사용률 견인 | A/B — RAG on / off | `ml-eval-lead` |

## 8. Validation Plan (→ v0.2 진입 조건)

- `user-researcher`: H1 / H3 / H4 인터뷰 8–10건 (≤ 2주)
- `ml-eval-lead`: 한국어 20회 샘플 골든셋 + H2 초기 측정 (≤ 2주)
- `ai-architect` + `system-architect`: ADR-001 (모델 스택), ADR-002 (파이프라인 경계), ADR-003 (저장소) 초안

**Exit → v0.2**
- PRD가 H1–H3 피드백 반영하여 MINOR 버전업 (v0.2.0)
- ADR-001 ~ 003 accepted
- 평가셋 v1 스펙 확정

## 9. Open Questions

- 온프렘 옵션을 v1에 포함시킬 것인가, 별도 SKU로 분리할 것인가?
- 첫 배포 대상 언어: 한국어 단독 vs 한 / 영 동시?
- 실시간 전사 MVP 수준: streaming 필수 vs 후처리로 충분?
- 회의 간 RAG 범위: 개인 · 팀 · 조직 중 어디까지?

## 10. Related

- [00-overview.md](00-overview.md)
- [01-agent-team.md](01-agent-team.md)
- [02-lifecycle-plan.md](02-lifecycle-plan.md)

---

## Change Log (문서 내부)
- **v0.1.0** (2026-04-24): PRD v0 초안. 가설 H1–H5, 지표 초안, validation plan 포함.
