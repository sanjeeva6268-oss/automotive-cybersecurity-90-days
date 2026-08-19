# Day 5 — CAN Reverse Engineering Methodology

## 1. Capture

Start with passive observation in an authorized lab or simulator.

```bash
candump -L vcan0 > capture.log
```

Do not interpret payload bytes before establishing a baseline.

## 2. Group by Identifier

For each CAN ID, record:

| Field | Description |
|---|---|
| CAN ID | Arbitration identifier |
| Count | Number of observed frames |
| Frequency | Approximate transmission rate |
| DLC | Payload length |
| Changing bytes | Candidate signal locations |
| Constant bytes | Possible constants/reserved fields |

## 3. Baseline vs Changed Capture

Use two controlled captures:

```text
Capture A = baseline state
Capture B = one variable changed
```

Then compare:

```text
Changed IDs
    ↓
Changed bytes
    ↓
Bit-level changes
    ↓
Candidate signal
    ↓
Repeat experiment
```

## 4. Candidate Signal Checklist

For every candidate, investigate:

- Byte offset
- Bit offset
- Width
- Endianness
- Signed/unsigned representation
- Scaling factor
- Offset
- Update frequency
- Counter behavior
- Checksum behavior
- Relationship to the controlled variable

## 5. Example

Synthetic traffic:

```text
180#0000000000000000
180#1000000000000000
180#2000000000000000
180#3000000000000000
```

Observation:

```text
ID       = 0x180
Byte 0   = 00 → 10 → 20 → 30
```

Hypothesis:

```text
Byte 0 may encode a synthetic variable used by the lab.
```

Confidence remains **medium** until repeated experiments establish the relationship and rule out counters or other changing fields.

## 6. Why Reverse Engineering Matters

Raw CAN traffic normally lacks application-level names such as `vehicle_speed` or `engine_rpm`. Reverse engineering attempts to map low-level frames to higher-level behavior using observation and controlled experiments.

The handbook's Chapter 5 explicitly progresses from CAN capture and grouping through record/playback, creative packet analysis, tachometer analysis, ICSim, OpenXC, and fuzzing. urlOpen Garages — Chapter 5 sourcehttps://opengarages.org/handbook/ebook/

## 7. Evidence Standard

A portfolio-quality claim should contain:

```text
Experiment
+ Baseline
+ Changed condition
+ Capture
+ Diff
+ Hypothesis
+ Repeat validation
+ Confidence
```

Never label a signal as definitively decoded from a single observation.
