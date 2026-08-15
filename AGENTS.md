# SPKM Agent Guide

Read [CURRENT_STATUS.md](CURRENT_STATUS.md) before changing this repository. Use [REFERENCE.md](REFERENCE.md) for architecture and deployment details, and [INTERNAL_OPERATIONS.md](INTERNAL_OPERATIONS.md) for operational procedures.

- Active workspace: `C:\Users\burnk\OneDrive\Documents-assets\SPKM`
- Current checkpoint: `612128e`
- Portal Mode: `AUTO`

## Critical Invariants

- Treat January–August 2026 as legacy eBayar months. Native eBayar begins September 2026.
- Keep Portal Mode `AUTO` unless an administrator explicitly authorizes a temporary override.
- Native payment submissions remain subject to the current-month guard, 1–5 student limit, exact `studentKey` resolution, per-student duplicate checks, ScriptLock, one bulk payment write, post-write verification and no-retry handling for uncertain outcomes.
- Stable Native student identities are `KANAK:<BIL>` and `DEWASA:<BIL>`. Never expose MyKid/MyKad, phone, email or address as a browser selector identifier.
- Historical V2 rows with blank `STUDENT_ID` may require conservative normalized-name fallback. Never weaken this duplicate protection.
- Native payment rows use one `PAYMENT_GROUP_ID` across child rows. Group totals must be counted once, not once per child row.
- Receipt generation is independent of payment success, idempotent per group and must never trigger a second payment write.
- Bank slips remain private/restricted. Only the final receipt PDF may be shared as anyone-with-link/view; never share either folder or a temporary Doc.
- Never bypass month or payment guards merely to test before September. Use the development deployment and the controlled first-transaction checklist.

## Deployment Invariants

- `origin` is the development/source repository; `pages` is the production GitHub Pages repository.
- A push to `origin` does not update production Pages. Fetch and inspect divergence before `git push pages main:main`.
- `.clasp` tracks only `appsscript.json`, `Code.js`, `portal.html` and `TestWA.js`.
- `clasp push` updates Apps Script editor/source only. It does not change production behavior.
- After `/dev` approval, edit the existing active Web App deployment and assign `New version`. Preserve its existing production URL; do not create a new deployment unless explicitly intended.
- Never stage all files blindly. Review `git status --short` and stage only intended files.

## Current Safety State

- Git checkpoint: `612128e` (`feat: add native ebayar receipt generation`).
- GitHub Pages and both remotes were aligned at this checkpoint on 16 August 2026.
- Apps Script editor/source includes Native Phase 2A and 2B, but the existing active production Web App deployment has not been assigned a new version containing that source.
- No real Native payment, upload or receipt has been executed.
- Existing dirty files and local diff artifacts listed in [CURRENT_STATUS.md](CURRENT_STATUS.md) must be preserved unless a task explicitly places them in scope.
