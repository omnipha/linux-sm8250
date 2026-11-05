---
date: 2026-11-05
issue: linux-sm8250#3
subsystem: boot
result: refuted
supersedes: 2026-11-05-192826-rescue-plan-stock-images-restore-path.md
---
# rescue plan correction

## Question

What in `records/2026-11-05-192826-rescue-plan-stock-images-restore-path.md`
(merged in PR #4, commit `3a23e8c`) is unsupported or unsafe, which facts are
now host-measured, and why does restore remain unverified? (linux-sm8250#3)

## What the merged record got wrong

1. **Archive status was hearsay.** It carried the archive check as "reported
   host-verified in shift state, not re-measured". That is not a measurement.
   Superseded by the host measurement below.
2. **Script claim was unresolved.** It said it was unverified whether
   `flash-all.bat` is inside the zip. Measured now: it is not (see below).
3. **A write procedure without a basis.** Step 5 outlined restoring
   `boot_a`, `dtbo_a`, `recovery_a`, `vbmeta_a` and the logical partitions in
   `super` via fastboot/fastbootd, plus "wipe cache/userdata if bootloop".
   None of those commands has been run or measured on mb005, the images are
   third-party, and no restore has ever been verified. Presenting it as a
   restore step invited a write before a verified restore, which the owner's
   gate forbids. It is withdrawn; see "Restore status" below.
4. **Layout over-read.** The table implied slot, role and filesystem facts.
   All of it comes from LineageOS build config, AOSP docs and a Lineage
   recovery fstab, none from mb005. Specifically unsupported: `recovery` as a
   stock "fastbootd entry" (stock recovery behaviour is unmeasured), and "A/B
   present" under measured facts (only `current-slot: a` was measured; A/B is
   inferred from it).
5. **Unsourced statement.** "`fastboot boot` generally needs an unlocked
   bootloader" had no source. It is unverified for this unit.
6. **PR text overstated checks.** The PR #4 description showed a
   verification block that `records.py check` does not print. Corrected in a
   PR comment; the real result is recorded here.

Not an error, for the avoidance of doubt: the merged record already treated
userdata loss as owner-accepted, kept relock forbidden, and listed no
flash as authorized. This record keeps those.

## Host-measured facts (this run, no device access)

Measured on the local copy of `in2023-590-fastboot.zip`; host path omitted.

- Size: 2,566,697,887 bytes; equals the SourceForge `best_release.json` value.
- MD5: `a31758dacb832404c4dab4741860f131`; equals the published value.
  SourceForge publishes no SHA-256; none is claimed.
- `unzip -tq`: no errors in compressed data.
- Entries: 34 = one directory (`IN2023_13.1.0.590(EX01) FASTBOOT IMAGE/`) and
  33 `.img` files. No script or other file is inside the zip.
   `flash-all.bat` is a separate SourceForge download (fetched as text, not
   executed): it flashes 32 images without `--slot`, skips `system_ext`,
   applies `--disable-verity` only to `vbmeta`/`vbmeta_system`, and runs
   `fastboot reboot fastboot` after six flashes. It is third-party and is
   forbidden by policy; it was not run.
- Images and sizes in bytes: abl 8388608, aop 524288, bluetooth 1048576,
  boot 100663296, cmnlib 524288, cmnlib64 524288, devcfg 131072, dsp 67108864,
  dtbo 25165824, featenabler 131072, hyp 8388608, imagefv 2097152,
  keymaster 524288, logo 16777216, mdm_oem_stanvbk 10485760, modem 536870912,
  multiimgoem 32768, odm 227540992, opproduct 314572800, product 6631424,
  qupfw 81920, recovery 78934016, spunvm 33554432, storsec 131072,
  system 725241856, system_ext 1103536128, tz 4194304, uefisecapp 2097152,
  vbmeta 65536, vbmeta_system 65536, vendor 964284416, xbl 5242880,
  xbl_config 131072. Names carry no `_a`/`_b`; no `xbl_lp5` or
  `xbl_config_lp5` is present.
- `vbmeta.img` and `vbmeta_system.img` start with the `AVB0` magic. Parsed
  header fields: vbmeta rollback index 0, flags 0x0; vbmeta_system rollback
  index 1717545600, flags 0x0. Only the header was parsed; no signature or
  hash descriptor was verified.
- Per-image SHA-256 values were computed on the host for a later comparison
  but are not published anywhere to compare against, so they prove nothing
  about origin and are not recorded here.

What these measurements do not show: that the images came from an
IN11BA-class unit, which slot they were read from, that they match mb005's
DDR type or rollback state, or that they restore a device.

## Images for this variant: status

1. Community archive above. Integrity of the download: host-measured.
   Origin: community extraction ("Extracted from Oneplus 8 Pro via adb from
   recovery", "Homebrew", uploader `edl-invoker`, 2025-03-07). Signing is
   unverified: the zip contains no signature file and no per-image checksums
   were found from any source. Fit to mb005 and restore
   viability: **unverified**.
2. Full OTA `IN2023_13.1.0.590(EX01)`, build
   `IN2023_11.F.73_2730_202406112246`: listed with MD5
   `2c929877e9643a9f37d548ad7debe4b3` in the XDA OxygenOS repo thread, whose
   author says the zips are signed full OTAs pulled from OnePlus servers.
   That is the index author's claim. **Not obtained**: a direct request
   returned HTTP 403 and the thread says these hosts have needed aria2 since
   September 2024. Checksum and signature unverified. An OTA is applied via
   recovery/local update and does not by itself give a fastboot restore path.
3. MSM/EDL: community packages exist only up to OxygenOS 11.0.4.4 (for example
   MD5 `7129972a08dee25d35d5b90a4edda64d` listed for one 11.0.4.4 package);
   none found for Android 13. Not obtained, signing and restore unverified.
   No verified EDL path was found for this unit; none is assumed.

## Partition map and slot layout

**Measured on mb005** (`records/2026-11-05-171422-fastboot-identity-readonly.md`):
`product: kona`, `current-slot: a`, `unlocked: no`, `secure: yes`,
`variant: SM8 UFS`. Android-side read-only observation: `IN2023`,
`13.1.0.590` (not a getvar measurement).

**Documented-only** (nothing below was read from mb005; sources are the AOSP
dynamic-partition and fastbootd pages, the LineageOS `instantnoodlep` wiki and
`BoardConfig*.mk`/`fstab.qcom`; Lineage describes what it builds, not the
stock layout):

- A/B in `AB_OTA_PARTITIONS`: `boot dtbo odm product recovery system
  system_ext vbmeta vbmeta_system vendor`.
- `super`: one physical container; `odm product system system_ext vendor` are
  dm-linear logical partitions inside it. Lineage build size
  `BOARD_SUPER_PARTITION_SIZE := 15032385536` (14 GiB) and group size
  7511998464. Build-time values, not measured sizes.
- `slotselect` in Lineage's fstab: `modem`, `dsp`, `bluetooth` and the logical
  partitions; none on `userdata`, `persist`, `misc`, `metadata`, `spunvm`.
- Lineage's firmware page flashes `spunvm` with `--slot=all` while its fstab
  mounts it without `slotselect`: unresolved conflict.
- `opproduct` is in the community zip but in neither Lineage list: role
  unknown.
- Never-write group by policy (no backup exists and a wrong write may brick;
  XBL is DDR-specific per Lineage, and mb005's DDR type is not measured):
  `xbl`, `xbl_config`, `abl`, `tz`, `hyp`, `aop`, `devcfg`, `keymaster`,
  `storsec`, `uefisecapp`, `qupfw`, `cmnlib`, `cmnlib64`, `featenabler`,
  `imagefv`, `multiimgoem`, `modem`, `dsp`, `bluetooth`, `mdm_oem_stanvbk`,
  `spunvm`, `logo`, `persist`, `modemst1`, `modemst2`, `misc`, `metadata`.
  Policy also forbids resizing or rewriting the dynamic `super` container,
  and requires the stock slot-A rescue partitions (`boot_a`, `vendor_boot_a`,
  `dtbo_a`, `vbmeta_a`) to stay intact.
- `persist`, `modemst1`, `modemst2` are device-unique and in no stock image.
  No known host read path exists while the bootloader is locked. Their loss
  is not recoverable by this plan.
- `userdata`: wiped by unlock (AOSP: the device should perform a factory data
  reset on `fastboot flashing unlock`); the owner has accepted that loss.
- Bootloader fastboot must refuse flash/erase of dynamic partitions (AOSP);
  those need userspace `fastbootd` (`getvar is-userspace`). Whether and how
  mb005 offers fastbootd is unmeasured.

## Restore status

No restore procedure is endorsed by this record. The step list below states
what each step is and what is known; none of them is authorized.

| Step | Status |
| --- | --- |
| Local archive size, MD5, zip integrity, entry list | host-measured (above) |
| Obtain and hash-check the full OTA, extract per-image hashes | not done; host-doable once a download works |
| Compare community images against OTA-derived images | not done; host-doable after the previous step |
| Read device-unique partitions (`persist`, `modemst1`, `modemst2`) | no known host path while locked; device-gated |
| Measure `getvar all`, `is-userspace`, `super-partition-name`, DDR type | device-gated, coordinator, read-only |
| Unlock (`fastboot flashing unlock` or `fastboot oem unlock`) | device-gated; wipes `userdata`; variant unverified; not authorized here |
| RAM-only `fastboot boot` of a test image | device-gated; owner order is boot test before any flash; needs-unlock is unverified |
| Any flash/erase of stock images to restore | unverified; **forbidden until a restore path is verified** |
| Relock | forbidden by the owner, never |

The gate stays: no flash before a verified restore. AOSP documents that
`flash` and `erase` require the unlocked state, so a restore cannot be
exercised before unlock. Whether the owner accepts unlocking with restore
unverified, using the boot-test-first order, is an owner decision (below);
this record does not make it. Accepting the archive as a baseline, or
approving an unlock, does not authorize any flash.

## Decisions still needed from owner/coordinator

1. Owner: is the community archive acceptable as the rescue baseline while
   unverified, or must the full OTA be obtained and cross-checked first?
2. Owner and coordinator: may unlock proceed with restore still unverified,
   given the boot-test-first order, and when? (Userdata loss is already
   accepted.)
3. Owner: approved payload carriers per
   `omnipha/agents/docs/partition-policy.md`; nothing is pre-approved here.
4. Coordinator: approve the read-only device measurements listed in the table
   (`getvar all`, `is-userspace`, `super-partition-name`, DDR type). No such
   approval exists yet.

## Consequence

This record supersedes `2026-11-05-192826-rescue-plan-stock-images-restore-path.md`
in full. Restore on mb005 stays **unverified**; layout stays
**documented-only**. `status.yaml` `next_question` now asks for the baseline
and unlock-order decisions. Boot, ssh and wifi stay `untested`. `PLAN.md` is
left to the mission owner. Issue linux-sm8250#3 stays open: its acceptance on
verified restore needs device measurements that are not host-doable.
