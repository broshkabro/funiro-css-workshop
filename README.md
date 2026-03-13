# funiro-css-workshop

A static landing page built with **HTML + SCSS**, bundled with **Vite**.
This project is created for educational purpose only.

---

## Getting Started

```bash
npm install
npm run dev
```

Vite starts a dev server at `http://localhost:5173` with hot reload.

```bash
npm run build     # production build → /dist
npm run preview   # preview the production build locally
npm run lint      # run ESLint on src/**/*.js
npm run format    # run Prettier on all files
```

---

## Project Structure

```
funiro/
├── index.html                        # page entry point, all HTML sections
├── vite.config.js                    # Vite configuration
├── eslint.config.js                  # ESLint flat config (v9)
├── .prettierrc                       # Prettier formatting rules
├── .gitignore
├── package.json
└── src/
    ├── js/
    │   └── main.js                   # JS entry — imports SCSS, future modules
    └── scss/
        ├── main.scss                 # master file — imports everything in order
        ├── base/
        │   ├── _variables.scss       # all design tokens
        │   ├── _reset.scss           # CSS reset and base element styles
        │   └── _typography.scss      # Google Fonts import
        ├── utils/
        │   └── _mixins.scss          # reusable SCSS mixins
        ├── layout/
        │   └── _layout.scss          # .container, .sr-only
        └── components/
            ├── _globals.scss         # .btn, .dot, .arrow-btn, section titles
            ├── _header.scss
            ├── _hero.scss
            ├── _trust-bar.scss
            ├── _product-card.scss
            ├── _inspiration.scss
            ├── _blog.scss
            ├── _gallery.scss
            └── _footer.scss
```

---

## How Styles Are Loaded

Vite does not load CSS directly from HTML. Instead, SCSS is imported inside the JS entry point:

```js
// src/js/main.js
import '../scss/main.scss';
```

Vite compiles the SCSS and injects the resulting CSS into the page automatically.

---

## SCSS Architecture

### Import order in `main.scss`

```
1. base/variables    — tokens must be available first
2. utils/mixins      — mixins depend on variables
3. base/reset        — base element styles
4. base/typography   — font imports
5. layout/layout     — structural helpers
6. components/*      — one file per section
```

Order matters — variables must be loaded before anything that uses them.


---

### Using variables and mixins in a component file

Every component file must import both at the top:

```scss
@use '../base/variables' as *;
@use '../utils/mixins' as *;
```

The `as *` makes tokens available without a namespace prefix, so you can write `$clr-primary` directly instead of `variables.$clr-primary`.

The path `../base/variables` is relative to the file — adjust `../` depth if the file is nested deeper.

### Adding a new component

1. Create `src/scss/components/_my-component.scss`
2. Add `@use` imports at the top
3. Register it in `main.scss`:

```scss
@use 'components/my-component';
```

---


## Mixins (`_mixins.scss`)

### `respond-to($bp)`

Mobile-first media query shorthand.

```scss
@include respond-to(md) {
  // applies from 768px upward
}
```

### `flex($dir, $align, $justify, $gap)`

Flex container shorthand. All arguments are optional after the first.

```scss
@include flex(row, center, space-between, $sp-4);

    // compiles to:
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    gap: 1rem;

```

### `fluid-type($min, $max)`

Smoothly scales font size between two values using `clamp()`.

```scss
h1 {
  @include fluid-type(1.75rem, 3.25rem);
}

// compiles to:
font-size: clamp(1.75rem, 3vw, 3.25rem);
```

### `truncate($lines)`

Clips overflowing text with an ellipsis.

```scss
@include truncate;     // single line  →  "Long text that overfl..."
@include truncate(2);  // two lines    →  clips after second line
```

### `sr-only`

Visually hides an element while keeping it readable by screen readers.

```scss
.label {
  @include sr-only;
}
```

### `focus-ring($color, $offset)`

Consistent keyboard focus outline applied to all interactive elements.

```scss
@include focus-ring;             // default color + offset
@include focus-ring(red, 4px);  // custom
```

---

## Responsive Strategy

The project uses **mobile-first CSS** — base styles target small screens, and `@include respond-to()` adds styles for larger screens.

Layout changes at key breakpoints:

| Section | Mobile | Tablet (768px) | Desktop (1024px+) |
|---|---|---|---|
| Header | hamburger only | hamburger only | full nav + search |
| Hero | stacked | stacked | side by side |
| Products | 1 column | 2 columns | 4 columns |
| Inspiration | stacked | stacked | side by side |
| Blog | 1 column | 1 column | 3 columns |
| Gallery | 2 columns | 3 columns | 5 columns |
| Footer | 1 column | 2 columns | 5 columns |

---

## Accessibility

- Semantic HTML5 elements throughout (`header`, `nav`, `main`, `section`, `article`, `footer`)
- Correct heading hierarchy — `h1` → `h2` → `h3`, never skipped
- All images have descriptive `alt` text
- Icon-only buttons have `aria-label`
- Decorative elements marked `aria-hidden="true"`
- Visible focus ring on all interactive elements via `@include focus-ring`
- Visually hidden labels using `.sr-only` where text would clutter the UI
- `aria-current="page"` on the active nav link

---

## Code Style

**ESLint** — lints JS files in `src/`. Rules: `prefer-const`, `eqeqeq`, `curly`, no unused vars.

**Prettier** — formats all files. Key settings: single quotes, 2 space indent, 100 char print width, trailing commas.

Run both before committing:

```bash
npm run lint
npm run format
```
