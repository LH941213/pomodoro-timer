# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file Pomodoro timer web application. Zero dependencies, open `pomodoro.html` in any browser to use. All HTML, CSS, and JS are self-contained in one file.

## Architecture

**Dual-view design** — same file renders different views based on URL parameter:

| View | Condition | Purpose |
|------|-----------|---------|
| Main (control panel) | No URL param | Mode cards, settings, "launch timer" button |
| Popup (timer) | `?popup=1` | Compact standalone timer window |

**Communication:** `BroadcastChannel('pomodoro-sync')` enables real-time sync between main window and popup. Main window sends `init`/`switchMode`/`updateSettings`; popup sends `stateUpdate`/`popupClosed`.

**Timer core class:** `PomodoroTimer` (popup only) — manages countdown, session state machine (`focus` → `shortBreak`/`longBreak` → `focus`), progress ring, session dots, button states.

**Three modes** defined in `MODE_TYPES` and `MODE_DEFAULTS`:
- `work`: 25min focus, 5min short break, 15min long break (every 4 rounds), coral red
- `study`: 45min focus, 10min short break, 20min long break (every 3 rounds), blue-purple
- `entertain`: 30min focus, no breaks, green

**CSS variables** (`:root`) control theming: `--accent` and `--accent-glow` are set dynamically via `applyAccent(mode)` to switch color schemes. Each mode has its own color variable set (`--work`, `--study`, `--entertain`).

**Persistence:** Settings stored per-mode in `localStorage` under keys like `pomodoro-focus-work`, `pomodoro-selectedMode`, etc.

## No build, no test suite

This is a static HTML application. No build step, no package manager, no test framework.

## Validation

To test changes:
1. Open `pomodoro.html` in a browser — confirm control panel renders with three mode cards
2. Click a mode card — theme color and durations update
3. Click "弹出计时器" — a compact popup window opens with the timer
4. Start/pause/reset/skip in the popup — all controls work
5. Close popup — main window status resets
6. Open settings, modify values, save — verify values persist across page reloads
