---
name: penpot-atomic-design
description: Structure a Penpot file as a real design system using Atomic Design — token sets and themes, page layout, naming, components, variants, shared libraries, annotations and handoff. Use when organising or auditing a Penpot file, deciding where a component belongs, naming assets, building a token hierarchy, turning loose boards into a library, or driving any of that through the Penpot MCP server. Also use when porting a Figma-shaped design system into Penpot, because the two tools disagree on more than vocabulary.
---

# Atomic Design in Penpot

Atomic Design gives you a vocabulary for *what* to build. This skill covers *where it goes*
in Penpot and *what Penpot does differently* — because a file that was organised with Figma
habits will fight you in three specific places, listed below.

## Version baseline

Verified against **Penpot 2.18** (September 2026). Two capabilities decide how a system is
organised, and both are recent:

| Capability | Since | Why it matters here |
| --- | --- | --- |
| Design tokens: colour, spacing, sizing, radius, stroke, opacity, rotation | 2.6 | Tokens, not colour styles, are the foundation layer |
| Type-related token types (font family, size, weight, letter spacing, case, decoration, number) | 2.10 | A type scale can live in tokens rather than in components |
| Component variants (property axes, variant container) | 2.10 | The third segment of a component name is now a *property*, not a name |
| Shadow tokens | 2.13 | Elevation joins the token layer |
| Import tokens from a linked library | 2.16 | Token distribution stops being a manual file copy |

Anything older than 2.10 needs the migration note in `references/06-variants.md`. On a
self-hosted instance, check the version before promising a token type: the set was filled in
over several releases.

## The three things Penpot does not do like Figma

Read these before applying any Atomic Design guide written for another tool.

**1. Tokens are file-wide, not a page.** Token sets, themes and values live in the file's
Tokens tab, not on a canvas. A "Foundations" page in Penpot is therefore a *rendered view*
of the token system for humans to look at — never its storage. Swatch rectangles painted on
a page are a drawing, not a foundation; nothing binds to them.

**2. A main component is a real shape on a real page.** It is not an invisible published
record. Delete the page and the components go with it. Page structure is therefore load
bearing, which is also why deprecated work gets *moved* to an archive page rather than
deleted, and why main instances for one level all belong on that level's page.

**3. Variants are a construct, not a naming convention.** `Button / Primary / Hover` as a
name produced a flat list of three-deep folders. In Penpot, related components are combined
into a **variant container** — a board holding every variant — and the differences become
named property axes (`Type`, `Size`, `State`). Keep naming `category/component`; put the
rest in properties.

## Build order

Each stage may only reference stages above it. Building out of order is the single most
expensive mistake in a design system, because every skipped foundation becomes a
find-and-replace across finished components.

```
1. Token sets + themes      → the only place raw values exist
2. Library colours + typographies → the assets Penpot's UI surfaces, driven by tokens
3. Icons                    → the most reused atom; everything else references them
4. Atoms                    → button, input, checkbox, badge, avatar, divider
5. Molecules                → atoms in a layout with one job (field with label + error)
6. Organisms                → navbar, form, card grid, data table
7. Templates                → page-level layout, no real content
8. Screens                  → templates with real content; the deliverable
```

## Page plan

A design-system file and a product file are two files (see
`references/08-libraries-and-publishing.md`). Inside the system file:

| # | Page | Holds |
| --- | --- | --- |
| 1 | `00 Foundations` | A rendered view of tokens: palette ramps, type scale, spacing rhythm, elevation. Documentation, not storage. |
| 2 | `01 Icons` | Icon main components, one grid, uniform box size |
| 3 | `02 Atoms` | Atom main components and their variant containers |
| 4 | `03 Molecules` | Molecule main components |
| 5 | `04 Organisms` | Organism main components |
| 6 | `05 Templates` | Content-free page layouts |
| 7 | `06 Screens` | Real content; the deliverable |
| 8 | `99 Archive` | Deprecated main components, kept alive on purpose |

Numeric prefixes are not decoration: Penpot appends new pages at the end and offers no API
to reorder them, so the prefix is what keeps the order readable after the fifth page gets
added. Emoji in page names work and scan fast in the page list — put them *after* the
number (`00 🎨 Foundations`) so sorting still holds.

## Naming

One formula, lowercase, no spaces around the separator — this is what Penpot's own
guidance uses and what maps cleanly onto code:

```
category/component        → button/primary, form/input, nav/top-bar
```

`/` creates real folders in the Assets panel, so the separator is structure, not
decoration. The variant axis does **not** belong in the name. Full rules, including what
Penpot silently does to names you set through the API, are in
`references/02-naming-conventions.md`.

## Non-negotiables

1. **No raw values in components.** Every colour, radius, spacing, font size and weight
   resolves from a token. A hardcoded hex is a future migration.
2. **Never detach an instance inside a molecule or organism.** Detaching is how a system
   quietly becomes a folder of pictures. If an atom does not fit, the atom needs a variant
   — or the difference belongs in a token.
3. **Organisms define layout, never style.** If a button must look different inside a
   navbar, that is a new variant of the button, not an override in the navbar.
4. **Depth of 3–4 nesting levels, maximum.** Deeper trees are slow to render, painful to
   override and unreadable in the Layers panel.
5. **Layout comes from flex/grid, never from invisible rectangles or manual coordinates.**
6. **One concept, one name, one source of truth.** Two components that mean the same thing
   is the failure Atomic Design exists to prevent.
7. **Every main component carries an annotation.** It shows on every copy and in the
   Inspect tab; it is the cheapest defence against misuse.
8. **Deprecate by archiving, never by deleting.** Deleting a main component breaks every
   copy in every connected file.

## Which reference to read

| Question | File |
| --- | --- |
| Where do pages, boards and main components go? | `references/01-file-and-page-structure.md` |
| What exactly do I call this? | `references/02-naming-conventions.md` |
| How do I build the token layer, themes, tiers? | `references/03-tokens-first.md` |
| How do I build icons and the first atoms? | `references/04-atoms-and-icons.md` |
| When is something a molecule vs. an organism? | `references/05-molecules-and-organisms.md` |
| How do variants and properties actually work? | `references/06-variants.md` |
| How do I document components and hand off? | `references/07-documentation-and-handoff.md` |
| How do I publish and consume a shared library? | `references/08-libraries-and-publishing.md` |
| How do I do all this through an MCP agent? | `references/09-agent-recipes.md` |
| Why did that not work / is this file healthy? | `references/10-pitfalls-and-audit.md` |

## Quick audit

Five questions that separate a design system from a pile of boards:

1. Does every page have a single stated purpose, and do main components live on the page
   matching their level?
2. Can you change the brand's primary colour in one place and see it everywhere?
3. Does the Assets panel read as folders, or as a flat list of thumbnails?
4. Do the component names survive being read out loud to a developer?
5. Is there exactly one button component — or five that all render a button?

## Sources

- Brad Frost, *Atomic Web Design* (2013): <https://bradfrost.com/blog/post/atomic-web-design/>
- Brad Frost, *Atomic Design* (full book, free online): <https://atomicdesign.bradfrost.com/>
- Penpot design systems guide: <https://help.penpot.app/user-guide/design-systems/>
- Penpot file-structure best practices: <https://help.penpot.app/mcp/design-file-structure-best-practices/>
