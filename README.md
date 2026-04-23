# MeetMind

> 회의를 "이해하는 두뇌" — 오픈소스 AI 스택 기반 회의 이해 서비스

단순 전사가 아니라, 회의의 맥락과 의도를 **이해하고 기억하고 추론**하는 AI.

## Status
초기 기획 단계 · **v0.1.0** · 2026-04-24

## Documents
| 문서 | 내용 |
|---|---|
| [docs/00-overview.md](docs/00-overview.md) | 제품 개요 & 비전 |
| [docs/01-agent-team.md](docs/01-agent-team.md) | 에이전트 팀 구성 (11명) |
| [docs/02-lifecycle-plan.md](docs/02-lifecycle-plan.md) | 라이프사이클 계획 · 릴리스 로드맵 |
| [docs/VERSIONING.md](docs/VERSIONING.md) | 문서 버전 규칙 · 커밋 컨벤션 |
| [CHANGELOG.md](CHANGELOG.md) | 변경 이력 |

## Tech Stack (계획)
- **STT**: HuggingFace Whisper (large-v3 / faster-whisper)
- **Speaker Diarization**: pyannote.audio 3.x (+ WhisperX alignment 검토)
- **Reasoning**: LLM (오픈 모델 + Claude 혼합 라우팅)
- **App**: Next.js (App Router) + FastAPI · 실시간 전사 UI
- **Infra**: GitHub Actions, Terraform, GPU 컨테이너, OpenTelemetry

## Quick Start
TBD — 아키텍처 확정 후 v0.3에서 추가.

## License
[Apache License 2.0](LICENSE) · Copyright © 2026 Hyojin Joo
