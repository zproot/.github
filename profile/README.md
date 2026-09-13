# zproot

Linux on Android, without root or Termux.

zproot is a clean-room Zig reimplementation of PRoot. It uses Linux `ptrace()` to intercept syscalls and translate filesystem paths, creating a virtual root filesystem for a guest Linux distribution without actual root privileges.

## Repositories

| Repository | Description | Status |
|---|---|---|
| [zproot](https://github.com/zproot/zproot) | ptrace tracer core, written in Zig | active |
| [zproot-android](https://github.com/zproot/zproot-android) | Android APK, Kotlin frontend | active |
| [zproot-compositor](https://github.com/zproot/zproot-compositor) | Wayland compositor for Android | planned |

## Status

M1 through M5 are complete: syscall loop, argument reading, path rewriting, `execve` handling, and `openat2`/`statx` interception.

M6 (Android seccomp / SIGSYS handler) is in progress. M7 (aarch64-linux-android build) is planned.

No working APK yet. If you need something usable today, use [pr](https://github.com/oonid/pr) or [Termux](https://github.com/termux/termux-app).

## Goals

- Run Alpine, Debian, Ubuntu, Arch, Fedora, and more on any Android device
- Full package manager support: `apk`, `apt-get`, `pacman`
- Compile and run C, Rust, and other programs inside the guest
- MIT-licensed, small static binary, no GPL obligations inherited from upstream C
- Sideload and F-Droid friendly

## How it works

zproot sits between a Linux program and the Android kernel. When the guest calls `openat("/etc/passwd")`, zproot rewrites the syscall argument to point at `/data/data/com.zproot/files/rootfs/etc/passwd`. The kernel opens the real file. The guest sees `/etc/passwd`.

This is the same mechanism PRoot uses. It is not emulation, not virtualization, and not a container. It is a debugger that lies to one process about what paths it is asking for.

## Why Zig

- Direct syscall control via `std.os.linux` and inline `asm`
- Built-in Android cross-compilation with no NDK sysroot
- Compile-time code generation for multi-architecture register handling
- Small static binary
- Clean-room licensing

## Clean-room statement

zproot is a clean-room reimplementation. No source code from `proot`, `termux-proot`, or `proot-distro` was read, copied, or translated during its development. The implementation is based on the Linux man pages, the PRoot academic paper, and public usage documentation.

This is what allows zproot to be released under the MIT license while the upstream C projects remain GPL.

## License

MIT
