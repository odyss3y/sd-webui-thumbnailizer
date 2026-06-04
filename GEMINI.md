# Gemini Context

Primary coding-agent instructions live in `AGENTS.md`.

Before making changes, read:

1. `AGENTS.md`
2. `readme.md`
3. `scripts/thumbnailizer.py`
4. `override_settings.py`
5. the relevant GitHub issue or task description

Project reminders:

- This extension runs inside A1111-compatible WebUI environments, including Forge Neo.
- Primary development and testing target is `https://github.com/Haoming02/sd-webui-forge-classic/tree/neo`.
- Do not assume WebUI, Gradio, or checkpoint-path APIs are stable across versions.
- Preserve user-created settings and filtering files.
- Keep generation local and do not add remote upload or remote generation behavior.
- Report validation gaps honestly when live WebUI restart/testing is not performed.

Useful focused checks:

```powershell
C:\AI\sd-webui-forge-neo\venv\Scripts\python.exe -c "from pathlib import Path; compile(Path('scripts/thumbnailizer.py').read_text(), 'scripts/thumbnailizer.py', 'exec'); print('syntax ok')"
git diff --check
```
