---
name: cpp23-migration-planner
description: Plans and triages a C++17 to C++23 migration for eda_stl, including compiler/toolchain matrix, language feature adoption order, compatibility audit, and phased rollout. Use when the user asks about C++23 migration, raising the language level, modernizing the codebase to C++23, or upgrading the compiler floor.
disable-model-invocation: true
---

# C++23 Migration Planner

## When To Use
Use this skill when the request concerns moving the project from C++17 to a
C++23 baseline, or when proposing C++23 idioms to apply to existing code.

## Inputs
- [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
- [`doc/build-test-ci.md`](../../../doc/build-test-ci.md)
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/performance/cpp23-and-parallel-runtime.md`](../../../doc/performance/cpp23-and-parallel-runtime.md)

## Output
A structured migration plan that includes:
1. Compiler matrix (GCC, Clang, MSVC) with explicit minimum versions.
2. Feature adoption order and rationale.
3. Compatibility audit findings (what fails or warns under `-std=c++23`).
4. Phased rollout aligned with Phase 2 in
   [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
5. Risk register cross-links.

## Workflow
1. Verify the current standard in `CMakeLists.txt`.
2. Identify language features safe to introduce first
   (`std::expected`, ranges, concepts, `std::span`, `std::flat_map`).
3. Identify deprecated/removed forms (e.g., `std::random_shuffle`, raw
   `auto_ptr` if any).
4. Propose a minimal compiler matrix update.
5. Map to debt items D-04 and D-05 in
   [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md).

## Migration Order
- Step 1: enable `-std=c++23` in a CI lane while keeping default at C++17.
- Step 2: switch default to C++20 and clean warnings.
- Step 3: switch default to C++23 once the matrix supports it.
- Step 4: introduce new idioms behind the API tiers in
  [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md).

## Compiler Matrix Template
| Toolchain | Minimum Version | Tier | Notes |
|---|---|---|---|
| GCC | 13 | primary | C++23 features broadly available |
| Clang | 17 | primary | libc++ vs libstdc++ caveats |
| MSVC | recent | secondary | feature gaps tracked |

## Acceptance Criteria
- Plan output cites file paths.
- Feature adoption order justified by readability or correctness gains.
- Compiler matrix and timeline are explicit.
- Cross-references to debt items and phases included.
