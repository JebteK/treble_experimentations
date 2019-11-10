# Pre-requisites
* Ubuntu 16.04
* Access to the JebteK private repos
* [Java SDK for Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04)

# How to build

* clone this repository
* call the build scripts from a separate directory

```
    git clone https://github.com/JebteK/treble_experimentations
    mkdir HollerdOS; cd HollerdOS
    bash ../treble_experimentations/build-dakkar.sh hollerd151 arm-aonly-vanilla-su-userdebug
```

# Setting up a new machine
## 1. Configure Git

Configure git to save credentials globally
```git config --global credential.helper store```

Try to clone a private repo
``` 
mkdir ~/temp;cd ~/temp
git clone https://github.com/JebteK/Hollerd-www.git
```

Enter your username and password when prompted. Then delete the temp repo.

```rm -rf ~/temp```

You’ll also need to set up git identity in order to sync the source. To do that, run these commands:

```
git config --global user.name "Your Name"
git config --global user.email alias@hollerd.com
```

## 2. Install Java

```sudo apt-get update```

```sudo apt-get -y install default-jdk```

## 3. Install SDK
If you haven’t previously installed `adb` and `fastboot`, you can [download them from Google](https://dl.google.com/android/repository/platform-tools-latest-linux.zip).
Extract it using: ```unzip platform-tools-latest-linux.zip -d ~```

Now we have to add `adb` and `fastboot` to our path. Open ~/.profile and add the following:

```
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
```

Then, run this to update your environment. ```source ~/.profile```

## 4. Install build packages

Several packages are needed to build Android. You’ll need to run:

```
sudo apt install -y bc bison build-essential curl flex g++-multilib gcc-multilib git gnupg gperf imagemagick lib32ncurses5-dev
lib32z1-dev liblz4-tool libncurses5-dev libsdl1.2-dev libwxgtk3.0-dev unzip zip
libxml2 libxml2-utils lunzip lzop pngcrush schedtool squashfs-tools xsltproc zip zlib1g-dev openjdk-8-jdk python perl
```

Sometimes you may need to install `python` again as it might have errored out:
```sudo apt install python```

## 5. Install the repo command

Run these commands to download the repo binary and make it executable:

```
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

Put the ~/bin directory in your `path`

In recent versions of Ubuntu, ~/bin should already be in your PATH. You can check this by opening ~/.profile with a text editor and verifying the following code exists (add it if it is missing):

```
# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```

Then, use this to update your environment.

```source ~/.profile```

## 6. Turn on caching to speed up the build process

You can speed up subsequent builds by running:

```
export USE_CCACHE=1
export CCACHE_COMPRESS=1
export CCACHE_MAXSIZE=50G # 50 GB
```

And adding these lines to your ~/.bashrc file. 

You can configure the maximum amount of disk space you want the cache to use by editing `CCACHE_MAXSIZE` consequently. Anywhere from 25GB-100GB will result in very noticeably increased build speeds (for instance, a typical 1hr build time can be reduced to 20min). If you’re only building for one device, 25GB-50GB is fine. If you plan to build for several devices that do not share the same kernel source, aim for 75GB-100GB. This space will be permanently occupied on your drive, so take this into consideration.

# Using Docker - NOT VERIFIED

clone this repository, then:

    docker build -t treble docker/
    
    docker container create --name treble treble
    
    docker run -ti \
        -v $(pwd):/treble \
        -v $(pwd)/../treble_output:/treble_output \
        -w /treble_output \
        treble \
        /bin/bash /treble/build-dakkar.sh rr \
        arm-aonly-gapps-su \
        arm64-ab-go-nosu

# Conventions for commit messages:

* `[UGLY]` Please make this patch disappear as soon as possible
* `[master]` tag means that the commit should be dropped in a future
  rebase
* `[device]` tag means this change is device-specific workaround
* `::device name::` will try to describe which devices are concerned
  by this change
* `[userfriendly]` This commit is NOT used for hardware support, but
  to make the rom more user friendly
