# LeadFinder Local — Free Apollo Alternative

Private, local, no API keys. Find leads by plain English (“marketing agencies in Miami”, “fintech founders”), verify emails via free public data (syntax + MX/DNS + pattern inference + disposable/role checks), dedupe, score, and label **safe / risky / bad**, enrich your own CSV, draft personalized cold emails, and send with warmup, throttling & suppression.

## Features
- **Search:** `“marketing agencies in Miami”` → people, company, domain, verified email, LinkedIn, location.
- **Verification (free, no API):** `email-validator` + `dnspython` MX lookup, disposable & role detection, 8 pattern variants (`{first}.{last}`, `{f}{last}` etc.), confidence 0-100, score 0-100, labels safe ≥75 / risky ≥45 / bad.
- **Dedupe** by email (and name+company fallback).
- **Enrich CSV:** upload `first_name,last_name,company,domain` or `Name,Company` → adds best-guess email + verification + draft.
- **Cold email drafts:** personalized with company/vertical/city hooks.
- **Sending:** SMTP (Gmail App Password / Outlook / Mailpit), warmup ramp `5→200/day`, throttle delay, daily limit, suppression lists (`unsubscribed`/`replied`/`bounced`/`complained`), `List-Unsubscribe` header, dry-run mode.
- **Two interfaces:** Terminal CLI + Local Web Dashboard (privacy-first, all data in `./data`).

## Quick Start

```bash
cd leadfinder
pip install -r requirements.txt
python app.py        # → http://localhost:5000
```

### CLI
```bash
python cli.py search "marketing agencies in Miami" -n 25 -o leads.csv
python cli.py search "fintech founders" -n 50
python cli.py verify jordan@brightlinemedia.com
python cli.py enrich my_list.csv -o enriched.csv
python cli.py draft --first Jordan --company "Atlas Creative" --vertical marketing
python cli.py send enriched.csv --username you@gmail.com --password "app-pass" --dry-run
python cli.py send enriched.csv --username you@gmail.com --password "app-pass" --warmup-day 14 --throttle 45
```

### Web Dashboard
1. Type description → **Find Leads**
2. Filter by label / search / select
3. **Enrich CSV** tab → upload your file
4. Check **Drafts** → edit per-lead
5. Set SMTP → **Dry-run** then **Live send** (respects warmup & throttling, skips suppressed)

### Suppressions
Dashboard → Suppressions tab or API:
```bash
curl -X POST http://localhost:5000/api/suppressions -H "Content-Type: application/json" -d '{"email":"a@b.com","kind":"replied"}'
```

### API
- `POST /api/search` `{query, count}`
- `POST /api/verify` `{email}`
- `POST /api/enrich` (multipart CSV)
- `POST /api/draft` `{lead, sender_name, sender_company}`
- `POST /api/send` `{leads, smtp, dry_run}`
- `GET/POST /api/suppressions`
- `POST /api/export`

## How verification scores (no API keys)
- Syntax ✅, MX ✅ (+20), disposable ❌ (-40), role ❌ (-15), consumer catch-all (-10), pattern confidence (±15).  
- `safe` = score ≥75 + MX ok + not disposable; `risky` 45-74; `bad` <45 or no MX/disposable.

Data stays on disk: `data/suppressions.json`, `data/campaigns.json`. No external tracking.
# The_Mars_Trail
