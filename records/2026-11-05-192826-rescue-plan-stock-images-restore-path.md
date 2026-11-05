---
date: 2026-11-05
issue: linux-sm8250#3
subsystem: boot
result: confirmed
---
# rescue plan stock images restore path

## Question

Which stock images, partition map and restore procedure would make an unlock of
mb005 recoverable, and which parts of that are still unverified? (linux-sm8250#3)

## Method

Host-only research against the measured identity from
`records/2026-11-05-171422-fastboot-identity-readonly.md`, public references
for OnePlus 8 Pro (IN2023 / `instantnoodlep` / `kona`), and live fetches of the
SourceForge project page, its `best_release.json`, LineageOS build config and
fstab, and AOSP fastboot/fastbootd documentation. No physical unlock, boot,
flash, erase or write was run. No device data, credentials, or private paths
are recorded here.

## Measured state vs documented-only facts

### Measured on mb005 (evidence: `records/2026-11-05-171422-fastboot-identity-readonly.md`)

- Product: `kona`
- Variant: `SM8 UFS`
- Slot layout: A/B present, active slot: `a`
- Bootloader lock state: `unlocked: no`, `secure: yes`
- Android identity (read-only observation before the fastboot reboot, not a
  fastboot getvar measurement): model `IN2023`, build `13.1.0.590`

### Stock and factory images

1. Community fastboot archive (local file `in2023-590-fastboot.zip`,
   published title `IN2023_13.1.0.590(EX01) FASTBOOT IMAGE.zip`):
   - Source: SourceForge project
     `https://sourceforge.net/projects/in2023-stock-oos13-fastboot/`
   - Published size: 2,566,697,887 bytes; published MD5:
     `a31758dacb832404c4dab4741860f131` (per `best_release.json`, fetched
     this run; `sha256sum` is null there).
   - Local archive status: [reported host-verified in shift state, not
     re-measured in this run] size and MD5 match the published values, ZIP CRC
     ok, 33 image entries. This record treats host artifact fit as
     [unverified] until a local re-hash and payload extraction happen in a
     recorded run.
   - Page description (uploader `edl-invoker`, 2025-03-07): "Extracted from
     Oneplus 8 Pro via adb from recovery", labelled "Homebrew". No slot of
     origin is stated; images carry no `_a`/`_b` suffix; no `xbl_lp5` or
     `xbl_config_lp5` variant is listed. Not OEM-signed; no per-image
     checksums are published by anyone.
   - The 33 listed images: `abl`, `aop`, `bluetooth`, `boot`, `cmnlib`,
     `cmnlib64`, `devcfg`, `dsp`, `dtbo`, `featenabler`, `hyp`, `imagefv`,
     `keymaster`, `logo`, `mdm_oem_stanvbk`, `modem`, `multiimgoem`, `odm`,
     `opproduct`, `product`, `qupfw`, `recovery`, `spunvm`, `storsec`,
     `system`, `system_ext`, `tz`, `uefisecapp`, `vbmeta`, `vbmeta_system`,
     `vendor`, `xbl`, `xbl_config` (each `.img`).
   - Separate `flash-all.bat` (1.5 kB, title "Mauronofrio Fastboot Rom
     Flasher", third-party template): flashes 32 of the 33 images, never
     flashes `system_ext.img`; uses plain partition names with no `--slot`;
     `--disable-verity` appears only on the `vbmeta`/`vbmeta_system` flashes;
     it runs `fastboot reboot fastboot` after the first six flashes, so
     everything after that (including `odm`, `opproduct`, `system`, `vendor`,
     `product`) is issued from fastbootd. Whether the same script is also
     bundled inside the zip is unverified.
   - Restore validity: [unverified] restore has never been executed on any
     device. A matching MD5 proves download integrity only, not fit to mb005
     (regional variant IN11BA vs crossflash, rollback indices, DDR type).
2. Full OTA package `IN2023_13.1.0.590(EX01)` (build
   `IN2023_11.F.73_2730_202406112246`):
   - Source: third-party index (XDA OxygenOS repo,
     `https://xdaforums.com/t/oneplus-8-pro-rom-ota-oxygen-os-repo-of-oxygen-os-builds.4084315/`),
     which states the files are signed full OTAs pulled from OnePlus/OPPO
     servers. That claim is the index author's, not verified here.
   - Published MD5 (same index): `2c929877e9643a9f37d548ad7debe4b3`
   - Linked download host: `gauss-componentotacostmanual-sg.allawnofs.com`
     (path `component-ota/24/06/12/`).
   - Local archive status: [not obtained] A direct request returned HTTP 403;
     the index notes downloads from these hosts have needed aria2 since
     September 2024. Checksum and signature unverified locally.
   - Limit: a full OTA is applied through recovery/local update; it does not by
     itself prove a fastboot/fastbootd restore path. Per-image hashes would
     come from its `payload.bin`, which has not been obtained.
3. MSM / EDL unbrick packages:
   - Source: no public official signed package found for OxygenOS 13.1 / F.73.
     Community-hosted EDL tools exist only up to OxygenOS 11.0.4.4 (XDA thread
     `op8pro-oos-11aa-ba-da-unbrick-tool-to-restore-your-device-to-oxygenos.4084953`);
     signing and restore unverified. OnePlus publishes no MSM downloads;
     service is via OnePlus support.
   - Local status: [not obtained / unverified]. No EDL path is assumed.
4. Image/DDR caveat: public LineageOS firmware docs state XBL is DDR-specific
   (`xbl`/`xbl_config` vs `xbl_config_lp5`/`xbl_lp5`) and flashing the wrong
   one bricks the phone. DDR type of mb005 is [not measured]. The community
   zip lists only `xbl` and `xbl_config`. XBL images are never to be written
   in this plan.

## Partition map and slot layout

No raw GPT dump has been read on mb005, so every entry below is
[documented-only] until measured on-device. Slot and group assignments come
from public LineageOS build config (`BoardConfigCommon.mk`,
`BoardConfig.mk`) and recovery fstab, not from mb005; they describe what
Lineage builds, not the stock layout. Grouping follows the partition policy
(`omnipha/agents` `docs/partition-policy.md`).

| Partition | Type / Group | Slot | Evidence status | Role in rescue plan |
| --- | --- | --- | --- | --- |
| `boot` | Physical boot image | A/B | [documented-only] | Stock rescue component; carrier use needs owner approval, not decided here |
| `dtbo` | Physical device tree blob | A/B | [documented-only] | Stock rescue component |
| `recovery` | Physical recovery image | A/B | [documented-only] | Stock recovery / fastbootd entry |
| `vbmeta` | AVB metadata | A/B | [documented-only] | Verified boot root hash |
| `vbmeta_system` | AVB system metadata | A/B | [documented-only] | Verified boot auxiliary |
| `super` | Dynamic container | Single physical | [documented-only] | Contains logical OS partitions |
| `system` | Logical (dm-linear) | Inside `super` | [documented-only] | Restorable only via fastbootd/recovery |
| `system_ext` | Logical (dm-linear) | Inside `super` | [documented-only] | Restorable only via fastbootd/recovery |
| `product` | Logical (dm-linear) | Inside `super` | [documented-only] | Restorable only via fastbootd/recovery |
| `vendor` | Logical (dm-linear) | Inside `super` | [documented-only] | Restorable only via fastbootd/recovery |
| `odm` | Logical (dm-linear) | Inside `super` | [documented-only] | Restorable only via fastbootd/recovery |
| `opproduct` | Present in community zip only | Unknown | [documented-only] | Unexplained: absent from Lineage dynamic-partition and AB_OTA lists; never write until resolved |
| `userdata` | Physical | Single | [documented-only] | Wiped on unlock; Linux-root use needs owner approval, not decided here |
| `metadata` | Physical | Single | [documented-only] | Dynamic partition metadata; never write |
| `misc` | Physical BCB | Single | [documented-only] | Bootloader command block / fastbootd entry; never write |
| `xbl` / `xbl_config` (+ `_lp5` variants) | Bootloader firmware | A/B | [documented-only] | Never write; DDR-specific (brick risk) |
| `abl` | Android Bootloader | A/B | [documented-only] | Never write |
| `tz` / `hyp` / `aop` | TrustZone / Hypervisor | A/B | [documented-only] | Never write |
| `modem` / `dsp` / `qupfw` / `storsec` / `bluetooth` | Firmware | A/B per Lineage fstab `slotselect` | [documented-only] | Never write; no stock-fit confirmed for mb005 |
| `spunvm` | Unresolved conflict | Unknown | [documented-only] | Lineage wiki flashes with `--slot=all` but fstab mounts without `slotselect`; never write until resolved |
| `persist` | Calibration / sensor data | Single physical | [documented-only] | Never write; cannot be restored from OTA |
| `modemst1` / `modemst2` | EFS / IMEI / RF nv | Single physical | [documented-only] | Never write; device-unique |

Build-config notes (documented-only, not mb005 measurements):
`AB_OTA_PARTITIONS` lists `boot dtbo odm product recovery system system_ext
vbmeta vbmeta_system vendor`; `BOARD_SUPER_PARTITION_SIZE` is 15032385536
with dynamic list `odm product system system_ext vendor`; recovery fstab sets
`slotselect` on logical `system system_ext product vendor odm` and on
`modem dsp bluetooth`, and no `slotselect` on `userdata persist misc metadata
spunvm`.

## Step-by-step restore procedure

Each step is a plan, not an authorization. Nothing here authorizes a flash,
erase, boot, or relock. Every flash or erase needs an unlocked bootloader in
both fastboot and fastbootd (AOSP), so this path cannot be exercised before
the unlock, and the unlock wipes `userdata`.

1. **Verify host artifacts before any physical action**
   - Re-hash the local fastboot archive, confirm size and MD5 against the
     published values, unpack and list the 33 entries in a recorded run.
   - Status: [unverified in this run] prior shift-state check reported a
     match but was not re-measured here; full OTA payload extraction is
     [not obtained].
2. **Back up device-unique state if a read path ever exists**
   - Partition reads (`persist`, `modemst1`, `modemst2`) stored outside the
     repository; `misc` handled as BCB only, never as payload.
   - Status: [device-gated / currently impossible] the bootloader is locked
     and no root or recovery read path exists; with no backup their loss is
     not recoverable by this plan.
3. **Bootloader unlock**
   - Command: `fastboot oem unlock` (or `fastboot flashing unlock`).
   - Phone prompts on-screen; wipes `userdata`; resets device state to UNLOCKED.
   - Status: [device-gated] Data loss accepted by owner; execution timing
     requires physical coordinator sign-off (decision below).
4. **RAM-only test boot (owner order: before any flash)**
   - `fastboot boot <image>` does not write storage. It generally needs an
     unlocked bootloader, so it follows step 3. No image exists yet.
   - Status: [device-gated], [unverified] on this unit.
5. **Conditional emergency restore (only what was written, only after unlock,
   never as a first step)**
   - Keep the unchanged stock rescue partitions on slot A intact; restore only
     partitions actually overwritten, from checksum-verified backups, never by
     wholesale reflash.
   - Bootloader fastboot: physical partitions that were written (for example
     `boot_a`, `dtbo_a`, `recovery_a`, `vbmeta_a`).
     Status: [unverified] sequence defined, never executed on mb005.
   - Fastbootd (`fastboot reboot fastboot`, `getvar is-userspace`): logical
     partitions inside `super` that were written (`system`, `system_ext`,
     `product`, `vendor`, `odm`).
     Status: [unverified] fastbootd reachability not measured on mb005.
   - Never execute any bundled script such as `flash-all.bat`; exclude all
     bootloader-chain images (`xbl*`, `abl`) and all firmware/EFS partitions
     from every restore command.
     Status: standing prohibition.
   - Wipe cache/userdata only if a bootloop occurs after a restore.
     Status: [unverified].
6. **Never relock the bootloader**
   - Status: owner rule on record. Public rationale [documented-only]: the
     LineageOS FAQ warns relocking can leave a device unusable. Not measured
     on this unit.

### Structural limits of this procedure

- Bootloader `fastboot` must refuse logical-partition flash/erase; that needs
  `fastbootd`, whose `is-userspace` / `super-partition-name` values are not
  measured on mb005.
- `persist`, `modemst1`, `modemst2` (EFS) are device-unique and are in no
  stock image. Their loss is not recoverable by this plan.
- Owner-reported one-time data backup exists; its content and completeness
  are not verified here.

## Exact owner / coordinator decisions still needed

Before any bootloader unlock can be initiated, the following decisions are
pending and cannot be decided autonomously:

1. **Acceptance of stock image provenance**: The owner must decide whether the
   community fastboot archive (third-party origin, MD5 checked for integrity
   only, restore never performed) is sufficient as the rescue baseline, or
   whether the third-party indexed full OTA (published MD5
   `2c929877e9643a9f37d548ad7debe4b3`, [not obtained]) must be fetched,
   hash-matched, extracted and its signature and image fit to mb005 confirmed
   first.
2. **Approval of unlock execution timing**: Owner has confirmed userdata loss
   is accepted at unlock; coordinator requires sign-off only on execution
   timing.
3. **Approval of payload carriers**: The owner must formally approve the
   carrier partition allocation (for example `userdata` as Linux root home,
   and which small boot carrier is used) per the partition policy. Nothing in
   this record pre-approves any carrier.
4. **Approval of non-writing test order**: Coordinator confirmation to execute
   `fastboot boot` (RAM-only test) before issuing any flash command to
   physical storage.

## References fetched this run

- `https://sourceforge.net/projects/in2023-stock-oos13-fastboot/` (33 names,
  "Extracted from Oneplus 8 Pro via adb from recovery", "Homebrew")
- `https://sourceforge.net/projects/in2023-stock-oos13-fastboot/best_release.json`
  (bytes 2566697887, MD5 `a31758dacb832404c4dab4741860f131`, `sha256sum` null)
- `https://xdaforums.com/t/oneplus-8-pro-rom-ota-oxygen-os-repo-of-oxygen-os-builds.4084315/`
  (IN11BA = IN2023 EU; F.73 OTA entry, MD5, aria2 note, persist-backup note)
- `https://xdaforums.com/t/op8pro-oos-11aa-ba-da-unbrick-tool-to-restore-your-device-to-oxygenos.4084953/`
  (EDL tools only up to OOS 11.0.4.4)
- `https://wiki.lineageos.org/devices/instantnoodlep/fw_update/` (22 firmware
  partitions, DDR-specific XBL brick warning, fastbootd entry rule)
- `https://github.com/LineageOS/android_device_oneplus_sm8250-common/blob/lineage-24.0/BoardConfigCommon.mk`
  and `instantnoodlep/lineage-23.0` `BoardConfig.mk` + `init/fstab.qcom`
  (AB_OTA list, super size, dynamic list, slotselect rows)
- `https://source.android.com/docs/core/architecture/bootloader/fastbootd`,
  `.../core/ota/dynamic_partitions/implement`, `.../core/architecture/partitions`
  (fastboot vs fastbootd, super/dm-linear, unlock wipe, slots)

## Consequence

The rescue plan and restore limitations are documented. All layout entries
remain `documented-only` until measured on-device. Boot, ssh and wifi
subsystems remain `untested`.
