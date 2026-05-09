---
name: code-quality-enforcement
description: Enforces eda_stl code quality (clang-format, clang-tidy, cppcheck, sanitizers, coverage, forbidden patterns) by reviewing changes and running the documented gates. Use when the user asks for a code-quality review, lint/format/sanitizer enforcement, or to check compliance with eda_stl standards.
disable-model-invocation: true
---

# Code Quality Enforcement

## When To Use
Use this skill before merging C++ changes, when reviewing PRs, or when
auditing the codebase against
[`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md).

## Inputs
- Changed C++ files.
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/build-test-ci.md`](../../../doc/build-test-ci.md)
- `.clang-format` and `.clang-tidy` (introduced in Phase 1).

## Output
A code-quality review report containing:
1. Forbidden patterns detected, with file/line citations.
2. Format / lint / sanitizer / coverage gate status.
3. Concrete fix suggestions.
4. Severity classification per finding.

## Mandatory Checks
- `clang-format --dry-run --Werror` on every modified file.
- `clang-tidy` with the configured baseline.
- `cppcheck` for the configured rule set.
- ASan + UBSan run for impacted suites.
- TSan for parallel scenarios after Phase 5.
- Coverage threshold per the active phase.

## Forbidden Patterns
- Raw `new` or `delete`.
- Raw owning pointers in public APIs.
- C-style casts.
- `using namespace` in headers.
- `#if 0` blocks.
- Unbounded queues/buffers.
- Silent error swallowing.
- Magic numbers without named constants.
- Mutable global state.

## Review Workflow
1. Run all mandatory checks; cite the exact command for each gate.
2. Categorize each finding using the severity scale from
   [`doc/quality-gaps-and-risks.md`](../../../doc/quality-gaps-and-risks.md).
3. Cross-link new debt to
   [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)
   if the issue cannot be fixed in the same change.
4. Confirm cross-references in the resulting report.

## Acceptance Criteria
- Every finding has a citation.
- Every finding has a severity.
- Every finding has either a fix or a debt-register link.
- Mandatory gate status reported even when nothing failed.
