---
title: Architecture
description: System design and tech stack for cpac-trust-db.
order: 2
---

# Architecture

## Overview

```
CPAC client
  writes directly to Supabase (snapshots, tokens)
       ↓
  Supabase (Postgres)
  Compiled, queryable database
  REST API: qzhhsyucnlswmsvpssdh.supabase.co
       ↓
  CPAC client reads (GET advisories, snapshots, meta)
  Local cache at ~/.cpac/trust-db/
       ↑
  GitHub Actions (runs nightly)
  Reads from Supabase → commits updated TOML to repo
```

## Why This Stack

- **Supabase (Postgres)** — stable, mature, generous free tier, auto-generated REST API, row-level security handles public read / authenticated write cleanly. CPAC clients talk directly to Supabase — no proxy or intermediate server.
- **GitHub** — source of truth, fully auditable, human-readable TOML diffs on every advisory or snapshot change. GitHub Actions reads aggregated data from Supabase and commits updated TOML files on a schedule (nightly). One single commit per run, no overlap possible.

## Data Flow

1. **Snapshots** — CPAC clients POST directly to Supabase REST API
2. **Advisories** — Core team merges TOML to `main` → GitHub Actions upserts to Supabase
3. **Sync** — GitHub Actions runs nightly → reads from Supabase → commits TOML to repo
4. **Queries** — CPAC client hits Supabase REST API → reads data → caches locally

## No API Proxy

CPAC talks directly to Supabase at `https://qzhhsyucnlswmsvpssdh.supabase.co`. There is no custom domain proxy or中间 layer. The Supabase anon key is embedded in the CPAC client, which is safe because row-level security policies enforce public reads and rate-limited writes only.

A custom domain proxy (e.g. `thecinderproject.qd.je/cpac-trust-db/api/*`) was planned but not implemented — GitHub Pages is static and cannot proxy API requests. If a proxy is needed in the future (e.g. for API key rotation or request logging), it can be added as a Cloudflare Worker without changing the CPAC client architecture.

---

*Part of The Cinder Project*
