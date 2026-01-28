# Fleet Management System — Complete Document (Structured)

## Preface
This document presents a structured exposition of the Assignment Engine logic used in mining fleet management systems (Cat MineStar / DISPATCH / Joptimizer). It is intended as an operational guide for Controllers and Builders: explaining the optimization models (BP, LP, DP), critical inputs, assignment triggers, common failure modes, key utilities, and best practices for maintaining data integrity and haulage performance.

## Abstract
Modern mining fleet management operates across three computational layers: Best Path (BP) for routing, Linear Programming (LP) for master planning (production arcs and feed rates), and Dynamic Programming (DP) for real-time execution. Assignment decisions depend heavily on data quality (mine model, GPS, equipment status, blending rules, TKPH, fuel levels). This document details the decision flow, important parameters, assignment triggers and types, failure modes, monitoring metrics, and recommended operational configurations.

---

# Table of Contents
- Chapter I: Introduction
- Chapter II: System Architecture & Operational Goals
- Chapter III: Optimization Models — BP, LP, DP
- Chapter IV: Inputs, Triggers, and Assignment Types
- Chapter V: User Impact & Data Integrity
- Chapter VI: Failure Modes and Troubleshooting
- Chapter VII: Operations, Monitoring & Dashboards
- Chapter VIII: Best Practices & Configuration Recommendations
- References
- Appendix A: Glossary of Technical Terms

---

# Chapter I: Introduction

## 1.1 Background
Automated haul truck allocation delivers significant benefits when the mine model and configuration are accurate. The system combines strategic planning and dynamic execution to maximize tonnage while minimizing cost-per-cycle.

## 1.2 Document Purpose
- Provide a structured guide to how assignments are produced.
- Deliver a data-integrity checklist for Controllers and Builders.
- Present common troubleshooting steps and configuration recommendations.

## 1.3 Scope
Covers assignment logic (BP/LP/DP), inputs and triggers, assignment modes, common failures, key utilities, monitoring metrics, and best practices. Implementation-level details (database schemas, specific code) are out of scope.

---

# Chapter II: System Architecture & Operational Goals

## 2.1 System Components
- Servers: application servers, SQL database, reporting services.
- Workstations: Mine Controller consoles, Builder tools (MineView, Dispatch utilities, FormView).
- Field devices: on‑board displays, GPS, payload systems (OEM telemetry), TMAC communications.
- Utilities/modules: TruckMod, ShovelMod, LocMod, LP Configuration, BlendMod, Road Utility, TI Page, Exceptions Window, Report Manager.

## 2.2 Optimization Objectives
- Maximize production (tons per hour).
- Minimize cycle components (travel time, idle time) to reduce cost per ton.
- Respect constraints: shovel/dump/crusher capacities, blending rules, TKPH, locks/bars, scheduled tasks (refuel, tiedown).

---

# Chapter III: Optimization Models — BP, LP, DP

## 3.1 Best Path (BP)
- Purpose: compute the fastest/shortest route between nodes (callpoints/locations).
- Inputs: road network topology, callpoint coordinates and elevations, road statuses (open/closed, one-way).
- Output: route geometry (green line) and estimated travel time per segment (design and dynamic).

## 3.2 Linear Programming (LP)

### 3.2.1 LP Goals & Outputs
- Produce the LP solution: production arcs (shovel → dump → material) and feed rates (tons/hour).
- Objective: minimize total hauling requirement or maximize truck productivity subject to capacity and blending constraints.

### 3.2.2 Global LP Parameters (key)
- LPMATCHTRUCKS (allow unused shovels)
- Use Global Truck Productivity Objective (YES/NO)
- Proportional sharing of crusher capacity
- Treat shovel-to-dump locks as two-way (YES/NO)
- Desired Coverage (%) per shovel (0–150%)
- LPTRUCK types and dig factors (match truck sizes to shovels)

### 3.2.3 LP Recalculation Triggers
- Time-based (default 600 s; minimum 60 s)
- Event-based: status changes (shovel/dump ready/down), configuration changes (locks, priorities, blending), major operational changes

## 3.3 Dynamic Assignment (DP / DA)

### 3.3.1 Role of DP
- Execute LP solution in real time; generate assignments on events (truck requests, misroutes, waypoint arrivals).
- Principle: balance and synchronization to minimize lost tons and overall cost.

### 3.3.2 Empty Truck → Shovel Assignment
Steps:
1. Capture a snapshot of current operations (positions of trucks and shovels, ETAs).
2. Identify the "neediest shovel":
   - If a shovel has been idle beyond a threshold (assignByIdle) it becomes a priority; or
   - Compute deficit = Required Haulage (from LP) − Current Haulage (assigned trucks).
3. Select the neediest route (dump → shovel empty path) with the largest deficit.
4. For all eligible trucks, compute cost:
   - cost = travelTime * travelCost + forecastQueueTime * idleCost
   - travelCost is usually weighted higher (e.g., 1.5)
   - idleCost is adjusted by a Shovel Priority Factor to favor high-priority shovels
5. Apply restrictions (locks/bars, assignability) and assign the truck with the lowest cost.

### 3.3.3 Loaded Truck → Dump Assignment
- Respect the lock hierarchy:
  1. Dump Lock on Grade with Hard Lock (highest)
  2. Truck Dump Lock
  3. Shovel Dump Lock
  4. Grade Dump Lock (standard)
- If no locks block, use the neediest loaded LP path from the originating shovel; selected dump must be Ready and accept the material.

### 3.3.4 Overrides & Special Assignments
- Scheduled assignments (refuel, tiedown) and critical fuel conditions override production assignments.
- Manual assignments by controller/operator override automatic logic.

---

# Chapter IV: Inputs, Triggers, and Assignment Types

## 4.1 Categories of Critical Inputs
1. Static configuration (mine model): roads, callpoints, elevations, compatibility matrices, dump capacities, queue limits.
2. Strategic configuration: goals, priorities, desired coverage, global LP parameters, LPTRUCK definitions.
3. Real-time data: GPS positions, activity state transitions, shovel dig rates, truck payloads, fuel levels, TKPH.
4. System-calculated metrics: rolling-average travel times per road segment, spotting/load times, TKPH rolling averages.

## 4.2 Assignment Triggers
- Truck completes dump → enters Traveling Empty → automatic assignment to a shovel.
- Truck completes loading / load report → assignment to a dump.
- Operator presses Assign (truck request).
- Mine Controller requests an assignment (Request Assignment).
- Truck enters a reassignment waypoint (ReAssign when Loaded/Empty).
- Misroute detection (truck deviates beyond misrouteDistance, default 100 m).
- Assignment delay expires.
- Loaded material changes (material mismatch triggers reassignment).
- Scheduled assignment window becomes active.

## 4.3 Assignment Types
- Immediate Automatic: event-driven (dump complete, load report).
- Scheduled Automatic: time-window based (Required At, Arrive After/Before) — for refueling, tiedown.
- Immediate Manual: controller/operator override (drag-and-drop, Truck Assistant).
- Manual Scheduled: pre-specified assignment that takes effect on a specified load state.

## 4.4 Scheduled Assignments & Manual Circuits
- Scheduled automatic assignments use Required At ± arrival windows and can be strict or flexible.
- Automated refueling uses Fuel Low and Fuel Empty thresholds; Fuel Empty triggers immediate assignment to refuel.
- Manual circuits chain multiple scheduled manual assignments for a single truck (use sparingly — these disrupt DP statistics).

---

# Chapter V: User Impact & Data Integrity

## 5.1 Areas Impacted by Users
Controllers, Builders, and Operators must maintain:
1. Mine model configuration (compatibility, locks, callpoint elevations)
2. Spatial mine model (roads & callpoints)
3. Placement of reassignment waypoints
4. Cycle times and dynamic efficiency settings
5. Assignment groups and regional locks
6. Production requirements (Goals/Priorities)
7. Blending models and grade definitions
8. Delay records (accurate durations and reason codes)
9. Scheduled assignments (correct windows)
10. Operator behavior (follow green line, use onboard assignment buttons correctly)

## 5.2 Data Control Practices (start-of-shift checklist)
- Verify statuses (Ready/Down/Delay/Standby), assignability flags, locks/bars, location elevations, and road openness.
- Check LPTRUCK types, dig factors, fuel-bay capacities, and TKPH thresholds.
- Use Assignment Context and Exceptions for diagnosing assignment failures.
- Correct corrupted cycles promptly with Cycle Assistant / Shift Edit.

## 5.3 Key Utilities (brief)
- TruckMod / Truck Assistant — truck status, fuel, locks, LPTRUCK type, TKPH.
- ShovelMod / Loading Tool Assistant — dig rate, spotting time, assignable status, dump locks.
- LocMod / Location Utility — dump configuration, target feed, capacity, beacons, elevations.
- RoadMod — road segments, open/close, gradients.
- LP Config, Shovel Need, TI Page, Exceptions Window, Report Manager, BlendMod, GPS Utility.

---

# Chapter VI: Failure Modes and Troubleshooting

## 6.1 Failure Modes — Empty Truck
Common causes for empty-truck assignment failures:
- Shovel is Down / Unassignable or deleted.
- Truck is unavailable (not marked available for assignment).
- Truck-to-shovel restrictions (locks/bars) create a dead end match.
- No best path exists (road missing/closed, bad elevation data).
- Shovel not in LP production plan.
- Shovel location UNKNOWN or shovel not Ready.
Troubleshoot: check Assignment Context popup, TruckMod, ShovelMod, RoadMod, Exceptions.

## 6.2 Failure Modes — Loaded Truck
Common causes for loaded-truck assignment failures:
- Destination does not accept the material (dump not configured for the grade).
- Material on truck is undetermined (no material recorded).
- Grade or mining-block locks are conflicting.
- Processor/dump not Ready or not available for assignment.
- Missing unload-time for the truck class on the processor.
Troubleshoot: verify truck material, check Processor Assistant (accepted materials), view Assignment Context.

## 6.3 Non-selection Reasons
- Lower-cost alternative chosen based on travel + idle cost.
- Queue/capacity exceeded at candidate destinations.
- Scheduled assignment windows incompatible (Arrive After/Before).
- Production goals or blending constraints prevent the assignment.
- TKPH steering limits options.
Always read the message in Assignment Context for a specific reason.

---

# Chapter VII: Operations, Monitoring & Dashboards

## 7.1 Trucking Indication (TI) Page — key metrics
- Current Target (trucks required per arc) — LP output.
- Associated (trucks assigned) — DP realtime assignments.
- Computed Max — theoretical throughput limit for an arc (minimum of shovel, processor, available trucks).
- Coverage = Associated / Current Target (%) — color key: Under (blue), Adequate (green), Over (red).

Use TI to detect over/under-trucking and to guide redistribution actions.

## 7.2 Exceptions Window & Monitoring Utilities
- Exceptions Window must be monitored (Accept/Reject actions).
- FUA (Fleet Update Assistant) — check the Available for Assign column.
- Haul Route / Mine Graphics — visualize green line and queues.
- Scheduled Assignment Monitor — view scheduled assignments (Active, Late).
- Assignment Context — reasons for assignment failures.

## 7.3 Monitoring Best Practices
- Keep Exceptions and Assignment Context visible.
- Avoid manual assignments unless necessary — they distort DP statistics.
- Fix data issues (delays, cycles) promptly using Shift Edit / Cycle Assistant.
- Monitor TI color indicators and take corrective actions (park surplus trucks, reassign).

---

# Chapter VIII: Best Practices & Configuration Recommendations

## 8.1 Goals vs Priorities
- Prefer numeric Goals rather than relying solely on Priorities (which are ambiguous). Goals represent measurable targets (tons/hour).
- Apply a 10% buffer to numeric targets to give DP flexibility.

## 8.2 Reassignment Waypoints
- Place waypoints strategically before major intersections, not exactly at intersections.
- Use sparingly and visually differentiate them to alert operators.
- Avoid placing waypoints in areas with poor communications.

## 8.3 Locks & Bars
- Minimize use of locks; hard locks greatly reduce optimization flexibility.
- Document hard locks (Dump Lock on Grade Hard) — these carry the highest priority.
- Remember lock hierarchy: Dump Lock (Hard) > Truck Dump Lock > Shovel Dump Lock > Grade Lock.

## 8.4 Fuel, TKPH, Tiedown
- Configure Fuel Low and Fuel Empty thresholds by truck class; define refuel-bay capacities and refuel windows.
- Set TKPH thresholds and TKPH steering percentages; TKPH steering guides assignments but should not cause failures.
- Plan tiedown (shift change) using the Tiedown Utility; automate tiedown if possible to preserve optimization.

---

# References
This document was prepared by summarizing and reorganizing content from the source file in the repository (Cat MineStar / DISPATCH / Joptimizer materials). For traceable details, consult Assignment Context, LP Summary Reports, and vendor documentation.

---

# Appendix A — Glossary of Technical Terms
- BP: Best Path  
- LP: Linear Programming  
- DP / DA: Dynamic Programming / Dynamic Assignment  
- TKPH: Ton-Kilometer per Hour  
- TI: Trucking Indication  
- FUA: Fleet Update Assistant  
- TMAC: messaging protocol (request/response)  
- LPTRUCK: logical truck type used by the LP  
- Shovel Priority Factor (SPF): priority weighting applied to idle-cost calculations

--------------------------------------------------------------------------------