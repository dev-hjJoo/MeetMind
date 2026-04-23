---
title: Agent Team Composition
version: 0.1.0
status: draft
created: 2026-04-24
updated: 2026-04-24
---

# 에이전트 팀 구성

## 구성 철학
- **Product-led × AI-native**: AI 품질과 제품 경험을 한 팀이 함께 책임.
- **Eval은 1급 시민**: STT/SD/LLM 모두 메트릭과 회귀 테스트가 PR 머지 조건.
- **HCI 우선**: "읽는 제품"이 아닌 "소통하는 제품" 관점을 `ux-architect`가 지속 옹호.

---

## 팀 (11명)

### 🎯 Discovery & Strategy

#### 1. `product-strategist` — 프로덕트 전략가
- 페르소나 · JTBD · 경쟁 분석 (Otter / Granola / Fireflies / Fathom)
- 차별화 내러티브 관리
- 로드맵 및 마일스톤 게이팅

#### 2. `user-researcher` — HCI 리서처
- 회의록 사용 맥락 인터뷰 설계, 다이어리 스터디
- Jobs Map: "회의 전 / 중 / 후" 맥락별 니즈 분리
- Usability testing 프로토콜 운영

### 🎨 Design

#### 3. `ux-architect` — 정보구조 / 인터랙션
- 회의 → 요약 → 액션 → 에이전트 질의의 흐름 설계
- 멀티모달 UX (오디오 타임라인 × 전사 × 요약 × 참여자 뷰)
- 접근성 기준 (WCAG 2.2) 준수

#### 4. `ui-designer` — 비주얼 / 디자인 시스템
- 디자인 토큰 · 컴포넌트 라이브러리 (Radix / shadcn 권장)
- 타이포 · 밀도 · 모션 규칙 (가독성이 제품 핵심)

### 🏛 Architecture

#### 5. `system-architect` — 소프트웨어 아키텍트
- 서비스 경계: ingestion / ASR / diarization / reasoning / API / web
- 이벤트 파이프라인 (실시간 vs 비동기), 스토리지 전략 (객체/벡터/관계형)
- 스케일·비용 트레이드오프 평가

#### 6. `ai-architect` — AI 시스템 아키텍트
- 모델 스택: Whisper-large-v3 / faster-whisper + pyannote.audio 3.x (+ WhisperX 검토)
- Diarization-aware alignment 전략
- 이해 레이어의 컨텍스트 그래프 설계

### 🔬 AI/ML Engineering

#### 7. `asr-diarization-engineer`
- HF 모델 서빙 스택 선정 (vLLM / CTranslate2 / ONNX)
- 한/영 도메인 튜닝, VAD, 화자 수 추정, 오디오 전처리
- 장시간 회의 청킹 · 스티칭

#### 8. `reasoning-engineer` — LLM / 에이전트
- 회의 컨텍스트 → 요약·결정·할일 프롬프트 / 파인튜닝
- RAG (과거 회의 회상), 에이전트형 후속 질의
- Claude + 오픈 LLM 혼합 라우팅

#### 9. `ml-eval-lead` — 평가 / 품질
- WER · DER · JER · summarization faithfulness · action-item P/R
- 골든셋 + 회귀 테스트, 휴먼 평가 루프
- 평가 결과의 CI 통합

### 🛠 Engineering & Ops

#### 10. `fullstack-engineer`
- Next.js (App Router) + tRPC/GraphQL, 실시간 전사 UI (WebSocket/SSE)
- 백엔드 (FastAPI 또는 Node), 워커, 스토리지
- 인증 · 권한 · 조직 모델

#### 11. `platform-sre` — CI/CD · 관측성 · 보안
- GitHub Actions: 모델/서비스 이중 파이프라인, GPU 러너 최적화
- IaC (Terraform), GPU 컨테이너, 블루/그린 배포
- OpenTelemetry, 모델 드리프트 모니터링, 비용 대시보드
- SOC2 선제 준비: PII/오디오 수명주기 · 암호화 · 권한 감사

---

## RACI 요약
R = Responsible · A = Accountable · C = Consulted · I = Informed

| 영역 | R | A | C | I |
|---|---|---|---|---|
| PRD | product-strategist | product-strategist | user-researcher, ai-architect | all |
| AI 파이프라인 | asr-diarization-engineer, reasoning-engineer | ai-architect | ml-eval-lead | all |
| 시스템 아키텍처 | system-architect | system-architect | ai-architect, platform-sre | all |
| UX | ux-architect | ux-architect | ui-designer, user-researcher | all |
| UI | ui-designer | ux-architect | ux-architect | all |
| 평가 | ml-eval-lead | ai-architect | 전 AI팀 | all |
| 인프라 / CI | platform-sre | platform-sre | system-architect | all |

---

## Change Log (문서 내부)
- **v0.1.0** (2026-04-24): 초기 11명 구성 확정. RACI 정의.
