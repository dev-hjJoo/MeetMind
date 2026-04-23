---
title: ADR-0001 — Speech & Reasoning Model Stack
status: proposed
date: 2026-04-24
deciders: [ai-architect, asr-diarization-engineer, reasoning-engineer]
---

# ADR-0001 — Speech & Reasoning Model Stack

## Context

MeetMind의 v1 파이프라인은 네 계층으로 구성된다:

1. **Speech-to-Text (STT)** — 한국어 + 영어, 최대 ~2시간 회의, 다수 화자.
2. **Speaker Diarization (SD)** — 가변 화자 수, 겹침 / 짧은 턴에 강건.
3. **Transcript–Diarization Alignment** — 단어 단위(word-level) 화자 라벨.
4. **Reasoning Layer** — 요약, 결정·액션 추출, RAG 기반 후속 질의.

PRD v0의 제약 (`docs/03-prd-v0.md`, H2 · H4):

- **자체 호스팅 / 온프렘 가능**할 것 (규제 고객 대응).
- **한국어 WER** ≤ 15% (도메인 튜닝 후).
- **DER** ≤ 15%.
- **요약 latency p95** ≤ 90초 (1시간 회의 기준).
- 예산은 v0.2에서 확정하되, **CPU/GPU 유연성**과 **양자화**가 가능할 것.

## Decision

### STT — faster-whisper (Whisper large-v3, CTranslate2 runtime)
- 레포: https://github.com/SYSTRAN/faster-whisper
- 동일 GPU에서 레퍼런스 Whisper 대비 ~4× 처리량, 비슷한 WER.
- `int8`, `int8_float16` 양자화로 CPU/저메모리 배포 경로 확보.

### Speaker Diarization — pyannote.audio 3.x
- 레포: https://github.com/pyannote/pyannote-audio
- 프리트레인 파이프라인 `pyannote/speaker-diarization-3.1` 사용.
- 라이선스: BSD-2 (코드) / 모델 카드상 연구·상업 사용 허용. 첫 실행 시 HF 토큰 필요(운영상 불편 정도).

### Alignment — WhisperX 포싱 얼라인먼트
- 레포: https://github.com/m-bain/whisperx
- Whisper 세그먼트 + pyannote 턴을 단어 단위로 정합.
- 오픈소스 커뮤니티의 표준 레시피 — 글루 리스크 최소.

### Reasoning (LLM) — 티어 라우팅
- **Default**: **Claude Sonnet 4.6** (Anthropic API) — 요약, 결정/액션 추출, 복잡 질의.
- **Self-hosted fallback**: `Llama-3.1-70B-Instruct` 또는 `Qwen2.5-72B-Instruct` on vLLM — 풀 온프렘 고객용.
- 라우팅 규칙은 ADR-002 (파이프라인 경계)에서 확정.

### Embeddings (RAG) — multilingual-e5-large-instruct
- 모델: `intfloat/multilingual-e5-large-instruct`
- 라이선스: MIT. 한국어 + 영어 모두 강건. Apache-2.0 스택에 호환.

## Consequences

### 장점
- 네 구성 요소 모두 **permissive 라이선스** (MIT / MIT / BSD-2 / Apache-2.0) — 본 프로젝트(Apache-2.0)와 정합.
- **faster-whisper**: 처리량·메모리·양자화 유연성 모두 우위.
- **pyannote + WhisperX**: 커뮤니티 검증 레시피 → 적은 integration 리스크.
- **Claude Sonnet 4.6 default + 온프렘 fallback**: 품질과 배포 옵션을 동시에 확보 (H4 대응).
- **한국어 대응**: Whisper large-v3가 바로 쓸 만한 한국어 품질 → 도메인 LM 리스코어로 WER ≤ 15% 도달 가능성 높음.

### 단점 / 트레이드오프
- pyannote 첫 다운로드 시 HF 토큰 필수 — 온프렘 환경에서 오프라인 부트스트랩 레시피 필요.
- ASR + SD + alignment 세 구성의 글루 코드 ownership → `asr-diarization-engineer`.
- Default 경로의 **Claude API 의존**은 비용·PII 처리 고려 필요. **온프렘 fallback은 일등 시민**으로 유지 (차선이 아닌 동등 대안).
- Whisper large-v3 fp16 기준 VRAM ~5–10 GB → GPU 클래스가 비용에 직접 영향.

### Open Follow-ups
- 한국어 골든셋 20건 벤치마크 (`ml-eval-lead`, v0.3 전).
- CPU 티어의 기본 양자화 설정 결정.
- WhisperX 대신 경량 in-house aligner 필요성 재평가.
- 온프렘 LLM의 정확도 격차 측정 (Claude vs Llama/Qwen) — 라우팅 정책 캘리브레이션.

## Alternatives Considered

| Alternative | 기각 사유 |
|---|---|
| Reference OpenAI Whisper (PyTorch) | 느림 + 메모리↑ + 양자화 어려움. large-v3 기준 품질 우위 없음. |
| NVIDIA NeMo (Parakeet/Canary) | 영어 강함, **한국어 레시피 미성숙**. 한국어 품질 정체 시 재검토. |
| Deepgram / AssemblyAI (SaaS) | DX·품질 모두 우수하나 **온프렘 제약 위배 (H4)**. 후일 "managed" 티어 후보. |
| wav2vec2 + custom diarizer | 저수준 · 공수↑ · 장시간 품질 열위. |
| Gemini / GPT-4o end-to-end audio | 다화자 장시간 다이어라이즈 미성숙, 벤더 락인, 온프렘 불가. |
| LLM 계층을 오픈모델 단독 | 복잡 추론 품질 격차 여전. Claude default + fallback이 현실적. |

## Related

- PRD v0 `docs/03-prd-v0.md` (H2 한국어 품질, H4 온프렘)
- Overview `docs/00-overview.md` (모델 스택 기술 초안)
- ADR-0002 (pipeline boundaries) — *예정*
- ADR-0003 (storage strategy) — *예정*

## Change Log
- **2026-04-24**: Initial proposal (v0.1.0).
