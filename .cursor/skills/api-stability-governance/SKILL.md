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
- [`doc/mission.md`](../../../doc/mission.md) (the charter; every
  governance call inherits its reject criteria).
- [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md)
  (API tiers + binding tiers).
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (the SSOT and the LLM surface stability rules).
- [`doc/library-catalog.md`](../../../doc/library-catalog.md)
  (substitution policy).
- [`doc/code-quality-standards.md`](../../../doc/code-quality-standards.md)
- [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)
- The C-ABI headers under
  [`/home/rohit/src/eda_stl/binding/cabi/include/`](../../../binding/cabi/include)
  (Phase 3+).
- The schemas under
  [`/home/rohit/src/eda_stl/binding/schemas/`](../../../binding/schemas)
  (Phase 3+).
- The system card and allowlist at
  [`/home/rohit/src/eda_stl/binding/schemas/llm/`](../../../binding/schemas/llm)
  (Phase 3+).
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
- A binding tier table for affected files: `binding-ssot`,
  `binding-wrapper`, `binding-impl`.
- Annotation guidance (header-level comment, `[[deprecated(...)]]`, or
  documented internal location).
- A list of changes proposed and the approval path required.
- LLM surface stability call: any change to the system card,
  capability registry, or allowlist with the corresponding SemVer
  implication.

## Workflow
1. Read [`doc/mission.md`](../../../doc/mission.md) and the
   extensibility contract.
2. For each symbol or header proposed for change, classify into:
   - an API tier (`public-stable`, `public-evolving`, `internal`),
   - and a binding tier (`binding-ssot`, `binding-wrapper`,
     `binding-impl`).
3. Verify SSOT discipline: no `binding-impl` types in
   `binding-ssot`; no template signatures or library types crossing
   the C-ABI; the LLM surface (system card, capability registry,
   allowlist) is consistent with the C-ABI plus schemas.
4. If the change is breaking, route through deprecation policy and a
   public RFC (see [`doc/mission.md`](../../../doc/mission.md)
   §"Governance").
5. Propose ABI checks if `public-stable` is affected. For the C-ABI,
   apply the gate `p7-cabi-abi-gate` per
   [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
6. Cross-link to D-17 (no ABI/API tier policy yet) and D-21 (no
   C-stable ABI yet) in the technical debt register.
7. Apply the mission-aligned reject criteria from
   [`doc/mission.md`](../../../doc/mission.md) and the composite
   reject criteria from
   [`doc/binding-architecture.md`](../../../doc/binding-architecture.md).

## Deprecation Steps
- Mark `[[deprecated("reason and migration target")]]`.
- Add a release note entry.
- Set the removal version (next major).
- File a tracking item in the technical debt register if needed.

## Acceptance Criteria
- Every symbol decision has both an API tier and a binding tier with
  a citation.
- Breaking changes have an explicit deprecation plan and an RFC
  reference where applicable.
- ABI implications stated when `public-stable` or `binding-ssot` is
  affected.
- LLM surface SemVer implications stated when the system card,
  capability registry, or allowlist is affected.
- Mission cross-reference is present.
