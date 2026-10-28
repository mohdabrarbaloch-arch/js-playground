# JS Playground

A browser-based JavaScript playground: a split-pane editor and console that runs your code in a real JavaScript engine, sandboxed in a Web Worker. No build step, no dependencies, no server — the entire app is one self-contained `index.html`.

**Live demo:** https://static.teamily.ai/sites/47009ffe-980b-4643-82f9-2de4b0791106/webpages/js-playground/index.html

---

## Features

### Split-pane editor + console
- Custom syntax-highlighted editor (keywords, strings, numbers, comments, builtins) with a synced line-number gutter.
- Auto-indent, bracket and quote pairing, Tab / Shift+Tab block indent, `Ctrl+/` comment toggle, and a re-indent button.
- Draggable divider to resize the panes; on narrow screens the panes stack vertically.

### Real JS engine, sandboxed
- Every run spins up a **fresh Web Worker** with its own global scope, so `console.log`, `await`, timers and promises all behave natively.
- Because the code runs off the main thread, an infinite loop cannot freeze the page. A timeout selector (1s / 5s / 15s) terminates runaway code and reports it.
- Snippets execute as an async function body, so **top-level `await`** works and a trailing bare expression is echoed as the **return value** (devtools-style), including `({ ... })` object literals.

### Console capture
- Captures `log`, `info`, `warn`, `error`, `debug`, `table` (real box-drawn tables), `count`, `time` / `timeEnd`, `group`, `assert` and `trace`.
- Uncaught exceptions and unhandled promise rejections are reported with the **exact line and column** in your snippet; syntax errors are located by bisection and the offending gutter line is highlighted red.
- Late async output is tagged `async` so you can tell it apart from synchronous output.
- **Clear** and **Run** buttons, plus `Ctrl/⌘+Enter` to run and `Ctrl+L` to clear.

### Snippet library
Eight starters grouped by topic, filterable, with your edits persisted in `localStorage`:

| Snippet | What it teaches |
| --- | --- |
| Closures & private state | Lexical scope, `var` vs `let` loop capture |
| Promises & combinators | `all`, `allSettled`, `race`, `any` |
| async / await & errors | Sequential vs parallel timing, `try`/`catch` |
| Array methods | `map`/`filter`/`reduce` with a tally |
| Event loop: micro vs macro | Microtask vs macrotask ordering |
| Generators & iteration | `function*`, `for await` |
| Classes, getters, private fields | `#private` fields, accessors |
| Gotchas: sort, ==, NaN | Numeric sort, `==`, `NaN`, float math, `typeof null` |

### Execution stats
- **Execution time in ms** for every run, plus a per-run summary line with log / warn / error counts.
- **V8 heap usage** via `performance.memory` where the browser exposes it.
- An engine pill showing the detected JS engine (V8 / SpiderMonkey / JavaScriptCore) and core count.

---

## Run it locally

There is nothing to install and nothing to build. Pick whichever you prefer:

**Option 1 — just open the file**

```bash
git clone https://github.com/mohdabrarbaloch-arch/js-playground.git
cd js-playground
open index.html        # macOS
# xdg-open index.html  # Linux
# start index.html     # Windows
```

**Option 2 — serve it over HTTP** (recommended; some browsers restrict Web Workers on `file://`)

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

Or with Node:

```bash
npx serve .
```

---

## Keyboard shortcuts

| Shortcut | Action |
| --- | --- |
| `Ctrl/⌘ + Enter` | Run the current snippet |
| `Ctrl/⌘ + L` | Clear the console |
| `Tab` / `Shift + Tab` | Indent / outdent selection |
| `Ctrl/⌘ + /` | Toggle line comment |

---

## How it works

1. The editor holds your source as plain text.
2. On **Run**, the app builds a small worker script that wraps your code in an async function, installs a `console` shim that posts structured messages back to the main thread, and reports the return value.
3. The worker is created from a `Blob` URL, so it is same-origin and needs no extra files.
4. The main thread renders each message into the console panel, measures wall-clock execution time, and terminates the worker on timeout.
5. Each run gets a brand-new worker, so state never leaks between runs.

---

## Browser support

Any modern evergreen browser (Chrome, Edge, Firefox, Safari). Web Workers, `Blob` URLs and `async`/`await` are required. `performance.memory` is Chromium-only — on other engines the heap readout is simply hidden.

---

## License

MIT
