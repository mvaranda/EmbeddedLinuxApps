## Release Notes:

### release 01
TBD

## Building

### original branches
Original branch for poky and meta-xxx were dunfell


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
git clone https://github.com/mvaranda/EmbeddedLinuxApps.git
cd ~/EmbeddedLinuxApps/LinuxBuild
git submodule update --init
source ./poky/oe-init-build-env rpi-build
```

### when opening a new bash instance
```
eval `ssh-agent -s`
ssh-add ~/.ssh/id_rsa
cd ~/EmbeddedLinuxApps/LinuxBuild
source ./poky/oe-init-build-env temp
```

### build the image for target (Flash/SD-Card)
```
cd ~/EmbeddedLinuxApps/LinuxBuild/rpi-build
bitbake core-image-base
```
The output is the file:<br/>
~/EmbeddedLinuxApps/LinuxBuild/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg

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
  sudo mount -t ext4 -o loop,offset=$((90112 * 512)) ~/EmbeddedLinuxApps/LinuxBuild/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg /mnt/sdcard
for FAT32 (boot):
  sudo mount -t vfat -o loop,offset=$((8192 * 512)) ~/EmbeddedLinuxApps/LinuxBuild/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg /mnt/sdcard
```
Note: offsets 90112 and 8192 may change. Use fdisk to read the offsets:
```
fdisk -l ~/EmbeddedLinuxApps/LinuxBuild/rpi-build/tmp/deploy/images/raspberrypi3/core-image-base-raspberrypi3.rpi-sdimg
```
And update this readme.

### When submodules change
EmbeddedLinuxApps/LinuxBuild points to submodules hash in most cases from master branches. Every time a repo is changed and you want EmbeddedLinuxApps/LinuxBuild to point to your changes you will need to pull/commit the change. For example, lets say meta-openembedded has new chenges in its my_meta_branch branch. then you would:
```
cd ~/EmbeddedLinuxApps/LinuxBuild
git branch MyBranch
git checkout MyBranch
cd ~/EmbeddedLinuxApps/LinuxBuild/meta-openembedded
git pull origin my_meta_branch
cd ..
git add -u
git commit -m "update submodule meta-embedded hash (my_meta_branch)"
git push
```

### Burn SDCard
Check which driver your SDCard is (usually sdb).
```
fdisk -l
sudo dd if=tmp/deploy/images/raspberrypi3/rpi-basic-image-raspberrypi3.rpi-sdimg of=/dev/sdb bs=4M
```

