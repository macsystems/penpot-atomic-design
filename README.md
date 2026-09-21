# penpot-atomic-design

An agent skill for structuring a
**[Penpot](https://penpot.app)** file as a real design system, using Atomic Design as the
organising model.

It answers the questions that come up while a file is being built: where does this component
live, what is it called, what belongs in a token, when is something a variant, what gets
documented, and what does Penpot do differently from the tool the team came from.

The skill follows the open `SKILL.md` convention — a directory with a Markdown entry point
and reference files — so it works with any agent that reads that format.

## What it covers

| File | Topic |
| --- | --- |
| `SKILL.md` | Build order, page plan, naming formula, non-negotiables, quick audit |
| `01-file-and-page-structure.md` | Teams → projects → files → pages → boards; the page plan; why main components pin the structure |
| `02-naming-conventions.md` | The naming formula, slash grouping, layer names, aligning with code, what Penpot does to names |
| `03-tokens-first.md` | Token types, sets, themes and theme groups, the three tiers, references and maths, DTCG import/export |
| `04-atoms-and-icons.md` | Icon set contract, stroke vs fill, SVG import losses, the button atom, the first atom set |
| `05-molecules-and-organisms.md` | Where the line runs, composition rules, nesting depth, editing inside component copies |
| `06-variants.md` | Variant containers, properties and values, what Penpot has no equivalent for, migrating from name-encoded states |
| `07-documentation-and-handoff.md` | Annotations, documentation boards, the Foundations page as a living spec, deprecation |
| `08-libraries-and-publishing.md` | Publishing, consuming, why tokens are imported rather than linked, versioning, multi-brand |
| `09-agent-recipes.md` | MCP recipes: inventory, token foundation, library colours, component registration, variant containers, audits |
| `10-pitfalls-and-audit.md` | The nine expensive mistakes, a table of Penpot-specific traps, and a full audit checklist |

## Why a Penpot-specific skill

Most Atomic Design guidance was written for a different tool, and three of Penpot's design
decisions make that guidance actively wrong:

1. **Tokens are file-wide, not a page.** A "Foundations" page is a rendered view of the token
   system, never its storage. Swatches painted on a canvas bind to nothing.
2. **A main component is a real shape on a real page.** Delete the page and the components go
   with it — which is why page structure is load bearing and why deprecation means archiving.
3. **Variants are a construct, not a naming convention.** Since 2.10, `button/primary/hover`
   is the old way; the state belongs in a property axis on a variant container.

The skill also carries the behaviours that are easy to mistake for bugs: references that stay
unresolved while their set is inactive, token bindings that reset partial opacity, outline
icons that ignore fill changes, and SVG imports that silently drop gradients.

## Version baseline

Verified against **Penpot 2.18** (September 2026). Design tokens require 2.6 or newer (the
type-related token types landed in 2.10, shadows in 2.13); component variants require 2.10. The variant chapter is the one that ages fastest —
check the [release notes](https://penpot.app/release-notes) before relying on anything
version-specific.

## Installation

### As a plain folder (any agent)

A skill is a directory. Copy or symlink it into wherever your agent looks for skills.

```bash
# per user — available in every project
ln -s "$PWD/skills/penpot-atomic-design" <your-agent-skills-dir>/penpot-atomic-design

# or per project, committed and shared with the team
cp -r skills/penpot-atomic-design <project>/<agent-skills-dir>/
```

Agents disagree on where that directory lives (`~/.config/…/skills`, `.gemini/skills/`,
`.agent/skills/`, a project-local `skills/`). The folder itself is tool-agnostic — symlink
it wherever your setup expects skills, and a `git pull` keeps it current.

### As a plugin

This repository is also a plugin marketplace, so agents that support that format can install
it and keep it up to date:

```shell
/plugin marketplace add macsystems/penpot-atomic-design
/plugin install penpot-atomic-design@penpot-atomic-design
```

CLI equivalents:

```bash
claude plugin marketplace add macsystems/penpot-atomic-design
claude plugin install penpot-atomic-design@penpot-atomic-design
```

`/plugin marketplace update penpot-atomic-design` refreshes the catalog;
`/plugin update penpot-atomic-design` then applies a new release. Because the plugin declares
an explicit `version`, installed copies only move when that number changes — so **bump
`version` in both `.claude-plugin/plugin.json` and the marketplace entry** whenever the
content changes materially, for example a new Penpot baseline. `claude plugin tag` creates a
matching git tag and checks that the two manifests agree.

No authentication is needed — the repository is public.

To try it without installing:

```bash
claude --plugin-dir /path/to/penpot-atomic-design
```

## Using it with the Penpot MCP server

`09-agent-recipes.md` assumes a connected Penpot MCP session: the server acts on the focused
page of the active browser tab, through the Penpot MCP plugin. Setup instructions are in
[Penpot's MCP documentation](https://help.penpot.app/mcp/). The recipes are written to be
idempotent and to verify structurally, because MCP writes are immediate and have no undo.

## Verifying it loaded

Ask the agent something only the skill would know, for example *"why would a new token set
appear to do nothing?"* — the answer should mention that new sets start inactive and that a
reference into an inactive set resolves to nothing rather than breaking.

## Contributing

Corrections welcome, especially where Penpot has moved on. Two ground rules:

1. **Cite the source.** Prefer Penpot's own documentation, release notes, plugin API docs, or
   a reproducible observation over recollection.
2. **Keep it product-neutral.** The skill describes Penpot and Atomic Design, not any
   particular product built with them.

## Credits

Atomic Design is Brad Frost's model — the
[original article](https://bradfrost.com/blog/post/atomic-web-design/) (2013) and the
[book](https://atomicdesign.bradfrost.com/), which is free to read online. Penpot is
© Kaleidos and is not affiliated with this repository.

## License

Apache-2.0.
