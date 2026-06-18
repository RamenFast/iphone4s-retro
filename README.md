# iphone4s-retro

> Bring a family iPhone 4S back as a **retro media player** — ideally **iOS 6** — **entirely
> from Linux**. No Windows, no iTunes.

The short version of the good news and the honest news:

- ✅ **No Windows needed.** Linux's `libimobiledevice` + `idevicerestore` do everything iTunes
  did (backup, restore, factory-reset, recovery/DFU, media). Some of it (`ideviceinfo`,
  `idevice_id`) is already on the box.
- ✅ **Wiping the passcode is a normal restore**, not a hack — DFU/recovery mode + restore the
  latest signed firmware (iOS 9.3.6). It's the family's device; this is legitimate reuse.
- ⚠️ **Activation Lock** (if it was signed into iCloud) needs the **family's Apple ID** — the
  legitimate path, and the one boundary we don't cross.
- ❓ **iOS 6 is the dream but not yet a plan.** Apple stopped signing it; getting it onto an A5
  needs saved blobs (unlikely) or a special downgrade method (powdersn0w-class) that must be
  **verified** first. Reachable fallback: **iOS 9.3.6 + Phoenix jailbreak**.

Full orientation, the verified-vs-research breakdown, and the next-session plan: **[CLAUDE.md](CLAUDE.md)**.

*This is the right-to-tinker ethos, lived: your device, your hands, retro by choice — the same
spirit as the `cartograph` project. The 4S era was peak jailbreak, and `libimobiledevice` is the
open-source descendant of that scene. We're standing in a good lineage.*
