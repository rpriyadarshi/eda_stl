---
name: docs-generation-core
description: Generates and maintains the doc/ tree for eda_stl (core C++ only) using the standard document template, mermaid diagrams, file-path citations, and cross-references. Use when the user asks to create or update eda_stl documentation, doc/ files, or to align docs across the repository.
disable-model-invocation: true
---

# Core Docs Generation

## When To Use
Use this skill to create or update any markdown document under
`/home/rohit/src/eda_stl/doc/`.

## Inputs
- The repository at `/home/rohit/src/eda_stl/`.
- The existing doc set listed in
  [`doc/README.md`](../../../doc/README.md).

## Output
A markdown document that strictly follows the universal template below.

## Universal Template
```markdown
# <Title>

## Purpose
<one paragraph>

## Audience
<who this is for>

## In Scope
<bullets>

## Out Of Scope
<bullets>

## Cross References
<links>

<one or more sections covering the topic>

## Acceptance Criteria For This Document
<bullets>
```

## Required Elements
- Mermaid diagram(s) where structural relationships exist.
- File-path citations for every claim about the codebase.
- Cross-references to related docs and to relevant phases in
  [`doc/implementation/implementation-phases.md`](../../../doc/implementation/implementation-phases.md).
- **Mission cross-reference**: every new doc must include a Cross
  References link to [`doc/mission.md`](../../../doc/mission.md) and
  inherit any reject criteria stated there.
- **Library catalog and binding architecture cross-references**: when
  the doc mentions third-party libraries or interfacing, it must
  link to [`doc/library-catalog.md`](../../../doc/library-catalog.md)
  and [`doc/binding-architecture.md`](../../../doc/binding-architecture.md).

## Workflow
1. Read [`doc/mission.md`](../../../doc/mission.md) so every new doc
   inherits the charter.
2. Read [`doc/glossary.md`](../../../doc/glossary.md) before
   introducing any term.
3. Locate or create the doc under the proper subfolder (`roadmap/`,
   `performance/`, `implementation/`, root).
4. Follow the universal template.
5. Insert mermaid diagrams and file-path citations.
6. Add cross-references to mission, glossary, repository map,
   binding architecture, library catalog, and the relevant phases.
7. Update the navigation hub
   [`doc/README.md`](../../../doc/README.md) if a new doc is
   introduced.

## Forbidden
- Documents without a Cross References link to
  [`doc/mission.md`](../../../doc/mission.md).
- Documents without mermaid diagrams when structural content exists.
- Documents without acceptance criteria.
- Vague claims without file-path citations.
- Documents that paraphrase the mission charter (only
  [`doc/mission.md`](../../../doc/mission.md) defines it).

## Acceptance Criteria
- Every produced doc has all template sections.
- Every produced doc cross-references
  [`doc/mission.md`](../../../doc/mission.md).
- New docs are linked from `doc/README.md`.
- Terminology is consistent with `doc/glossary.md`.
