# APK Runner — Release Readiness

This document is the release gate for the public distribution repository. It is intentionally conservative: a working development-machine demonstration is not the same thing as a portable, reproducible release.

**Current decision: HOLD — no downloadable release is approved yet.**

The project has demonstrated an interactive Android 13 x86-64 userspace in an LXC container on one Windows 11 + WSL2 + WSLg development machine. It has not yet demonstrated a clean-machine installation or a relocatable end-user package.

## 1. Current validated scope

The following results have been observed on the development machine only:

| Area | Current evidence | Release interpretation |
| --- | --- | --- |
| Windows host | Windows 11 x64 with WSL2 and WSLg | One-host proof; not a supported-host matrix |
| Linux environment | Ubuntu WSL distribution with systemd | The current target is Windows + WSL2; a separate Ubuntu installation is not required for this target |
| Kernel | Custom WSL2 kernel boots with Android Binder IPC and BinderFS support, SecurityFS, AppArmor and cgroup v2 | Candidate kernel is not yet a clean-machine, reproducible kernel-and-modules package |
| Container | LXC 6.0.6 | Tested version on one development environment |
| Android userspace | Waydroid-derived LineageOS 20 / Android 13 x86-64 userspace | Image origin, version pinning and redistribution rights must be resolved before bundling |
| Android boot | Container has reached `sys.boot_completed=1` | Successful runtime proof on the development machine |
| Desktop | Android desktop and individual application windows have been displayed through WSLg | Window integration remains experimental |
| Input | Keyboard and pointer interaction have been demonstrated | Must be repeated from a clean installation |
| Window lifecycle | Resize, Alt+Tab, minimize, maximize and restore have been exercised | A restore/redraw artifact remains: after restoring from a maximized state, a stale maximized image can remain behind the window until focus changes (for example, Alt+Tab) |
| Rendering | ANGLE with SwiftShader software rendering was observed | Do not claim hardware-accelerated graphics or gaming performance |
| Android app | F-Droid was present and launched as a development test app | This does not yet prove a polished, user-facing APK install/update/uninstall workflow |
| Bootstrap | Experimental WSL2 scripts have performed host preparation, runtime assembly and Android startup on the development machine | Not yet a first-run installer or clean-machine provisioning system |

The development baseline also recorded a kernel configuration match between the active and candidate kernel. The tested modules VHDX was reused from the development installation and passed ABI checks; it was not independently regenerated as a release artifact. This is useful compatibility evidence, but **it is not proof of reproducible kernel-plus-modules packaging**.

## 2. Release blockers

Every blocker below must be closed or explicitly excluded from the published product scope before a public preview can be approved.

### R1 — Reproducible WSL2 kernel and modules package

- [ ] Pin the exact kernel source revision, configuration, toolchain and build inputs.
- [ ] Build the kernel and its matching modules from the same documented inputs.
- [ ] Produce a versioned package manifest and SHA-256 checksums.
- [ ] Verify that the package boots and loads the required Binder/AppArmor facilities on a clean WSL2 distribution.
- [ ] Do not reuse an unexplained modules VHDX from the development machine as a release dependency.

### R2 — First-run WSL2 provisioning

- [ ] Define the supported Windows and WSL versions.
- [ ] Detect WSL2, WSLg, the selected distribution and required host features before making changes.
- [ ] Provision required Ubuntu packages and the pinned LXC version without silently replacing incompatible packages.
- [ ] Install or select the matching custom kernel/modules package through a controlled, documented operation.
- [ ] Generate machine-specific paths and LXC configuration instead of relying on development-machine paths.
- [ ] Make privileged operations explicit, narrowly scoped and auditable.
- [ ] Provide rollback and recovery instructions for interrupted or failed setup.
- [ ] Never require users to install a separate Linux operating system for the Windows + WSL2 target.

### R3 — Android userspace provenance and licensing

- [ ] Record the exact system/vendor image source, upstream project, build identifier and cryptographic hashes.
- [ ] Review the licenses and redistribution terms for every image and bundled component.
- [ ] Obtain approval before bundling, mirroring or redistributing Android images.
- [ ] Until approval is recorded, do not attach Android system/vendor images to GitHub Releases.
- [ ] Provide a verified user-supplied-image or separately hosted provisioning route only after its licensing and integrity model is documented.

### R4 — Portable runtime lifecycle

- [ ] Install and start from a fresh WSL2 distribution with no pre-existing APK Runner runtime state.
- [ ] Stop and restart reliably without manual cleanup.
- [ ] Preserve Android user data across ordinary runtime restarts and application upgrades.
- [ ] Support uninstall and rollback without deleting unrelated WSL distributions or user files.
- [ ] Detect missing, corrupt or mismatched images/configuration and report actionable errors.
- [ ] Eliminate hard-coded development-only paths and assumptions.

### R5 — End-user APK and desktop workflow

- [ ] Demonstrate selecting a local APK, installing it into Android, resolving its launcher activity and opening it.
- [ ] Demonstrate app close/relaunch and Android runtime restart.
- [ ] Retest keyboard, pointer, resize, minimize, maximize, restore and Alt+Tab behavior.
- [ ] Resolve or clearly document the stale-image/redraw issue after restoring a maximized window.
- [ ] Verify persistent data and app installation after a Windows restart.
- [ ] Document known compatibility limits; do not promise universal APK support.

### R6 — Clean-machine validation

- [ ] Validate on a second Windows x64 machine or a genuinely clean Windows/WSL test environment.
- [ ] Start with no APK Runner-specific kernel, LXC container, Android image mount or runtime directory.
- [ ] Follow only the published setup instructions.
- [ ] Record Windows build, WSL version, WSLg version, Ubuntu version, kernel, CPU/GPU and test results.
- [ ] Complete install, first boot, APK install/launch, stop/start, data persistence and uninstall tests.
- [ ] Keep the full logs with the release candidate record, with secrets and personal data removed.

### R7 — Build, security and release evidence

- [ ] Run the source build and all unit/regression tests successfully.
- [ ] Run shell syntax checks and WSL2 bootstrap tests.
- [ ] Run an end-to-end smoke test on the clean-machine environment.
- [ ] Generate SHA-256 checksums for every downloadable asset.
- [ ] Include source commit, build workflow/run, toolchain and package manifest in the release notes.
- [ ] Include third-party notices and a dependency/license inventory.
- [ ] Scan the package for credentials, local `.env` files, Android user data, caches and machine-specific paths.
- [ ] Document supported Windows/WSL versions, hardware expectations, known limitations and recovery steps.

## 3. Suggested release sequence

| Stage | Purpose | Publication rule |
| --- | --- | --- |
| Development milestone | Record progress on the source repository | Not a user download |
| Internal candidate | Validate a versioned package and its checksums | Keep artifacts private or clearly marked internal |
| Preview 1 | Demonstrate installation on a clean second environment with known limitations | Publish only after R1–R7 are satisfied for the preview's declared scope |
| Stable release | Support a defined host matrix and repeatable lifecycle | Requires broader regression and compatibility evidence |

A tag such as `v0.1.0-preview.1` is only a proposed naming example. Do not create the tag or publish that version until the release gate is approved.

## 4. Release asset policy

A future release should contain only reviewed, reproducible end-user assets. Each asset should have:

- A clear product name, semantic version and target architecture.
- A declared packaging model and installation/uninstallation instructions.
- A SHA-256 digest in `SHA256SUMS.txt`.
- Release notes identifying the exact source commit and known limitations.
- Third-party notices and license information.
- No secrets, local environment files, Android private data, developer caches or machine-specific absolute paths.

Do not upload WSL kernel/modules or Android system/vendor images until their provenance, integrity and redistribution rights have been reviewed.

## 5. Out of scope for the first Windows + WSL2 preview

Unless the product scope is explicitly changed, the first preview should not promise:

- A separate Ubuntu installation or replacement of the user's Windows operating system.
- Universal compatibility with all Windows/WSL hardware and configurations.
- Hardware GPU acceleration when the tested renderer is ANGLE + SwiftShader.
- ARM-only APK translation, Google Play services, DRM-dependent apps or universal Android app compatibility.
- A hardened security sandbox for running untrusted APKs.

## 6. Decision record

**Status: HOLD.** The interactive Android runtime is a meaningful development milestone, but portability is not established until a fresh WSL2 environment can install, boot, launch an APK, preserve data, recover from failure and uninstall using only the published instructions. The release repository must not imply that these gates have passed before test evidence is attached.
