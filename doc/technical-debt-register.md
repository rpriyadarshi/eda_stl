# Technical Debt Register

## Purpose
Maintain the authoritative, classified list of technical debt items in
`eda_stl`, with severity, evidence, owner, and remediation phase mapping.

## Audience
Maintainers prioritizing remediation, reviewers gating new debt, and AI tools
driving phase execution and the `technical-debt-tracker` skill.

## In Scope
- Build, test, code, and documentation debt visible in the repository today.
- Newly introduced debt items as they are logged.

## Out of Scope
- Risks not classified as debt (see
  [`quality-gaps-and-risks.md`](quality-gaps-and-risks.md)).

## Cross References
- [`glossary.md`](glossary.md)
- [`code-quality-standards.md`](code-quality-standards.md)
- [`quality-gaps-and-risks.md`](quality-gaps-and-risks.md)
- [`build-test-ci.md`](build-test-ci.md)
- [`extensibility-contract.md`](extensibility-contract.md)
- [`implementation/implementation-phases.md`](implementation/implementation-phases.md)

## Severity Scale
- `critical`: blocks reproducibility, correctness, or release.
- `high`: degrades reliability, security, or maintainability.
- `medium`: notable but contained; remediate when phase-aligned.
- `low`: cosmetic or minor.

## Debt Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Logged
    Logged --> Triaged
    Triaged --> Scheduled
    Scheduled --> InRemediation
    InRemediation --> Verified
    Verified --> Closed
    Triaged --> Wontfix
    Wontfix --> [*]
    Closed --> [*]
```

## Debt Items

### D-01 Floating FetchContent tags
- severity: critical
- evidence: [`/home/rohit/src/eda_stl/CMakeLists.txt`](../CMakeLists.txt)
- description: GoogleTest, JsonCpp, and SWIG declared with `origin/main`
  and `origin/master` tags.
- remediation_phase: Phase 0
- owner: build maintainer

### D-02 linux/ path drift
- severity: critical
- evidence: [`/home/rohit/src/eda_stl/.github/workflows/cmake-single-platform.yml`](../.github/workflows/cmake-single-platform.yml),
  [`/home/rohit/src/eda_stl/README.md`](../README.md),
  [`/home/rohit/src/eda_stl/.gitignore`](../.gitignore)
- description: README, CI, and gitignore reference a `linux/` directory that
  no longer exists at repo root; CMake operates from the root.
- remediation_phase: Phase 0
- owner: build maintainer

### D-03 CTest dispatch via make targets
- severity: high
- evidence: [`/home/rohit/src/eda_stl/CMakeLists.txt`](../CMakeLists.txt)
- description: `add_test(... COMMAND make run_*)` is generator-fragile.
- remediation_phase: Phase 0
- owner: build maintainer

### D-04 GCC 9.2 hard floor
- severity: high
- evidence: [`/home/rohit/src/eda_stl/CMakeLists.txt`](../CMakeLists.txt)
- description: `FATAL_ERROR` for GCC `< 9.2.0` blocks viable consumers; the
  C++23 migration must establish an updated, documented matrix.
- remediation_phase: Phase 2
- owner: compiler matrix owner

### D-05 Python 3.12 hard floor
- severity: high
- evidence: [`/home/rohit/src/eda_stl/CMakeLists.txt`](../CMakeLists.txt)
- description: `find_package(Python3 3.12 REQUIRED ...)` constrains
  bindings consumers.
- remediation_phase: Phase 2
- owner: bindings maintainer

### D-06 Empty algo test
- severity: high
- evidence: [`/home/rohit/src/eda_stl/algo/test/test.cpp`](../algo/test/test.cpp)
- description: `main` returns immediately; no GTest cases.
- remediation_phase: Phase 4
- owner: algo maintainer

### D-07 Sig test signal-only
- severity: medium
- evidence: [`/home/rohit/src/eda_stl/sig/test/test.cpp`](../sig/test/test.cpp)
- description: Custom `main` exercises signal handling; no GTest cases
  despite linking GTest.
- remediation_phase: Phase 4
- owner: sig maintainer

### D-08 Dead #if 0 blocks in rack tests
- severity: medium
- evidence: [`/home/rohit/src/eda_stl/rack/test/test.cpp`](../rack/test/test.cpp)
- description: Large dead blocks containing legacy hierarchy and dump tests.
- remediation_phase: Phase 1
- owner: rack maintainer

### D-09 Dead #if 0 in rack public headers
- severity: medium
- evidence: [`/home/rohit/src/eda_stl/rack/include/rackinc.h`](../rack/include/rackinc.h)
- description: Commented-out includes that obscure the header surface.
- remediation_phase: Phase 1
- owner: rack maintainer

### D-10 SWIG parity gaps
- severity: medium
- evidence: [`/home/rohit/src/eda_stl/rack/swig/test.py`](../rack/swig/test.py),
  [`/home/rohit/src/eda_stl/rack/swig/rack_int.i`](../rack/swig/rack_int.i)
- description: Python tests omit clone/dissolve/multi-pin iteration;
  several `%include`s are commented out.
- remediation_phase: Phase 4
- owner: bindings maintainer

### D-11 No install/export targets
- severity: high
- evidence: [`/home/rohit/src/eda_stl/CMakeLists.txt`](../CMakeLists.txt)
- description: No `install()`, `EXPORT`, or package config files; consumers
  cannot do `find_package(eda)`.
- remediation_phase: Phase 3
- owner: packaging maintainer

### D-12 Stub navigator dfs
- severity: medium
- evidence: [`/home/rohit/src/eda_stl/algo/include/navigatorbase.h`](../algo/include/navigatorbase.h)
- description: `BasicNavigatorBase::dfs` returns false unconditionally.
- remediation_phase: Phase 4
- owner: algo maintainer

### D-13 SWIG symlink post-build steps
- severity: low
- evidence: [`/home/rohit/src/eda_stl/rack/swig/CMakeLists.txt`](../rack/swig/CMakeLists.txt)
- description: `create_symlink` post-build commands assume POSIX FS.
- remediation_phase: Phase 3
- owner: packaging maintainer

### D-14 scripts/build.sh defects
- severity: medium
- evidence: [`/home/rohit/src/eda_stl/scripts/build.sh`](../scripts/build.sh)
- description: `PARALLEL` validated before initialization; destructive
  `libs/` removal during `getdep`; help text mismatches accepted targets.
- remediation_phase: Phase 0
- owner: build maintainer

### D-15 No clang-format/clang-tidy/cppcheck baseline
- severity: high
- evidence: repository-wide
- description: No formatter or linter configuration exists.
- remediation_phase: Phase 1
- owner: code quality owner

### D-16 No sanitizer or coverage gates in CI
- severity: high
- evidence: [`/home/rohit/src/eda_stl/.github/workflows/cmake-single-platform.yml`](../.github/workflows/cmake-single-platform.yml)
- description: CI builds and tests only; no ASan/UBSan/TSan/coverage.
- remediation_phase: Phase 1
- owner: CI owner

### D-17 No ABI/API tier policy
- severity: high
- evidence: repository-wide
- description: No declared API tiers, deprecation policy, or ABI checks.
- remediation_phase: Phase 3
- owner: API governance owner

### D-18 No throughput/memory regression baselines
- severity: high
- evidence: repository-wide
- description: No benchmarks, baselines, or regression gates exist.
- remediation_phase: Phase 5
- owner: performance owner

## Acceptance Criteria For This Document
- Severity scale defined.
- Every debt item has id, severity, evidence path, description, remediation
  phase, and owner.
- Lifecycle diagram present.
- Cross-references present.
