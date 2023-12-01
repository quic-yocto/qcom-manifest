# QCOM Repo Manifest README

This git repository is used to download manifests for QCOM Linux Yocto BSP releases.

The branch will be based on the release type Linux with release manifests in each branch tied to the base releases.

For QCOM Linux Yocto BSP releases the manifest branches will be named as qcom-linux-<Yocto-Project-release>,
so qcom-linux-kirkstone with all manifests tied to releases on Kirkstone in this branch.

If you already having standard yocto environment, skip below prepare host environment steps

## Host Setup

The host machine needs a few setup operations to ensure the required software tools are ready
The released software requires the Ubuntu 20.04 host machine. Follow below instructions

Install below packages to prepare your host environment for Yocto build

```bash
sudo apt update
sudo apt install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential \
chrpath socat cpio python-is-python3 python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping \
libsdl1.2-dev xterm tar locales net-tools rsync sudo vim curl zstd liblz4-tool libssl-dev bc lzop
```

### Ensure bash is the default shell
Run the following command to confirm that the output is bash

```bash
ls -la /bin/sh
```

if the output is not pointing to /bin/bash, modify the Ubuntu shell to bash with the following command

```bash
sudo ln -sf /bin/bash /bin/sh
```

### Install the `repo` utility

To use this manifest repo, the repo tool must be installed first.
To install repo, run these commands:

```bash
mkdir -p ~/bin
cd ~/bin
git clone https://android.googlesource.com/tools/repo.git -b v1.13.9.3 repo_tool
cd repo_tool
git checkout -b v1.13.9.3
```

Add the following line to the ~/.bashrc file to ensure that the repo_tool folder is in your PATH variable
export PATH=~/bin/repo_tool:$PATH

### Set up locales if you haven't setup already

```bash
sudo locale-gen en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8

export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

### Run below instructions if you don't have your account identity in ~/.gitconfig

git config --global user.email you@example.com
git config --global user.name "Your Name"

### (Recommended) Apply these git configurations to address the remote hung-up issues while git cloning

```bash
git config --global http.postBuffer 1048576000
git config --global http.maxRequestBuffer 1048576000
```


## Download the Yocto Project BSP

mkdir <release>
cd <release>
repo init -u https://github.com/quic-yocto/qcom-manifest -b <branch name> [ -m <release manifest>]
repo sync

Each branch will have detailed READMEs describing exact syntax.

## Examples

To download the `qcom-6.6.00-QLI.1.0-Ver.1.0` release

```bash
repo init -u https://github.com/quic-yocto/qcom-manifest -b qcom-linux-kirkstone -m qcom-6.6.00-QLI.1.0-Ver.1.0.xml 
repo sync
```

## Setup the build folder for a BSP release

Build commands needs to be executed in bash shell environment
To confirm current working shell is bash or not by running below command and check the output

```bash
echo $0
```

Output of above command should be bash
if the output is not bash, enter into the bash shell by entering the below command in the command prompt

```bash
bash
```

```bash
export SHELL=/bin/bash
```

[MACHINE=<machine>] [DISTRO=qcom-<backend>] source setup-environment

<machine>   defaults to `qcm6490`
<backend>   Graphics backend type
    qcom-wayland     meta-qcom-distro

Examples:
- Setup for Wayland.

```bash
MACHINE=qcm6490 DISTRO=qcom-wayland source setup-environment
```

## Build an image

bitbake <image recipe>

Some image recipes:

Image Name           	| Description
---------------------	|---------------------------------------------------
qcom-console-image     	| core image with boot to shell

Example command:

```bash
bitbake qcom-console-image
```

## Flash the image

Refer to User Guide on [qualcomm-linux-preview](https://www.qualcomm.com/products/internet-of-things/industrial/building-enterprise/qualcomm-linux-preview)
to complete Dev Kit setup and flash the artifacts.


## References

[Standard Yocto environment](https://docs.yoctoproject.org/4.0.13/brief-yoctoprojectqs/index.html)
