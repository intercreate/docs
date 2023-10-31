Copyright (c) 2023 Intercreate, Inc. 

# Summary

These guide provides instructions for setting up an environment for developing, debugging, and programming embedded systems firmware in Windows, both natively or via the Windows Subsystem for Linux (WSL2).

Anecdotally, WSL2 builds are faster.  Native builds in Windows can be sped up somewhat by making sure that Windows Defender or other antivirus is not actively scanning.

# Links

- [Windows Development Environment](windows.md)
- [WSL2 Development Environment](wsl2.md)

# Revision History

## 2023-06-25
 
- Author: J.P. Hutchins <jp@intercreate.io>
- Initial outline and draft
- Add instructions to get clang-format 17

## 2023-07-14

- Author: Alden Haase <alden@intercreate.io>
- Formatting corrections
- Correct JLink install instructions
- Add note about enabling virtual machine platform in Windows
- Add note about Windows warning for unsigned software
- Add bellStyle config

## 2023-07-17

- Author: Alden Haase <alden@intercreate.io>
- Improve clarity
- Disambiguate which system packages should be installed to (Windows vs WSL2)

## 2023-10-06

- Author: Michael Brust  <mike@intercreate.io>
- Add error # for clarity

## 2023-10-31 🎃

- Author: J.P. Hutchins <jp@intercreate.io>
- Split the native Windows and WSL2 documents
