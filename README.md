# Fieldnote landing page

A single-file HTML landing page template for a software product.

- **Structure** from [linkjar.app](https://linkjar.app): header, hero, product shot,
  founder story, three steps, principles, questions, closing CTA, one-row footer.
- **Design** from [palette.team](https://palette.team): warm cream panels on white,
  black on white type, and a grainy orange gradient on the closing CTA.

## Run it

```sh
open index.html
```

That is the whole build step. Tailwind v4 compiles in the browser from the CDN script in
the head. The page carries no `<style>` block: every colour, radius, and gradient is a
stock utility or an arbitrary value, so the markup pastes into any Tailwind project
without a theme to merge first.

For a real deploy, compile the CSS instead of shipping the ~400 KB browser compiler.
There is nothing to move across — `src/input.css` is one line:

```css
@import "tailwindcss";
```

```sh
npx @tailwindcss/cli -i src/input.css -o dist/style.css --minify
```

Then swap the `<script>` tag for `<link rel="stylesheet" href="dist/style.css">`.

If the project has no CSS build and you do not want to add one for a single page, write
the design out in plain CSS instead. It is about 200 lines: the tokens, one centred
column, cream boxes, and the gradient.

## The design

Narrow and quiet. The visual vocabulary is two things: cream boxes, and one box with a
gradient.

- No webfont. There is no `<link>` and no font override — Tailwind's default
  `--font-sans` is already the system stack, so `-apple-system` resolves on macOS with
  nothing loaded over the network.
- 768px column, left-aligned throughout. Nothing on the page is centred.
- Pill buttons (`rounded-full`), 16px panel radius, 12px card radius. No shadows anywhere.
- No rule under the header and none over the footer. The whitespace separates them.
- One product shot in the hero and keycaps in step 1. No other images.
- The principles section has no boxes at all, only whitespace between a two-column `<dl>`.
  Three boxed steps followed by six boxed principles would turn the page into cards.
- The questions are a `<dl>` too, stacked and ruled by the hairline rather than boxed, for the
  same reason. Four short answers, all of them open — an accordion hides two lines behind a
  click and buys nothing. Answer what somebody would otherwise email about, and keep the
  answers true; a question is where a page lies most easily.
- The header is two plain text links, so the hero holds the only filled button.
- Type tops out at `text-5xl` in the hero and `text-3xl` for section headings. The
  principles section runs one step larger than the others and carries a line under its
  heading, because it is the section that has to land.
- The story sits in from the column behind a hairline, and its turn — the one sentence the
  section exists to land — takes full ink against the muted rest. Left flush at the same edge
  as everything else, four paragraphs of muted prose read as filler and the turn is lost in
  them.
- The gradient box carries more padding on top than on the other three sides, so the cream
  end of the gradient has room to read before the text starts.
- The gradient box is the one thing wider than the column. It reaches out by exactly its own
  horizontal padding, so the text inside lands on the same line as every other paragraph on
  the page. Under `54rem` there is no room to give up, so it goes edge to edge and drops its
  radius — the text still lines up.

## Rebranding

The palette is written into the class names. There is no theme block, so changing a
colour is one find-and-replace per row of this table.

| Value              | Written as                              | Used for                                  |
| ------------------ | --------------------------------------- | ----------------------------------------- |
| `#ffffff`          | `bg-white`, `text-white`                | Page background, secondary button fill    |
| `#f7f4ef`          | `bg-[#f7f4ef]`                          | Cream boxes and cards                     |
| `#000000`          | `text-black`, `bg-black`                | Headings, body, primary button            |
| `#656463`          | `text-[#656463]`                        | Supporting copy, eyebrows, footer links   |
| `#ff4f00`          | `outline-[#ff4f00]`, the CTA gradient   | Focus ring and the hot end of the gradient|
| `#c93400`          | `text-[#c93400]`                        | Inline links on light panels              |
| `rgb(0 0 0 / 0.1)` | `border-`/`ring-`/`outline-black/10`    | Every rule, ring, and outline on the page |
| `1rem`             | `rounded-2xl`                           | Full-width panels                         |
| `0.75rem`          | `rounded-xl`                            | Cards and step boxes                      |

The same list sits in a comment at the top of `index.html`, because a class attribute
cannot hold one and the hex values otherwise explain themselves nowhere.

**One hairline, not three.** Everything that draws a 1px line reads `black/10`. Reaching
for `black/5` here and `black/10` there is how a page ends up with three weights of the
same line doing the same job.

**Rebranding twice is the point to stop find-and-replacing.** Put the six values in a
`@theme` block, swap the hex classes for token names, and you are back to one edit point.
The hex-in-the-class version exists so the file drops into a project whose theme you do
not want to touch, not because it is the better way to hold a palette.

### The gradient

The closing CTA is the one piece of decoration: grain over a radial gradient, both in a
single `bg-[image:...]` value with `bg-[#f7f4ef]` under it. The grain is an inline
`feTurbulence` SVG, percent-encoded down to alphanumerics — a space, quote, or bracket in
there would end the class early. Raise its `intercept` (`%270.09%27` in the URI) to make
the grain coarser.

## Icons

Lucide, served from jsDelivr as plain `<img>` tags, pinned to a version:

```html
<img src="https://cdn.jsdelivr.net/npm/lucide-static@1.32.0/icons/hard-drive.svg" alt="" class="size-6" />
```

Swap the filename for any name in the [Lucide set](https://lucide.dev/icons). Keep
`size-6` — Lucide's viewBox is 24×24, so that renders 1:1. Keep the version pinned;
`@latest` on a CDN means the icons can change under you.

`<img>` rather than inline SVG or the `lucide` UMD script, because the page has no
JavaScript and should keep it that way. The cost: **an `<img>` cannot be recoloured.** The
icons render black, which matches the body text. If you move off black, or need a light
icon on a dark control, inline that SVG with `stroke="currentColor"` instead.

**Before shipping, vendor the six files into the repo.** A CDN request per icon per page
view sends every visitor's IP to jsDelivr, and this page's own copy says "No tracking."
Unlike the Tailwind CDN, this dependency does not disappear when you compile the CSS.

```sh
mkdir -p icons && for i in hard-drive ban eye-off wifi-off download file-text; do
  curl -sO --output-dir icons "https://cdn.jsdelivr.net/npm/lucide-static@1.32.0/icons/$i.svg"
done
```

## Favicon

An inline `data:` SVG, so the page stays a single file. It is Lucide's `archive` with two
changes worth keeping when you swap the glyph:

- **Stroke thickened from 2 to 2.25.** Lucide is drawn for 24px. At 16px in a tab, the default
  weight breaks up.
- **A `prefers-color-scheme` rule inside the SVG.** A black mark is invisible on a dark tab bar.
  The media query matches the viewer's system theme, not the page's background.

`icon.svg` in this repo is that same mark written out as a file, commented, ready to copy
into a project and re-glyph. Use it for a real deploy — the inline `data:` URI exists so the
demo stays one file, not because it is the better way to ship:

```html
<link rel="icon" href="/icon.svg" type="image/svg+xml">
```

**Do not point this at an app-icon tile.** Those are drawn for a 512px rounded square with a
background and a border; at 16px in a tab one renders as a grey smudge. The favicon is the
bare mark on nothing. Keep the tile for `apple-touch-icon` and the PWA manifest, which need a
solid ground — iOS backs transparency with black — and add a PNG for those while you are here.

## Placeholder content

Images come from `assets.ui.sh`. Replace before shipping.

- Logo: the bare mark (`/marks/1.svg?color=black`) with the name set in the page font,
  since the endpoint has no system-font option.
- Hero shot: `/screenshots/1.webp` (the default variant is light; `mauve`, `mist`,
  `olive`, and `taupe` are dark and clash with this palette)

The copy is a fictional product with a fictional founder. Replace all of it, and do not
leave a made-up person's name on a real page.

## Layout rules

Worth keeping if you extend the page:

- Centred layouts are for heroes and CTAs only. This page centres nothing.
- One filled primary button per page, in the hero. Every other action is the white button
  with `ring-1 ring-hairline` — except inside the gradient box, where the buttons carry no
  ring at all. White on orange is already the contrast, and a hairline only muddies it.
- One `gap-6` value on every multi-column section so column edges line up down the page.
- Every section is `<section class="{padding}"><div class="mx-auto {max-width} px-6">`.
- Text width is constrained per element in `ch`, never on the heading-group wrapper.
- Two-column splits break at `lg:`, so tablets get a single column.
- The palette is light only. Cream and orange do not invert into a dark one, so a media
  query is not the fix. Either the page stays light while the app follows the system, or
  somebody designs a dark palette.

## Not included

This structure has no logo cloud, testimonials, or pricing table.
[palette.team](https://palette.team) has all three if you want to graft them on.
