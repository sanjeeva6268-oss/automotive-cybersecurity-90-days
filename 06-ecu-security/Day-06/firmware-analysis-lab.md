# Day 6 — Synthetic Firmware Analysis Lab

## Purpose

Practice the workflow used when analyzing an ECU firmware image without using proprietary OEM firmware.

## Lab Input

Create or obtain a small synthetic binary named:

```text
synthetic_ecu.bin
```

Do not use firmware extracted from a vehicle unless you have explicit authorization to possess and analyze it.

## Step 1 — Hash the Original

```bash
sha256sum synthetic_ecu.bin | tee synthetic_ecu.sha256
```

Record:

- Filename
- Size
- SHA-256
- Date/time
- Source

## Step 2 — Identify the File

```bash
file synthetic_ecu.bin
xxd -l 256 synthetic_ecu.bin
strings -a synthetic_ecu.bin | tee strings.txt
```

## Step 3 — Inspect Entropy

A high-level entropy review can help distinguish compressed/encrypted-looking regions from lower-entropy structured data. It is only a heuristic and does not prove encryption or compression.

## Step 4 — Map Candidate Regions

Document observations in a table:

| Offset | Observation | Hypothesis | Confidence |
|---|---|---|---|
| 0x0000 | Header-like bytes | Image metadata | Medium |
| 0x0100 | Repeated instruction-like pattern | Code region | Low/Medium |
| 0x0200 | Repeated numeric values | Calibration-like table | Low |
| 0x0300 | ASCII/config-like values | Configuration | Medium |

These offsets are a synthetic exercise and must not be interpreted as a real ECU memory map.

## Step 5 — Analysis Questions

1. Which bytes look like metadata?
2. Which regions have repeated structures?
3. Are there printable strings?
4. What architecture would be required to disassemble executable code?
5. Which observations are facts versus hypotheses?
6. What additional evidence would validate each hypothesis?

## Evidence Standard

Do not claim that a region is executable code simply because it looks like instructions. Record the evidence and confidence level.

```text
Observation → Hypothesis → Validation → Conclusion
```

## Optional Tools

For authorized lab firmware, common offline analysis tools include:

- `file`
- `strings`
- `xxd`
- `hexdump`
- Ghidra
- radare2
- objdump/binutils

The tool is less important than preserving evidence and documenting assumptions.

## Completion Checklist

- [ ] Synthetic binary created
- [ ] SHA-256 recorded
- [ ] File metadata recorded
- [ ] Hex dump captured
- [ ] Strings extracted
- [ ] Candidate regions documented
- [ ] Hypotheses separated from facts
- [ ] Analysis limitations documented

## Safety Boundary

This lab intentionally avoids real ECU firmware, proprietary seed-key algorithms, or modification of a production vehicle ECU.
