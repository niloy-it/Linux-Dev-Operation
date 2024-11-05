## LVM (Logical Volume Manager)

![](https://github.com/abrahimcse/Linux-CLI-Cheatsheet/blob/main/Images/LVM%20Overview.jpg)

To extend your existing storage using LVM (Logical Volume Manager) when you `add a new hard disk`, you'll need to follow a series of steps. This includes creating `physical volumes`, adding them to a `volume group`, and then `extending your logical volume`. Here's a detailed step-by-step guide for easy implementation:
# Steps for Adding a New Hard Disk to LVM on Linux

## Step 1: Verify the New Hard Disk
1. **List all available disks**:
    ```bash
    sudo fdisk -l
    ```
   *Identify your new disk (e.g., `/dev/sdb`).*

2. **Check the existing LVM setup**:
    - Display physical volumes:
        ```bash
        sudo pvs
        ```
    - Display volume groups:
        ```bash
        sudo vgs
        ```
    - Display logical volumes:
        ```bash
        sudo lvs
        ```

---

## Step 2: Create a Physical Volume (PV) on the New Disk
1. **Partition the new disk** *(Optional, but recommended)*:
    ```bash
    sudo fdisk /dev/sdb
    ```
    Inside `fdisk`:
    - Type `n` (new partition), then press `p` (primary), and accept the defaults.
    - Type `t` to change the partition type, and enter `8e` (Linux LVM).
    - Type `w` to write the changes and exit.

2. **Create a physical volume** on the partition:
    ```bash
    sudo pvcreate /dev/sdb1
    ```

---

## Step 3: Add the Physical Volume to the Volume Group (VG)
1. **Add the new PV to your existing volume group**:
    ```bash
    sudo vgextend your_volume_group_name /dev/sdb1
    ```
   Replace `your_volume_group_name` with the actual name of your volume group (e.g., `vg_data`).

---

## Step 4: Extend the Logical Volume (LV)
1. **Find your logical volume name**:
    ```bash
    sudo lvdisplay
    ```

2. **Extend the logical volume** by a specific size (e.g., 50GB):
    ```bash
    sudo lvextend -L +50G /dev/your_volume_group_name/your_logical_volume_name
    ```

3. **Resize the file system** on the logical volume:
    - For `ext4` file systems:
        ```bash
        sudo resize2fs /dev/your_volume_group_name/your_logical_volume_name
        ```
    - For `XFS` file systems:
        ```bash
        sudo xfs_growfs /dev/your_volume_group_name/your_logical_volume_name
        ```

---

## Step 5: Verify the Changes
1. **Check the new size of the logical volume**:
    ```bash
    sudo lvdisplay
    ```

2. **Verify the file system size**:
    ```bash
    df -h
    ```

---

## Important Notes
- Always **backup your data** before making changes.
- Be cautious with disk partitioning and volume management to avoid data loss.
