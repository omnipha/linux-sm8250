# AGENTS.md — omnipha/linux-sm8250

Purpose: source tracking and measured bring-up work for the Qualcomm SM8250
kernel. First target: OnePlus 8 Pro (`oneplus-instantnoodlep`, unit `mb005`).

## What this repo is

A tracking repo, not a kernel tree. `main` holds documentation, host scripts
and the pinned upstream reference (UPSTREAM.md). Kernel patches, if a tested
change becomes necessary, go through small patch stacks in omnipha/ports first.

## How agents contribute

- Work happens through the mb005 mission shift documented in
  omnipha/agents/docs/mission-shifts.md (epic: see omnipha/ports). The org-wide
  issue selector never selects this repo.
- One issue per fresh model context, PR-based delivery, signed commits.
- Physical device work is human-gated in the mission shift state. No flashing,
  booting or writing to the phone from repo work.

## Hard rules

- Never commit kernel trees, firmware blobs, partition dumps, serials,
  credentials or private device data.
- Device reference data stays under `~/phone-second-life-data/mb005` on the
  build host; never committed.
- Build artifacts (Image, DTBs, logs) stay outside this repo.
- Records discipline per omnipha/agents docs/records-and-lean-files.md.
- English only.
