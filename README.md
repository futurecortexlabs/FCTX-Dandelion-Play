<h1 align="center">Pop! The Real Dandelion Pulling Simulator</h1>

<p align="center"><b>English</b> | <a href="README.ja.md">日本語</a></p>

<p align="center">
  <b>Grab the crown, rock it side to side, and draw the whole taproot out in one clean pop.<br>
  A single-file browser toy with a soil cross-section, a spring-chain root and synthesized sound.</b>
</p>

<p align="center">
  <a href="https://futurecortexlabs.github.io/FCTX-Dandelion-Play/"><b>▶ Play it</b></a>
  &nbsp;·&nbsp;
  <a href="#how-it-works">How it works</a>
  &nbsp;·&nbsp;
  <a href="#known-limits">Known limits</a>
</p>

<p align="center">
  <img src="docs/media/pulling.png" width="720" alt="Mid-pull: the hand grips the crown above the ground, the soil around it has been loosened into a mound, and the pale taproot is sliding up out of the cross-section">
</p>

<p align="center">
  <sub>
  Mid-pull after watering and loosening the soil. The crown is already above ground and the taproot is sliding out. Captured in headless Chromium while playing with real mouse input.
  </sub>
</p>

---

## What it is

Anyone who has weeded a garden knows the feeling: pull a dandelion by its leaves and it tears, leaving the root behind to grow back. Pull it right and the whole root slides out in one piece.

This is a toy about that one moment. The ground is drawn as a cross-section, so you can watch the taproot and its rootlets under the soil while you work. The page text is in Japanese.

| What you do | What happens |
|:--|:--|
| **Grab a flower or a leaf** | It bends towards your finger, and the tension gauge rises. Pull it too far and it snaps, with white latex at the break |
| **Water the soil** | The soil darkens as it soaks up water, and grips the root less. Only water that lands near the plant counts |
| **Loosen it with the weeding fork** | Scrape around the base and the soil heaps up, loose and soft |
| **Grab the crown and rock it** | Shaking side to side loosens the soil and pops the rootlets off one by one |
| **Pull straight up** | If no part of the root goes past its limit, it comes out to the very tip: *pop!* Pull at an angle or too fast and the root breaks partway |
| **Afterwards** | A result card shows the length and a rank. If the root broke, you can make it grow back: a new shoot, leaves and flowers grow out of the root left in the ground |

Each new plant is one of five species, chosen at random.

| Species | Root depth | Grip | |
|:--|:--|:--|:--|
| セイヨウタンポポ (common dandelion) | 72 % | 0.95 | Two flowers. Rock it and it comes out |
| カントウタンポポ (Kantō dandelion) | 66 % | 0.82 | One flower, the easiest |
| シロバナタンポポ (white dandelion) | 76 % | 1.10 | White petals, a slightly deeper root. Water helps |
| わたげタンポポ (seed head) | 80 % | 1.15 | Seed heads. Shake it and the seeds blow away; pull hard and the stem tears |
| ヌシの大タンポポ (the old master) | 88 % | 1.45 | Three stems and the thickest root. Needs water and the fork |

Root depth is the share of the underground area the taproot reaches. Grip is how hard the soil holds it.

<table>
<tr>
<td width="50%"><img src="docs/media/stem-snap.png" alt="A flower stem has been pulled too far and snapped: the flower flies off with yellow petals and white latex droplets appear at the stump"></td>
<td width="50%"><img src="docs/media/water.png" alt="The watering can sprinkles blue drops over the plant, and the moisture gauge reads 100 %"></td>
</tr>
<tr>
<td><img src="docs/media/root-break.png" alt="The old master dandelion yanked straight up out of dry soil: the taproot has snapped just below the crown, with white latex at the break, and the rest of the root stays in the ground"></td>
<td><img src="docs/media/result.png" alt="The result card: a clean pull, 23.2 cm, rank S"></td>
</tr>
<tr>
<td align="center"><img src="docs/media/mobile.png" width="240" alt="On a phone-sized screen, pulling the old master dandelion with its three stems"></td>
<td align="center"><img src="docs/media/mobile-zukan.png" width="240" alt="The collection on a phone-sized screen, listing a clean pull and, below it, the broken pull before it"></td>
</tr>
</table>

---

## Play it

- **Online:** [futurecortexlabs.github.io/FCTX-Dandelion-Play](https://futurecortexlabs.github.io/FCTX-Dandelion-Play/)
- **Locally:** open `index.html` in a browser. There is no build step and no local server is needed, but it needs an internet connection to load Tailwind CSS and the font.

It plays sound. Any current browser works, on desktop or phone.

| Button | Desktop | Phone |
|:--|:--|:--|
| 🖐️ **つかむ** (grab) | Press on the crown, a flower or a leaf and drag | Touch and drag with one finger. Other fingers are ignored |
| 🚿 **じょうろ** (watering can) | Hold the mouse button down over the plant; drops that land near it soak in | Same |
| ⛏️ **くさぬき** (weeding fork) | Drag around the base of the plant | Same |
| 🌱 **つぎの草** (next plant) | Swap in a new random dandelion | Same |
| 🔍 **土のなか** (underground) | Show or hide the cross-section | Same |
| 📖 **ずかん** (collection) | Your last 30 pulls | Same |
| ❓ **コツ** (tips) | Three tips for a clean pull | Same |

**Keyboard:** press Tab to reach the play area. Then 1, 2 and 3 pick the grab, the watering can and the fork. Hold Space (or Enter) to use the tool, and move it with the arrow keys (hold Shift for small steps). With the grab, rock left and right, then pull up. N swaps in the next plant, and Esc closes any dialog.

The crown is the small disc at ground level, inside the dotted circle. While 土のなか is showing, tap a worm and it wriggles; tap a pill bug and it curls up.

---

## How it works

Everything lives in `index.html`: Tailwind CSS for the layout, one `<canvas>` for the play area, and plain JavaScript. There are no image or audio files.

```
every frame (requestAnimationFrame)
  |
  +- physics     fixed steps of 1/60 s, as many as the time since the last frame needs
  |    +- input       pointer speed for this step; the grabbed part and how far it has been pulled
  |    +- soil        resistance = max(0.15, 1 - (0.42 x moisture + 0.45 x looseness))
  |    +- root chain  springs from the crown down, rootlets popping, tension per node, break or extract
  |    +- ambient     water drops, latex, dirt, seeds, clouds, worms and pill bugs
  +- render      Canvas 2D at the screen's pixel density: sky, soil cross-section, root, leaves, flowers, overlays
```

Because the physics runs in fixed steps, the game plays at the same speed on a 60 Hz, 120 Hz or 144 Hz display.

The header, the play area and the button bar are three separate rows, so nothing is ever drawn over the plant. The plant's name and the records sit on their own row and never wrap, so the header keeps its height as the numbers grow. The ground line sits at 48 % of the play area. The plant is scaled to fit above and below it. When the play area changes size, for example when a phone is turned or its address bar hides, the plant on screen is rescaled to the new size rather than replaced.

### The taproot is a chain of springs

The taproot is 11 to 16 nodes, from the crown downwards. Each node has a rest length to its parent, a radius that thins towards the tip, and a strength of `28 + 4.6 × radius`. The soil holds each node back towards where it started. The hold is strongest at mid-depth, and scaled by the species' grip, the soil resistance, how loose the soil is around that node, and how many rootlets are still attached (each adds 32 %).

When the crown moves up, each node follows its parent. A node held hard by the soil moves less, so the spring between them stretches. That stretch becomes tension:

```
tension = stretch x (1 + 1.08 x hold) x angle penalty x speed penalty
```

- **Angle penalty:** once the parent is above the ground, pulling sideways multiplies tension by up to 2.6×.
- **Speed penalty:** moving the pointer more than 14 px in one physics step (1/60 s) adds 5.5 % for every extra pixel. The speed is measured afresh every step, so holding still brings it straight back to zero.

When any node reaches 1.0, the root breaks there and everything below it stays in the ground. When every node still attached is above the ground, the whole root is out. A node that has come out is free of the soil, but if it is pushed more than 10 px back under the ground, the soil grips it again.

### Rootlets and rocking

Each rootlet is anchored in the soil. When its node moves further away than `length + 12 × soil resistance + 5`, it pops off with a small *pip*. Rocking the crown side to side does two things. It loosens the soil, and the sideways offset between nodes loosens each node's own patch of soil, so the rootlets come away before you pull up.

### Stems and leaves

Grabbing a flower or a leaf does not break it. Only pulling it past 68 px (65 px for a leaf) snaps it. Until then it bends towards your finger and springs back when you let go. The crown is tugged a little from wherever it is at that moment, so grabbing a flower after the crown is out does not push it back into the ground. A broken stem leaves a hollow stump, and the break oozes white latex.

### Growing back

If the root broke, **🌱 残った根から再生させる** on the result card grows the same species back from the root left in the ground. Over about 2.5 seconds the leaves and flowers grow out of the crown, and the upper part of the root fills out down to where it broke. The part that stayed in the ground is there from the start. The plant cannot be grabbed until it has finished growing.

### Results and saving

The length in centimetres is the pixel length of the root that came out × 0.12. If the root breaks at its very first segment, nothing came out and the length is 0 cm. A clean pull ranks **SSS** at 25 cm or more, **S** at 18 cm or more, and **A** otherwise. A broken root ranks **B** if more than 60 % of its length came out, and **C** otherwise.

The pull count, best length and last 30 pulls are saved in `localStorage` (key `dandelion_sim_save_v2`). The pull count only counts clean pulls. The best length counts broken ones too.

The saved data is checked when it is loaded. Anything missing or damaged is replaced with an empty record, so a bad save never stops the game. Each result is added by reading what is stored first, so two tabs open at once both keep their pulls, and the other tab's header updates to match.

### Sound

Eight sounds are synthesized on the spot with Web Audio: the creak of tension (faster and higher as it rises), the snap, the *pip* of a rootlet, the whoosh of seeds, the watering can, the scrape of the fork, the *pop* of a clean pull followed by a C-major arpeggio, and a soft rising chime as a plant grows back. Browsers only allow sound after a user gesture, so it starts on the first touch, click or key press. If the browser or the phone pauses it, for example for a call, the next touch resumes it. In a browser without Web Audio the game runs silently.

---

## Accessibility

- **Zoom works.** The page can be pinch-zoomed. Only the play area itself takes over touch, so that dragging there does not scroll the page.
- **It can be played from the keyboard.** See the keys under [Play it](#play-it). The play area has a label and a description of the keys for screen readers.
- **The dialogs behave as dialogs.** The result card, the collection and the tips move focus inside when they open, keep Tab inside, close with Esc or a tap outside, and return focus to where it was.
- **State is announced.** The advice bar is a live region, and the tool buttons and the 土のなか toggle report whether they are on.

---

## Verification

Checked in headless Chromium with Playwright:

- **35 automated checks** of the fixes in this version, run against the page itself: pointer speed while holding still and per step, damaged saves (`null`, `[]`, partial data, HTML in a name, invalid JSON), header height on a 340 px phone, resizing without replacing the plant, pixel density, the result card after switching plants, regrowing, cancelled and second touches, right-click, 0 cm and B/C ranking, re-burying a root, watering away from the plant, hidden critters, header DOM writes while idle, dialogs, keyboard play, pinch zoom, two tabs, and a 60-step-per-second physics rate. 34 passed. The second-finger check failed because of the test harness: the DevTools protocol cannot lift one of two fingers. That case was checked again by sending each finger's pointer events separately, and it passed.
- **Played with real mouse input at 900 × 760:** watered, loosened, rocked and pulled a common dandelion out clean (23.2 cm, rank S). Pulled a flower until its stem snapped. Yanked an old master straight up out of dry soil; it broke just below the crown (1.9 cm, rank C).
- **Played with touch input at 390 × 844 in phone emulation:** broke an old master, let it grow back, then watered, loosened, rocked and pulled it out clean (33.1 cm, rank SSS), and opened the collection.
- No errors in the console.

The Tailwind CDN could not be reached from the test machine, so these runs loaded Tailwind's v4 browser build in its place. The live page uses the v3 Play CDN, so spacing and shadows may differ slightly from the screenshots. The screenshots in this README come from these runs, with the real font. The scripts that drove them are not part of this repository, and there is no automated test suite. Touch has been tested in emulation only, not on a real phone.

---

## Performance

| | |
|:--|:--|
| Size | One file, `index.html`, about 113 KB |
| Network | Tailwind CSS Play CDN, and M PLUS Rounded 1c from Google Fonts |
| Frame rate | Not measured on real hardware yet. The physics runs at 60 steps per second whatever the display's refresh rate |

---

## Known limits

- **Japanese only.** All the text on the page is in Japanese.
- **It needs a connection.** The styling comes from Tailwind's runtime on the CDN. With the CDN blocked, the page loses its styling and the result dialog shows below the buttons.
- **Lengths depend on the screen.** The root is scaled to the height of the play area, so records are not comparable across devices. If the play area changes size mid-pull, the root is rescaled with it and measured at the new size.
- **One pointer at a time.** The first finger, the mouse or the keyboard is followed, and any others are ignored until it lets go.
- **Tight on narrow phones.** At 390 px wide and below, the gauge labels wrap onto two lines.
- **The picture is not described.** A screen reader hears the advice bar, the gauges and the buttons, but not what is drawn on the canvas.
- **Slow devices slow down.** If a single frame takes longer than a quarter of a second, the game runs slower rather than skipping ahead.

---

## Repository

| Path | Contents |
|:--|:--|
| [`index.html`](index.html) | The whole game: layout, simulation, drawing, sound, saving, and the text |
| [`docs/media/`](docs/media) | The screenshots used in this README |
| [`README.ja.md`](README.ja.md) | This README in Japanese |

Tailwind CSS (MIT licence) and the M PLUS Rounded 1c font (SIL Open Font License 1.1) are loaded at runtime and are not redistributed in this repository.
