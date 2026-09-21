# Agent recipes

Recipes for building and auditing an atomic structure through the Penpot MCP server. They
assume a connected MCP session and a file that is open in the browser — the plugin acts on
the **focused page of the active tab**, and nowhere else.

Read this together with the general MCP rules; this file only covers what is specific to
building a design system.

## The loop

```
READ    → inventory pages, assets, tokens. Never write into a file you have not read.
PLAN    → state what will be created, in what order, before creating it.
WRITE   → small batches, one logical unit each.
VERIFY  → structural read after every batch. Not an export — a read.
```

**Batch size.** Roughly 5–10 shape operations per call, fewer once a page is busy: response
time grows with page size, and batches that were instant on an empty page take 30–45 s on a
page with a dozen screens. Oversized batches fail by timing out, which is the worst failure
mode because it leaves partial state with no error.

**Page switching is its own call.** Open the page in one call; write in the next. Then guard
every write batch:

```javascript
if (penpot.currentPage.name !== '02 Atoms') throw new Error('wrong page');
```

That guard is not paranoia — the user can click another page at any moment, and writes only
apply to the active page.

**After a failure, read before retrying.** An exception rolls the whole call back; a timeout
usually does not, and the work is often already done. Retrying blindly after a timeout
creates a duplicate board with the same name, and the next `find by name` picks the wrong one.

## Recipe: inventory an existing file

Run this before proposing any change. It answers "what is already here" in one call.

```javascript
const pages = penpotUtils.getPages().map(p => {
  const page = penpotUtils.getPageByName(p.name);
  return { name: p.name, boards: page ? page.findShapes({ type: 'board' }).length : 0 };
});

const lib = penpot.library.local;
const comps = lib.components.map(c => c.name);

return {
  pages,
  componentCount: comps.length,
  componentPaths: [...new Set(comps.map(n => n.split('/')[0]))].sort(),
  colorCount: lib.colors.length,
  typographyCount: lib.typographies.length,
  tokenSets: lib.tokens.sets.map(s => ({ name: s.name, active: s.active, tokens: s.tokens.length })),
  tokenThemes: lib.tokens.themes.map(t => ({ group: t.group, name: t.name, sets: t.activeSets.length })),
};
```

`penpotUtils.tokenOverview()` gives the same picture for tokens alone, grouped by set and
type.

## Recipe: build the token foundation

Idempotent helpers, because calls get retried:

```javascript
const cat = penpot.library.local.tokens;

const ensureSet = name =>
  cat.sets.find(s => s.name === name) || cat.addSet({ name });

const addToken = (set, type, name, value) =>
  set.tokens.find(t => t.name === name && t.type === type) ||
  set.addToken({ type, name, value: String(value) });

const ensureTheme = (group, name, sets) => {
  const theme = cat.themes.find(t => t.group === group && t.name === name)
    || cat.addTheme({ group, name });
  sets.forEach(s => { if (!theme.activeSets.some(a => a.name === s.name)) theme.addSet(s); });
  return theme;
};

const base = ensureSet('base/color');
addToken(base, 'color', 'color.base.neutral.900', '#1A1A1A');
addToken(base, 'color', 'color.base.brand.500',   '#0066FF');

const light = ensureSet('semantic/light');
addToken(light, 'color', 'color.text.primary', '{color.base.neutral.900}');
addToken(light, 'color', 'color.action.primary', '{color.base.brand.500}');

if (!base.active)  base.toggleActive();
if (!light.active) light.toggleActive();

ensureTheme('Mode', 'Light', [base, light]);

return { sets: cat.sets.map(s => ({ name: s.name, active: s.active, n: s.tokens.length })) };
```

Two things that look like bugs and are not:

- **A reference resolves to nothing while its source set is inactive.** Activate the set and
  it resolves. Newly created sets start inactive.
- **`addToken` takes one object**, not three positional arguments. Called positionally it
  fails quietly: no token, no exception.

Keep to ~15 tokens per call.

## Recipe: library colours from tokens

The Assets panel needs library colours; the tokens hold the truth. Derive one from the other
rather than typing values twice:

```javascript
const lib = penpot.library.local;
const ensureColor = (name, hex) => {
  const found = lib.colors.find(c => [c.path, c.name].filter(Boolean).join('/') === name);
  if (found) return found;
  const c = lib.createColor();
  c.name = name;          // 'brand/navy' — path is derived from the slashes
  c.color = hex;
  return c;
};
```

Note the lookup: a library colour stores only the **last** path segment in `name`, with the
rest in `path`. Matching on `name` alone creates duplicates on every run. Hex also reads back
lowercase, so compare case-insensitively.

## Recipe: register a component

```javascript
const shape = penpotUtils.findShape(s => s.name === 'button-primary-draft');
const comp  = penpot.library.local.createComponent([shape]);
comp.path = 'button';     // set path and name SEPARATELY
comp.name = 'primary';
```

Setting `comp.name = 'button/primary'` **appends** to the existing path — run it twice and
the path grows to `button/button/primary`. Registering also prepends the path to the source
shape's name, so rename the shape afterwards if it matters.

## Recipe: build a variant container

```javascript
const [def, hov, dis] = ['btn-default', 'btn-hover', 'btn-disabled']
  .map(n => penpotUtils.findShape(s => s.name === n));

const container = penpotUtils.createVariantContainer([
  { shape: def, properties: { State: 'default'  } },
  { shape: hov, properties: { State: 'hover'    } },
  { shape: dis, properties: { State: 'disabled' } },
]);
container.name = 'button/primary';
return { name: container.name, props: container.variants.properties };
```

Rename the **container**, not the variant components inside it: renaming children can
deregister them from the library listing — they stay on canvas and disappear from Assets.

## Recipe: bind a token to a shape

```javascript
const token = penpotUtils.findTokenByName('color.action.primary');
token.applyToShapes([shape], ['fill']);
```

Three behaviours to design around:

- The binding may read back empty **in the same call** and be correct in the next one. Verify
  in a later call before concluding anything.
- Binding sets the fill or stroke to full opacity. Partial transparency belongs on
  `shape.opacity`.
- Writing `shape.fills = [...]` afterwards removes the binding. Bind last.

## Recipe: audit for hardcoded values

The check that tells you whether the system is real:

```javascript
const shapes = penpotUtils.findShapes(() => true, penpot.root);
const offenders = shapes
  .filter(s => s.fills?.length && !Object.keys(s.tokens || {}).length)
  .map(s => ({ name: s.name, type: s.type, fill: s.fills[0]?.fillColor }))
  .filter(o => o.fill);

return { total: shapes.length, unbound: offenders.length, sample: offenders.slice(0, 20) };
```

Variations worth running on a component page:

- **Unnamed layers**: `s.name.match(/^(Rectangle|Board|Group|Path|Ellipse)\s*\d*$/i)`
- **Depth**: walk `shapeStructure(board, 6)` and flag anything past four levels
- **Naming**: component names that do not match `^[a-z0-9-]+(\/[a-z0-9-]+)*$`
- **Containment**: `penpotUtils.analyzeDescendants(board, (root, s) =>
  !penpotUtils.isContainedIn(s, root) ? 'outside-bounds' : null)`

## A prompt preamble that works

```
ROLE: senior design systems designer. Penpot plugin API, WCAG AA, token-driven systems.
SOURCE: this Penpot file only. NO_GUESSING. IF_MISSING: report as TODO.
RULES:
  - read before write; verify structurally after every batch
  - max ~8 shape ops per call; never switch page and write in the same call
  - guard every write with a currentPage check
  - idempotent helpers only (ensureSet / addToken / ensureColor / ensureComponent)
  - never invent colours, fonts or font weights not present in the file
  - never detach a component copy
  - names: lowercase category/component; variant axes are properties, not names
OUTPUT: structured data, stable ordering, no narration.
```

## What agents should not do unsupervised

- **Delete anything.** Removal is unreliable across calls and a deleted main component
  breaks every copy. Propose; let a human confirm.
- **Bulk rename published assets.** Renames ripple into every consuming file.
- **Restructure pages.** Shapes cannot be moved between pages; the rebuild path
  (instantiate → detach → re-register) loses history and must be a deliberate decision.
- **Trust an export as verification.** Export is best-effort and fails for unrelated
  reasons. Verify by reading structure.

## Sources

- <https://help.penpot.app/mcp/>
- <https://help.penpot.app/mcp/design-file-structure-best-practices/>
- <https://help.penpot.app/mcp/good-prompting-practices-design/>
- <https://help.penpot.app/plugins/api/>
