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
   Since this is **family-owned**, the legitimate path is to use the **family's Apple ID**. ⚠️
   **Do not attempt to circumvent Activation Lock.** If the family can't supply the Apple ID, the
   project pauses there — that's the honest boundary. (A passcode lock is *not* Activation Lock;
   the passcode is wiped by the normal restore.)
2. **iOS 6 itself — the central open question.** Apple **no longer signs iOS 6**, so a plain
   `idevicerestore` *cannot* install it. Getting iOS 6 onto an A5 in 2026 requires one of:
   - **saved SHSH blobs** for that exact iOS 6 build (almost certainly were never saved → likely dead end), or
   - an **A5 downgrade method** that doesn't need matching blobs. Historically: **powdersn0w**
     (xerub) booted custom-patched downgraded firmware on A5/A6 *without* matching blobs —
     tethered/semi-tethered, fiddly. **VERIFY before promising anything:** is iOS 6 a supported
     powdersn0w target? what's the current working toolchain on modern Linux? does any
     bootrom-level path (checkm8 lists A5–A11, but A5 tool support was historically experimental)
     apply? **Treat iOS 6 as research, not a plan, until a path is confirmed.**

   **Fallback that IS reachable:** iOS **9.3.6** + the **Phoenix** semi-untethered jailbreak
   (32-bit, 9.3.x). Less retro-pure than iOS 6, but a solid, low-risk jukebox base with real
   control. Good plan B if the iOS 6 path doesn't pan out.

## Suggested order of attack (next session)
1. **Assess the device.** Plug in over USB; `idevice_id -l` / `ideviceinfo` (may work even if
   screen-locked). Record current iOS version, ECID, and — crucially — **Activation Lock status**
   and whether the family has the Apple ID. *Decide go/no-go on gate #1 before anything else.*
2. **Install the toolkit** (the apt line above; Ben runs it).
3. **Baseline restore to iOS 9.3.6** (signed, safe) → get a clean, working, activated device
   first. This proves the Linux toolchain end-to-end and gives a known-good fallback state.
4. **Choose the retro target** with eyes open: (a) iOS 6 — only after the research in gate #2
   confirms a real path; (b) iOS 9.3.6 + Phoenix jailbreak — the reachable retro base.
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
