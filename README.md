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

### Skyhop (`skyhop/`)

A 3D platformer in a single HTML file. Hop across a floating path, collect gems, reach the flag.

- WASD or arrow keys to move, relative to the camera
- Space to jump, and press it again in the air for a double jump
- Q and E or mouse drag to rotate the camera
- R to return to the last checkpoint, M to mute

One level of about 20 platforms, including a sliding platform, an elevator and a narrow beam, with 24 gems and 2 checkpoints. Keyboard only, so it loads on phones but can't be played there.

### Float lab (`float/`)

IEEE 754 floating point, bit by bit, in 32-bit and 64-bit.

- Type a decimal and it's rounded to the nearest float, or click any bit to flip it and the decimal follows
- Sign, exponent and mantissa are coloured separately, with hex and grouped binary readouts
- The exact value stored, the shortest decimal that maps back to the same bits, and the rounding error against what you typed
- Next up and next down step to the neighbouring float
- Presets for 0.1, 0.1 + 0.2, the largest float, the smallest normal and subnormal, ±0, ±Infinity and NaN
- A field by field breakdown: bias, hidden leading 1, subnormals, and the formula that turns the bits into the value

Decimal input is converted with exact BigInt arithmetic rather than JavaScript's own parser, so 32-bit results are rounded once, straight from the decimal, instead of passing through a 64-bit value first. A hint under the presets works out live whether 0.1 + 0.2 lands on the float nearest 0.3: it does in 32-bit and misses by one float in 64-bit.

### Sound lab (`sound/`)

The Web Audio behind Skyhop's sound effects, in two parts.

`beep()` playground:

- Sliders for each argument of the `beep()` function Skyhop uses: starting pitch, length, waveform, volume and pitch slide
- A graph of pitch and volume over the length of the sound, and the exact `beep(...)` call, both updating as you move the sliders
- Every Skyhop sound as a preset, including the multi-note gem, checkpoint and win sounds
- Space plays the current sound

Waveform types:

- One card per `OscillatorNode.type`: sine, square, sawtooth and triangle, each with its waveform drawn over two periods
- Slow motion mode runs the oscillator below hearing range and uses it to bend the pitch of a sine tone, so you hear the shape as movement while a dot tracks it on the graph
- Tone mode plays the oscillator directly, so the shape comes through as tone colour instead

## Running locally

Open `index.html` in a browser. That is the whole setup.

One caveat: the landing page links to `./bitwise/`, `./sorting/`, `./skyhop/`, `./float/` and `./sound/`, and directory URLs do not resolve to `index.html` over `file://` in every browser. To click between pages locally, serve the folder instead:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Opening a tool page directly, for example `bitwise/index.html`, works fine over `file://`. Skyhop is the exception: Chrome blocks ES module imports over `file://`, so serve it instead.

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
├── skyhop/
│   ├── index.html
│   ├── three.module.min.js   vendored Three.js r186
│   └── three.core.js         imported by three.module.min.js
├── float/
│   └── index.html
├── sound/
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

Skyhop is the exception. As a full screen game it uses its own fixed palette, with no dark mode and no print stylesheet, and it loads a vendored copy of Three.js r186 (`three.module.min.js` and `three.core.js`) as an ES module, so it's three files rather than one.

## Adding a tool

1. Create a folder named for the topic, keeping it short: `regex`, not `regex-visualizer`
2. Write `index.html` inside it, copying the token block and print styles from an existing tool
3. Add a card to the root `index.html`
4. Keep it self-contained

Duplicating the token block across tools is deliberate. At this size, copying 40 lines costs seconds, while a shared stylesheet would introduce relative path handling and break pages opened over `file://`. Worth revisiting at four or more tools, when a palette change means four edits.

## License

MIT
