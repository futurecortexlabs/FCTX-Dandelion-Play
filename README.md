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
  Mid-pull after watering and loosening the soil. The crown is already above ground and the taproot is sliding out. Captured in headless Chrome while playing with real mouse input.
  </sub>
</p>

---

## What it is

Anyone who has weeded a garden knows the feeling: pull a dandelion by its leaves and it tears, leaving the root behind to grow back. Pull it right and the whole root slides out in one piece.

This is a toy about that one moment. The ground is drawn as a cross-section, so you can watch the taproot and its rootlets under the soil while you work. The page text is in Japanese.

| What you do | What happens |
|:--|:--|
| **Grab a flower or a leaf** | It bends towards your finger, and the tension gauge rises. Pull it too far and it snaps, with white latex at the break |
| **Water the soil** | The soil darkens as it soaks up water, and grips the root less |
| **Loosen it with the weeding fork** | Scrape around the base and the soil heaps up, loose and soft |
| **Grab the crown and rock it** | Shaking side to side loosens the soil and pops the rootlets off one by one |
| **Pull straight up** | If no part of the root goes past its limit, it comes out to the very tip: *pop!* Pull at an angle or too fast and the root breaks partway |
| **Afterwards** | A result card shows the length and a rank. If the root broke, you can watch the dandelion grow back from what was left |

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
<td><img src="docs/media/root-break.png" alt="The old master dandelion yanked straight up from dry soil: the taproot glows red with tension and has broken in the middle, with white latex at the break"></td>
<td><img src="docs/media/result.png" alt="The result card: a clean pull, 24.0 cm, rank S"></td>
</tr>
<tr>
<td align="center"><img src="docs/media/mobile.png" width="240" alt="On a phone-sized screen, pulling the old master dandelion with its three stems"></td>
<td align="center"><img src="docs/media/mobile-zukan.png" width="240" alt="The collection on a phone-sized screen, listing one broken pull and one clean pull"></td>
</tr>
</table>

---

## Play it

- **Online:** [futurecortexlabs.github.io/FCTX-Dandelion-Play](https://futurecortexlabs.github.io/FCTX-Dandelion-Play/)
- **Locally:** open `index.html` in a browser. There is no build step and no local server is needed, but it needs an internet connection to load Tailwind CSS and the font.

It plays sound. Any current browser works, on desktop or phone.

| Button | Desktop | Phone |
|:--|:--|:--|
| 🖐️ **つかむ** (grab) | Press on the crown, a flower or a leaf and drag | Touch and drag |
| 🚿 **じょうろ** (watering can) | Hold the mouse button down; the drops soak into the ground | Same |
| ⛏️ **くさぬき** (weeding fork) | Drag around the base of the plant | Same |
| 🌱 **つぎの草** (next plant) | Swap in a new random dandelion | Same |
| 🔍 **土のなか** (underground) | Show or hide the cross-section | Same |
| 📖 **ずかん** (collection) | Your last 30 pulls | Same |
| ❓ **コツ** (tips) | Three tips for a clean pull | Same |

The crown is the small disc at ground level, inside the dotted circle. Tap a worm and it wriggles; tap a pill bug and it curls up.

---

## How it works

Everything lives in `index.html`: Tailwind CSS for the layout, one `<canvas>` for the play area, and plain JavaScript. There are no image or audio files.

```
every frame (requestAnimationFrame)
  |
  +- input       the grabbed part: crown, stem or leaf, and how far it has been pulled
  +- soil        resistance = max(0.15, 1 - (0.42 x moisture + 0.45 x looseness))
  +- root chain  springs from the crown down, rootlets popping, tension per node, break or extract
  +- ambient     water drops, latex, dirt, seeds, clouds, worms and pill bugs
  +- render      Canvas 2D: sky, soil cross-section, root, leaves, flowers, overlays
```

The header, the play area and the button bar are three separate rows, so nothing is ever drawn over the plant. The ground line sits at 48 % of the play area. The plant is scaled to fit above and below it.

### The taproot is a chain of springs

The taproot is 11 to 16 nodes, from the crown downwards. Each node has a rest length to its parent, a radius that thins towards the tip, and a strength of `28 + 4.6 × radius`. The soil holds each node back towards where it started. The hold is strongest at mid-depth, and scaled by the species' grip, the soil resistance, how loose the soil is around that node, and how many rootlets are still attached (each adds 32 %).

When the crown moves up, each node follows its parent. A node held hard by the soil moves less, so the spring between them stretches. That stretch becomes tension:

```
tension = stretch x (1 + 1.08 x hold) x angle penalty x speed penalty
```

- **Angle penalty:** once the parent is above the ground, pulling sideways multiplies tension by up to 2.6×.
- **Speed penalty:** moving the pointer faster than 14 px per frame adds 5.5 % for every extra pixel.

When any node reaches 1.0, the root breaks there and everything below it stays in the ground. When every node still attached is above the ground, the whole root is out.

### Rootlets and rocking

Each rootlet is anchored in the soil. When its node moves further away than `length + 12 × soil resistance + 5`, it pops off with a small *pip*. Rocking the crown side to side does two things. It loosens the soil, and the sideways offset between nodes loosens each node's own patch of soil, so the rootlets come away before you pull up.

### Stems and leaves

Grabbing a flower or a leaf does not break it. Only pulling it past 68 px (65 px for a leaf) snaps it. Until then it bends towards your finger and springs back when you let go. A broken stem leaves a hollow stump, and the break oozes white latex.

### Results and saving

The length in centimetres is the pixel length of the root that came out × 0.12. A clean pull ranks **SSS** at 25 cm or more, **S** at 18 cm or more, and **A** otherwise. A broken root ranks **B** if more than 60 % of it came out, and **C** otherwise.

The pull count, best length and last 30 pulls are saved in `localStorage` (key `dandelion_sim_save_v2`). The pull count only counts clean pulls. The best length counts broken ones too.

### Sound

Seven sounds are synthesized on the spot with Web Audio: the creak of tension (faster and higher as it rises), the snap, the *pip* of a rootlet, the whoosh of seeds, the watering can, the scrape of the fork, and the *pop* of a clean pull followed by a C-major arpeggio. Browsers only allow sound after a user gesture, so it starts on the first touch or click.

---

## Verification

Checked in headless Chrome, driving the page with real mouse events over the DevTools protocol:

- At 900 × 760: pulled a stem until it snapped, watered, loosened the soil, then rocked and pulled a common dandelion out clean (24.0 cm, rank S). Yanked an old master straight up out of dry soil; it broke at 13.7 cm, rank C. Then watched it grow back, and opened the collection and the tips.
- The same run at 390 × 844 with phone emulation.
- The only console message was Tailwind's warning that its CDN build is not meant for production. No errors.

The screenshots in this README come from those runs. The script that drove them is not part of this repository, and there is no automated test suite. Touch input has not been tested on a real phone; the runs above sent mouse events.

---

## Performance

| | |
|:--|:--|
| Size | One file, `index.html`, about 94 KB |
| Network | Tailwind CSS Play CDN, and M PLUS Rounded 1c from Google Fonts |
| Frame rate | Not measured on real hardware yet |

---

## Known limits

- **Japanese only.** All the text on the page is in Japanese.
- **It needs a connection.** The styling comes from Tailwind's runtime on the CDN. With the CDN blocked, the page loses its styling and the result dialog shows below the buttons.
- **Resizing replaces the plant.** Every change in the play area's size spawns a new random dandelion, including turning a phone. On phones, the browser's address bar showing or hiding may do the same mid-pull; this has not been checked on a real device.
- **The physics runs per frame, not per second.** On a 120 Hz or 144 Hz display, everything happens faster.
- **Lengths depend on the screen.** The root is scaled to the height of the play area, so records are not comparable across devices.
- **The canvas is not scaled for high-DPI screens.** It looks soft on Retina and most phones.
- **One finger only.** Only the first touch is tracked.
- **Tight on narrow phones.** At 390 px wide, the gauge labels wrap.
- **The result card overlaps a little.** On a clean pull, the "完全抜き" label runs into the length (visible in the screenshot above).

---

## Repository

| Path | Contents |
|:--|:--|
| [`index.html`](index.html) | The whole game: layout, simulation, drawing, sound, saving, and the text |
| [`docs/media/`](docs/media) | The screenshots used in this README |
| [`README.ja.md`](README.ja.md) | This README in Japanese |

Tailwind CSS (MIT licence) and the M PLUS Rounded 1c font (SIL Open Font License 1.1) are loaded at runtime and are not redistributed in this repository.
