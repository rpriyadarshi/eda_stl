---
name: ci-hardening-core
description: Hardens the eda_stl CI workflow by aligning paths, pinning dependencies, and adding format/lint/sanitizer/coverage/perf/memory/ABI gates. Use when the user asks to harden CI, fix the GitHub Actions workflow, add quality gates, or stabilize CTest dispatch.
disable-model-invocation: true
---

# CI Hardening For Core

## When To Use
Use this skill when modifying or reviewing
[`/home/rohit/src/eda_stl/.github/workflows/cmake-single-platform.yml`](../../../.github/workflows/cmake-single-platform.yml)
or planning new CI gates.

## Inputs
- [`doc/mission.md`](../../../doc/mission.md) (the charter; CI
  guards the public-utility quality bar).
- [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
- [`/home/rohit/src/eda_stl/.github/workflows/cmake-single-platform.yml`](../../../.github/workflows/cmake-single-platform.yml)
- [`doc/build-test-ci.md`](../../../doc/build-test-ci.md)
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/library-catalog.md`](../../../doc/library-catalog.md)
  (`library-catalog-drift` and `license-audit` gates).
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (`cabi-lint`, `llm-card-lint`, binding-zero-copy gates).

## Output
A CI plan including:
1. Path alignment fix (`build/` instead of `linux/build`).
2. Required gates per phase.
3. Job topology (parallel vs serial).
4. Failure surfacing rules.

## Required Gates Across Phases
- Phase 0: build + ctest; vcpkg manifest mode wiring.
- Phase 1: clang-format dry-run, clang-tidy, cppcheck, ASan, UBSan,
  `library-catalog-drift`, `license-audit`.
- Phase 2: full C++23 matrix.
- Phase 3: `cabi-lint` (no template signatures or `binding-impl`
  types crossing the C-ABI).
- Phase 4: coverage threshold, sanitizers across all suites,
  `llm-card-lint`, binding parity tests.
- Phase 5: throughput regression gate, `binding-zero-copy` gate,
  `binding-bench` (binding/service/tile/MCP KPIs).
- Phase 6: memory ceiling regression gate, tile protocol gates.
- Phase 7: ABI compatibility gate (`p7-abi-check-ci` and
  `p7-cabi-abi-gate`), allowlist governance, mission-alignment soft
  gate, OTel/prom telemetry.
- Phase 8: technical-debt SLO gate.

## Failure Surfacing
- Each gate must produce a structured artifact (JUnit, JSON) attached to
  the run.
- Failure must short-circuit only its own gate path, not unrelated lanes.

## Workflow
1. Audit the current workflow file.
2. Propose a gate diagram (mermaid).
3. Generate concrete YAML edits.
4. Validate with a dry run when possible.
5. Cross-link to debt items D-02, D-15, D-16, D-17, D-18.

## Acceptance Criteria
- Mission cross-reference is present (`doc/mission.md`).
- Path drift fix included.
- New gates mapped to phases, including binding/library/mission gates
  (`cabi-lint`, `llm-card-lint`, `binding-zero-copy`,
  `binding-bench`, `library-catalog-drift`, `license-audit`,
  `mission-alignment-review`).
- Output includes a mermaid diagram of the resulting workflow topology.
- All edits cite YAML keys explicitly.
