# Playground

Small, self-contained visualizers built to understand things properly rather than to read about them. No build step, no dependencies, no framework. Each tool is a single HTML file you can open in a browser, email to someone, or serve from any static host.

Live at `https://jsubroto.github.io/playground/`

## Tools

### Bitwise lab (`bitwise/`)

Two instruments on one page.

ASCII to bits:

- Type text and each character renders as a byte, most significant bit first, with place values labelled
- Click any bit to flip it. The glyph, decimal, hex and the text field all update together
- Shortcuts for flipping bit 5 on letters (the case bit), inverting all bits, and resetting
- Live binary, hex and decimal output for the whole string
- Non printable results are labelled (`NUL`, `LF`, `SP`, `DEL`) or shown as raw hex

De Morgan's laws:

- Two switches for A and B drive every view on the page at once
- Both identities shown with live TRUE/FALSE values and an equality badge
- Gate schematics where wires light up with signal value, so `AND` then `NOT` can be compared against `NOT, NOT` then `OR`
- A shaded set view: five Venn panels per law tracing both derivation routes to the same region, with a dot marking which region the current switches sit in
- Full truth table with the current switch position highlighted
- Bitwise proof on two toggleable bytes showing `~(A&B)`, `~A|~B`, `~(A|B)` and `~A&~B` side by side

The Venn equality is computed by evaluating each expression over the four disjoint regions and comparing the resulting sets, so a wrong expression would report a mismatch rather than silently claiming agreement.

### Sorting visualizer (`sorting/`)

Bubble, insertion, selection, merge and quicksort.

- Every run is recorded as a list of operations before playback starts, so stepping and scrubbing are exact rather than interpolated
- Play, pause, step forward, step back, or drag the timeline. Space toggles playback, arrow keys step
- Five input distributions: random, nearly sorted, reversed, sorted, few unique
- Array size 8 to 80, speed roughly 2 to 900 operations per second
- Bars are coloured by role: comparing, pivot, partially ordered, final position. Merge and quicksort also show the active subrange
- Live comparison, swap and write counters, plus a readout of the current operation
- Per algorithm complexity panel and a side by side comparison table

The counters make the tradeoffs concrete. At n=40, bubble sort drops to 39 comparisons on sorted input because of its early exit, selection sort always does exactly 780 regardless of input, merge sort does a constant 216 writes, and quicksort goes from about 174 comparisons on random input to about 596 on sorted input as its last element pivot degrades.

## Running locally

Open `index.html` in a browser. That is the whole setup.

One caveat: the landing page links to `./bitwise/` and `./sorting/`, and directory URLs do not resolve to `index.html` over `file://` in every browser. To click between pages locally, serve the folder instead:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Opening a tool page directly, for example `bitwise/index.html`, works fine over `file://`.

## Deploying

GitHub Pages, deploy from a branch:

1. Settings, then Pages
2. Source: deploy from a branch
3. Branch `main`, folder `/ (root)`

No Actions workflow and no `.nojekyll` file are needed. Jekyll passes plain HTML through untouched and nothing here is underscore prefixed. If a folder ever mysteriously 404s after adding build output, `touch .nojekyll` is the fix.

## Structure

```
.
├── index.html            landing page, one card per tool
├── bitwise/
│   └── index.html
├── sorting/
│   └── index.html
└── README.md
```

Each tool lives in its own folder named `index.html` so the URLs stay clean: `/bitwise/` rather than `/bitwise.html`.

## Conventions

Every page is one file containing a single `<style>` block and a single `<script>` block. The point is that any page can be copied, shared or opened offline years from now without a toolchain to resurrect.

- Colours are defined as `--artifact-*` CSS variables with light and dark values, and elements only reference the variables
- No external requests at all: no CDN, no web fonts, no images. Typography uses a system font stack
- Every page carries a print stylesheet, so browser print produces a usable PDF
- Tool pages link back to the landing page with a relative `../`, which keeps working if the repo is renamed

## Adding a tool

1. Create a folder named for the topic, keeping it short: `regex`, not `regex-visualizer`
2. Write `index.html` inside it, copying the token block and print styles from an existing tool
3. Add a card to the root `index.html`
4. Keep it self-contained

Duplicating the token block across tools is deliberate. At this size, copying 40 lines costs seconds, while a shared stylesheet would introduce relative path handling and break pages opened over `file://`. Worth revisiting at four or more tools, when a palette change means four edits.

## License

MIT
