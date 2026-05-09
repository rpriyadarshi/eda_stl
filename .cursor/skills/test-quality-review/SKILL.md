---
name: test-quality-review
description: Reviews and improves the eda_stl test suite (GTest C++ tests, SWIG Python parity, and CTest registration), identifying missing coverage and parity gaps with explicit citations. Use when the user asks to review tests, improve test coverage, audit GTest fixtures, fix CTest wiring, or close C++/Python parity gaps.
disable-model-invocation: true
---

# Test Quality Review

## When To Use
Use this skill when assessing or extending tests in `eda_stl`.

## Inputs
- [`/home/rohit/src/eda_stl/rack/test/test.cpp`](../../../rack/test/test.cpp)
- [`/home/rohit/src/eda_stl/rack/swig/test.py`](../../../rack/swig/test.py)
- [`/home/rohit/src/eda_stl/algo/test/test.cpp`](../../../algo/test/test.cpp)
- [`/home/rohit/src/eda_stl/sig/test/test.cpp`](../../../sig/test/test.cpp)
- [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
- [`doc/rack-model-and-verification.md`](../../../doc/rack-model-and-verification.md)
- [`doc/build-test-ci.md`](../../../doc/build-test-ci.md)

## Output
A test-quality report containing:
1. Inventory of test binaries and Python tests.
2. Coverage gaps (named and cited).
3. C++/Python parity analysis with priorities.
4. Action items linked to phases (Phase 0 for CTest dispatch, Phase 1 for
   `#if 0` removal, Phase 4 for parity uplift).

## Workflow
1. Confirm CTest registration in `CMakeLists.txt`.
2. Inventory tests:
   - `rack/test`: deep hierarchy build, verify, clone, dissolve, flat verify.
   - `rack/swig/test.py`: SWIG parity (currently partial).
   - `algo/test`: empty `main` (D-06).
   - `sig/test`: signal-only (D-07).
3. Identify coverage gaps tied to debt items in
   [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md).
4. Propose new test cases for parity (clone, dissolve, multi-pin
   iteration) and for `algo`/`sig` real coverage.
5. Recommend execution-quality improvements (sanitizer/coverage gates per
   [`doc/build-test-ci.md`](../../../doc/build-test-ci.md)).

## Forbidden Practices
- Tests with empty `main()`.
- Tests that bypass orchestration (e.g., calling internal helpers without
  exercising the public API surface).
- Mock or fake artifacts; tests run real builds and real bindings.

## Acceptance Criteria
- Every gap has a file-path citation.
- Each recommendation is mapped to a phase and to a debt item.
- No fabricated coverage numbers.
