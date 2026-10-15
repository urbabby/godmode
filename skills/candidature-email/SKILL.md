---
name: candidature-email
description: Use when preparing, tracking, or sending job applications by email - declares the capability and delegates all drafting, validation, tracking and sending to the isolated tars-mail module, enforcing human-in-the-loop confirmation and mandatory guards.
---

# Candidature E-mail (Job Applications by Email)

## Overview

Reusable **capability declaration** (not an engine). All drafting, PDF checking,
tracking and sending is delegated to the isolated module **`~/projects/tars-mail/`**
(Python stdlib). This skill never speaks SMTP directly.

Prime directive: **prepare and propose; never send without explicit human confirmation.**

## When to Use

Use this skill when the objective involves:
- preparing a job-application e-mail (CV attached);
- tracking which applications were prepared or sent;
- sending an application **after explicit human approval**.

Do NOT use this skill for:
- reading/classifying inbound mail (use an e-mail parsing agent);
- automatic or mass sending (forbidden — the system is human-in-the-loop);
- any action that bypasses the module's guards.

## Preconditions

- Module present at `~/projects/tars-mail/` with `cli.py`.
- Candidate profile at `~/projects/tars-mail/data/profile.json` (gitignored).
- For a **real** send only: secrets at `~/.config/tars/mail.env`, mode `0600`.
- A target recipient address (company / job URL optional).

## Procedure

1. Check state: `python3 cli.py status`
2. Create (dry-run by default):
   `python3 cli.py draft --to <addr> --company "<company>" --position "<role>"`
   - refuses an invalid/missing PDF attachment (unless `--force`);
   - computes a non-binding `dedup_key`.
3. Let the human review: `python3 cli.py show <ID>`
4. Explicit human approval: `python3 cli.py approve <ID>`
5. Rehearsal (no network): `python3 cli.py send <ID>`
6. **Only after explicit human go-ahead:**
   `python3 cli.py send <ID> --real --confirm-ok`

## Tools and Permissions

- Tool: the `tars-mail` CLI via the shell/bash tool.
- Risk (SOURCE 1 §14 / §2.4): step 6 is **external / irreversible** → explicit human validation required.
- Steps 1-5 are read-only or local writes (safe).

## Mandatory Guardrails (enforced by the module, not optional)

- Dry-run is the default; sending is never automatic.
- Real send requires: `approved` status + `--confirm-ok` + secrets file **present** at `0600` (a missing file is refused explicitly — M7).
- Attachments validated (PDF magic bytes + size) at draft **and** send time.
- Recipient format-validated.
- Duplicates blocked once a real `sent` exists.
- Never reveal secret values; never paste credentials into the conversation.
- Never bypass a refusal (`rc=1`) — fix the input instead.

## Success Criteria

- The draft exists and the human reviewed it.
- Dry-run: an accurate preview with **zero** network access.
- Real send: the ledger records a `sent` entry for the correct recipient.

## Tests / Verification

- Module: `cd ~/projects/tars-mail && python3 tests/run_all.py` → 8/8.
- Guards: non-PDF draft → refused; `--real` without `--confirm-ok` → refused;
  secrets not `0600` → refused.
- This skill: validated by `node scripts/validate-skills.js --ci`.

## Error Handling

- A guard refusal (`rc=1`) is expected behavior, not a bug: fix the input, do not bypass.
- On SMTP failure the ledger records `failed`; retry only after review.
- If the module is missing, do not reimplement it: report and stop.

## Connections

- Router: `godmode:orchestrator-core`
- Safety: `godmode:security-protocol`
- Verification: `godmode:completion-gate`
- Memory: `godmode:knowledge-capture`
