## Release Notes:

### release 01
TBD

## Building

### Install dependencies (Ubuntu 20.04)
```
apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
build-essential chrpath socat cpio python3 python3-pip python3-pexpect \
xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa \
libsdl1.2-dev pylint3 xterm python2 openssl openssh-server vim g++ dos2unix
```

### Clone the repo
Assuming the developer will clone under its $HOME directory
```
eval `ssh-agent -s`
ssh-add ~/.ssh/id_rsa
cd ~/
git clone git@eos2git.cec.lab.emc.com:Mobile-Phoenix/radiosw-root.git
cd ~/radiosw-root
git submodule update --init
source ~/radiosw-root/poky/oe-init-build-env temp
cd ..
rm -rf temp
cd rpi-build
```

### when opening a new bash instance
```
eval `ssh-agent -s`
ssh-add ~/.ssh/id_rsa
cd ~/radiosw-root
source ~/radiosw-root/poky/oe-init-build-env temp
cd ..
rm -rf temp
cd rpi-build
```

### build the image for target (Flash/SD-Card)
```
bitbake core-image-base
```
The output is the file:<br/>
~/radiosw-root/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg

### build the SDK
```
bitbake core-image-base -c populate_sdk
```

### To inspect the image you can mount it:

```
fdisk -l tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg
Note the 90112 is the start block of img2
sudo mkdir /mnt/sdcard
for Linux partition:
  sudo mount -t ext4 -o loop,offset=$((90112 * 512)) ~/radiosw-root/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg /mnt/sdcard
for FAT32 (boot):
  sudo mount -t vfat -o loop,offset=$((8192 * 512)) ~/radiosw-root/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg /mnt/sdcard
```
Note: offsets 90112 and 8192 may change. Use fdisk to read the offsets:
```
fdisk -l ~/radiosw-root/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg
```
And update this readme.

### When submodules change
radiosw-root points to submodules hash in most cases from radio-dev branches. Every time a repo is changed and you want radiosw-root to point to your changes you will need to pull/commit the change. For example, lets say meta-openembedded has new chenges in its radio-dev branch. then you would:
```
cd ~/radiosw-root
git branch MyBranch
git checkout MyBranch
cd ~/radiosw-root/meta-openembedded
git pull origin radio_dev
cd ..
git add -u
git commit -m "update submodule metaembedded hash"
git push
```

