# Agent protocol

## Commands

There is no package manager, dev server, or CI.

- Install: `chmod +x src/webapp-launcher/webapp-launcher` (optional: `ln -s "$PWD/src/webapp-launcher/webapp-launcher" ~/.local/bin/webapp-launcher`)
- Dev: edit `src/webapp-launcher/webapp-launcher`
- Test (one file): `bash -n src/webapp-launcher/webapp-launcher`
- Test (full): `bash -n src/webapp-launcher/webapp-launcher`
- Lint (knowledge): `python3 scripts/lint_knowledge.py --strict` (vendored from knowledge-architecture v0.1.0)
- Lint (script): `bash -n src/webapp-launcher/webapp-launcher`; `shellcheck src/webapp-launcher/webapp-launcher` if the host has it

`desktop-file-validate` and `gtk-launch` apply to generated user files under `~/.local/share/applications/`, not this tree.

## Hard rules

- Do not add a dependency, edit generated code, or change a migration without an explicit ask.
- Do not commit secrets, credentials, or `.env` values into the repo or these docs.
- Minimal diffs. Touch only what the task requires.
- For work that will edit more than two files, write `PLAN.md` first. Overwrite it each loop. Do not commit an empty `PLAN.md`.
- Run the targeted test before calling the task done.
- One directory per tool under `src/`. Do not flatten tools into the repo root.
- Do not invent StartupWMClass. The live window is the source of truth; see `src/webapp-launcher/README.md`.

## Authority

- Level 0 (not facts): `PLAN.md`, chat
- Level 2 (constraints): none yet — no accepted decisions
- Level 3 (prefer over prose): none — no generated schema
- Level 4 (do not edit unless asked): this file, `README.md`, `LICENSE`

## Where to read

| Need | File |
|---|---|
| What this is | README.md |
| How to run webapp-launcher | src/webapp-launcher/README.md |

## After you finish

Propose, do not silently apply: a `docs/now.md` draft if weekly intent appeared, a decision draft if you chose something you would hate to re-litigate, a one-line history note if git will not explain it. Do not create empty `docs/` rings. Do not silently edit this file or `README.md`.
