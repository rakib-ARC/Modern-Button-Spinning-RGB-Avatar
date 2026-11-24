# Modern Button & Spinning RGB Avatar

A small, dependency-free front-end demo built with plain HTML and CSS. It shows three UI elements on a dark indigo background:

1. A modern gradient button with a springy hover effect
2. A loading spinner
3. A circular profile picture surrounded by a spinning red / green / blue ring with a soft glow

The animations use eased timing (not constant-speed rotation), so the motion feels natural rather than mechanical.

---

## Table of Contents

- [Preview](#preview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [How It Works](#how-it-works)
- [Customization](#customization)
- [Using Your Own Profile Picture](#using-your-own-profile-picture)
- [Accessibility](#accessibility)
- [Browser Support](#browser-support)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Preview

When you open the page, you will see (top to bottom, centered):

| Element | Description |
| --- | --- |
| **Hello World! button** | Blue-to-purple gradient, rounded corners, lifts on hover and presses down on click |
| **Spinner** | 50px ring with a blue/purple spinning segment |
| **Avatar** | 150px circular photo with a spinning RGB ring and a pulsing glow |

The page background is a deep indigo-violet gradient with two blurred color glows that drift slowly in the corners.

---

## Features

- **Zero dependencies**: no frameworks, no build step, no JavaScript
- **Cohesive theme**: the background, button, spinner and glows share one palette defined with CSS variables
- **Natural motion**: eased `cubic-bezier` timing on spinning elements, a spring-style hover on the button, and a breathing glow on the avatar
- **Pure CSS animation**: the ring is a `conic-gradient` rotated with `@keyframes`
- **Responsive and centered**: the layout stays centered at any viewport size
- **Accessible**: visible keyboard focus on the button and `prefers-reduced-motion` support

---

## Project Structure

```
project/
├── buttton-spinnig_rgb.html   # Page markup (button, spinner, avatar)
├── st.css                     # All styles and animations
└── README.md                  # This file
```

> The HTML file name contains a typo (`buttton`, three t's, and `spinnig`). It is kept as-is so existing links keep working. You can rename it (for example to `index.html`) at any time; nothing in the code depends on the file name.

---

## Getting Started

### Option 1: Open directly

1. Put `buttton-spinnig_rgb.html` and `st.css` in the **same folder**.
2. Double-click the HTML file to open it in your browser.

### Option 2: Run a local server (optional)

Useful if you want live reloading or plan to load local images.

```bash
# Python 3
python3 -m http.server 8000

# or Node.js
npx serve .
```

Then visit `http://localhost:8000`.

---

## How It Works

### 1. HTML structure

```html
<button class="btn-modern">Hello World!</button>
<div class="spinner"></div>
<div class="avatar-ring">
  <img src="..." alt="Profile Picture" class="avatar-img">
</div>
```

`<body>` is a flex column, so the three elements are stacked and centered with a 48px gap.

### 2. Background

- `body` uses a `radial-gradient` from `--bg-3` through `--bg-2` to `--bg-1`.
- `body::before` and `body::after` are two large, blurred circles (blue and purple) fixed in opposite corners.
- Both circles run a slow `drift` animation (22s and 28s) with `alternate`, so they move back and forth without a visible loop point.
- They sit at `z-index: -1` behind all content and have `pointer-events: none`, so they never block clicks.

### 3. Button (`.btn-modern`)

| State | Effect |
| --- | --- |
| Default | `135deg` blue-to-purple gradient with a soft glow shadow |
| Hover | Moves up 3px, with a brighter purple shadow |
| Active (pressed) | Moves down 1px and scales to 98% |
| Keyboard focus | A white outline appears via `:focus-visible` |

The transform uses `cubic-bezier(0.34, 1.56, 0.64, 1)`, which overshoots slightly and settles. This gives the springy feel.

### 4. Spinner (`.spinner`)

- A transparent-white ring with two colored border sides (`--accent-1`, `--accent-2`).
- It rotates using the shared `spin` keyframes with `--ease-soft`, so it eases through each turn instead of rotating at a constant speed.

### 5. Avatar ring (`.avatar-ring`)

This is the most involved part:

```
.avatar-ring               -> 150x150 circle, padding: 5px (ring thickness)
  ├── ::before             -> spinning conic-gradient (the visible ring)
  ├── ::after              -> same gradient, blurred, lower opacity (the glow)
  └── .avatar-img          -> the photo, drawn on top
```

- `isolation: isolate` creates a stacking context so the negative `z-index` layers stay inside the avatar and do not slip behind the page background.
- Both pseudo-elements are rotated; the image itself never moves, so the photo stays upright.
- The ring thickness is the `padding` on `.avatar-ring`. The photo fills the inside, and the gradient shows around it.
- `::after` also runs a `breathe` animation (4s) that fades the glow in and out.

### 6. Animations

| Name | Used by | Purpose |
| --- | --- | --- |
| `spin` | spinner, ring, glow | 0 to 360 degree rotation |
| `breathe` | ring glow | Opacity pulse between 0.35 and 0.7 |
| `drift` | background glows | Slow translate and scale movement |

---

## Customization

### Change the color theme

All main colors are CSS variables at the top of `st.css`:

```css
:root {
  --bg-1: #0f0c29;      /* darkest background */
  --bg-2: #1b1646;      /* mid background */
  --bg-3: #2a1f5e;      /* lightest background (top-left) */
  --accent-1: #6e8efb;  /* blue accent: button, spinner, glow */
  --accent-2: #a777e3;  /* purple accent: button, spinner, glow */
  --ease-soft: cubic-bezier(0.45, 0.05, 0.55, 0.95);
}
```

Change these values and the whole page follows.

### Change the ring colors

Edit the gradient in `.avatar-ring::before, .avatar-ring::after`:

```css
background: conic-gradient(from 0deg, #ff0000, #00ff00, #0000ff, #ff0000);
```

- **Keep the last color equal to the first** so the loop has no visible seam.
- **For hard color blocks** (the original look), use:
  ```css
  conic-gradient(#ff0000 0deg 120deg, #00ff00 120deg 240deg, #0000ff 240deg 360deg);
  ```

### Change the ring thickness

```css
.avatar-ring { padding: 5px; }  /* try 3px (thin) to 10px (thick) */
```

### Change the avatar size

```css
.avatar-ring { width: 150px; height: 150px; }
```

Keep width and height equal so it stays a circle.

### Change the animation speed

```css
.avatar-ring::before,
.avatar-ring::after { animation: spin 5s var(--ease-soft) infinite; }
```

- Lower the number (for example `3s`) to spin faster; raise it to spin slower.
- If you change the speed of `::after`, update it in the combined `animation:` line for `.avatar-ring::after` too, so the glow stays in sync.
- Replace `var(--ease-soft)` with `linear` for a constant, mechanical speed.

### Change the glow strength

```css
.avatar-ring::after { filter: blur(18px); opacity: 0.55; }
```

Increase `blur` for a wider glow, and edit the `breathe` keyframes to change how much it pulses.

### Change the button text and size

Edit the text in the HTML. In CSS:

```css
.btn-modern { font-size: 1.125rem; padding: 14px 32px; }
```

### Add behavior to the button

The button has no JavaScript attached. To add an action:

```html
<button class="btn-modern" onclick="alert('Hello!')">Hello World!</button>
```

---

## Using Your Own Profile Picture

The sample image URL is a Facebook CDN link with an expiring token (`oe=...`), so **it will stop loading eventually**. For a permanent setup:

1. Save your image into the project folder (for example `avatar.jpg`).
2. Update the `src` in the HTML:
   ```html
   <img src="avatar.jpg" alt="Profile Picture" class="avatar-img">
   ```

Tips:
- Use a roughly square image. `object-fit: cover` crops it to fill the circle.
- 300x300px or larger looks sharp on high-density screens.
- Update the `alt` text so it describes the image (for example, `alt="Portrait of Jane"`).

---

## Accessibility

- **Reduced motion**: if the user's system has "reduce motion" enabled, all animations (background drift, spinner, ring, glow) are turned off through `@media (prefers-reduced-motion: reduce)`.
- **Keyboard focus**: the button shows a clear white outline when focused with the keyboard.
- **Alt text**: the avatar image has an `alt` attribute; make it meaningful for your own image.
- **Contrast**: white text on the blue-purple gradient has strong contrast against the dark background.
- **Spinner note**: the spinner is purely decorative here. If you use it to show real loading, add `role="status"` and a visually hidden label such as "Loading".

---

## Browser Support

Works in current versions of Chrome, Edge, Firefox, Safari and their mobile versions.

Features used and their requirements:

| Feature | Notes |
| --- | --- |
| CSS custom properties | All modern browsers |
| `conic-gradient` | Chrome 69+, Firefox 83+, Safari 12.1+ |
| `inset` shorthand | Chrome 87+, Firefox 66+, Safari 14.1+ |
| `:focus-visible` | Chrome 86+, Firefox 85+, Safari 15.4+ |
| `isolation` | All modern browsers |

---

## Troubleshooting

| Problem | Likely cause and fix |
| --- | --- |
| Page has no styling | `st.css` is not in the same folder as the HTML file, or the file name differs. |
| Avatar image is blank or broken | The Facebook URL expired. Use a local image (see above). |
| Ring is not visible | Check that `.avatar-ring` still has `padding` and that the pseudo-elements have `content: ""`. |
| Glow appears behind the whole page or is cut off | Make sure `isolation: isolate` is still set on `.avatar-ring`, and do not add `overflow: hidden` to it. |
| Nothing animates | Your OS may have reduce-motion on. Turn it off to see the animations. |
| Ring "jumps" at the end of each turn | The first and last colors of the gradient are different. Make them equal. |
| Page scrolls or clips content on small screens | `body` uses `overflow: hidden` to hide the background glows. Remove it if you add more content, and set `overflow-x: hidden` on `html` instead. |

---

## License

Free to use, modify and share. Add a license of your choice (for example MIT) if you plan to publish it.
