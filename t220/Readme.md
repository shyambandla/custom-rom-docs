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




