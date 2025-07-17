# Rocky Linux Disk Resizing Procedure

This document outlines the steps required to resize disk partitions and logical volumes on a Rocky Linux system.

## Prerequisites

* Ensure you have root or sudo access.
* Backup important data before performing disk resizing operations.

## Step 1: Install Required Packages

Install the necessary tools for disk management:

```bash
sudo yum -y install cloud-utils-growpart gdisk
```

## Step 2: Verify Current Disk Layout

List block devices to confirm the current partition structure:

```bash
lsblk
```

## Step 3: Extend the Partition Using growpart

Identify the device and partition number. Example for `/dev/sda2`:

```bash
sudo growpart /dev/sda 2
```

Verify changes again:

```bash
lsblk
```

## Step 4: Resize the Filesystem

If your filesystem is `ext4`, use:

```bash
sudo resize2fs /dev/sda2
```

If your filesystem is `xfs` (common on Rocky Linux), use:

```bash
sudo xfs_growfs /
```

*Note: Use `xfs_growfs` only if `/` is an XFS filesystem.*

## Step 5: Extend Logical Volume (If Using LVM)

Extend the logical volume and resize the filesystem in a single command:

```bash
sudo lvextend -r -l +100%FREE /dev/mapper/rl-root
```

* `-r` resizes the filesystem automatically.
* `-l +100%FREE` uses all available free space.

## Step 6: Verify Disk Space

Check the updated disk space:

```bash
df -h
```

---

**Notes:**

* Always confirm whether your setup uses LVM or standard partitions.
* `growpart` is safe for GPT and MBR partitions but requires caution.
* `lvextend` and `xfs_growfs` only apply to LVM setups.
