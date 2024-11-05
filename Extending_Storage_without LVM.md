
# Extending Storage in Linux without LVM: Step-by-Step Guide

Expanding storage without using LVM (Logical Volume Manager) is more rigid, but still possible.

---

## Method 1: Adding a New Disk and Mounting It to a Directory

### Step 1: Identify the New Disk
1. **List all available disks and partitions**:
    ```bash
    sudo fdisk -l
    ```
   *Find the new disk (e.g., `/dev/sdb`).*

### Step 2: Partition the New Disk
1. **Create a new partition on the disk**:
    ```bash
    sudo fdisk /dev/sdb
    ```
   Inside `fdisk`:
   - Type `n` to create a new partition.
   - Press `Enter` to accept the default values for partition type, start, and end sectors.
   - Type `w` to write the changes and exit.

### Step 3: Format the New Partition
1. **Format the partition with ext4 file system**:
    ```bash
    sudo mkfs.ext4 /dev/sdb1
    ```
   *(Adjust the file system type if needed, e.g., `mkfs.xfs` for XFS.)*

### Step 4: Create a Mount Point
1. **Create a directory to mount the partition**:
    ```bash
    sudo mkdir /mnt/newdisk
    ```

### Step 5: Mount the New Partition
1. **Mount the partition to the new directory**:
    ```bash
    sudo mount /dev/sdb1 /mnt/newdisk
    ```

### Step 6: Verify the Mount
1. **Check the mounted partitions**:
    ```bash
    df -h
    ```

### Step 7: Automate the Mount at Boot
1. **Edit the `/etc/fstab` file**:
    ```bash
    sudo vim /etc/fstab
    ```
   Add the following line to `/etc/fstab`:
    ```bash
    /dev/sdb1   /mnt/newdisk   ext4   defaults   0   0
    ```
   This ensures the partition is automatically mounted at startup.

---

## Method 2: Using Symbolic Links

### Step 1: Move Data to the New Disk
1. **Move a large directory to the new disk**:
    ```bash
    sudo mv /path/to/large/data /mnt/newdisk/large_data
    ```

### Step 2: Create a Symbolic Link
1. **Link the moved directory to its original location**:
    ```bash
    sudo ln -s /mnt/newdisk/large_data /path/to/large/data
    ```
   This allows the system to access the directory from its original path, even though the data is now on the new disk.

---

## Example Commands (Summary)

1. **Check existing partitions and disks**:
    ```bash
    sudo fdisk -l
    ```

2. **Create and format a new partition**:
    ```bash
    sudo fdisk /dev/sdb
    sudo mkfs.ext4 /dev/sdb1
    ```

3. **Mount a partition**:
    ```bash
    sudo mount /dev/sdb1 /mnt/newdisk
    ```

4. **Automate the mount using `/etc/fstab`**:
    ```bash
    /dev/sdb1   /mnt/newdisk   ext4   defaults   0   0
    ```

---

## Important Notes
- **Backup your data** before making any changes to your partitions or file systems to prevent data loss.
