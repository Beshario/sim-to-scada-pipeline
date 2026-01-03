# Signal Design Specification

Signals are designed to represent process state and inspection events independent of vision algorithms or controller implementation. This abstraction enables seamless integration across different SCADA platforms and hardware configurations.

## Overview

This specification defines three primary signal groups that form the foundation of the system's real-time monitoring and historical tracking capabilities.

## Signal Groups

### 1. Cell State (Supervisory Control)

Provides real-time operational status and cycle position information to SCADA systems.

| Signal | Type | Description |
|--------|------|-------------|
| cell.state | enum | Current operational state: IDLE, RUNNING, or FAULT |
| cell.step | enum/string | Current cycle step: MOVE_P1, CAPTURE_P1, MOVE_P2, CAPTURE_P2, RESET |
| cell.cycle_active | bool | Indicates whether a cycle is actively executing |

**Consumption Pattern:** SCADA systems consume these signals continuously for real-time monitoring and control decisions.

### 2. Cycle & Throughput (Historical Metrics)

Captures performance and throughput data for cycle analysis and trend reporting.

| Signal | Type | Description |
|--------|------|-------------|
| cycle.count | int | Total number of completed cycles (incremented on completion) |
| cycle.time_ms | float | Duration of the last completed cycle in milliseconds |
| cycle.start_ts | datetime | Timestamp of cycle start (optional) |
| cycle.end_ts | datetime | Timestamp of cycle completion (optional) |

**Update Policy:** These signals are updated only at cycle boundaries to ensure data consistency.

### 3. Inspection Events (Event-Driven Data)

Documents inspection activities with precise temporal and spatial information.

| Signal | Type | Description |
|--------|------|-------------|
| inspect.event | bool/pulse | Event pulse triggered upon image capture (one tick duration) |
| inspect.pose_id | int | Identifier for the capture position (1, 2, 3, etc.) |
| inspect.capture_ts | datetime | Precise timestamp of image capture |
| inspect.pose_xyz | tuple/array | Optional spatial coordinates of capture location |
| inspect.index_in_cycle | int | Sequential index of capture within current cycle (1..N) |

**Event Semantics:** These signals represent discrete events rather than continuous values and should be processed accordingly.

## Asset Identity

Every signal transmission must include asset identification metadata to enable hardware abstraction and future production deployment.

| Signal | Type | Description |
|--------|------|-------------|
| asset.id | string | Unique identifier for the asset instance |
| asset.type | string | Asset classification (e.g., robot_inspection_cell) |

## Naming Conventions

The following naming conventions must be observed to maintain protocol independence and semantic clarity:

- Use dot notation for hierarchical signal organization (e.g., `cell.state`)
- Exclude vendor-specific terminology from signal names
- Avoid exposing internal code variable names
- Prioritize semantic meaning over implementation convenience

This approach ensures compatibility with diverse SCADA platforms (Ignition, MQTT, OPC UA) without requiring translation layers.

## Deferred Features

The following data categories are explicitly excluded from this initial specification but may be incorporated in future iterations without modifying existing signal definitions:

- Image data
- Confidence scores
- Defect classification results
- AI/ML metrics
- PLC-level bits and flags

## SCADA Integration Model

The following consumption patterns define how SCADA systems interact with the signal set:

| Use Case | Signals |
|----------|---------|
| Real-time monitoring | cell.state, cell.step |
| Historical trends | cycle.time_ms, cycle.count |

## Implementation Approach

The simulation publisher implements a direct data emission strategy:

- The simulation (Python) emits structured signal data
- SCADA systems (Ignition) consume this data directly
- No intermediate protocol abstraction layers are introduced

This approach validates both process logic and data structure integrity while maintaining implementation simplicity and enabling direct production mapping.
