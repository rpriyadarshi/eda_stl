---
name: api-stability-governance
description: Classifies eda_stl symbols into public-stable, public-evolving, and internal tiers, enforces deprecation policy, and gates breaking changes against ABI rules. Use when the user asks about API stability, deprecation, public surface, ABI policy, or stability tiers in eda_stl.
disable-model-invocation: true
---

# API Stability Governance

## When To Use
Use this skill when classifying, modifying, deprecating, or reviewing symbols
in `eda_stl` headers.

## Inputs
- [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md)
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)
- The headers under
  [`/home/rohit/src/eda_stl/rack/include/`](../../../rack/include),
  [`/home/rohit/src/eda_stl/utl/include/`](../../../utl/include),
  [`/home/rohit/src/eda_stl/algo/include/`](../../../algo/include),
  [`/home/rohit/src/eda_stl/sig/include/`](../../../sig/include),
  [`/home/rohit/src/eda_stl/tmat/include/`](../../../tmat/include),
  [`/home/rohit/src/eda_stl/cmn/include/`](../../../cmn/include).

## Output
- A tier table mapping each public symbol to one of `public-stable`,
  `public-evolving`, `internal`.
- Annotation guidance (header-level comment, `[[deprecated(...)]]`, or
  documented internal location).
- A list of changes proposed and the approval path required.

## Workflow
1. Read the extensibility contract for tier definitions.
2. For each symbol or header proposed for change, classify into a tier.
3. If the change is breaking, route through deprecation policy.
4. Propose ABI checks if `public-stable` is affected.
5. Cross-link to D-17 in the technical debt register.

## Deprecation Steps
- Mark `[[deprecated("reason and migration target")]]`.
- Add a release note entry.
- Set the removal version (next major).
- File a tracking item in the technical debt register if needed.

## Acceptance Criteria
- Every symbol decision has a tier and a citation.
- Breaking changes have an explicit deprecation plan.
- ABI implications stated when public-stable is affected.
