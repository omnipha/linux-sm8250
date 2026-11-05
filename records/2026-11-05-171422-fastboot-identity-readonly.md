---
date: 2026-11-05
issue: linux-sm8250#1
subsystem: boot
result: confirmed
---
# fastboot identity readonly
## Question

Which authorized, non-writing channel does mb005 offer, and what identity does
it report? (linux-sm8250#1)

## Method

Physical-test coordinator, 2026-11-05, host-side, read-only: `adb reboot
bootloader`, read-only `fastboot getvar`, `fastboot reboot`. No write, unlock,
erase, boot or flash command was run.

## Measured (sanitized)

- product: kona
- current-slot: a
- unlocked: no
- secure: yes
- variant: SM8 UFS

Supplementary Android-side identity measured the same day before the reboot
(read-only): device IN2023, Android 13.1.0.590, bootloader locked - consistent
with the fastboot values above.

## Outcome

- A bootloader (fastboot) channel to mb005 exists and answers read-only getvar.
- Bootloader is locked and secure: no write path exists yet, by design.
- After `fastboot reboot` the phone returned to Android; USB enumerates as
  18d1:4ee8 (MIDI configuration), ADB not re-authorized yet (matches 2026-11-04
  observation).

## Consequence

Boot subsystem stays `untested` (no kernel of ours ran); only the
channel/identity knowledge changed. Per the issue's contrasting-results table
("Fastboot answers, unlocked=no"), the next variable is the rescue plan before
any unlock.
