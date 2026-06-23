# Ghost Protocol

An **autonomous Superteam Earn agent** by [GreYat Labs](https://github.com/G-ojies).

Ghost Protocol registers as an Earn agent, discovers listings (the agent-eligible endpoint **and** the public bounty board), scores them by skill-keyword match, filters out the ones it can't usefully act on (closed, past-deadline, region-locked, non-code roles), and can submit work — leaving the human to claim payouts via a claim code.

It's a small, real-world example of the operational discipline an unattended agent needs: authenticated API calls, retries with exponential backoff, rate-limit handling, deduplication across sources, and guarded submission. (Those patterns, generalized to on-chain agents, became the [agent-ops-skill](https://github.com/G-ojies/agent-ops-skill).)

## Commands

```bash
python ghost_protocol.py register     # register a new agent identity
python ghost_protocol.py scan         # discover + score agent-eligible listings
python ghost_protocol.py show         # print the table from a previous scan (no network)
python ghost_protocol.py watch        # lightweight scheduled discovery (agent + public board)
python ghost_protocol.py heartbeat    # emit a status heartbeat
python ghost_protocol.py submit ...   # submit to a listing (guarded; --dry-run to preview)
```

Examples:

```bash
python ghost_protocol.py scan --min-score 3
python ghost_protocol.py submit --from-saved 2 --link https://github.com/me/pr --dry-run
python ghost_protocol.py submit --slug some-bounty --link https://… --info "…" --yes
```

## How it works

- **Discovery** — merges the authenticated agent listings endpoint with the public Superteam Earn board, dedupes by id.
- **Scoring** — keyword match against title/description/skills/slug/type.
- **Filtering** — drops closed/expired listings, region-locked listings outside the operator's eligible regions, and non-code roles (marketing/design/content/community) that have no code deliverable, unless an engineering signal overrides.
- **Submission** — builds the payload, answers eligibility questions, and submits (guarded behind `--dry-run` / confirmation by default).
- **Notification** — `ghost_watch.sh` runs `watch` on a schedule and notifies (desktop + `ghost_notify.py` Telegram/email) only when OPEN skill-matched listings appear.

## Configuration

Credentials and settings come from environment variables (or a `.env` file — **never committed**):

| Variable | Purpose |
|----------|---------|
| `GHOST_API_KEY` | Agent API key (from `register`) |
| `GHOST_CLAIM_CODE` | Claim code for payouts |
| `GHOST_AGENT_ID` | Agent id |
| `GHOST_REGIONS` | Comma-separated eligible regions (default `global,nigeria`) |
| `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID` | Optional Telegram alerts |
| `SMTP_*`, `ALERT_EMAIL_*` | Optional email alerts |

## Requirements

```bash
pip install -r requirements.txt
```

## License

MIT
