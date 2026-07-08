# Handoff — toward iOS 6.1.3 on the 4S

*Living next-session doc. Last updated: **2026-07-02**. Read [CLAUDE.md](../CLAUDE.md) first for
the full orientation; this file is the concrete, action-oriented state + plan.*

---

## Where we are (verified this session)

- ✅ **Linux toolkit complete.** `ideviceinfo`, `idevice_id`, `ideviceinstaller`, `usbmuxd` were
  already here; this session added **`idevicerestore`** and **`irecovery`** (via apt). No Windows,
  no iTunes needed.
- ✅ **Device seen and identified.** iPhone 4S confirmed on USB bus (`05ac:12a0`).
  - **UDID: `63ab386a053068b7fd52c3d17e5a1f594db87585`**
  - Plain `ideviceinfo` is blocked (`Could not connect to lockdownd: Password protected (-17)`)
    because the phone is passcode-locked/disabled — but **`ideviceinfo -s` (simple mode) works
    anyway** and gave up the whole identity (read 2026-07-02):
    - **iOS 8.3** (build `12F70`) — so it *was* updated past its shipped iOS 5 at some point.
    - **ECID:** `1404841926094` (decimal) = `147171A9DCE` (hex)
    - **Black** iPhone 4S, board `N94AP`, baseband `5.4.00`, Wi-Fi MAC `08:70:45:94:21:7b`
  - `ActivationState` still needs pairing → unreadable until unlock/wipe. The passcode itself is
    wiped by the restore; not a gate.
  - **Plan implication of iOS 8.3:** in-place jailbreak was never on the table anyway (the 8.3-era
    jailbreaks were Windows-only tools, and the passcode blocks it regardless) — Path A below
    stands unchanged.
- ✅ **iOS 6.1.3 confirmed reachable** — see CLAUDE.md gate #2. Short version: **iOS 6.1.3 is
  OTA-signed for the A5**, so it's restorable **without saved SHSH blobs** via
  [LukeZGD/Legacy-iOS-Kit](https://github.com/LukeZGD/Legacy-iOS-Kit). Untethered result.

## Chosen route: **Path A** (no extra hardware) — decided 2026-07-02

Getting an A5 into **pwned DFU** (required for the 6.1.3 downgrade) needs either kDFU (from a
running jailbreak) or `checkm8-a5` hardware. Because the phone is passcode-locked we can't
jailbreak it in place, so Path A **restores first**, then jailbreaks, then kDFUs:

> **DFU-restore → iOS 9.3.6** (wipes passcode) **→ jailbreak with Phoenix → kDFU → OTA-downgrade
> to 6.1.3 (untethered).** $0, all software, no Pi Pico needed.

*(Path B — a ~$5 Raspberry Pi Pico + micro-USB OTG-Y cable running `checkm8-a5` — was the
hardware alternative that pwns DFU directly regardless of the lock. **Not** chosen, but kept here
as a fallback if the Phoenix/kDFU chain gives trouble on this unit.)*

## Blocking gate — settle BEFORE any wipe

**Provenance:** Ben's **dad** passed it on — it's a **cousin's old phone**, never used, handed over
for this project. Legitimate reuse, clear chain. **But** the Activation Lock question follows the
*cousin's* iCloud account, not Ben's or his dad's.

**Activation Lock (iCloud / Find My).** Per Ben (2026-07-02): **the cousin's iCloud *was* signed
in** — and the phone is on iOS 8.3, which supports Activation Lock. So expect the post-restore
activation screen to demand the **cousin's Apple ID + password**. No device data needs preserving.

**Cleanest move — do this *before* (or independently of) the wipe:** have the cousin log into
[icloud.com/find](https://icloud.com/find), pick this iPhone under All Devices, and **"Remove from
Account"** (erasing it from there first if prompted). That clears Activation Lock server-side with
no passwords changing hands, and the restore then activates like a fresh phone. The alternative is
the cousin typing their Apple ID + password at the activation screen after the restore. Either is
fine; both are the legitimate path. (A passcode lock is *not* Activation Lock — the passcode is
wiped by the normal restore. The kit's "hacktivation" is about SIM/activation, **not** a way
around someone's iCloud lock — that boundary stands.)

## Next session — order of attack

1. **[Ben, offline] Clear the Activation Lock gate.** Cousin's iCloud *was* signed in (per Ben,
   2026-07-02), so either have the cousin **remove the device at icloud.com/find** beforehand
   (cleanest — see gate section above), or have them ready to enter their Apple ID at the
   activation screen post-restore. No device data needs preserving.
2. **Path A is chosen** — no hardware to buy. Proceed on software alone.
3. **Pull Legacy-iOS-Kit and check deps against this box** (Ubuntu-family; kit supports 22.04+):
   ```bash
   git clone https://github.com/LukeZGD/Legacy-iOS-Kit
   # first run of ./restore.sh fetches its own dependencies
   ```
4. **Baseline restore to iOS 9.3.6** (signed, always safe) to prove the toolchain end-to-end and
   get a known-good, activated fallback device. Enter DFU (below), then `idevicerestore -l` or via
   the kit.
5. **Reach 6.1.3** via the chosen path (A: Phoenix→kDFU; B: Pico checkm8-a5).
6. **Make it the jukebox** — load media (`ideviceinstaller` / kit), theme the skeuomorphic iOS 6
   home screen, consider a kiosk-ish always-on setup.

## Reference — enter DFU on iPhone 4S

1. Plug into USB.
2. Hold **Power (top) + Home together ~10 s**.
3. **Release Power, keep holding Home ~8 s.**
4. **DFU = totally black screen**, but the computer still detects it. Confirm from Linux with
   `irecovery -q` (shows ECID/CPID) or `lsusb` (Apple PID changes: `1281`=DFU/recovery-ish).
   - Apple logo = held Power too long, it rebooted → retry.
   - "Connect to iTunes" cable image = **recovery mode**, not DFU (still usable for a plain
     restore, but not for the pwned-DFU downgrade).

## Handy commands (already work on this box)

```bash
idevice_id -l                       # list UDIDs of connected devices (normal mode)
ideviceinfo -k ProductVersion       # read a key (blocked while device is passcode-disabled)
irecovery -q                        # query device in recovery/DFU (ECID, CPID, MODE)
lsusb | grep -i Apple               # see the device in ANY mode, incl. DFU
idevicerestore -l <ipsw|--latest>   # restore (use latest signed = 9.3.6 for baseline)
```

## Open items to re-verify next time

- **Re-confirm 6.1.3 is still OTA-signed** at restore time (historically stays signed for years,
  but check via the kit before committing).
- ~~**Current iOS version of the phone**~~ ✅ Resolved 2026-07-02: **iOS 8.3 (12F70)** via
  `ideviceinfo -s`, which works even while passcode-disabled.
- ~~**ECID**~~ ✅ Resolved 2026-07-02: `1404841926094` / hex `147171A9DCE` (same source).
