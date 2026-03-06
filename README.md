## Bayside General Hospital — Diagnostic Imaging Process Improvement

A data-driven operations analysis of the outpatient diagnostic imaging workflow at Bayside General Hospital, using **service blueprinting**, **M/M/c queueing theory**, and **Monte Carlo simulation** to identify bottlenecks and redesign the patient journey for improved throughput and experience.

---

## Project Overview

This project is based on Case Problem 5.2 — *Herding the Patient* — from a Business Operations course. A cross-functional problem-solving team (nurse, technician, doctor, administrator, and patient representative) examined the 12-step outpatient diagnostic imaging process to identify inefficiencies and recommend improvements.

The analysis was conducted entirely in Python using `numpy`, `pandas`, `matplotlib`, and `scipy`, producing five dashboards covering everything from process mapping to simulation-based sensitivity analysis.

---

## Dashboards

### Dashboard 1 — Current-State Process Flow

A **service blueprint** of the existing 12-step patient journey, organized into swimlanes by responsible party:

| Lane | Responsible Parties |
|------|-------------------|
| Patient | Enter hospital, change, dress & depart |
| Admin Staff | Registration queue management |
| Transport / Staff | Inter-department transport |
| Imaging Dept | Imaging registration, dressing room management |
| Radiology / Tech | Exam execution |

Key features:
- **Decision diamonds** at three probabilistic branch points (registration queue, dressing room availability, exam readiness), with empirically estimated probabilities
- **Service time badges** (in minutes) on every step
- **Color-coded nodes** by responsible party, making accountability gaps immediately visible
- Corrected attribution: wait steps (e.g. "Wait for Dressing Area") are assigned to the *staff who control the delay*, not the patient experiencing it

---

### Dashboard 2 — M/M/c Queueing Analysis (Current State)

Applies an **M/M/c (Erlang-C) queueing model** to each station, assuming:
- Poisson patient arrivals at **6 patients/hour**
- Exponentially distributed service times
- Multiple parallel servers per station (where applicable)

**Why M/M/c?** This model is appropriate for outpatient clinical settings where arrivals are stochastic and memoryless, service times vary, and multiple staff/rooms serve the same function in parallel.

Metrics calculated per station:

| Metric | Description |
|--------|-------------|
| **ρ (utilization)** | Fraction of server capacity in use |
| **Lq** | Average number of patients queued |
| **Wq** | Average wait time in queue (minutes) |
| **W** | Total time in system per station |

**Bottlenecks identified:**
-  **Wait for Dressing Area** — highest utilization, longest queue
-  **Imaging Department Registration** — redundant step creating unnecessary congestion
-  **Main Registration** — moderate utilization, improvable via self-service

---

### Dashboard 3 — Before vs After Improvement Analysis

Side-by-side comparison of the current and optimized processes across all stations, showing:

- **Server utilization** reduction across every station
- **Queue wait time** reduction per station
- **Total patient journey time** (pure service time vs total with queuing overhead)
- **Waterfall chart** of per-station wait time improvements, annotated with the specific intervention driving each gain

**Proposed improvements and their queueing rationale:**

| Improvement | Rationale |
|-------------|-----------|
| Self-service kiosk at main registration | Reduces service time 5→2 min; lowers utilization below 80% threshold |
| Pre-registration app (mobile/web) | Eliminates imaging dept registration step entirely — removes a full queue node |
| SMS/digital dressing room queue alerts | Patients notified remotely; reduces idle waiting, increases effective server capacity |
| Parallel exam pre-scheduling | Pre-assigns slots before patient arrives in dressing area; drops Wq at that station by ~60% |
| +1 exam room | Reduces peak exam station utilization from critical to manageable |

---

### Dashboard 4 — Simulation & Sensitivity Analysis

Four sub-analyses providing statistical depth:

**Sensitivity: Utilization vs Arrival Rate**
Shows how the dressing area bottleneck degrades as patient volume grows. The current 6 patients/hr load sits uncomfortably close to the saturation zone.

**Server Count Tradeoff**
Demonstrates how adding dressing rooms reduces average wait time, with diminishing returns beyond 3 rooms — supporting the recommendation for exactly +1 room rather than a larger capital investment.

**Monte Carlo Simulation (n = 2,000)**
Simulates full patient journeys by sampling exponential service and queue wait times at every station. Outputs the distribution of total visit duration including mean, standard deviation, and 90th percentile — useful for setting patient expectations and staffing schedules.

**Utilization Heatmap**
Maps server utilization across all 12 stations at arrival rates from 2–12 patients/hour, making it easy to see which stations become critical first as volume scales. Supports capacity planning for high-volume periods.

---

### Dashboard 5 — Optimized Process Flow

A redesigned swimlane flowchart of the improved process in the same format as Dashboard 1, with visual annotations highlighting what changed:

- **Green-bordered boxes** — steps improved by the analysis
- **Green callout bubbles** — specific change made at each improved step
- **Eliminated step** — Imaging Department Registration removed entirely via pre-registration
- **Service time badges** — green with ↓ arrow where times were reduced, showing the before/after delta
- **Updated decision probabilities** — reflect improved wait/proceed ratios (e.g. dressing room wait probability drops from 45% → 30%)

---

## Key Results

| Metric | Current | Optimized | Change |
|--------|---------|-----------|--------|
| Total journey time (modeled) | ~109 min | ~82 min | ↓ ~25% |
| Peak station utilization | ~89% | ~67% | ↓ 22 pts |
| Max avg queue wait (Wq) | ~14 min | ~5 min | ↓ ~64% |
| Queue nodes in process | 3 | 2 | ↓ 1 eliminated |
