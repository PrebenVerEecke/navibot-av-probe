# navibot-av-probe

Empty staging probe repository for the `staging.navibot.dev` AV-projects endpoint
research. This repo is intentionally minimal — its only role is to exist as a
public git target so we can later test what `staging.navibot.dev/api/av/projects`
actually does with the `githubRepo` field once a real project row is created.

## Why this exists

`staging.navibot.dev/api/av/projects` accepts unauthenticated POST/GET/DELETE
(per pentest sweep 2026-04-28). The schema includes a `githubRepo` field. We
want to understand whether the navibot-AV worker:

1. Clones the repo (passive read)
2. Executes any code from the repo (RCE primitive)
3. Reads/writes via the Meta GitHub App (cross-org access)
4. Stores the string only and never touches it (boring case)

This repository is the controlled bait: a known-clean, single-README repo we
can attach to a project row to observe behavior server-side without any
collateral risk to other repos under PrebenVerEecke/.

## Probe plan

- Create AV project: `POST /api/av/projects` with `githubRepo=PrebenVerEecke/navibot-av-probe`
- Watch GitHub clone events (Insights → Traffic → Clones)
- Watch GitHub webhook deliveries if any installed
- Watch this repo's referrer log for fetch User-Agent / source IP
- Optionally add a canary file with a unique token; check for replay

## Status

Repository created 2026-04-28. Not yet attached to any AV project row.

