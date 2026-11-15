# omarchy-m5

**[Omarchy](https://omarchy.org) on the 14-inch MacBook Pro with M5 Pro (J714s).**

We don't reinvent anything. A lot of people are getting Linux running on the M5 Macs, each
in their own repo. We collect the parts that already work, put them together in **one image
with one installer**, test it on a real J714s, and say who made each part.

**The image: [omarchy-j714s 7](https://github.com/betizzel/omarchy-m5/releases/tag/omarchy-j714s-7)** (prerelease): release 6 plus a boot guard
(the image refuses to let pacman replace its kernel, m1n1, U-Boot or firmware package with builds
that don't boot this Mac, [#11](https://github.com/betizzel/omarchy-m5/issues/11)) and the speaker switch-over fix.

**Send your M5 work here.** If you have something working on an M5 Mac, open a pull request
under your own name ([how](CONTRIBUTING.md)); keep your upstream pull request, and we drop our
copy when it merges there. Every release is an edge prerelease: it works on the hardware listed
as tested, and the known gaps are listed, not hidden.

---

## What works

Checked on one J714s (TripleU613's) on Apple's 27.2 firmware. On 2026-11-15 release 6 was
installed **from scratch** there: the installer shrank macOS, installed, Finish setup in Recovery
worked and the first boot came up on kernel v272-11. Release 7 is release 6 with one package
changed (firmware j714s12: the boot guard and the speaker switch-over timing). "Seen" and "heard" mean its owner looked or
listened; the display and speaker rows were seen and heard on release 5's kernel (v272-7,
2026-11-14) and have not been re-checked by eye or ear on the fresh install yet.

| Thing | Release 6 and 7 | Whose work |
| --- | --- | --- |
| Boots from the Mac's own disk, all 15 CPU cores at EL2 | ✅ | aurora-silicon, DjDeveloperr, rolotrealanis98, betizzel, RyanTheTide |
| Keyboard, backlight, trackpad | ✅ | rolotrealanis98, edimoldovan |
| Wi-Fi | ✅ | rolotrealanis98, betizzel, TripleU613, DjDeveloperr, edimoldovan |
| Bluetooth | 🟡 on; RFCOMM loads (for hands-free and headset profiles); headset audio not tried | same, chrisohearn (report) |
| GPU (Vulkan, the desktop renders on it) | ✅ | TripleU613, betizzel, DjDeveloperr, rolotrealanis98, joshuaswarren |
| Screen: full panel, 3024x1964 at 120 Hz | ✅ seen | FatBoy721, edimoldovan, rolotrealanis98, betizzel |
| Speakers, with speaker protection | ✅ heard at the first-start 30% (release 5's kernel); on release 6's fresh install the switch-over to speakerguardd v2 ran too early at first boot and passed when run by hand (fixed in 7: it waits for the speaker card; not yet seen through a first boot); nobody has played loud yet | DjDeveloperr (speakerguardd v2), Jens Axboe (27.2 AOP fix) |
| Microphones | 🟡 very faint | edimoldovan, kazuhidet |
| Headphone jack | untested on 27.2 | rolotrealanis98, edimoldovan |
| HDMI | ✅ seen on a Samsung Odyssey G9: 5120x1440 at 60/120/144 Hz, 3840x2160 at 60 Hz (release 5's kernel; not re-checked on 6 yet) | FatBoy721 |
| DisplayPort over USB-C, the left port that is not the DFU port | ✅ seen: 5120x1440 at 144 Hz, 10-bit (release 5's kernel; not re-checked on 6 yet) | DjDeveloperr, erikwb, this repo |
| DisplayPort over USB-C, right port | ❌ the link does not come up (640x480 fallback only) | open |
| DisplayPort over USB-C, left DFU port | ❌ nothing shows in Linux | open |
| Thunderbolt dock, three displays | untested (release 6 adds Isaac-Duarte's dark-display fix, #1930) | Isaac-Duarte |
| Front camera | 🟡 device up, tuning files collected from macOS at boot; no picture checked yet | edimoldovan, Jens Axboe, sithglan, Erik Bourget, DjDeveloperr |
| Neural Engine | 🟡 device up; no workload run | DjDeveloperr, Joshua Warren, Nick, this repo |
| Hardware video encode | ❌ probe only on 27.2 | DjDeveloperr, this repo |
| Hardware video decode | untested on 27.2 | DjDeveloperr |
| Touch ID | untested on 27.2 (worked on the earlier 27.3 build; writes the Secure Enclave store macOS shares, read [the warning](installer/recipes/aurora-sep-upstream/README.md)) | DjDeveloperr, aurora-silicon |
| Variable refresh on the panel | 🟡 offered to the compositor; not checked by eye | rolotrealanis98 |
| Firewall (`ufw`) | ✅ | this repo, chrisohearn (report) |
| Sleep | ❌ no deep sleep; lid close untested on 27.2 | open |
| Fans | ✅ Linux can set fan targets (tested: target 3000 gave 2989 rpm) | Isaac-Duarte (#1944) |
| Sustained CPU load | ✅ the clusters follow the fast-die limit; the die held 107–109 C with 15 busy cores | DjDeveloperr (#1950) |
| Updates (`omarchy update`) | ✅ checked in CI ([`update-check`](.github/workflows/update-check.yml)) | this repo |
| Two Omarchy installs on one disk | ✅ each mounts its own boot partitions ([why](docs/duplicate-boot-ids.md)) | this repo |

✅ works · 🟡 partly · ❌ no

Also known: every boot pauses about 15 seconds at the logo, and a DisplayPort cable plugged in
at boot was once not detected until it was plugged in again.

---

## Try it

It runs on a Mac, not from a Linux PC. You need:

- A **MacBook Pro 14-inch, M5 Pro** (J714s). Not M5 Max, not the 16-inch, not yet.
- **macOS 27.2 or newer** on it, FileVault on, an administrator account.
- About **35 GB free** on the Mac's disk, the charger, and about an hour.

Steps (the [release page](https://github.com/betizzel/omarchy-m5/releases/tag/omarchy-j714s-7) has every detail):

1. **Download** the installer package from the release (online: small; offline: the `.partN`
   files joined) and check it against `SHA256SUMS`.
2. **Install and run** Omarchy Installer. It shrinks macOS, adds a new startup disk (Apple's 27.2
   firmware, downloaded from Apple) and writes Omarchy.
3. **Finish setup in Recovery:** shut down, hold the power button, pick the **Omarchy with the
   logo icon**, sign in, click **Install** in Omarchy's own "Finish setup" window and wait (up to
   30 minutes). Don't use Startup Security Utility.
4. **Restart.** First boot asks for a disk password, then your name and password.

**To go back to macOS:** hold the power button at startup and pick macOS.

**Before turning the speakers up,** follow the low-level test in
[the speaker recipe](installer/recipes/speakerguardd-v2-upstream/README.md).

Stuck? Open an [issue](https://github.com/betizzel/omarchy-m5/issues) and paste what you see.

Earlier releases (1 to 6) and the older developer images are superseded; what each one was and
why it was replaced is in [`docs/status-and-history.md`](docs/status-and-history.md). What is
being sent upstream, and what waits on a test first, is in [`docs/upstream.md`](docs/upstream.md).

---

## K114s (14-inch OLED, M5 Pro): experimental, tested on one machine

The 14-inch MacBook Pro with the OLED touchscreen (Mac17,13, K114s, `apple,k114s`, board 0x14, same
T6050 chip as the J714s) is admitted by the installer's developer build
([`installer/patches-j714s-r2/0009`](installer/patches-j714s-r2), for
[#10](https://github.com/betizzel/omarchy-m5/issues/10)). It was tested on one K114s, by its owner
(chrisohearn), on Apple's 27.2 stub firmware (build 26B8125). Nobody else has run it, and the
maintainers here have no K114s: this repo's images and releases are J714s only.

- **There is no K114s image here.** The K114s engine entry accepts only an image built for the
  `k114sap` device class with its own Stage 1 members and Stage 2; the J714s image is refused on a
  K114s. Admission alone lets a developer build of the installer accept such an image; this repo
  does not build one.
- **What admission alone gives** is what this image gives on a K114s: a **keyboard-only** system.
  Installed from an installer built with the equivalent earlier patches on release 3 (release 3's
  image repacked with the K114s members), it booted to the Hyprland desktop on the encrypted disk and the keyboard worked
  (owner-observed on the trial install). Without the extra pieces below there was no trackpad, no
  Wi-Fi or Bluetooth (upstream's K114s device tree keeps the PCIe host off), no sound card, and the
  display was the boot framebuffer only. The owner saw the missing pointer; the log lines
  (including the `magicmouse` probe failure, listed in
  [#10](https://github.com/betizzel/omarchy-m5/issues/10)) and the missing sound card were
  agent-run on later boots of the same release-3 kernel.
- **What the K114s needs on top** is in
  [chrisohearn/omarchy-m5p-oled](https://github.com/chrisohearn/omarchy-m5p-oled): its K114s device
  tree, the trackpad patch ([aurora-silicon/linux#1568](https://github.com/aurora-silicon/linux/pull/1568),
  a draft written for the K114c), and the update guards that stop a package update from replacing
  the K114s boot files. With them (its bundle r2c) the same machine also showed the trackpad
  (owner-observed) and Wi-Fi associated and carrying traffic (agent-run; 6 GHz not tried); later,
  with that repo's separate r3-gpu device tree, the GPU rendering the desktop (owner-observed
  desktop; agent-run renderer `Apple M5 Pro (G17S B1)`). Bluetooth powers on (agent-run), nothing paired. Its notes list what else was and was not tried; sound, the native panel
  mode and brightness, sleep, USB-C data and external displays are not working or not tried.
- **How far to trust it:** one machine, one boot chain, firmware 27.2 (26B8125) only; the
  admission patch and its tests were built against this repo's current set, but no installer or
  image has been built from it here, and nothing was run on release 7. Treat it as a developer
  board, not a supported one. K114s owners: please start with the notes in that repo, and report
  results in [#10](https://github.com/betizzel/omarchy-m5/issues/10).

---

## Who we collect from

These are the people and repos we watch. A script ([`tools/watch/watch.sh`](tools/watch/watch.sh))
checks all of them every 30 minutes for new commits, and anything new that works on a J714s
gets pulled in here.

### M5 Mac owners doing the bring-up

| Who | Mac | Their repo | What we took |
| --- | --- | --- | --- |
| [rolotrealanis98](https://github.com/rolotrealanis98) | 16" M5 Pro (J716s) | [m5pro-linux](https://github.com/rolotrealanis98/m5pro-linux), [m4-m5-linux](https://github.com/rolotrealanis98/m4-m5-linux) (status tracker) | the T6050 kernel base, audio, many fixes |
| [edimoldovan](https://github.com/edimoldovan) | 14" M5 Max (J714c) | [linux-aurora](https://github.com/edimoldovan/linux-aurora), [omarchy-mac-pkgs](https://github.com/edimoldovan/omarchy-mac-pkgs) | trackpad reset, speaker safety + sound profiles, 6 GHz Wi-Fi, quiet idle fans |
| [DjDeveloperr](https://github.com/DjDeveloperr) | 14" M5 Pro (J714s) | via [aurora-silicon/linux](https://github.com/aurora-silicon/linux) and [aura](https://github.com/aurora-silicon/aura) | the upstream J714s device tree: USB-C, USB4, displays, GPU handoff (testing now) |
| [betizzel](https://github.com/betizzel) | 14" M5 Pro (J714s) | this repo | the first J714s boot, display, GPU |
| [TripleU613](https://github.com/TripleU613) | 14" M5 Pro (J714s) | this repo | Wi-Fi, GPU, the installer image |
| [FatBoy721](https://github.com/FatBoy721) | 14" M5 Pro (J714s) | | lid sleep fixes; first install by someone else (#293) |
| [cameroncooper](https://github.com/cameroncooper) | 16" M5 Max (J716c) | | watched (Aura stage 1, accelerated Hyprland on J716c) |
| [joshuaswarren](https://github.com/joshuaswarren) | | | J714c PMP and USB-C upstream, PMP analysis in #293 |
| [sithglan](https://github.com/sithglan) | K116s (M5 Pro/Max) | via [aurora-silicon/linux](https://github.com/aurora-silicon/linux) and [aura](https://github.com/aurora-silicon/aura) | watched: K116s bring-up on 27.2, camera hand-off (aura #188) |
| [erikwb](https://github.com/erikwb) | | | T6050 USB4 and external displays upstream (J716c) |

### The Omarchy-on-Mac team ([omacom](https://github.com/omacom))

[maralcbr](https://github.com/maralcbr) (Marcelo Alcantara) ·
[scottjones](https://github.com/scottjones) (Scott Jones) ·
[chriskearney](https://github.com/chriskearney) (Chris Kearney) ·
[malik-na](https://github.com/malik-na) (Naeem Malik) ·
[DjDeveloperr](https://github.com/DjDeveloperr) (Dj) ·
[joshuaswarren](https://github.com/joshuaswarren) (Joshua Warren) ·
[themartiano](https://github.com/themartiano) (Eduardo Martinez) ·
[riverscn](https://github.com/riverscn) (Shun Li) ·
[RyanTheTide](https://github.com/RyanTheTide) (Ryan Murray) ·
[Acelogic](https://github.com/Acelogic) (Miguel Cruz) ·
[LiamRay11](https://github.com/LiamRay11) (Liam) ·
[erykwieliczko](https://github.com/erykwieliczko) (Eryk Wieliczko) ·
[subedijaidip](https://github.com/subedijaidip) (Jaidip Subedi) ·
[shawnyeager](https://github.com/shawnyeager) (Shawn Yeager) ·
[jondkinney](https://github.com/jondkinney) (Jon Kinney) ·
Randy (GitHub name unknown: tell us)

Also watched: [satchlj](https://github.com/satchlj), [joske](https://github.com/joske).

### M4 neighbours (one generation older, fixes often carry over)

| Who | Mac | Their repo |
| --- | --- | --- |
| [IronsideXXVI](https://github.com/IronsideXXVI) | 14" M4 (J604) | [omarchy-j604](https://github.com/IronsideXXVI/omarchy-j604): 120 Hz 10-bit, all 6 speakers, video decode |
| [jgoyvaerts](https://github.com/jgoyvaerts) | 16" M4 Pro (J616s) | [omarchy-m4](https://github.com/jgoyvaerts/omarchy-m4) |
| [kazuhidet](https://github.com/kazuhidet) | M4 Air 15" (J713) | [Omarchy-M4-Native](https://github.com/kazuhidet/Omarchy-M4-Native) |
| [andreinita21](https://github.com/andreinita21) | M4 Mac mini | [OmarchyM4Mini](https://github.com/andreinita21/OmarchyM4Mini) |
| [fbalazsy](https://github.com/fbalazsy) | 16" M4 Pro (J616s) | [j616s-omarchy-m4-tutorial](https://github.com/fbalazsy/j616s-omarchy-m4-tutorial) |
| [Blueemi](https://github.com/Blueemi) | M4 Pro | [omarchy-dots](https://github.com/Blueemi/omarchy-dots): macOS-like trackpad and scrolling |
| [pixbroker](https://github.com/pixbroker) | 16" M4 Pro | 120 Hz GPU desktop, sound, sleep (posts) |

Posting on X but not found on GitHub: StudioXRadio (M4 Air installer), jefehollis (M4 Air GPU fix),
thellmgenius (M4 Air Thunderbolt monitor). Know their GitHub names? Tell us.

### Sent upstream from this repo

| Change | Where | Status |
| --- | --- | --- |
| T6050 PCIe: let the auto-PM storage domain gate itself (boot hang / missing Wi-Fi) | [aurora-silicon/aura#203](https://github.com/aurora-silicon/aura/pull/203) | merged 2026-11-13 |
| drm/apple: soft-depend on the T6050 combo PHY module (USB-C external displays) | [aurora-silicon/linux#1745](https://github.com/aurora-silicon/linux/pull/1745) | merged 2026-11-13 |
| AVE: T6050 encoder on the 27.3 firmware | [aurora-silicon/linux#1747](https://github.com/aurora-silicon/linux/pull/1747) | merged 2026-11-13 |
| ANE: T6050 on the 27.3 firmware | [aurora-silicon/linux#1746](https://github.com/aurora-silicon/linux/pull/1746) | open |

### Repos we watch

| Repo | What it is |
| --- | --- |
| [aurora-silicon/linux](https://github.com/aurora-silicon/linux) | the Apple Silicon kernel everyone lands work in |
| [aurora-silicon/aura](https://github.com/aurora-silicon/aura) | the bootloader (m1n1 fork) |
| [aurora-silicon/mesa](https://github.com/aurora-silicon/mesa), [u-boot](https://github.com/aurora-silicon/u-boot) | graphics drivers, U-Boot |
| [omacom/linux-aurora](https://github.com/omacom/linux-aurora), [m1n1](https://github.com/omacom/m1n1) | Omarchy's kernel and bootloader |
| [omacom/omarchy-mac-pkgs](https://github.com/omacom/omarchy-mac-pkgs), [omarchy-mac-installer](https://github.com/omacom/omarchy-mac-installer) | Omarchy's Mac packages and installer |
| [omacom/linux-aurora#293](https://github.com/omacom/linux-aurora/issues/293) | the thread where M5 owners post results |
| [omarchyarchive.com](https://omarchyarchive.com/search/?q=M5) | community posts about the M5 Macs (X, blogs) |

Plus every repo the people above push to, and a GitHub-wide search for `t6050`, `j714s`,
`j714c`, `j716s`, `m5 pro linux`, `m5 max linux`. Know someone we missed? Open an issue.

---

## Help out (dev)

You don't need to be a kernel hacker. The easiest ways to help:

- **Got a J714s?** Try the image and tell us what works and what doesn't.
- **Found someone's M5 work that we don't have?** Open an issue with the link. That's the whole job here.
- **Want to watch the repos yourself?** `tools/watch/watch.sh` (needs `gh` logged in). The first
  run just records where everything is; after that it prints what changed.

Want to build it yourself? The kernel and bootloader patches are in [`patches/`](patches/), the
image recipe in [`installer/`](installer/README.md), and the full build guide is
[`docs/bringup.md`](docs/bringup.md). What we know of but left out on purpose, and why: [`docs/left-out.md`](docs/left-out.md). Everything detailed (status history, how each piece was
found, the old notes) lives in [`docs/status-and-history.md`](docs/status-and-history.md).

---

Built on [Asahi Linux](https://asahilinux.org), [Aurora Silicon](https://github.com/aurora-silicon)
and [omacom](https://github.com/omacom). [MIT](LICENSE) for this repo's own scripts and docs;
patches keep the license of the project they apply to. No Apple firmware is shipped here.
