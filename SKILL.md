---
name: macos-automation-permissions
description: Make unattended macOS automation (launchd jobs, browser driving, screen capture, AppleScript, physical clicks) survive permission resets, and diagnose the cryptic errors. Use when AppleScript returns -1743 or -1728, when permissions "reset" after a Homebrew/Python upgrade, when launchd jobs can't screen-record or reach the LAN, when a CDP click fails but a real click works, or when typing into a browser console goes wrong under a CJK input method.
---

# macos-automation-permissions

Status: doc-only. Observed on recent macOS releases; permission UI wording varies.

## Error decoder

| Error | Meaning |
|---|---|
| AppleScript `-1743` | Automation denied: the app that launched your session lacks "Automation" permission for the target app |
| System Events "is not allowed assistive access" (`-25211`, sometimes `-1719`) | Accessibility permission missing for the launching terminal app |
| `-1728` ("can't get object") | Not a permission error by itself: the object does not exist (wrong window or element, app not ready). Check that before blaming permissions |
| Screenshot is wallpaper-only / black | Screen Recording permission missing |

These attach to the **launching app** (your terminal), not to the script. Permissions are human-granted; a program can't
grant itself. But "blocked" is a bad report - say which of the three it is. (Codes can differ between macOS releases; treat the table as a starting point.)

## Make permissions stick for launchd jobs

If launchd starts your interpreter directly, the grants attach to that interpreter binary - and silently vanish when
the interpreter is upgraded (a minor Python bump from a package manager is enough).

Fix: a **small signed launcher app** that launchd runs; the launcher spawns the real worker as a **child**
(never `exec` - the launcher must stay the responsible process). Sign with a stable developer identity so
rebuilding keeps the grants. Local-network permission cannot be added manually in Settings and the prompt doesn't
reappear if missed -> run LAN calls through a helper that detaches responsibility and invokes Apple's `/usr/bin/curl`.
If the Local Network prompt hangs, do not `killall UserNotificationCenter` (it throws the decision away); keep the
requesting process alive and click Allow. (`ios-simulator-ui-test-hygiene` suggests a different, narrower use of that process for the unrelated crash-report dialog: the two situations differ.)

## Real input vs synthetic input

In the author's experience some browser features (passkey/WebAuthn system dialogs) react only to real OS-level input: a CDP `page.click` left the button spinning, while an OS-level click (e.g. `cliclick`) opened the dialog. Observed on one setup, so test it on yours. Re-measure coordinates from a
fresh screenshot each time (Retina = 2x), never store them. If the OS asks for Touch ID or a password, hand off to the human: automation must not try to get around it.

## Typing into a page or DevTools console reliably

- With a Korean/Japanese/Chinese IME active, synthetic typing turns into jamo. Switch to an ABC layout first
  (and restore after).
- `System Events keystroke` doesn't reach a DevTools console; physical key events do. Paste instead of typing:
  `pbcopy` -> cmd+v via `cliclick kd:cmd t:v ku:cmd` -> Return; read results back by calling `copy(result)` in the
  console and `pbpaste`.
- Typing letters while DevTools "Elements" has focus triggers shortcuts (e.g. `h` hides the element). Set page
  inputs from JS by setting `value` and dispatching `input`/`change` events.
- Browser "Allow JavaScript from Apple Events" toggles may not persist under automation; the DevTools-console route did. Security note: that browser setting lets any local script drive your logged-in pages, so turn it on only for a throwaway profile and off again afterwards.

## Diagnose first, then report

Write a tiny diagnostic that tries each of (Chrome AppleScript, System Events, screencapture) and prints which one
fails. Offer a degraded mode (open app + physical clicks + key codes) when only direct app scripting is blocked.
