# Agent Instructions for sd-webui-thumbnailizer

sd-webui-thumbnailizer is a local Stable Diffusion WebUI extension for managing and generating checkpoint thumbnail sets. It runs inside A1111-compatible WebUI environments and writes thumbnail images next to checkpoint files.

This fork's development and testing target is [Haoming02/sd-webui-forge-classic `neo`](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo), the Forge Neo branch used in the local install.

Use this file as the primary repository context for coding agents. Keep changes narrow, testable, evidence-preserving, and compatible with the local WebUI install.

## Core Product Model

- Checkpoint files are user-owned assets.
- Thumbnail images are generated local evidence for comparing model behavior across preset prompts.
- Thumbnail sets are user-configurable prompt/settings bundles.
- The extension runs inside WebUI; it should not become a standalone service.
- The extension may generate images through WebUI processing, but it must not add remote generation or upload behavior.
- The default posture is local-first, Windows-friendly, and conservative around model folders.

## High-Value Docs And Files To Read

Before broad changes, read:

- `readme.md` for user-facing behavior, setup, known issues, and changelog.
- `metadata.ini` for extension identity.
- `scripts/thumbnailizer.py` for UI registration, model discovery, gallery updates, and generation flow.
- `override_settings.py` for user override parsing and prompt/settings mutation.
- `scripts/sets_template.json` for default thumbnail set schema.
- `scripts/blocklist_template.json` and `scripts/blocked_paths_template.txt` for filtering behavior.
- Relevant GitHub issues for the specific task.
- Haoming02/sd-webui-forge-classic `neo` docs or source when debugging Forge Neo compatibility.

Do not assume a GitHub issue is fixed unless the local code and current behavior show it.

## Current Stack

- Python inside the host WebUI virtual environment.
- Gradio UI components embedded through `modules.script_callbacks`.
- Pillow for image handling.
- A1111/Forge modules such as `shared`, `sd_models`, `processing`, and `images`.
- Windows-first local development under `C:\AI\sd-webui-forge-neo\extensions\sd-webui-thumbnailizer`.
- Primary host target: `https://github.com/Haoming02/sd-webui-forge-classic/tree/neo`.

## Compatibility Rules

- Preserve A1111 compatibility where practical while adding Forge Neo compatibility.
- Prioritize the Haoming02 Forge Classic `neo` branch for local testing and compatibility fixes.
- Prefer capability checks such as `getattr(...)` and `hasattr(...)` when WebUI or Gradio APIs differ.
- Treat `shared.cmd_opts`, Gradio component APIs, checkpoint path handling, and WebUI image saving behavior as version-sensitive.
- Do not assume `shared.cmd_opts.ckpt_dir` exists; Forge Neo may use `ckpt_dirs`.
- Do not assume old Gradio components such as `gr.Box` exist; Gradio 4 may require replacements.
- Avoid broad rewrites of the generation flow until the current WebUI processing behavior is understood from logs and local tests.

## Development Rules

- Prefer small, focused diffs.
- When the user proposes a feature or implementation path, briefly surface obvious smarter designs, safer sequencing, or important risks before executing. Proceed with the requested work unless the concern is a real blocker or the user redirects.
- Preserve user-created files such as `sets_user.json`, `blocklist_user.json`, `blocked_paths_user.txt`, and `override_settings_user.txt`.
- Do not delete checkpoint files, model folders, generated thumbnails, preview images, caches, or local logs unless explicitly requested.
- Do not silently change thumbnail naming semantics, set suffix semantics, overwrite behavior, blocklist behavior, or blocked-path behavior.
- Treat model paths, checkpoint names, prompt text, settings files, and sidecar metadata as user-controlled input.
- Be careful with cross-drive paths and symlinks on Windows.
- Keep publication to the user's fork unless the user explicitly asks for upstream PR work.
- Leave unrelated working-tree dirt alone.

## GitHub Workflow And User Learning

The user wants GitHub issues, commits, and PRs to be used as an explicit learning surface. For non-trivial work, ghostwrite clear issue and PR text that explains what happened, why the change exists, how it was validated, and what remains uncertain.

When the user reports a runtime error, pasted traceback, broken UI behavior, or Forge/A1111 compatibility issue:

- Treat it as a user-reported issue unless the user says not to.
- Create or draft a GitHub issue on the user's fork before or during the fix when practical.
- Preserve the exact traceback or log excerpt in the issue, trimmed only when needed for secrets or excessive noise.
- Describe observed behavior, expected behavior, environment, suspected cause, fix scope, validation, and remaining uncertainty.
- Link the issue in the commit message or PR body when committing or opening a PR.
- After the fix, update the issue or PR with what changed and what still needs live WebUI verification.

Prefer draft PRs for larger or multi-step work so the user can see how GitHub review flow works. Keep PR descriptions practical, not promotional. Include:

- Summary
- Why
- What Changed
- Validation
- Data / File Impact
- Compatibility Notes
- Unchanged / Non-goals
- Risks / Follow-ups

Do not open upstream PRs unless explicitly asked. Default publication target is the user's fork, `odyss3y/sd-webui-thumbnailizer`.

## UI Registration

The extension should register a top-level WebUI tab named `Thumbnailizer` through:

```text
script_callbacks.on_ui_tabs(on_ui_tabs)
```

If the tab does not appear, first inspect WebUI startup logs for:

- `*** Error loading script: thumbnailizer.py`
- `ui_tabs_callback` errors
- Gradio API errors
- missing module imports
- checkpoint path attribute errors

Fix import-time errors before debugging gallery or generation behavior.

## Generation And File Handling

- Thumbnail generation should target the checkpoint's directory unless a task explicitly changes that behavior.
- Preserve metadata behavior when using WebUI image saving helpers.
- Keep support for Civitai Helper `.preview` thumbnails distinct from generated set thumbnails.
- Respect overwrite controls unless the task is explicitly about fixing forced naming or WebUI filename-numbering behavior.
- When adding support for additional output formats such as WebP, update gallery lookup, generation logging, and file existence checks together.

## Testing And Validation

Use the smallest validation that proves the changed surface:

```powershell
C:\AI\sd-webui-forge-neo\venv\Scripts\python.exe -c "from pathlib import Path; compile(Path('scripts/thumbnailizer.py').read_text(), 'scripts/thumbnailizer.py', 'exec'); print('syntax ok')"
git diff --check
```

When practical, restart Forge Neo and verify:

- no `thumbnailizer.py` startup error
- the `Thumbnailizer` tab appears
- set dropdown loads
- gallery renders without crashing
- generation logs target the expected checkpoint and thumbnail path

If a validation path is blocked by the live WebUI environment, say exactly what was checked and what remains unverified.

## Decision Notes

For non-trivial changes, preserve the reason and constraints in a commit message, PR body, issue comment, or compact project note. Do not preserve chat transcripts, private reasoning, or chain-of-thought.

Use this format when a durable decision matters:

```text
Decision: <short title>
Prompt / trigger: <one sentence>
Intent inferred: <one sentence>
Decision: <what changed>
Why: <one or two bullets>
Guardrail: <what future agents must not break>
Follow-up: <next task, if any>
```

Keep it short. Preserve intent, scope, validation, compatibility boundaries, and deferred work.

## Definition Of Done

A non-trivial code change should include:

- focused implementation
- syntax/import/UI validation appropriate to the changed surface
- docs or changelog updates when user-facing behavior changes
- no deletion of user models or local generated assets
- no silent semantic change to naming, overwrite, set suffix, blocklist, or blocked-path behavior
- note of any tests not run and why
- compact decision note when future agents need the rationale

## Known Priority Areas

- Forge Neo script-load compatibility.
- Gradio 4 component compatibility.
- checkpoint discovery across `ckpt_dir`, `ckpt_dirs`, symlinks, and cross-drive paths.
- WebUI image save behavior, including filename numbering and WebP output.
- gallery refresh after generation.
- set persistence and default-set behavior.
