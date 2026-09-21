# Atoms and icons

An atom is a UI element that cannot be usefully broken down further and still mean
something: a button, an input, a checkbox, an avatar, a badge, a divider, an icon.

## Build icons first

Icons are the most reused atom in any interface — buttons contain them, inputs contain them,
nav items contain them. Build them before anything that will reference them, or you will
rebuild those components later.

### The icon component contract

- **One box size for the whole set.** 24×24 is the usual base; add 16 and 32 only if the
  interface genuinely uses them. Mixed box sizes are the reason icon rows never align.
- **A board, not a group.** The board *is* the box: it holds the size even when the glyph
  inside is 18 px wide, which is what makes optical alignment work.
- **Artwork centred inside the box**, with consistent padding. An icon set where some
  glyphs bleed to the edge and others float in the middle reads as sloppy at 16 px.
- **Colour comes from a token**, never a baked hex — bound to whichever property the glyph
  actually uses (see below).
- **Name it `icon/<name>`**, lowercase, hyphenated: `icon/arrow-right`, `icon/chevron-down`.

### Stroke icons vs. fill icons

This is the one that costs people an afternoon. An icon drawn with outlines paints through
its **stroke**, not its fill. A recolour that sets fills silently does nothing and the icon
stays whatever colour it was — it looks like the token binding failed when nothing failed.

Decide the set's drawing style up front and keep it uniform, because the two styles need
different bindings, different token properties and different scaling behaviour.

A second trap follows from the first: **resizing a shape does not scale its stroke width.**
A 48 px icon scaled to 24 px keeps its original stroke, so it renders twice as heavy as its
neighbours. Import or draw icons at their target size, or scale the numbers rather than
applying a transform.

### Importing SVG

Penpot's SVG import is good but lossy in specific, predictable ways:

| What is lost | Fix before import |
| --- | --- |
| Gradients (`fill="url(#…)"`) | Replace with a solid stop, or apply the gradient after import |
| `clipPath` | Remove it; `<defs>` survives only as an inert raw block |
| `stroke-dasharray` | Apply a dashed stroke style after import |
| `rgba()` colours | Convert to hex plus a separate opacity |
| Arcs drawn as `<circle>` with a dash offset | Draw them as a `<path>` with `A` commands |
| Element `id` as a layer name | Rename after import, by document order |

A hidden background rectangle sized to the viewBox is created on import and holds the box
dimensions. Deleting it collapses the bounds to the glyph's ink extent, and every icon in
the set ends up a different size.

### Icon plugins

The Penpot hub ships icon plugins — Iconify (150+ sets), Lucide, Feather, IconFlow, All
Icons — which insert an icon as a shape. They are a fine starting point, but an inserted
icon is not yet a system asset: normalise the box size, rename it, bind its colour to a
token and register it as a component. Plugins deliver artwork, not a library.

## The button, done properly

The button teaches the pattern every other atom follows, and it is worth over-building once.

**Structure**

```
button/primary                     board, flex row, gap = {spacing.sm}
├── icon-leading                   instance of icon/*, optional
├── label                          text, typography token
└── icon-trailing                  instance of icon/*, optional
```

**Rules**

- **Flex layout, horizontal sizing `auto`.** The button must grow with its label. A fixed
  width that happens to fit "Save" will clip "Zahlungsmethode ändern" — and localisation is
  exactly where a hand-sized button fails.
- **Padding from spacing tokens**, never typed numbers.
- **Minimum touch target 44 px (iOS) / 48 dp (Android).** If the visual height is smaller,
  the interactive board is still not allowed to be.
- **Every colour bound**: background, label, border, and the icon inside.
- **States are properties, not components** — see `references/06-variants.md`.

**Axes worth having**

| Axis | Typical values |
| --- | --- |
| `Type` | `primary`, `secondary`, `tertiary`, `ghost`, `danger` |
| `Size` | `sm`, `md`, `lg` |
| `State` | `default`, `hover`, `focus`, `disabled`, `loading` |

Five types × three sizes × five states is 75 combinations. Do not build 75. Build the
combinations the product actually uses, and add the rest when a screen needs them — an
unused variant is maintenance cost with no reader.

## The rest of the first atom set

| Atom | Axes that usually matter |
| --- | --- |
| `form/input` | `State` (default/focus/error/disabled), leading icon, trailing icon |
| `form/checkbox`, `form/radio` | `Checked` (boolean), `State` |
| `form/toggle` | `On` (boolean), `Size` |
| `badge` | `Tone` (neutral/success/warning/error), `Size` |
| `avatar` | `Size`, `Type` (image/initials/placeholder) |
| `divider` | `Orientation` |
| `text/*` | Not a component — a typography token applied to text |

That last row matters: a type scale belongs in tokens, not in a component per heading level.
A `heading/h1` component is a box around a text shape that adds nothing and blocks the
reflow the layout needs.

## Boolean axes

When an axis is genuinely binary, name its values `true`/`false`, `on`/`off` or `yes`/`no`.
Penpot recognises those pairs and renders a toggle instead of a dropdown, which is both
faster to use and self-documenting.

## Sources

- <https://help.penpot.app/user-guide/design-systems/components/>
- <https://help.penpot.app/user-guide/designing/flexible-layouts/>
- <https://penpot.app/penpothub/plugins>
