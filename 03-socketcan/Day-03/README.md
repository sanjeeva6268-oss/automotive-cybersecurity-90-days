# Day 3 — Vehicle Communication with SocketCAN

## Source

Primary study source: Craig Smith, *The Car Hacker's Handbook*, Chapter 3 — **Vehicle Communication with SocketCAN**, Open Garages online edition. The chapter introduces SocketCAN as a Linux CAN interface, covers `can-utils`, virtual CAN, CAN utilities, ISO-TP support, SocketCAN application development, `socketcand`, and Kayak. citeturn0view0

## Objective

Build a safe Linux-based CAN laboratory using **SocketCAN + virtual CAN (`vcan0`)** and learn the workflow used to inspect, generate, filter, record, and analyze synthetic CAN traffic.

No physical vehicle is required for this lab.

## Learning Outcomes

By the end of Day 3, I should be able to:

- Explain what SocketCAN is.
- Explain why Linux represents CAN interfaces as network interfaces.
- Install and verify `can-utils`.
- Load the CAN and virtual-CAN kernel modules.
- Create and bring up `vcan0`.
- Inspect the interface with `ip`.
- Generate synthetic CAN frames with `cansend`.
- Capture frames with `candump`.
- Monitor changing bytes with `cansniffer`.
- Filter traffic with `candump`.
- Record and replay synthetic traffic safely.
- Explain the role of `PF_CAN` / `AF_CAN` at a conceptual level.
- Document reproducible lab evidence in Git.

## 1. SocketCAN Architecture

SocketCAN integrates CAN with the Linux networking stack. The book describes it as a common interface that allows applications to interact with different CAN hardware through network-device interfaces rather than requiring each application to use a separate hardware-specific interface. citeturn0search0

```text
+-------------------------------+
|        User Applications      |
| candump / cansend / sniffer   |
+---------------+---------------+
                |
             SocketCAN
                |
+---------------+---------------+
|          Linux Kernel         |
|       CAN network stack       |
+---------------+---------------+
                |
        +-------+-------+
        |               |
      vcan0          can0
    (virtual)       (hardware)
        |               |
    Lab traffic     CAN adapter
```

For this project, `vcan0` is the preferred interface because it is isolated from a real vehicle network.

## 2. Install can-utils

On Ubuntu/Debian:

```bash
sudo apt update
sudo apt install -y can-utils
```

Verify:

```bash
cansend --help
candump --help
cansniffer --help
```

The handbook specifically uses the Debian/Ubuntu package-manager approach for installing `can-utils`. citeturn0search0

## 3. Create the Virtual CAN Interface

Load the modules:

```bash
sudo modprobe can
sudo modprobe vcan
```

Create `vcan0`:

```bash
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
```

Verify:

```bash
ip -details link show vcan0
```

Expected conceptual state:

```text
vcan0: <NOARP,UP,LOWER_UP>
```

The exact output can vary by kernel and distribution.

### Cleanup

When finished:

```bash
sudo ip link set vcan0 down
sudo ip link delete vcan0
```

## 4. First Synthetic CAN Frame

Open Terminal 1 and start the receiver:

```bash
candump vcan0
```

Open Terminal 2 and transmit a synthetic frame:

```bash
cansend vcan0 123#1122334455667788
```

The receiver should display a frame corresponding to:

```text
Interface  ID      Data
vcan0      123     11 22 33 44 55 66 77 88
```

The exact timestamp/output formatting depends on `candump` options and version.

**Important:** this is virtual CAN traffic only. Do not substitute a physical `can0` interface when following this beginner lab unless you are working in a controlled, explicitly authorized environment.

## 5. Multiple Synthetic Frames

Generate several different frames:

```bash
cansend vcan0 100#01020304
cansend vcan0 101#AABBCCDD
cansend vcan0 200#1122334455667788
cansend vcan0 201#DEADBEEF
```

Observe them with:

```bash
candump vcan0
```

### What to observe

- Arbitration ID
- DLC/data length
- Payload bytes
- Timestamp
- Order of frames
- Repeated identifiers

## 6. Filtering with candump

Capture only selected identifiers using a CAN filter:

```bash
candump vcan0,123:7FF
```

This asks `candump` to display frames matching the specified ID/mask pattern.

Try generating:

```bash
cansend vcan0 123#01020304
cansend vcan0 124#05060708
```

Only the matching traffic should appear in the filtered capture.

## 7. Using cansniffer

Start:

```bash
cansniffer vcan0
```

Then generate repeated frames with one changing byte:

```bash
cansend vcan0 321#0100000000000000
cansend vcan0 321#0200000000000000
cansend vcan0 321#0300000000000000
```

`cansniffer` is useful for visually identifying changing bytes in a stream. It is a **traffic-analysis aid**, not a decoder that automatically tells you what a signal means.

## 8. Logging Traffic

Capture a synthetic session to a text file:

```bash
candump -L vcan0 > can-session.log
```

Generate traffic in another terminal, then stop `candump` with `Ctrl+C`.

Review it:

```bash
cat can-session.log
```

Keep sanitized synthetic captures under:

```text
03-socketcan/Day-03/evidence/
```

## 9. Replay Synthetic Traffic

A recorded `candump -L` file can be replayed with `canplayer` in a virtual environment:

```bash
canplayer -I can-session.log
```

Use only synthetic or authorized captures.

## 10. SocketCAN Programming Concept

SocketCAN applications can access CAN through Linux sockets. The handbook shows the use of the CAN protocol family and raw CAN sockets, including `PF_CAN`, `SOCK_RAW`, and `CAN_RAW`. citeturn0search0

Conceptually:

```text
Application
    |
    | socket()
    v
PF_CAN / AF_CAN
    |
    v
SocketCAN
    |
    v
CAN interface
    |
    v
vcan0 / can0
```

This is analogous to ordinary network programming: applications use sockets while the kernel handles the interface and protocol plumbing.

## 11. Procfs / Interface Observation

The chapter also discusses inspecting CAN-related information through Linux interfaces. For this project, begin with practical network inspection:

```bash
ip link show
ip -details link show vcan0
```

Optional CAN-related checks:

```bash
lsmod | grep -E 'can|vcan'
```

## 12. Day 3 Mini-Lab

### Lab A — Interface Setup

```bash
sudo modprobe can
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
ip -details link show vcan0
```

### Lab B — Send and Capture

Terminal 1:

```bash
candump vcan0
```

Terminal 2:

```bash
cansend vcan0 123#1122334455667788
```

### Lab C — Generate a Small Dataset

```bash
cansend vcan0 100#0100000000000000
cansend vcan0 100#0200000000000000
cansend vcan0 100#0300000000000000
cansend vcan0 200#AABBCCDD
cansend vcan0 200#AABBCCEE
```

Capture and analyze the resulting stream.

### Lab D — Identify the Changing Byte

For ID `0x100`, determine which byte changes between the three frames.

Expected answer:

```text
0x100
01 00 00 00 00 00 00 00
02 00 00 00 00 00 00 00
03 00 00 00 00 00 00 00
^
changing byte
```

We can identify the changing byte, but **we cannot yet claim that it represents speed, RPM, steering angle, temperature, or any other real signal**. Signal meaning requires reverse engineering or a known database.

## 13. Portfolio Evidence

Save the following evidence:

```text
03-socketcan/
└── Day-03/
    ├── README.md
    ├── socketcan-commands.md
    ├── can-analysis.md
    ├── notes.md
    └── evidence/
        ├── interface.txt
        ├── candump.txt
        └── can-session.log
```

Do not commit credentials, private vehicle data, proprietary captures, or personally identifying information.

## 14. Interview Questions

1. What is SocketCAN?
2. Why is SocketCAN useful for automotive security research?
3. What is `vcan0`?
4. What is the difference between `vcan0` and a physical `can0`?
5. What does `cansend` do?
6. What does `candump` do?
7. What does `cansniffer` help identify?
8. What is `can-utils`?
9. Why is Linux networking knowledge useful for SocketCAN?
10. What is `PF_CAN`?
11. What is a raw CAN socket?
12. Why should virtual CAN be preferred for initial experiments?
13. What is CAN filtering?
14. Why does observing a changing CAN byte not immediately reveal its signal meaning?
15. How does SocketCAN separate applications from CAN hardware drivers?

## 15. Day 3 Security Perspective

SocketCAN itself is not an automotive security control. It is an interface used to communicate with CAN devices. Security depends on the environment, permissions, physical/network access, gateway architecture, message handling, and controls around the CAN network.

The important professional workflow is:

```text
Set up isolated lab
        ↓
Observe traffic
        ↓
Generate synthetic traffic
        ↓
Filter / capture / replay
        ↓
Analyze patterns
        ↓
Form hypotheses
        ↓
Validate in an authorized environment
```

## Deliverables

- [x] Chapter 3 SocketCAN study notes
- [x] `can-utils` workflow
- [x] Virtual CAN setup commands
- [x] `cansend` / `candump` workflow
- [x] CAN filtering
- [x] `cansniffer` workflow
- [x] Logging and replay workflow
- [x] SocketCAN programming concepts
- [ ] Run commands in WSL/Ubuntu
- [ ] Capture real terminal output
- [ ] Commit evidence files

## Status

**Day 3: Theory deliverable committed — hands-on evidence pending.**

## Source

Open Garages, *The Car Hacker's Handbook*, Chapter 3: Vehicle Communication with SocketCAN. citeturn0view0
