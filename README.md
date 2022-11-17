# MalayaWSL

## Deprecation Notice

As [the original repo is back](https://github.com/sirredbeard/Awesome-WSL/pull/77#issuecomment-1318453821), I no longer have the intention to maintain this.
You are encouraged to download latest release from [sileshn/ManjaroWSL2](https://github.com/sileshn/ManjaroWSL2) instead.

***

UNOFFICIAL Manjaro on WSL2 (Windows 10 FCU or later) based on [wsldl](https://github.com/yuk7/wsldl).

![screenshot](https://github.com/Vinfall/ManjaroWSL/blob/main/img/screenshot.webp)

## Disclaimer

### THIS REPO IS NOT AFFILIATE TO THE OFFICIAL "MANJARO LINUX" DISTRIBUTION IN ANY WAY!

## Name

From [a post on Manjaro Linux Forum](https://forum.manjaro.org/t/origin-of-the-name-manjaro/110428), I learn that the name *Manjaro* derives from Mount Kilimanjaro. And from [this post on Manjaro Linux Forum](https://forum.manjaro.org/t/bad-use-of-manjaro-name-by-a-project/110695) and https://github.com/sirredbeard/Awesome-WSL/pull/77, it seems that someone does not like the name ~~so the original author deleted its repo~~, it's back at [sileshn/ManjaroWSL2](https://github.com/sileshn/ManjaroWSL2) with permission granted from Manjaro team.

The word *Malaya* derives from Mount Himalaya.
In Sanskrit `himá` means 'snow, frost', which may reminds you of a novel written by Ernest Hemingway named *The Snow of Kilimanjaro*, hence the name.

## 💻 Requirements
* For x64 systems: Version 1903 or higher, with Build 18362 or higher.
* For ARM64 systems: Version 2004 or higher, with Build 19041 or higher.
* Builds lower than 18362 do not support WSL 2.
* Enable Windows Subsystem for Linux feature. Open PowerShell as Administrator and run:
```cmd
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```
* Enable Virtual Machine feature. Open PowerShell as Administrator and run:
```cmd
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
* Download and install the Linux kernel update package from [here](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi).

For more details, check [this](https://docs.microsoft.com/en-us/windows/wsl/install-win10) microsoft document.

## Install
1. [Download](https://github.com/Vinfall/MalayaWSL/releases/latest) installer zip
2. Extract all files in zip file to same directory
3. Run Manjaro.exe to Extract rootfs and Register to WSL

**Note:**
Exe filename is using the instance name to register. If you rename it you can register with a diffrent name and have multiple installs.

If you want to use WSL2 after install, convert it with the following command.
```powershell
wsl --set-version Manjaro 2
```

You can also set wsl2 as default. Use the command below before running Manjaro.exe.
```powershell
wsl --set-default-version 2
```

## How-to-Use(for Installed Instance)
#### exe Usage
```
Usage :
    <no args>
      - Open a new shell with your default settings.

    run <command line>
      - Run the given command line in that distro. Inherit current directory.

    runp <command line (includes windows path)>
      - Run the path translated command line in that distro.

    config [setting [value]]
      - `--default-user <user>`: Set the default user for this distro to <user>
      - `--default-uid <uid>`: Set the default user uid for this distro to <uid>
      - `--append-path <on|off>`: Switch of Append Windows PATH to $PATH
      - `--mount-drive <on|off>`: Switch of Mount drives
      - `--default-term <default|wt|flute>`: Set default terminal window

    get [setting]
      - `--default-uid`: Get the default user uid in this distro
      - `--append-path`: Get on/off status of Append Windows PATH to $PATH
      - `--mount-drive`: Get on/off status of Mount drives
      - `--wsl-version`: Get WSL Version 1/2 for this distro
      - `--default-term`: Get Default Terminal for this distro launcher
      - `--lxguid`: Get WSL GUID key for this distro

    backup [contents]
      - `--tgz`: Output backup.tar.gz to the current directory using tar command
      - `--reg`: Output settings registry file to the current directory

    clean
      - Uninstall the distro.

    help
      - Print this usage message.
```

#### Just Run exe
```cmd
>{InstanceName}.exe
[root@PC-NAME user]#
```

#### Run with command line
```cmd
>{InstanceName}.exe run uname -r
4.4.0-43-Microsoft
```

#### Run with command line with path translation
```cmd
>{InstanceName}.exe runp echo C:\Windows\System32\cmd.exe
/mnt/c/Windows/System32/cmd.exe
```

#### Change Default User(id command required)
```cmd
>{InstanceName}.exe config --default-user user

>{InstanceName}.exe
[user@PC-NAME dir]$
```

#### Set "Windows Terminal" as default terminal
```cmd
>{InstanceName}.exe config --default-term wt
```

## How to setup

Open Manjaro.exe and run the following commands.
```dos
passwd
useradd -m -G wheel -s /bin/bash <username>
passwd <username>
exit
```
Execute the command below in a windows cmd terminal from the directory where Manjaro.exe is installed.
```dos
>Manjaro.exe config --default-user <username>
```

## How to uninstall instance
```dos
>Manjaro.exe clean
```

## How to build

### The hard way (which I use for now)

#### Prerequisites

`curl`, `jq`, `libarchive`, `tar`, `unzip` need to be installed.

#### Build rootfs

```sh
# 1. Install all the dependencies:
sudo pacman -S --needed arch-install-scripts curl jq libarchive make manjaro-tools-base-git patch tar unzip wget

# 2. Clone the repo
git clone --depth=1 https://github.com/manjaro/manjaro-docker.git
git clone --depth=1 https://github.com/Vinfall/MalayaWSL.git

# 3. Apply the patch & build the rootfs, this could take a while
cp MalayaWSL/manjaro-rootfs.patch manjaro-docker/x86_docker/
cd manjaro-docker/x86_docker
patch Makefile < manjaro-rootfs.patch
sudo make
```

#### Package WSL

```sh
# Remember to place the `base.tar` generated above inside the folder
mv base.tar ../../MalayaWSL/base.tar
cd ../../MalayaWSL
make
sha512sum Manjaro.zip > Manjaro.zip.sha512
```
Copy `Manjaro.zip` and its hash to a safe location and run the command below to clean.
```sh
make clean
```

You may check out my blog [Revive ManjaroWSL](https://blog.vinfall.com/posts/2022/10/revive-manjarowsl/) on this for details.

### The easy way (the old way)

#### Prerequisites

`curl`, `docker`, `jq`, `libarchive`, `tar`, `unzip` need to be installed.

```sh
git clone --depth=1 https://github.com/changrui/ManjaroWSL.git
git clone --depth=1 https://github.com/Vinfall/MalayaWSL.git
cp MalayaWSL/manjaro-docker.patch ManjaroWSL
cd ManjaroWSL
patch Makefile < manjaro-docker.patch
# Uncomment the following line if you use podman instead of docker
#sed -i 's/docker/podman/g' Makefile
make
```
Copy the Manjaro.zip file to a safe location and run the command below to clean.
```sh
make clean
```
