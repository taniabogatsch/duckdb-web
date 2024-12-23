---
layout: docu
title: Platforms
redirect_from:
  - docs/dev/building/supported_platforms
---

## Supported Platforms

DuckDB officially supports the following platforms:

| Platform name      | Description                                |
|--------------------|--------------------------------------------|
| `linux_amd64`      | Linux AMD64                                |
| `linux_arm64`      | Linux ARM64                                |
| `osx_amd64`        | macOS 12+ (Intel CPUs)                     |
| `osx_arm64`        | macOS 12+ (Apple Silicon: M1, M2, M3 CPUs) |
| `windows_amd64`    | Windows 10+ on Intel and AMD CPUs (x86_64) |
| `windows_arm64`    | Windows 10+ on ARM CPUs (AArch64)          |

## Other Platforms

There are several platforms with varying levels of support.
For some, DuckDB binaries and extensions (or a [subset of extensions]({% link docs/extensions/working_with_extensions.md %}#platforms)) are distributed.
For most platforms, DuckDB can often be [built from source](#building-duckdb-from-source).

| Platform name          | Description                                                                                          |
|------------------------|------------------------------------------------------------------------------------------------------|
| `freebsd_amd64`        | FreeBSD AMD64 (x64_64)                                                                               |
| `freebsd_arm64`        | FreeBSD ARM64                                                                                        |
| `linux_arm64_android`  | Android ARM64                                                                                        |
| `linux_arm64_gcc4`     | Linux AMD64 with GCC 4 (e.g., CentOS 7)                                                              |
| `wasm_eh`              | WebAssembly Exception Handling                                                                       |
| `wasm_mvp`             | WebAssembly Minimum Viable Product                                                                   |
| `windows_amd64_mingw`  | Windows 10+ AMD64 (x86_64) with MinGW                                                                |
| `windows_amd64_rtools` | Windows 10+ AMD64 (x86_64) for [RTools](https://cran.r-project.org/bin/windows/Rtools/) (deprecated) |
| `windows_arm64_mingw`  | Windows 10+ AMD64 (x86_64) with MinGW                                                                |

32-bit architectures are officially not supported but it is possible to build DuckDB manually for some of these platforms, e.g., for [Raspberry Pi boards]({% link docs/dev/building/build_instructions.md %}#raspberry-pi-32-bit).

## Building DuckDB from Source

DuckDB can be [built from source]({% link docs/dev/building/build_instructions.md %}) for several other platforms such as Android, FreeBSD, macOS 11, and Linux distributions using musl libc.

For details on free and commercial support, see the [support policy blog post](https://duckdblabs.com/news/2023/10/02/support-policy#platforms).
