# AGENTS.md

## Cursor Cloud specific instructions

### Product

Single Python 3 CLI (`scripts/deals_channel.py`): fetch deals → filter → optional Cuelinks wrap → Telegram post → write `out/whatsapp_deals.txt`. No database, no Docker, no long-running server. See `README.md` for config modes.

### Dependencies

**Python 3 only** (stdlib). There is no `requirements.txt`, `pyproject.toml`, or virtualenv step.

### Commands (from repo root)

| Task | Command |
|------|---------|
| Tests | `python3 -m unittest discover -s tests` |
| Syntax check | `python3 -m py_compile scripts/deals_channel.py tests/test_deals_channel.py` |
| Dry-run (no Telegram) | `CUELINKS_CHANNEL_ID=... python3 scripts/deals_channel.py --config config/simple-cuelinks.json --dry-run` |

There is no configured linter (ruff/flake8/mypy) or build step in this repo; CI runs the script directly (`.github/workflows/run-deals.yml`).

### Local “run”

This app is a one-shot CLI, not a dev server. Use `config/simple-cuelinks.json` for offline E2E (manual feed items in JSON). Set `CUELINKS_CHANNEL_ID` when `affiliate.required` is true. Add `--skip-affiliate` to test without Cuelinks.

### External secrets (optional for full live E2E)

- `CUELINKS_CHANNEL_ID` — affiliate link wrapping
- `GOOGLE_SHEET_CSV_URL` — for `config/google-sheet-cuelinks.json`
- `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID` — live Telegram posting (omit `--dry-run`)

### Gotchas

- `affiliate.required: true` in configs fails the run if `CUELINKS_CHANNEL_ID` is unset (unless `--skip-affiliate`).
- Feeds in `config/deals.json` need per-merchant `*_FEED_URL` env vars or they are skipped.
- Output directory `out/` is created on first run; `out/whatsapp_deals.txt` is gitignored by convention (artifact in Actions).
