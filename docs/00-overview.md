---
title: MeetMind Product Overview
version: 0.1.0
status: draft
created: 2026-04-24
updated: 2026-04-24
---

# MeetMind — Product Overview

## One-liner
회의를 **이해하는 두뇌**. 단순 전사가 아니라, 맥락과 의도를 읽고 기억하고 추론하는 AI.

## 방향성
- **이해 중심**: 기록보다 "이해" — 요약·결정·액션·후속 질의까지 하나의 이해 레이어로.
- **확장성**: planning, reasoning, agentic follow-up으로 자연스럽게 확장.
- **오픈 AI 스택**: HuggingFace 기반 STT + Speaker Diarization 자체 구현 — 품질·비용·프라이버시 컨트롤 확보.

## 핵심 기능 (v1 범위 가설)
1. **실시간 / 업로드** 양쪽 오디오 입력
2. **STT(Whisper) + SD(pyannote)** 정합된 전사
3. **이해 레이어**: 요약 · 결정사항 · 액션아이템 · 이슈/블로커 추출
4. **회의 간 컨텍스트 연결** (RAG): "지난번 회의에서 결정한 X"
5. **대화형 질의**: "이 회의에서 누가 뭘 맡았지?"

## 비-목표 (초기 범위 아님)
- 화상회의 SaaS 자체(카메라/룸) 제공
- 완전 오프라인 모드 (v1 제외)
- 자체 캘린더/태스크 관리 (기존 툴 연동으로 대체)

## 차별화 가설
| 경쟁 | MeetMind 차별점 |
|---|---|
| Otter / Fireflies | "전사"가 아닌 "이해" — reasoning agent 포지셔닝 |
| Granola | 한국어 품질 + 오픈 모델 기반 온프렘/프라이버시 옵션 |
| 범용 LLM + 수동 기록 | 회의 전용 파이프라인의 정합성(화자·시점·결정 연결) |

## Open Questions → 담당 에이전트
| 질문 | 담당 |
|---|---|
| 타겟 페르소나 1순위 (리서치/제품/임원)? | `product-strategist`, `user-researcher` |
| 온프레미스 배포 옵션 제공 시점? | `system-architect`, `platform-sre` |
| 실시간 vs 후처리 품질 트레이드오프 기준? | `ai-architect`, `ml-eval-lead` |
| 다국어 우선순위 (한 → 영 → ?) | `product-strategist`, `asr-diarization-engineer` |

## Change Log (문서 내부)
- **v0.1.0** (2026-04-24): 초안. 비전·범위 가설·차별화·Open Questions 정의.
