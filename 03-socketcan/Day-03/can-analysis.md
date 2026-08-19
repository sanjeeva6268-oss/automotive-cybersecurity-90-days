# Day 3 — Synthetic CAN Analysis

## Dataset

The following frames are intentionally synthetic and are suitable for `vcan0` testing:

```text
100#0100000000000000
100#0200000000000000
100#0300000000000000
200#AABBCCDD
200#AABBCCEE
```

## Observations

| ID | Frame sequence | Observation |
|---|---|---|
| 0x100 | 01 → 02 → 03 in byte 0 | Byte 0 changes while remaining bytes stay constant |
| 0x200 | `AABBCCDD` → `AABBCCEE` | Final byte changes from DD to EE |

## What We Can Infer

- `0x100` has a changing first payload byte.
- `0x200` has a changing final payload byte.
- These IDs and payloads can be filtered and observed with SocketCAN tools.

## What We Cannot Infer Yet

We cannot determine the real-world meaning of a signal from this dataset alone.

For example, we should **not** claim that byte 0 is vehicle speed or RPM without additional evidence.

Signal reverse engineering requires controlled experiments, timing analysis, correlation with known vehicle state, and/or authoritative signal definitions.

## Security Relevance

A security researcher first establishes what traffic exists and how it changes. Later CAN reverse-engineering work can investigate whether messages correspond to safety-relevant or security-sensitive functions.

## Example Capture Workflow

```bash
candump vcan0
```

In another terminal:

```bash
cansend vcan0 100#0100000000000000
cansend vcan0 100#0200000000000000
cansend vcan0 100#0300000000000000
cansend vcan0 200#AABBCCDD
cansend vcan0 200#AABBCCEE
```

## Analysis Checklist

- [ ] Identify unique CAN IDs
- [ ] Record DLC for each ID
- [ ] Compare payloads byte-by-byte
- [ ] Identify changing bytes
- [ ] Check whether changes are periodic or event-driven
- [ ] Record timestamps
- [ ] Avoid assigning signal meaning without evidence
- [ ] Keep captures synthetic or authorized
