# App Management for Raycast Free — Merge Specification

Status: specification for review. Nothing in this repository has been built, copied, compiled, installed, or imported. This repository contains only this file and git metadata.
Decisions taken by the owner on 2026-09-30 after two reviews: repository name `raycast-app-management` and slug `app-management`; title `Window Switcher & Badges`; author `bretbuilds`, because the merged extension is intended for the Raycast Store eventually; settings migration by seed-and-adjust; GitHub remote deferred. Store submission itself remains outside this specification (section 2.2). Store feasibility of the window helper and of the configure command is checked early (section 11.8) and is not assumed.
Written 2026-09-30 against macOS 27.0.1 (26A434), Raycast 2.6.0.0, `@raycast/api` 2.6.0 (installed in both source projects), Swift 6.4 Command Line Tools, Node 24.11.0.

This document merges two working private extensions into one new, independent extension with one everyday hotkey:

| Source project | Path | Identity today | License | Git |
| --- | --- | --- | --- | --- |
| App Window Switcher | `~/dev/projects/raycast-window-switcher` | `e:n:bret/app-window-switcher` (installed at `~/.config/raycast/extensions/app-window-switcher/`) | `UNLICENSED`, `private: true` | not a git repository |
| Dock Notification Badges | `~/dev/projects/badge-count-raycast` | `e:n:bretbuilds/dock-badges` (installed at `~/.config/raycast/extensions/dock-badges/`) | MIT | `main` at `7ddaab1`, clean, remote `bretbuilds/raycast-dock-notification-badges` |

Neither source project is modified by this phase or by any milestone below. Both stay installed and usable until the final cutover check passes.

## Evidence legend

| Tag | Meaning |
| --- | --- |
| `[DOC]` | Stated in current official Raycast documentation read on 2026-09-30, or in the installed `@raycast/api` 2.6.0 type definitions. |
| `[CODE]` | Present in one of the two source projects' code or tests today. Behaviour that the new project inherits by copying, not by rewriting. |
| `[OBSERVED]` | Recorded in one of the two source projects' `VERIFICATION.md` as seen on this Mac, or observed read-only during this phase (files, hashes, log lines, process list). |
| `[UNPROVEN]` | Needed by this design but not demonstrated on this Mac. Each one is assigned to a milestone. |

Untagged statements are design decisions.

---

## 1. Repository and extension identity

### 1.1 Decisions

| Item | Decision | Why |
| --- | --- | --- |
| Repository | `~/dev/projects/raycast-app-management`, its own git history, no remote yet. | Named by the owner on 2026-09-30; the directory was created empty by this phase. |
| Manifest `name` | `app-management` | Distinct from `app-window-switcher` and `dock-badges`. The installed folder is named after `name` (`[OBSERVED]` both projects install at `~/.config/raycast/extensions/<name>/`), so the new copy gets its own folder `~/.config/raycast/extensions/app-management/`. This matters: removing a development entry deleted the folder it shared with another entry once before (`[OBSERVED]` badge VERIFICATION, change 5). |
| Manifest `author` | `bretbuilds` (decided by the owner) | Changing `author` later creates a new extension identity with empty storage and no hotkeys (`[OBSERVED]` badge VERIFICATION, change 5, "Identity change from author"). Using the Raycast username from the start means the identity never has to change when the project is published. `ray lint` validates that the author exists (`[OBSERVED]`). |
| Manifest `title` | `Window Switcher & Badges` (owner's decision, second review) | The Store asks for titles that make it "easy for people to understand what it does when they see it in the Store" and prefers nouns `[DOC]`; "App Management" was too broad. The slug `app-management` stays, since `name` is the identifier and the Store link, not the display text. Whether `ray lint` accepts the ampersand is confirmed in M2; if not, the fallback title is `Window Switcher and Badges`. |
| Resulting identity | `e:n:bretbuilds/app-management` | Distinct from both `e:n:bret/app-window-switcher` and `e:n:bretbuilds/dock-badges` (`[OBSERVED]` in Raycast's log). Its LocalStorage is isolated: "Extensions can not access the storage of other extensions" `[DOC]`. |
| Manifest `license` | `MIT` from the first commit | The Store requires MIT ("Ensure you use `MIT` in the `license` field" `[DOC]`), and with MIT `ray lint` runs fully instead of being replaced by ESLint and Prettier as the `UNLICENSED` window switcher had to do (`[OBSERVED]` window VERIFICATION M2). The owner holds the copyright of both sources, so relicensing the copied window-switcher code from `UNLICENSED` to MIT is the owner's own act (section 10.4). The repository stays private in practice until the owner publishes it; MIT in the manifest does not publish anything. |
| Store path, later | Submission is out of scope, but Store feasibility is checked **early** (rows ST-1 … ST-3 in M1) so that no design element is built on an assumption of acceptance. Facts as they stand: (1) the badge project's committed-binary pattern (binary next to its source, hash test, build command in the README) has **not** been submitted or accepted by Raycast reviewers; its `SPEC-STORE.md` recorded that four already-published extensions ship compiled Swift helpers that way (`[OBSERVED]` in that document, checked against the public `raycast/extensions` repository on 2026-09-30), which is precedent, not approval; (2) the guideline "Don't bundle opaque binaries where sources are unavailable or where it's unclear how they have been built" `[DOC]` requires traceable sources and builds, which both helpers can show, but the window helper also uses undocumented macOS symbols (listed in `PrivateAPI.swift`) and is 430 KB, and reviewer acceptance of that is unknown; (3) a separate configuration command may be questioned by reviewers (the badge project chose one because Raycast preferences have no list or reordering type `[DOC]` badge SPEC §4; the exact guideline text on configuration commands is read and quoted in ST-3). | Nothing in the design depends on passing review: the configure view is reached from the main list and the standalone `configure-app-management` entry can be dropped without any other change; the window helper's private-API use is the same whether the extension is private or public. Store review may still require packaging or dependency changes; those are a separate change after the merged command works locally. |
| Commands | `manage-apps` (`no-view`, the hotkey target), `app-list` (`view`), `configure-app-management` (`view`) | The launcher pattern is what makes every hotkey press a fresh mount and a fresh scan; both source projects use it and both recorded it as verified (`[OBSERVED]` badge U5 fix; window M3 "one scan per open"). |
| Hotkey | One new hotkey on `manage-apps`, recorded by the owner in Raycast Settings. No hotkey on `app-list` or `configure-app-management`. | Section 3.1. Until the cutover (M5) the old hotkeys stay where they are: ⌃⌥D on Show Dock Badges and ⌃⌥G on Configure Dock Badge Apps (`[OBSERVED]` badge VERIFICATION, change 5), plus whatever the owner assigned to Switch Windows by App. |

### 1.2 Things the new extension never does

- It never reads, writes, or scrapes another extension's LocalStorage or Raycast's database.
- It never uninstalls, overwrites, disables, or rebuilds either source extension. Disabling the old commands is the owner's manual step in the cutover check (M5) and comes after verification.
- It never uses `launchCommand` against the old extensions. Cross-extension launch exists `[DOC]` but "the user will be presented with a permission alert" and it would couple the new command to identities that are going away.

---

## 2. Objective and non-goals

### 2.1 Objective

One hotkey opens one app-first list. Every running app with a discovered window is there, so any window (hidden, minimized, on another Desktop, or plain) can be reached and focused exactly by `(pid, CGWindowID)`. The apps whose Dock badges the owner tracks show their current badge text on the same rows, and pinned tracked apps are listed even when they have no window, so the list also works as a launcher. The two existing filters, Pinned + Badged and Badged Only, remain available as explicit alternatives. Configuration (which apps are tracked, their order, pins) is reachable from the main list.

### 2.2 Non-goals

- Changing either helper's Swift source, JSON schema, protocol version, binary, or source hash. The two helpers are copied as-is and composed in TypeScript (section 5). A Swift change is made only if an acceptance row in section 11 cannot be met without it, and it is then recorded as a separate, reviewed change.
- Any resident process: no background watcher, LaunchAgent, login item, menu bar command, `interval`, timer, or polling while the list is closed.
- Raycast Pro or AI APIs, the Pro-only `WindowManagement` API, Screen Recording, network access, analytics.
- Raycast's built-in Switch Windows as a data source or as something to modify.
- Complete system-wide "last used" history. Section 8 defines what "recent" can honestly mean.
- Store publication. The merged extension is intended for the Store eventually (owner's decision, section 1.1), but submitting it is a later, separate change with its own review; nothing in the milestones below publishes anything. The badge project continues on its own Store path unchanged.
- Trash integration in the core milestones (section 9 was added after M5).
- Window-level actions other than focus and app quit (close, minimize, tile), per-tab browser rows, and Desktop numbering ("Desktop 3"), all unchanged from the window switcher's non-goals.

---

## 3. User journeys

### 3.1 Invocation

Press the one hotkey. `manage-apps` calls `launchCommand({ name: "app-list", type: LaunchType.UserInitiated })` `[DOC]`, which mounts `app-list` fresh. On mount the command starts three independent loads at once: configuration from LocalStorage, the window helper, and the badge helper. Rows appear as soon as configuration is loaded (pinned apps in loading state) and fill in as each helper answers (section 6). Nothing is awaited jointly.

### 3.2 The five everyday journeys

| # | Journey | Keystrokes | What must be true |
| --- | --- | --- | --- |
| J1 | See badges | hotkey | Tracked apps with a badge show the badge tag within the badge helper's time; the badge shows even while the window helper is still running or has failed. |
| J2 | Find an app | hotkey, type part of the name, Return | The app row is the only result for that app (its windows are not listed under a name-only match, section 4.5). Return switches to its one window, or its first listed window when it has several (the action label names that window), or opens the app when it has no window. |
| J3 | Restore a specific window | hotkey, type a distinctive title fragment, Return | The app row shows "1 of N matches" with the title as subtitle and Return focuses exactly that `(pid, wid)`, restoring it from minimized, hidden, or another Desktop. |
| J4 | Reach a window by arrowing | hotkey, ↓ to the app, ↓ once more, Return | Down from an expanded multi-window app lands on its first window row, in the same list, and Return focuses it. No pushed view. |
| J5 | Configure | hotkey, ⌘⇧C, pin or add or reorder, Esc | Changes are saved at once and the main list reflects them on return with no helper run. No second everyday hotkey is needed; `configure-app-management` also exists in root search for convenience, and that root-search entry is the one part that ST-3 may remove without changing the journey. |

### 3.3 Journeys that must not regress from the sources

Quit App (⌃Q) from an app row or a window row, with a rescan of windows afterwards; ⌘R refresh; "That window closed" on a stale window with an automatic rescan and never a substitute window (`[CODE]` window `switch-action.ts`); Return on a pinned quit app launches it (`[OBSERVED]` badge U7, V16); Badged Only never reads as "no badges" when the Dock read failed (`[CODE]` badge `views.ts emptyState`, `[OBSERVED]` V13).

---

## 4. Exact row, filter, and action behaviour

### 4.1 The two data sources stay distinct on every row

Every row is built from up to three facts, each with its own unknown state:

| Fact | Source | Values |
| --- | --- | --- |
| Windows | window helper `list` (`[CODE]` window `protocol.ts`) | `loading`, `ok(windows[])`, `failed(failure)` |
| Badge | badge helper (`[CODE]` badge `badge.ts rowState`) | tracked apps only: `loading`, `numeric`, `zero`, `nonNumeric`, `noBadge`, `notInDock`, `notInstalled`, `unavailable(failure)`; untracked apps: `notTracked` (no badge fact is ever shown) |
| Configuration | LocalStorage (section 5.3) | tracked (in the app list), pinned, order index |

Rule: an accessory that states a fact ("No badge", "No windows", "3 windows", a badge tag) is shown only when the corresponding read succeeded. When a read failed, the row shows an unknown marker for that fact, never a zero or an absence. An untracked app never shows "No badge" because no badge was read for it on purpose.

### 4.2 App row

`List.Item` with `id = "app:" + appKey` (section 5.1 defines `appKey`).

| Slot | Content |
| --- | --- |
| `icon` | `{ fileIcon: bundlePath }` from the window helper when running, else from the tracked app's saved path; `Icon.AppWindow` if neither. |
| `title` | App name first, always. |
| `subtitle` | Empty, except: one-window app → that window's display title; search matched exactly one window title → that title (section 4.5); badge `unavailable` → its reason (badge project wording, `[CODE]`). |
| `accessories`, in this order, only those that apply | (1) pin icon when pinned (badge project's accessory, `[CODE]`); (2) badge, tracked apps only: the badge project's exact accessory per state (`[CODE]` `badge-list.tsx accessory()`), including `No badge`, `Not in Dock`, `Not installed`, and the yellow `Unavailable` marker; (3) windows: `N windows` / `1 window` when the window read is ok and N ≥ 1; `No windows` when the window read is ok, N = 0, and the app is shown for another reason (pinned or badged); a yellow warning icon with tooltip `Windows unknown: <failure title>` when the window read failed; nothing while loading; (4) `Hidden` tag, `N minimized` tag, `N on other Desktop` tag (preference-gated exactly as the window switcher's `showOtherDesktop`, `[CODE]`); (5) the window switcher's exclamation accessory for unresolved windows or helper warnings (`[CODE]`); (6) `k of N match` / `1 of N matches` while searching (section 4.5); (7) collapsed groups: `▸` with tooltip `Windows collapsed` (section 7). |
| `keywords` | Bundle ID and the app's file name (badge project, `[CODE]`). Not window titles: filtering is ours (section 4.5). |

**Revision (owner, 2026-09-30, after first use):** the accessory order above put icons between texts and read as clutter. The shipped order is text first, icons last: (2) badge, (3) windows (`N windows` only for N ≥ 2, since a one-window app's subtitle is its window title; `No windows` unchanged), (4) state tags (a one-window app's minimized state reads `Minimized`), (6) match text, (7) `▸`, then (1) the pin icon, the windows-unknown warning, and (5) the exclamation icon at the far right. Facts and their unknown markers are unchanged; only their placement moved.

**Second revision (owner, 2026-09-30, after the first):** (a) window-row titles and app subtitles are clipped to 64 characters with an ellipsis so browser-tab titles do not run to the row's end; the full title stays in the row's tooltip, in search matching, and in Copy Window Title. (b) Every app row and window row ends with one icon slot that is always present: the pin, the warning icons, or a transparent 16×16 `blank.png` placeholder, so the tags of neighbouring rows line up whether or not a row is pinned. (c) The badge moves to the right of the window facts, directly before that icon slot, so counts form one column at the right edge.

**Third revision (owner, 2026-09-30):** the badge slot is always present too (a blank placeholder on untracked and still-loading rows; `No badge` becomes a muted `–` tag with the words in its tooltip), and window rows carry the same two trailing slots, so the window facts (`No windows`, `N windows`, `Minimized`) end in one column on every row. Raycast's List has no true grid: right edges align, widths still vary with the text.

**Fourth revision (owner, 2026-09-30):** Raycast scales accessory images into a 16×16 box (measured with a read-only Accessibility frame dump), so a blank image cannot hold a badge pill's width. The badge placeholder is therefore a tag with a fully transparent colour (`#00000000`) and a one-character value, which has exactly a one-digit badge pill's footprint; measured right edges of every `Minimized` tag now agree within 2 px (1031–1033) across pinned, unpinned, tracked, untracked, and window rows.

**Seventh revision (owner, 2026-09-30):** the `No windows` accessory is removed (a pinned or badged app with no window simply has nothing in the window-fact slot; the primary action `Open App` still says what Return does), and the helper's `ax-budget` warning no longer produces the exclamation icon (it stays in the raw helper output behind Copy Diagnostic Info). The unresolved-window marker and other helper warning codes are unchanged. This narrows §4.1's rule for one fact: "No windows" is no longer stated as text; the windows-unknown warning icon for a failed read is kept, so unknown still never reads as zero.

**Eighth revision (owner, 2026-09-30):** the last offset was the badge pill itself: a pill is as wide as its text, so rows with `1` (a narrow glyph), `•`, `−`, or `6` pushed the tags to their left by different amounts. Every badge value is now padded on the left to the width of three digits (a zero-width word joiner, which Raycast's trimming keeps, followed by non-breaking spaces computed from measured glyph widths), and the blank placeholder has the same footprint. Measured after the change: `Minimized` ends at 1013–1016 px on rows with a blank, `1`, `6`, `−`, and `•`. Cost: badge pills are about 55 px wide with the value at their right edge; values longer than three characters widen their row's slot.

**Hotkey quit commands (owner, 2026-09-30):** two `no-view` commands, `quit-selected-app` and `quit-other-apps`, exist so that Raycast's per-command hotkey recorder (and therefore the Hyper Key) can drive the two quits. The list writes its selected row (`selection.v1`: key, name, bundle ID, pids, timestamp; no titles) and its open state (`listState.v1`) to LocalStorage; a command acts only while the list is open or within 3 s of its close, and only on a selection younger than 30 s, then relaunches `app-list`. Observed by deeplink: a selected Calculator was quit and the list reopened; after the list had been closed for 6 s the command refused. This adds two commands and two keys beyond §1.1 and §5.3. Both commands are `view` commands that mount the list with a startup action (a no-view command hid and re-showed the window), and after acting they hand the window back to `app-list` with the acted-on row selected, so a second press re-mounts the action instead of toggling the window closed. Pids for apps running without windows (absent from the window helper's list) come from `/usr/bin/lsappinfo` (`find bundleID=`, then `info -only pid`), a system binary needing no permission; the row Quit action is offered on every tracked row for the same reason.

**Quit-shortcut preferences removed (owner, 2026-09-30, after the hotkey commands proved out):** the four typed/preset quit-shortcut preferences and the parser are gone; the in-list actions are fixed at ⌃Q / ⌃⇧Q and the hotkey commands carry the owner's recorded (Hyper) keys. The extension's only preference is `Expand windows by default`.

**Preferences trimmed (owner, 2026-09-30):** the `Activation` dropdown is removed and the helper always runs with `--activation auto` (public first, private fallback); the `Show “Other Desktop” tags` preference and the tag itself are removed (§4.2 item 4's Desktop count and §4.3's `Other Desktop` tag no longer render; the helper's Space data is unchanged and still used for discovery). Hyper-key presets for the quit shortcuts were tried and removed: Raycast maps Caps Lock to F20 at the HID level and consumes F20+key as its own hotkeys, so no action inside the list can receive it.

**Ninth revision (owner, 2026-09-30):** the three-digit slot made the pills too wide. Pills are back to one glyph: a numeric Dock badge of ten or more is shown as a red dot (`badgeLabel`; the exact count stays in the tooltip and in Copy Diagnostic Info), so red always means a count and orange a non-numeric badge; only the narrow `1` is padded to a digit's width. This is the one place the merged command departs from the badge project's "never parse the number for display" rule, by the owner's decision. Tag wording `N minimized` becomes `N Minimized` to match the one-window and window-row tags.

**Fifth revision (owner, 2026-09-30):** Raycast draws a tag's pill even for a transparent colour, so the badge placeholder is now plain text of nine non-breaking spaces (a one-digit pill's width; measured right edges of `Minimized`, `No windows`, and `N windows` agree within 3 px on every row kind). Window facts (`N windows`, `No windows`) are grey tags so the column is one shape. A multi-window app row no longer carries `N Minimized` or `N on other Desktop`; its window rows carry their own tags. The helper's `ax-budget` warning is reworded in plain words in the tooltip ("N windows of this app could not be identified within the helper's 250 ms budget and may be missing from the list").

**Sixth revision (owner, 2026-09-30):** two residual offsets were measured and removed: `Icon.Pin` reports a 12 px frame while the blank and warning icons report 16 px, so the pin is now a square `assets/pin.png` (SF Symbol `pin.fill` rendered on a 32×32 transparent canvas, tinted `Color.SecondaryText`); and the en dash pill was 2 px narrower than a digit pill, so the no-badge pill uses the minus sign `−`. Measured after the change: every window-fact text ends at 2347–2349 px and every badge pill's text at 2392 px, across pinned, unpinned, tracked, untracked, and window rows.

Actions, in panel order. The primary (Return) action depends on the row's state and its label always says what it will do:

| Row state | Primary action | Label |
| --- | --- | --- |
| Exactly one window | Switch to Window | `Switch to Window` |
| Several windows, group expanded | Switch to the first window row listed directly beneath this row | `Switch to “<that title>”` |
| Several windows, group collapsed | Expand the group | `Show Windows` |
| No windows (pinned or badged app, read ok) | Open the app (`open(path)` then close Raycast, badge project's `activate`, `[CODE]`) | `Open App` |
| Windows unknown (read failed) | Open the app | `Open App` (the window actions are absent, not disabled) |

Secondary actions on every app row: `Refresh` (⌘R, both helpers), `Refresh Windows`, `Refresh Badges`, `Pin App` / `Unpin App` (⌘., section 5.4 defines pinning an untracked app), `Collapse Windows` / `Expand Windows` (multi-window apps), `Show <other filter>` (cycles All Apps → Pinned + Badged → Badged Only, ⌘⇧V), `Sort: Alphabetical` / `Sort: Recent in This Command` (section 8), `Configure Apps` (⌘⇧C, `Action.Push`), `Quit <App>` (⌃Q, `Action.Style.Destructive`, `[CODE]` window `QuitAction`), `Copy Diagnostic Info` (⌘⇧C is taken by Configure in the badge project, so Diagnostic Info moves to ⌘⇧D; confirmed against the ⌘K panel in M3), and the badge project's `Open Accessibility Settings` when a permission failure is present.

`Quit <App>` is offered only when the app is running (the window helper listed at least one pid for it). For an app key with several pids (section 5.1), Quit asks each pid in turn with its bundle guard and reports per instance.

### 4.3 Window row

`List.Item` with `id = "win:" + pid + ":" + wid`, rendered directly beneath its app row, only for apps with two or more windows (a one-window app stays one direct-action row, per §4.2).

| Slot | Content |
| --- | --- |
| `icon` | `{ source: Icon.Window, tintColor: Color.SecondaryText }` so the row reads as a child of the app row above it. |
| `title` | `"↳ " + displayTitle` (the window switcher's display title rules, `[CODE]` `model.ts displayTitle`). Whether Raycast preserves the leading glyph and spacing is `[UNPROVEN]` (M2 spike). |
| `subtitle` | `"2 of 3 with this title"` for duplicate titles (`[CODE]` `numberDuplicates`). |
| `accessories` | The window switcher's tags: `Minimized`, `Hidden`, `Full Screen`, `Other Desktop` (gated), unresolved marker (`[CODE]` `windowAccessories`). |

Actions: `Switch to Window` (Return, `[CODE]` `switchToWindow`), `Copy Window Title` (⌘⌥C), `Quit <App>` (⌃Q), `Collapse Windows`, and the common actions above.

### 4.4 Filters

A `List.Dropdown` in the search bar accessory (⌘P opens it, `[DOC]`, `[OBSERVED]` badge A2) with three items, plus the ⌘⇧V cycle action. The chosen filter is saved (section 5.3) and used by the next hotkey press. Switching filters never runs a helper.

| Filter | A row is visible when | Order |
| --- | --- | --- |
| **All Apps** (default) | the window read is ok and the app has ≥ 1 window; **or** the app is tracked and pinned; **or** the app is tracked and badged (`isBadged`, `[CODE]`); **or** the app is tracked and its badge is `unavailable`; **or** the window read failed and the app is tracked (unknown never hides a tracked app). Untracked apps with no discovered window are never shown. | Section 8 (alphabetical or recent). |
| **Pinned + Badged** | the badge project's rule over tracked apps only: `pinned || isBadged(state) || state.kind === "unavailable"` (`[CODE]` `rowVisible`). Window data is shown on the rows but never decides visibility. | Tracked order, pinned and unpinned interleaved (`[CODE]`, `[OBSERVED]` V8). |
| **Badged Only** | `isBadged(state)` over tracked apps only. When the badge read failed the list shows the badge project's failure empty view, not rows (`[CODE]` `emptyState`). | Tracked order. |

Exact visibility of the cases the review asked for:

| Case | All Apps | Pinned + Badged | Badged Only |
| --- | --- | --- | --- |
| App with windows, untracked (no pin, no badge fact) | shown, windows only, no badge accessory | hidden | hidden |
| Pinned tracked app, quit, no window, badge read ok | shown: `Not in Dock` or retained badge, `No windows`; Return = Open App | shown | hidden unless badged |
| Badge on a quit app that keeps a Dock icon (badge retained, `[OBSERVED]` badge S5) | shown: badge tag, `No windows` | shown | shown |
| Tracked app with no Dock item (`notInDock`) and no window, unpinned | hidden | hidden | hidden |
| Tracked app whose saved path is gone (`notInstalled`) | shown only if pinned | shown only if pinned | hidden |
| Window read failed, badge read ok | every tracked app row shown (pinned, badged, or not) with the windows-unknown marker; untracked apps cannot be listed, and a status row says so (section 6.3) | as its rule | as its rule |
| Badge read failed, window read ok | every app with windows shown; tracked apps show the yellow `Unavailable` badge accessory; pinned apps with no windows still shown | all tracked rows `Unavailable` (`[CODE]`) | failure empty view (`[CODE]`) |
| Both reads failed | tracked apps shown with both unknown markers; status rows for both failures; never an empty "No windows found" | all tracked rows `Unavailable` | failure empty view |
| Both reads ok, nothing to show | `List.EmptyView` "No windows and nothing pinned or badged" with Refresh and Configure actions | badge project's empty views (`[CODE]`) | badge project's empty view (`[CODE]`) |

### 4.5 Search

Own filtering (`filtering={false}` with `onSearchTextChange`, `[DOC]`), because the list must know which windows matched. Tokens split on whitespace, case-insensitive substring, every token must match (`[CODE]` window `tokens`, `includesAll`).

| Query matches | App row | Window rows beneath | Return on the app row |
| --- | --- | --- | --- |
| nothing for this app | hidden | none | — |
| the app name (and possibly titles too) | shown, `N windows` | **none** unless a token also matches a window title; then only the matching windows | 1 window: switch to it. Several: `Show Windows` expands all of them beneath the row even during the search (an explicit choice, not a flood). No window: Open App. |
| exactly one window title, not the app name | shown, subtitle = that title, `1 of N matches` | that one window | switch to exactly that `(pid, wid)` |
| several window titles, not the app name | shown, `k of N match` | the k matching windows, in group order | switch to the first listed match; label names it |

Duplicate titles: both windows are listed and numbered; the direct-result rule counts matches by window, so two identical titles give `2 of N match`, never a silent pick (`[CODE]` `numberDuplicates`, `[OBSERVED]` window R-6). A title fragment that matches windows in several apps yields one app row per app, each with its matches beneath. Tracked apps with no window match by name only.

### 4.6 Empty views

Rendered only when no row is visible for the current filter and query (`[DOC]` custom empty view rules). Loading states show `Reading…` with the spinner, never Raycast's default no-results view during the first reads (`[OBSERVED]` badge A6 reasoning). A failure of either helper with no rows to show uses the failure views from the sources, with `Refresh`, `Copy Diagnostic Info`, `Open Accessibility Settings` (permission failures), and the filter switch actions.

---

## 5. Data join, storage, and migration

### 5.1 Join model (TypeScript only)

```
windowsRead: loading | ok(WindowList)   from window helper   (protocol.ts, unchanged)
badgeRead:   loading | ok(DockApp[])    from badge helper    (helper-output.ts, unchanged)
config:      { apps: TrackedApp[]; pins: string[]; filter; sort; recent }

appKey = bundleId ?? ("pid:" + pid)
```

Rows are keyed by `appKey`:

- **From windows:** group `WindowList.apps` by `bundleId`. All pids that share a bundle ID form one app row; each window keeps its own `(pid, wid)` and every window action targets that exact pair. An app with no bundle ID is keyed by pid. Rationale: badges and Dock items are per bundle, and the Dock shows one icon per bundle. Multiple pids per bundle ID are acceptance row J-2.
- **From configuration:** every tracked app contributes a row keyed by its `bundleId`, merged with the window row when one exists.
- **Badge lookup:** `findDockItem` by bundle ID then path (`[CODE]` badge `badge.ts`), evaluated only for tracked apps. `rowState` is reused unchanged.
- **Path for icons and Open App:** window helper `bundlePath` when running, else the tracked app's saved `path`; `Not installed` when neither exists (`existsSync`, `[CODE]`).

Pure functions, unit-tested with fixtures from both projects: `buildRows(windowsRead, badgeRead, config)`, `visibleRows(rows, filter, query, sort, expanded)`, `primaryAction(row, query)`.

### 5.2 What is copied and what is new

| Piece | From | Change |
| --- | --- | --- |
| `protocol.ts`, `run-helper.ts` (window), `helper.ts`, `switch-action.ts`, `model.ts` grouping/ordering/duplicates/labels | window switcher | Copied. `rootRows`/`rootPrimary` are replaced by section 4.5's rules; the old functions stay for their tests until M3 removes them. |
| `badge.ts`, `helper-output.ts`, `run-helper.ts` (badge), `dock.ts`, `views.ts` (`isBadged`, `rowVisible`, `emptyState`, `parsePins`, `parseView`, `loadPinsFrom`, `loadViewFrom`), `config.ts` (`parseSelection`, `seedSelection`, `addApp`, `removeApp`, `moveApp`, `availableApps`) | badge project | Copied. Storage key constants are renamed (section 5.3); `parseView` gains the third filter value. The two `run-helper.ts` files keep their different shapes and live in `src/lib/window/` and `src/lib/badge/`. |
| Join, visibility, search, inline grouping, sort, recency, first-run rules, status rows | new | Section 4, 7, 8, and 5.5. |

### 5.3 Storage keys (new identity, new keys)

All in the new extension's LocalStorage (`string | number | boolean` values, `[DOC]`). Badge values and window titles are never stored.

| Key | Content | Maps to the old key | Meaning change |
| --- | --- | --- | --- |
| `apps.v1` | JSON array of `{ bundleId, path, name }`, ordered. `[]` is a valid, deliberate empty list and is never reseeded. | `selectedApps.v1` | Same shape and parser. Meaning: "tracked apps": the apps whose badge is read and shown, in the order used by Pinned + Badged and Badged Only. |
| `pins.v1` | JSON array of bundle IDs, subset of tracked apps. `[]` is deliberate. | `pinnedApps.v1` | Same shape and parser. Meaning widened by exactly one clause: a pinned app is always shown in **All Apps** as well as in Pinned + Badged. Pins remain a subset of tracked apps (section 5.4). |
| `filter.v1` | `allApps` \| `pinnedAndBadged` \| `badgedOnly` | `listView.v1` (`pinnedAndBadged` \| `badgedOnly`) | One new value. The two old values keep their old meaning. Unknown → `allApps`, never written. |
| `sort.v1` | `alphabetical` \| `recent` | none | New. Applies to All Apps only (section 8). |
| `recent.v1` | JSON object `{ [bundleId]: { switchedAt?: number; frontAt?: number } }`, at most 50 entries, oldest pruned | none | New. Section 8. Bundle IDs and timestamps only. |
| `setup.v1` | JSON `{ seededAt?: string }` | none | New. Records that the one-time first-run seed happened, so "never configured" and "deliberately configured to nothing" are never confused (below). |

First-run and recovery rules for `apps.v1`, in order:

| Stored `apps.v1` | `setup.v1` | Result | Why |
| --- | --- | --- | --- |
| missing | missing | **Never configured.** Seed the badge project's defaults (`[CODE]` `seedSelection`: Reminders, Mail, WhatsApp, Calendar, Slack, Discord, Microsoft Teams, those installed), write `apps.v1`, write `seededAt`, show the seed toast. | Same first-run behaviour the badge project verified (`[OBSERVED]` V11). |
| `[]` | any | **Deliberately empty.** Tracked list is empty; no seed, no write, no toast. The empty view offers Configure. | An explicit `[]` is a valid selection (`[CODE]` `parseSelection`). |
| missing | present | **Configured before, key gone.** Treat as `[]`, write nothing, show a Failure toast `Tracked app list was missing; nothing is tracked`. | The seed ran once already, so reseeding would silently undo whatever the owner chose afterwards. |
| corrupt | any | Reseed the defaults and show the badge project's "unreadable, defaults restored" toast (`[CODE]` `RESEEDED_TOAST_TITLE`). | Corruption is not a choice; the badge project's verified recovery applies. |

Pins: missing or corrupt `pins.v1` → all tracked apps pinned, with the badge project's toasts; an explicit `[]` stays empty (`[CODE]` `loadPinsFrom`, `[OBSERVED]` V11 automated rows). Removing the last tracked app in Configure writes `[]`, which is then respected on every later launch.

### 5.4 Pins and tracking in the combined command

Invariant: `pins ⊆ apps`. It is kept by `prunePins` on every save (`[CODE]`).

- Pin on a **tracked** app: toggles the pin, as before.
- Pin on an **untracked** app row (an app that is only in the list because it has windows): one action, `Pin App`, adds the app to the tracked list (at the end, `addApp`) **and** pins it, then shows a toast `Added <App> to tracked apps and pinned it`. Its badge becomes visible from the next badge refresh. This is stated in the action's title tooltip, so the old `pinnedApps.v1` meaning is not silently widened: pinning still means "tracked and always shown".
- Unpin never untracks. Removing from tracking (Configure → Remove) drops the pin in the same save (`[CODE]`).

### 5.5 Migration of the owner's settings

Raycast documents that extensions cannot read each other's storage `[DOC]`, and the old identities' data is not inherited. Three explicit paths; the owner picks one in M4:

| Path | How | Preserves a deliberately empty selection? |
| --- | --- | --- |
| **M-A. Seed and adjust** (chosen, Q2 resolved) | The new extension seeds the badge project's defaults on its first run, exactly as the badge project did on this Mac. The owner's actual state on 2026-09-30 is the seven defaults in default order, all pinned except Slack, filter Badged Only (`[OBSERVED]` badge VERIFICATION, "The user's setup before the author change" and change 5 "Restore in the new copy"). The adjustment is two actions: unpin Slack, choose the filter. The old extensions keep their settings untouched. | Yes, by the section 5.3 rules: the seed runs only when the extension has never been configured (`setup.v1` absent). Once configured, an explicit `[]` is respected forever, and a missing key after setup is treated as empty with a warning, never reseeded. |
| **M-B. Import from clipboard** | Not in the first version. It would need an `Export Settings` command in the working badge extension, which the owner declined to add. Recorded here only so a later version does not reinvent the format: a JSON document `{ format, version, apps, pins, filter, sort }` parsed with the same parsers as the stored keys. | Deferred. |
| **M-C. Manual re-entry** | Configure view. Always available. | Yes. |

Nothing is inherited, scraped, or guessed from the old identities.

---

## 6. Independent loading, refresh, and error states

### 6.1 State per source

```
config:  loading → ready(config)                       (LocalStorage, ~ms; failure = seed + toast, as today)
windows: idle → running(seq) → ok(list, at) | failed(failure, at)
badges:  idle → running(seq) → ok(items, at) | failed(failure, at)
```

Each helper has its own sequence counter; a result from a superseded run is dropped (`[CODE]` badge `readSeq`). Each has its own timeout: window list 5 s, focus/quit 3 s (`[CODE]`), badge 3 s (`[CODE]`). The two helpers are started with two independent `execFile` calls and never awaited together.

### 6.2 Truth table: what the list shows

| config | windows | badges | Rows | Spinner | Status rows (section 6.3) |
| --- | --- | --- | --- | --- | --- |
| loading | any | any | none | on | none |
| ready | running | running | pinned tracked apps in loading state (no badge, no window facts) | on | none |
| ready | ok | running | apps with windows (window facts) + pinned apps; tracked rows show no badge accessory yet | on | none |
| ready | running | ok | pinned + badged tracked apps with badge accessories, no window facts | on | none |
| ready | ok | ok | full rows | off | none |
| ready | failed | ok | tracked rows with windows-unknown marker; no untracked rows | off | `Windows unavailable` |
| ready | ok | failed | apps with windows; tracked rows `Unavailable` badge accessory; pinned no-window apps | off | `Badges unavailable` |
| ready | failed | failed | tracked rows with both markers | off | both |

"No windows" is shown on a row only in the `windows = ok` column. "No badge" is shown only in the `badges = ok` column. The spinner is on while any read is running, so `List.EmptyView` never shows during a first read (`[DOC]`: the empty view is not displayed while `isLoading` is true and the search bar is empty).

### 6.3 Status rows

When a helper failed **and** rows are still visible, the failure is shown as the first `List.Item` of the list (there is no non-selectable row type in Raycast's List `[DOC]`):

| Row | Title | Subtitle | Actions |
| --- | --- | --- | --- |
| `status:windows` | the window switcher's failure title (`[CODE]` `failureText`), e.g. `Raycast needs Accessibility`, `Window scan timed out`, `Helper not installed` | its description | `Refresh Windows`, `Copy Diagnostic Info`, `Open Accessibility Settings` when `not-trusted` |
| `status:badges` | the badge project's reason (`[CODE]` `REASONS`), e.g. `Accessibility access is off for Raycast`, `Dock read timed out` | badge project's description | `Refresh Badges`, `Copy Diagnostic Info`, `Open Accessibility Settings` when `permission` |

A status row is never shown for a read that succeeded or is running. When no rows are visible the failure uses the empty views instead (section 4.6).

### 6.4 Refresh rules

| Trigger | Windows | Badges | Selection | Order |
| --- | --- | --- | --- | --- |
| Hotkey (fresh mount) | run | run | first row | as saved sort |
| ⌘R `Refresh` | run | run | kept by id when it still exists | recomputed; ids stable |
| `Refresh Windows` | run | — | kept | recomputed |
| `Refresh Badges` | — | run | **kept: badge data never affects order** in any filter (alphabetical/recent use window and recency facts only; tracked order is configuration) | unchanged |
| After `Quit <App>` | run (automatic) | — | selection moves to the nearest surviving row (section 7.4) | recomputed |
| After a focus that returned `window-gone` or `pid-mismatch` | run (automatic) | — | nearest surviving row | recomputed |
| Filter switch, sort switch, pin, collapse | — | — | kept by id | recomputed |
| Return from Configure (`onPop`) | — | — | kept | config reloaded, no helper run (`[OBSERVED]` badge C3) |

A badge refresh can change **visibility** in Badged Only (a row leaves when its badge clears); that is the badge project's accepted behaviour (`[CODE]`, A4 still unverified). It never refocuses a window and never reorders.

### 6.5 Latency and failure measurements (defined before targets)

| Measure | Definition | Existing baseline |
| --- | --- | --- |
| `t_rows` | hotkey press to first rows painted (config loaded) | badge project: rows and badge visible in 0.34–0.40 s from the hotkey, including Accessibility polling delay (`[OBSERVED]` U1) |
| `t_win` | window helper `elapsedMs` + parse | 172–194 ms helper, 182–206 ms end to end, 11 apps / 12 windows (`[OBSERVED]` window F-14) |
| `t_badge` | badge helper `elapsedMs` | 97–206 ms (`[OBSERVED]` badge M1) |
| `t_both` | max of the two when started together, minus their solo values | `[UNPROVEN]`: both helpers talk to Accessibility; whether they slow each other is measured in M1 |
| `t_focus` | Return to `focused: true` | 120–600 ms first tier, ~1.8 s when both tiers run (`[OBSERVED]`) |
| `f_win`, `f_badge` | failures per 20 hotkey presses at the owner's normal load, by failure class | 0 recorded in both projects' runs; re-measured in M1 |

Targets are set only after M1 records these on Raycast 2.6.0 with both helpers running concurrently. Proposal to confirm then: `t_rows` p95 ≤ 400 ms; `t_win` and `t_badge` p95 ≤ 2× their solo baselines when run together; any concurrent slowdown above 100 ms is a design question (serialize the second helper) rather than a target miss.

---

## 7. Inline windows: feasibility and fallback

### 7.1 What Raycast offers

`List` renders flat `List.Item`s, optionally grouped in `List.Section`s with a title; there is no tree, indentation, or disclosure control in the documented API `[DOC]`. Selection is by item `id`; `selectedItemId` "selects the item with the specified id" and `onSelectionChange` reports the current id, with `null` when everything is filtered out `[DOC]`. Whether Raycast keeps the selected id across a re-render that changes the item set is not documented and is `[UNPROVEN]`.

### 7.2 Proposed hierarchy

- App row, then its window rows, in one flat list, in that order, no `List.Section`. (Sections would make the app header non-selectable and lose Quit/Pin on it.)
- Visual hierarchy: window rows have a secondary-tinted window icon and a `↳ ` title prefix (section 4.3). The app row carries the app icon and the counts.
- Expanded by default for every multi-window app. A per-app `Collapse Windows` / `Expand Windows` action, plus `Collapse All` / `Expand All`. Collapse state lives in component state only (fresh mount = expanded), with one preference `Expand windows by default` (checkbox, default on) for owners who prefer a compact list.
- One-window app: one direct-action row, no child (`[CODE]` behaviour kept).
- While searching: window rows appear only under the section 4.5 rules.

### 7.3 Keyboard cost, stated honestly

Today (pushed child list), reaching window `k` of the app in position `p`: `(p−1)` ↓, Return, `(k−1)` ↓, Return, plus the child list's mount. Inline: `(p−1 + E)` ↓, then `k` ↓, Return, where `E` is the number of expanded window rows belonging to apps above `p`. Inline removes one Return and one view push but can add `E` arrow presses. It is only a win when `E` is small or when search is used (search collapses name-only matches, so `E` is 0 for J2 and J3). Therefore inline rows must be **measured, not assumed**: acceptance rows K-1 to K-4 record real keystroke counts for three scenarios on this Mac's normal window load, for both the inline layout and the pushed fallback.

### 7.4 Selection stability

- Ids are stable: `app:<appKey>`, `win:<pid>:<wid>`, `status:<source>`.
- After a rescan the list is rebuilt; if the previously selected id still exists, the list is rendered without `selectedItemId` and Raycast is expected to keep the selection (`[UNPROVEN]`, K-5). If it no longer exists (window closed, app quit), `selectedItemId` is set once to the nearest surviving row: the app row of the same app, else the row that preceded it.
- `onSelectionChange` tracks the current id for that fallback only; it triggers no helper.

### 7.5 Tested fallback

If the M2 spike shows any of: the `↳` prefix is stripped or misaligned, selection jumps to the top on re-render with stable ids, or the K-rows show inline costs more keystrokes in the arrow-only scenario without a search win, then M3 ships the **pushed child list** from the window switcher (`[CODE]` `AppWindows`, `[OBSERVED]` M3 flow) with these merged-list adjustments: the app row's Return pushes the child, the child's `Refresh` rescans windows only, and search from the root behaves per section 4.5 with the child pre-filtered (the window switcher's rules, `[CODE]` `rootPrimary`). Both layouts share every pure function; only `app-list.tsx` differs, so the fallback is a rendering change, not a design change.

**Decision written back from M2 (2026-09-30): inline rows are adopted.** The spike (the real `app-list` command opened by deeplink, Raycast 2.6.0, read back through a read-only Accessibility dump; `VERIFICATION.md` §M2) showed the `↳ ` prefix preserved verbatim on window rows, and the K-3 table derived from the rendered row order gives inline the same count as the pushed layout in the arrow-only scenario (`E = 0` on this Mac's load: every app above Edge has one window) and the same count in the search and one-window scenarios, while removing the view push. Selection stability (K-5) and the one-↓ step (K-1) are keyboard rows still owed by the owner; if K-5 fails on the real keyboard, the `selectedItemId` fallback in `app-list.tsx` (§7.4) is the first thing to revisit before the pushed layout.

---

## 8. Sorting semantics

Sort applies to **All Apps** only. Pinned + Badged and Badged Only keep the tracked order (`[CODE]`, `[OBSERVED]` V8). Windows within an app keep the window switcher's order: on-screen front to back by `zIndex`, then off-screen, then minimized, then unresolved, ties by title then wid (`[CODE]` `compareWindows`). Refresh never reorders while data is unchanged.

### 8.1 Alphabetical (default)

App name, locale-aware, case-insensitive, numeric-aware (`[CODE]` `compareApps`); ties by `appKey`.

### 8.2 "Recent in This Command"

Named for what it measures. It is not system-wide last-used history and the action label, dropdown label, and README say so.

Signals, all available without a resident process or a helper change:

| Signal | Source | Coverage |
| --- | --- | --- |
| `switchedAt` | written when a switch or Open App from this command succeeds (`focused: true`, or `open()` resolved) | only switches made here |
| `frontAt` | at each invocation, the app that owns the on-screen window with `zIndex === 0` (the frontmost layer-0 window; Raycast's own windows are excluded by the helper, `[CODE]` `raycastBundlePrefix`) is stamped with the scan time | one sample per hotkey press: "what was in front when I opened this" |
| `zIndex` | window helper, on-screen windows only (`[CODE]` `zIndex?: number`) | current stacking order of visible windows; `undefined` for hidden, minimized, other-Desktop windows |

Order:

1. Apps with a `switchedAt` or `frontAt` stamp, newest first (max of the two).
2. Apps with no stamp but at least one window with a `zIndex`, by their smallest `zIndex` (front to back, "what is visible now"), ties alphabetical.
3. Everything else (only hidden/minimized/other-Desktop windows, or no windows): alphabetical.

Deterministic: identical inputs give identical order; a null `zIndex` never sorts above a known one; two stamps at the same millisecond fall back to alphabetical. Persistence: `recent.v1`, capped at 50 bundle IDs, pruned on write, and only bundle IDs (apps keyed by pid are not recorded). Window-level recency (which window of an app was last switched) is a non-goal for now; windows keep their group order.

---

## 9. Trash row (added after M5, 2026-09-30)

Raycast ships `Open Trash` and `Empty Trash` as built-in System Actions; Empty Trash has the setting `Show Warning Before Emptying Trash` `[DOC]` manual.raycast.com/system-commands. The owner wanted both reachable from the list without another hotkey. Built as follows (revises the earlier deferred sketch: a pin replaces the `Show Trash row` preference, and Open Trash no longer goes through Raycast).

**Placement.** One row, id `util:trash`, in a `List.Section` titled **Utilities** rendered after every item `visibleItems` returns, so app rows, their order, and the pure rules in `src/lib/rows.ts` are unchanged (the extension defines no ⌘-number shortcuts; nothing above the row can shift). It renders only once both reads have landed, so it never shows alone first and takes the initial selection from the top app row. Its trailing accessories are the blank badge slot and the pin/blank icon slot, so its pin lines up with app pins.

**Visibility** (`trashVisible`, `src/lib/utilities.ts`, T-1):

| Filter | Pinned, no search | Not pinned, no search | Searching (`trash`, `empty`, `bin`, `recycle`, `open` …) |
| --- | --- | --- | --- |
| All Apps | shown | hidden | shown when every token matches, pinned or not |
| Pinned + Badged | shown | hidden | same |
| Badged Only | hidden | hidden | hidden (the Trash has no Dock badge) |

**Pin.** Its own key `utilityPins.v1` (a JSON array of utility ids; missing or unreadable means nothing pinned, and reading never writes). It cannot live in `pins.v1`, which holds tracked bundle IDs and is pruned to them on every load. Pin/Unpin (⌘.) on the row and in a **Utilities** section of Configure, outside the tracked order.

**Selection.** Selecting the Trash row (or a status row) removes `selection.v1`, so Quit Selected App refuses ("Select an app first") instead of quitting the app selected before it. The §7.4 survivor fallback leaves a selected Trash row alone while it is shown.

**Open Trash** (primary action): `open("~/.Trash", "com.apple.finder")`, then close Raycast. Finder shows its Trash window; no Raycast command is launched, so no external-launch prompt.

**Empty Trash…** (destructive, no shortcut): always this extension's own `confirmAlert` first ("Empty Trash?", destructive primary, no remember-choice), because Raycast's warning can be switched off, including from its own "remember my choice" checkbox. On confirm: `launchCommand({ ownerOrAuthorName: "raycast", extensionName: "system-actions", name: "empty-trash" })`. Read from Raycast 2.6.0's bundle `[CODE-RAYCAST]`: built-ins resolve by the slug of the extension title (System Actions) and the command title; a launch from an extension is never marked internal, so Raycast shows "Run Command / Always Run Command" until the owner picks Always; then Raycast's own warning if enabled; then Raycast's host empties the Trash (no Finder AppleScript, no new permission). A disabled or missing command throws and gives a failure toast saying nothing was erased. This extension never deletes a file.

**Not done:** an item count (reading `~/.Trash` would need Full Disk Access for Raycast); Finder AppleScript (would add an Automation permission).

---

## 10. Permissions, privacy, packaging, and provenance

### 10.1 Permissions

- Exactly one permission, already granted: Accessibility for Raycast.app on the Device Control and Data Access page. Both helpers run as children of Raycast and use Raycast's grant; neither has or needs its own entry (`[OBSERVED]` badge M1 permission boundary; window VERIFICATION setup facts). The merged extension adds nothing.
- Safety rule carried forward from both projects: **Raycast's Device Control and Data Access is never switched off while Raycast is running.** It froze keyboard and click input twice on this Mac (`[OBSERVED]` badge Incident). Any permission-denied test follows the badge project's change 2 procedure: quit Raycast, switch, relaunch. Rows that cannot be exercised safely are recorded as "not testable safely", never faked.
- No Screen Recording, no Automation, no network entitlement, no LaunchAgent (`~/Library/LaunchAgents` holds only Microsoft Edge's updater today, `[OBSERVED]`).

### 10.2 Privacy

Data read at runtime: window titles and states through Accessibility while the list is open (window helper), Dock item title, path, running flag, and badge text (badge helper). Data stored: the six configuration keys in section 5.3, which hold bundle IDs, paths, names, filter, sort, and timestamps. Never stored: badge values, window titles, window IDs. Diagnostic info leaves memory only by the explicit copy action and says it includes window titles (`[CODE]`).

### 10.3 Packaging and lifecycle

- Two helper binaries under `assets/`, each run on demand with `execFile`, no shell, hard timeouts, executable-bit repair before each run (`[CODE]` both `run-helper.ts`). No helper process exists while the command is closed (`[OBSERVED]` window R-13: 1,094 samples over 10 minutes, 0 seen; badge P3/P4/V14). Idle behaviour is re-verified for the merged extension in row I-2. At this moment neither helper is running (`[OBSERVED]` `pgrep` during this phase).
- `npm run dev` imports the extension; the imported copy at `~/.config/raycast/extensions/app-management/` keeps working after the dev server stops, after Raycast restarts, and after reboot (`[OBSERVED]` in both projects for their own copies; re-verified for the new copy in I-3).
- The build scripts do not compile Swift (badge project's D1, `[CODE]` `helper-binary.test.ts`). A separate manual `build:helper:*` script per helper exists for rebuilds and writes a source hash.

### 10.4 Code and binary provenance (to be executed in M2, not now)

Nothing is copied in this phase. When M2 starts, the following are brought in verbatim, with these notices:

| Item | From | Hash observed today | License notice to carry |
| --- | --- | --- | --- |
| `assets/window-helper` (Mach-O arm64, ad-hoc linker-signed, 430,672 bytes) | window switcher `assets/window-helper` | SHA-256 `a5c4be9e97c54e9b0c9e832f15865531487a29debd717be2c09b87df85b866c2` (same bytes installed at `~/.config/raycast/extensions/app-window-switcher/assets/window-helper`) | The window switcher is `UNLICENSED` and owned by the same author, who relicenses the copied code under this project's MIT license at the M2 commit that brings it in; no third-party code was copied into it (its `PrivateAPI.swift` header states that AltTab and devadathanmb's Window Switcher, both GPL, were read as documentation only). That header is carried verbatim, and the README repeats that no GPL code is included. |
| `helper/window/*.swift` (`main.swift`, `Inventory.swift`, `Focus.swift`, `Quit.swift`, `PrivateAPI.swift`) | window switcher `helper/` | recorded in M2 as `helper/window/SOURCES.sha256`; build command `swiftc -O -swift-version 5 -target arm64-apple-macos14 helper/window/*.swift -o assets/window-helper` (from its `package.json`), Swift 6.4 | as above |
| Reproducibility of `window-helper` | M2 rebuilds from the copied sources on this Mac and compares with `a5c4be…`. The window project recorded its installed copy as byte-identical to its build (`[OBSERVED]` M5) but never recorded a second build; if the rebuild differs, both hashes are recorded and the copied binary is kept as the one that was verified. | | |
| `assets/dock-badges` (Mach-O arm64, ad-hoc, 90,872 bytes) | badge project `assets/dock-badges` | SHA-256 `9d16509531b9bc9ebd6dea6b07f805da34f81799aebd5274c6b0b4efafcd9af5` (same bytes installed) | MIT, copyright the same author (`bretbuilds`). This project's own `LICENSE` is MIT with the same copyright holder, so one license file covers both; the README's provenance section names the source project and its repository. |
| `helper/badge/dock-badges.swift` + `dock-badges.swift.sha256` | badge project `helper/` | source SHA-256 `95c261debb5a012a9ead7e6217f611e3d21287ef9b37824bcfe528cf2500b0ce`; rebuild was byte-identical on this Mac (`[OBSERVED]` change 5 step 1) | MIT |
| TypeScript and tests listed in section 5.2 | both projects | tracked by git in the new repo; each copied file keeps a one-line header `// Copied from <project> <file> on <date>, unchanged except <list>` | as above |
| Test fixtures | `test/fixtures.ts` (window), `test/fixtures/helper-output-sample.json` (badge; a sample in the helper's exact format, badges Mail `2`, System Settings `1`, and one `•`) | | as above |

Automated tests carried over and expected to stay green after the key renames: window project 27 (`[OBSERVED]` M2 + Quit), badge project 158 (`[OBSERVED]` change 5). New tests are listed in section 11.

---

## 11. Acceptance matrix

Type **A** = automated (`node --test`, pure functions, fake helpers, static checks). Type **M** = manual on this Mac inside Raycast, recorded in this repository's `VERIFICATION.md` with date, Raycast version, and the evidence channel. Automated results are never reported as proof of on-Mac behaviour.

### 11.1 Window-switcher gaps to close before adopting its behaviour (M1, on the existing installed extension)

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| W-1 | Regular Desktop → regular Desktop on the built-in display (window F-1) | M | The display slides to the other Desktop and the exact window is frontmost; ⌘L selects its address bar. |
| W-2 | Real ⌘M on an Edge window, then hotkey → Return | M | Row shows `1 minimized`; the window un-minimizes and is in front. |
| W-3 | Real ⌘H on Edge, then choose one Edge window | M | `Hidden` tag; Edge reappears with that window in front; other Edge windows keep their state. |
| W-4 | Keyboard-only row use (window R-2, R-3, R-5, R-7, R-8) | M | Return on a one-window app switches; multi-window pushes; Esc returns with selection intact; order stable over three refreshes; new windows appear on ⌘R and on reopen. |
| W-5 | Search rules (window R-4) | M | One-title match → subtitle and direct switch; two-title match → `2 of N match` and pre-filtered child; app name → app row. |
| W-6 | Duplicate titles (window R-6 through the UI) | M | `1 of 2 with this title` / `2 of 2`, each Return reaches its own window. |
| W-7 | ⌃Q in both lists, and an app that asks to save | M | List stays open and rescans; "still running" toast when the app asks to save. |
| W-8 | Compare with built-in Switch Windows (window F-11) | M | Helper count ≥ Switch Windows count per app; differences listed. |
| W-9 | Raycast restart and reboot (window R-12) on Raycast 2.6.0 | M | Hotkey works both times with no terminal open. |
| W-10 | Timing baseline on 2.6.0: 10 runs of `list`, 10 badge reads, then 10 concurrent pairs | M | `t_win`, `t_badge`, `t_both`, `f_win`, `f_badge` recorded (section 6.5). |
| W-11 | Badge project's pending rows: V10 after reboot, A4 (a badge clearing while Badged Only is open) | M | As specified in the badge project. Recorded here because the merged command inherits both behaviours. |

### 11.2 Joins and visibility

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| J-1 | App/window/badge join on fixtures from both projects | A | Rows keyed by bundle ID carry the right windows and badge; a tracked app with no windows gets a row; an untracked app with windows gets a row with `notTracked` badge. |
| J-2 | Two pids, one bundle ID | A, M | A: one row, both pids' windows beneath, each window keeps its pid. M: launch a second instance of an app (`open -n`) with a window; one row, both windows switchable, Quit reports per instance. |
| J-3 | Tracked app with no Dock item | A, M | `Not in Dock` only when tracked and badge read ok; hidden in All Apps unless pinned or it has windows. |
| J-4 | Pinned app, quit | A, M | Shown in All Apps and Pinned + Badged with `No windows` and its badge state; Return launches it. |
| J-5 | Badge on a quit app with a retained Dock badge | M | Shown in all three filters with the badge tag and `No windows`. |
| J-6 | Unpinned, unbadged app with windows | A, M | Shown in All Apps with no badge accessory (never `No badge`); hidden in the other two filters; its window is focusable from All Apps (this is the "one hotkey reaches any window" check). |
| J-7 | Badge-bearing tracked app with no windows remains visible | A, M | All Apps, Pinned + Badged, and Badged Only all show it. |
| J-8 | Untracked app never shows a badge fact | A | No accessory text `No badge` / `Not in Dock` on `notTracked` rows in any state. |

### 11.3 Search, inline rows, keyboard

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| S-1 | Direct title search | A, M | Distinctive fragment → app row with subtitle, `1 of N matches`, one window row beneath, Return on either focuses that exact `(pid, wid)`. |
| S-2 | App-name search does not flood | A, M | `edge` → one Edge row, no window rows, `N windows`; Return expands. |
| S-3 | Duplicate titles and multi-app matches | A, M | `2 of N match` for identical titles; one app row per matching app. |
| K-1 | Down from an expanded app reaches its first window | M | One ↓ from the app row selects `win:<pid>:<wid>` of its first listed window. |
| K-2 | Return on a window row focuses without a pushed view | M | The navigation stack depth is unchanged (no Esc needed afterwards); `focused: true` in the helper response. |
| K-3 | Keystroke counts, three scenarios, inline vs pushed fallback | M | Recorded: (a) arrow-only to window 2 of the third app; (b) search fragment then Return; (c) one-window app. Inline is adopted only if (b) and (c) are not worse and (a) is not worse by more than the number of expanded rows above, as predicted in section 7.3. |
| K-4 | `↳` prefix and icon tint render as intended | M | Screenshot in `VERIFICATION.md`. |
| K-5 | Selection stability across re-render | M | Selection stays on the same id after ⌘R, after a badge-only refresh, after a filter switch, and moves to the nearest survivor after Quit and after `window-gone`. |
| K-6 | Collapse/expand | M | Per-app and all; state resets on the next hotkey press; preference respected. |

### 11.4 Ordering

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| O-1 | Alphabetical, ties by key; refresh with identical data gives identical order | A | |
| O-2 | Recent order with null `zIndex`: stamped apps first by newest stamp, then on-screen by `zIndex`, then alphabetical; no null above a known value | A | |
| O-3 | Recency persistence: `switchedAt` written on success only; `frontAt` from the `zIndex === 0` window; cap 50; bundle IDs only | A, M | M: switch to Mail through the command, reopen: Mail first under Recent. |
| O-4 | Badge refresh never reorders or refocuses | A, M | Order and selection unchanged after `Refresh Badges` in all filters. |
| O-5 | Pinned + Badged and Badged Only ignore the sort setting | A | |

### 11.5 Quit, stale windows, helper independence

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| Q-1 | Quit App from app row and window row | M | App quits, list rescans windows only (exactly one `window-helper` run, zero `dock-badges` runs), selection moves to the nearest row. |
| Q-2 | Stale window | M | Close a window after listing, Return on it: "That window closed", one window rescan, nothing else focused. |
| H-1 | Failure classes for each helper map to the sources' states | A | Fake helpers: missing, no execute bit (repaired), timeout, crash, `not-trusted` / exit 10, `version-mismatch`, malformed JSON. |
| H-2 | Window helper failed, badge ok | A, M | Tracked rows shown with the windows-unknown marker; status row present; badges still shown; Badged Only unaffected. M: rename the installed `window-helper`, ⌘R. |
| H-3 | Badge helper failed, window ok | A, M | Apps with windows shown; tracked rows `Unavailable`; Badged Only failure view; windows still switchable. M: rename the installed `dock-badges`, ⌘R. |
| H-4 | Both failed | A | Tracked rows with both markers; two status rows; no "No windows" or "No badge" text anywhere. |
| H-5 | Independent refresh counts | M | `Refresh Windows` → 1 window run, 0 badge runs; `Refresh Badges` → the reverse; ⌘R → one of each. Counted with `pgrep` polling as before. |
| H-6 | UI usable as window data arrives | M | With a deliberately slow badge read (fake helper `sleep 2` installed temporarily), window rows are switchable before badges land. |

### 11.6 Migration, idle, install

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| M-1 | Never configured vs deliberately empty | A | The four-row table in section 5.3 asserted case by case with an in-memory store: missing+missing seeds once and writes `seededAt`; `[]` never seeds and writes nothing; missing+`seededAt` writes nothing and yields an empty list with the warning; corrupt reseeds with the toast. |
| M-2 | First run seed on this Mac | M | First launch shows the seven defaults, all pinned, All Apps; second launch shows no toast and reseeds nothing; after removing every tracked app, a third launch stays empty. |
| M-3 | Chosen migration path executed | M | The new extension shows the owner's seven apps in order, Slack unpinned, filter as chosen; the old extensions' settings are unchanged (their lists still show the same state). |
| M-4 | Persistence across Raycast restart and reboot | M | Filter, sort, pins, tracked list, recency unchanged. |
| I-1 | No timers, no `interval`, no `menu-bar` | A | Static checks from both projects, over the new `src/`. |
| I-2 | Helper-free idle | M | 10-minute `pgrep` watch at 0.5 s with the list closed: 0 `window-helper`, 0 `dock-badges`; no LaunchAgent or login item added. |
| I-3 | Installed use after the dev server stops, Raycast restart, reboot | M | Hotkey works each time with no terminal open; installed helpers executable. |

### 11.7 Cutover

| ID | Check | Type | Pass when |
| --- | --- | --- | --- |
| C-1 | Both old commands still work while the new one is installed | M | ⌃⌥D opens Dock Badges; the window switcher hotkey opens its list; the new hotkey opens the App Management list. Three extensions, three folders, no shared files. |
| C-2 | Assign the one hotkey, then disable the old two | M | Section 12, M5 procedure. Only after every M row above passes. |
| C-3 | Rollback | M | Re-enabling the old commands and their hotkeys restores the previous setup with their storage intact (they were disabled, not removed). |

### 11.8 Store feasibility (early, informational, never a gate for the merged command)

| ID | Check | Type | Records |
| --- | --- | --- | --- |
| ST-1 | Window helper against the Store binary guideline | M (documentation and repository reading) | Whether "traceable sources and builds" is met by the section 10.4 provenance (source, hash test, build command, reproducible rebuild), the helper's size, and a written disclosure of every undocumented symbol it uses. Outcome is "likely acceptable", "likely questioned", or "unknown", with the guideline sentence quoted. No assumption of acceptance. |
| ST-2 | Badge helper against the same guideline | M | Same record. Its committed-binary pattern is precedent from four published extensions, not an accepted submission. |
| ST-3 | Separate configuration command against the guidelines | M | The exact guideline text about configuration or settings commands is quoted. If it discourages them, the design keeps the configure view reachable from the main list (J5) and the standalone `configure-app-management` entry is marked "remove before submission"; the everyday journey is unchanged either way. |

---

## 12. Milestones (five, each independently verifiable)

### M1 — Close the window-switcher gaps and establish the baseline (no new code)

- **Scope:** W-1 … W-11 on the two installed extensions as they are today, on Raycast 2.6.0, plus the Store feasibility reading ST-1 … ST-3 (documentation only, no code, no submission). A read-only timing script in this repository (`scripts/measure.sh`, runs the installed helpers directly from a terminal that already holds Accessibility, and separately records Raycast-side `elapsedMs` from Copy Diagnostic Info) produces the section 6.5 numbers, solo and concurrent.
- **Touches:** this repository only (`VERIFICATION.md` §M1, `scripts/`). Neither source project changes.
- **Done when:** every W row has a dated PASS / FAIL / not-testable entry and the baseline table is filled. The stop rule from the window switcher applies: if W-1, W-2, or W-3 fails and cannot be fixed within the window helper without changing its schema, stop and present the evidence before M2.

### M2 — Bring-in, pure logic, and the inline-rows spike

- **Scope:** copy the files in section 5.2 and 10.4 with headers and license notices; verify both binary hashes; rebuild `window-helper` from source and record the comparison; rename storage keys; write `buildRows`, `visibleRows`, `primaryAction`, sort, recency, first-run rules; port all 185 existing tests and add A rows J-1, J-3, J-6, J-8, S-1..S-3, O-1..O-5, H-1..H-4, M-1, I-1. A throwaway `Inline Spike` view command (removed at the end of M2) renders real helper data as inline rows to answer K-1, K-4, K-5 and to collect the K-3 keystroke counts for both layouts.
- **Done when:** `npm test`, `npm run typecheck`, `npm run lint` (`ray lint`, which passes with MIT and validates the author) pass; `VERIFICATION.md` §M2 records the hash comparisons and the K-3 table with the inline-or-fallback decision written back into section 7.

### M3 — The App Management command

- **Scope:** `manage-apps`, `app-list` (section 4, 6, 7 as decided, 8), `configure-app-management` (Pinned section, then Tracked, then Available; Move Up/Down within the tracked order; Pin/Unpin; Add; Remove), preferences (`Expand windows by default`, `Show "Other Desktop" tags` carried over, `Activation` carried over). The owner records the new hotkey on `manage-apps`. Both old extensions stay installed with their hotkeys.
- **Done when:** J-2, J-4 … J-7, K-1 … K-6, S-1 … S-3, O-3, O-4, Q-1, Q-2, H-2, H-3, H-5, H-6 pass on this Mac; screenshots of All Apps expanded, a search with a direct match, both status rows, and Configure.

### M4 — Migration, persistence, idle, install

- **Scope:** execute migration path M-A (seed, then unpin Slack and choose the filter), M-2 … M-4, I-2, I-3, W-11's remaining items in the new command, and the README (install, hotkey, filters, sort naming, permissions, uninstall, provenance).
- **Done when:** the new command is the owner's daily driver in parallel with the old ones for at least one working day at home and one at work (multi-display), with no FAIL rows open.

### M5 — Cutover to one hotkey, old commands kept intact

Procedure, in order, each step recorded:

1. C-1: confirm all three extensions work side by side.
2. In Raycast Settings, record the chosen everyday hotkey on **Manage Apps** (if it is ⌃⌥D, first clear ⌃⌥D from Show Dock Badges; Raycast will not let two commands share it).
3. Press it three times from different apps: fresh list each time, one run of each helper per press.
4. Only then: clear the remaining hotkeys from **Show Dock Badges**, **Configure Dock Badge Apps**, and **Switch Windows by App**, and switch those extensions' commands off in Settings. Do **not** remove either extension: removal deletes its folder and storage (`[OBSERVED]`), and the folders are separate from the new one, but keeping them is the rollback (C-3).
5. Both source project directories stay untouched; the badge project continues toward the Store on its own.
6. C-3 rehearsed once: re-enable the old commands, re-record one old hotkey, confirm the old list opens with its settings, then disable again.

- **Done when:** one hotkey opens the App Management list, the old commands are disabled but present, `VERIFICATION.md` §M5 lists the final hotkey table, and I-2 has been re-run after the cutover.

---

## 13. Where each requirement stands today

| Requirement | Supported by existing code or evidence | Needs live proof |
| --- | --- | --- |
| One hotkey reaches an unpinned, unbadged window | All Apps includes every app with a discovered window (section 4.4); exact-window focus is `[CODE]` + `[OBSERVED]` (window F-2, F-3, F-4, F-7) | J-6 through the merged UI; W-1 … W-3 for the still-open window cases |
| Badge-bearing app with no windows stays visible | `rowVisible`/`isBadged` `[CODE]`, retained badge on quit `[OBSERVED]` S5 | J-5, J-7 in the merged UI |
| Helper failure never reads as zero | both projects' failure mapping `[CODE]`, badge `emptyState` ordering `[CODE]`, F5/V13 `[OBSERVED]` | H-2 … H-4 (the merged truth table is new) |
| Inline rows save keyboard steps | nothing; section 7.3 predicts the trade-off | K-3 measurement decides |
| "Recent" label matches what is measured | `zIndex` semantics `[CODE]`; no recency store exists today | O-3 |
| Independent helper refresh | both runners are independent `[CODE]`; nothing composes them yet | H-5, H-6 |
| Selection stability | window project restores by id in the child list `[CODE]`; Raycast's behaviour on re-render | K-5 |
| Migration | parsers and seeds `[CODE]`; storage isolation `[DOC]` | M-3 |
| Idle, restart, reboot | both projects `[OBSERVED]` for their copies | I-2, I-3 for the new copy |
| Concurrent helper timing | solo baselines `[OBSERVED]` | W-10 |

---

## 14. Blocking questions

None remain. Both questions from the first review were answered by the owner on 2026-09-30:

- **Q1 (resolved).** Author is `bretbuilds`; identity `e:n:bretbuilds/app-management`; title `Window Switcher & Badges`.
- **Q2 (resolved).** Migration is seed-and-adjust (path M-A). No `Export Settings` command is added to the badge project. The seed runs only for a never-configured extension; a deliberately empty selection is preserved (section 5.3).

Work can start with M1.
