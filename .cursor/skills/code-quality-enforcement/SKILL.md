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
- [`doc/mission.md`](../../../doc/mission.md)
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/library-catalog.md`](../../../doc/library-catalog.md) (the
  catalog mandate; ad-hoc third-party deps are rejected).
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (binding-side rules and LLM safety rules).
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
- **Ad-hoc third-party dependency** not listed in
  [`doc/library-catalog.md`](../../../doc/library-catalog.md) and not
  reviewed by the `library-selection` skill.
- **Library types crossing the C-ABI** (`simdjson::*`, `arrow::*`,
  `absl::*`, `glaze::*`, ...). See
  [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  §"C-ABI Contract".
- **Foreign runtime on the LLM critical path** (Python, TypeScript,
  ...). Only native C++ is permitted in `binding/llm/`.

## Review Workflow
1. Read [`doc/mission.md`](../../../doc/mission.md) so the review
   inherits the charter.
2. Run all mandatory checks; cite the exact command for each gate.
3. Apply the **library catalog mandate** from
   [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
   §"Library Catalog Mandate". Reject changes that introduce
   third-party deps not in
   [`doc/library-catalog.md`](../../../doc/library-catalog.md).
4. Apply the **binding-side code quality rules** and the **LLM
   surface safety rules** from
   [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md).
5. Categorize each finding using the severity scale from
   [`doc/quality-gaps-and-risks.md`](../../../doc/quality-gaps-and-risks.md).
6. Cross-link new debt to
   [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)
   if the issue cannot be fixed in the same change.
7. Confirm cross-references in the resulting report.

## Acceptance Criteria
- Mission cross-reference is present.
- Every finding has a citation.
- Every finding has a severity.
- Every finding has either a fix or a debt-register link.
- Mandatory gate status reported even when nothing failed.
- Library catalog mandate, binding-side rules, and LLM safety rules
  are checked when relevant.
