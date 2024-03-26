# QCOM Repo Manifest README

This git repository is used to download manifests for QCOM Linux Yocto BSP releases.

The branch will be based on the release type Linux with release manifests in each branch tied to the base releases.

For QCOM Linux Yocto BSP releases the manifest branches will be named as qcom-linux-[Yocto-Project-release],
so qcom-linux-kirkstone with all manifests tied to releases on Kirkstone in this branch.

Below procedure assumes that you have gone through [qualcomm-linux-preview](https://www.qualcomm.com/products/internet-of-things/industrial/building-enterprise/qualcomm-linux-preview) and have a [valid Qualcomm account](https://www.qualcomm.com/support)

If you are already having standard yocto environment, skip below prepare Host setup steps


# Qualcomm Tools

Run the following commands to set up Qualcomm Package Manager. It is used to generate
personalized access token to access Qualcomm proprietary software, for example, to fetch
binary SDKs during SDK build.

## Follow below instructions to install qsc-cli

- **Install curl if you haven't installed already**
```bash
sudo apt install curl
```

- **Download and Install the debian package for qsc-cli**
```bash
curl -L https://softwarecenter.qualcomm.com/api/download/software/qsc/linux/latest.deb -o qsc_installer.deb
sudo dpkg -i qsc_installer.deb
```

- **log in to qsc-cli**
```bash
qsc-cli login -u [username]
```

# Host Setup

The host machine needs a few setup operations to ensure the required software tools are ready
The released software requires the Ubuntu 20.04 host machine. Follow below instructions

## Install below packages to prepare your host environment for Yocto build

```bash
sudo apt update
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential \
    chrpath socat cpio python3 python3-pip python3-pexpect xz-utils \
    debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa \
    libsdl1.2-dev pylint3 xterm python3-subunit mesa-common-dev zstd \
    liblz4-tool locales tar python-is-python3 file libxml-opml-simplegen-perl \
    vim whiptail
```

## Install the `repo` utility

To use this manifest repo, the repo tool must be installed first.
To install repo, run these commands:

```bash
mkdir -p ~/bin
cd ~/bin
#Note if you already have a previous directory of repo_tool, you can delete it
rm -rf ~/bin/repo_tool
git clone https://android.googlesource.com/tools/repo.git -b v2.41 repo_tool
cd repo_tool
git checkout -b v2.41
export PATH=~/bin/repo_tool:$PATH
```

If the above method did not work you can try below commands for repo installation

**Note:** latest repo version works with python3
```bash
mkdir -p ~/bin
curl https://raw.githubusercontent.com/GerritCodeReview/git-repo/v2.41/repo -o ~/bin/repo && chmod +x ~/bin/repo
export PATH=~/bin:$PATH
```

## Add your Qualcomm login credentials to .netrc file in your home directory

**Log in to qsc-cli to generate the PAT ( Personalized Access Token )**
```bash
qsc-cli login -u <username>
```

**Run below command to generate the PAT**
```bash
qsc-cli pat --get
```

This command will give output as captured in the Info note below
The last line in this output is the token, which can be used to access
Qualcomm Proprietary repositories. This token will expire in two weeks
```bash
qsc-cli pat --get
```
[Info]: Starting qsc-cli version 0.0.0.9

**5LThNlklb55mMVLB5C2KqUGU2jCF**

vim ~/.netrc # add below entries

```bash
machine chipmaster2.qti.qualcomm.com
login <your Qualcomm login id>
password <your PAT>

machine qpm-git.qualcomm.com
login <your Qualcomm login id>
password <your PAT>
```

## Set up locales if you haven't setup already, by using the following commands

```bash
sudo locale-gen en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

## Update git configurations

**Check if your identity is configured in .gitconfig**
```bash
git config --get user.email
git config --get user.name
```

**Run the following commands if you do not have your account identity set in .gitconfig**
```bash
git config --global user.email you@example.com
git config --global user.name "Your Name"
```

**Add below UI color option for output of console**
```bash
git config --global color.ui auto
```

**Add below configuration required to fetch Qualcomm proprietary sources/binaries**
```bash
git config --global http.postBuffer 1048576000
git config --global http.maxRequestBuffer 1048576000
git config --global http.lowSpeedLimit 0
git config --global http.lowSpeedTime 999999
git config --global http.https://chipmaster2.qti.qualcomm.com.followRedirects true
git config --global http.https://qpm-git.qualcomm.com.followRedirects true
```

## Download the Yocto Project BSP

```bash
mkdir [release]
cd [release]
repo init -u https://github.com/quic-yocto/qcom-manifest -b [branch name] -m [release manifest]
repo sync
```

Each branch will have detailed READMEs describing exact syntax.

**Example:**

To download the `qcom-6.6.17-QLI.1.0-Ver.1.3` release

```bash
repo init -u https://github.com/quic-yocto/qcom-manifest -b qcom-linux-kirkstone -m qcom-6.6.17-QLI.1.0-Ver.1.3.xml
repo sync
```

## Setup the build folder for a BSP release

MACHINE=[machine] DISTRO=qcom-[backend] source setup-environment

[machine]   defaults to `qcm6490`

[backend]   Graphics backend type
- qcom-wayland     meta-qcom-distro

Example for setup Wayland, machine qcm6490:

```bash
MACHINE=qcm6490 DISTRO=qcom-wayland source setup-environment
```

## Build an image

bitbake [image recipe]

**Some image recipes:**

Image Name           	    	| Description
---------------------	    	|---------------------------------------------------
qcom-minimal-image          	| A minimal rootfs image that boots to shell.
qcom-console-image          	| Boot to shell with Package group to bring in all basic packages.
qcom-multimedia-image       	| Image recipe includes recipes for multimedia software components, such as, audio, bluetooth, camera, computer vision, display and video.
qcom-multimedia-test-image  	| This image recipe includes tests

**Example command:**

```bash
bitbake qcom-multimedia-image
```

## To Include add-on layers, refer below README.md files

1. [Qualcomm Intelligent Multimedia Product SDK](https://github.com/quic-yocto/meta-qcom-qim-product-sdk/blob/kirkstone/README.md)
2. [Realtime Linux](https://github.com/quic-yocto/meta-qcom-realtime/blob/kirkstone/README.md)
3. [Qualcomm Intelligent Robotics Product SDK](https://github.com/quic-yocto/meta-qcom-robotics-sdk/blob/kirkstone/README.md)

## Flash the image

To flash the generated build, see the [Flash software](https://docs.qualcomm.com/bundle/resource/topics/80-70014-251/flash_rb3_software_0.html)


## References

If you are new to the Yocto project, you may try your first build as documented in the Yocto project at [Standard Yocto environment](https://docs.yoctoproject.org/4.0.14/singleindex.html#document-brief-yoctoprojectqs/index)

The complete index of Yocto project docs is available at [Yocto project docs](https://docs.yoctoproject.org/4.0.14/singleindex.html#welcome-to-the-yocto-project-documentation)
