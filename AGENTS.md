# Agent playbook

`@johnhenry/cyclable` — a small, self-contained family of four modules
built around one idea: cycle a localStorage-backed value through a fixed
list of strings. One engine, three consumption shapes: the raw function
(`localstorage-cycler`), a DOM-class-applying wrapper around it
(`localstorage-class-cycler`), and two ready-made custom elements built on
that wrapper (`class-cycler.component`, `class-cycler.button.component`).
Single package, Node >= 26, one subpath export per module
(`"./*": "./src/*"`) — no shared barrel, no build step; every module ships
as source, unbundled.

`CLAUDE.md` in this directory is a symlink to this file.

## The verification loop (before every push)

1. `node --check` every `.mjs`/`.js` file you touched — there's no
   bundler to catch a syntax error for you. `npm test` runs this over
   everything under `src/` (`scripts/check-syntax.mjs`).
2. This package has no DOM test environment configured (no jsdom/
   happy-dom) — verify a DOM-touching change against the usage example in
   the relevant module's own `readme.md`, in a real browser.
3. `npm pack --dry-run` — confirm the file list still includes every
   module's real files.
4. A genuinely fresh clone: `git clone . /tmp/cyclable-verifyN && cd $_ && npm ci && npm test`.

## Repo-specific gotchas

- **All four modules live at the same directory depth,
  `src/<module>/...`, with plain sibling-relative imports** —
  `class-cycler.component/index.mjs` and
  `class-cycler.button.component/index.mjs` both import
  `../localstorage-class-cycler/index.mjs`, and
  `localstorage-class-cycler/index.mjs` imports
  `../localstorage-cycler/index.mjs`. This is the same flat convention
  `@johnhenry/domkit` (this package's immediate former home) uses; no
  path rewriting was needed when these four modules were extracted from
  it, because both packages collapse `johnhenry/lib`'s
  `js/<module>/0.0.0/index.mjs` layout to `src/<module>/index.mjs` the
  same way.
- **`localstorage-cycler` has no DOM dependency at all** — it only
  touches `globalThis.localStorage` and, if a handler is passed, fires a
  `CustomEvent` for the initial call. It's the one module in this package
  that would work verbatim in a service worker or any other
  `localStorage`-having, DOM-less context.
- **The two custom elements are alternate front-ends for the exact same
  engine, not independent implementations.** If you fix a bug in cycling
  behavior itself (index wraparound, missing-key handling, the handler
  contract), it almost certainly belongs in `localstorage-cycler/index.mjs`,
  not in either component.
- **`class-cycler.component`'s `global` attribute is required** — with no
  `global` attribute set, `reset()` is a no-op (nothing is assigned to
  `globalThis`), by design, not as a bug.
- **`class-cycler.button.component` defaults its target to `html`**, not
  `body` — deliberately different from `class-cycler.component`'s default
  (`body`), inherited as-is from the original `lib` modules. Don't
  "fix" this inconsistency without checking both readmes first — it's
  documented, not accidental.

## Definition of done (changing a module)

- `node --check` passes (`npm test`).
- The module's own `readme.md` stays accurate (real attributes/API, a
  working usage example).
- If you touch a custom element's lifecycle, verify
  `connectedCallback`/`disconnectedCallback` stay symmetric — both
  existing components already correctly clean up their subscriptions
  (`attributeChangedCallback`-driven `globalThis` assignment for
  `class-cycler.component`; a bound click listener for
  `class-cycler.button.component`); don't regress that.

## Non-goals

- No bundling/build step is planned — modules ship as source.
- A real DOM test environment (jsdom/happy-dom + a test runner) doesn't
  exist yet, matching `domkit`'s own current state.

## Releases

Bump `version` in `package.json` in a PR, add a `CHANGELOG.md` entry,
merge, then `gh release create v<version>` (fires
`.github/workflows/publish.yml`, gated on the full CI suite).
