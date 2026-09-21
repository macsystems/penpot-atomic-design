# Libraries and publishing

## What a shared library is

Any Penpot file can be published as a **shared library**. Its components, colours and
typographies then become available to other files **in the same team**. Tokens travel too,
but on a separate path (below).

The team boundary is absolute: a library cannot be consumed across teams. If two product
teams need the same system, they either share a team or maintain a copy — decide this before
the system has 200 components, not after.

## Publishing

Publish from the file menu or from the **Libraries** dialog in the Assets tab. The file keeps
working normally; publishing only makes its assets visible elsewhere.

**Before you publish, the file should pass this bar:**

- Every asset named to convention (`references/02-naming-conventions.md`)
- No `Component 12`, no `Board 4`, no leftover exploration on a component page
- Every main component annotated
- Tokens complete enough that a consumer is not tempted to hardcode
- One name per concept — publishing duplicates propagates them

## Consuming

In the product file: Assets tab → **Libraries** → pick the library. Its assets appear in the
sidebar under their own section, grouped by their slash paths.

Two things to know:

- **Updates are pull, not push.** When the library changes, consuming files show an update
  indicator; someone chooses when to take it. That is your release valve.
- **Disconnecting does not delete.** Already-used assets stay in the file, unlinked. Useful
  for a controlled migration; dangerous as an accident, because the file then looks fine and
  is no longer part of the system.

## Tokens are imported, not linked

Design tokens do not flow automatically from a connected library. The Libraries dialog
offers **Import tokens**, which *copies* sets and themes into the current file.

The consequence matters: an imported token set is a snapshot. It does not update when the
library changes. So:

- Import tokens deliberately, at a known point.
- Record which version was imported (a version label in the file is enough).
- Re-import when the system publishes a token change, and treat it as a change worth
  reviewing rather than a silent sync.

If tokens must stay in lockstep across many files, the export/import JSON plus a repository
is the more reliable path than manual re-imports.

## Versioning

Penpot keeps file versions, and a label on a version is the closest thing to a release tag.
Label before anything structural: a rename sweep, a variant migration, a token restructure.

For consumers, communicate changes in three buckets:

| Change | Consumer impact |
| --- | --- |
| **Additive** — new component, new token, new variant value | Safe. Take the update. |
| **Behavioural** — changed spacing, restyled state, retuned token value | Review. Screens may shift. |
| **Breaking** — renamed or removed component, removed variant axis | Coordinate. Copies will detach or lose overrides. |

Breaking changes are the reason for the archive page: deprecate, wait, then remove — never
rename-and-hope.

## Multi-brand and multi-platform

Two shapes that work:

**One file, themed.** One system file, brand differences expressed as token sets and theme
groups (`Brand: core | partner`). Best when the brands share structure and differ in values.
It is also the cheapest to maintain, because there is one component to fix.

**Core plus extension files.** A core system file publishes the shared foundation; a
brand file connects to it, imports its tokens and adds brand-specific components. Best when
the brands genuinely diverge in components, not just values.

What does not work is a copied file. Two copies drift within weeks, and nothing signals when
they have.

## Sources

- <https://help.penpot.app/user-guide/design-systems/libraries/>
- <https://help.penpot.app/user-guide/design-systems/assets/>
- <https://help.penpot.app/user-guide/design-tokens/>
