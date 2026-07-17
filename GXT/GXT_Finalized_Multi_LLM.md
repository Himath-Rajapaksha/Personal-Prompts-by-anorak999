# GXT — GNOME eXtension Toolkit

A recursive checklist to run against a GNOME extension project at **any** point —
new project, mid-feature, "why is this broken," or pre-release. Don't treat this
as linear stages; treat it as a set of checks you re-run whenever something feels
"dumb broken." Most GNOME extension pain comes from a small, repeating set of
category mismatches — this protocol exists to catch those before they turn into
a 2-hour debugging session.

---

## CHECK 0 — Identity: what am I actually building?

Before touching code, answer these. If you can't answer all four, stop and find out —
guessing here is the #1 source of cascading bugs.

- [ ] Target **GNOME Shell version** (or range)? — 45 was the ESM/API break point. Pre-45 and 45+ are different worlds.
- [ ] Which surface am I touching: **top bar / quick settings / overlay actor** (all `St`, live inside `gnome-shell` process) vs **preferences window** (`Gtk4` + `Adw`, separate process)?
- [ ] Is this component **long-lived** (panel indicator, persistent signal connection) or **transient** (popup, one-shot dialog)?
- [ ] Does it touch **GSettings/schema**, **D-Bus**, or **external processes** (nmcli, systemd, etc.)?

Re-ask this whenever you paste in a new snippet or come back after time away — API drift is invisible until it crashes.

---

## CHECK 1 — Import & module hygiene

- [ ] Shell version ≥45? → ESM only. `import GObject from 'gi://GObject'`, no `imports.misc.extensionUtils`, no `imports.gi.*` inside extension.js/prefs.js.
- [ ] `metadata.json` `"shell-version"` array matches the actual runtime — a mismatch silently disables the extension, no error surfaces.
- [ ] `enable(context)`/`disable()` signatures match current API — `this.path`/`this.dir`/`this.metadata` replace the old `Me = ExtensionUtils.getCurrentExtension()` pattern in 45+.
- [ ] No legacy (`var Me = ...`) and ESM (`import ... from`) styles mixed in the same file — a classic AI-assistant copy-paste artifact from mismatched doc eras.

---

## CHECK 2 — St vs Gtk4 boundary (the recursive-bug generator)

This is the single most common "dumb issue." Shell-side UI is **St**, not Gtk.
Preferences windows are **Gtk4 + Adwaita**. They are not interchangeable and
do not share a widget set.

- [ ] Every actor added to `Main.panel`, `Main.layoutManager`, quick settings, or any overlay is `St.*` (or `Clutter.*`), never `Gtk.*`.
- [ ] Every widget in `prefs.js`'s `fillPreferencesWindow` (or `getPreferencesWidget` pre-45) is `Gtk.*`/`Adw.*`, never `St.*`.
- [ ] No copy-pasted snippet is mixing the two because it "looked similar" — if a widget name (`Box`, `Label`, `Button`) appears, confirm which namespace it's imported from at the top of the file.
- [ ] CSS applied via `style-class` (St, uses `stylesheet.css`) is not confused with GTK CSS providers (Gtk4, different property support).

---

## CHECK 3 — Lifecycle symmetry (memory leak / "works once then breaks" bugs)

Treat `enable()`/`disable()` as a contract that must be perfectly symmetric. Test
by toggling on/off ~20-50x via `gnome-extensions disable/enable <uuid>` — leaks
and stale references usually only show up after repeated cycles, not the first run.

- [ ] Every `.connect()` → tracked and `.disconnect()`'d in `disable()` (signal IDs, not just "hope GC handles it").
- [ ] Every `GLib.timeout_add`/`GLib.idle_add` → its source ID stored and `GLib.Source.remove()`'d in `disable()`.
- [ ] Every actor added to `Main.panel`/layout in `enable()` → explicitly `.destroy()`'d in `disable()`, not just dereferenced.
- [ ] No module-level (top-of-file) mutable state persists across disable/enable cycles unless deliberately designed to.
- [ ] Any subprocess spawned (`Gio.Subprocess`, `GLib.spawn_async`) has its watch/exit handler cleaned up, and the process itself is killed on `disable()` if it should be.
- [ ] Settings object (`this.getSettings()`) connections are disconnected too — easy to forget since it doesn't feel like "your" signal.

---

## CHECK 4 — Settings & schema

- [ ] Ran `glib-compile-schemas schemas/` after **any** edit to `.gschema.xml` — stale compiled schema silently serves old/default values with zero errors.
- [ ] Schema `id` in the `.gschema.xml` matches exactly what `getSettings()`/`ExtensionUtils.getSettings()` expects (typos here fail silently).
- [ ] Key names/types in schema match what the code reads (`get_boolean` vs `get_string` mismatch throws, but a name typo just returns undefined/default silently).
- [ ] If prefs UI binds a widget to a setting (`settings.bind(...)`), confirm the bind flags (`Gio.SettingsBindFlags`) match intended direction (default is bidirectional — surprising for one-way UI elements).

---

## CHECK 5 — External processes / system integration

(Relevant for anything shelling out — nmcli, systemctl, custom daemons, etc.)

- [ ] Subprocess calls are async (`Gio.Subprocess` + `communicate_utf8_async`), never a blocking sync call inside the Shell process — a hang here freezes the entire desktop, not just your extension.
- [ ] Errors from the subprocess (non-zero exit, missing binary) are caught and surfaced/logged, not silently swallowed.
- [ ] Any polling loop (`timeout_add` checking process/network state) has a sane interval — too tight and you're burning cycles in the compositor thread's neighborhood.

---

## CHECK 6 — Debugging loop breakers

When something's "dumb broken" and you're about to recursively debug, use the
right tool before touching code again:

- [ ] Check live logs first: `journalctl -f -o cat /usr/bin/gnome-shell` (Wayland) — don't guess from `console.log`/`print()` alone.
- [ ] For crashes that kill your session: test in a **nested session** — `dbus-run-session -- gnome-shell --nested --wayland` — so a crash doesn't take down your actual desktop and lose context.
- [ ] Use Looking Glass (`Alt+F2` → `lg`) to inspect live actor state/objects rather than adding print statements and restarting.
- [ ] On X11 you can `Alt+F2` → `r` to restart Shell in-place; on Wayland you must log out/in — know which session you're in before assuming a restart shortcut works.
- [ ] If an error appears only intermittently, suspect signal/timeout leak (Check 3) before suspecting logic — most "works then randomly breaks" bugs in extensions are lifecycle bugs, not logic bugs.

---

## CHECK 7 — Pre-release / packaging sanity

- [ ] `metadata.json` has correct `uuid`, matches directory name, matches `settings-schema` id references.
- [ ] No leftover `console.log`/`print()` debug spam left enabled in shipped code (fine for dev, noisy in `journalctl` for users).
- [ ] Test a clean install (fresh user or VM) — local dev environments accumulate compiled schemas, gsettings overrides, and cached state that mask packaging bugs.
- [ ] License and any `extensions.gnome.org` review requirements (no bundled binaries fetched at runtime, no unreviewed eval'd code) if publishing there.

---

## How to actually use this

Don't run all 7 top to bottom every time. Instead:

1. **Starting something new / new surface** → run Check 0, 1, 2 before writing code.
2. **"It worked, now it doesn't" / weird intermittent bug** → jump straight to Check 3 and Check 6.
3. **Settings not taking effect** → Check 4 first, always, before touching logic.
4. **Anything touching a subprocess** → Check 5.
5. **About to publish / share the repo** → Check 7.
6. **Copy-pasted a snippet from an old blog post / old AI output** → Check 1 and 2 — this is the single most common source of version-mismatch bugs.

The point isn't to do this in order — it's to have named categories so that "dumb issue" becomes "oh, this is a Check 3 leak" instead of an undirected debugging spiral.
