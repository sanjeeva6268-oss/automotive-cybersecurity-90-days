# Day 1 — Learning Notes

## What I Learned

The automotive attack surface includes every meaningful interface through which data, commands, software, or physical access can influence vehicle systems.

A useful way to reason about the surface is:

```text
Input
  ↓
Receiver
  ↓
Trust Boundary
  ↓
Process / Network
  ↓
Asset
  ↓
Security Impact
```

## Key Concepts

### Attack Surface

The collection of reachable interfaces and entry points that may be used to interact with a vehicle system.

### Receiver

The first component or process that receives an external input. Identifying receivers helps turn a high-level vehicle diagram into concrete security analysis targets.

### Trust Boundary

A boundary between components, networks, or environments with different security assumptions or trust levels.

### Untrusted Input

Data originating from a source that the receiving system cannot inherently trust. Examples include wireless traffic, diagnostic requests, media files, network packets, and external service requests.

### Attack Path

A sequence showing how an input can move through receivers and boundaries toward an asset.

Example:

```text
Cellular
   ↓
TCU
   ↓
Gateway
   ↓
Internal Network
   ↓
ECU
   ↓
Vehicle Function
```

## Important Questions I Can Now Ask

1. Where can data enter the vehicle?
2. Who controls the source of that data?
3. Which component receives it first?
4. Which trust boundaries does it cross?
5. Can it reach another vehicle network?
6. What asset is downstream?
7. What security controls should stop or limit the path?

## What I Should Not Assume

- An exposed interface is not automatically vulnerable.
- Crossing a trust boundary is not automatically an attack.
- CAN communication by itself does not prove that an ECU can be compromised.
- A gateway does not automatically provide complete isolation.
- A threat model is not a vulnerability finding; it is a structured way to identify and prioritize potential threats.

## Day 1 Reflection

The most important lesson is that automotive security starts with architecture rather than exploitation. Before testing a protocol or ECU, I need to understand where untrusted data enters, which receivers process it, which trust boundaries it crosses, and which assets could ultimately be affected.

## Status

**Day 1: Complete**
