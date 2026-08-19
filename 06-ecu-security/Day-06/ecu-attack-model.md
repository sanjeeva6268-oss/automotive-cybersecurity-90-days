# Day 6 — ECU Attack Model

## Chapter 6 Mapping

The Open Garages chapter divides ECU attack vectors into three classes: front door attacks, backdoor attacks, and exploits. citehttps://opengarages.org/handbook/ebook/

| Class | Entry point | Research objective | Example defensive question |
|---|---|---|---|
| Front door | Diagnostic/programming interface | Understand intended access path | Are privileged services strongly authenticated? |
| Backdoor | Hardware/debug/memory interface | Identify unintended or direct access | Are debug interfaces disabled or access-controlled? |
| Exploit | Software/logic flaw | Identify unintended behavior | Can malformed input cross a security boundary? |

## Security Boundaries

```text
External Tester
      |
      v
Diagnostic Interface
      |
      v
Authentication / Session Control
      |
      v
Diagnostic Services
      |
      v
Bootloader / Application
      |
      v
Persistent Memory / Vehicle Functions
```

A strong ECU security design should limit privileges at each transition rather than relying on one control.

## Assessment Checklist

### Identification

- [ ] ECU role documented
- [ ] MCU/SoC identified
- [ ] Communication interfaces documented
- [ ] Firmware provenance documented
- [ ] Test authorization documented

### Diagnostic Interface

- [ ] Services enumerated in the lab
- [ ] Session transitions documented
- [ ] Authentication requirements documented
- [ ] Failed-authentication behavior documented
- [ ] Privileged operations identified

### Firmware

- [ ] Original image hashed
- [ ] Architecture identified
- [ ] Sections/regions mapped
- [ ] Strings extracted
- [ ] Functions identified
- [ ] Security-relevant routines documented

### Hardware

- [ ] Board photographed/documented
- [ ] MCU identified
- [ ] Nonvolatile memory identified
- [ ] Debug/test points documented
- [ ] Debug access policy recorded

## Portfolio Finding Template

```text
Finding ID:
Target:
Interface:
Precondition:
Observed behavior:
Expected behavior:
Security boundary crossed:
Potential impact:
Evidence:
Confidence:
Recommended control:
```

## Day 6 Takeaway

The key lesson is that ECU security is broader than CAN traffic. An ECU is a computing platform with diagnostic interfaces, programming paths, firmware, persistent data, and potentially hardware-level interfaces. A useful assessment therefore maps the complete path from interface to privilege to code/data to vehicle function.
