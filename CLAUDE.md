# iphone4s-retro — Claude space

*Working title; Ben names things — rename freely.* A space to bring a **family-owned
iPhone 4S** back to life as a **retro media player**, ideally on **iOS 6**, doing all of it
**from Linux** (Ben hates Windows). This file orients the next session. It is written with the
Fi/Ti honesty discipline: **what's verified is marked verified; what's uncertain is marked
research; nothing is painted with confidence it hasn't earned.**

## The dream
Wipe the old passcode-locked 4S, put the skeuomorphic **iOS 6** era back on it (the classic
iPod app, the linen, the felt), and keep it as a dedicated retro jukebox / media player. This
is the modding/right-to-tinker ethos lived again — your device, your hands, retro by choice.
(It's the same spirit as the Cartograph project next door: own your machine, make it legible,
let the curious tinker.)

## Ground truth (verified, high confidence)
- **Device:** iPhone 4S = Apple **A5** chip, 32-bit. Shipped iOS 5; **max official = iOS 9.3.6**.
- **You do NOT need Windows or iTunes.** Linux has the full toolkit — the **libimobiledevice**
  suite. On this box `ideviceinfo` and `idevice_id` are **already installed**; the rest is apt:
  ```bash
  sudo apt install libimobiledevice-utils idevicerestore usbmuxd ideviceinstaller
  ```
  (apt is NOT passwordless in the agent sandbox — Ben runs this himself, or via `! sudo …`.)
  These cover backup, restore, factory-reset, recovery/DFU, and app/media management.
- **Factory-resetting a passcode-locked device is a normal restore, not a "bypass."** Put the
  4S into **DFU or recovery mode** (a hardware button combo that works regardless of the
  passcode), then restore a firmware image with `idevicerestore`. This wipes the passcode + data.
  The latest **signed** image (always restorable) is **iOS 9.3.6**.
- **It's hard to permanently brick a 4S.** A DFU restore to the latest signed iOS is the safety
  net — the "bootloop you can always un-loop." Experiments are recoverable. (Make experiments
  recoverable on purpose — that's the whole ethos.)

## The two real gates (one responsible, one hard research)
1. **Activation Lock (iCloud / Find My).** If the phone was signed into iCloud (iOS 7+ supports
   it), then *after* a restore it will demand the **original Apple ID + password** to reactivate.
   Provenance: it's a **cousin's old phone** (never used), passed to Ben via his **dad** —
   legitimate reuse, but Activation Lock, if present, follows the **cousin's** iCloud account. The
   legitimate path is the cousin supplying the Apple ID, or removing the device from their iCloud
   remotely. There's a real chance it was never signed in (find out at activation). ⚠️ **Do not
   attempt to circumvent Activation Lock.** If it's locked and the cousin's ID can't be had, the
   project pauses there — that's the honest boundary. (A passcode lock is *not* Activation Lock;
   the passcode is wiped by the normal restore.)
2. **iOS 6 itself — RESEARCH RESOLVED (2026-07-02): there is a real, no-blobs path.** Apple
   **no longer signs iOS 6**, so a plain `idevicerestore` *cannot* install it — but two facts
   change everything, both verified against **LukeZGD/Legacy-iOS-Kit** (actively maintained, runs
   on Linux — Ubuntu 22.04+/Debian 12+/Fedora 40+/Arch):
   - **iOS 6.1.3 is an "OTA-signed" build.** Apple still signs the 6.1.3 OTA blob for A5. That
     means iOS 6.1.3 is restorable **today, without saved SHSH blobs**, via the OTA-downgrade
     path. (The other OTA-signed A5 versions are 8.4.1 and 10.3.3.) This is the single biggest
     unlock — the old "blobs were never saved → dead end" fear does **not** apply to 6.1.3.
   - The old **powdersn0w** route also still exists (targets 5.0–9.3.5 on A5; needs iOS 7.1.x
     blobs for the 4S) — but we likely don't need it, since 6.1.3 is OTA-signed.
   - ⚠️ **The one catch — entering pwned DFU on A5.** A5 has no software-only checkm8; putting the
     4S into *pwned* DFU needs **either** (a) the device already jailbroken → use **kDFU** (no
     extra hardware; LukeZGD's explicit recommendation for A5), **or** (b) **checkm8-a5** external
     hardware: a **Raspberry Pi Pico** (RP2040 — *not* Pico 2/2W) + a micro-USB OTG-Y cable
     (~$5, the reliable option), or an ATmega Arduino + USB Host Shield (less recommended).
     → **Practical plan:** the current 4S almost certainly isn't jailbroken, so path (a) means
     *first* jailbreak wherever it currently sits, *then* kDFU → 6.1.3. If that's awkward, a
     ~$5 Pi Pico makes path (b) turnkey. **This is the next thing to pin down against the device's
     actual current iOS version.**
   - Result target: **iOS 6.1.3, untethered** once restored. Hacktivation is supported by the kit
     (matters for gate #1 if the family Apple ID is unavailable — but the responsible boundary on
     genuine **Activation Lock** still stands; hacktivation ≠ defeating someone's iCloud lock).

   **Fallback that is also reachable:** iOS **9.3.6** + the **Phoenix** semi-untethered jailbreak
   (32-bit, 9.3.x). Less retro-pure than iOS 6, but a solid, low-risk jukebox base — and note the
   jailbreak-then-kDFU route to 6.1.3 could actually *start* from a 9.3.6+Phoenix device.

## Suggested order of attack (next session)
1. **Assess the device.** Plug in over USB; `idevice_id -l` / `ideviceinfo` (may work even if
   screen-locked). Record current iOS version, ECID, and — crucially — **Activation Lock status**
   and whether the family has the Apple ID. *Decide go/no-go on gate #1 before anything else.*
2. **Install the toolkit** (the apt line above; Ben runs it).
3. **Baseline restore to iOS 9.3.6** (signed, safe) → get a clean, working, activated device
   first. This proves the Linux toolchain end-to-end and gives a known-good fallback state.
4. **Choose the retro target** with eyes open: (a) **iOS 6.1.3 — now a confirmed path** via
   Legacy-iOS-Kit's OTA downgrade (needs pwned DFU: kDFU off a jailbreak, or a ~$5 Pi Pico); or
   (b) iOS 9.3.6 + Phoenix jailbreak — the reachable retro base, which can itself be the launchpad
   into the kDFU→6.1.3 route.
5. **Make it a media player.** Load music/video (libimobiledevice / `ideviceinstaller`), theme
   it, consider a kiosk-ish always-on setup. (Details once the OS target is settled.)

## The fun thread Ben wondered about (a research/writing doc to come)
*"itunes worked in interesting ways back then, I wonder what the community of computer tinkerers
thought."* The 4S era (≈2011–2013) was **peak jailbreak**. iTunes was the walled garden everyone
routed around: the *sync* model (you didn't drag-drop files, you synced a library bound to one
computer; "Erase and Sync" ate libraries), iTunes-as-Windows-bloatware, the one-computer leash.
The scene's answer: **redsn0w, sn0wbreeze, limera1n (geohot), GreenPois0n/Absinthe (pod2g /
Chronic Dev), Cydia (saurik)**, the bootrom cat-and-mouse, **SHSH blob hoarding** (TinyUmbrella,
iFaith), and — the Linux hero — **libimobiledevice**, the open reimplementation of the iTunes
protocol so you never needed Apple's software at all. That community *is* the ancestor of this
project. Worth writing up as `docs/community-then.md` for the love of it.

## House notes
- Be honest about uncertainty (see iOS 6 gate). Don't ship a confident procedure you haven't
  verified — that's exactly the failure mode this project's whole spirit is against.
- Git here is **local-only** for now (no remote). Ben handles git automatically elsewhere; a new
  remote is a fresh publishing decision — ask before pushing this anywhere.
- Respect the responsible boundary on Activation Lock. Family device, legitimate reuse, real Apple ID.
