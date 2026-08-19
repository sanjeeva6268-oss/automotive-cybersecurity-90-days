# Day 4 — Diagnostic Reference

## OBD-II Quick Reference

| Mode | Function | Example |
|---|---|---|
| `01` | Current data | PID `0C` = RPM |
| `02` | Freeze frame | Snapshot associated with fault |
| `03` | Stored DTCs | Read confirmed codes |
| `04` | Clear DTCs | Clear diagnostic information |
| `07` | Pending DTCs | Read pending codes |
| `08` | Control onboard systems | Manufacturer-dependent capabilities |
| `09` | Vehicle information | VIN and other vehicle information |
| `0A` | Permanent DTCs | Read permanent codes |

## UDS Quick Reference

| SID | Service | Security relevance |
|---|---|---|
| `10` | Diagnostic Session Control | Changes diagnostic context |
| `11` | ECU Reset | Can change ECU state |
| `14` | Clear Diagnostic Information | Alters fault memory |
| `22` | Read Data By Identifier | Can expose ECU data |
| `27` | Security Access | Authentication-related mechanism |
| `2E` | Write Data By Identifier | Can modify supported data |
| `34` | Request Download | Firmware/software transfer path |
| `36` | Transfer Data | Transfers download data |
| `37` | Request Transfer Exit | Ends transfer sequence |
| `3E` | Tester Present | Maintains diagnostic presence |

## Example Diagnostic Flow

```text
Tester
  |
  | Diagnostic request
  v
Gateway / Diagnostic Router
  |
  | Addressed diagnostic traffic
  v
ISO-TP
  |
  v
UDS Service
  |
  v
ECU
  |
  | Positive / negative response
  v
Tester
```

## Security Review Checklist

- [ ] Is the diagnostic endpoint externally reachable?
- [ ] Is the diagnostic path isolated by a gateway?
- [ ] Are sessions authenticated or otherwise access-controlled?
- [ ] Are privileged services restricted?
- [ ] Is security access enforced before sensitive operations?
- [ ] Are failed access attempts monitored?
- [ ] Are firmware-transfer functions protected?
- [ ] Are diagnostic events logged?
- [ ] Is diagnostic access disabled or restricted when not required?

## Important Limitation

Service identifiers and behavior alone do not establish a vulnerability. Security assessment requires understanding the ECU's implementation, gateway policy, authentication, authorization and operational context.
