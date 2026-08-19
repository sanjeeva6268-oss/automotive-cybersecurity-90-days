# Day 3 — SocketCAN Command Reference

## Install

```bash
sudo apt update
sudo apt install -y can-utils
```

## Load modules

```bash
sudo modprobe can
sudo modprobe vcan
```

## Create vcan0

```bash
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
ip -details link show vcan0
```

## Receive

```bash
candump vcan0
```

## Send

```bash
cansend vcan0 123#1122334455667788
```

## Filter

```bash
candump vcan0,123:7FF
```

## Sniff changing bytes

```bash
cansniffer vcan0
```

## Log

```bash
candump -L vcan0 > can-session.log
```

## Replay

```bash
canplayer -I can-session.log
```

## Inspect modules

```bash
lsmod | grep -E 'can|vcan'
```

## Inspect interfaces

```bash
ip link show
ip -details link show vcan0
```

## Remove virtual interface

```bash
sudo ip link set vcan0 down
sudo ip link delete vcan0
```

## Safety

Use `vcan0` for this learning lab. Do not replace it with a physical CAN interface connected to a vehicle unless the vehicle/network is owned or explicitly authorized and the test environment is controlled.
