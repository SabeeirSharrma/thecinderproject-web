---
title: Advisories
description: Maintainer-curated records of malicious or compromised packages.
order: 6
---

# Advisories

Maintained directly by The Cinder Project core team. Stored as TOML in the GitHub repo, synced to Supabase via GitHub Actions on merge.

## Format

```toml
[advisory]
package = "malicious-pkg"
severity = "critical"          # critical | high | medium | low
status = "confirmed"           # confirmed | suspected | resolved
reported = "2026-06-14"
updated = "2026-06-14"
reported_by = "The Cinder Project"
cve = ""                       # if applicable

[details]
summary = "PKGBUILD modified to execute remote script post-install"
description = """
On 2026-06-14, maintainer transferred ownership and introduced a
curl | bash call targeting an external IP.
"""
affected_versions = ["1.2.3-1", "1.2.4-1"]
safe_versions = []

[references]
urls = ["https://thecinderproject.qd.je/advisories/malicious-pkg-2026-06-14"]
```

## Submission

Community members report via GitHub issue or Discord. Core team reviews evidence and publishes. No automated advisory submissions.

## Trust Score Impact

The `severity` and `status` fields in the TOML advisory are evaluated independently. Severity applies a base penalty; status applies an additional adjustment on top. For example, `severity = "critical"` with `status = "suspected"` applies the critical penalty (-30) plus the suspected adjustment (-15) for a combined -45.

| Severity | Trust Penalty | Recommendation Floor |
|---|---|---|
| Critical | -30 | DANGER |
| High | -20 | WARNING |
| Medium | -10 | CAUTION |
| Low | -5 | No floor change |

| Status | Additional Adjustment |
|---|---|
| Confirmed | 0 (no change to severity penalty) |
| Suspected | -15 |
| Resolved | Reverts severity penalty to 0 |

---

*Part of The Cinder Project*
