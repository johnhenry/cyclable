# Changelog

All notable changes to this project will be documented in this file.

## [0.0.0] - 2026-09-29

Initial release. Extracted as a standalone package from
[`@johnhenry/domkit`](https://github.com/johnhenry/domkit)'s `src/`
directory — specifically `src/localstorage-cycler/`,
`src/localstorage-class-cycler/`, `src/class-cycler.component/`, and
`src/class-cycler.button.component/`, copied verbatim with no code
changes (all four already used the same flat, sibling-relative-import
`src/<module>/...` layout this package also uses, so no path rewriting
was needed).

domkit itself originally received these four modules from
[`johnhenry/lib`](https://github.com/johnhenry/lib)'s `js/` directory
(`js/localstorage-cycler/0.0.0/`, `js/localstorage-class-cycler/0.0.0/`,
`js/class-cycler.component/0.0.0/`,
`js/class-cycler.button.component/0.0.0/`) during domkit's own 0.0.0
extraction from `lib`.

The extraction reason: these four form a real, coherent family — one
`localStorageCycler` engine, a DOM-class-applying wrapper around it, and
two custom elements built on that wrapper — not just unrelated widgets
that happened to ship in the same toolkit. This mirrors the precedent set
by [`@johnhenry/domable`](https://github.com/johnhenry/domable), an
earlier cluster extracted from the same `lib` → `domkit` lineage.
`domkit` no longer carries this functionality as of its own corresponding
removal.

No behavioral changes; no dependencies (this cluster has none, runtime or
dev).
