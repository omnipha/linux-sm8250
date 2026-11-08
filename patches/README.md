# SM8250 source patches

Source diffs for the OnePlus 8 Pro (instantnoodlep) against the local
display pin `9f87167f1` (`arm64: dts: qcom: sm8250-oneplus-instantnoodlep:
Enable display`), which sits on top of the packaged tag
`sm8250-7.2.0_git20261010` (`3c5440e9d`).

These are source diffs, not decompiled device-tree dumps, firmware blobs,
or device-specific runtime data. The GPU zap-shader line only names the
firmware file; the blob itself is not included.

## Order

`0002` touches drivers only and applies on its own. `0001` and `0003` both
edit `sm8250-oneplus-instantnoodlep.dts` from the same base, so they do not
apply with plain `patch -p1` one after the other. Apply `0003` first, then
`0001` with a 3-way merge, then `0004`:

    git apply patches/0003-oneplus-instantnoodlep-camera-dts.patch
    git apply --3way patches/0001-oneplus-instantnoodlep-enable-audio.patch
    git apply patches/0004-oneplus-instantnoodlep-gpu-fastboot.patch

`0004` enables the GPU with the measured zap-shader firmware name, sets the
PM8150 power-on mode values used to reach fastboot and recovery, and
disables the PM8009 power-on node. It was checked by compiling the
resulting device tree; it has not been booted from this patch.
