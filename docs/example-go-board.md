# Example Go Board

## Legend

- ● DONE
- ○ TODO
- ◐ IN_PROGRESS
- ▲ BLOCKED
- ◆ RISK
- ✕ BAD_MOVE / DO_NOT_REPEAT
- ⚑ NEXT_MOVE

## Board Areas

| Area | Meaning |
|---|---|
| A1-F5 | Core Skeleton |
| G1-M5 | Market Data |
| N1-T5 | Strategy Engine |
| A6-H12 | Paper Trading |
| I6-T12 | Risk Management |
| A13-H19 | Dashboard |
| I13-T19 | Evaluation |

## Current Board

```text
A1 ● Project Spine        G2 ● Market Data Collector       N4 ○ Strategy Engine
B1 ● Config Loader        H2 ● Candle Storage              N5 ○ Signal Model
C1 ● Logger               I2 ◐ WebSocket Recovery          O5 ○ Entry Rules

A6 ● Paper Order Model    I6 ○ Risk Limits                 A13 ○ Dashboard Layout
K7 ⚑ Execution Simulator  J6 ○ Loss Guard                  B13 ○ Live Chart Panel
K8 ◆ Position Engine      K6 ● Order Schema                C13 ○ PnL Panel
L7 ◐ Fee/Slippage Model   L8 ○ Trade Log                   I13 ○ Evaluation Report
```

## Current Fight

- K7 Execution Simulator

## Next Move

- K7: finish fee/slippage tests

## Stable Territory

- A1 Project Spine
- B1 Config Loader
- C1 Logger
- G2 Market Data Collector
- H2 Candle Storage
- A6 Paper Order Model
- K6 Order Schema

## Atari Nodes

- K8 Position Engine
  - Reason: fill event schema changes may break integration.

## Ko Decisions

### KO-001: No Real Trading Yet

Current ruling:
- No real trading implementation until paper trading is verified.

### KO-002: Market Orders First

Current ruling:
- Market order simulation first.
- Limit order simulation later.
