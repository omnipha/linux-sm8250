# Strategy dossier: stock-restore-baseline

Status: active
Step: Host-measure a rollback-safe stock restore baseline before any unlock
Keep this a concise current strategy, not a run log. Replace stale detail;
link records for the history.

## Measured

- Fastboot answers read-only getvar: product `kona`, current slot `a`, unlocked `no`, secure `yes`, variant `SM8 UFS` (`records/2026-11-05-171422-fastboot-identity-readonly.md`).
- The community `IN2023_13.1.0.590(EX01)` archive matches its published size and MD5 and contains 33 images. It is unsigned, has no independently comparable per-image hashes, and has never restored a device (`records/2026-11-05-193750-rescue-plan-correction.md`).
- The indexed full OTA and every MSM/EDL candidate remain unobtained. No stock partition or rollback value has been read from mb005.

## Current explanation

The indexed full OTA may give an independent image-identity comparison. Even a match proves neither a working restore nor device fit nor recovery of device-unique partitions. A failed step refutes only that path, not every possible baseline.

## Next single variable

Obtain the indexed full OTA and measure its published hash, signature, payload image identities, and comparison against the integrity-checked community archive. Stop when that comparison is recorded or the download/hash/signature path is recorded as unavailable. This authorizes no unlock, boot, flash, erase, or device read.

Dispatch: issue #6 is ready but unclaimed. The supervisor's selector excludes `linux-sm8250` and returns no candidates, so its queue gate keeps it from firing. An idle worker here is a routing gap, not a measurement. The 403 came from plain `curl`; the index says the hosts need `aria2`, so a first `aria2c` attempt is not a downloader-only retry.

## Budget and layer switch

The current layer is host artifact verification, not rescue documentation. Budget: one complete OTA acquisition and comparison attempt; no downloader-only retries. If it fails, record the exact unavailable step, not a universal impossibility. Fallback: return the unresolved restore and unlock risk to the owner as a genuine decision; do not unlock, flash, or boot a device as a substitute for this host measurement.

## Not this

- Rewriting the rescue plan or calling documented layout a measurement: closed by `records/2026-11-05-193750-rescue-plan-correction.md`.
- Treating the community archive's matching MD5 as stock provenance or restore proof: measured insufficient by the same record.
- Any unlock, `fastboot boot`, flash, erase, relock, or device read before this host result.

## Acceptance

The active step closes only when one merged record shows either a rollback-safe stock baseline or a measured host-side reason that no such baseline can exist.
