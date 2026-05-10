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
- [`doc/mission.md`](../../../doc/mission.md) (the charter; every
  report must anchor on it).
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (the SSOT and best-in-class wrappers).
- [`doc/library-catalog.md`](../../../doc/library-catalog.md) (the
  best-in-class library inventory).
- [`doc/repository-map.md`](../../../doc/repository-map.md),
  [`doc/build-test-ci.md`](../../../doc/build-test-ci.md),
  [`doc/rack-model-and-verification.md`](../../../doc/rack-model-and-verification.md).

## Output
Return a structured report that contains:
1. **Mission anchor** - one paragraph citing
   [`doc/mission.md`](../../../doc/mission.md) and tying the
   findings to the "STL for EDA" premise.
2. Repository map (top-level directories with purpose, including the
   `binding/` umbrella once Phase 3 lands).
3. Build graph (CMake top-level + module subprojects + dependency
   acquisition through vcpkg / CPM / FetchContent + SWIG transitional
   path).
4. Binding architecture summary (SSOT + wrappers per
   [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)).
5. Library catalog summary (canonical libraries per concern from
   [`doc/library-catalog.md`](../../../doc/library-catalog.md)).
6. Test orchestration summary (CTest dispatch + custom targets +
   binding parity tests).
7. Rack model lifecycle summary.
8. Risk findings cross-linked to
   [`doc/quality-gaps-and-risks.md`](../../../doc/quality-gaps-and-risks.md)
   and [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md),
   including R-19..R-27.

Every claim must cite a file path.

## Workflow
1. Read [`doc/mission.md`](../../../doc/mission.md) first - the
   charter anchors every analysis.
2. Read [`doc/glossary.md`](../../../doc/glossary.md) to align terms.
3. Read [`doc/repository-map.md`](../../../doc/repository-map.md) for the
   directory baseline (including the `binding/` umbrella).
4. Read [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
   and [`doc/library-catalog.md`](../../../doc/library-catalog.md).
5. Inspect [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
   for the build graph and dependencies.
6. Inspect rack and test entry points:
   - [`/home/rohit/src/eda_stl/rack/CMakeLists.txt`](../../../rack/CMakeLists.txt)
   - [`/home/rohit/src/eda_stl/rack/test/test.cpp`](../../../rack/test/test.cpp)
7. Inspect bindings (current and target):
   - [`/home/rohit/src/eda_stl/rack/swig/CMakeLists.txt`](../../../rack/swig/CMakeLists.txt)
     (legacy SWIG, on a deprecation path through Phase 7),
   - the binding umbrella `binding/cabi/`, `binding/python/` (nanobind),
     `binding/tcl/` (cpptcl), `binding/llm/` (mcp_server),
     `binding/server/` (eda_server), `binding/web/` (tile gateway)
     once those phases land.
8. Cross-link findings to risks and debt entries (R-19..R-27, D-19..D-27).
9. Map any recommendations to phases in
   [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).

## Report Template
Use this template for the response:

```markdown
# eda_stl Architecture Findings

## Mission Anchor
[one paragraph citing doc/mission.md]

## Repository Map
[bullet list with paths]

## Build Graph
[mermaid diagram + commentary; cite vcpkg/CPM/FetchContent posture]

## Binding Architecture
[SSOT + wrappers per doc/binding-architecture.md]

## Library Catalog
[canonical libraries per concern with citations]

## Test Orchestration
[CTest registration summary; binding parity tests when present]

## Rack Model Lifecycle
[lifecycle summary with citations]

## Risks And Debt
[table linking each finding to a risk or debt id, including
 R-19..R-27 and D-19..D-27 where relevant]

## Phase Recommendations
[mapping to implementation phases]
```

## Acceptance Criteria
- Every finding has a file-path citation.
- Output anchors on [`doc/mission.md`](../../../doc/mission.md).
- Output references the glossary, binding architecture, library
  catalog, and the implementation phases doc.
- No claims without evidence.
