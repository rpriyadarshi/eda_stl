---
name: eda-architecture-analysis
description: Performs deep architecture analysis of the eda_stl C++ EDA library, producing a repository map, runtime flows, build graph, and risk findings backed by file-path citations. Use when the user asks for an architecture overview of eda_stl, a repo map, a build graph review, or "how does eda_stl fit together".
disable-model-invocation: true
---

# EDA Architecture Analysis

## When To Use
Use this skill when reasoning about `eda_stl` as a whole: layout, build, test,
data-model, and bindings.

## Inputs
- The repository at `/home/rohit/src/eda_stl/`.
- The documentation under
  [`doc/repository-map.md`](../../../doc/repository-map.md),
  [`doc/build-test-ci.md`](../../../doc/build-test-ci.md),
  [`doc/rack-model-and-verification.md`](../../../doc/rack-model-and-verification.md).

## Output
Return a structured report that contains:
1. Repository map (top-level directories with purpose).
2. Build graph (CMake top-level + module subprojects + FetchContent + SWIG).
3. Test orchestration summary (CTest dispatch + custom targets).
4. Rack model lifecycle summary.
5. Risk findings cross-linked to
   [`doc/quality-gaps-and-risks.md`](../../../doc/quality-gaps-and-risks.md)
   and [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md).

Every claim must cite a file path.

## Workflow
1. Read [`doc/glossary.md`](../../../doc/glossary.md) first to align terms.
2. Read [`doc/repository-map.md`](../../../doc/repository-map.md) for the
   directory baseline.
3. Inspect [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
   for the build graph and dependencies.
4. Inspect rack and test entry points:
   - [`/home/rohit/src/eda_stl/rack/CMakeLists.txt`](../../../rack/CMakeLists.txt)
   - [`/home/rohit/src/eda_stl/rack/test/test.cpp`](../../../rack/test/test.cpp)
5. Inspect bindings:
   - [`/home/rohit/src/eda_stl/rack/swig/CMakeLists.txt`](../../../rack/swig/CMakeLists.txt)
   - [`/home/rohit/src/eda_stl/rack/swig/rack_int.i`](../../../rack/swig/rack_int.i)
6. Cross-link findings to risks and debt entries.
7. Map any recommendations to phases in
   [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).

## Report Template
Use this template for the response:

```markdown
# eda_stl Architecture Findings

## Repository Map
[bullet list with paths]

## Build Graph
[mermaid diagram + commentary]

## Test Orchestration
[CTest registration summary]

## Rack Model Lifecycle
[lifecycle summary with citations]

## Risks And Debt
[table linking each finding to a risk or debt id]

## Phase Recommendations
[mapping to implementation phases]
```

## Acceptance Criteria
- Every finding has a file-path citation.
- Output references at least the glossary and the implementation phases doc.
- No claims without evidence.
