# The iOS 6 path — VERIFIED (2026-07-02)

Gate #2 from CLAUDE.md is resolved: **iOS 6.1.3 on the iPhone 4S is reachable today, from
Linux, untethered, with no saved SHSH blobs and no extra hardware.** The CLAUDE.md fear
("almost certainly a dead end without blobs") turned out to be wrong in the best way.

## The key fact that unlocks everything

Apple **still signs the iOS 6.1.3 OTA (over-the-air) update tickets for the iPhone 4S** — and
also iOS 8.4.1. These never stopped being signed. So a downgrade to 6.1.3 doesn't need hoarded
blobs at all; it uses signing Apple performs *today*. Because the firmware is genuinely signed,
the result boots **untethered** like any normal install — no computer needed at boot, ever.

## The tool: Legacy iOS Kit (LukeZGD)

- https://github.com/LukeZGD/Legacy-iOS-Kit — all-in-one shell script: restore, downgrade,
  OTA downgrade, jailbreak, SSH ramdisk. Actively maintained.
- **Linux is a first-class platform**: Ubuntu 22.04+ (this box is Noble 24.04 ✅), Fedora 40+,
  Debian 12+, Arch. Run `restore.sh`; first run fetches its own dependencies.
- iPhone 4S supported for: OTA downgrade to **6.1.3** and **8.4.1**, tethered restores to
  other versions, jailbreaking, SSH ramdisk.

## The confirmed route (three legs)

1. **DFU restore to iOS 9.3.6** — `idevicerestore --latest` or via Legacy iOS Kit. Wipes the
   passcode, proves the toolchain, gives the always-recoverable baseline. (Activation Lock
   gate is open and applies here — family Apple ID)
2. **Jailbreak 9.3.6** — Phoenix (semi-untethered, 32-bit 9.3.x); Legacy iOS Kit can sideload
   it from Linux. Needed only as a stepping stone: a jailbroken device can enter **kDFU mode**
   (software pwned-DFU, no exploit hardware required).
3. **OTA downgrade to iOS 6.1.3** — Legacy iOS Kit, from kDFU. Untethered. Done: linen, felt,
   and the real iPod app.

## Notes & alternates

- **checkm8-a5 (optional hardware path):** the A5 bootrom *is* checkm8-vulnerable, but needs a
  helper board — a **Raspberry Pi Pico (RP2040, ~$5)** is the recommended one (Arduino + USB
  Host Shield is the legacy option). Not required for our route (kDFU replaces it), but a fun
  ~$5 add-on that enables tethered boots of *any* iOS version and deeper experiments later.
  LIK's own advice: prefer jailbroken/kDFU when you can.
- **powdersn0w:** supports the 4S but **requires that device's iOS 7.1.x blobs** — we don't
  have them; irrelevant given the OTA route.
- **8.4.1 as middle ground:** also OTA-signed for the 4S; slightly more modern, still 32-bit
  jailbreakable. Our target stays 6.1.3, but it's good to know the shelf has two jars.
- Media-player use doesn't care about baseband/cellular; any baseband quirks from the
  downgrade are cosmetic for a jukebox.

## Sources

- [Legacy iOS Kit](https://github.com/LukeZGD/Legacy-iOS-Kit) · [README](https://github.com/LukeZGD/Legacy-iOS-Kit/blob/main/README.md)
- [Wiki: OTA Downgrade](https://github.com/LukeZGD/Legacy-iOS-Kit/wiki/OTA-Downgrade)
- [Wiki: How to Use](https://github.com/
