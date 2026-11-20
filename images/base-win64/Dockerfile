# syntax=docker/dockerfile:latest
# Reuse the prebuilt cross toolchain image from upstream instead of rebuilding
# Fedora + UCRT64 mingw from scratch on every run.
#
# Trade-off: the mingw version, installed packages and FFBUILD_* environment
# below are whatever upstream published. If you need to change the toolchain
# itself, restore the original Dockerfile from nanake/ffmpeg-tinderbox and
# rebuild `ghcr.io/<owner>/base-win64:latest` from this directory.
FROM ghcr.io/nanake/base-win64:latest AS base
