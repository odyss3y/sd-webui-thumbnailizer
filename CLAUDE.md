# Claude Context

Primary coding-agent instructions live in `AGENTS.md`.

Read `AGENTS.md` first, then use these project-specific reminders:

- This is a local Stable Diffusion WebUI extension, not a standalone app.
- Primary development and testing target is `https://github.com/Haoming02/sd-webui-forge-classic/tree/neo`.
- Preserve A1111 compatibility where practical while adding Forge Neo compatibility.
- Check WebUI startup logs first when the tab does not appear.
- Preserve user files such as `sets_user.json`, `blocklist_user.json`, `blocked_paths_user.txt`, and `override_settings_user.txt`.
- Do not delete checkpoint files, model folders, generated thumbnails, preview images, caches, or local logs unless explicitly requested.
- Keep changes narrow and validate with syntax checks plus live Forge Neo checks when practical.

When uncertain, prefer a small reversible compatibility shim with clear notes over a broad rewrite.
