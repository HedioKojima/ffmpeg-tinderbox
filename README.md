# FFmpeg Build Environment

This repository provides a Docker-based build environment for compiling recent git versions of FFmpeg for 64-bit Windows. It is a trimmed fork of [nanake/ffmpeg-tinderbox](https://github.com/nanake/ffmpeg-tinderbox): the dependency set is reduced to the components listed under [Enabled Libraries](#enabled-libraries), including NVIDIA codec headers, JPEG XL, Opus, WebP, SVT-AV1 and vid.stab.

The project automates dependency compilation and FFmpeg builds for supported targets.

## Requirements

Make sure the following tools are installed:

- Bash
- Docker

## Build Configuration

### Targets

| Target | Description |
|----------|-------------|
| `win64` | 64-bit Windows |

### Variants

Static builds only; the `-shared` variants from upstream are not part of this fork.

| Variant | Description |
|----------|-------------|
| `gpl` | LGPL/GPL build without non-free components |
| `nonfree` | Adds non-free `libfdk-aac` (AAC and xHE-AAC); the variant built by CI |

### Add-ins

| Add-in | Description |
|---------|-------------|
| `debug` | Embeds GDB-compatible debug symbols |

## Building Docker Images

If you only need to build FFmpeg, you do not need to build the `base` or `target` images; you can skip directly to the [Building FFmpeg](#building-ffmpeg) step.

### Build the Base Image

```bash
docker build \
    -t base-win64:latest \
    images/base-win64
```

This fork does not compile the Fedora + UCRT64 mingw toolchain itself; `images/base-win64/Dockerfile` reuses `ghcr.io/nanake/base-win64:latest` and re-tags it under your own namespace. Restore the original Dockerfile from upstream if you need to change the toolchain packages, mingw version or `FFBUILD_*` environment.

When using GitHub Actions, run the **Base image** workflow once before the first **Windows Cross Compiled** run so that `ghcr.io/<owner>/base-win64:latest` exists.

### Generate the Target Dockerfile

```bash
./generate.sh <target> <variant>
```

### Build the Target Image

```bash
docker build \
    -t <target>-<variant>:latest .
```

### Build Everything Automatically
Instead of running the previous steps manually:

```bash
./makeimage.sh <target> <variant>
```

This command generates and builds both the base image and the target image.

## Building FFmpeg

Build FFmpeg using a target and variant:

```bash
./build.sh <target> <variant> <add-in>
```

### Output
After a successful build, the generated binaries and packages can be found in `artifacts` folder

## Enabled Libraries

The build environment currently enables the following libraries and features:

- cuda-llvm
- ffnvcodec
- iconv
- lcms2
- libass
- libcurl
- libdav1d
- libfdk-aac
- libfdk-xhe-aac
- libfontconfig
- libfreetype
- libfribidi
- libharfbuzz
- libjxl
- libopus
- libplacebo
- libsrt
- libssh
- libsvtav1
- libvidstab
- libwebp
- libxml2
- libzimg
- lzma
- opencl
- openssl
- pthreads
- vulkan
- zlib

`brotli`, `lcms2`, `xxhash`, `zstd` and `shaderc` are also built as dependencies of the libraries above.

### Intentionally Not Built

The following upstream components are removed in this fork: libx264, libx265, libvpx, rav1e, AMF/VPL/VA-API, DeckLink, DVD/Blu-ray, AviSynth/VapourSynth, SDL2 (therefore `ffplay` is not built), whisper and the remaining optional codecs from the upstream list. Restore the corresponding `scripts.d/*.sh` and variant files from upstream if any of them are needed.

##  Acknowledgments

This project is heavily inspired by the excellent work in [BtbN](https://github.com/BtbN/FFmpeg-Builds).
