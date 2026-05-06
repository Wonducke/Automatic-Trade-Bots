# Hermes Agent용 Go Board Memory 운영 프롬프트

아래 내용을 Hermes Agent의 프로젝트 운영 규칙 또는 SOUL.md 보조 규칙으로 사용할 수 있다.

```markdown
Use a Go Board Memory system for this project.

## Startup Rule

Before working:
1. Read docs/GO_BOARD.md.
2. Read docs/CURRENT_STATE.md.
3. Read docs/HANDOVER.md.
4. Identify the current coordinate.
5. Read only the current coordinate file, its direct neighbors, and the relevant group summary.

Do not read the whole project unless explicitly required.

## Board Model

- One coordinate equals one bounded task.
- A stone represents a task node.
- Stable territory means completed and tested module area.
- Liberties represent unresolved dependencies or missing completion conditions.
- Atari means fragile state that requires tests before modification.
- Ko decisions are design decisions that must not be reopened without explicit user approval.
- Bad moves are known failed approaches that must not be repeated.
- Every task must end with exactly one next move.

## Work Rule

During work:
1. Work on one coordinate only.
2. Do not expand scope unless the current coordinate requires it.
3. Do not redesign stable territory.
4. If another coordinate is needed, explain why and read only that coordinate.
5. If a node is in ATARI state, run or inspect related tests before modification.
6. If a KO decision is encountered, obey it unless the user explicitly reverses it.

## Completion Rule

A coordinate is DONE only when:
1. The intended implementation or document update exists.
2. Relevant tests exist or the reason for no tests is documented.
3. Tests pass or failures are explicitly recorded.
4. The coordinate file is updated.
5. docs/CURRENT_STATE.md is updated.
6. docs/HANDOVER.md is updated.
7. The next move is recorded.

## Context Budget Rule

Load only:
- Current board summary
- Current state
- Current handover
- Current coordinate
- Direct neighbors
- Relevant group summary
- Directly relevant source/test files

Do not load:
- All coordinate files
- All old logs
- Archive files
- Unrelated modules
- Completed node details unless debugging requires them

## End-of-Work Output

At the end of each task, report:
1. Current coordinate
2. Files changed
3. Tests run
4. Result
5. Updated board status
6. Next move
```
