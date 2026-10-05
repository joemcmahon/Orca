# ORCA Electron — Handoff Notes
_2026-10-04_

## What we did

Started from the official Intel-only `hundredrabbits/Orca` Electron release (Electron 8.5.3)
and brought it up to date for modern macOS / Apple Silicon.

### Repo
- Fork: `github.com/joemcmahon/Orca`, branch `main`
- Local: `/Users/joemcmahon/Code/Orca-electron`

### Changes made

| File | Change |
|---|---|
| `desktop/main.js` | Added `contextIsolation: false` (required since Electron 12); replaced `electron.remote` with `@electron/remote` (removed in Electron 14) |
| `desktop/sources/scripts/lib/acels.js` | `require('electron').remote.app` → `require('@electron/remote').app` |
| `desktop/package.json` | `electron` 8.5.3 → 44.5.1; `node-osc` 4.1.9 → 11.7.1; `electron-packager` → `@electron/packager` 20.3.0; build target `x64` → `universal` |
| `desktop/package-lock.json` | Regenerated; went from 108 upstream vulnerabilities to 0 |

### Operator fixes backported from JS upstream (2025-11-16 commits)
Applied in `/Users/joemcmahon/Code/ORCA/sim.c` (the C/terminal build):

| Operator | Fix |
|---|---|
| `D` delay | Default mod 8 → 1 |
| `I` increment | mod=0 now outputs `0` instead of wrapping at 36 |
| `R` random | Range is now inclusive on both ends |
| `L` lesser | Removed `.` special-case; treats `.` as 0 |

Z, J, Y were already correct in the C port. C/clock change was UI-only in JS.

## Installed app
`/Applications/Orca.app` — universal binary (arm64 + x64), Electron 44.5.1.
Built from `~/Documents/Orca-darwin-universal/`.

To rebuild after code changes:
```sh
cd /Users/joemcmahon/Code/Orca-electron/desktop
npm run build_osx
cp -R ~/Documents/Orca-darwin-universal/Orca.app /Applications/
```

## C terminal build
`/Users/joemcmahon/Code/ORCA/build/orca` — ARM64 native, operator fixes applied.

To rebuild:
```sh
cd /Users/joemcmahon/Code/ORCA
HOMEBREW_PREFIX=/opt/homebrew make
```

## Known non-issues
- CSP warning in dev console — dev-only, absent in packaged app
- UDP `EADDRINUSE` on first launch after a crash — kill the zombie process:
  `lsof -i :49160` then `kill <PID>`

## MIDI setup
- Output: IAC Driver Bus 1 (routes to DAWs / Pilot)
- Input: none selected (fine for sequencing out only)
- Hit **Space** to start/stop the clock
