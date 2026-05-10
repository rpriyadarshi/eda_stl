---
name: technical-debt-tracker
description: Logs, classifies, and remediates technical debt items in eda_stl by editing doc/technical-debt-register.md, mapping items to severity, owner, evidence, and remediation phase. Use when the user asks to log a debt item, update the debt register, classify a deficiency, or plan remediation.
disable-model-invocation: true
---

# Technical Debt Tracker

## When To Use
Use this skill whenever a deficiency is identified that cannot or should not
be fixed in the same change.

## Inputs
- [`doc/mission.md`](../../../doc/mission.md) (every debt item is
  weighed against the mission's required capabilities and reject
  criteria).
- [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)
- [`doc/quality-gaps-and-risks.md`](../../../doc/quality-gaps-and-risks.md)
- [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md)
- [`doc/library-catalog.md`](../../../doc/library-catalog.md)
  (library-substitution items, e.g. JsonCpp -> simdjson + glaze).
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (binding/LLM debt items, e.g. SWIG decommissioning, missing
  C-ABI).

## Output
- An update or addition to
  [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md).
- A short report summarizing the change, with the new id and severity.

## Required Fields For Every Item
- `id`: stable kebab-case identifier (e.g., `D-19`).
- `severity`: `critical` | `high` | `medium` | `low`.
- `evidence`: file path with line numbers when relevant.
- `description`: one sentence explanation.
- `remediation_phase`: which phase resolves it.
- `owner`: nominated maintainer.

## Workflow
1. Reuse existing ids; new items get the next free `D-` id.
2. Confirm severity using
   [`doc/quality-gaps-and-risks.md`](../../../doc/quality-gaps-and-risks.md).
3. Pick a remediation phase from
   [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
4. Update the register entry.
5. Verify the lifecycle state (`Logged`, `Triaged`, `Scheduled`, etc.).

## Acceptance Criteria
- Mission cross-reference is present (`doc/mission.md`).
- Every entry passes the schema.
- Every change to the register is reported back to the caller with the new
  id and severity.
- No item is added without a citation.
- Items that touch the library catalog or the binding/LLM surface
  also link to `doc/library-catalog.md` or
  `doc/binding-architecture.md` respectively.
