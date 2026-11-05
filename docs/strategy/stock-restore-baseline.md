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

A rollback-safe stock baseline can be established on the host by obtaining the indexed full OTA and comparing its image identity with the community archive. A failed or unavailable download, hash, signature, or comparison refutes that explanation.

## Next single variable

Obtain the indexed full OTA and measure its published hash, signature, payload image identities, and comparison against the already verified community archive. The issue stops when that comparison is recorded or the download/hash/signature path is recorded as unavailable. It authorizes no unlock, boot, flash, erase, or device read.

## Budget and layer switch

The current layer is host artifact verification, not rescue documentation. Budget: one complete OTA acquisition and comparison attempt. If the OTA cannot be obtained or verified, stop repeating it. Fallback: record that no rollback-safe host baseline exists and return the owner decision on unlocking with an unverified community archive. Do not use another device boot as a substitute for this host measurement.

## Not this

- Rewriting the rescue plan or calling documented layout a measurement: closed by `records/2026-11-05-193750-rescue-plan-correction.md`.
- Treating the community archive's matching MD5 as stock provenance or restore proof: measured insufficient by the same record.
- Any unlock, `fastboot boot`, flash, erase, relock, or device read before this host result.

## Acceptance

The active step closes only when one merged record shows either a rollback-safe stock baseline or a measured host-side reason that no such baseline can exist.
