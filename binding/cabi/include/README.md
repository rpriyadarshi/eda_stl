# C-ABI Headers (`eda_c_*.h`)

> **This directory is a placeholder.** The C-stable ABI headers land
> in **Phase 3** (task `p3-cabi-define`) per
> [`/home/rohit/src/eda_stl/doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
>
> This README exists so cross-references in
> [`/home/rohit/src/eda_stl/doc/binding-architecture.md`](../../../doc/binding-architecture.md)
> and the companion skills resolve before the Phase 3 task fires.

## What Lives Here (After Phase 3)

The single source of truth for all cross-language interfacing:

- `eda_c_rack.h` - opaque `eda_rack_t` plus `eda_rack_create`,
  `eda_rack_destroy`, `eda_rack_find_design`, ...
- `eda_c_design.h` - opaque `eda_design_t` plus its lifecycle
  functions.
- `eda_c_module.h` - opaque `eda_module_t` plus its lifecycle
  functions.
- `eda_c_error.h` - the error model (`eda_last_error`,
  `eda_error_message`, `eda_error_category`).
- `eda_c_version.h` - `EDA_ABI_VERSION_MAJOR/MINOR/PATCH` macros.

## Rules (Authoritative In `doc/binding-architecture.md`)

- Every entry point is `extern "C"`.
- Every domain object is exposed only through a forward-declared
  opaque pointer typedef.
- Every fallible function returns an `int32_t` status code.
- No exceptions cross this boundary.
- No template signatures cross this boundary.
- No `binding-impl` library types (e.g., `simdjson::*`, `arrow::*`,
  `absl::*`, `glaze::*`) cross this boundary.

## Related

- [`/home/rohit/src/eda_stl/doc/mission.md`](../../../doc/mission.md) -
  the charter.
- [`/home/rohit/src/eda_stl/doc/binding-architecture.md`](../../../doc/binding-architecture.md) -
  the SSOT design and rules.
- [`/home/rohit/src/eda_stl/doc/extensibility-contract.md`](../../../doc/extensibility-contract.md) -
  binding tiers and SemVer policy.
- [`/home/rohit/src/eda_stl/.cursor/skills/binding-architecture/SKILL.md`](../../../.cursor/skills/binding-architecture/SKILL.md) -
  enforcement skill.
- [`/home/rohit/src/eda_stl/.cursor/skills/api-stability-governance/SKILL.md`](../../../.cursor/skills/api-stability-governance/SKILL.md) -
  API/binding tier governance.
