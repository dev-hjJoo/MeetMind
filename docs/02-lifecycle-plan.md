---
title: Lifecycle Plan
version: 0.1.0
status: draft
created: 2026-04-24
updated: 2026-04-24
---

# 라이프사이클 계획

## 단계별 주도 / 참여 / 산출물

| 단계 | 주도 | 참여 | 산출물 | Exit Criteria |
|---|---|---|---|---|
| 0. Discovery | product-strategist, user-researcher | ai-architect | PRD v0, 페르소나, 차별화 가설 | 우선 페르소나 1개 + JTBD 확정 |
| 1. Concept | ux-architect, ai-architect | ui-designer, system-architect | 흐름 다이어그램, AI 능력 지도 | 핵심 흐름 3개 승인 |
| 2. Architecture | system-architect, ai-architect | platform-sre, ml-eval-lead | ADR, 데이터/모델 계약, 평가 스펙 | ADR-001 ~ 003 accepted |
| 3. Build | fullstack, asr-diarization, reasoning | ui-designer | End-to-end vertical slice | 실제 오디오 → 이해 결과 반환 |
| 4. Evaluate | ml-eval-lead, user-researcher | 전원 | 지표 보드, usability 결과 | WER/DER/요약 품질 목표 통과 |
| 5. Ship / Operate | platform-sre, fullstack | 전원 | 관측성 · 온콜 · 릴리스 노트 | SLO 정의 + 첫 배포 |

---

## 즉시 다음 스텝 (v0.1.0 → v0.2.0)
1. **`product-strategist`**: PRD v0 + 차별화 가설 초안 (≤ 2주)
2. 병렬로 **`ai-architect` + `system-architect`**: 각자 아키텍처 초안 (≤ 2주)
3. **`ml-eval-lead`**: Day-1 평가셋 스펙 (≤ 1주) — 위 둘 직후 착수

---

## 릴리스 로드맵 (잠정)

| 버전 | 내용 | 완료 조건 |
|---|---|---|
| **v0.1** | 문서 · 팀 구성 (현재) | 본 문서 세트 머지 |
| **v0.2** | PRD + 아키텍처 ADR 세트 | ADR-001~003 accepted |
| **v0.3** | ASR+SD 프로토타입 | 한국어 샘플 30분, WER/DER 목표치 도달 |
| **v0.4** | 이해 레이어 프로토타입 | 요약·결정·액션 추출 품질 평가 통과 |
| **v0.5** | End-to-end 알파 | 내부 dogfooding 1주 무중단 |

---

## 위험 & 대응 (초안)

| 위험 | 영향 | 완화 |
|---|---|---|
| 한국어 Whisper 품질 편차 | STT 핵심 지표 미달 | 도메인 파인튜닝 + 후처리 LM 병행 |
| pyannote 라이선스/운영 제약 | 배포 차단 가능 | 라이선스 선제 검토 + 대체 후보 확보 |
| 실시간 지연 | UX 치명 | 스트리밍 + 청킹 최적화, GPU 러너 예산 |
| 프라이버시 / PII | 규제·신뢰 리스크 | 온디바이스/온프렘 옵션 로드맵 명시 |
| 평가셋 부재 | AI 품질 판단 불가 | v0.2 단계에서 골든셋 구축 선행 |

---

## Change Log (문서 내부)
- **v0.1.0** (2026-04-24): 초안. 단계별 주도/산출물/Exit, 다음 스텝, 로드맵, 위험 정의.
