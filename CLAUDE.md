# Pearwave

**Read `AGENTS.md` first — it is the working guide for this repo** (purpose,
stack, repository map, working agreements, run/test commands, backend and
frontend conventions, safety checklist). This file exists only to record what
`AGENTS.md` and `README.md` do not say, because both are inherited from
upstream and describe the upstream project.

## This is a fork

Pearwave is the Pearcache edition of **Airwave**
(`76696265636f646572/Airwave`) — a self-hosted shared radio: FastAPI backend
plus Vue 3/Vite frontend serving one live MP3 stream that all clients hear in
sync, fed from YouTube/SoundCloud/Mixcloud/Spotify links, consumable by
browsers and Sonos, with SendSpin for synchronized playback.

The fork is **light**. Of 263 commits, only the two most recent are
Pearcache-specific, and both are branding:
- `7934e2e` Add the Pearwave mark as an icon set
- `8469a41` Fix the icon paths — this app does not serve its build at the root

Everything else is upstream. Practical consequences:

- **`README.md` is upstream's** — its title, badges, screenshots, star-count
  shields and `docker run ghcr.io/76696265636f646572/airwave` image all point
  at the upstream project, not at anything Pearcache runs. Treat it as upstream
  documentation, not as a description of this deployment.
- **The icon set is the one thing that is genuinely ours.** When merging
  upstream changes, that is what to protect: the mark, the icon paths, and the
  fact that this app does **not** serve its build at the root.
- Before writing new features here, check whether they belong upstream instead.
  Divergence beyond branding makes future upstream merges expensive.

## Python version — get this right

`pyproject.toml` sets `requires-python = ">=3.12"`. That is authoritative, and
both the Dockerfile (`python:3.12-slim`, two stages) and GitHub Actions CI
(`python-version: "3.12"`) agree.

Two places disagree and are wrong:
- `.gitlab-ci.yml` pins `image: python:3.11`. `pip install ".[dev]"` on 3.11
  fails outright against `requires-python = ">=3.12"`. This file is inherited
  from upstream and is **not used** here — this repo builds on GitHub Actions —
  but do not treat it as a working reference.
- `README.md`'s Python badge and `AGENTS.md`'s Stack section both said
  "Python 3.10+". Corrected to 3.12+.

Use Python 3.12 for anything local.

## Quick reference

```bash
python3 -m venv .venv && source .venv/bin/activate
python -m pip install ".[dev]"          # needs Python 3.12
python -m pytest                        # tests/ — 20 test modules
scripts/run_dev.sh                      # builds frontend if needed, runs uvicorn
```

Frontend (`frontend/`): `npm run dev` / `npm run build` (Vite → `app/static/dist/`,
which FastAPI serves via `app/templates/index.html`).

Runtime binaries `yt-dlp`, `deno`, `ffmpeg`/`ffprobe` are fetched by the
`scripts/setup_*.sh` helpers rather than assumed present.

## Visibility

**This repository is public.** It currently contains no committed secrets
(verified: no `.env` tracked, none in history, no credential-shaped literals).
Keep it that way — `.gitignore` covers `.env` and the venvs. Anything
deployment-specific belongs in environment variables, not in the tree.
