# Limine Configuration — README

## Overview

This folder contains a pre-configured Limine (bootloader) config file to give you a smoother, ready-to-go boot setup for Arch Linux.

> ⚠️ **Important:** Unlike the other configs in `tastyConfigFiles`, this one **cannot simply be copied and used as-is**. Before applying it, you must edit the file and set the correct **root UUID** for each boot entry. Instructions below.

## Step 1: Find your root partition's UUID

Each boot entry in the Limine config needs to know the UUID of the partition it should boot from. To find your root partition's UUID, run:

```bash
lsblk -f
```

or alternatively:

```bash
sudo blkid
```

Look for the partition that is mounted as `/` (your root partition) and copy its `UUID` value. It will look like a long string of letters and numbers separated by dashes

> 💡 If you have multiple boot entries (e.g., separate kernels, fallback entries, or multiple installs), you will need the UUID for **each** corresponding root partition.

## Step 2: Set the root UUID in each boot entry

Open the provided config file and locate each boot entry's `CMDLINE` (or kernel command line) option. You'll see a line similar to:

```
CMDLINE=root=UUID=REPLACE_WITH_YOUR_UUID rw
```

Replace `REPLACE_WITH_YOUR_UUID` with the UUID you copied in Step 1. **Do this for every boot entry in the file** — each one needs its own correct UUID (they may be the same UUID if all entries boot from the same root partition, or different if you have multiple installs).

## Step 3: Apply the configuration

Once every boot entry has the correct UUID set, copy the config file into place:

```bash
sudo cp -f YetAnotherArchInstall/tastyConfigFiles/limine.conf /boot/limine.conf
```

> Double-check that `/boot/limine.conf` is the correct path for your setup — some installations may use a different location (e.g., `/boot/limine/limine.conf`) depending on how Limine was installed.

## Step 4: Update Limine

After copying the file, regenerate/update Limine so it picks up the new configuration:

```bash
sudo limine-update
```
> This command requires the `limine-entry-tool` package to be installed
>
(Or use whichever Limine update command applies to your specific installation method.)

## Notes

- If you have an existing Limine configuration you want to keep, back it up first:
  ```bash
  sudo cp /boot/limine.conf /boot/limine.conf.bak
  ```
- **Do not skip Step 2** — if the UUID is left as a placeholder or set incorrectly, your system will fail to boot.