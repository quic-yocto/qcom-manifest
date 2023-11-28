# QCOM Repo Manifest README

This git repository is used to download manifests for QCOM Linux Yocto BSP releases.

The branch will be based on the release type Linux with release manifests in each branch tied to the base releases.

For QCOM Linux Yocto BSP releases the manifest branches will be named as qcom-linux-<Yocto-Project-release>,
so qcom-linux-kirkstone with all manifests tied to releases on Kirkstone in this branch.

## Host Setup

Below instructions are for 20.04 Ubuntu Version

### Install the `repo` utility

To use this manifest repo, the `repo` tool must be installed first.

```bash
mkdir ~/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo  > ~/bin/repo
chmod a+x ~/bin/repo
PATH=${PATH}:~/bin
```

If above repo is not working try below commands
```bash
cd /opt
git clone https://android.googlesource.com/tools/repo.git -b v1.13.9.3 repo_tool
cd repo_tool
git checkout -b v1.13.9.3
export PATH=/opt/repo_tool:$PATH
```

If you already having standard yocto environment, skip below prepare host environment step
Install below packages to prepare your host environment for Yocto build

```bash
sudo apt install -y gawk gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential \
chrpath socat cpio python-is-python3 python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping \
libsdl1.2-dev xterm tar locales net-tools rsync sudo vim curl zstd liblz4-tool libssl-dev bc lzop
```

Set up locales if you haven't setup already

```bash
locale-gen en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8

export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

## Download the Yocto Project BSP

```bash
mkdir <release>
cd <release>
repo init -u https://github.com/quic-yocto/qcom-manifest -b <branch name> [ -m <release manifest>]
repo sync
```

Each branch will have detailed READMEs describing exact syntax.

## Examples

To download the `qcom-6.6.00-QLI.1.0-Ver.1.0` release

```bash
repo init -u https://github.com/quic-yocto/qcom-manifest -b qcom-linux-kirkstone -m qcom-6.6.00-QLI.1.0-Ver.1.0.xml 
```

## Setup the build folder for a BSP release

```bash
export SHELL=/bin/bash
[MACHINE=<machine>] [DISTRO=qcom-<backend>] source setup-environment

<machine>   defaults to `qcm6490`
<backend>   Graphics backend type
    qcom-wayland     meta-qcom-distro
```

Examples:
- Setup for Wayland.

```bash
MACHINE=qcm6490 DISTRO=qcom-wayland source setup-environment
```

## Build an image

```bash
bitbake <image recipe>
```

Some image recipes:

Image Name           	| Description
---------------------	|---------------------------------------------------
qcom-console-image     	| core image with boot to shell


## Flash the image

Refer to User Guide on [qualcomm-linux-preview](https://www.qualcomm.com/products/internet-of-things/industrial/building-enterprise/qualcomm-linux-preview)
to complete Dev Kit setup and flash the artifacts.


## References

[Standard Yocto environment](https://docs.yoctoproject.org/4.0.13/brief-yoctoprojectqs/index.html)
