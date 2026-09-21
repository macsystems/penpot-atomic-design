# Molecules and organisms

## Where the line runs

The taxonomy is a communication tool, not a law. Use it to answer "where does this live and
who may change it", and stop arguing once that question has an answer.

| Level | Test | Example |
| --- | --- | --- |
| **Molecule** | A few atoms doing **one** job together; useless alone but trivially reusable | Labelled input with helper and error text, search field, list row, card header |
| **Organism** | A section of interface that could carry a heading in a spec | Nav bar, sign-up form, product card, data table, footer, modal |

If two people disagree for more than a minute, it is an organism. The cost of guessing wrong
is a component on the wrong page; the cost of the argument is the afternoon.

## Building a molecule

Example: `form/field`

```
form/field                      board, flex column, gap = {spacing.xs}
├── label                       text, {typography.label}
├── form/input                  ← instance of the atom, never a copy of its shapes
└── message                     text, {typography.caption}, colour switches by state
```

**The rules that make it a molecule and not a drawing**

1. **Every atom inside is an instance.** If you find yourself rebuilding the input's border
   inside the field, stop — you are forking the atom.
2. **Never detach.** Detaching breaks the link that the entire system rests on: change the
   atom, and the detached copy silently stays behind. If an atom does not fit, the atom
   needs a new variant, or the difference belongs in a token.
3. **Spacing comes from the layout**, using spacing tokens for gap and padding. No invisible
   rectangles, no nudged coordinates.
4. **Expose only what varies.** A field needs `State` and perhaps `Has helper`. It does not
   need to expose every property of the input it contains.
5. **One job.** A "field" that also renders a submit button is not a molecule; it is a small
   organism with an identity crisis.

## Building an organism

Example: `nav/top-bar`

```
nav/top-bar                     board, flex row, justify space-between, padding {spacing.md}
├── brand                       instance of logo atom
├── nav/links                   instance of a molecule (flex row of link items)
├── search                      instance of form/search — optional
└── actions                     flex row
    ├── button/icon             instance (notifications)
    └── avatar                  instance
```

**The rule that keeps organisms honest**

> An organism defines **layout and composition**. It never overrides the style of the atoms
> and molecules inside it.

If a button must look different inside the nav bar, that is a variant of the button. The
moment an organism starts restyling its children, the design system has two sources of truth
for what a button looks like, and the one in the organism is invisible to everyone else.

**Sizing behaviour is part of the contract.** Decide per child whether it is fixed, hugs its
content, or fills the remaining space, and set constraints so the organism survives being
resized. An organism that only looks right at exactly 1440 px has not been designed, it has
been positioned.

**Flex or grid:**

- **Flex** for linear arrangements: a row of actions, a column of fields, anything
  distributed along one axis.
- **Grid** for two-dimensional structures known in advance: a card grid, a table, a
  dashboard shell. Grid is also what makes responsive behaviour declarative instead of a
  second board.

## Nesting depth

Keep the tree to **3–4 levels**. Deeper nesting is where the trouble starts:

- overrides become impossible to reach, because each level filters what the level above can
  change,
- the Layers panel stops being navigable,
- rendering and selection get slow,
- and developers reading Inspect see a tree that matches nothing in their code.

If a component needs five levels, it is usually two components.

## Working inside component copies

Penpot deliberately restricts structural edits inside a copy, and the restrictions are worth
knowing before they surprise you mid-build:

- **Adding or removing children in a copy is blocked.** The structure belongs to the main
  component.
- **Removing a layer inside a copy hides it instead of deleting it.** The layer stays in the
  tree, drops out of the layout flow, and siblings collapse — which looks like a broken
  layout rather than a refused edit. When something "disappears but the spacing is wrong",
  look for a hidden child.
- **Text, colours and icon swaps are fair game.** Those are the overrides copies exist for.
- **Instances adopt new children from the main component, but not new sizes.** After a
  height or width change in the main, instances may need a nudge to recompute.

The takeaway for composition: decide structure in the main component, leave content to the
copy.

## Overrides that survive a variant switch

An override carries over to another variant when the layer matches in **name**, **type** and
**hierarchy level**. Consistent layer naming across variants is therefore not tidiness — it
is the mechanism. A `label` in one variant and `Label 2` in another loses the designer's
text every time they switch state.

## Sources

- <https://help.penpot.app/user-guide/design-systems/components/>
- <https://help.penpot.app/user-guide/designing/flexible-layouts/>
- <https://atomicdesign.bradfrost.com/chapter-2/>
