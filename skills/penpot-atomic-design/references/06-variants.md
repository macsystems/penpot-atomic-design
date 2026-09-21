# Variants

Variants are the biggest structural difference between a Penpot design system built today
and one built before 2.10 — or one ported from a tool with a different component model.

## The model

- A **variant container** is a board that physically holds every variant of one component,
  laid out in a horizontal flex row.
- **Properties** are the named axes along which variants differ: `Type`, `Size`, `State`.
- **Values** are the options per axis: `primary`/`secondary`, `sm`/`md`/`lg`,
  `default`/`hover`/`disabled`.
- **One variant is one unique combination** of values across all properties —
  `Type=primary + Size=md + State=hover`.
- Every variant component must have at least one property.

Designers place the component once and then pick values in the Design tab. If a combination
does not exist, Penpot offers the closest match rather than failing.

## Creating them

| Situation | How |
| --- | --- |
| One component, want a second state | Select it, `Ctrl/Cmd + K` or right-click → **Create variant** |
| Several existing components that belong together | Select them → right-click → **Combine as variants** (works from the Assets tab or the canvas) |
| Adding one more later | Drag the main component into the existing variant container |

**Combine as variants** requires the components to be on the same page and none of them to
already have variants. Property names and values are derived from the component names, which
is the one moment the old `button/primary/hover` naming pays off — it converts cleanly. Name
things properly *before* combining and the migration is nearly free.

When variants are created, the first axis is called `Property 1` with values `Value 1`,
`Value 2`. Rename both immediately: an axis called `Property 1` is a dropdown nobody can
read.

## Naming properties and values

Two equivalent routes:

- **Design tab**: click the property name to rename it, edit its values there.
- **Layers panel**: name the variant layer with the formula `property=value`, space-separated
  for multiple axes.

Conventions that pay off:

- **Property names in `PascalCase` or plain capitals** — `Type`, `Size`, `State`, `Tone`.
  They are read as labels in the UI, not as code identifiers.
- **Values in lowercase**, matching the vocabulary developers use: `primary`, `hover`,
  `disabled`.
- **Keep the same value name across components.** If the button says `disabled`, the input
  must not say `inactive`. Consistent vocabularies let a designer switch state across a whole
  screen without relearning per component.
- **Binary axes use `true`/`false`, `on`/`off` or `yes`/`no`** — Penpot detects those pairs
  and shows a toggle instead of a dropdown.

## What Penpot does *not* have

This is where imported habits break. Penpot has **no** separate component-property system
for text content or instance swapping. There is no "Label" text property and no "swap this
icon" property on a component.

The equivalents:

| Want | In Penpot |
| --- | --- |
| Change a button's label | Edit the text in the copy — a normal override |
| Swap the icon inside a component | Select the nested instance and use **component swap** |
| Show/hide a trailing icon | A boolean variant axis (`TrailingIcon=true/false`) |
| Vary colour, size, state | Variant axes |

So the classic advice "use properties instead of variants to keep the matrix small" does not
transfer. In Penpot the matrix is controlled by **building only the combinations the product
uses** — not by moving axes into a different mechanism.

## Keeping the matrix honest

- Build the combinations that exist in the product. Five types × three sizes × five states
  is 75 boards; most products use twelve of them.
- If an axis has one value everywhere, it is not an axis — delete it.
- If two axes are never combined independently, they are one axis.
- Watch for the duplicate-combination error: two variants claiming the same combination is
  flagged, and it always means an axis is missing or a value was mistyped.
- A variant container with 40 boards is a signal to split the component, not to keep going.

## When it is not a variant

Use a variant only when the differences are part of the *same pattern*. A primary and a
danger button are the same pattern. A button and a link are not, even though both are
clickable text — different semantics, different accessibility behaviour, different code.

Signals that you are looking at two components:

- The internal structure differs (different children, not different styling)
- They appear in different contexts and never substitute for each other
- Developers implement them as separate things

## Pulling a variant back out

Dragging a variant out of its container (or cut and paste outside it) turns it back into an
independent component; its new name combines the original name with its property values. A
useful escape hatch when a variant turns out to be its own component.

## Two practical cautions

- **Rename the container, not its children.** Renaming variant components from inside the
  container can deregister them from the library listing — they stay on the canvas and
  vanish from the Assets panel, which reads as data loss and is not.
- **Consistent layer names across variants are load-bearing.** Overrides survive a switch
  only when layers match by name, type and hierarchy level.

## Migrating from name-encoded states

If a file already contains `button/primary/default`, `button/primary/hover`,
`button/primary/disabled`:

1. Check the names are consistent — the conversion reads them.
2. Select the three in the Assets tab → **Combine as variants**.
3. Rename the generated property to `State`; confirm the values.
4. Rename the container to `button/primary`.
5. Repeat per type, then consider whether `Type` should become a second axis on one
   container rather than one container per type.
6. Replace old instances on screens; they do not migrate themselves.

Step 6 is the one that gets skipped, and it is the one that matters — until screens use the
variant component, both versions are live and the library reports a coverage it does not have.

## Sources

- <https://help.penpot.app/user-guide/design-systems/variants/>
- <https://penpot.app/blog/tutorial-creating-and-using-component-variants-in-penpot/>
- <https://penpot.app/release-notes/2-10-go-your-own-way>
