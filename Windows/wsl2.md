Copyright (c) 2023-2024 Intercreate, Inc. 

# Summary

This guide provides instructions for setting up an environment for developing, debugging, and programming embedded systems firmware in the Windows Subsystem for Linux (WSL2).

# Table of Contents

- [Dependencies](#dependencies)
- [Setup WSL2 on First Boot](#setup-wsl2-on-first-boot)
- [Revision History](#revision-history)

# Dependencies

This section should be completed in order.

## Required Dependencies

### Windows 11

While many of these same techniques are possible in Windows 10, WSL2 in Windows 10 is no longer receiving important feature updates. It is recommended to run Windows Update until up to date.

[Installation Documentation](https://support.microsoft.com/en-us/windows/ways-to-install-windows-11-e0edbbfb-cfc5-4011-868b-2ce77ac7c70e)

---

### Windows Terminal

If you don't have Windows Terminal yet, you'll need to use PowerShell to install it.  After this, Windows Terminal is your interface to PowerShell.

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

To avoid ambiguity, any reference to the use of a "Windows Terminal", "terminal", "CLI", "command line", etc. assumes that the terminal being used is Windows Terminal using PowerShell 5 (default) or PowerShell 7 (recommended).

### Ubuntu 24.04 LTS on Windows Subsystem for Linux 2 (WSL2)

These days, WSL2 is the default instead of WSL1.  If you are on a fresh install, you can assume that WSL2 is being used.  If you are worried that you might be on WSL1, then [upgrade version from WSL 1 to WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install#upgrade-version-from-wsl-1-to-wsl-2).

Open PowerShell (or Windows Terminal) in administrator mode by right-clicking and selecting "Run as administrator".  Enter the following wsl install command and then restart your machine.

```powershell
wsl --install -d Ubuntu-24.04 --web-download
```

> Note: Although the Microsoft documentation claims this is done automatically, you may have to manually enable the virtual machine optional component. In this case you may see the error message "WslRegisterDistribution failed with error: 0x80370114". It can be fixed by enabling the Virtual Machine Platform.
>
> Go to Control Panel &rarr; Programs &rarr; Programs and Features &rarr; Turn Windows Features On or Off.
>
> Make sure that "Virtual Machine Platform" is checked.

[Installation Documentation](https://learn.microsoft.com/en-us/windows/wsl/install)

#### About Distros

J.P. says: "of the available options, I recommend Ubuntu 24.04 LTS"

See `wsl --list --online` for more info.

#### About Hardware Configuration

You will need to enable virtualization support in your system BIOS if it is not already enabled.  On Intel this is called VT-d and an AMD it is called AMD-V.  See your laptop/motherboard manufacturer's documentation.

Further reading:

- [Set up a WSL development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
- [Run multiple distros/instances](https://learn.microsoft.com/en-us/windows/wsl/install#ways-to-run-multiple-linux-distributions-with-wsl)

---

### USBIPD Installation using the WSL USB GUI

USB IP Device allows pass through of USB devices (serial, debugger, etc.) from Windows to WSL2. Install the WSL USB GUI [most current release](https://gitlab.com/alelec/wsl-usb-gui/-/releases).

> Note: Windows will likely complain that the software is unsigned.

Further documentation for USB forwarding setup without using the GUI found below:

- [Source and docs](https://github.com/dorssel/usbipd-win)

- [Microsoft write-up](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)
---

## Recommended Tools

### VSCode

```powershell
winget install -e --id Microsoft.VisualStudioCode
```

Or installer: https://code.visualstudio.com/download#

---

# Setup WSL2 on First Boot

## Create a User

- Open WSL2 by right-clicking on Terminal and selecting Ubuntu 22.04 LTS
  - Or, in an already open Terminal, from the top bar, dropdown V to display shells
  - You can set defaults from the Terminal Settings
- Choose a short and normal username, such as your first name, all lowercase
- Choose a short and easy password (J.P. says: "just use `admin` or similar - if a bad actor has gotten this far they already have access to your entire Windows OS")

---

## Get Common Dependencies

- Update packages: `sudo apt update && sudo apt upgrade -y`
- Install suggested packages:
  ```
  sudo apt install --no-install-recommends git cmake ninja-build gperf \
  ccache dfu-util device-tree-compiler wget build-essential tio python3-venv \
  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
  make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1 firefox clang-format
  ```
  - Test WSL2 GUI:
    ```
    firefox
    ```
- Skip this step if running Ubuntu 24.04. If running Ubuntu 22.04 or 20.04, get a newer CMake:
  ```
  wget https://apt.kitware.com/kitware-archive.sh
  sudo bash kitware-archive.sh
  sudo apt update && sudo apt upgrade -y
  ```
- Additional information on USB/IP client tools can be found [here](https://github.com/dorssel/usbipd-win/wiki/WSL-support#usbip-client-tools).

---

## Setup Git

- Set your global git config (same as in Windows):
  ```
  git config --global user.name "John Doe"
  git config --global user.email johndoe@example.com
  ```
- Create and add SSH keys to GitHub and BitBucket (as necessary):
  - [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?platform=linux)
  - [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-personal-ssh-keys-on-linux/)

---

## Install Segger J-Link Software

- From WSL - launch Firefox:
  ```
  firefox
  ```
- Using Firefox, [download the latest Linux 64-bit DEB installer](https://www.segger.com/downloads/jlink/).
- Enter the following commands to copy the installer to root directory and install:
  ```
  cd ~
  cp ~/snap/firefox/common/Downloads/<name_of_file> ~/
  sudo apt install ./<name_of_file>
  # The previous command will likely result in dependency issues.
  sudo apt-get -f install
  rm <name_of_file>
  ```

---

## Test VSCode

- If you are using VSCode, enter `code .` from a WSL2 Terminal to understand how to open VSCode from within WSL2.  Typically this command would be used after `cd` to a repository root.

## Google Chrome

- Install Google Chrome:
  ```
  cd ~
  wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
  sudo apt install ./google-chrome-stable_current_amd64.deb
  rm google-chrome-stable_current_amd64.deb
  ```
  - Test:
    ```
    google-chrome
    ```

# Revision History

## 2023-06-25
 
- Author: J.P. Hutchins
- Initial outline and draft
- Add instructions to get clang-format 17

## 2023-07-14

- Author: Alden Haase
- Formatting corrections
- Correct JLink install instructions
- Add note about enabling virtual machine platform in Windows
- Add note about Windows warning for unsigned software
- Add bellStyle config

## 2023-07-17

- Author: Alden Haase
- Improve clarity
- Disambiguate which system packages should be installed to (Windows vs WSL2)

## 2023-10-06

- Author: Michael Brust
- Add error # for clarity

## 2023-10-31 🎃

- Author: J.P. Hutchins
- Split the native Windows and WSL2 documents

## 2023-11-02

- Author: J.P. Hutchins
- Update and upgrade after running the Kitware script
- Remove email addresses
