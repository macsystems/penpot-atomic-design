# Documentation and handoff

> A component without documentation is a liability. Someone will misuse it within a week,
> and the misuse will ship.

## Annotations: the cheapest win in the whole system

Every main component can carry an **annotation**. It shows on every copy of that component
and in the **Inspect** tab, which means a developer reading the design sees it without
asking anyone. Use it on every component.

Three to five lines, in this order:

```
What it is        One sentence. "Primary call to action. One per view."
When to use it    And explicitly when not to: "Not for navigation — use link/inline."
Critical rule     The thing that gets it wrong: min width, required icon, label length.
Accessibility     Keyboard behaviour, focus order, required label, contrast status.
```

Writing this once prevents most component misuse, because misuse is nearly always a guess
made by someone who had no way to check.

## Documentation boards

Annotations are short by design. Anything longer belongs next to the component on its page,
as a board — a documentation panel beside each component or component group.

What earns its place on that panel:

| Section | Content |
| --- | --- |
| **Anatomy** | The component with its parts labelled |
| **Variants** | What each axis means and when to pick which value |
| **States** | What triggers each state, including loading and error |
| **Spacing** | Min/max width, internal padding, how it behaves when resized |
| **Do / Don't** | Two examples side by side, visual, no prose |
| **Accessibility** | Contrast result, touch target, keyboard, screen-reader label |

Do/Don't pairs are worth more than any paragraph. They are read at a glance, they survive
translation, and they settle arguments.

Keep the panel bound to tokens like everything else, so it cannot drift from the component
it documents.

## The Foundations page as living documentation

The Foundations page is documentation that maintains itself, if it is built from bound
shapes rather than painted ones:

- **Colour ramps** — every step of every base ramp, labelled with the token name, not the
  hex. The hex is in the token; repeating it on the page creates a second source of truth
  that will go stale.
- **Semantic pairs** — background over text, side by side, with the contrast ratio written
  next to them. This is where a WCAG failure is caught, not in review.
- **Type scale** — every level, at real size, with the token name and intended use.
- **Spacing rhythm** — the scale rendered as stacked bars, so the ratio is visible.
- **Radius and elevation** — samples, labelled.

If the page ever renders grey or blank, a token reference broke. That is a feature: the page
is a regression test you can see.

## Handoff

Penpot's **Inspect** mode gives developers code for any selected shape, plus the layer tree,
spacing and exports. Some of what makes it useful is set during design:

1. **Name boards for handoff.** `screens/login`, `components/button`. Predictable names make
   predictable links and predictable exports.
2. **Bind everything to tokens.** A developer reading Inspect on a hardcoded fill gets a hex
   and has to guess which constant it corresponds to.
3. **Export the token file.** The DTCG JSON is the handoff artifact for values; screenshots
   of a palette are not.
4. **Use names developers can map.** If Inspect says `button/primary` and the codebase says
   `btn-primary`, nobody has to ask a question.
5. **Set export settings** on the boards that need assets, at the scales the platforms use.

## Deprecation

Penpot has no deprecated flag, so deprecation is a convention. Undocumented deprecations are
how a product quietly ends up with two of everything for a year.

1. **Announce it in the annotation first**: `DEPRECATED — use card/product instead. Removal:
   2026-Q4.` The note appears on every existing copy, so every consumer sees it in place.
2. **Prefix the name**: `zz-deprecated/card-old` sorts it to the bottom of the Assets panel
   and out of the way of search.
3. **Move the main component to the archive page.** Never delete it — deleting breaks every
   copy in every connected file.
4. **Tell the team**, with the replacement named. A deprecation nobody reads is a rename
   with extra steps.
5. **Remove only after** the instances are gone. Check by searching for copies before you
   touch anything.

## Sources

- <https://help.penpot.app/user-guide/design-systems/components/>
- <https://help.penpot.app/user-guide/dev-tools/>
- <https://help.penpot.app/user-guide/export-import/exporting-layers/>
