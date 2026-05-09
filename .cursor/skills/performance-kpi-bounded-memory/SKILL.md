---
name: performance-kpi-bounded-memory
description: Designs and reviews performance KPIs for eda_stl under strict memory constraints, including throughput, scaling, peak RSS, bytes-per-work-unit, and reject criteria. Use when the user asks for performance KPIs, parallelism planning under memory limits, throughput vs memory trade-offs, or benchmark design.
disable-model-invocation: true
---

# Performance KPIs Under Bounded Memory

## When To Use
Use this skill when designing, reviewing, or evaluating performance metrics
or parallelism choices for `eda_stl`.

## Inputs
- [`doc/performance/cpp23-and-parallel-runtime.md`](../../../doc/performance/cpp23-and-parallel-runtime.md)
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md)

## Output
A structured KPI proposal with:
1. KPI list (throughput, parallel efficiency, latency, memory).
2. Measurement methods, units, targets, and regression thresholds.
3. Benchmark scenarios with explicit dataset sizes.
4. Reject criteria.
5. CI gating proposal.

## Required KPI Schema
Every KPI returned must contain:
- `name`,
- `units`,
- `measurement_method`,
- `target`,
- `regression_threshold`,
- `benchmark_scenario`.

## Workflow
1. Confirm the performance charter from
   [`doc/performance/cpp23-and-parallel-runtime.md`](../../../doc/performance/cpp23-and-parallel-runtime.md).
2. List the KPIs being added, modified, or evaluated.
3. For each KPI fill the schema above.
4. Validate against memory budget categories:
   - per-design,
   - per-thread,
   - per-work-unit,
   - allocator-class breakdown.
5. State explicit reject criteria.
6. Propose CI gates and where they fit in
   [`doc/build-test-ci.md`](../../../doc/build-test-ci.md).

## Mandatory Constraint
Throughput-first proposals must include a memory cost model. Any optimization
that grows working set without bound is rejected.

## Reject-Criteria Template
- Reject if `parallel.efficiency_at_n_threads` falls below the documented
  threshold.
- Reject if `memory.peak_rss` increases beyond the regression threshold.
- Reject if `memory.bytes_per_work_unit` exceeds the regression threshold.
- Reject if any benchmark uses an unbounded queue or buffer.

## Acceptance Criteria
- All KPIs present with schema fields.
- Memory budget categories accounted for.
- Reject criteria stated.
- CI gating mapped to phases in
  [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
