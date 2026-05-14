# WimTools Restore

WimTools Restore is the reduced restore-only package for the universal WindowsPE boot environment.

This repository does not build Windows PE. The PE boot environment is provided by the separate `WindowsPE` repository.

## Startup model

The universal WindowsPE loader searches the USB data partition for:

```text
\start.cmd
```

This repository provides that file at the repository root. The router then starts:

```text
\WimTools\startup.cmd
```

The restore script itself uses `%~dp0` to locate its own directory and expects the image file next to it:

```text
\WimTools\Recovery.wim
```

## USB runtime layout

Copy the contents of this repository to the NTFS data partition of the USB stick.

Expected NTFS layout:

```text
\start.cmd
\WimTools\WIMTOOLS.TAG
\WimTools\startup.cmd
\WimTools\Recovery.wim
```

The FAT32 boot partition is created from the `WindowsPE` repository output.

## Restore flow

At boot:

```text
boot.wim
-> X:\Windows\System32\startnet.cmd
-> searches all drive letters for \start.cmd
-> calls \start.cmd from the detected USB data partition
-> \start.cmd calls \WimTools\startup.cmd
-> restore script starts
```

The restore script:

```text
1. Shows available disks and volumes
2. Uses \WimTools\Recovery.wim as the image
3. Asks for the target disk number
4. Erases the selected disk
5. Creates EFI + Windows partitions
6. Applies the image to W:\
7. Writes boot files with bcdboot
8. Reboots after completion
```

## Recovery.wim

`Recovery.wim` is the image file used for restore.

Do not commit real production WIM images to the repository. If a placeholder is present, replace it locally with the real image before preparing the USB stick.

## Warning

The restore script erases the selected disk. Always verify the disk number before continuing.
