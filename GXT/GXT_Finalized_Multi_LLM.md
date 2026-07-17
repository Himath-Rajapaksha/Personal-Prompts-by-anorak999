<gxt_protocol name="GXT" version="1.0">

<identity>
GNOME eXtension Toolkit. A recursive checklist for GNOME Shell extension development. Run against any project at any point — new project, mid-feature, debugging, or pre-release. Treat as a set of re-usable checks, not linear stages. Most extension pain comes from a small set of category mismatches. This protocol catches them before they become 2-hour debugging sessions.
</identity>

<check id="0" name="Identity: what am I actually building?">
<instruction>Before touching code, answer these. If you cannot answer all four, stop and find out. Guessing here is the number one source of cascading bugs.</instruction>
<items>
- [ ] Target GNOME Shell version (or range)? Version 45 was the ESM/API break point. Pre-45 and 45+ are different worlds.
- [ ] Which surface am I touching: top bar / quick settings / overlay actor (all St, live inside gnome-shell process) vs preferences window (Gtk4 + Adw, separate process)?
- [ ] Is this component long-lived (panel indicator, persistent signal connection) or transient (popup, one-shot dialog)?
- [ ] Does it touch GSettings/schema, D-Bus, or external processes (nmcli, systemd, etc.)?
</items>
<note>Re-ask this whenever you paste in a new snippet or come back after time away. API drift is invisible until it crashes.</note>
</check>

<check id="1" name="Import and module hygiene">
<items>
- [ ] Shell version 45 or above? Use ESM only. import GObject from 'gi://GObject'. No imports.misc.extensionUtils, no imports.gi.* inside extension.js or prefs.js.
- [ ] metadata.json "shell-version" array matches the actual runtime. A mismatch silently disables the extension with no error.
- [ ] enable(context) and disable() signatures match current API. this.path / this.dir / this.metadata replace the old Me = ExtensionUtils.getCurrentExtension() pattern in 45+.
- [ ] No legacy (var Me = ...) and ESM (import ... from) styles mixed in the same file. This is a classic AI-assistant copy-paste artifact from mismatched documentation eras.
</items>
</check>

<check id="2" name="St vs Gtk4 boundary (the recursive-bug generator)">
<instruction>This is the single most common dumb issue. Shell-side UI is St, not Gtk. Preferences windows are Gtk4 + Adwaita. They are not interchangeable and do not share a widget set.</instruction>
<items>
- [ ] Every actor added to Main.panel, Main.layoutManager, quick settings, or any overlay is St.* (or Clutter.*), never Gtk.*.
- [ ] Every widget in prefs.js fillPreferencesWindow (or getPreferencesWidget pre-45) is Gtk.* or Adw.*, never St.*.
- [ ] No copy-pasted snippet is mixing the two because it looked similar. If a widget name (Box, Label, Button) appears, confirm which namespace it is imported from at the top of the file.
- [ ] CSS applied via style-class (St, uses stylesheet.css) is not confused with GTK CSS providers (Gtk4, different property support).
</items>
</check>

<check id="3" name="Lifecycle symmetry (memory leak / works-once-then-breaks bugs)">
<instruction> Treat enable() and disable() as a contract that must be perfectly symmetric. Test by toggling on and off 20 to 50 times via gnome-extensions disable/enable uuid. Leaks and stale references usually only show up after repeated cycles, not the first run.</instruction>
<items>
- [ ] Every .connect() is tracked and .disconnect()ed in disable(). Use signal IDs, not just hoping GC handles it.
- [ ] Every GLib.timeout_add or GLib.idle_add has its source ID stored and GLib.Source.remove()ed in disable().
- [ ] Every actor added to Main.panel or layout in enable() is explicitly .destroy()ed in disable(), not just dereferenced.
- [ ] No module-level (top-of-file) mutable state persists across disable/enable cycles unless deliberately designed to.
- [ ] Any subprocess spawned (Gio.Subprocess, GLib.spawn_async) has its watch/exit handler cleaned up, and the process itself is killed on disable() if it should be.
- [ ] Settings object (this.getSettings()) connections are disconnected too. Easy to forget since it does not feel like your signal.
</items>
</check>

<check id="4" name="Settings and schema">
<items>
- [ ] Ran glib-compile-schemas schemas/ after any edit to .gschema.xml. Stale compiled schema silently serves old or default values with zero errors.
- [ ] Schema id in the .gschema.xml matches exactly what getSettings() or ExtensionUtils.getSettings() expects. Typos here fail silently.
- [ ] Key names and types in schema match what the code reads. get_boolean vs get_string mismatch throws, but a name typo just returns undefined or default silently.
- [ ] If prefs UI binds a widget to a setting (settings.bind(...)), confirm the bind flags (Gio.SettingsBindFlags) match intended direction. Default is bidirectional, which is surprising for one-way UI elements.
</items>
</check>

<check id="5" name="External processes / system integration">
<instruction>Relevant for anything shelling out — nmcli, systemctl, custom daemons, etc.</instruction>
<items>
- [ ] Subprocess calls are async (Gio.Subprocess + communicate_utf8_async), never a blocking sync call inside the Shell process. A hang here freezes the entire desktop, not just your extension.
- [ ] Errors from the subprocess (non-zero exit, missing binary) are caught and surfaced or logged, not silently swallowed.
- [ ] Any polling loop (timeout_add checking process or network state) has a sane interval. Too tight and you are burning cycles in the compositor thread's neighborhood.
</items>
</check>

<check id="6" name="Debugging loop breakers">
<instruction>When something is dumb broken and you are about to recursively debug, use the right tool before touching code again.</instruction>
<items>
- [ ] Check live logs first: journalctl -f -o cat /usr/bin/gnome-shell (Wayland). Do not guess from console.log or print() alone.
- [ ] For crashes that kill your session: test in a nested session — dbus-run-session -- gnome-shell --nested --wayland — so a crash does not take down your actual desktop and lose context.
- [ ] Use Looking Glass (Alt+F2 then lg) to inspect live actor state and objects rather than adding print statements and restarting.
- [ ] On X11 you can Alt+F2 then r to restart Shell in-place. On Wayland you must log out and in. Know which session you are in before assuming a restart shortcut works.
- [ ] If an error appears only intermittently, suspect signal or timeout leak (Check 3) before suspecting logic. Most works-then-randomly-breaks bugs in extensions are lifecycle bugs, not logic bugs.
</items>
</check>

<check id="7" name="Pre-release / packaging sanity">
<items>
- [ ] metadata.json has correct uuid, matches directory name, matches settings-schema id references.
- [ ] No leftover console.log or print() debug spam left enabled in shipped code. Fine for dev, noisy in journalctl for users.
- [ ] Test a clean install (fresh user or VM). Local dev environments accumulate compiled schemas, gsettings overrides, and cached state that mask packaging bugs.
- [ ] License and any extensions.gnome.org review requirements (no bundled binaries fetched at runtime, no unreviewed eval'd code) if publishing there.
</items>
</check>

<usage_guide>
<scenario trigger="Starting something new or a new surface">Run Check 0, 1, and 2 before writing code.</scenario>
<scenario trigger="It worked, now it does not work, or weird intermittent bug">Jump straight to Check 3 and Check 6.</scenario>
<scenario trigger="Settings not taking effect">Run Check 4 first, always, before touching logic.</scenario>
<scenario trigger="Anything touching a subprocess">Run Check 5.</scenario>
<scenario trigger="About to publish or share the repo">Run Check 7.</scenario>
<scenario trigger="Copy-pasted a snippet from an old blog post or old AI output">Run Check 1 and 2. This is the single most common source of version-mismatch bugs.</scenario>
<principle>The point is not to do this in order. It is to have named categories so that dumb issue becomes oh, this is a Check 3 leak instead of an undirected debugging spiral.</principle>
</usage_guide>

</gxt_protocol>
