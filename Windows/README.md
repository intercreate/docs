Copyright (c) 2023 Intercreate, Inc. 

# Summary

This guide provides instructions for setting up an environment for developing, debugging, and programming embedded systems firmware in the Windows Subsystem for Linux (WSL2).

Some additional notes, marked with 🪟, are provided for developing from Windows 11 natively with the singular compromise that build times are (anecdotally) slower.  It is recommended to add these tools to Windows 11 but it is not necessary for using WSL2 as a development environment.

# Table of Contents

- [Dependencies](#dependencies)
- [Revision History](#revision-history)

# Dependencies

This section should be completed in order.

## Required Dependecies

### Windows 11

While many of these same techniques are possible in Windows 10, WSL2 in Windows 10 is no longer receiving important feature upates.

[Installation Documentation](https://support.microsoft.com/en-us/windows/ways-to-install-windows-11-e0edbbfb-cfc5-4011-868b-2ce77ac7c70e)

---

### Windows Terminal

Because you don't have Windows Terminal yet, you'll need to use PowerShell to install it.  After this, Windows Terminal is your interface to PowerShell.

```powershell
winget install --id Microsoft.WindowsTerminal -e
```

#### Configuration

- Pin Terminal to the taskbar.
- Open PowerShell as administrator and [allow local PS scripts to run](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.3#remotesigned):

  ```powershell
  Set-ExecutionPolicy RemoteSigned
  ```
- In Windows Terminal, increase your history size.
  - Press `Ctrl Shift ,` to open your Windows Terminal [settings.json](https://learn.microsoft.com/en-us/windows/terminal/install#settings-json-file)
  - Under the top level field `"profiles"`, find the `"defaults"` field or create it.
  - Edit the `"defaults"` object to have `"historySize": 100000` (though apparently 32767 is max), for example:
    ```json
    "defaults": 
    {
        "historySize": 100000
    },
    ```
- Optionally turn off the bell.
    ```json
    "defaults":
    {
        "bellStyle": "none",
        "historySize": 100000
    },
    ```
[Installation Documentation](https://github.com/microsoft/terminal#installing-and-running-windows-terminal)

To avoid ambiguity, any reference to the use of a "Windows Terminal", "terminal", "CLI", "command line", etc. assumes that the terminal being used is Windows Terminal using PowerShell 5 (default) or [PowerShell 7](#powershell-7) (recommended).

---

### 🪟 Git

```powershell
winget install --id Git.Git -e --source winget
```

#### Configuration

- Set your global git config:
  ```
  git config --global user.name "John Doe"
  git config --global user.email johndoe@example.com
  ```
- Create and add SSH keys to GitHub and BitBucket (as necessary):
  - [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?platform=windows)
  - [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-windows/)

[Installation Documentation](https://git-scm.com/download/win)

---

### Ubuntu 22.04 LTS on Windows Subsystem for Linux 2 (WSL2)

These days, WSL2 is the default instead of WSL1.  If you are on a fresh install, you can assume that WSL2 is being used.  If you are worried that you might be on WSL1, then [upgrade version from WSL 1 to WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install#upgrade-version-from-wsl-1-to-wsl-2).

Open PowerShell (or Windows Terminal) in administrator mode by right-clicking and selecting "Run as administrator".  Enter the following wsl install command and then restart your machine.

```powershell
wsl --install -d Ubuntu-22.04
```

> Note: Although the Microsoft documentation claims this is done automatically, you may have to manually enable the virtual machine optional component.
>
> Go to Control Panel &rarr; Programs &rarr; Programs and Features &rarr; Turn Windows Features On or Off.
>
> Make sure that "Virtual Machine Platform" is checked off.

After completing other Windows steps, [setup your WSL2](#Setup-WSL2).

[Installation Documentation](https://learn.microsoft.com/en-us/windows/wsl/install)

#### About Distros

J.P. says: "of the available options, I recommend Ubuntu 22.04 LTS"

See `wsl --list --online`:

```
The following is a list of valid distributions that can be installed.
Install using 'wsl.exe --install <Distro>'.

NAME                                   FRIENDLY NAME
Ubuntu                                 Ubuntu
Debian                                 Debian GNU/Linux
kali-linux                             Kali Linux Rolling
Ubuntu-18.04                           Ubuntu 18.04 LTS
Ubuntu-20.04                           Ubuntu 20.04 LTS
Ubuntu-22.04                           Ubuntu 22.04 LTS
OracleLinux_7_9                        Oracle Linux 7.9
OracleLinux_8_7                        Oracle Linux 8.7
OracleLinux_9_1                        Oracle Linux 9.1
SUSE-Linux-Enterprise-Server-15-SP4    SUSE Linux Enterprise Server 15 SP4
openSUSE-Leap-15.4                     openSUSE Leap 15.4
openSUSE-Tumbleweed                    openSUSE Tumbleweed
```

#### About Hardware Configuration

You will need to enable virtualization support in your system BIOS if it is not already enabled.  On Intel this is called VT-d and an AMD it is called AMD-V.  See your laptop/motherboard manufacturer's documentation.

Further reading:

- [Set up a WSL development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
- [Run multiple distros/instances](https://learn.microsoft.com/en-us/windows/wsl/install#ways-to-run-multiple-linux-distributions-with-wsl)

---

### USBIPD

USB IP Device allows pass through of USB devices (serial, debugger, etc.) from Windows to WSL2.

```powershell
winget install usbipd
```

[Source and docs](https://github.com/dorssel/usbipd-win)

[Microsoft write-up](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)

#### wsl-usb-gui

Strongly recommended to add this GUI for simplicity.

> Note: Windows will likely complain that the software is unsigned.

[wsl-usb-gui Releases](https://gitlab.com/alelec/wsl-usb-gui/-/releases)

---

### 🪟 Python

At the time of writing, the [Microsoft Store](https://www.microsoft.com/store/productId/9NRWMJP3717K) has a more up to date version than Winget.

#### About Python Versions

- Use the latest Python (3.11.4 at time of writing)
- Worry more about your code being "forward-compatible" than "backward-compatible": **do not ignore deprecation warnings!**
- Set and use aliases:
  - From Start, type "Manage app execution aliases" and click on it (gets you to Settings>Apps>Advanced app settings>App execution aliases)
  - Make sure that both `python` and `python3` point to `python3.11` (latest)
  - When writing docs, specificy use of `python3` for Linux compatability.

---

## Recommended Tools

### VSCode

```powershell
winget install -e --id Microsoft.VisualStudioCode
```

Or installer: https://code.visualstudio.com/download#

---

### 🪟 PowerShell 7

```powershell
winget install --id Microsoft.Powershell --source winget
```

"PowerShell 7 is a new edition of PowerShell that is cross-platform (Windows, macOS, and Linux), open-source, and built for heterogeneous environments and the hybrid cloud."

J.P. says: "it supports `&&` making the interface much more compatible with `sh`.  Unaware of downsides."

---

### 🪟 Chocolatey

Chocolatey is a package manager for Windows that still has a few packages that are not on Winget.

[Installation Documentation](https://chocolatey.org/install#individual).  _If you have to change the global execution policy, make sure to change it back to RemoteSigned after Chocolatey is installed_

#### Configuration

```powershell
choco feature enable -n allowGlobalConfirmation
```

---

### 🪟 Windows Build Tools

Requires [Chocolatey](#🪟-Chocolatey)

```
choco install cmake --installargs 'ADD_CMAKE_TO_PATH=System'
choco install ninja gperf dtc-msys2 wget 7zip
```

Note that some of these are also available from Winget or stand alone installers.

---

# Setup WSL2

## First Boot

- Open WSL2 by right-clicking on Terminal and selecting Ubuntu 22.04 LTS
  - Or, in an already open Terminal, from the top bar, dropdown V to display shells
  - You can set defaults from the Terminal Settings
- Choose a short and normal username, such as your first name, all lowercase
- Choose a short and easy password (J.P. says: just use `admin` or similar - if a bad actor has gotten this far they already have access to your entire Windows OS)
- Update packages: `sudo apt update && sudo apt upgrade -y`
- Install suggested packages:
  ```
  sudo apt install --no-install-recommends git cmake ninja-build gperf \
  ccache dfu-util device-tree-compiler wget build-essential tio python3-venv \
  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
  make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1 firefox
  ```
- Install the USB/IP client tools [reference](https://github.com/dorssel/usbipd-win/wiki/WSL-support#usbip-client-tools)
  ```
  sudo apt install linux-tools-generic hwdata
  sudo update-alternatives --install /usr/local/bin/usbip usbip `ls /usr/lib/linux-tools/*/usbip | tail -n1` 20
  ```
- If you are using VSCode, enter `code .` to understand how to open VSCode from within WSL2.  Typically this command would be used after `cd` to a repository root.
- Set your global git config (same as in Windows):
  ```
  git config --global user.name "John Doe"
  git config --global user.email johndoe@example.com
  ```
- Create and add SSH keys to GitHub and BitBucket (as necessary):
  - [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?platform=linux)
  - [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-linux/)
- [Install Segger JLink](https://www.segger.com/downloads/jlink/).  Visit the page and download the correct package.
  ```
  cd ~
  cp mnt/c/Users/<your_user_name_>/Downloads/<name_of_file> ~/
  sudo dpkg -i <name_of_file>
  # The previous command will likely result in dependency issues.
  sudo apt-get -f install
  rm <name_of_file>
  ```
- Test USB pass through using the USBIPD CLI or wsl-usb-gui
- Install Google Chrome:
  ```
  cd ~
  wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
  sudo dpkg -i google-chrome-stable_current_amd64.deb
  rm google-chrome-stable_current_amd64.deb
  ```
  Test GUI with `google-chrome`.
- Install `clang-format` 17:
  ```
  sudo add-apt-repository 'deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy main'
  sudo apt update
  wget https://apt.llvm.org/llvm-snapshot.gpg.key
  sudo apt-key add llvm-snapshot.gpg.key
  sudo apt update
  sudo apt install clang-format-17
  ```
  Check: `clang-format --version`

# Revision History

## 2023-06-25
 
- Author: J.P. Hutchins <jp@intercreate.io>
- Initial outline and draft
- Add instructions to get clang-format 17
