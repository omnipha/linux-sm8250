# Plan

Limit 150 lines. Ordered steps only. No protocol text: results go into `records/`.

## Active step

- Step: Host-measure a rollback-safe stock restore baseline before any unlock
- Strategy: docs/strategy/stock-restore-baseline.md
- Done when: one merged record shows a rollback-safe stock baseline or a measured host-side reason it cannot exist

## Next

1. Device inventory: model, RAM/storage, Android/firmware version, bootloader state.
2. First Omnipha server image build (sshd key-only, USB network) via omnipha/images.
3. USB SSH acceptance, then WLAN autoconnect acceptance across reboot.

## Done

- Establish the first authorized channel to mb005 (fastboot identity or USB debugging) without writes: fastboot answers read-only getvar; product kona, slot a, unlocked no, secure yes, variant SM8 UFS (`records/2026-11-05-171422-fastboot-identity-readonly.md`).
