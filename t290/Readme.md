

Follow this guide to build for t290 [link](https://wiki.lineageos.org/devices/gtowifi/build "link")

Things required to build .

 1. AWS Instance (or) Linux Machine
 2. good knowledge of repo and git

 if you are using AWS instance (preferred) use c5a.8xlarge instance . it has 32 vcpus which is suitable. if you use Linux machine it will take longer (min 5-10 hours). 

 get a c5a.8xlarge Ubuntu 20.04 instance. more cores more faster the build

 # Hardware

 use c5a.8xlarge instance 

 # Environment setup

 connect to AWS instance using vs code. 

now update the env 

 ```bash
    sudo apt-get update
 ```

 ```bash
    sudo apt-get upgrade
 ```

 ## platform tools

open terminal in vscode and paste this 

 ```shell 
 curl -L https://dl.google.com/android/repository/platform-tools-latest-linux.zip -o platform-tools-latest-linux.zip 
 
 ```
this will download the platform tools

download unzip

```bash
    sudo apt install unzip
 ```

extract platform-tools


 ```bash 
    unzip platform-tools-latest-linux.zip
 ```

find .profile file in /home/ubuntu/.profile (home) path

paste this in the end 

```bash
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
```

now apply changes

```bash
        source ~/.profile
```

## installing required packages

```bash 
    sudo apt install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev libelf-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

these are required packages to be installed

## creating the directory

    ```bash 
    mkdir -p ~/bin
    mkdir -p ~/android/lineage
    ```
## install the repo 

 ```bash 
    curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    chmod a+x ~/bin/repo
 ```

 paste the below content into .profile file in home directory (/home/ubuntu/.profile)

 ```bash
    # set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```

    now run the following command

    ```bash
        source ~/.profile
    ```

 ## configure git 

 ```bash
    git config --global user.email "you@example.com"
git config --global user.name "Your Name"

 ```

 ## turn on cache to speedup build performance

 paste this in terminal 

 ```bash
    export USE_CCACHE=1
    export CCACHE_EXEC=/usr/bin/ccache
 ```

 and paste below content in ~/.bashrc

 ```bash 
    ccache -M 50G
 ```

 and apply changes

 ```bash
    source ~/.bashrc
 ```

 # building Lineage OS

 initialize repo

 ```bash
    cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b lineage-20.0 --git-lfs

```

## sync the sources 

    ```bash
        repo sync -c -j$(nproc)
    ```

sitback and relax it will take time

if fails run the command again

## preparing device specific code 

```bash
    source build/envsetup.sh
breakfast gtowifi
```

for sure you will get error message . it's ok it will be fixed in the next step

## extracting the proprietary blobs

we need to download a build first

```bash
cd ~
curl -L https://mirrorbits.lineageos.org/full/gtowifi/20230321/lineage-20.0-20230321-nightly-gtowifi-signed.zip -o lineage-20.0-20230321-nightly-gtowifi-signed.zip
```

make a dump folder

```bash 
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

make sure you are in ~/android/system_dump/ path

```bash 
    unzip ../../lineage-20.0-20230321-nightly-gtowifi-signed.zip system.transfer.list system.new.dat*
```

this will extract system

then extract vendor

```bash
unzip ../../lineage-20.0-20230321-nightly-gtowifi-signed.zip vendor.transfer.list vendor.new.dat*
```

then decompress using brotli

```bash
sudo apt-get install brotli
brotli --decompress --output=system.new.dat system.new.dat.br
brotli --decompress --output=vendor.new.dat vendor.new.dat.br
```

now clone sdat2img

```bash 
git clone https://github.com/xpirt/sdat2img
```

now convert dat files to img
```bash
python sdat2img/sdat2img.py system.transfer.list system.new.dat system.img
```

mount system 

```bash
mkdir system/
sudo mount system.img system/
```

now vendor

```bash
python sdat2img/sdat2img.py vendor.transfer.list vendor.new.dat vendor.img
```

now mount vendor

```bash
sudo rm system/vendor
sudo mkdir system/vendor
sudo mount vendor.img system/vendor/
```

now go to ~/android/lineage

```bash
    cd ~/android/lineage
```

now extract blobs


```bash
    ./device/samsung/gtowifi/extract-files.sh ~/android/system-dump/
```

## changes to make












 

 