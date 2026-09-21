# Naming conventions

Naming is where most design files come apart. It is also the only part of a design system
that costs nothing to get right at the start and is brutally expensive to fix later, because
every rename ripples through instances, developer references and muscle memory.

## The formula

```
category/component
```

Lowercase, singular category, hyphenated words, **no spaces around the slash**:

```
button/primary            form/input              nav/top-bar
button/secondary          form/checkbox           nav/side-rail
icon/arrow-right          card/product            feedback/toast
```

The slash is not cosmetic: renaming an asset `buttons/alert-button` moves it into a
`buttons` folder in the Assets panel. Nested folders come from nested slashes. A flat list
of 200 thumbnails and a browsable tree differ only by this character.

## What does *not* go in the name

**States, sizes and styles.** Since variants exist, `button/primary/hover` is the old way.
The modern equivalent is one component `button/primary` with a `State` property whose values
include `hover`. Writing the axis into the name gives you a folder of near-duplicates that
no one can swap between. See `references/06-variants.md`.

**Appearance.** Name what a thing *is*, not what it looks like:

| Write | Not |
| --- | --- |
| `button/danger` | `button/red` |
| `card/featured` | `card/special-version` |
| `text/heading-large` | `text/heading-1` |

Appearance names are lies waiting to happen: the day `button/red` turns orange, the name is
actively misleading, and nobody will dare rename it because instances reference it.

**Redundant context.** Inside `form/input`, a child layer called `form-input-label` says
nothing the tree did not already say. Call it `label`.

## Layer names inside a component

Name layers by function: `background`, `icon-leading`, `label`, `helper-text`, `divider`.
Never leave `rectangle 23` or `Group 7` in a component you intend to ship.

This matters more in Penpot than in a tool where nobody looks at layers, for two reasons:

- **Overrides survive variant switching by matching layer name, type and hierarchy level.**
  A layer called `label` in one variant and `Label copy` in another loses its override the
  moment a designer switches between them.
- **Inspect shows the tree to developers.** Layer names are part of the handoff surface.

## Aligning with code

The closer design names sit to code identifiers, the cheaper the handoff. If the codebase
calls it `btn-primary`, the component is `button/primary` — never `button/blue` or
`button/main`. Names do not have to be identical; they have to be mechanically derivable.

The same applies to tokens: `color.button.primary.bg` maps onto a CSS custom property
without a translation table, which is the entire point of a token name.

## Token names use dots

Tokens are the one place where `/` is **not** the separator. Token names are dot-paths and
nest into folders in the Tokens tab:

```
color.base.neutral.100        spacing.md         radius.lg
color.bg.default              font.size.body     border.width.hairline
```

Token *set* names, confusingly, do use `/` for folders (`theme/light`, `theme/dark`). Sets
are containers; tokens are paths. Full detail in `references/03-tokens-first.md`.

## What Penpot does to names behind your back

These are real behaviours worth knowing before a bulk rename, and every one of them makes a
naive "find by name" comparison fail:

- **A library colour keeps only the last segment in `name`.** Created as `brand/navy`, it
  reads back as `name = "navy"` with `path = "brand"`. Compare both or you will create a
  second copy of everything.
- **Setting a component's name with a path *appends* to the existing path.** Run it twice
  and you get `card/preview/card/preview/card`. Set `path` and `name` separately.
- **Registering a component prepends its path to the source shape's name.** A component
  `icon/account` built from a shape called `icon / account` leaves the shape named
  `icon / icon / account`. Rename the shape afterwards.
- **Renaming a component renames its main instance too.** Reach a component from its shape
  rather than matching names during a cleanup — names are unreliable mid-operation.
- **Hex values read back lowercase.** Written `#001F3F`, read `#001f3f`. Case-insensitive
  comparison or false alarms.

## Anti-patterns

| Anti-pattern | Why it hurts |
| --- | --- |
| `Button / Primary / Default` (spaces, capitals, state in name) | Three-deep folders, no swap UI, does not match code |
| `Component 12`, `Frame 4`, `Group copy` | Unsearchable, meaningless in Inspect |
| `button-primary-hover-large-icon` | Encodes four axes in a string that cannot be filtered |
| Numbers as meaning: `heading/1` | Breaks when a step is inserted; `heading/large` does not |
| Emoji inside component names | Fine on pages, painful in search, exports and code mapping |
| Mixed separators (`nav_top/Bar-2`) | Folder tree turns into noise |

## Sources

- <https://help.penpot.app/user-guide/design-systems/assets/>
- <https://help.penpot.app/mcp/design-file-structure-best-practices/>
