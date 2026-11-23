# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A deliberately simple to-do app: one file, `index.html`, with inline CSS and vanilla JS. No frameworks, no build step, no tests, no package manager, not a git repo. Run it by opening `index.html` in a browser (or the Claude browser pane). Keep it dependency-free.

## Architecture (index.html)

- **State is in memory only**: `todos` (`{id, text, category, due, done}`), `nextId`, `editingId`. A reload clears items. Only the theme choice persists (`localStorage` key `todo-theme`, always wrapped in try/catch).
- **`render()` rebuilds the whole `<ul>`** from `todos` on every change; mutate state, then call `render()`. Text is set via `textContent` (no HTML injection). Edit mode is just `editingId` selecting which row renders inputs instead of text.
- **Event delegation**: one `click`, `keydown` and `focusout` listener on `#list` (not per-row). Buttons are identified by class (`edit-btn`, `del-btn`), so the SVG icons use `pointer-events:none` to keep `e.target` as the button. `focusout` saves an edit but ignores focus moves within the same `<li>`.
- **Theming**: CSS custom properties on `:root`, overridden under `:root[data-theme="dark"]`; `applyTheme()` sets `data-theme` and `aria-pressed` on the icon buttons. Defaults to the system `prefers-color-scheme` when nothing is saved.
- **Categories** are a fixed list (`CATEGORIES`: Work, Personal, Others), used by both the add form's `<select>` (static markup) and the edit-row select (built in JS), so change both places together.
- **Demo data**: opening `index.html#demo` or `#demo-dark` seeds sample items (block at the end of the script). It exists only for the mock-ups.

## mockup.html

Phone mock-ups (iPhone 15 light, Pixel dark) that embed a **copy of `index.html` inside `srcdoc` iframes**, because the browser pane can't load relative iframe paths from local files. The copy is baked in, so it goes stale whenever `index.html` changes and must be regenerated (rewrite the srcdoc contents from the current `index.html`, loaded with `#demo` / `#demo-dark`).

## Other files

- `Claude-Code-Key-Learnings.docx`: a generic write-up of Claude Code learnings (not app documentation). Generated with python-docx; it is separate from the claude.ai doc of the same name and can drift from it.
