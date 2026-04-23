---
title: Documentation Versioning Conventions
version: 0.1.0
status: active
created: 2026-04-24
updated: 2026-04-24
---

# 문서 버전 규칙

## 목적
MeetMind의 모든 문서(`docs/**`)가 코드와 동일한 품질로 버전 관리되도록 규칙을 정의한다.
문서도 **하나의 산출물**이며, 리뷰·릴리스·태깅의 대상이다.

## 1. 문서 Frontmatter
모든 `docs/**/*.md`는 다음 YAML frontmatter를 상단에 포함한다.

```yaml
---
title: 문서 제목
version: 0.1.0           # SemVer
status: draft            # draft | active | deprecated | superseded
created: 2026-04-24      # 최초 작성일 (YYYY-MM-DD)
updated: 2026-04-24      # 마지막 수정일
owner: (이름/에이전트)    # 선택
supersedes: (경로)       # 이전 문서 대체 시
---
```

## 2. 문서 SemVer 기준
| 변경 유형 | 버전 증가 | 예시 |
|---|---|---|
| 핵심 결정/구조 뒤집힘 | **MAJOR** (1.x → 2.0.0) | 팀 전면 재구성, 아키텍처 방향 전환 |
| 새 섹션 추가, 의미 있는 보강 | **MINOR** (0.1 → 0.2.0) | 새 에이전트 추가, RACI 확장 |
| 오타, 서식, 명확화 | **PATCH** (0.1.0 → 0.1.1) | 표현 다듬기 |

문서 내부 변경은 frontmatter의 `version` + `updated`를 함께 갱신하고,
해당 문서 하단의 "Change Log" 섹션에도 한 줄 기록한다.

## 3. 루트 CHANGELOG.md
- [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) 형식.
- 리포지토리(전체) 릴리스 버전을 따른다 (문서+코드 통합 태그).
- 각 항목에 해당 파일과 개별 버전 표기: `docs/01-agent-team.md → 0.2.0`.

## 4. 커밋 컨벤션
[Conventional Commits](https://www.conventionalcommits.org/) 기반.

```
<type>(<scope>): <subject>

<body>

<footer>
```

| type | 용도 |
|---|---|
| `docs` | 문서 변경 |
| `feat` | 새 기능 |
| `fix` | 버그 수정 |
| `refactor` | 구조 개선 |
| `test` | 테스트 |
| `chore` | 빌드/도구/의존성 |
| `ci` | CI/CD 변경 |

**예시**
- `docs(agent-team): bump to 0.2.0 — add observability lead`
- `docs(overview): patch typo in vision statement`
- `feat(asr): add faster-whisper streaming pipeline`

## 5. 브랜치 & PR
- `main`: 안정 / 태깅 기준.
- 기능 브랜치: `feat/<slug>`, 문서 브랜치: `docs/<slug>`.
- 문서 변경도 PR 리뷰 대상(최소 1인).
- 큰 결정은 **ADR 초안 → 합의 → 본 문서 반영** 순서.

## 6. ADR (Architecture Decision Records)
중요한 기술/제품 결정은 `docs/adr/NNNN-title.md`로 남긴다.

**템플릿**
```markdown
---
title: ADR-NNNN - 결정 제목
status: proposed | accepted | deprecated | superseded
date: YYYY-MM-DD
deciders: [이름/에이전트]
---

## Context
어떤 문제/상황인가.

## Decision
무엇을 선택했는가.

## Consequences
긍정/부정 결과, 트레이드오프.

## Alternatives Considered
다른 옵션과 기각 사유.
```

## 7. 릴리스 태깅
- 리포 전체 릴리스는 `v0.1.0`처럼 태그.
- 태그 메시지에 해당 버전의 CHANGELOG 발췌 포함.
- 릴리스 시점에 모든 `active` 문서의 `updated` 필드를 확인.

## Change Log (문서 내부)
- **v0.1.0** (2026-04-24): 초기 버전. Frontmatter/SemVer/CHANGELOG/커밋/ADR 규칙 확정.
