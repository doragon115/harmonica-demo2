# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A collection of standalone, self-contained HTML files implementing an interactive diatonic harmonica note-layout chart with Web Audio playback. There is no build system, package manager, server, or test suite — each `.html` file is a complete app (HTML + CSS + JS inline) meant to be opened directly in a browser. Files were added one-by-one via GitHub's web upload UI, so there is no meaningful git history of incremental diffs per file.

## Running/testing changes

There are no build, lint, or test commands — this is plain static HTML/CSS/JS. To verify a change, open the file directly in a browser (or serve the directory with any static file server, e.g. `python3 -m http.server`) and interact with it manually. Web Audio requires a user gesture (click/tap) before sound will play — every file handles this via a "tap to start" button or a resume-on-click listener on `AudioContext`.

## File inventory and relationships

- `cloude4harmonica.html`, `cloude4harmonica_final_restored.html`, `cloude4harmonica_full_fixed.html`, `cloude4harmonica_working_ios.html` — **byte-for-byte identical** (638 lines each). These are the full-featured app, saved multiple times under different names during iteration. Treat any one of them as canonical; if you fix a bug or add a feature in the full app, apply the change to **all four** to keep them in sync (or ask the user whether the redundant copies should be consolidated/deleted).
- `cloude4harmonica_final_mobile.html` and `cloude4harmonica_ipad_ok.html` — smaller, near-duplicate mobile/iPad-focused test pages (~93 lines) with a "tap to start" button and a short row of note buttons. Not identical to each other; minor CSS and copy differences (title text, colors, spacing) between mobile vs iPad variants.
- `harmonica-ios.html` — minimal single-octave note tester (C–C5) for iOS Web Audio unlock testing.
- `test.html` — minimal single-button Web Audio smoke test (plays a 440Hz tone).

When asked to fix or extend "the harmonica app", the target is almost always the full 638-line variant (`cloude4harmonica*.html`). The other files are throwaway diagnostic pages used to isolate iOS/Safari audio-unlock issues.

## Architecture of the full app (`cloude4harmonica*.html`)

Everything lives in one file: inline `<style>` block, static HTML shell, and a `<script>` at the bottom. Key parts of the script:

- **`notesLayouts`** — note names for each of the 10 harmonica holes, keyed by base key `C`. Each hole entry is a 7-slot array: `[topBend/special, overblow, blow, draw, bend1, bend2, bend3]` (nulls where that slot doesn't apply for that hole).
- **`frequencies.C`** — static frequency table for the C-key layout, keyed by note codes like `B1` (blow, hole 1), `D3` (draw, hole 3), `DB2-1` (draw bend 1, hole 2), `OB8` (overblow, hole 8), `SN1` (special/top bend, hole 1).
- **`semitoneMap` / `semitoneToNameSharp` / `semitoneToNameFlat`** — used to transpose the C-key layout and frequency table to any of the 12 keys when the key selector changes (frequencies are shifted by `Math.pow(2, offset/12)`; note names are relabeled preferring flats for the standard flat keys).
- **`renderHarmonica()`** — rebuilds the 10-hole DOM grid from `currentLayout`, using fixed absolute-positioned rows (`row-special-note`, `row-overblow`, `row-blow`, `row-hole-number`, `row-draw`, `row-bend-1..3`) so all holes align vertically regardless of which optional rows (bends/overblows) they have.
- **`playHarmonicaNote()` / `stopHarmonicaNote()`** — single-voice Web Audio playback via `OscillatorNode` + `GainNode`; only one note sounds at a time, toggled on repeat tap of the same cell. Oscillator waveform and optional attack/decay envelope are controlled by the "音色" (timbre) and "エンベロープ有効" (enable envelope) controls.
- **`addClickListeners()`** — binds both `click` and `touchstart` (with `preventDefault`) on the harmonica container so mobile taps register reliably, plus `Enter`/`Space` keyboard activation on each note cell for accessibility.

Key selector change flow: `keySelector` `change` handler computes a semitone offset from `C`, remaps `baseLayoutSemis` back into note names for `currentLayout`, recomputes `harmonicaFrequencies` from the static C frequencies by pitch-shifting, then calls `renderHarmonica()`.

## Conventions

- UI text, comments, and labels are in Japanese (`lang="ja"`); keep new user-facing text and comments consistent with that unless told otherwise.
- Note-code naming scheme to preserve when extending: `B{hole}` = blow, `D{hole}` = draw, `SN{hole}` = special/top bend, `OB{hole}` = overblow, `DB{hole}-{n}` = draw bend level `n`.
- Dark-themed, mobile-first inline CSS with breakpoints at `480px` and `360px` that shrink cell sizes and reposition the absolute row offsets — if you resize note cells, update the corresponding `.row-*`/`.label-*` `top` offsets at each breakpoint so rows stay aligned.
