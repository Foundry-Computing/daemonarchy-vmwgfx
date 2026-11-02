# FreeBSD vmwgfx for Daemonarchy

A Foundry Computing research project to restore a native VMware graphics path for Daemonarchy's Hyprland desktop on FreeBSD.

**Status — November 1, 2026: experimental source/build work. The complete candidate build fails; no linked vmwgfx kernel module exists, and native Hyprland is not working in the tested VMware guest.**

We are documenting the work as it happens: what builds, what fails, which interfaces need native implementations, and what evidence the next milestone must produce.

## Why this work matters

Our Daemonarchy guest can boot, run terminal tools and accept authenticated SSH connections. A separately configured X11/Openbox compatibility desktop can display a terminal and Chromium through the EFI framebuffer. Native Hyprland still lacks the VMware DRM display device it needs in this tested stack.

Software rendering and a software cursor helped our Linux Omarchy lab, where a working vmwgfx driver already existed. Applying those settings to this FreeBSD guest did not create the missing display interface.

The goal is a reviewed FreeBSD VMware DRM/KMS driver path that can eventually support Daemonarchy's normal Hyprland session. The compatibility desktop remains a recovery option during development.

## What we have demonstrated

| Milestone | Evidence | Limit |
| --- | --- | --- |
| Reproducible source baseline | Exact kernel, DRM, Daemonarchy ports and Linux source revisions recorded | Applies to the pinned FreeBSD snapshot |
| Existing DRM baseline builds | Matching DMA-BUF, DRM core and TTM modules compile | Does not validate VMware driver operation |
| Initial vmwgfx adaptations | 32 of 38 translation units compile into real ELF objects | Full build fails; no vmwgfx.ko |
| Allocator fault-injection tests | Actual patched allocator bodies pass user-space ASan/UBSan tests, failure cases and 1,000 reuse cycles | Kernel VM, DMA and locking remain unvalidated |
| Recovery desktop | X11/Openbox desktop, keyboard, terminal and Chromium HTTPS page observed; graphical login returns after reboot | Owner sign-in through the returned login screen remains unobserved; this is not native Hyprland |

The object count is a compile checkpoint, not a percentage estimate of project completion. No experimental module has been installed or loaded.

## Pinned source checkpoint

| Component | Revision |
| --- | --- |
| Daemonarchy kernel source | jordanhubbard/daemonarchy-src `303b3646270ffa4bf7cec65c5c1d28bd8000cbce`, OSVERSION 1600029 |
| FreeBSD DRM baseline | freebsd/drm-kmod `1d054ae55685c45c23aa86541427dfc72afa9ca1`, tag drm_v6.12.110_1 |
| Daemonarchy ports patches | jordanhubbard/daemonarchy-ports `dd277aff0961905b32a122fa4909051d7a62abb1` |
| Linux vmwgfx source | gregkh/linux `fa5b06866b643cce7072a4e7c6be7ee3f0f1b95c`, tag v6.12.110 |

## The next engineering problems

- Native VM/pager mapping for GEM and imported DMA-BUFs.
- Correct write protection and dirty-page tracking.
- Native DMA scatter/gather segment constraints.
- VMware backdoor/hypercall transport and ABI behavior.
- Timer expiry and overrun semantics.
- Remaining driver registration, identification, linking and dependency checks.

Missing APIs need real implementations and review. Returning dummy success values would conceal the very failures we need to understand.

## Before a driver can be called usable

1. Complete the native interfaces, compilation, linking and symbol review.
2. Prepare a recoverable isolated VMware test clone.
3. Verify device attach, DRM nodes, modesetting, memory handling, GBM/EGL and repeated rendering.
4. Verify recovery, unload where supported, reboot and SSH availability.
5. Launch the actual native Daemonarchy Hyprland session and check terminal, browser, cursor, keyboard, logout and reboot.

A successful compile alone will not close these gates.

## Follow the work and help

This repository starts as the public progress home. Reviewed source patches and build material are not published here yet. There is no driver download or installation recipe at this checkpoint.

Follow or star the repository for updates. Issues are welcome for relevant upstream work, native-interface design suggestions and corrections to the documented findings. Include source revisions and a small reproducible example where possible. Keep credentials, private addresses and customer information out of reports.

We especially welcome experience with FreeBSD VM/pager internals, LinuxKPI, DRM/GEM, DMA-BUF, VMware transport and kernel testing. Please propose a bounded design or review topic before attempting runtime experiments.

## Upstream context and credit

This work builds on the Linux vmwgfx driver, FreeBSD DRM/LinuxKPI, Daemonarchy and Hyprland/Aquamarine. Historical FreeBSD vmwgfx support existed; we are not claiming to be the first VMware graphics effort on FreeBSD.

- [FreeBSD request to restore VMware and other virtual GPU drivers](https://github.com/freebsd/drm-kmod/issues/356)
- [FreeBSD DRM sources](https://github.com/freebsd/drm-kmod)
- [Linux vmwgfx source](https://github.com/gregkh/linux/tree/v6.12.110/drivers/gpu/drm/vmwgfx)
- [Daemonarchy](https://github.com/jordanhubbard/daemonarchy)
- [Aquamarine](https://github.com/hyprwm/aquamarine)

Foundry Computing's contribution at this checkpoint is the documented, pinned porting experiment and its initial adaptations and tests. This project is not an official release or endorsement by the upstream projects.

## From the workshop

Howdy! Mr. Fix I.T. here. A desktop that lights up is encouraging. A driver that earns our trust takes a little more work. Pull up a chair, follow the evidence, and help us make the next checkmark mean something.

Read the workshop stories and VM series at [The Foundry Log](https://foundrylog.blogspot.com/).
