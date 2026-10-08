# A67LG2 Sus Enabler

Apply susfs at boot to line up the bootloader lock-state and hide the root infrastructure.

A KernelSU Next module for the **Foxxd A67L Gen 2**.

The phone's `getprop` lock-state is already factory-green, but detectors read the
real values from `/proc/bootconfig` and the kernel command line. This module uses
[susfs](https://gitlab.com/simonpunk/susfs4ksu) to make those agree with the
spoofed props and to tuck KernelSU's infrastructure out of sight.

## What it does

At boot (via `post-fs-data`):

- **Lock-state.** Rewrites `/proc/bootconfig` so `androidboot.verifiedbootstate`,
  `androidboot.flash.locked` and `androidboot.vbmeta.device_state` report
  `green` / `1` / `locked` — matching the already-spoofed getprop values, so the
  two can't be played off against each other.
- **Hide `/data/adb`.** Registers the KernelSU infrastructure directory as a
  susfs `sus_path`, so denylisted apps (the ones that run in the un-mounted
  namespace) don't see it.
- **Quiet avc logs.** Turns on susfs avc-log spoofing so su-domain denials don't
  leak the ksu domain to `/proc/<pid>` scrapers.

It leaves KernelSU Next's own `bin/susfs` alone and installs its matching
`ksu_susfs` tool alongside it.

## Requirements

- Foxxd A67L Gen 2
- Rooted with KernelSU Next, on a kernel with **susfs v2.3.0** built in — the
  [Wicked Kernel](https://github.com/thewickedlabs/Wicked-Kernel). The bundled
  `ksu_susfs` must match the kernel's susfs version; a mismatch just no-ops.

The installer stops without changing anything if the device or KSU check fails.

## Install

1. Download [`A67LG2-SusEnabler.zip`](https://github.com/thewickedlabs/A67LG2-SusEnabler/releases/latest/download/A67LG2-SusEnabler.zip) (latest release).
2. KernelSU Next → Modules → Install from storage → select the zip.
3. Reboot.

## Uninstall

Disable or remove the module and reboot. The kernel-side susfs is left untouched;
the module only removes the `ksu_susfs` copy it placed, and the runtime spoofs
clear on the next boot since nothing re-applies them.

## Credits

susfs by [simonpunk / susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu). This
module only drives the stock `ksu_susfs` userspace tool; its own scripts are WTFPL.

## License

[WTFPL](LICENSE).

---

Wicked Labs · https://www.cyberspace7.org
