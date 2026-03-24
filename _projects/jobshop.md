---
layout: page
title: Genetic Algorithms for Flexible Job Shop Scheduling
description: A Python implementation of Genetic Algorithms for the NP-hard Flexible Job Shop Scheduling Problem (FJSP), comparing multiple GA variants (initialization, selection, crossover, mutation) and evaluating them on benchmark instances.
img: assets/img/projects/jobshop/jobshop_OG.png
importance: 3
category: Research
related_publications: false
---

This project summarizes my Bachelor’s thesis on applying **Genetic Algorithms (GAs)** to the **Flexible Job Shop Scheduling Problem (FJSP)**—a classic NP-hard optimization problem where each operation can be processed by multiple compatible machines.  
I implemented and compared different GA “recipes” (initialization, selection, crossover, mutation), validated them on **benchmark datasets**, and analyzed trade-offs between **solution quality** and **machine utilization**.

<p class="lead">
  Goal: assign each operation to a compatible machine and schedule all operations to <b>minimize the makespan</b>
  (total completion time), while respecting precedence and machine-capacity constraints.
</p>

<div class="row g-3">
  <div class="col-md-6">
    <div class="card p-3 shadow-sm rounded">
      <b>What I built</b>
      <ul class="mb-0">
        <li>A complete GA pipeline for FJSP (Python)</li>
        <li>Multiple GA variants (conditional / broad / mixed)</li>
        <li>Simulation + Gantt chart visualization for schedules</li>
      </ul>
    </div>
  </div>
  <div class="col-md-6">
    <div class="card p-3 shadow-sm rounded">
      <b>What I learned</b>
      <ul class="mb-0">
        <li>Constraint-safe genetic operators (precedence-preserving)</li>
        <li>How initialization and mutation shape convergence</li>
        <li>Diagnosing schedule quality via idle-time patterns</li>
      </ul>
    </div>
  </div>
</div>

---

## Problem

In **FJSP**, each job is a sequence of operations with strict precedence constraints, and each operation can be executed by **one of multiple machines** with different processing times.  
The optimization target is typically **minimizing makespan**, under constraints such as: one operation per machine at a time, no preemption, and precedence-feasible schedules.

---

## Approach

### GA representation (feasible scheduling)

A chromosome encodes the schedule as a sequence of operation assignments *(job, operation, machine)*, designed to preserve **precedence feasibility**. The fitness function evaluates a chromosome by **simulating execution** and computing the resulting makespan.

### Operators and heuristics

To study the impact of design choices, I implemented and combined:

- **Initialization heuristics**
  - Random assignment (diversity)
  - *Random Longest–Shortest time* (mix exploration and exploitation)
  - *Minimum Processing Time* (strong baseline, but risk of premature convergence)

- **Selection**
  - k-Tournament selection
  - Optional **elitism** (carry best individuals forward)

- **Crossover**
  - **IPOX** (Improved Precedence Preserving Order-based Crossover), tailored to keep precedence constraints satisfied

- **Mutation**
  - Precedence-preserving operation mutation (safe reordering within valid bounds)
  - Machine mutation (swap assigned machine for an operation)
  - Conditional mutation (apply only when it improves fitness)

---

## Implementation highlights

The project was implemented in **Python 3.8** with a clean object model:

- Core domain entities (*Problem, Job, Task, Operation, Machine*)
- A dedicated **GAHandler** orchestrating initialization → selection/crossover → mutation → evaluation
- A **Simulation** utility that generates schedule timelines and **Gantt charts** to inspect machine utilization and bottlenecks

---

## Experiments & Results

I evaluated four GA variants:

- **Conditional**: strong initialization + conditional mutation (stable improvements, risk of “sticking”)
- **Broad**: more randomness + elitism (better exploration, slower convergence)
- **Mixed Conditional**: broad init + conditional mutation (balanced exploration/exploitation)
- **Mixed Valence**: strong init + freer mutation (often improves utilization)

Benchmarks were taken from **Brandimarte** instances (e.g., MK01, MK02).  
Results showed that the “best” configuration depends on the instance: some datasets reward aggressive exploration, while others benefit from stronger initialization and controlled mutation. A recurring pattern was that some approaches reached good makespan but suffered from **idle-time inefficiency**, visible in the Gantt charts—highlighting that “good makespan” and “good machine usage” can diverge.

---

## Skills developed

- **Metaheuristic optimization**: GA design for NP-hard combinatorial problems  
- **Constraint-aware genetic operators**: precedence-preserving crossover and mutation  
- **Scheduling & operations research**: makespan minimization, routing + sequencing formulation  
- **Benchmarking & evaluation**: comparative experiments, convergence behavior, trade-off analysis  
- **Python engineering**: modular architecture, parsers, simulation tooling, plotting (Gantt charts)

---

## Artifacts

- **Thesis (PDF)**: On-request
- **Code (GitHub)**: [Repository](https://github.com/KingPowa/Genetic_Algorithm_FJSP)