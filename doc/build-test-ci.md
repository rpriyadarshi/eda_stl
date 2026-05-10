# Build, Test, And CI

## Purpose
Describe how the `eda_stl` core is configured, built, tested, and exercised in
CI today, identify the structural inconsistencies that exist, and define the
required CI quality gates moving forward.

## Audience
Build engineers, contributors fixing CI, and AI tools driving the
implementation playbook.

## In Scope
- Top-level CMake configuration and dependencies.
- CTest registration and module-level custom targets.
- Existing GitHub Actions workflow.
- Helper script in `scripts/build.sh`.
- Required CI gates aligned with code-quality and performance policies.

## Out of Scope
- AI tooling (relocated outside this repository).

## Cross References
- [`mission.md`](mission.md)
- [`glossary.md`](glossary.md)
- [`repository-map.md`](repository-map.md)
- [`code-quality-standards.md`](code-quality-standards.md)
- [`binding-architecture.md`](binding-architecture.md)
- [`library-catalog.md`](library-catalog.md)
- [`performance/cpp23-and-parallel-runtime.md`](performance/cpp23-and-parallel-runtime.md)
- [`technical-debt-register.md`](technical-debt-register.md)
- [`implementation/implementation-phases.md`](implementation/implementation-phases.md)

## Current Build Architecture

```mermaid
flowchart TD
    Top["CMakeLists.txt"]
    Top --> FC[FetchContent]
    FC --> GTest[GoogleTest origin/main]
    FC --> Json[JsonCpp origin/master]
    FC --> Swig[SWIG origin/master]
    Top --> Subs[add_subdirectory]
    Subs --> CMN[cmn]
    Subs --> STL[stl]
    Subs --> JSON[jsoncpp]
    Subs --> UTL[utl]
    Subs --> TMAT[tmat]
    Subs --> SIG[sig]
    Subs --> ALGO[algo]
    Subs --> RACK[rack]
    Top --> Tests[enable_testing + add_test]
    Tests --> CTest[CTest -> make run_*]
```

## Configuration Constraints
- C++17 standard with `CMAKE_CXX_STANDARD_REQUIRED True`
  ([`CMakeLists.txt`](../CMakeLists.txt)).
- Hard floor on GCC `>= 9.2.0` enforced by a `FATAL_ERROR`
  ([`CMakeLists.txt`](../CMakeLists.txt)).
- Hard requirement on Python `3.12` for SWIG bindings
  ([`CMakeLists.txt`](../CMakeLists.txt)).
- Inline assembly probe gates `-DUSE_ASM_DIV`
  ([`CMakeLists.txt`](../CMakeLists.txt)).

## Dependency Acquisition

### Current State
- GoogleTest, JsonCpp, and SWIG are obtained via `FetchContent` with floating
  branch tags (`origin/main`, `origin/master`)
  ([`CMakeLists.txt`](../CMakeLists.txt)).
- SWIG is built from source by an `execute_process` call wrapping a separate
  CMake configure/build under `${CMAKE_BINARY_DIR}/libs/swig`
  ([`CMakeLists.txt`](../CMakeLists.txt)).

### Target State (Phase 0 onward)
The dependency acquisition strategy is governed by
[`library-catalog.md`](library-catalog.md):

```mermaid
flowchart LR
    Need["Dependency"]
    Need --> Vcpkg["vcpkg manifest mode\n(default)"]
    Vcpkg -->|available| Built["Built and exposed via\nfind_package"]
    Vcpkg -->|unavailable| Cpm["CPM.cmake fallback\n(pinned commit SHA)"]
    Cpm --> Built
    Need -.legacy.-> Fetch["FetchContent\n(only with pinned SHA, Phase 0 fix)"]
    Fetch --> Built
```

- **Default**: vcpkg manifest mode. A `vcpkg.json` at the repo root
  enumerates every dependency from
  [`library-catalog.md`](library-catalog.md) with a pinned baseline.
  Lands in Phase 0 task `p0-introduce-vcpkg-manifest`.
- **Fallback**: CPM.cmake. Used only when a dependency is unavailable
  through vcpkg; commit SHA is pinned in `cmake/cpm/Dependencies.cmake`.
- **Legacy `FetchContent`**: tolerated only for the existing
  GoogleTest entry, and only with a pinned commit SHA after the
  Phase 0 task `p0-pin-fetchcontent` lands. JsonCpp and the from-source
  SWIG build are removed by `p0-jsoncpp-to-simdjson-glaze` and the
  Phase 7 SWIG decommission, respectively.

## Test Registration
- `add_test(... COMMAND make run_*)` registers tests through Make-only custom
  targets, producing fragility on non-Make generators
  ([`CMakeLists.txt`](../CMakeLists.txt)).
- C++ test binaries live under `*/test/` (e.g., `racktest`, `utltest`).
- Python tests are launched via `python test.py` from SWIG module build
  directories
  ([`rack/swig/CMakeLists.txt`](../rack/swig/CMakeLists.txt)).

## CI Workflow

```mermaid
flowchart TD
    PR[push or pull_request to main]
    PR --> Setup[setup-python 3.12 + pip install lxml]
    Setup --> Configure["cmake -B linux/build"]
    Configure --> Build[cmake --build]
    Build --> Test[ctest --output-on-failure]
```

- Workflow file: [`.github/workflows/cmake-single-platform.yml`](../.github/workflows/cmake-single-platform.yml).
- Configures into `linux/build`, but the repository no longer keeps the
  legacy `linux/` directory; this is a known defect to fix in Phase 0.
- Only `lxml` is installed for Python; SWIG/Python runtime needs are not
  staged in CI.

## Helper Script
- [`scripts/build.sh`](../scripts/build.sh) is a legacy driver assuming
  `linux/`-rooted layouts and accepting subcommands beyond what the help
  message describes (`getdep`, `googletest`, `jsoncpp`, `swig`, `genscript`).
- It does not initialize `PARALLEL` before validating it; missing `-j` causes
  exit code `5`.
- It deletes the `libs/` directory unconditionally during `getdep`.

## Known Defects (Cross-Linked)
- `linux/` path drift across [`README.md`](../README.md),
  [`.gitignore`](../.gitignore), and the GitHub Actions workflow.
- Floating `FetchContent` tags compromise reproducibility.
- GCC and Python floors limit adopter base.
- CTest depends on Make targets; non-Make generators will fail to dispatch
  `run_*` commands.
- See [`technical-debt-register.md`](technical-debt-register.md) for full
  ownership and remediation phase mapping.

## Required CI Quality Gates
- Configure and build succeed on supported compilers (matrix to be defined
  in [`performance/cpp23-and-parallel-runtime.md`](performance/cpp23-and-parallel-runtime.md)).
- `ctest --output-on-failure` passes.
- `clang-format --dry-run --Werror` passes.
- `clang-tidy` clean for the configured check set.
- `cppcheck` clean for the configured rule set.
- ASan and UBSan test runs succeed on test suites.
- TSan run succeeds on parallel scenario tests once Phase 5 begins.
- Coverage threshold met for the active phase.
- Throughput regression check passes against the recorded baseline
  (Google Benchmark from Phase 5).
- Memory-ceiling regression check passes against the recorded
  baseline.
- ABI compatibility check passes when the API enters a stable tier.
- Doxygen documentation generation succeeds.

### Binding And Service Gates
- `cabi-lint` (Phase 3): rejects template signatures and `binding-impl`
  types crossing the C-ABI; rejects exception escapes.
- `llm-card-lint` (Phase 4): the `system-card.yaml`,
  `capability-registry.yaml`, and `allowlist.yaml` are consistent with
  the current C-ABI and schemas.
- `binding-zero-copy` gate (Phase 5): test harness reports zero hot-path
  copies in nanobind, cpptcl, and `mcp_server` traversal paths.
- `binding-bench` (Phase 5): binding/service/tile/MCP KPIs from
  [`performance/cpp23-and-parallel-runtime.md`](performance/cpp23-and-parallel-runtime.md)
  pass their regression thresholds.

### Library And Mission Gates
- `library-catalog-drift` (Phase 1+): every third-party dependency in
  the build resolves to an entry in
  [`library-catalog.md`](library-catalog.md); unknown dependencies fail
  the build.
- `license-audit` (Phase 1+): no GPL- or LGPL-only library is present;
  failed audit fails the build.
- `mission-alignment-review` (advisory Phase 0-6, soft gate Phase 7):
  invoked on changes to public surfaces, governance, and dependencies;
  reports against [`mission.md`](mission.md) §"Mission-Aligned Reject
  Criteria".

### Profiler And Telemetry Hooks
- Tracy build profile available behind `-DEDA_ENABLE_TRACY=ON`
  (Phase 5).
- OpenTelemetry C++ integrated for `eda_server` and `mcp_server`
  (Phase 7); prometheus-cpp metrics exposition.

## CI Gating Flow

```mermaid
flowchart LR
    Source[Source change]
    Source --> Format[clang-format check]
    Format --> Lint[clang-tidy + cppcheck]
    Lint --> Compile[Build matrix]
    Compile --> Tests[ctest]
    Tests --> Sanitizers[ASan + UBSan + TSan]
    Sanitizers --> Coverage[Coverage threshold]
    Coverage --> Throughput[Throughput regression]
    Throughput --> Memory[Memory ceiling regression]
    Memory --> ABI[ABI compatibility]
    ABI --> Docs[Doxygen]
    Docs --> Merge[Eligible to merge]
```

## Acceptance Criteria For This Document
- Build graph documented with a mermaid diagram.
- CI workflow documented and inconsistencies listed.
- vcpkg manifest mode declared as the default dependency acquisition;
  CPM.cmake declared as fallback.
- All required CI gates enumerated, including binding, service-plane,
  library-catalog drift, license audit, and mission alignment.
- Defect list cross-linked to the technical debt register.
- Mission cross-reference present.
- File-path citations present for every nontrivial claim.

## Implementation Phase Mapping
- Phase 0 fixes path drift, pinning, CTest fragility, JsonCpp swap, and
  introduces vcpkg manifest mode.
- Phase 1 introduces the format/lint/sanitizer/coverage gates plus
  spdlog/fmt/CLI11 adoption and library-catalog drift / license-audit
  gates.
- Phase 2 enables the full C++23 build matrix.
- Phase 3 introduces the C-ABI lint and the binding export targets.
- Phase 4 introduces `llm-card-lint`, the MCP server, nanobind, and
  cpptcl wrappers.
- Phase 5 onward enforces throughput and memory regression gates and
  the binding zero-copy gate.
- Phase 6 introduces tile-protocol gates.
- Phase 7 enforces ABI gates, the mission-alignment soft gate, and
  the SWIG decommission.
