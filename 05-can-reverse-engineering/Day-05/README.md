# Day 5 — Reverse Engineering the CAN Bus

## Source

Based on Chapter 5, **Reverse Engineering the CAN Bus**, of *The Car Hacker's Handbook*. The chapter covers locating CAN, analyzing traffic with can-utils and Wireshark, `candump`, grouping streamed data, record/playback, creative packet analysis, tachometer signal analysis, ICSim, OpenXC, translation, writing to CAN, fuzzing, and troubleshooting. urlOpen Garages — The Car Hacker's Handbookhttps://opengarages.org/handbook/ebook/

## Objective

Learn how to move from **raw CAN frames** to a defensible hypothesis about what an identifier or byte field represents.

The central rule is:

> **Observe first. Change one variable at a time. Compare captures. Form a hypothesis. Validate it in a safe simulator.**

## Learning Outcomes

By the end of Day 5 I should be able to:

- Capture and organize CAN traffic.
- Group frames by arbitration ID.
- Identify periodic versus event-driven messages.
- Compare two captures using a controlled-variable approach.
- Identify bytes that change when a synthetic signal changes.
- Distinguish correlation from proof.
- Document a candidate signal hypothesis.
- Explain why diagnostic PID values are not necessarily the same as raw application CAN signals.
- Understand the role of Wireshark, can-utils, record/playback, and ICSim.

## Reverse-Engineering Workflow

```text
Raw CAN traffic
      |
      v
Capture / Record
      |
      v
Group by CAN ID
      |
      v
Identify periodic IDs
      |
      v
Create baseline capture
      |
      v
Change ONE variable
      |
      v
Capture again
      |
      v
Diff IDs + bytes
      |
      v
Form signal hypothesis
      |
      v
Validate with another controlled capture
```

## Important Concepts

### Arbitration ID

The CAN identifier is useful for grouping and prioritizing messages, but it does not by itself tell us the application meaning of a frame.

### Periodic Messages

Many vehicle signals are transmitted repeatedly. A first-pass analysis should record:

- CAN ID
- approximate frequency
- payload length
- bytes that change
- bytes that remain constant

### Event-Driven Messages

Some messages appear mainly when an event occurs, such as a user interaction or state transition. These are often easier to isolate with before/after captures.

### Controlled Variables

A strong reverse-engineering experiment changes one observable variable at a time. For a simulator, examples include:

- synthetic vehicle speed
- synthetic RPM
- switch state
- indicator state

Do not assume a byte's meaning from one capture.

## Synthetic Dataset

This repository uses synthetic CAN traffic for the analysis exercise. It is deliberately not claimed to represent a real vehicle's signal database.

Example candidate speed frame:

```text
ID 0x180

Baseline:  00 00 00 00 00 00 00 00
Sample 1:  10 00 00 00 00 00 00 00
Sample 2:  20 00 00 00 00 00 00 00
Sample 3:  30 00 00 00 00 00 00 00
```

Initial hypothesis:

```text
ID 0x180, byte 0 changes with the synthetic speed variable.
```

This is a **hypothesis**, not a decoded production-vehicle signal.

## Candidate Signal Analysis

| ID | Byte | Baseline | Changed Samples | Candidate Meaning | Confidence |
|---|---:|---|---|---|---|
| 0x180 | 0 | 00 | 10, 20, 30 | Synthetic speed-related field | Medium |
| 0x181 | 2 | 55 | 55 | No observed change | Low |
| 0x182 | 0-1 | 12 34 | 12 34 | Constant field in sample | Low |

### Why confidence is only Medium

A changing byte establishes correlation with the experiment. It does not establish:

- physical units
- scaling
- signedness
- endianness
- whether the field is a counter or checksum
- whether another byte also participates

These require additional controlled experiments.

## Day 5 Lab — Virtual CAN Only

Use the `vcan0` environment from Day 3. Do not connect these exercises to a real vehicle.

### 1. Verify interface

```bash
ip -details link show vcan0
```

### 2. Start a capture

```bash
candump -L vcan0 > day5-capture.log
```

### 3. Generate synthetic periodic traffic

In a second terminal:

```bash
cansend vcan0 180#0000000000000000
cansend vcan0 180#1000000000000000
cansend vcan0 180#2000000000000000
cansend vcan0 180#3000000000000000
```

### 4. Group traffic by ID

```bash
candump -L vcan0
```

Use the saved capture to count occurrences of each identifier and inspect which payload bytes change.

### 5. Controlled comparison

Create two synthetic captures:

```text
baseline.log
changed.log
```

Compare them and record:

```text
ID -> changed byte(s) -> hypothesis -> confidence
```

## Wireshark Analysis

The book includes Wireshark as a CAN traffic analysis tool. citeturn3search2

For a virtual capture, the objective is to learn the workflow:

1. Capture traffic.
2. Open the capture in Wireshark where supported by the local setup.
3. Filter/group by CAN identifier.
4. Compare payload bytes.
5. Record observations without prematurely assigning meaning.

## Record and Playback

A useful reverse-engineering workflow separates **collection** from **analysis**.

```text
Live/synthetic traffic
        |
        v
     Record
        |
        v
      File
        |
        +------> Analyze repeatedly
        |
        +------> Replay in lab
```

This makes experiments repeatable and reduces the need to interact with a live target.

## ICSim Connection

The book includes the Instrument Cluster Simulator (ICSim) as a controlled environment for generating background CAN traffic and observing vehicle-like signals. citeturn3search2

For this 90-day project, ICSim should be treated as the preferred next-stage simulator before any physical vehicle experimentation.

## Security Perspective

CAN reverse engineering is security-relevant because the analyst may discover:

- unauthenticated message flows
- safety-relevant signals
- weak network segmentation
- predictable counters
- missing freshness protections
- messages accepted from unexpected network paths
- diagnostic/application traffic that is insufficiently isolated

A finding requires evidence and context. A message being easy to observe does not automatically mean that it is exploitable.

## Day 5 Deliverables

- [x] Chapter 5 study notes
- [x] CAN reverse-engineering methodology
- [x] Controlled-variable methodology
- [x] Synthetic CAN dataset
- [x] Candidate signal-analysis table
- [x] Virtual CAN lab procedure
- [x] Wireshark analysis workflow
- [x] Record/playback workflow
- [x] ICSim learning objective
- [x] Security analysis checklist
- [ ] Real `vcan0` capture evidence from local WSL
- [ ] Optional ICSim screenshot/evidence

## Interview Questions

1. What is CAN reverse engineering?
2. Why group CAN traffic by arbitration ID?
3. How would you identify a candidate signal?
4. Why should only one variable be changed during an experiment?
5. What is the difference between correlation and validation?
6. Why can a diagnostic RPM value differ from the raw CAN representation used by the instrument cluster?
7. What are periodic and event-driven CAN messages?
8. What is the purpose of record/playback during reverse engineering?
9. Why is a virtual CAN/ICSim environment safer than testing on a live vehicle?
10. What evidence would increase confidence in a decoded CAN signal?

## Safety

All Day 5 exercises are designed for `vcan0`, synthetic data, or a simulator. Do not replay, inject, or fuzz CAN traffic on a vehicle you are not explicitly authorized to test. The handbook itself warns that experimentation with vehicle networks and electronics can damage or disable a vehicle. citeturn0view0

## Status

**Day 5 theory + documentation: COMPLETE**

**Hands-on evidence: PENDING local WSL execution**
