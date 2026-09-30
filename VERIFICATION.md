# Verification record — Window Switcher & Badges (`app-management`)

Mac: M1 Pro, macOS 27.0.1 (26A434), arm64. Raycast 2.6.0.0 (pid 81278 at the start of M1). Swift 6.4 Command Line Tools
(swiftlang-6.4.0.34.1). Node 24.11.0. Two displays: built-in (Space 1) and one external 3840×2400 (Space 7).

Result labels, as in SPEC.md §11: **PASS (observed)** = seen on this Mac. **FAIL (observed)** = seen failing here.
**pass (automated)** = `node --test` logic only, never proof of on-Mac behaviour. **Needs owner** = requires the owner at
the Mac (keystrokes inside Raycast, Settings, reboot). **Not testable safely** = SPEC.md §10.1 rule. **Untested** = not
yet exercised, with the reason.

Evidence channels available to this session, in order of preference: (1) the installed helpers run read-only from this
terminal, which holds its own Accessibility grant (`window-helper check` → `accessibilityTrusted: true`; this is a
baseline channel, not Raycast's grant); (2) Raycast's log in `~/Library/Logs/com.raycast.macos/` for `openCommand`,
`commandExited`, and hotkey registrations; (3) `pgrep` polling for helper runs; (4) `raycast://` deeplinks to open a
command. This session does not send keystrokes to Raycast and does not read Raycast's window text; rows that need either
are marked **Needs owner** with the exact steps.

---

## Milestone checklist (SPEC.md §11, every acceptance ID)

| ID | Type | Milestone | Status | Where |
| --- | --- | --- | --- | --- |
| W-1 | M | M1 | Needs owner | §M1 |
| W-2 | M | M1 | Needs owner | §M1 |
| W-3 | M | M1 | Needs owner | §M1 |
| W-4 | M | M1 | Needs owner | §M1 |
| W-5 | M | M1 | Needs owner | §M1 |
| W-6 | M | M1 | Needs owner | §M1 |
| W-7 | M | M1 | Needs owner | §M1 |
| W-8 | M | M1 | Needs owner | §M1 |
| W-9 | M | M1 | Restart half PASS (observed): both old hotkeys re-registered after a normal Raycast quit/relaunch on 2.6.0 and the owner used both old commands afterwards; reboot needs owner | §M4 |
| W-10 | M | M1 | PASS (observed), terminal channel | §M1 |
| W-11 | M | M1 | Needs owner | §M1 |
| ST-1 | M | M1 | Recorded: likely questioned | §M1 |
| ST-2 | M | M1 | Recorded: likely acceptable | §M1 |
| ST-3 | M | M1 | Recorded: discouraged; standalone entry marked "remove before submission" | §M1 |
| J-1 | A | M2 | pass (automated) | §M2 |
| J-2 | A, M | M2, M3 | A pass; M PASS (observed): two Calculator instances → one row `2 windows`; helper quit per pid | §M3 |
| J-3 | A, M | M2, M3 | A pass (automated); M partial: `Not in Dock` observed on tracked quit apps under Windows unavailable | §M2 |
| J-4 | A, M | M2, M3 | A pass (automated); M PASS (observed): Calendar pinned, quit → `No badge`, `No windows`, primary Open App | §M2 |
| J-5 | M | M3 | Needs owner (an app quit with a retained Dock badge was not present; Discord is running without a window) | §M3 |
| J-6 | A, M | M2, M3 | A pass (automated); M PASS (observed): Finder and two untracked third-party app rows with no badge fact; Return = Switch to Window (focus itself needs owner) | §M2 |
| J-7 | A, M | M2, M3 | A pass (automated); M PASS (observed) in All Apps: Discord `2`, `No windows` (other two filters need owner) | §M2 |
| J-8 | A | M2 | pass (automated) | §M2 |
| S-1 | A, M | M2, M3 | A pass; M PASS (observed) | §M2 |
| S-2 | A, M | M2, M3 | A pass; M PASS (observed) | §M2 |
| S-3 | A, M | M2, M3 | A pass; M PASS (observed) | §M2 |
| K-1 | M | M2 spike, M3 | pending | |
| K-2 | M | M3 | Needs owner (Return on a window row) | §M3 |
| K-3 | M | M2 spike | Recorded (derived from rendered order); inline adopted | §M2 |
| K-4 | M | M2 spike | PASS (observed) for the `↳ ` text; icon tint needs owner screenshot | §M2 |
| K-5 | M | M2 spike, M3 | pending | |
| K-6 | M | M3 | Needs owner (collapse/expand keys); preference wired | §M3 |
| O-1 | A | M2 | pass (automated) | §M2 |
| O-2 | A | M2 | pass (automated) | §M2 |
| O-3 | A, M | M2, M3 | A pass (automated); M needs owner | §M2 |
| O-4 | A, M | M2, M3 | A pass (automated); M needs owner | §M2 |
| O-5 | A | M2 | pass (automated) | §M2 |
| Q-1 | M | M3 | Needs owner (⌃Q in the list); helper quit half PASS (observed) in J-2 | §M3 |
| Q-2 | M | M3 | Needs owner (Return on a closed window) | §M3 |
| H-1 | A | M2 | pass (automated, ported: window A-8 and badge F4/F5 fake-helper suites) | §M2 |
| H-2 | A, M | M2, M3 | A pass; M PASS (observed) in All Apps; Badged Only half needs owner | §M2 |
| H-3 | A, M | M2, M3 | A pass; M PASS (observed) in All Apps; Badged Only failure view needs owner | §M2 |
| H-4 | A | M2 | A pass; M PASS (observed) in All Apps | §M2 |
| H-5 | M | M3 | PASS (observed): owner's ⌘⇧R → 1+0, ⌘⌥R → 0+1, ⌘R → 1+1 (15:13:33–:46), all from app-management | §M5 |
| H-6 | M | M3 | PASS (observed) | §M3 |
| M-1 | A | M2 | pass (automated) | §M2 |
| M-2 | M | M4 | PASS (observed) for the first two launches (seed once, no reseed/no toast on later launches); the empty-list third launch needs owner | §M4 |
| M-3 | M | M4 | Needs owner (unpin Slack, choose the filter); old extensions' settings untouched (their storage was never read or written) | §M4 |
| M-4 | M | M4 | Restart half PASS (observed): tracked list, pins, filter unchanged after the Raycast restart; sort/recency not yet changed from defaults; reboot needs owner | §M4 |
| I-1 | A | M2 | pass (automated) | §M2 |
| I-2 | M | M4, M5 | PASS (observed) for app-management: 0 helper runs in 10 min at 0.5 s; the 3 window-helper runs seen belong to the old App Window Switcher launched by the owner (log) | §M4 |
| I-3 | M | M4 | PASS (observed) after the dev server stopped and after a Raycast restart; reboot needs owner | §M4 |
| C-1 | M | M5 | PASS (observed): all three extensions launched side by side from their own folders (log 14:19–14:22) | §M4 |
| C-2 | M | M5 | PASS (observed): ⌃⌥D on Manage Apps; old hotkeys removed and old commands switched off, extensions kept | §M5 |
| C-3 | M | M5 | PASS (observed): Show Dock Badges re-enabled with ⌃⌥J, launched twice from its own folder with its own storage, then removed again | §M5 |

---

## M1 — baseline and window-switcher gaps (2026-09-30)

### Baseline facts observed (read-only)

| Fact | Observed |
| --- | --- |
| Spec commit | `b886db3` is HEAD of this repository; no newer commit. Working tree was clean before M1. |
| Source projects | Window switcher: not a git repository, files unchanged during M1. Badge project: `main` at `7ddaab1`, clean, before and after M1. |
| Installed copies | `~/.config/raycast/extensions/app-window-switcher/assets/window-helper` SHA-256 `a5c4be9e…b866c2`, 430,672 bytes, `-rwxr-xr-x`, byte-identical to the project's `assets/window-helper`. `~/.config/raycast/extensions/dock-badges/assets/dock-badges` SHA-256 `9d165095…af5`, 90,872 bytes, `-rwxr-xr-x`, byte-identical to the project's copy. No `app-management` folder exists yet. |
| Helper sources | `helper/dock-badges.swift` SHA-256 `95c261de…0ce` matches its committed `.sha256`. Window helper sources: `Focus.swift 410241fc…`, `Inventory.swift e133c4c8…`, `PrivateAPI.swift 5bb7df14…`, `Quit.swift 127fe8e0…`, `main.swift c654b595…` (full hashes are written by M2 into `helper/window/SOURCES.sha256`). |
| Source test suites | Window switcher `npm test`: 27 pass, 0 fail. Badge project `npm test`: 158 pass, 0 fail. Both match SPEC.md §10.4. |
| `@raycast/api` | 2.6.0 installed in both projects (the window switcher's manifest says `^2.5.3`). |
| Hotkeys today (Raycast log, hotkey registrations) | `⌃⌥D` → `c:n:bretbuilds/dock-badges::-::show-badges`; `⌃⌥G` → `…::configure-apps`; `⌃⌥W` (keycode 13) → `c:n:bret/app-window-switcher::-::switch-windows`. |
| Permissions | `window-helper check` from this terminal: `accessibilityTrusted: true`, all eight private symbols resolved, WindowServer connection non-zero, 2 displays. Raycast.app's own grant was not touched and is not switched off while Raycast runs (§10.1). |
| Idle | `pgrep -fl "window-helper|dock-badges"` at the start of M1: no helper process. One unrelated orphan `grep --line-buffered -iE "dock badge|dock-badges|Dock Notification"` (pid 70980, ppid 1, running ~2 h) was left by an earlier session; not ours, left alone. `~/Library/LaunchAgents` holds only `com.microsoft.EdgeUpdater.wake.plist`. |

### Discrepancies between SPEC.md and the observed code (none blocking)

| Where | Spec says | Observed | Consequence |
| --- | --- | --- | --- |
| §6.5 `t_win` baseline | 172–194 ms helper for 11 apps / 12 windows (window F-14) | 407–495 ms for 12 apps / 14 windows today. `list --debug` shows why: Safari (pid 50356) has a window omitted from `kAXWindows` on a visible Space, so the helper spends its full 250 ms remote-token budget on it and reports `ax-budget` (`1 window(s) not resolved within 250 ms`). Without that scan the run is in the F-14 range. | The p95 target proposal in §6.5 (`t_win` ≤ 2× solo) is evaluated against today's solo numbers. The 250 ms budget is helper behaviour, unchanged (§2.2). |
| §4.2 keyboard | Diagnostic Info moves to ⌘⇧D | Window project uses `Keyboard.Shortcut.Common.Copy` (⌘⇧C) for Copy Diagnostic Info; badge project uses the same constant for Configure Apps | As the spec resolves: Configure keeps ⌘⇧C, Diagnostic Info takes ⌘⇧D (confirmed in the ⌘K panel in M3). |
| §5.2 | `parseView` gains the third filter value | Badge `views.ts` has `ListView = "pinnedAndBadged" \| "badgedOnly"` and `otherView` flips between two | The copied `views.ts` in this repo is edited (documented in its header): three values, a cycle instead of a flip. |
| §1.1 | badge project `SPEC-STORE.md` recorded four published extensions shipping compiled helpers | Re-checked on GitHub during M1 (see ST-2): still true, sizes 183 KB to 3.1 MB | Precedent stands; still not approval of ours. |
| §10.3 | no helper process while the command is closed | Confirmed for the source extensions at the start of M1 | Re-verified for the new copy in I-2. |

### W rows (SPEC.md §11.1)

| ID | Result | Evidence and what remains |
| --- | --- | --- |
| W-1 Regular Desktop → regular Desktop, built-in display | **Needs owner** | Today's inventory shows no window on a non-visible Space (visible Spaces `[1, 7]`; every discovered window is on 1 or 7), so the case cannot be arranged read-only. Window VERIFICATION F-1* observed the same mechanism fullscreen→regular on the external display. Steps: window README "Manual checks" item 1. |
| W-2 Real ⌘M, then hotkey → Return | **Needs owner** | Discovery half observed now: 7 minimized windows listed with `isMinimized: true` (Edge wid 73, WhatsApp 147, Slack 156, Reminders 167, two untracked third-party apps, Finder 1895), none dropped. Restore-by-Return needs the owner's keystrokes (⌘M is a synthetic-input step this session does not send). Window F-3a/F-3b observed restore for the AX-minimized end state. |
| W-3 Real ⌘H, then choose one Edge window | **Needs owner** | No app is hidden right now (`isHidden: false` for all 12). Window F-4a observed the `hide()` end state. |
| W-4 Keyboard-only row use | **Needs owner** | Steps: window README items 5, 6, 8. |
| W-5 Search rules | **Needs owner** | Logic is automated in the window project (A-3, 27 tests pass); the in-Raycast run is the owner's (README item 4). |
| W-6 Duplicate titles through the UI | **Needs owner** | README item 7. Helper side observed in window R-6. |
| W-7 ⌃Q in both lists, app that asks to save | **Needs owner** | Helper side observed (Calculator quit, `pid-mismatch`, `app-gone`). |
| W-8 Compare with built-in Switch Windows | **Needs owner** | Our count today: Edge 2 windows (one minimized). README item 9. |
| W-9 Raycast restart and reboot on 2.6.0 | **Needs owner** (reboot); restart half can be run by this session in M4/M5 with a normal quit and relaunch | Raycast updated itself to 2.6.0 at 11:34 today and the badge project observed both extensions' hotkeys after that relaunch; the window switcher's `⌃⌥W` registration appears in the 11:47 log after Raycast's relaunch, so the hotkey survived one Raycast restart on 2.6.0 (`[OBSERVED]` log). Reboot: not yet. |
| W-10 Timing baseline | **PASS (observed)**, terminal channel | Table below. |
| W-11 Badge V10 after reboot; A4 badge clearing in Badged Only | **Needs owner** | Both need the owner (reboot; a real badge change from the phone). |

Stop rule (§12 M1): nothing in W-1, W-2, or W-3 has been observed to fail; the still-open parts are owner keystrokes, not helper failures. M2 proceeds.

### W-10 timing baseline (`scripts/measure.sh 10`, 2026-09-30 17:42 UTC, terminal channel)

Load at the time: 12 apps, 13–14 windows (7 minimized in the inventory snapshot), 19 Dock items, Dock badges Slack `•`, Discord `2`, System
Settings `1`. Wall time is measured around `execFile`-equivalent process runs from `sh`; `helper` is the window
helper's own `elapsedMs`.

| Measure | Solo (10 runs) | Concurrent pairs (10 runs) | `t_both` |
| --- | --- | --- | --- |
| `t_win` helper `elapsedMs` | 415–495 ms (median 427) | 407–437 ms (median 418) | ≤ 0 ms: no slowdown |
| `t_win` wall | 443–524 ms | 433–462 ms | |
| `t_badge` wall (helper has no `elapsedMs`) | 81–97 ms (median 86) | 80–86 ms | ≤ 0 ms |
| pair wall | | 473–500 ms | = max of the two, as designed |
| `f_win` | 0/10 solo, 0/10 concurrent | | |
| `f_badge` | 0/10 solo, 0/10 concurrent | | |

Decision for §6.5: the two helpers do not slow each other (concurrent medians are within run-to-run noise of solo), so
the second helper is **not serialized**. Targets confirmed for the merged command, to be measured inside Raycast in M3:
`t_rows` p95 ≤ 400 ms; `t_win` and `t_badge` p95 ≤ 2× today's solo medians (≤ 854 ms and ≤ 172 ms). Note that `t_win`
depends on whether any app triggers the 250 ms remote-token budget (see discrepancies).

### ST-1 … ST-3 Store feasibility (documentation reading, 2026-09-30; informational, never a gate)

Primary source: https://developers.raycast.com/basics/prepare-an-extension-for-store, read 2026-09-30.

**Guideline text, verbatim.**

- Binary Dependencies and Additional Configuration: "Don't bundle opaque binaries where sources are unavailable or where
  it's unclear how they have been built." and "Don't bundle heavy binary dependencies in the extension – this would lead
  to an increased extension download size." Listed as acceptable: "Calling known system binaries"; "Binary downloaded
  or installed from a trusted location with additional integrity checking through hashes"; "Binary extracted from an
  npm package and copied to assets, with traceable sources how the binary is built."
- Preferences: "Use the preferences API to let your users configure your extension or for providing credentials like
  API tokens." and "You should not build separate commands for configuring your extension. If you miss some API to
  achieve the preferences setup you want, please file a GitHub issue with a feature request."
- Naming: extension titles "should follow Apple Style Guide convention"; "Aim to use nouns rather than verbs"; command
  titles "usually follows `<verb> <noun>` or just `<noun>`"; "Avoid articles". The page has no sentence about
  ampersands; `ray lint` decides that in M2.
- Metadata: "Ensure you use your Raycast account username in the `author` field"; "Ensure you use `MIT` in the
  `license` field."
- The page contains no sentence mentioning Accessibility, permission, private API, or undocumented symbols.

| ID | Helper | What it uses | Provenance we can show | Outcome |
| --- | --- | --- | --- | --- |
| ST-1 | `window-helper` (430,672 bytes, arm64, ad-hoc) | Public AX and AppKit, plus eight undocumented symbols resolved with `dlsym` and listed in `PrivateAPI.swift`: `_AXUIElementGetWindow`, `_AXUIElementCreateWithRemoteToken`, `SLSMainConnectionID`, `SLSCopySpacesForWindows`, `SLSCopyManagedDisplaySpaces`, `SLSManagedDisplayGetCurrentSpace`, `_SLPSSetFrontProcessWithOptions`, `GetProcessForPID`. Its header states AltTab and devadathanmb's Window Switcher (GPL) were read as documentation only. | Sources in `helper/window/`, per-file hash list, exact `swiftc` command, Swift version, hash test, and a rebuild comparison (M2). | **Likely questioned.** The binary guideline's literal test ("sources unavailable or unclear how built") is met. What the guideline does not address is private-API use; a reviewer may still ask about the eight symbols, the 250 ms remote-token brute-force scan, or the size, and no published precedent for an AX/SkyLight window helper was found in the `raycast/extensions` listing. Concrete alternative if refused: keep the extension private (no design change), or drop other-Desktop discovery and private activation and ship a public-AX-only helper (loses F-1/F-7 reach; a Swift change outside §2.2). |
| ST-2 | `dock-badges` (90,872 bytes, arm64, ad-hoc) | Public AX only, plus the undocumented Dock attribute name `AXStatusLabel` (a string attribute, no private symbol). | Source, `.sha256`, `swiftc` command, hash test; rebuild was byte-identical on this Mac. | **Likely acceptable**, by analogy with four published extensions that commit a compiled helper in `assets/` next to its Swift source and do not compile in `build` (re-checked on GitHub 2026-09-30: `display-modes` `assets/display-modes` 3,163,792 B with `Package.swift`+`Sources/`; `keyboard-brightness` `assets/keyboard` 1,381,304 B with `sources/`; `audio-device` `assets/audio-devices` 2,061,658 B and `assets/sound-control` 96,752 B with `swift/sound-control.swift`; `global-media-key` `assets/media-key` 182,790 B with `Package.swift`+`Sources/`). This is precedent, not an accepted submission of ours. `AXStatusLabel` remains a standing risk disclosed in the README. |
| ST-3 | `configure-app-management` command | — | — | **Discouraged by the guideline** ("You should not build separate commands for configuring your extension"). Design response, as §1.1 and J5 allow: the configure view stays reachable from the main list (⌘⇧C, `Action.Push`); the standalone root-search entry is kept for local convenience and is marked **"remove before submission"** in the manifest description and README. Removing it changes nothing else. A preferences-API replacement is not possible today: preferences have no list or reordering type (`[DOC]`, badge SPEC §4). |

Remaining review risk, in one line: the window helper's private symbols are the only element with no precedent and no
guideline sentence either way; everything else has a traceable build or a documented mitigation. Nothing in M2–M5
depends on Store acceptance.

### M1 done-when check

Every W row has a dated entry (W-10 PASS; the others Needs owner with the exact remaining step). The §6.5 baseline table
is filled. `scripts/measure.sh` is committed. Neither source project changed (window switcher files untouched; badge
project `git status` clean at `7ddaab1`).

---

## M2 — bring-in, pure logic, inline-rows spike (2026-09-30)

### Provenance (SPEC.md §10.4)

| Item | Result |
| --- | --- |
| `assets/window-helper` | Copied from the window switcher. SHA-256 `a5c4be9e97c54e9b0c9e832f15865531487a29debd717be2c09b87df85b866c2`, 430,672 bytes, Mach-O arm64, `adhoc,linker-signed`. **Rebuilt from the copied sources** with `swiftc -O -swift-version 5 -target arm64-apple-macos14` (Swift 6.4, 6.6 s) into the scratchpad: **byte-identical** (same SHA-256, same size). The copied binary is kept as the shipped one. |
| `assets/dock-badges` | Copied from the badge project. SHA-256 `9d16509531b9bc9ebd6dea6b07f805da34f81799aebd5274c6b0b4efafcd9af5`, 90,872 bytes (the badge project recorded its own byte-identical rebuild). |
| `helper/window/*.swift` | Verbatim (no header, so the hashes are the originals'): `helper/window/SOURCES.sha256` lists all five; `shasum -c` OK. `PrivateAPI.swift` carries its GPL-read-as-documentation-only header unchanged. |
| `helper/badge/dock-badges.swift` + `.sha256` | Verbatim; source SHA-256 `95c261de…0ce`, hash file rewritten only for the new relative path. |
| TypeScript and tests | Every copied file starts with `// Copied from <project> <file> on 2026-09-30, unchanged except <list>`. Changes are limited to import paths, the three storage-key renames (§5.3), `views.ts` gaining `allApps` as the default with a three-way cycle, `switch-action.ts` gaining an optional `onFocused` hook for recency, and `helper-binary.test.ts` covering both binaries (recorded hashes) and both hash files. |
| Test provenance test | `test/badge/helper-binary.test.ts` now fails if either binary's SHA-256 differs from the §10.4 values or any Swift source differs from its hash file. |
| License | `LICENSE` (MIT, same copyright holder as the badge project); manifest `license: MIT`. The copied window-switcher code is relicensed by its owner at this commit. |

### Automated checks

| Check | Result |
| --- | --- |
| Ported tests | Window switcher 27 and badge project 158 all green after the moves and key renames; three badge `views` assertions were rewritten for the new default filter (`allApps`) and the three-way cycle, as their headers state. |
| New tests | `test/rows.test.ts` (J-1, J-2 A, J-3, J-4 A, J-6, J-7 A, J-8, S-1..S-3, O-4 A, O-5, H-2..H-4, §6.2 truth table, §4.4 cases), `test/sort.test.ts` (O-1, O-2, O-3 A, recency store), `test/setup.test.ts` (M-1, all four rows plus corrupt `setup.v1`), `test/idle.test.ts` (I-1: no timers, no `interval`/`menu-bar`, launchCommand only to our own `app-list`, no network/Pro/AI APIs, one runtime dependency, exactly the six storage keys). |
| `npm test` | **255 pass, 0 fail** (185 ported + 8 binary/script + 62 new). |
| `npm run typecheck` | pass (tests included in `tsconfig`). |
| `npm run lint` (`ray lint`) | pass: manifest, icons, ESLint, Prettier. **The ampersand title `Window Switcher & Badges` is accepted**, and the author `bretbuilds` validated, so the fallback title is not needed. |
| `npm run build` to a scratch folder | pass; generated `raycast-env.d.ts` with the three preferences. |

### Inline-rows spike (K-1, K-3, K-4, K-5) and first real-UI observations

Method: instead of a throwaway `Inline Spike` command, the real `app-list` command (already written for M3) was imported with `npm run dev` and opened through `raycast://extensions/bretbuilds/app-management/manage-apps` and `…/app-list?fallbackText=<query>` (the command takes root-search fallback text as its initial query, added for this and for Raycast's fallback feature). Rows were read back with a read-only Accessibility dump of Raycast's window from this terminal (its own grant). Helper runs were counted with `scripts/count-helpers.sh` (`pgrep` every 10 ms). No keystrokes were sent to Raycast. Identity observed in Raycast's log: `e:n:bretbuilds/app-management`, entry points `dev_/bretbuilds/app-management/{manage-apps,app-list}`, installed at `~/.config/raycast/extensions/app-management/` with both helpers `-rwxr-xr-x` and hash-identical to the repository.

| Check | Result | Evidence |
| --- | --- | --- |
| First open (fresh identity) | PASS (observed) | Seed ran: seven default apps tracked and pinned, filter All Apps, toast `Pinned all tracked apps — Unpin the ones you only want to see when badged` visible (the earlier `Tracking the default apps` toast is replaced by it; only the last toast is readable). Exactly **1 window-helper run and 1 dock-badges run**, started 11 ms apart (14:03:06.256 / .267). |
| Rendered list (alphabetical All Apps, expanded) | PASS (observed) | Calendar `No badge` `No windows`; two untracked third-party apps (one with its window title as subtitle) `1 window` each; Discord `2` `No windows`; Finder `1 window` `1 minimized`; Mail `No badge` `No windows`; Microsoft Edge `2 windows` `1 minimized` followed by two window rows `↳ …` (one tagged `Minimized`); Microsoft Teams `No badge` `No windows`; two more untracked third-party apps, Reminders (`No badge`), Safari, Slack (`•`, `1 window`, `1 minimized`), System Settings, WhatsApp (`No badge`, `1 window`, `1 minimized`). Untracked apps show no badge fact (J-6/J-8). Primary action shown in the footer: `Open App ↵` on the first row (Calendar, no windows), `Switch to Window ↵` on one-window rows. |
| K-4 `↳` prefix | PASS (observed) for the text: window rows read `↳ <title>` verbatim. Icon tint: needs an owner screenshot (`screencapture` of Raycast's window needs Screen Recording for the host, which was not granted). |
| K-1 one ↓ reaches the first window row | Needs owner | The window rows are the items directly beneath the app row in the flat list; the keyboard step itself was not sent. |
| K-5 selection stability | Needs owner | `selectedItemId`/`onSelectionChange` mirror plus the nearest-survivor fallback are implemented (§7.4); not exercised by keyboard. |
| K-3 keystrokes (derived from the rendered order on this load, not from real presses) | Recorded; **inline adopted** | (a) arrow-only to window 2 of Edge, the 7th app row and the only multi-window app: inline 6 ↓ + 2 ↓ + ↵ = **9**; pushed 6 ↓ + ↵ + 1 ↓ + ↵ = **9** plus a view push. `E` = 0 (every app above Edge has one window). (b) fragment `luma` then ↵: **5** in both layouts (single title match focuses from the root in both). (c) one-window app Finder: 4 ↓ + ↵ = **5** in both. Inline is not worse in any scenario and removes the child mount; the §7.3 prediction holds. Decision written into SPEC.md §7.5. |
| S-1 | PASS (observed) | `luma` → Edge row, subtitle = the matching title, `2 windows`, `1 minimized`, `1 of 2 matches`, one `↳` row tagged `Minimized`; footer `Switch to Window ↵`. |
| S-2 | PASS (observed) | `edge` → one Edge row, `2 windows`, `1 minimized`, `▸` (collapsed), no window rows; footer `Show Windows ↵`. |
| S-3 | PASS (observed) | `safari` → Edge (single title match, `1 of 2 matches`, its `↳` row) and Safari (name match, `1 window`): one app row per matching app. `edge sleeping` → Edge single match with the Sleeping title (a token outside the app name is a title match, not a name match). |
| J-4 | PASS (observed) | Calendar (pinned, quit): `No badge`, `No windows`, primary `Open App`. Launch itself needs owner (Return). |
| J-7 | PASS (observed), All Apps | Discord `2` with `No windows` listed. Pinned + Badged and Badged Only need the owner to switch filters. |
| H-2 window helper failed, badge ok | PASS (observed) | Installed `window-helper` renamed → reopen: first row `Helper not installed — Rebuild and re-import…` tagged `Windows unavailable` with primary `Refresh Windows ↵`; then only the seven tracked rows, each with its badge (Slack `•`, Discord `2`, others `No badge`) and **no** `No windows` or `N windows` text; untracked apps absent. Restored, hash unchanged. |
| H-3 badge helper failed, window ok | PASS (observed) | Installed `dock-badges` renamed → first row `Helper not found — Badges could not be read` tagged `Badges unavailable`; tracked rows subtitle `Helper not found` with `Unavailable` and their window facts (`No windows` / `1 window`); untracked rows unchanged, Edge's window rows still listed (switchable). Restored. |
| H-4 both failed | PASS (observed) | Both status rows first (windows, then badges); only tracked rows, each `Helper not found` `Unavailable`, no window fact text anywhere. Restored; both hashes unchanged. |
| No helper process while a helper was missing | PASS (observed) | Counter saw none. |

M2 done-when: tests, typecheck, and lint pass; hash comparisons recorded; K-3 table recorded and the decision written back into §7. No spike command needs removing (the real command served as the spike).

---

## M3 — the App Management command (2026-09-30)

Code: `src/manage-apps.ts` (launcher), `src/app-list.tsx` (§4, §6, §7 inline as decided, §8), `src/configure-app-management.tsx`
(Pinned, Tracked, Available; Move up/Down within the tracked order; Pin/Unpin; Add; Remove), `src/storage.ts`, preferences
`Activation`, `Show “Other Desktop” tags`, `Expand windows by default`. Shortcuts as coded: Refresh ⌘R, Refresh Windows ⌘⇧R,
Refresh Badges ⌘⌥R, Pin/Unpin ⌘., filter cycle ⌘⇧V, sort ⌘⇧S, Configure ⌘⇧C, Copy Diagnostic Info ⌘⇧D, Quit ⌃Q, Copy Window
Title ⌘⌥C, Collapse/Expand ⌥←/⌥→, all ⌥⇧←/⌥⇧→. **The ⌘K panel rendering of these was not read (needs the owner; K-rows).**

Same evidence channel as §M2 (deeplink + read-only Accessibility dump + `pgrep` counter; no keystrokes). The hotkey
on `manage-apps` is the owner's to record; deeplinks stood in for it here.

| ID | Result | Evidence |
| --- | --- | --- |
| J-2 (M) | PASS (observed) | `open -n -a Calculator` twice (pids 57457, 57463). Search `calcul` → one `Calculator` row, `2 windows`, `▸`, footer `Show Windows ↵`. Quit through the helper for each pid with `--bundle com.apple.calculator`: `{"ok":true,"quit":true}` twice, no Calculator left. The in-list ⌃Q (Q-1) itself needs the owner. |
| J-5 | Needs owner | No app on this Mac was quit with a retained Dock badge (Discord shows `2` but is running with no window; Mail is quit with no badge). Owner: quit Mail with unread mail, press the hotkey: Mail with the badge tag and `No windows` in all three filters. |
| H-6 | PASS (observed) | Installed `dock-badges` replaced by `sh: sleep 2; exec <real helper>`. At ≈1.2 s after the press: **2 window rows present, 0 badge facts** (spinner on). At ≈4.0 s: 2 window rows, **5 badge facts**. Real helper restored, hash `9d165095…`. |
| H-5 | Partial | Every press by deeplink: exactly one `window-helper` and one `dock-badges` run (14:03:06, 14:09:38 after the dev server stopped). ⌘R / ⌘⇧R / ⌘⌥R counts need the owner: expected 1+1, 1+0, 0+1 (`scripts/count-helpers.sh 30` while pressing). |
| K-1, K-2, K-5, K-6, Q-1, Q-2, O-3 (M), O-4 (M), J-5, filters other than All Apps | Needs owner | Keyboard-only rows. Steps in the final report. |
| Screenshots (All Apps expanded, a direct-match search, both status rows, Configure) | Needs owner | `screencapture` of Raycast's window from this terminal failed (`could not create image from window`: the host has no Screen Recording grant, and none was added). Use Raycast's Window Capture. |

Installed build: dev server stopped (`ray develop` killed), `npm run build` refreshed `~/.config/raycast/extensions/app-management/`
(no `cli.pid`/`dev.log`; helpers `-rwxr-xr-x`, hashes unchanged). A deeplink press afterwards produced one run of each
helper and a populated list (I-3, first half).

---

## M4 — migration, persistence, idle, install (2026-09-30)

| ID | Result | Evidence |
| --- | --- | --- |
| M-2 first run | PASS (observed), first two launches | First open (14:03): seven defaults tracked and pinned, All Apps, seed toast. Every later open (14:04–14:22): no toast, same seven tracked rows, no reseed. Third-launch-after-emptying needs the owner (Configure → Remove all, then two presses: the list must stay empty with the "No apps tracked" view for tracked rows, while apps with windows still appear in All Apps). |
| M-3 seed-and-adjust | Needs owner | Two actions in the new list: select Slack, ⌘. (unpin), then ⌘⇧V or ⌘P to the filter you want (the badge project left this Mac in Badged Only with Slack unpinned). The old extensions' settings were never read or written; their lists still show their own state (owner launched both at 14:19, see C-1). |
| M-4 persistence across Raycast restart | PASS (observed), restart half | Raycast quit normally (`tell application "Raycast" to quit`, which reports "User canceled" yet quits) and relaunched: pid 81278 → 58376 at 14:11:37. After the relaunch the deeplink press showed the same tracked list, pins (Calendar and Mail listed with `No windows`, which only pinned tracked apps are), and filter All Apps. Reboot: needs owner. Sort and recency are still defaults, so their persistence is only unit-tested (parse/serialize round trips). |
| I-2 helper-free idle | PASS (observed) for this extension | 10-minute `pgrep` watch at 0.5 s (18:11:57–18:21:57 UTC) with our list closed after the Raycast restart: **0 runs of either helper from `~/.config/raycast/extensions/app-management/`**. Three `window-helper` runs were seen at 14:19:32, :38, :42; Raycast's log attributes them to the **old App Window Switcher** (`dev_/bret/app-window-switcher/switch-windows` + `window-list`, four launches) and two Dock Notification Badges launches by the owner in that minute (its ~90 ms helper is missed at a 0.5 s poll). `scripts/count-helpers.sh` now prints the extension folder so this attribution is direct next time. No LaunchAgent added (`~/Library/LaunchAgents` unchanged), no login item. |
| I-3 installed use | PASS (observed), restart half | `npm run build` after the dev server was killed; no `cli.pid` or `dev.log` in the installed folder; helpers `-rwxr-xr-x` with the recorded hashes. Press by deeplink at 14:09:38 (dev server stopped) and at 14:22:08 (after the Raycast restart): one `window-helper` and one `dock-badges` run each, 5–50 ms apart, list populated. Reboot: needs owner. |
| W-9 (old extensions) restart | PASS (observed), restart half | After the relaunch the log shows `Hotkey registered` for `c:n:bret/app-window-switcher::-::switch-windows` and `c:n:bretbuilds/dock-badges::-::show-badges` (25 registrations reconciled), and the owner launched both old commands at 14:19. |
| C-1 three extensions side by side | PASS (observed) | Log 14:19–14:22: `App Window Switcher: openCommand` (×4), `Dock Notification Badges: openCommand` (×2), `Window Switcher & Badges: openCommand` (×1), each from its own folder (`app-window-switcher`, `dock-badges`, `app-management`); no shared files. |
| README | Done | Install, hotkey, filters, sort naming ("Recent in This Command"), permissions and the never-switch-off rule, uninstall, provenance and private-symbol disclosure, Store notes (ST-3 "remove before submission"). |
| Daily-driver period (M4 done-when: one working day at home and one at work) | Needs owner | Cannot be compressed; the new command is installed in parallel with the old ones now. |

Source projects after M4: window switcher files untouched (helper mtime 12:11, hash `a5c4be9e…`); badge project `git status` clean at `7ddaab1`.

---

## M5 — cutover to one hotkey (owner's steps; not performed by this session)

Hotkeys are recorded in Raycast Settings, which this session does not drive, and SPEC.md §12 M5 step 4 disables the old
commands only after every M row above passes. Status: **not started**; the old hotkeys are still ⌃⌥D (Show Dock
Badges), ⌃⌥G (Configure Dock Badge Apps), and ⌃⌥W (Switch Windows by App); Manage Apps has no hotkey yet. Both old
extensions are installed, enabled, and were used by the owner during M4 (C-1 PASS).

| Step (SPEC.md §12 M5) | Status | What to do / what to expect |
| --- | --- | --- |
| 1. C-1 | PASS (observed) | §M4. |
| 2. Record the everyday hotkey on **Manage Apps** | **PASS (observed) 2026-09-30 14:34** — ⌃⌥D (`⌥⌃ #2`) registered as `c:n:bretbuilds/app-management::-::manage-apps`; the owner had briefly put it on `app-list` at 14:26 and moved it at 14:34; Show Dock Badges no longer holds ⌃⌥D | Raycast Settings → Extensions → Window Switcher & Badges → Manage Apps → Record Hotkey. If it is ⌃⌥D, clear ⌃⌥D from Show Dock Badges first. The log will show `Hotkey ID: c:n:bretbuilds/app-management::-::manage-apps`. |
| 3. Press it three times from different apps | **PASS (observed)**: presses at 15:12:10, 15:13:04, 15:13:16 each logged a launcher → list `openCommand` pair and ran exactly one `app-management window-helper` and one `app-management dock-badges` (10–18 ms apart); H-5 counts followed | Fresh list each time. Run `scripts/count-helpers.sh 30` first: expect one `app-management window-helper` and one `app-management dock-badges` line per press. |
| 4. Clear the remaining old hotkeys and switch the old commands off | **Done by the owner 2026-09-30** (owner's decision to proceed with the keyboard rows K-1, K-2, K-5, K-6, Q-1, Q-2 and the reboot checks still open; rollback remains available). Log: the last event for `dock-badges::-::configure-apps` (14:26:18) and `app-window-switcher::-::switch-windows` (14:26:39) is `Hotkey removed`; `show-badges` ends with `Hotkey removed` at 15:16:57 after the rehearsal. Both old folders and their helpers are intact (hashes unchanged). | Do **not** remove either extension (removal deletes its folder and storage). |
| 5. Source project directories untouched | PASS (observed) | Window switcher files unchanged; badge project clean at `7ddaab1`. |
| 6. C-3 rollback rehearsal | **PASS (observed) 15:16:42–15:16:57**: the owner re-enabled Show Dock Badges with ⌃⌥J (`⌥⌃ #38`), launched it twice (15:16:48, 15:16:52; `Dock Notification Badges: openCommand` pairs, helper runs from the `dock-badges` folder), saw the old list with its own settings, then removed the hotkey and disabled it again. | Re-enable an old command, re-record one old hotkey, confirm its list opens with its settings, disable again. |
| I-2 after the cutover | **PASS (observed)**: 10-minute `pgrep` watch at 0.5 s (19:17:44–19:27:44 UTC, right after the rehearsal): the only helper run was at 15:20:02, which Raycast's log attributes to a Manage Apps launch at 15:20:01 (the owner's press); nothing else from any folder. (In the earlier 15-minute cutover counter one line at 15:17:20 read `dock-badges window-helper`, a folder/name pairing the counter cannot produce from a single path; it fell during the rehearsal, before this watch, and is recorded as unexplained rather than attributed.) | `scripts/count-helpers.sh 600 0.5` with the list closed: expect no `app-management` line. |

### Final hotkey table (2026-09-30 15:17)

| Command | Hotkey | State |
| --- | --- | --- |
| Window Switcher & Badges → Manage Apps | **⌃⌥D** (`c:n:bretbuilds/app-management::-::manage-apps`) | enabled, the everyday hotkey |
| Window Switcher & Badges → Configure Tracked Apps | ⌃⌥F (`⌥⌃ #3`, recorded by the owner at 14:27; optional) | enabled |
| Window Switcher & Badges → App List (no hotkey) | none | enabled (opened by Manage Apps; retitled at the owner's request so the Settings row says so) |
| Dock Notification Badges → Show Dock Badges / Dock Badges List / Configure Dock Badge Apps | none (⌃⌥D and ⌃⌥G cleared) | disabled, not removed |
| App Window Switcher → Switch Windows by App / App Window List | none (⌃⌥W cleared) | disabled, not removed |

Cutover done-when: one hotkey opens the App Management list (three presses observed, one run of each helper per press); the old commands are disabled but present; this table is the final hotkey table; I-2 re-run below.

### Post-cutover additions (owner requests, 2026-09-30, afternoon)

| Change | Where | Checks |
| --- | --- | --- |
| Row layout revisions 1–9 (text first, fixed icon column with a square `pin.png`, badge slot always present, clipped titles, no `No windows`, no ax-budget icon, ten-or-more badge as a red dot, `N Minimized`) | `app-list.tsx`, SPEC.md §4.2 revisions | Alignment measured with a read-only Accessibility frame dump after each change (last: every `Minimized` tag within 2 px across blank, digit, dot, and minus rows); `badgeLabel` unit-tested. |
| Preferences trimmed to one (`Expand windows by default`) | manifest | Activation fixed to `auto`; Desktop tags removed; the four quit-shortcut preferences removed once the hotkey commands proved out. |
| Hotkey quit commands `quit-selected-app` and `quit-other-apps` (view commands mounting the list with a startup action; selection shared through `selection.v1` / `listState.v1`; pids for windowless apps via `/usr/bin/lsappinfo`) | `src/quit-*.tsx`, `src/lib/selection.ts`, `src/lib/lsappinfo.ts`, `src/running.ts` | Owner: Hyper H quits the selected app (Calendar, Discord, Mail) and the list returns with one reload; recorded hotkeys in the log: quit-selected-app `⌘⌥⌃⇧ #4`, quit-other-apps `none`. This session, by deeplink: selected Calculator quit with one helper quit call and one scan pair; a selection from a list closed > 3 s earlier refused; the previous list's selection is read before the new mount can overwrite it. Quit Other Apps: same code path and confirmation, not run against the owner's apps. Raycast's Hyper Key cannot drive a row action (Caps Lock → F20 at the HID level, consumed by Raycast's tap; confirmed with Caps Lock and Right Option as sources), which is why these are commands. |
| Command titles | manifest | `App List (no hotkey)` (the Settings title column clips at ~22 characters; Raycast shows neither descriptions nor subtitles there). Quit Selected App has `✦ H`; Quit Other Apps has no hotkey yet. |
| Idle rule unchanged | `test/idle.test.ts` | No timers; `launchCommand` only to our own `app-list`; `lsappinfo` is a system binary run on demand. |

### Owner checklist: every row still open, with the step and the expected result

Set the hotkey (step 2) first; `scripts/count-helpers.sh 30` in a terminal counts helper runs where a row asks for it.

| Row | Do | Expect |
| --- | --- | --- |
| W-1 | Built-in display: an Edge window on Desktop 1 and another on Desktop 2; on Desktop 1 press the hotkey, ↓ to Edge, ↓ to the Desktop-2 window row, Return | The display slides to Desktop 2, that window is in front, ⌘L selects its address bar |
| W-2 / K-2 | ⌘M an Edge window; hotkey; the Edge row shows `1 minimized`; ↓ to its `↳` row tagged `Minimized`; Return | Un-minimized and in front; Raycast closed with no Esc needed |
| W-3 | ⌘H Edge; hotkey; Edge row shows `Hidden`; choose one Edge window row; Return | Edge reappears with that window in front; its other windows keep their state |
| W-4 / K-1 / K-6 | Hotkey; ↓ to a multi-window app; one more ↓ | Selection lands on its first `↳` row. ⌥← collapses the group (▸ appears), ⌥→ expands; ⌥⇧←/→ all; the next hotkey press starts expanded again (or compact if the preference is off) |
| W-5 / S-1..S-3 (keyboard half) | Type a distinctive title fragment, Return | Exactly that window focused. Type `edge`, Return: the group expands beneath the row (no view push) |
| W-6 | In Edge press ⌘N twice; hotkey; type `new tab` | `2 of N match`, rows `1 of 2 with this title` / `2 of 2`; each Return reaches its own window |
| W-7 / Q-1 | ⌃Q on an app row and on a window row; once on an app with unsaved changes (TextEdit) | App quits; the list stays open; counter shows 1 `window-helper`, 0 `dock-badges`; selection moves to the nearest row; "still running" toast for the unsaved app |
| W-8 | Raycast's built-in Switch Windows: count Edge windows; compare with our Edge count | Ours ≥ theirs; note any difference |
| W-9 / M-4 / I-3 / W-11 (V10) after reboot | Reboot; log in; press the hotkey with no terminal open | List opens, one run per helper, filter/sort/pins/tracked list as left |
| W-11 (A4) | With Badged Only open, clear a badge from the phone, ⌘R | The row leaves; another row is selected; no error |
| J-3 / J-5 / J-7 in Pinned + Badged and Badged Only | ⌘⇧V to each filter | J-5: quit Mail with unread mail first: Mail row with its badge tag and `No windows` in all three filters. J-7: Discord `2` `No windows` in all three. J-3: an unpinned tracked app that is not in the Dock is absent from all three |
| K-3 (optional confirmation) | Count real presses for the three §M2 scenarios | Matches the derived table (9 / 5 / 5 on the same load) |
| K-4 | Raycast Window Capture of All Apps expanded, a direct-match search, both status rows (rename the installed helpers as in §M2), and Configure | Screenshots into `VERIFICATION.md`; window rows show the secondary-tinted window icon and `↳` |
| K-5 | Select a middle row; ⌘R; ⌘⌥R (badges only); ⌘⇧V; then ⌃Q on the selected app; then Return on a window closed after listing | Selection stays on the same row for the first three; moves to the nearest survivor after Quit and after "That window closed" |
| H-5 | Counter running: ⌘R, then ⌘⇧R, then ⌘⌥R | 1+1, 1+0, 0+1 |
| H-2 / H-3 in Badged Only | Rename the installed helper as in §M2, ⌘⇧V to Badged Only | H-2: Badged Only unaffected (badged rows shown, windows-unknown marker). H-3: the yellow "Helper not found" failure view, never "No tracked apps have a badge" |
| O-3 / O-4 | ⌘⇧S to Recent; switch to Mail through the command; reopen. Then ⌘⌥R in each filter | Mail first under Recent; badge refresh never reorders or refocuses |
| M-2 third launch | Configure → Remove every tracked app; Esc; press the hotkey twice | Apps with windows still listed; no tracked rows; no reseed, no toast |
| M-3 | Unpin Slack (⌘.), choose the filter | New list shows the seven apps in order with Slack unpinned; ⌃⌥D still shows the old list unchanged |
| K-rows ⌘K panel | Open ⌘K on an app row | Shortcuts as listed in §M3, Configure ⇧⌘C, Copy Diagnostic Info ⇧⌘D |
| M4 done-when | Use the new command for one working day at home and one at work (multi-display) with the old ones still enabled | No FAIL rows |
| C-2, C-3 | Only after the above | §12 M5 steps 4 and 6 |

## Trash row (SPEC.md §9, 2026-09-30 evening)

Built on the owner's request after M5; installed with `npm run build` at 17:06 (no dev server). Evidence channel as in
§M2: deeplink with `fallbackText`, read-only Accessibility dump, Raycast log. No keystrokes were sent and Empty Trash was
never run by this session.

| Row | Status | Evidence / what to do |
| --- | --- | --- |
| T-1 visibility table (filter × pin × search) | PASS (unit) | `test/utilities.test.ts` |
| T-2 `utilityPins.v1` read/write, unknown ids dropped, never written on read; storage-key list updated | PASS (unit) | `test/utilities.test.ts`, `test/idle.test.ts` |
| T-3 `launchCommand` only to `app-list` or `raycast/system-actions/empty-trash` | PASS (unit) | `test/idle.test.ts` (guard narrowed, not removed) |
| T-4 search, unpinned, All Apps | PASS (observed 17:07) | `trash`: Finder's `Trash` window rows first (Raycast labels 1, 2; footer "Switch to Window ↵"), then section **Utilities** › **Trash** (label 3). `empty`: only Utilities › Trash, footer "Open Trash ↵". `zzqq`: "No app or window matches", no Trash row. Log clean. |
| T-5 app order and ⌘-number labels unchanged | PASS (observed, search half) | Raycast numbers the first ten rows 1…9, 0 by position; the Trash row is always after every app row, so it only ever takes the next free number. Owner: pin Trash (⌘. on the row, or Configure › Utilities), compare All Apps with no search to before: same app rows and numbers, Trash last. |
| T-6 pinned in each filter | **Needs owner** | Pinned: Trash at the end of All Apps and Pinned + Badged, absent from Badged Only (⌘⇧V to cycle). Unpin: gone from all three. |
| T-7 Quit hotkey with Trash selected | **PASS (owner, 2026-09-30 evening)** | Trash selected, Quit Selected App pressed: the refusal toast "Select an app first, then press the hotkey", nothing quit. |
| T-8 Open Trash | **Needs owner** | Return on Trash: Finder's Trash window, Raycast closes, no permission prompt; the log shows no `System Actions: openCommand` (native `open`, not Raycast's command). |
| T-9 Empty Trash | **PASS (owner), confirm half**: Empty Trash… reached by right-clicking the main-list Trash row; the owner reports the Trash emptied. Log: `Action performed: System Actions: openCommand` at 17:18:37.278 (the launch from this extension was not dropped), an alert showing at 17:18:39.5. Cancel half and which Raycast prompts appeared: not reported. | Put one throwaway file in the Trash. ⌘K › Empty Trash… › Cancel: file still there. Again › Empty Trash: Raycast's "Run Command / Always Run Command" (first time only once Always is chosen), then Raycast's warning if on, then "Trash emptied"; log `Action performed: System Actions: openCommand`. If nothing appears after confirming, Raycast dropped the launch while our alert was closing: record it. |
| T-10 Empty Trash disabled | **Needs owner** | Disable System Actions › Empty Trash in Raycast Settings, repeat T-9's confirm: failure toast "Raycast's Empty Trash command is not available … Nothing was erased"; re-enable. |
| T-11 Configure › Utilities | **Needs owner** | Trash listed between Tracked and Available with Pin/Unpin (⌘.), no `#n`; returning to the list shows the new pin state without a helper run. |

Side note from the investigation (16:56:43): opening `raycast://extensions/raycast/system-actions/open-trash` from the
terminal raised Raycast's external-launch alert, which was answered at the Mac at 16:56:46; Finder, which had not been
running, opened a Trash window. That is why Finder shows a `Trash` window in T-4.

### Wording: "Tracked" renamed to "Badge Tracking" (owner request, 2026-09-30 evening)

User-facing text only; storage keys, code identifiers, and SPEC.md terminology keep "tracked". Configure's sections
read Pinned (`badge tracking, always listed`), Badge Tracking (`listed only with a window or a badge`), Utilities,
Available (`no badge tracking, listed only with a window`); toasts and empty views say "badge tracking" /
"badge-tracked". The standalone command is retitled **Configure Apps** (the in-list action's name; `Configure Badge
Tracking` would clip in Raycast Settings' ~22-character title column). Its command name `configure-app-management` is
unchanged, so the owner's ⌃⌥F hotkey stays attached. Section titles observed by deeplink + read-only dump; owner confirms ⌃⌥F still opens Configure (PASS).

T-9 note: the owner reports Empty Trash… is reachable by right-clicking the Trash row in the main list (Raycast opens
the Actions panel); right-clicking the Trash row in Configure shows only Pin, by design.

## Store preparation (2026-09-30, late evening)

ST-3 decision (owner): the standalone command stays, retitled **Manage Pinned Apps** (command name
`configure-app-management` unchanged, so ⌃⌥F stays attached). The in-list action, its navigation title, the seed toast,
and the empty views use the same name. The case for keeping it goes in the PR description: Raycast preferences have no
list or reordering type, and the same view is reachable from the list with ⌘⇧C. If a reviewer still asks, removing the
manifest entry changes nothing else.

Intel: not built (owner decision). The window helper now fails before running on any architecture other than arm64
with "Requires a Mac with Apple silicon", as the badge helper already did; unit-tested. Rebuilding universal binaries
would replace the verified `window-helper` and reopen its M2–M5 rows.

Other changes: `App List (no hotkey)` → `App List` (lint title-case warning; the hint moved to the description and
README), command descriptions rewritten for Store users, window-helper failure text says "Reinstall the extension from
the Raycast Store" instead of `npm run dev` (unit test: no failure text mentions npm/rebuild), `CHANGELOG.md`, `publish`
script, README rewritten for Store users. `npm test` 276/276, `ray lint` clean, `npm run build` succeeded (installed
copy refreshed).

Still open before `npm run publish`: screenshots in `metadata/` (owner, Window Capture, 2000×1250), icon check in light
and dark (owner), whether SPEC.md / VERIFICATION.md stay in the submitted folder, the Dock Notification Badges overlap,
and the private-API disclosure in the PR description.
