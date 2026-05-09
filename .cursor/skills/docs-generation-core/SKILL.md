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

## Workflow
1. Read [`doc/glossary.md`](../../../doc/glossary.md) before introducing
   any term.
2. Locate or create the doc under the proper subfolder
   (`roadmap/`, `performance/`, `implementation/`, root).
3. Follow the universal template.
4. Insert mermaid diagrams and citations.
5. Add cross-references; update the navigation hub
   [`doc/README.md`](../../../doc/README.md) if a new doc is introduced.

## Forbidden
- Documents without mermaid diagrams when structural content exists.
- Documents without acceptance criteria.
- Vague claims without file-path citations.

## Acceptance Criteria
- Every produced doc has all template sections.
- New docs are linked from `doc/README.md`.
- Terminology is consistent with `doc/glossary.md`.
