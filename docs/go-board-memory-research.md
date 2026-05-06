# Go Board Memory: 바둑판식 AI 컨텍스트 관리 연구 노트

작성일: 2026-05-06

## 1. 문제 정의

AI 코딩 에이전트로 긴 프로젝트를 진행하면 다음 문제가 반복된다.

1. 대화 컨텍스트가 커진다.
2. 컨텍스트 압축 또는 세션 전환이 발생한다.
3. AI가 이미 완료한 작업을 다시 하려 한다.
4. 기존 설계 결정을 잊고 다른 방향으로 진행한다.
5. 전체 프로젝트 문서를 계속 읽게 되면서 문서 자체가 다시 거대한 컨텍스트가 된다.

따라서 단순한 “요약”이나 “마인드맵”만으로는 충분하지 않다. 필요한 것은 AI가 매번 읽어야 하는 범위를 제한하는 운영 구조다.

## 2. 핵심 가설

Go Board Memory의 핵심 가설은 다음과 같다.

> 프로젝트 상태를 바둑판처럼 공간화하고, AI가 현재 좌표와 인접 좌표만 읽게 하면 장기 작업에서 컨텍스트 비대화를 줄일 수 있다.

이 방식은 정보를 무한히 압축하는 기술이 아니다. 대신 정보를 배치하고, 접근 범위를 제한하는 운영 모델이다.

## 3. 바둑 개념의 프로젝트 관리 대응

| 바둑 개념 | 프로젝트 관리 대응 |
|---|---|
| 돌 | 작업 노드, 기능, 결정, 테스트, 버그 |
| 좌표 | 작업 노드의 고정 위치 |
| 집 | 안정화된 모듈 영역 |
| 자유도 | 남은 의존성, 미해결 조건 |
| 단수 | 깨지기 쉬운 위험 상태 |
| 패 | 반복되면 안 되는 설계 논쟁 또는 금지 결정 |
| 세력 | 앞으로 확장 가능한 구조 |
| 악수 | 지금 하면 안 되는 작업 |
| 다음 수 | 현재 가장 가치 있는 단일 작업 |

## 4. 기본 구조

```text
docs/
  GO_BOARD.md
  BOARD_RULES.md
  CURRENT_STATE.md
  HANDOVER.md
  DECISIONS.md

board/
  A1_project_spine.md
  B1_config.md
  K7_execution_simulator.md
  K8_position_engine.md
  L7_fee_model.md

groups/
  market_data.md
  paper_trading.md
  strategy_engine.md
  dashboard.md

archive/
  old_moves/
  failed_variations/
  logs/
```

## 5. 컨텍스트 로딩 규칙

AI는 작업 시작 시 전체 저장소를 읽지 않는다.

기본적으로 읽을 것은 다음으로 제한한다.

1. `docs/GO_BOARD.md`
2. `docs/CURRENT_STATE.md`
3. `docs/HANDOVER.md`
4. 현재 좌표 파일
5. 현재 좌표의 직접 이웃 좌표 파일
6. 관련 그룹 요약 파일

읽지 말아야 할 것:

- 전체 board 디렉터리
- archive 디렉터리
- 오래된 작업 로그
- 완료된 안정 영역의 상세 기록
- 관련 없는 모듈 코드

## 6. 좌표 파일 예시

```markdown
# K7 - Execution Simulator

Status: IN_PROGRESS
Group: Paper Trading
Stone: ◐

Purpose:
Simulate market order fills for paper trading.

Neighbors:
- K6: Paper Order Model
- K8: Position Engine
- J7: Market Price Feed
- L7: Fee/Slippage Model

Liberties:
- Fee model test needed
- Slippage assumption needed
- Position update integration needed

Risks:
- If fill event schema changes, K8 may break.

Do Not:
- Do not implement real exchange orders here.
- Do not add strategy logic here.

Next Move:
- Add tests for market buy/sell fill with fee and slippage.

Done Criteria:
- Unit tests pass
- Integration test with K8 passes
- CURRENT_STATE.md updated
```

## 7. 패 규칙

패는 같은 논쟁이 반복되는 것을 막기 위한 장치다.

예시:

```markdown
KO-001: 실거래 API를 지금 구현할 것인가?

Current Ruling:
- NO
- Paper trading 먼저
- 실거래 API는 safety lock 이후 별도 노드

Ko Rule:
- 이 결정을 뒤집으려면 명시적 사용자 승인 필요
```

## 8. 단수 규칙

단수는 깨지기 쉬운 위험 상태다.

예시:

```markdown
ATARI: K8 Position Engine

Reason:
- Fill event schema가 바뀌면 paper trading integration test가 깨질 수 있음.

Rule:
- 수정 전 관련 테스트를 먼저 실행한다.
- 스키마 변경 시 K7, K8, L7을 함께 갱신한다.
```

## 9. 다음 수 선택 규칙

AI는 다음 작업 후보를 여러 개 제시할 수 있지만, 실제 실행은 하나만 한다.

평가 기준:

- 가치
- 위험
- 의존성 해소 효과
- 컨텍스트 비용
- 테스트 가능성
- 안정 영역 침범 여부

예시:

```text
Candidate Moves:

1. K7 체결 시뮬레이터 완성
   Value: High
   Risk: Medium
   Context Cost: Low
   Recommendation: Execute

2. N4 전략 엔진 시작
   Value: Medium
   Risk: High
   Context Cost: High
   Recommendation: Defer

3. A14 대시보드 디자인 개선
   Value: Low
   Risk: Low
   Context Cost: Medium
   Recommendation: Later
```

## 10. 기대 효과

- AI가 전체 프로젝트를 매번 다시 읽는 일을 줄인다.
- 이미 완료된 영역을 다시 설계하는 빈도를 줄인다.
- 반복되는 설계 논쟁을 `KO` 규칙으로 차단한다.
- 깨지기 쉬운 영역을 `ATARI`로 표시하여 수정 전 검증을 강제한다.
- 다음 작업을 “하나의 수”로 제한해 작업 범위가 퍼지는 것을 막는다.

## 11. 한계

- 이 방식은 압축 알고리즘이 아니다.
- 좌표 규칙이 없으면 오히려 혼란이 생길 수 있다.
- 문서를 계속 누적만 하면 다시 컨텍스트 문제가 생긴다.
- archive 격리와 active context 제한이 반드시 필요하다.
- 자동화 도구 없이 수동으로 관리하면 관리 비용이 생긴다.

## 12. 결론

Go Board Memory는 AI 장기 개발에서 컨텍스트를 직접 줄이는 기술이라기보다, 컨텍스트 접근 범위를 공간적으로 제한하는 운영 방식이다.

핵심은 다음 한 문장으로 요약할 수 있다.

> 기억을 많이 저장하는 것보다, 매번 적게 읽게 만드는 구조가 더 중요하다.
