# Plan

Limit 150 lines. Ordered steps only. No protocol text: results go into `records/`.

## Active step

- Step: Establish the first authorized channel to mb005 (fastboot identity or USB debugging) without writes
- Issue: omnipha/ports#MB005-EPIC (see link in README)
- Done when: serial/slot/unlock or adb identity measured on the physical unit

## Next

1. Device inventory: model, RAM/storage, Android/firmware version, bootloader state.
2. Rescue plan: stock images, partitions, verified restore path before any write.
3. First Omnipha server image build (sshd key-only, USB network) via omnipha/images.
4. USB SSH acceptance, then WLAN autoconnect acceptance across reboot.

## Done

- (none yet)
