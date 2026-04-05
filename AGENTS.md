# Repository Guidelines

## Project Structure & Module Organization

`codex.nvim` is a small Neovim plugin with a standard Lua layout. Core modules live in `lua/codex/`:
`init.lua` exposes the public API, `actions.lua` is the user-facing command layer, `terminal.lua` manages the Codex job, and `ui.lua`, `session.lua`, `scratch.lua`, `config.lua`, and `log.lua` handle focused concerns.

`plugin/codex.lua` bootstraps the plugin on startup. Vim help lives in `doc/codex.txt`, demo media in `assets/`, and tests in `tests/` with `tests/minimal_init.lua` providing the isolated runtime.

The main user-facing flows currently live around terminal lifecycle and send helpers in `lua/codex/terminal.lua`, so changes there should be treated as API-adjacent even when they are internal refactors.

## Build, Test, and Development Commands

There is no separate build step. Use headless Neovim for validation:

```sh
nvim --headless -u tests/minimal_init.lua -c "PlenaryBustedDirectory tests { minimal_init = 'tests/minimal_init.lua' }"
```

This runs the Plenary test suite in `tests/`. For manual checks, open Neovim in the repo and load the plugin locally; confirm `:help codex` and terminal open/toggle flows still work.

If you update sending behaviour, also verify the no-`open()` path and any interaction with `focus_after_send`, `insert_after_send`, or `opts.open_window`.

## Coding Style & Naming Conventions

Write Lua modules under `lua/codex/` and keep each file scoped to one responsibility. Prefer `local` bindings, simple tables for module exports, and snake_case names such as `focus_after_send` or `ensure_autocmds`.

Match the surrounding file’s indentation and quote style instead of reformatting unrelated lines. Keep public API additions mirrored through `lua/codex/init.lua` when they are intended for users. Add brief comments only where behavior is not obvious.

Keep `README.md` and `doc/codex.txt` aligned whenever defaults, send semantics, or user-visible options change.

## Testing Guidelines

Tests use Plenary’s Busted interface. Add specs to `tests/codex_spec.lua` or a new `*_spec.lua` file. Name examples by observable behavior, for example `it('reuses running Codex job when toggling the terminal', ...)`.

Cover user-visible behavior, especially window state, job lifecycle, buffer/selection sending, and config flags. Prefer stubbing Neovim APIs the way current tests do instead of requiring a real Codex process.

When adding or changing send-related behavior, include at least one regression test for the on-demand startup path so contributors do not accidentally reintroduce a requirement to call `open()` first.

## Commit & Pull Request Guidelines

Recent commits use short, imperative subjects such as `Add insert_after_send for terminal insert-mode handoff` and `Readme update`. Follow that pattern: one concise line describing the change.

PRs should explain the user-facing effect, note test coverage, and link any related issue. Include screenshots or GIFs when UI behavior changes, and mention any Neovim or Codex CLI assumptions reviewers need to reproduce the change.
