# File and page structure

## The hierarchy Penpot actually uses

```
Team → Project → File → Page → Board → Group → Shape
```

- **Team** owns members, permissions and — decisively — the set of files that can share a
  library. A shared library is only visible inside its own team.
- **Project** is a folder of files. It has no effect on libraries or components.
- **File** is the unit that gets published as a shared library, and the unit that owns the
  token catalogue. This is the most important boundary in the whole system.
- **Page** is a canvas inside a file. Pages are where main components physically live.
- **Board** is Penpot's frame: a container with its own bounds, optional layout, export
  settings and clipping. Components are almost always boards.
- **Group** is the lightweight grouping for shapes that are not a container.

## One system file, one product file

Keep the design system in its own file and consume it from product files as a shared
library. Mixing them is the most common structural failure, and it costs you three things:

- **Clarity.** Nobody can tell whether a board on the canvas is a system component or a
  one-off screen exploration.
- **Release control.** Every scribble on a product page becomes a library update
  notification for everyone.
- **Blast radius.** A file that holds both cannot be reverted, archived or handed over
  without taking the other half with it.

The split also makes a second system file cheap later — a brand-specific extension that
connects to the core library and adds its own theme.

## The page plan

| # | Page | Purpose | Contains |
| --- | --- | --- | --- |
| 1 | `00 Foundations` | Show the token system to humans | Colour ramps, type scale, spacing rhythm, radius and elevation samples — all bound to tokens |
| 2 | `01 Icons` | The most reused atom | Icon main components on a uniform grid |
| 3 | `02 Atoms` | Smallest components | Buttons, inputs, checkboxes, toggles, badges, avatars, dividers |
| 4 | `03 Molecules` | Atoms with one shared job | Labelled field, search bar, card header, list row |
| 5 | `04 Organisms` | Complex sections | Nav bar, sidebar, footer, modal, table, full form |
| 6 | `05 Templates` | Layout without content | Grid skeletons showing where organisms sit |
| 7 | `06 Screens` | The deliverable | Templates filled with real content |
| 8 | `99 Archive` | Deprecated, still alive | Retired main components and their annotations |

### Why the numeric prefix is mandatory

New pages are always appended at the end of the list, and there is no API to reorder them —
only manual drag in the UI. Without a prefix, a file's page order reflects the order someone
happened to create pages in, which is never the order anyone wants to read them in.

### Why `00 Foundations` is documentation, not storage

Tokens live in the file's Tokens tab and apply across every page. Nothing on the Foundations
page *holds* a value; the page exists so a human can see the system at a glance and so a
reviewer can spot a ramp with a missing step. Build it out of shapes that are **bound to
tokens**, not painted with hex values — then the page updates itself and doubles as a
regression check: if a swatch turns grey, a token reference broke.

### Why the archive page is not sentimentality

A main component is a shape on a page. Delete the page and every copy in every connected
file loses its main. Penpot offers no "deprecated" flag, so the archive page *is* the
deprecation mechanism: move the main component there, prefix its name, and write the
replacement into its annotation.

## Board structure inside a page

- **One board per functional area, not per screen.** `Onboarding`, `Dashboard`, `Settings`
  — not `Dashboard v2 final`.
- **Use the canvas as a map.** Left to right for flow, top to bottom for hierarchy.
  Wireframes left, finished work right, so scanning the page tells the story.
- **Leave real gutters** between component boards (100 px is a comfortable default). Penpot
  boards clip their content, and neighbours that touch are hard to select and harder to read.
- **Group variants into their container**, which is itself a board with a horizontal flex
  layout. Do not scatter the states of one component across the canvas.
- **Keep nesting at 3–4 levels.** Deeper trees slow rendering, complicate overrides and turn
  the Layers panel into a maze.

## Where main components belong

Put the main instance on the page that matches its atomic level. This is not bookkeeping —
it is how the file stays navigable when it holds 300 components, and it makes "what level is
this?" answerable by looking at where the component lives.

Two practical consequences:

- **Writes only happen on the active page.** Editing a main component that sits on another
  page fails outright, whether you are clicking or scripting. Open the page first.
- **Shapes do not move between pages.** There is no move-to-page operation that preserves a
  component. Rebuild on the target page (instantiate → detach → re-register) and then remove
  the old page, or keep the component where it was created.

## Templates and screens

Templates are the stage where a design system proves it composes. A template contains
organisms and layout, no real content — placeholder text that reads `Product title` rather
than a real product name, so nobody mistakes a layout for a spec.

Screens are templates with real content, and they are the only pages a stakeholder should be
asked to review. Name screen boards for handoff (`screens/login`, `screens/checkout-step-2`)
so exports and developer links stay predictable.

## Sources

- <https://help.penpot.app/user-guide/account-teams/projects-files/>
- <https://help.penpot.app/user-guide/designing/workspace-basics/>
- <https://help.penpot.app/mcp/design-file-structure-best-practices/>
