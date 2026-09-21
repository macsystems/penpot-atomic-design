# Tokens first

> Build 50 components, then decide to change the primary colour, and you will edit 50
> components. Build tokens first and you edit one value.

Tokens are Penpot's foundation layer and the reason a Penpot design system can be genuinely
single-sourced. Penpot was the first design tool to implement the W3C Design Tokens
Community Group format natively, so the token file is portable rather than a vendor export.

## Where tokens live

In the **Tokens tab** of the file — not on a page, not in a library asset. They apply to
every page in the file, and they travel with the file when it is published as a shared
library (consumers pull them in explicitly with *Import tokens*).

## Token types

| Group | Types |
| --- | --- |
| Colour | `color` |
| Size | `dimension`, `sizing`, `spacing`, `borderRadius`, `borderWidth` |
| Text | `fontFamilies`, `fontSizes`, `fontWeights`, `letterSpacing`, `textCase`, `textDecoration`, `typography` (composite) |
| Effect | `shadow`, `opacity` |
| Other | `rotation`, `number` |

`typography` is a composite: it bundles family, size, weight, line height, letter spacing
and case into one token, which is what a type scale wants.

## Sets

A **set** is a named collection of tokens. Sets are the mechanism for alternates: light and
dark, brand A and brand B, compact and comfortable density.

Two rules decide everything about how sets behave:

1. **Order is precedence.** If two active sets define the same token name, the set *further
   down the list* wins. This is how a theme set overrides a base set — and how an accidental
   duplicate silently changes a value in a way that is hard to see.
2. **Only active sets resolve.** A reference pointing into an inactive set resolves to
   nothing. The reference is not broken; it is unresolved — a distinction that saves an hour
   of debugging when a fresh set looks dead. Newly created sets start inactive.

Set names use `/` for folders: `theme/light` and `theme/dark` nest under `theme`.

A layout that scales:

```
base/color         base/spacing        base/typography      ← raw values, always active
semantic/light     semantic/dark                            ← meaning, one active at a time
component/button   component/field                          ← component-specific, always active
```

## Themes

A **theme** is a saved combination of active sets. Themes are multidimensional: several can
be active simultaneously, and **theme groups** make combinations manageable — one theme per
group is active at a time.

```
Group "Mode":      Light | Dark
Group "Brand":     Core  | Partner
Group "Density":   Comfortable | Compact
```

Three groups of two produce eight combinations from six themes instead of eight hand-built
ones. Without groups, every new axis multiplies the theme list.

## The three tiers

```
Tier 1  Global / base     color.base.blue.500        spacing.base.8
        Raw values. Named for what they are. No meaning attached.

Tier 2  Semantic          color.bg.default           color.text.primary
        Meaning, referencing tier 1. This is the layer components consume.

Tier 3  Component         color.button.primary.bg    radius.card
        Only when a component genuinely needs its own knob.
```

Components bind to **tier 2** by default. Tier 3 exists for the cases where a component must
be themeable independently of the semantic layer — not as a mechanical third copy of every
value. A tier-3 token for every property of every component is a rename of tier 2, not an
abstraction.

## References and maths

Reference another token in curly braces:

```
color.text.primary  =  {color.base.neutral.900}
spacing.lg          =  {spacing.md} * 2
sizing.card         =  round({sizing.base} * 1.33)
```

Numeric tokens support `+ - * / % ^` and functions like `round()`, `ceil()`, `floor()`,
`min()`, `max()`, `sqrt()`. A modular type scale can be expressed as arithmetic rather than
as fourteen hand-typed numbers — and then a change of ratio is one edit.

## Tokens vs. library colours and typographies

Both exist, and they are not competitors:

| | Tokens | Library colours / typographies |
| --- | --- | --- |
| Themeable | Yes, per set and theme | No, one value each |
| References other values | Yes | No |
| Exports as W3C JSON | Yes | No |
| Appears in the Assets panel | No | Yes |
| Applies to a text shape wholesale | Via `typography` composite | Yes |
| Supports gradients | No | Yes |

**Rule:** tokens are the source of truth; library assets are the convenience surface. Where
both make sense — a brand palette, a type scale — create the library asset *from* the token
value so the Assets panel and the Tokens tab never disagree. Where tokens cannot reach —
gradients, image fills — the library asset is the only option, and that value needs its own
documentation on the Foundations page.

## Applying a token

Bind a token to a specific property of a shape (fill, stroke colour, corner radius, gap,
padding, font size, opacity …). Three behaviours are worth knowing before you build a
component around one:

- **Binding resets partial opacity to fully opaque.** If a surface needs a colour at 9 %,
  put the transparency on the *shape's* opacity, not inside the fill. A shape whose fill is
  translucent but whose border is solid then needs two shapes, one above the other.
- **Writing a raw fill or stroke afterwards removes the binding.** Bind last, or re-bind.
- **A binding may not be readable in the same operation that created it.** It lands; it just
  reads back empty until the next read. Do not conclude it failed.

## Import and export

Tokens export as a single JSON file or as a folder of JSON files (plus `$themes.json` and
`$metadata.json`), and import the same way, including ZIP. Both directions follow the DTCG
format, which means:

- The token file can live in the product repository and be reviewed like code.
- A pipeline can generate CSS custom properties, Kotlin/Swift constants or Tailwind config
  from the same file the designers edit.
- Moving a system between tools, or to a second Penpot file, is a file copy rather than a
  rebuild.

Treat the exported JSON as the artifact that leaves the design tool. That is the
deliverable developers actually consume — not a screenshot of the palette.

## A starting token tree

```
base/color
  color.base.neutral.{0,100,300,500,700,900,1000}
  color.base.brand.{100,300,500,700,900}
  color.base.semantic.{success,warning,error,info}

base/spacing         (8 px rhythm)
  spacing.{xs,sm,md,lg,xl,2xl} = 4, 8, 16, 24, 32, 48

base/radius
  radius.{none,sm,md,lg,full} = 0, 4, 8, 16, 999

base/typography
  font.family.{brand,plain,mono}
  font.size.{caption,label,body,body-lg,h3,h2,h1,display}
  font.weight.{regular,medium,bold}

semantic/light
  color.bg.{default,raised,sunken}        → {color.base.neutral.*}
  color.text.{primary,secondary,inverse}  → {color.base.neutral.*}
  color.border.{subtle,strong}
  color.action.{primary,primary-hover,danger}

semantic/dark
  same names, different references        ← the whole point of sets
```

Same names in both semantic sets, different references. That symmetry is what makes theme
switching work, and an asymmetry in it is a bug you want to catch on the Foundations page.

## Sources

- <https://help.penpot.app/user-guide/design-tokens/>
- <https://help.penpot.app/user-guide/design-systems/design-tokens/>
- <https://help.penpot.app/mcp/prompting-token-aware/>
- W3C Design Tokens format: <https://tr.designtokens.org/format/>
