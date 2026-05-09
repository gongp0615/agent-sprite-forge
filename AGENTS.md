# Repository Guidelines

## Project Structure & Module Organization

This repository packages Codex skills for 2D game asset generation. Skill definitions live in `skills/<skill-name>/SKILL.md`; keep user-facing workflow rules there. Helper scripts are under `skills/<skill-name>/scripts/`, reference contracts under `skills/<skill-name>/references/`, and agent presets under `skills/<skill-name>/agents/`. Showcase images and GIFs used by the README live in `src/`. The root `README.md` is the canonical public overview, with localized variants in `README.ja.md`, `README.ko.md`, `README.zh-CN.md`, and `README.zh-TW.md`.

## Build, Test, and Development Commands

- `python3 -m venv .venv && source .venv/bin/activate`: create and enter a local Python environment.
- `pip install -r requirements.txt`: install runtime dependencies (`Pillow`, `numpy`) for post-processing scripts.
- `python3 skills/generate2dsprite/scripts/generate2dsprite.py --help`: inspect sprite processor modes and options.
- `python3 skills/generate2dmap/scripts/compose_layered_preview.py --help`: inspect layered-map preview arguments.
- `python3 -m compileall skills`: quick syntax check for all Python helpers.

There is no packaging or build step; changes are validated by script-level checks and targeted sample runs.

## Coding Style & Naming Conventions

Python scripts use 4-space indentation, `from __future__ import annotations`, `pathlib.Path`, type hints where useful, and small functions with explicit validation errors. Prefer structured formats (`json`, `argparse`) over ad hoc parsing. Use lowercase snake_case for functions, variables, and script filenames. Keep skill and reference Markdown direct and imperative; preserve existing terminology such as `solid magenta`, `prop pack`, and `layered map` when editing workflow rules.

## Testing Guidelines

No formal test suite is currently checked in. For script changes, run `python3 -m compileall skills` and at least one focused command against a small local fixture or `--help` when fixture creation is not practical. For image-processing behavior, verify output files, dimensions, alpha handling, and generated JSON reports. Add minimal fixtures only when they clarify a regression.

## Commit & Pull Request Guidelines

Recent commits use short imperative subjects, for example `Add Godot map showcase` or `Tighten map and sprite generation skill rules`. Follow that style: one concise line describing the user-visible change. Pull requests should include a summary, changed skill/script paths, validation commands run, and before/after assets or screenshots when README showcase images or generated visual outputs change. If public README content changes, note whether localized READMEs were updated or intentionally left unchanged.

## Agent-Specific Instructions

Keep edits scoped. Do not rewrite generated showcase assets unless the task requires it. When changing a skill, update its references or scripts in the same PR only if the behavior contract changes.
