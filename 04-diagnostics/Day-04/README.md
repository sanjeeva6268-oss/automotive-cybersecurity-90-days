# Day 4 — Diagnostics and Logging

## Source

Study based on Chapter 4, **Diagnostics and Logging**, of *The Car Hacker's Handbook* by Craig Smith, using the Open Garages online edition.

The chapter introduces Diagnostic Trouble Codes (DTCs), diagnostic services, Unified Diagnostic Services (UDS), ISO-TP over CAN, modes and PIDs, diagnostic state, Event Data Recorder (EDR) logging, SAE J1698, J2534, KWP2000, seed-key security, and firmware-oriented diagnostic attack surfaces.

## Objective

Understand how automotive diagnostic systems expose information and privileged functions, and learn to analyze diagnostic traffic safely using a virtual CAN environment.

## Learning Outcomes

By the end of Day 4 I should be able to:

- Explain what a DTC is.
- Read the structure of a standard OBD-II DTC.
- Explain the relationship between OBD-II, CAN and ISO-TP.
- Distinguish OBD-II service modes from UDS services.
- Explain PIDs and diagnostic requests/responses.
- Understand why diagnostic sessions are security-sensitive.
- Identify authentication/authorization boundaries around diagnostic services.
- Explain the purpose of Tester Present.
- Describe diagnostic logging and EDR at a high level.
- Identify seed-key authentication as a security control that requires careful assessment.

## 1. Diagnostic Trouble Codes (DTCs)

A Diagnostic Trouble Code represents a detected fault condition. Standard five-character DTCs use a leading letter to identify the broad system category:

| Prefix | Area |
|---|---|
| P | Powertrain |
| B | Body |
| C | Chassis |
| U | Network / communication |

Examples from the chapter include `P0477` and `U0151`.

### Security relevance

DTCs can disclose information about affected components and communication failures. During authorized research they can therefore become useful diagnostic telemetry, but they should not be treated as proof of exploitability.

## 2. OBD-II Modes and PIDs

Common OBD-II service modes discussed in the chapter include:

| Mode | Purpose |
|---|---|
| `0x01` | Current data |
| `0x02` | Freeze-frame data |
| `0x03` | Stored DTCs |
| `0x04` | Clear DTCs / diagnostic history |
| `0x07` | Pending DTCs |
| `0x08` | Control onboard systems/components |
| `0x09` | Vehicle information |
| `0x0A` | Permanent DTCs |

Useful standard PIDs include:

| PID | Meaning |
|---|---|
| `0x00` | Supported PIDs |
| `0x05` | Engine coolant temperature |
| `0x0C` | Engine RPM |
| `0x0D` | Vehicle speed |
| `0x1C` | OBD standards supported |
| `0x1F` | Runtime since engine start |

## 3. UDS

Unified Diagnostic Services (UDS) is a diagnostic protocol standardized by ISO 14229. It provides services beyond basic OBD-II data retrieval.

Important examples include:

| Service | Meaning |
|---|---|
| `0x10` | Diagnostic Session Control |
| `0x11` | ECU Reset |
| `0x14` | Clear Diagnostic Information |
| `0x22` | Read Data By Identifier |
| `0x27` | Security Access |
| `0x2E` | Write Data By Identifier |
| `0x34` | Request Download |
| `0x36` | Transfer Data |
| `0x37` | Request Transfer Exit |
| `0x3E` | Tester Present |

These services can be highly privileged. Actual service availability, identifiers, security mechanisms and permissions depend on the ECU implementation.

## 4. ISO-TP and CAN

ISO-TP (ISO 15765-2) allows diagnostic messages larger than the basic CAN payload to be transported across multiple CAN frames.

Conceptually:

```text
Diagnostic Application
        |
      UDS
        |
     ISO-TP
        |
       CAN
        |
   Vehicle Network
```

ISO-TP is therefore an important bridge between application-level diagnostics and CAN transport.

## 5. Diagnostic State

A diagnostic ECU may operate differently depending on its current diagnostic session. A Tester Present service (`0x3E`) can be used by legitimate diagnostic tooling to indicate that a tester remains active.

Security questions:

- Who can enter a diagnostic session?
- Which services are available in each session?
- Is authentication required?
- Is security access enforced before privileged operations?
- Are diagnostic requests rate-limited?
- Are attempts logged?
- Can an external interface reach the diagnostic ECU?

## 6. Diagnostic Attack Surface

```text
External / Physical Interface
          |
       Gateway
          |
   Diagnostic Addressing
          |
       ISO-TP
          |
         UDS
          |
   +------+------+
   |             |
 Read Data    Privileged Service
   |             |
 ECU Data    Configuration/Firmware
```

The security boundary is not simply the OBD connector. The complete path from an external interface through a gateway and diagnostic stack to an ECU should be analyzed.

## 7. Safe Virtual-CAN Exercise

Use only `vcan0` or another explicitly authorized laboratory environment.

Example synthetic diagnostic request:

```text
cansend vcan0 7DF#0201050000000000
```

This is a lab packet representing an OBD-style request. Do not assume that a response exists on a virtual bus unless a simulated ECU is configured to provide one.

For a virtual diagnostic exercise, the goal is to observe the transport and application layers rather than interact with a real vehicle.

Capture traffic with:

```text
candump vcan0
```

## 8. Synthetic Diagnostic Dataset

| CAN ID | DLC | Data | Interpretation |
|---|---:|---|---|
| `0x7DF` | 8 | `02 01 05 00 00 00 00 00` | OBD-style request for PID `0x05` |
| `0x7DF` | 8 | `02 01 0C 00 00 00 00 00` | OBD-style request for PID `0x0C` |
| `0x7DF` | 8 | `02 01 0D 00 00 00 00 00` | OBD-style request for PID `0x0D` |

These are **synthetic examples for analysis**, not captured vehicle traffic.

## 9. Security Analysis

Diagnostic systems can become high-impact attack surfaces because they may provide access to:

- ECU information
- Configuration data
- Fault memory
- Diagnostic routines
- Actuator controls
- Software download/transfer functions
- Security access mechanisms

A secure design should consider:

- Network segmentation
- Gateway filtering
- Authentication
- Authorization
- Security access
- Secure diagnostics
- Rate limiting
- Logging and monitoring
- Secure boot and firmware validation
- Controlled service access

## 10. Day 4 Questions

1. What is a DTC?
2. What is the difference between OBD-II and UDS?
3. Why is ISO-TP useful for diagnostics?
4. What is a PID?
5. What does UDS service `0x22` do?
6. Why is service `0x27` security-sensitive?
7. What is Tester Present (`0x3E`)?
8. Why should diagnostic traffic be segmented from untrusted networks?
9. Why are firmware download services sensitive?
10. Why should diagnostic testing begin in a virtual or bench environment?

## Deliverables

- [x] Chapter 4 study notes
- [x] DTC structure
- [x] OBD-II modes and PIDs
- [x] UDS service map
- [x] ISO-TP relationship to CAN
- [x] Diagnostic attack-surface model
- [x] Synthetic diagnostic traffic dataset
- [x] Security analysis
- [x] Safe virtual-CAN exercise
- [ ] Capture actual `vcan0` terminal evidence
- [ ] Build a simulated diagnostic ECU response

## Safety

All practical testing must use an owned vehicle, authorized test bench, emulator, or virtual CAN environment. Do not send diagnostic or control messages to vehicles or systems without explicit authorization.

## Status

**Day 4 theory/documentation: COMPLETE**

**Day 4 hands-on evidence: PENDING local lab execution**
