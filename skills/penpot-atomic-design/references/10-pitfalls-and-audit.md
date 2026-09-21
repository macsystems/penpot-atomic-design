# Pitfalls and audit

## The mistakes that cost the most

### 1. Components before tokens

Build 50 components, then change the brand colour, and you edit 50 components. This is the
classic mistake and it never stops being true — it just gets more expensive.

**Fix:** tokens, then library assets, then icons, then atoms. If components already exist,
build the token layer anyway and re-bind component by component, starting with the ones with
the most instances.

### 2. Painting the foundations instead of binding them

A Foundations page full of rectangles with hex fills looks like a design system and is a
picture of one. Nothing binds to it; nothing updates when a value changes; two weeks later
it disagrees with the tokens and nobody notices.

**Fix:** every swatch, sample and specimen on that page is bound to the token it documents.
Then the page maintains itself and breaks visibly when a reference breaks.

### 3. Encoding variant axes in the name

`button/primary/hover` gives you three components in a folder, no swap UI, and a matrix that
only grows. This is the single most common import from older files.

**Fix:** `Combine as variants`, then axes as properties. Migration steps in
`references/06-variants.md`.

### 4. Building every combination

Five types × three sizes × five states is 75 variants. Nobody needs 75. A variant container
with 40 boards is unusable in the swap menu and a maintenance tax on every future change.

**Fix:** build the combinations the product uses. Add on demand.

### 5. Detaching

One detached instance is invisible. Twenty are a second design system that nobody maintains
and that drifts silently from the first.

**Fix:** never detach in a molecule or organism. If the atom does not fit, the atom needs a
variant, or the difference belongs in a token.

### 6. Organisms restyling their children

A nav bar that overrides its button's colour has created a second definition of that button
— one that is invisible from the button's own page.

**Fix:** the difference becomes a variant of the button.

### 7. One file for everything

System and product in one file destroys the boundary between "this is the system" and "this
is an exploration", and makes every scratch board a library event.

**Fix:** two files, one shared library.

### 8. Undocumented deprecation

Replacing a component without a note leaves both live. Six months later, half the product
uses each.

**Fix:** annotate, prefix, archive, announce — in that order.

### 9. Names that mean nothing to developers

`Card / Special Version` cannot be mapped to code, searched or discussed.

**Fix:** name what it is; keep design names mechanically derivable from code names.

## Penpot-specific traps

These are behaviours, not opinions. Each one has cost somebody an afternoon.

| Trap | What actually happens |
| --- | --- |
| Deleting a page with main components on it | The components die with the page, and every copy in every connected file loses its main |
| Expecting shapes to move between pages | They do not. Rebuild on the target page (instantiate → detach → re-register) |
| Editing a component whose main sits on another page | Refused outright. Open that page first |
| A new token set that seems to do nothing | Sets start inactive, and references into an inactive set resolve to nothing |
| Two sets defining the same token name | The set later in the list wins. Silent, and hard to spot |
| Partial opacity plus a token binding | Binding forces full opacity. Put transparency on the shape, not the fill |
| Writing a fill after binding a token | Removes the binding |
| Recolouring outline icons by setting fills | Outline icons paint through strokes; the change does nothing visible |
| Scaling an icon and expecting the stroke to scale | Stroke width does not scale with a resize |
| Importing SVG with gradients or clip paths | They are dropped; the shape may import invisible |
| Removing a layer inside a component copy | It is hidden, not removed — it leaves the layout flow and siblings collapse |
| Matching library colours by `name` | Only the last path segment lives there; the rest is in `path` |
| Setting a component name that includes its path | The path is appended, not replaced |
| Renaming variant components inside their container | Can deregister them from the Assets panel while they stay on canvas |

## Audit checklist

Run top to bottom. Each section is worthless if the one above it fails.

### Tokens

- [ ] A token exists for every colour, spacing step, radius, font size and weight in use
- [ ] Base, semantic and component tiers are distinguishable by name
- [ ] Semantic sets define the **same token names** across themes
- [ ] Every set that should resolve is active; no unresolved references
- [ ] No duplicate token names across active sets (unless the override is intended)
- [ ] Themes are grouped; no combinatorial explosion of individual themes
- [ ] The token JSON has been exported and is available to developers

### Structure

- [ ] Pages follow the level plan and carry numeric prefixes
- [ ] Main components live on the page matching their level
- [ ] Foundations page is bound to tokens, not painted
- [ ] An archive page exists and contains only deprecated work
- [ ] System and product live in separate files
- [ ] Nesting stays within 3–4 levels

### Naming

- [ ] Every component matches `category/component`, lowercase, no spaces
- [ ] No state, size or style encoded in a component name
- [ ] No appearance-based names
- [ ] Layers named by function; no `Rectangle 23`
- [ ] Layer names consistent across variants of the same component
- [ ] Names map onto what developers call these things

### Components

- [ ] One component per concept — no duplicates that render the same thing
- [ ] Every atom instance inside molecules and organisms is an instance, not a rebuild
- [ ] Nothing is detached
- [ ] Organisms contain no style overrides of their children
- [ ] Layout comes from flex/grid; no invisible spacer rectangles
- [ ] Sizing and constraints defined, so components survive a resize
- [ ] Touch targets meet 44 px / 48 dp

### Variants

- [ ] Properties are named meaningfully (no `Property 1`)
- [ ] Value vocabularies are consistent across components
- [ ] Boolean axes use `true/false`, `on/off` or `yes/no`
- [ ] No duplicate combinations flagged
- [ ] The matrix reflects real usage, not the cartesian product

### Documentation

- [ ] Every main component has an annotation with usage and a critical rule
- [ ] Do/Don't examples exist for components with a history of misuse
- [ ] Accessibility notes recorded: contrast, keyboard, screen-reader label
- [ ] Deprecated components are annotated, prefixed and archived

### Handoff

- [ ] Boards named for handoff (`screens/…`, `components/…`)
- [ ] Inspect shows tokens, not raw values
- [ ] Export settings set where assets are needed
- [ ] The library is published and consumers know which version they hold

## Scoring

- **Tokens and structure failing** → not a design system yet. Start at build order.
- **Naming failing, everything else fine** → one rename sweep, highest return per hour in
  this list.
- **Variants failing only** → a migration, mechanical and safe.
- **Documentation failing only** → the system works and will be misused. Annotations first.
