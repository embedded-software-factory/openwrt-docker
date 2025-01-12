# OpenWrt Docker repository

[![GPL-2.0-only License][license-badge]][license-ref]
[![CI][ci-badge]][ci-ref]
[![Docker Hub][docker-hub-badge]][docker-hub-ref]

This repository contains scripts to create Docker containers for OpenWrt. The
scripts are run via an CI and upload such containers to docker.io.

Used variables are `VERSION`, `TARGET`, `DOCKER_USER`, `DOCKER_PASS` and `DOCKER_IMAGE`.

`$VERSION`: OpenWrt version to build (e.g. "snapshot" or "19.07.4")
`$TARGET`: OpenWrt target to build (e.g. "x86-64")
`$DOCKER_USER`: user to upload
`$DOCKER_PASS`: passwort to upload
`$DOCKER_IMAGE`: image name

To build multiple targets use the `gen-targets.sh` script which generates the
files `targets.yml` and `targets_rootfs.yml`, which are imported by the GitLab
runner. This allows to run multiple jobs in parallel.

See `.gitlab-ci.yml` for the current setup.

## `sdk`

Contains the [OpenWrt
SDK](https://openwrt.org/docs/guide-developer/toolchain/using_the_sdk) based on
a `debian:latest` container with required packages preinstalled. This can be
useful when building packages on MacOS X, Windows or via CI.

### Example

    docker run --rm -v "$(pwd)"/bin/:/home/build/openwrt/bin -it openwrt/sdk
    # within the Docker container
    ./scripts/feeds update base
    make defconfig
    ./scripts/feeds install firewall
    make package/firewall/{clean,compile} -j$(nproc)

Enjoy a local OpenWrt SDK container building the `firewall3` package and but the
binary in hosts `./bin` folder.

### Tags

All currently available SDKs via lower case `<target>-<subtarget>[-<branch>]`,
appending `19.07-SNAPSHOT` or `18.06.4` let's you build other than snapshots.

## `imagebuilder`

Contains the [OpenWrt
ImageBuilder](https://openwrt.org/docs/guide-user/additional-software/imagebuilder)
based on a `debian:latest` container with required packages preinstalled. This
can be useful when creating images on MacOS X, Windows or via CI.

### Example

    docker run --rm -v "$(pwd)"/bin/:/home/build/openwrt/bin -it openwrt/imagebuilder
    # within the Docker container
    make image

Enjoy a local OpenWrt ImageBuilder container building an image for x86/64 and
store the binary in hosts `./bin` folder.

### Tags

All currently available SDKs via lower case `<target>-<subtarget>[-<branch>]`,
appending `19.07-SNAPSHOT` or `18.06.4` let's you build other than snapshots.

## `rootfs` (experimental)

> The OpenWrt runtime uses multiple active serices to work, it's not really
> suited as a container. This `rootfs` should only be used for special cases
> like CI testing.

An unpackaged version of OpenWrt's rootfs for different architectures. The
`./rootfs` folder requires slight modifications to work within Docker,
additional files for the rootfs should be added there before building.

### Example

    docker run --rm -it openwrt/rootfs

Enjoy a local OpenWrt container with internet access. Once closed the image is
removed.

### Tags

* x86_64
* armvirt-32
* armvirt-64
* arm_cortex-a15_neon-vfpv4
* arm_cortex-a9_vfpv3-d16
* aarch64_generic
* mips_24kc
* malta-be
* i386_pentium4

[ci-badge]: https://gitlab.com/openwrtorg/docker/badges/master/pipeline.svg
[ci-ref]: https://gitlab.com/openwrtorg/docker/commits/master
[docker-hub-badge]: https://img.shields.io/badge/docker--hub-openwrtorg-blue.svg?style=flat-square
[docker-hub-ref]: https://hub.docker.com/u/openwrtorg
[license-badge]: https://img.shields.io/github/license/openwrt/docker.svg?style=flat-square
[license-ref]: LICENSE
