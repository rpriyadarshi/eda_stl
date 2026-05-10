---
name: release-packaging-governance
description: Designs install/export targets, package configs, versioning, and release governance for eda_stl, enabling find_package(eda) and downstream wheel builds. Use when the user asks about packaging, install rules, find_package, exported targets, semver, ABI compatibility, or release governance for eda_stl.
disable-model-invocation: true
---

# Release Packaging And Governance

## When To Use
Use this skill when defining, reviewing, or modifying how `eda_stl` is
distributed, versioned, or consumed downstream.

## Inputs
- [`doc/mission.md`](../../../doc/mission.md)
- [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
- [`/home/rohit/src/eda_stl/rack/CMakeLists.txt`](../../../rack/CMakeLists.txt)
- [`/home/rohit/src/eda_stl/utl/CMakeLists.txt`](../../../utl/CMakeLists.txt)
- [`doc/roadmap/eda-stl-library.md`](../../../doc/roadmap/eda-stl-library.md)
  (mission-aligned release lanes).
- [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md)
- [`doc/binding-architecture.md`](../../../doc/binding-architecture.md)
  (SSOT and wrappers; release lanes derive from this).
- [`doc/library-catalog.md`](../../../doc/library-catalog.md).

## Output
A packaging and governance plan including:
1. Exported target names: `eda::cmn`, `eda::utl`, `eda::tmat`,
   `eda::sig`, `eda::algo`, `eda::rack`, plus `eda::cabi` (Phase 3+).
2. Install rules and `edaConfig.cmake` skeleton.
3. **Mission-aligned release lanes** per
   [`doc/roadmap/eda-stl-library.md`](../../../doc/roadmap/eda-stl-library.md):
   `core`, `cabi`, `python` (nanobind wheel), `tcl` (cpptcl package),
   `mcp_server` (image), `eda_server` (image), `tile_gateway`
   (image). Each lane has its own SemVer.
4. SemVer policy and ABI rules across lanes.
5. Release checklist (must pass mission-alignment-review on tag).

## Workflow
1. Read [`doc/mission.md`](../../../doc/mission.md) - the release
   lanes and license posture flow from the charter.
2. Confirm there are no `install()` or `EXPORT` rules today (D-11 in
   [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)).
3. Define module-level install rules.
4. Generate a top-level `edaConfig.cmake.in` covering `eda::*` and
   the `eda::cabi` target from Phase 3.
5. Document SemVer mapping to API tiers and binding tiers.
6. Define ABI checks per
   [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md);
   for `binding-ssot` apply the cabi-specific ABI gate
   `p7-cabi-abi-gate`.
7. Define per-lane SemVer for `core`, `cabi`, `python`, `tcl`,
   `mcp_server`, `eda_server`, `tile_gateway` per
   [`doc/roadmap/eda-stl-library.md`](../../../doc/roadmap/eda-stl-library.md).
8. Apply the mission-aligned reject criteria - no paid runtime gating,
   no closed binary linkage, no GPL/LGPL-only dep.

## Release Checklist Template
- [ ] Tag matches lane prefix + `vX.Y.Z` (e.g., `python-v0.1.0`).
- [ ] Changelog updated.
- [ ] Public API tier headers reviewed.
- [ ] Binding tiers reviewed (`binding-ssot` only changes via RFC
      and SemVer major).
- [ ] Deprecated symbols removed if grace period elapsed.
- [ ] ABI check passes against previous tag (core + cabi).
- [ ] Wheels (Python) and Tcl packages built and verified.
- [ ] Container images for `mcp_server`, `eda_server`, and the tile
      gateway built and verified.
- [ ] License audit passes (no GPL/LGPL-only deps).
- [ ] `mission-alignment-review` skill returns approve.
- [ ] System card and allowlist regenerated and lint-clean.

## Acceptance Criteria
- Mission cross-reference is present.
- Targets and install paths declared, including `eda::cabi`.
- SemVer/ABI rules cited from the extensibility contract.
- Per-lane SemVer documented.
- Output maps to Phase 0 (vcpkg manifest), Phase 3
  (`p3-add-install-exports`, `p3-binding-export-targets`), Phase 4
  (wheel/tcl packages, mcp_server image), Phase 5 (eda_server
  image), Phase 6 (tile gateway image), and Phase 7 (ABI gate,
  mission gate, SWIG decommission) of
  [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
