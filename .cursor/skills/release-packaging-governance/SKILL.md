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
- [`/home/rohit/src/eda_stl/CMakeLists.txt`](../../../CMakeLists.txt)
- [`/home/rohit/src/eda_stl/rack/CMakeLists.txt`](../../../rack/CMakeLists.txt)
- [`/home/rohit/src/eda_stl/utl/CMakeLists.txt`](../../../utl/CMakeLists.txt)
- [`doc/roadmap/eda-stl-library.md`](../../../doc/roadmap/eda-stl-library.md)
- [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md)

## Output
A packaging and governance plan including:
1. Exported target names (`eda::cmn`, `eda::utl`, `eda::tmat`, `eda::sig`,
   `eda::algo`, `eda::rack`).
2. Install rules and `edaConfig.cmake` skeleton.
3. SemVer policy and ABI rules.
4. Release checklist.

## Workflow
1. Confirm there are no `install()` or `EXPORT` rules today (D-11 in
   [`doc/technical-debt-register.md`](../../../doc/technical-debt-register.md)).
2. Define module-level install rules.
3. Generate a top-level `edaConfig.cmake.in`.
4. Document SemVer mapping to API tiers.
5. Define ABI checks per
   [`doc/extensibility-contract.md`](../../../doc/extensibility-contract.md).

## Release Checklist Template
- [ ] Tag matches `vX.Y.Z`.
- [ ] Changelog updated.
- [ ] Public API tier headers reviewed.
- [ ] Deprecated symbols removed if grace period elapsed.
- [ ] ABI check passes against previous tag.
- [ ] Wheels built and verified.

## Acceptance Criteria
- Targets and install paths declared.
- SemVer/ABI rules cited from the extensibility contract.
- Output maps to Phase 3 and Phase 7 of
  [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
