# SPKM — Current Development Status

> **LAST VERIFIED: 16 August 2026, approximately 00:33 Asia/Kuala_Lumpur**
>
> **START HERE when resuming development.**

This file is the primary continuity handoff. Historical plans and staging logs remain useful, but this checkpoint controls whenever they conflict with an older note.

## 1. Repository and Deployment State

- Workspace: `C:\Users\burnk\OneDrive\Documents-assets\SPKM`
- Branch: `main`
- HEAD: `612128ebdd935a54baa764a3553b99c995e72f66` (`612128e`)
- Commit message: `feat: add native ebayar receipt generation`
- Development/source remote: `origin` → `https://github.com/BurnDVS/SPKM-SyafieLegacy.git`
- Production Pages remote: `pages` → `https://github.com/shafielegacy/SPKM.git`
- Public PWA: `https://shafielegacy.github.io/SPKM`
- Both remotes were aligned at `612128e`. The production Pages remote was fast-forwarded from `9b5469b` to `612128e`.

Pushing `origin` does not update GitHub Pages. Before any future Pages push, fetch both remotes and inspect the commits on each side. Use an explicit `git push pages main:main` only after confirming a safe fast-forward.

## 2. Google Apps Script State

- Apps Script owner: `shafielegacykelasmengaji@gmail.com`.
- `.clasp` tracks exactly: `appsscript.json`, `Code.js`, `portal.html` and `TestWA.js`.
- Native eBayar Phase 2B source was pushed with clasp on 16 August 2026 at approximately 00:26:45; four files were pushed.
- This updated the Apps Script editor/source only; it did not change production behavior.
- The existing active production Web App deployment has **not** been edited and assigned a new version containing Phase 2A/2B.
- Only after the `/dev` deployment passes the controlled approval sequence below, edit that existing active Web App deployment and select `New version`. Keep its existing production URL. Do not create a separate deployment unless explicitly intended.

## 3. Public Safety State

- Production GitHub Pages frontend is at `612128e`.
- Apps Script editor/source is at the Phase 2B code checkpoint.
- The existing active production Web App deployment is still assigned to the earlier code version, at the same production URL.
- Portal Mode is `AUTO`.
- `AUTO` resolves to `LEGACY` before 1 September 2026 and to `NATIVE` from 1 September 2026 onward in Malaysia time.
- The public PWA was visually verified on 16 August 2026: legacy eBayar visible, August current, September–December marked `Akan Datang`, Native eBayar hidden under `AUTO`, and production remained operational.
- August dashboard snapshot: 68 paid, 117 unpaid, RM2,520.

## 4. eBayar V2 Migration and Reconciliation

January–August 2026 legacy history remains authoritative. The corresponding V2 shadow migration and validation are complete.

- January–July: legacy and V2 matched exactly with zero differences, including paid-name sets.
- July catch-up: 39 source groups, 60 child payment rows, RM1,870 appended. Final July preview: 71 scanned, 71 unchanged existing, 0 changed existing and 0 genuinely new.
- August catch-up through source row 47 / 15 August: 46 source groups, 69 child rows, RM2,520. Final August preview: all 46 existing and unchanged, 0 changed and 0 genuinely new.
- August reconciliation: 68 paid, 117 unpaid, 185 total students and RM2,520; all legacy/V2 differences zero.
- September legacy source is header-only. No September legacy migration is required at this checkpoint.
- Known historical anomaly: `GROUP_ID_MULTIPLE_STAGED_HASHES` for `PG-2026-JUN2026-112`. It was investigated and is unrelated to the July/August catch-ups.

Canonical `Payments` schema:

`PAYMENT_ID`, `PAYMENT_GROUP_ID`, `TIMESTAMP`, `TAHUN`, `BULAN`, `BULAN_KEY`, `NAMA_MURID_RAW`, `NAMA_MURID_NORM`, `STUDENT_ID`, `NO_MYKID_MYKAD`, `STUDENT_TYPE`, `JUMLAH`, `AMOUNT_TOTAL`, `AMOUNT_ALLOCATED`, `STATUS`, `KAEDAH`, `RESIT_URL`, `SOURCE_YEAR`, `SOURCE_SHEET`, `SOURCE_ROW`, `SOURCE_ROW_HASH`, `MATCH_STATUS`, `MATCH_CONFIDENCE`, `NOTE`, `CREATED_AT`, `UPDATED_AT`.

## 5. eBayar V2 Maintenance

The maintenance panel is available inside the authenticated Yuran admin area. UI access is admin-only and backend authorization remains authoritative.

The generic current-month sync engine:

- defaults to read-only preview;
- considers only genuinely new source groups;
- limits each batch to 25 groups;
- validates group ID, source location, source hash and secondary content fingerprint;
- uses ScriptLock, post-lock source reread and TOCTOU comparison;
- performs one bulk `setValues()` append with no partial-write loop;
- rechecks staging before write and verifies after write;
- must not be retried automatically after an uncertain write outcome.

Last August state: 46 existing groups, 0 new, 0 review, RM0 projected and status `Synced`.

## 6. Portal Mode

Allowed modes are `AUTO`, `LEGACY`, `NATIVE` and `BOTH`.

- Script Property: `EBAYAR_PORTAL_MODE`
- Missing or invalid value falls back to `AUTO`.
- Audit properties: `EBAYAR_PORTAL_MODE_UPDATED_AT` and `EBAYAR_PORTAL_MODE_UPDATED_BY`.
- Cutoff: `2026-09-01` in Malaysia time.
- Current configured mode: `AUTO`.
- `BOTH` was tested through `/dev` and then restored to `AUTO`.
- Legacy future-month cards remain visible but their payment buttons are disabled until the month becomes current.

## 7. Native eBayar Boundary and User Flow

- January–August 2026: legacy Google Form only.
- September–December 2026: Native eBayar, enabled only when the selected month is current or past under the server rule.
- No physical legacy future-month tabs are required for Native operation.
- One submission supports 1–5 students sharing one month, payment date, total amount, optional transaction reference and bank slip.
- One `PAYMENT_GROUP_ID` is written across one child row per selected student.
- If any selected student is duplicate or invalid, the entire group is rejected.
- The unpaid-only lookup is a UX filter; backend preflight and locked duplicate checks remain authoritative.
- Future-month submission remains blocked.
- Do not introduce or use a testing bypass merely to submit before September.

## 8. Native Student Identity

- Stable identities: `KANAK:<BIL>` and `DEWASA:<BIL>`.
- MyKid/MyKad is never used as the public selector value and must not be exposed to the browser.
- Students with identical names remain distinct because selection and resolution use `studentKey`.
- Native duplicate detection uses `STUDENT_ID`.
- Historical V2 rows with blank `STUDENT_ID` use conservative normalized-name fallback.
- If multiple official records make that fallback ambiguous, the submission is blocked for review.

## 9. Native Phase 2A — Payment Persistence

Checkpoint commit: `d171e8c`.

The submission endpoint preserves these guarantees:

- Portal Mode must resolve to `NATIVE` or `BOTH`.
- September lower boundary and future-month/date rules remain server-authoritative.
- Exactly 1–5 unique, current official `studentKey` values are required.
- Per-student duplicate validation occurs before and again under ScriptLock.
- One shared slip is limited to 3 MB raw data.
- Base64 length and whitespace are capped before decode; MIME, extension and file signature are validated after decode.
- Slip is uploaded privately, payment rows are appended in one `setValues()` call, and orphan files are cleaned up when safe.
- Post-write verification distinguishes confirmed success from uncertain outcomes; uncertain writes must not be retried without staging inspection.
- Native rows use `STATUS=SELESAI`, `KAEDAH=NATIVE_EBAYAR`, `SOURCE_SHEET=NATIVE_EBAYAR`, Native source metadata and stable `STUDENT_ID`.
- `MATCH_STATUS` and `MATCH_CONFIDENCE` are left blank because direct Native resolution is not a legacy migration match.
- `NOTE` stores compact structured JSON for group context.
- `AMOUNT_TOTAL` may repeat on child rows for schema compatibility, but all reports must deduplicate totals by `PAYMENT_GROUP_ID`.

No real Native write or slip upload has been executed.

## 10. Native Phase 2B — Receipt Generation

Checkpoint commit: `612128e`.

Implemented functions validate the payment group, create a canonical snapshot, populate receipt fields and generate the receipt. Operational guarantees:

- Payment persistence finishes and releases its payment lock before receipt work begins.
- Receipt generation uses its own lock.
- Exactly one final PDF is created per payment group.
- A temporary Google Doc is created privately, converted to PDF, then trashed.
- The final PDF is stored in the receipt folder and only that file is changed to anyone-with-link/view.
- Every child row in the group receives the same `RESIT_URL`.
- Repeated generation is idempotent when the group already has one consistent URL.
- Mixed or inconsistent receipt URLs return a review state rather than creating another receipt.
- Payment success remains independent: receipt failure never causes payment re-submission.
- Post-write uncertainty is reported explicitly and must not trigger an automatic second attempt.
- Receipts exclude MyKid/MyKad, phone, email, address, slip URL and internal hashes.

Frontend handling:

- `receiptReady: true` shows a receipt message and opens the receipt link with a new-window target and `noopener` protection.
- `receiptReady: false` reports that payment succeeded but the receipt is unavailable.
- Neither path retries the payment.

No real Native receipt has been generated.

## 11. Drive Folder Configuration

Slip folder:

- Script Property: `NATIVE_EBAYAR_SLIP_FOLDER_ID`
- Name: `SPKM - Native eBayar Slips`
- Must remain private/restricted and must never be link-shared.
- Verify the configured folder ID in Apps Script Script Properties; do not copy the value into tracked documentation.

Receipt folder:

- Script Property: `NATIVE_EBAYAR_RECEIPT_FOLDER_ID`
- Name: `SPKM - Native eBayar Receipts`
- Folder should remain Restricted; only the final receipt PDF receives anyone-with-link/view.
- Verify the configured folder ID in Apps Script Script Properties; do not copy the value into tracked documentation.

## 12. Git Checkpoints

- `f0af240` — completed July migration validation.
- `acfaafa` — added eBayar V2 maintenance panel.
- `da0b6df` — added guarded current-month Auto Sync.
- `d171e8c` — Native eBayar Phase 2A payment persistence.
- `612128e` — Native eBayar Phase 2B receipt generation.

All were present on `origin`; `pages` was aligned to `612128e` at the checkpoint.

## 13. Preserved Dirty Worktree

Last recorded status before this documentation task:

```text
 M CHANGELOG.md
 M TestWA.js
 M appsscript.json
 M sw.js
?? MASTER PROMPT SPDK — POST COURSE SECURITY HARDENING.txt
?? native-ebayar-phase1.diff.txt
?? native-ebayar-phase2a.diff.txt
?? native-ebayar-phase2b.diff.txt
```

The three `native-ebayar-*.diff.txt` files are local audit artifacts and must not be committed. `TestWA.js` and `appsscript.json` previously matched HEAD by content despite their line-ending state; do not restore them casually. Preserve all unrelated changes.

## 14. Next Session Checklist

Follow this order; do not skip directly to production:

1. Read this file, `REFERENCE.md` and `INTERNAL_OPERATIONS.md`.
2. Verify `git status --short`, current branch, commit and both remote tips.
3. Confirm `EBAYAR_PORTAL_MODE` is still `AUTO`.
4. Check `NATIVE_EBAYAR_SLIP_FOLDER_ID` and `NATIVE_EBAYAR_RECEIPT_FOLDER_ID` in Apps Script Script Properties, then verify the resolved folder names and sharing: both folders Restricted; slip files private; only final receipt PDFs link-view.
5. Open the Apps Script `/dev` deployment and verify the Phase 2A/2B UI and backend source are the intended checkpoint.
6. Do not add a month/date/testing bypass while August is still before the Native boundary.
7. On or after September, perform one controlled first Native transaction using a genuinely unpaid official student and a small valid slip.
8. Verify the group and every child row: stable `STUDENT_ID`, one group ID, correct child count, status, amount semantics, source metadata and no duplicate.
9. Verify the slip exists in the private slip folder and is not publicly shared.
10. Verify exactly one final receipt PDF, one common `RESIT_URL`, correct safe content and a trashed temporary Doc.
11. Re-run receipt generation/read path to confirm idempotency; do not create a second payment or receipt.
12. Only after `/dev` approval, edit the existing active GAS Web App deployment and assign `New version`, preserving its current production URL; then reverify the public PWA and backend behavior. Do not create a separate deployment unless explicitly intended.
13. Keep Portal Mode `AUTO` unless a documented administrator decision explicitly changes it.

## 15. Current Completion Boundary

Implemented and code-audited: V2 migration/reconciliation, guarded maintenance sync, Portal Mode, Native Phase 2A and Phase 2B.

Not yet complete: first real Native payment/upload/receipt test, `/dev` acceptance based on that real transaction, and assigning the existing active production Web App deployment a version containing the Native source. Parent Google login/dashboard work in the original V2 plan also remains future scope.
