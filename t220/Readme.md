Things required to build .

 1. AWS Instance (or) Linux Machine
 2. good knowledge of repo and git

 if you are using AWS instance (preferred) use c5a.8xlarge instance . it has 32 vcpus which is suitable. if you use Linux machine it will take longer (min 5-10 hours). 

 get a c5a.8xlarge Ubuntu 20.04 instance. more cores more faster the build

 > :warning: please use latest instnace don't use one with ccache enabled

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


 > :warning: don't enable cache

 - Install xattr

  ```bash
    sudo apt-get install xattr
  ```

- Create a new working directory for your Pixel Experience build and navigate to it:
    ```
    mkdir pixel; cd pixel
    ```
- Clone this repo:
    ```
    git clone https://github.com/ponces/treble_build_pe -b twelve
    ```
- Finally, start the build script:
    ```
    bash treble_build_pe/build.sh twelve
    ```

    after successful build you will see pixel experience out in ~/builds folder

    like PixelExperience-*.img.xz

    delete the builds folder now

    ## making changes

    open this file in vascode

  /home/ubuntu/android/lineage/packages/apps/Settings/res/xml/reset_dashboard_fragment.xml


    comment ount the following line

    ```xml
    
    <com.android.settingslib.RestrictedPreference
        android:key="factory_reset"
        android:title="@string/main_clear_title"
        settings:keywords="@string/keywords_factory_data_reset"
        settings:userRestriction="no_factory_reset"
        settings:useAdminDisabledSummary="true"
        android:fragment="com.android.settings.MainClear" /> 
    ```

    now we are done removing factory reset options


    open this file in vs code 

    /home/ubuntu/android/lineage/treble_build_pe/build.sh

    comment ount the following line

    ```bash
    initRepos
    syncRepos
    applyPatches
    ```

    now run build again

    ```
    bash treble_build_pe/build.sh twelve
    ```

    this time it will build the img with changes

    builds will be in ~/builds folder

     download slim version to your windows pc

    # Repacking the firmware

    1. download A11 firmware from samfw for t220
    2. extract the tar file downloaded
    you will see AP,BL,CSC files

    3. rename AP file extention from .tar.md5 to .tar
    4. extract the AP tar file

    ## install wsl ubuntu distro

    open powershell as administrator

    and run the following command

    ```
    wsl --install
    ```

    this will install wsl . 

    - Download ubuntu distro

    ```
    wsl --install -d Ubuntu-20.04
    ```

    this will take some time and will ask you to set username and password

    ## setting up ota tools

    you can access windows files in /mnt/c/ path

    if you have this repo in C:\ drive downloads folder the path will be 

    /mnt/c/users/{username}/Downloads/custom-rom-docs/otatools/

    change permissions first

    ```bash
    sudo chmod 755 /mnt/c/users/{username}/Downloads/custom-rom-docs/otatools/bin/*
    ```

    now set path 

    ```bash
    export PATH=/mnt/c/users/{username}/Downloads/custom-rom-docs/otatools/bin:$PATH
    ```

    now go to the path where you extracted AP tar file go to extracted folder from wsl terminal

    ```cd /mnt/c/Users/{username}/Desktop/SamFw-T220/{extracted AP folder}/
    ```

    now these steps are crucial

    install xz tool 

    ```bash
    sudo apt-get install  unxz
    sudo apt-get install lz4

    ```

    in wsl terminal in extracted AP folder.

    - Decompress the image

    ```bash
    lz4 -d super.img.lz4 super.img
    ```

    - convert the sparse super.img

    ```bash
    simg2img super.img super.ext4.img
    ```

    - Unpack ext4 .img

    ```bash
    lpunpack super.ext4.img
    ```

    now you will see system.img, vendor.img, product.img, odm.img

    ## replacing pixelexperience with system

    copy the downloaded pixel experience img.xz to extracted AP folder

    delete the system.img

    - uncompress pixelexperience.img.xz

    ```bash
    unxz pixelexperience.img.xz
    ```

    rename uncompress img from pixelexperience.img to system.img

    ## note down the partition sizes

    get all .img sizes

    ```bash
    stat -c '%n %s' *.img
    ```

    note down the partition sizes

    > :warning: this step is very crucials please pay attention to every details else it will fail

    now replace the values in the below command

    ```bash
    lpmake --metadata-size 65536 \
    --super-name super \
    --metadata-slots 2 \
    --device super:ORIGINAL_SUPER_IMG_SIZE \
    --group main:SUM_OF_ALL_PARTITIONS_SIZES \
    --partition odm:readonly:ODM_PARTITION_SIZE:main \
    --image odm=./odm.img \
    --partition product:readonly:PRODUCT_PARTITION_SIZE:main \
    --image product=./product.img \
    --partition system:readonly:SYSTEM_PARTITION_SIZE:main \
    --image system=./system.img \
    --partition vendor:readonly:VENDOR_PARTITION_SIZE:main \
    --image vendor=./vendor.img \
    --sparse \
    --output ./super_new.img
    ```

ORIGINAL_SUPER_IMG_SIZE = size of super.ext4.img (not super.img)

SUM_OF_ALL_PARTITIONS_SIZES = sum of odm + system (new )+ product + vendor

replace all the values in the above command

and paste it in wsl terminal

make sure you are in extracted AP file

if you got any error like out of space. just calculate the space 

SPACE_TO_ADD=ORIGINAL_SUPERIMAGE_SIZE - used space - requested space



add the  SPACE_TO_ADD to ORIGINAL_SUPERIMAGE_SIZE

NEW_SUPER_IMG_SIZE = SPACE_TO_ADD + ORIGINAL_SUPERIMAGE_SIZE

now replace NEW_SUPER_IMAGE_SIZE value with ORIGINAL_SUPERIMAGE_SIZE in the command

now try the new command again

you might get system space is bigger than requested error. 

just do the same calculation and replace the system size and new SUM_OF_ALL_PARTITION_SIZES

now run the command again.

you will get invalid sparse file header waring but it's a good sign




    








