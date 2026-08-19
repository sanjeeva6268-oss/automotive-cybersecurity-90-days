# Day 4 — Safe Diagnostic Lab

## Lab Objective

Observe diagnostic-style CAN traffic on a virtual CAN interface without interacting with a real vehicle.

## Environment

- Linux / WSL Ubuntu
- SocketCAN
- `can-utils`
- `vcan0`

## Setup

```bash
sudo modprobe can
sudo modprobe can_raw
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
ip -details link show vcan0
```

If `vcan0` already exists, do not recreate it.

## Terminal 1 — Capture

```bash
candump vcan0
```

## Terminal 2 — Send Synthetic OBD-Style Requests

```bash
cansend vcan0 7DF#0201050000000000
cansend vcan0 7DF#02010C0000000000
cansend vcan0 7DF#02010D0000000000
```

## Expected Observation

`candump` should display the frames transmitted onto the virtual bus. There may be no response because `vcan0` does not contain an ECU simulator.

Example expected request format:

```text
7DF   [8]  02 01 05 00 00 00 00 00
```

The exact timestamp is environment-dependent.

## Analysis

For each frame record:

- CAN ID
- DLC
- Service/mode byte
- PID
- Payload
- Whether a response exists

## Evidence

After running the lab, save your own terminal output under:

```text
04-diagnostics/Day-04/evidence/
```

Recommended files:

```text
vcan-setup.txt
candump-diagnostic-session.txt
```

Do not label generated examples as captured evidence.

## Safety

This exercise intentionally uses `vcan0`. Do not substitute a physical vehicle CAN interface unless you have explicit authorization and an appropriate isolated test setup.
