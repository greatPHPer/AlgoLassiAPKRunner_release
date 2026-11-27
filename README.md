# AlgoLassi APK Runner — Releases

This repository is the public distribution home for **AlgoLassi APK Runner**. Published downloads, checksums, release notes and installation guidance will be attached to GitHub Releases here.

> **No installable release has been published yet.** The current project is an interactive Android 13 x86-64 proof of concept validated on one Windows 11 + WSL2 + WSLg development machine. It is not yet a portable installer and has not passed a clean-machine installation test.

## Project links

- **Development source:** https://github.com/greatPHPer/AlgoLassiAPKRunner
- **Development runtime bootstrap notes:** https://github.com/greatPHPer/AlgoLassiAPKRunner/blob/feature/wsl2-reproducible-bootstrap/docs/development/wsl2-runtime-bootstrap.md
- **Development baseline:** https://github.com/greatPHPer/AlgoLassiAPKRunner/blob/feature/wsl2-reproducible-bootstrap/docs/development/wsl2-reproducibility-baseline.md
- **Release readiness gates:** [docs/RELEASE-READINESS.md](docs/RELEASE-READINESS.md)

## Current engineering status

Validated on the development machine:

- Custom WSL2 kernel boots with Android Binder/BinderFS and AppArmor support.
- LXC 6.0.6 starts the Android 13 x86-64 userspace.
- Android reaches sys.boot_completed=1.
- F-Droid launches in the WSLg desktop session.
- The runtime can be cold-started using the repository's experimental bootstrap scripts.
- The kernel candidate's embedded configuration matches the active configuration; the existing modules VHDX passed ABI checks.

Still required before a public release:

- Reproducible kernel **and modules** packaging. The currently tested modules VHDX was reused from the development installation; it is not yet a clean, independently generated release artifact.
- Automated WSL/runtime provisioning with machine-independent paths and safe rollback.
- Android system/vendor image provenance and license review. Redistribution is not approved, so these images must not be bundled or mirrored by this project at this stage.
- A repeatable install/uninstall/upgrade process.
- Clean-machine validation on a second Windows host.
- Release checksums, provenance, notices and a documented support matrix.

The WSLg window stacking/tooltip issue is a known development-environment limitation. It is not being represented as resolved or as a failure of Android boot.

## Release policy

1. A GitHub Release is created only after the corresponding source revision and release checks have been validated.
2. Each downloadable asset must have a documented target platform, architecture, version and installation procedure.
3. SHA-256 checksums must be published for every release asset.
4. Android images, kernel modules and other third-party components must have verified provenance and redistribution permissions before they are included in an installer or release asset.
5. No development-machine configuration, Android user data, private configuration, build cache or machine-specific absolute paths may be included.
6. Experimental builds must be clearly labelled **Preview** and must not be described as generally portable.

## Downloading

There are currently no release downloads. Watch the [Releases page](https://github.com/greatPHPer/AlgoLassiAPKRunner_release/releases) for future preview and stable builds.

## Reporting problems

Please include the release tag, Windows/Linux version, CPU architecture, installation log and relevant runtime diagnostics. Do not attach Android user-data directories, credentials or private configuration files.
