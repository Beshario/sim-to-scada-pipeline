# Simulation-First Automation: Robot-Mounted Inspection

## Overview
This project demonstrates a **simulation-first automation workflow** for a **robot-mounted inspection cell**.

A simulated robot moves a camera through inspection poses, generates machine-like telemetry, and exposes it to a supervisory layer (SCADA) using a stable data contract. The focus is on **process modeling and integration**, not vision algorithms or PLC implementation.

---

## What This Is
- A simulation of a robot-mounted inspection **process**
- A reference architecture for simulation → telemetry → SCADA
- A stand-in for real hardware using a consistent data contract

## What This Is Not
- A full inspection product
- A vision or AI implementation
- A PLC / I/O communication demo

---

## Architecture
[ Mechanical Assets ]
↓
[ Simulation / Process Model ]
↓
[ Telemetry Contract ]
↓
[ SCADA + Historian ]


Simulation and real systems are treated as **peers** that produce the same telemetry.

---

## Process
One inspection cycle:
1. Robot moves camera through one or more inspection poses
2. Image capture events are triggered (abstracted)
3. Cycle completes and repeats

This simulation models the timing and spatial context of inspection events, not the inspection algorithms themselves.
---

## Telemetry Contract
| Field | Description |
|------|-------------|
| `asset_id` | Simulated cell identifier |
| `state` | IDLE / RUNNING / FAULT |
| `cycle_time_ms` | Last cycle duration |
| `cycle_count` | Completed cycles |
| `timestamp` | Event time |

---

## Notes
Vision algorithms, PLC I/O, and operator workflows are intentionally abstracted.  
The architecture is designed to scale toward AI inspection and real hardware without changing the process model.
---
