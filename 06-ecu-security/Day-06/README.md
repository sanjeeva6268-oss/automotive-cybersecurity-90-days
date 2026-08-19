# Day 6 — ECU Hacking Fundamentals

## Source

Primary reading: *The Car Hacker's Handbook*, Chapter 6 — ECU Hacking, Open Garages edition.

The chapter organizes ECU attack vectors into three broad classes: **front door attacks**, **backdoor attacks**, and **exploits**. It then covers J2534, KWP2000, seed-key mechanisms, firmware reversing, ROM/data identification, and code analysis. citehttps://opengarages.org/handbook/ebook/

## Objective

Understand how an ECU can be assessed from its legitimate diagnostic/programming interface through to firmware analysis, while keeping all practical work inside an isolated laboratory or simulator.

## Learning Outcomes

By the end of Day 6, I should be able to:

- Explain the difference between front door, backdoor, and exploit-based ECU attack paths.
- Explain the purpose of J2534 at a high level.
- Explain where KWP2000 fits historically.
- Describe the purpose of a seed-key authentication exchange.
- Explain why seed-key implementations must be treated as security-sensitive.
- Describe a safe firmware-analysis workflow.
- Distinguish firmware code, configuration data, calibration data, and metadata.
- Explain why processor architecture identification matters before disassembly.
- Build a safe ECU research workflow without modifying a real vehicle.

## ECU Attack-Surface Model

```text
                 ECU SECURITY ASSESSMENT
                          |
          +---------------+---------------+
          |               |               |
      FRONT DOOR       BACKDOOR        EXPLOIT
          |               |               |
   Diagnostic /       Hardware /      Unintended
   programming        debug / memory  vulnerability
   interface          interfaces      |
          |               |            |
       J2534           ECU board      Bug
       KWP2000         memory         |
       UDS             debug          Impact
          |               |            |
          +---------------+------------+
                          |
                   Firmware / Data
                          |
                Static Analysis / RE
```

## 1. Front Door Attacks

A front-door assessment studies the access mechanisms intended by the manufacturer or service ecosystem.

Typical research questions:

- What diagnostic/programming interface is exposed?
- Which authentication or authorization step protects privileged operations?
- Which services are available before authentication?
- What operations can read configuration or firmware?
- What operations can change ECU state?
- Are security controls enforced consistently across sessions?

**Important:** availability of a diagnostic service is not itself a vulnerability. The security assessment must establish authorization, intended behavior, and impact.

## 2. J2534

J2534 is a standardized vehicle communication API intended to improve interoperability between diagnostic/programming software and compatible vehicle communication interfaces. The book describes J2534 as a front-door research path because observing legitimate programming/diagnostic transactions can reveal how OEM tooling communicates with ECUs. citehttps://opengarages.org/handbook/ebook/

### Safe research model

```text
OEM / Test Application
          |
       J2534 API
          |
   Pass-through Interface
          |
       CAN / Other Bus
          |
         ECU
```

For portfolio work, use a simulator, documented test ECU, or captured lab data rather than a production vehicle.

## 3. KWP2000

KWP2000 (ISO 14230) is an earlier automotive diagnostic/application protocol discussed in the chapter. It may operate over different underlying transport/physical mechanisms depending on the implementation. citehttps://opengarages.org/handbook/ebook/

The important Day 6 security lesson is to identify the **protocol layer**, the transport beneath it, and the authorization mechanisms around privileged diagnostic operations.

## 4. Seed-Key Authentication

A simplified conceptual exchange is:

```text
Tester                         ECU
  |                            |
  |------ Request Access ----->|
  |                            |
  |<--------- Seed ------------|
  |                            |
  |------ Key(seed) ---------->|
  |                            |
  |<------ Accept/Reject ------|
```

The book notes that seed-key algorithms are not universal and can vary in seed/key size and algorithm. citehttps://opengarages.org/handbook/ebook/

### Security questions

- Is the seed unpredictable?
- Is the key algorithm resistant to reverse engineering?
- Are failed attempts rate-limited?
- Is there lockout or delay behavior?
- Is the algorithm tied to a secure secret?
- Can authentication be replayed?
- Are privileged services unavailable without successful authentication?

This repository does **not** contain real OEM keys, proprietary algorithms, or instructions for bypassing a production ECU's security controls.

## 5. Backdoor Attack Concept

Backdoor research examines direct hardware paths that may bypass normal software interfaces, such as exposed debug or memory interfaces.

Conceptual workflow:

```text
ECU Board
   |
   +--> Identify MCU / SoC
   |
   +--> Identify Flash / EEPROM / RAM
   |
   +--> Identify debug interfaces
   |
   +--> Obtain authorized firmware image
   |
   +--> Hash and preserve original
   |
   +--> Analyze offline
```

Hardware-level techniques belong in a dedicated test bench. They should not be attempted on an installed production ECU without explicit authorization.

## 6. Firmware-Reversing Workflow

```text
Authorized Firmware Image
          |
          v
      SHA-256 Hash
          |
          v
   File Identification
          |
          v
 Architecture / MCU ID
          |
          v
  Strings / Sections / Data
          |
          v
      Disassembly
          |
          v
 Function Identification
          |
          v
 Data / Table Analysis
          |
          v
 Security-Relevant Findings
```

### Preserve before analysis

```text
original.bin
original.sha256
analysis-copy.bin
notes.md
```

Never overwrite the original evidence image during analysis.

## 7. Firmware vs Calibration Data

| Area | Typical purpose | Security relevance |
|---|---|---|
| Bootloader | Startup/programming | Secure boot and update trust |
| Application code | ECU behavior | Logic and vulnerability analysis |
| Calibration | Control parameters | Integrity and safety |
| Configuration | Vehicle/module settings | Unauthorized modification |
| Diagnostic routines | Service functions | Privileged operations |
| NVM data | Persistent state | Secrets, counters, configuration |

Actual memory layouts are ECU-specific and must be determined from authorized documentation or analysis.

## 8. Safe Portfolio Exercise

Use a **synthetic firmware image** rather than an OEM firmware image.

Create a small binary containing recognizable regions:

```text
Offset        Purpose
0x0000        Synthetic header
0x0100        Synthetic code region
0x0200        Calibration-like table
0x0300        Configuration-like data
0x0400        Padding
```

Record:

- SHA-256 hash
- File size
- Entropy observations
- Printable strings
- Candidate data regions
- Architecture assumption
- Analysis limitations

The objective is to practice the workflow, not to reverse a real ECU.

## Interview Questions

1. What are front door, backdoor, and exploit attack classes?
2. What is J2534?
3. Why is J2534 useful to automotive security researchers?
4. What is KWP2000?
5. What is a seed-key mechanism?
6. Why is predictable seed generation dangerous?
7. What is the difference between firmware and calibration data?
8. Why must the processor architecture be identified before disassembly?
9. Why should an original firmware image be hashed before analysis?
10. What is the difference between static firmware analysis and dynamic analysis?

## Day 6 Deliverables

- [x] Chapter 6 study notes
- [x] ECU attack-class model
- [x] J2534 explanation
- [x] KWP2000 explanation
- [x] Seed-key security model
- [x] Backdoor concept
- [x] Firmware reversing workflow
- [x] Synthetic firmware-analysis exercise
- [x] Interview questions
- [x] Safety boundary
- [ ] Real ECU firmware evidence — requires an authorized lab ECU/test bench

## Safety

Perform ECU research only on systems you own or are explicitly authorized to test. Prefer `vcan`, ECU simulators, synthetic firmware, and isolated test benches. Do not attempt unauthorized reprogramming, seed-key bypass, or firmware modification on a road vehicle.

## Status

**Day 6: COMPLETE — Theory + portfolio documentation**

Hands-on ECU hardware evidence remains a separate lab task and is intentionally not fabricated.
