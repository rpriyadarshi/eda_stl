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
- [`doc/mission.md`](../../../doc/mission.md) (the charter; KPIs are
  in service of the public-utility, chip-class-scale mission).
- [`doc/performance/cpp23-and-parallel-runtime.md`](../../../doc/performance/cpp23-and-parallel-runtime.md)
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md)
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (the binding/service/tile/MCP KPI surface).
- [`doc/library-catalog.md`](../../../doc/library-catalog.md) (the
  canonical performance stack).

## Output
A structured KPI proposal with:
1. KPI list across the canonical categories: throughput, parallel
   efficiency, latency, memory, **binding** (`binding.cabi_call_overhead`,
   `binding.python_traversal_overhead`, `binding.tcl_traversal_overhead`,
   `binding.zero_copy_violations`), **service**
   (`service.flight_query_throughput`, `service.flight_p99_latency`,
   `service.plasma_handoff_throughput`), **tile**
   (`tile.frame_render_time`, `tile.compressed_frame_size`,
   `tile.lod_walk_efficiency`), and **MCP**
   (`mcp.tool_call_overhead`, `mcp.list_tools_latency`,
   `mcp.audit_log_overhead`).
2. Measurement methods, units, targets, and regression thresholds.
3. Benchmark scenarios with explicit dataset sizes.
4. Reject criteria.
5. CI gating proposal.
6. Canonical-stack callout: oneTBB + `std::jthread`, mimalloc + arena
   allocators, abseil flat hash, simdjson + glaze, Tracy +
   OpenTelemetry, mio mmap, zstd/lz4 codecs.

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
- Reject if `parallel.efficiency_at_n_threads` falls below the
  documented threshold.
- Reject if `memory.peak_rss` increases beyond the regression
  threshold.
- Reject if `memory.bytes_per_work_unit` exceeds the regression
  threshold.
- Reject if any benchmark uses an unbounded queue or buffer.
- Reject if `binding.zero_copy_violations` rises above zero.
- Reject if any binding/service/tile/MCP KPI regresses past its
  threshold.
- Reject if a canonical-stack library is replaced without satisfying
  the substitution policy in
  [`doc/library-catalog.md`](../../../doc/library-catalog.md).
- Reject if the change violates the mission-aligned reject criteria
  in [`doc/mission.md`](../../../doc/mission.md) §"Mission-Aligned
  Reject Criteria" or the composite reject criteria in
  [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  §"Composite Reject Criteria".

## Acceptance Criteria
- Mission cross-reference is present.
- All KPIs present with schema fields, including binding/service/
  tile/MCP categories.
- Memory budget categories accounted for.
- Canonical performance stack referenced.
- Reject criteria stated, including binding zero-copy and
  composite reject criteria.
- CI gating mapped to phases in
  [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
