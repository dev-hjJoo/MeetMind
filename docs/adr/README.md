---
title: ADR Index
version: 0.1.0
status: active
created: 2026-04-24
updated: 2026-04-24
---

# Architecture Decision Records

이 디렉터리는 MeetMind의 기술·제품 주요 결정을 **Architecture Decision Record (ADR)** 형태로 남긴다.
템플릿과 규칙은 [`docs/VERSIONING.md`](../VERSIONING.md) 참고.

## Status

- **proposed**: 제안됨, 아직 수용되지 않음
- **accepted**: 합의되어 현재 유효
- **deprecated**: 더 이상 유효하지 않지만 대체 결정 없음
- **superseded**: 다른 ADR로 대체됨 (frontmatter `supersedes:` 필드 참조)

## Records

| # | Title | Status | Owners |
|---|---|---|---|
| [0001](0001-model-stack.md) | Speech & Reasoning Model Stack | proposed | `ai-architect`, `asr-diarization-engineer`, `reasoning-engineer` |

## 추가하기

1. 번호는 4자리 zero-padded (`0002`, `0003`, ...).
2. 파일명: `NNNN-kebab-case-title.md`.
3. 프론트매터의 `status`를 `proposed`로 시작해, 리뷰 후 `accepted`로 업데이트.
4. 이 인덱스 테이블에 한 줄 추가.
