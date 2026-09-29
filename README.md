# cyclable

[![npm version](https://img.shields.io/npm/v/%40johnhenry%2Fcyclable.svg)](https://www.npmjs.com/package/@johnhenry/cyclable)
[![CI](https://github.com/johnhenry/cyclable/actions/workflows/ci.yml/badge.svg)](https://github.com/johnhenry/cyclable/actions/workflows/ci.yml)
[![license](https://img.shields.io/npm/l/%40johnhenry%2Fcyclable.svg)](LICENSE)

Full documentation: [opensource.johnhenry.me/cyclable](https://opensource.johnhenry.me/cyclable/)

> **Provenance:** originally four individually-versioned modules under
> [`johnhenry/lib`](https://github.com/johnhenry/lib)'s `js/` directory
> (`js/localstorage-cycler/0.0.0/`, `js/localstorage-class-cycler/0.0.0/`,
> `js/class-cycler.component/0.0.0/`, `js/class-cycler.button.component/0.0.0/`),
> then briefly part of [`@johnhenry/domkit`](https://github.com/johnhenry/domkit)'s
> ~33-module toolkit, now extracted into this standalone package because
> the four form a real, coherent family — one engine plus wrapper variants
> — rather than an unrelated grab-bag of widgets. `domkit` no longer
> carries this functionality. See `## Family` below and `CHANGELOG.md` for
> the full history.

A localStorage-backed value cycler: **one engine, three consumption
shapes.** `localStorageCycler` cycles a `localStorage`-backed value
through a fixed list of strings and calls an optional change handler.
`classStorageCycler` wraps it to apply the cycled value as a CSS class on
a DOM element. `<class-cycler>` and
`<button is="class-cycler-button">` are two ready-made custom elements
built on that wrapper — a global-function variant and a self-contained
button variant, respectively. All four modules are independent files
under `src/`, importable individually with no shared barrel and no build
step.

## Install

```bash
npm install @johnhenry/cyclable
```

## `localstorage-cycler` — the engine

Cycle a `localStorage` value through a given list of strings.

```javascript
import localStorageCycler from "@johnhenry/cyclable/localstorage-cycler/index.mjs";
const updateLocalStorage = localStorageCycler("my-key", "a", "b", "c");
```

The call to `localStorageCycler` checks for the existence of the key
(`"my-key"`) in `localStorage`, and sets it to the first value (`"a"`) if
not already set.

When called, the returned `updateLocalStorage` function cycles the value
associated with the key in `localStorage` through the given values (`"a"`,
`"b"`, and `"c"`).

`updateLocalStorage` returns an object with the following keys:

- `key` — the associated local storage key
- `value` — the current value of the local storage item
- `index` — the current index of the local storage item
- `result` — the result of a handler, if passed (see below)

### Change handler

To react to the change, pass an optional change handler as the second
parameter to `localStorageCycler`.

```javascript
const onChange = ({ value, key, index, events }) =>
  console.log({ value, key, index, events });
const updateLocalStorage = localStorageCycler(
  "my-key",
  onChange,
  "a",
  "b",
  "c"
);
```

The handler takes four parameters:

- the same `key`, `value`, and `index` parameters returned from calling
  `updateLocalStorage`
- an `events` parameter — an array of everything passed into the
  `updateLocalStorage` function, or an `init` `CustomEvent` if fired from
  the initial call to `localStorageCycler`

## `localstorage-class-cycler` — apply the cycled value as a class

Cycle a `localStorage` value through a given list of strings, and render
the cycled value as a class on a given element.

```javascript
import classStorageCycler from "@johnhenry/cyclable/localstorage-class-cycler/index.mjs";
const updateBodyClass = classStorageCycler(
  document.body,
  "my-key",
  "a",
  "b",
  "c"
);
updateBodyClass();
```

## `class-cycler.component` — global-function custom element

Exposes a `localstorage-class-cycler` instance as a named global
function, so it can be called from anywhere (e.g. a plain `<button
onclick="...">`).

| Attribute | Description |
|---|---|
| `global` | Name to assign the cycler function to on `globalThis` (required — removing this attribute, or disconnecting the element, unassigns it) |
| `selector` | Selector for the element whose classes get cycled. Defaults to `body` |
| `storage-key` | localStorage key the current class value persists under |
| `classes` | Comma-delimited list of classes to cycle through |

```html
<script
  type="module"
  src="https://esm.sh/@johnhenry/cyclable/class-cycler.component/global.mjs"
></script>
<class-cycler
  global="cycleTheme"
  selector="body"
  storage-key="theme"
  classes="light,dark,system"
></class-cycler>
<button onclick="cycleTheme()">Toggle theme</button>
```

## `class-cycler.button.component` — self-contained button custom element

A `<button is="class-cycler-button">` that cycles the classes of one or
more target elements every time it's clicked.

| Attribute | Description |
|---|---|
| `select` | Selector for a single target element. Defaults to `html` if neither `select` nor `select-all` is set |
| `select-all` | Selector for multiple target elements (all matches get cycled together) |
| `storage-key` | localStorage key the current class value persists under |
| `classes` | Comma-delimited list of classes to cycle through |

```html
<script
  type="module"
  src="https://esm.sh/@johnhenry/cyclable/class-cycler.button.component/global.mjs"
></script>
<button
  is="class-cycler-button"
  select="body"
  storage-key="theme"
  classes="light,dark,system"
>
  Toggle theme
</button>
```

## Family

- [`@johnhenry/domkit`](https://github.com/johnhenry/domkit) — the
  ~33-module DOM/HTML-component toolkit this package was extracted from.
  domkit is a real npm dependency of neither direction of this
  relationship — it does not depend on cyclable, and cyclable does not
  depend on domkit; the four modules here simply no longer live in
  domkit's `src/`.
- [`@johnhenry/domable`](https://github.com/johnhenry/domable) — the DOM
  ⇄ text ⇄ React conversion primitives domkit itself builds on. Unrelated
  to this package's functionality; listed here only because it's the
  other sibling extraction in this same lineage
  (`johnhenry/lib` → `johnhenry/domkit` → standalone package).

## License

MIT
