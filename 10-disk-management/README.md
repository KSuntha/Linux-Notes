# Disk and Storage Management in Linux

## 📌 Introduction to Disk and Storage Management
Managing disks and storage efficiently is crucial for system performance and stability. Linux provides various commands to monitor, partition, format, mount, and manage disk storage. In Linux, hard drives are treated as block devices and live as files inside the `/dev` directory.

---

## 🔍 Index of Commands Covered

### Viewing Disk Information
- `lsblk` – Display block devices and their mount points.
- `fdisk -l` – List disk partitions across the entire system.
- `blkid` – Show unique cryptographic UUIDs of devices.
- `df -h` – Check disk space usage in human-readable format.
- `du -sh /path` – Show total disk space usage of a specific directory.

### Partition Management
- `fdisk /dev/sdX` – Interactively create and manage partitions on MBR disks.
- `parted /dev/sdX` – Modern alternative to `fdisk` optimized for massive GPT disks.
- `mkfs.ext4 /dev/sdX1` – Format a targeted partition with the ext4 filesystem.
- `mkfs.xfs /dev/sdX1` – Format a targeted partition with the high-performance XFS filesystem.

### Mounting and Unmounting
- `mount /dev/sdX1 /mnt` – Temporarily mount a partition to a directory.
- `umount /mnt` – Unmount an active partition from the system tree.
- `mount -o remount,rw /mnt` – Remount a partition directly into read-write mode.

### Logical Volume Management (LVM)
- `pvcreate /dev/sdX` – Initialize a physical disk or partition as an LVM Physical Volume.
- `vgcreate vg_name /dev/sdX` – Group physical volumes together into a Volume Group pool.
- `lvcreate -L 10G -n lv_name vg_name` – Slice out a 10GB flexible Logical Volume from a group.
- `mkfs.ext4 /dev/vg_name/lv_name` – Format an LVM logical volume with a filesystem.
- `mount /dev/vg_name/lv_name /mnt` – Attach an LVM partition to a directory.

### Swap Management
- `mkswap /dev/sdX` – Format a designated partition sector to act as virtual Swap memory.
- `swapon /dev/sdX` – Enable the system's configured swap space buffer.
- `swapoff /dev/sdX` – Disable active swap space safely.

---

## 📊 Viewing Disk Information Deep Dive

### 1. Using `lsblk`
Lists all physical block storage devices and virtual partitions in a clean tree format:
```bash
lsblk
```

### 2. Using `fdisk`
Provides a detailed readout of lower-level partition boundaries and sector allocations:
```bash
sudo fdisk -l
```

### 3. Using `df`
Audits capacity thresholds and active usage percentages across all mounted disks:
```bash
df -h
```

### 4. Using `du`
Calculates exactly how much space is being consumed by files inside a specific directory:
```bash
du -sh /var/log
```

> 💡 **DevOps Context:** In automated environments (like Jenkins or GitHub Actions pipelines), `df -h` and `du -sh` are chained inside monitoring cronjobs to send alerts to Slack before a server runs completely out of disk space and crashes your applications.

---

## 🏗️ Partition Management & Filesystems

### 1. Creating a Partition with `fdisk`
Launches an interactive utility menu to slice up a raw drive:
```bash
sudo fdisk /dev/sdX
```
*💡 Standard Keystrokes inside fdisk:* Press **`n`** to create a new partition, **`p`** for primary, choose your sector size, and press **`w`** to write the configuration to disk.

### 2. Formatting a Partition
Before a partition can hold files, it must be stamped with a structured filesystem:
```bash
sudo mkfs.ext4 /dev/sdX1    # Formats as ext4 (Highly reliable, industry-standard choice)
sudo mkfs.xfs /dev/sdX1     # Formats as XFS (Optimized for large enterprise workloads)
```

---

## 🔌 Mounting and Unmounting

Connecting a formatted partition block to a directory path makes its storage workspace accessible:

### Mount a Partition
```bash
sudo mount /dev/sdX1 /mnt
```

### Unmount a Partition
```bash
sudo umount /mnt
```

### Remount a Partition
If a system drive error drops your OS into an emergency read-only state, force it back into an editable mode:
```bash
sudo mount -o remount,rw /mnt
```

> ⚠️ **DevOps Production Rule:** Standard `mount` commands vanish immediately when a cloud server reboots. To make mounts permanent, you must append them to the filesystem configuration table (`/etc/fstab`). Skipping this step causes production servers to lose access to their database volumes upon rebooting!

---

## 🎛️ Logical Volume Management (LVM)

LVM pools multiple physical drives together into an abstract layout so you can resize volumes on the fly without breaking systems or losing data.

```plaintext
📊 LVM Architecture Pipeline:
  [ Physical Disk /dev/sdX ]  ==>  pvcreate (Physical Volume)
              ||
  [ Volume Group Pool (VG) ]  ==>  vgcreate (Combines all PVs together)
              ||
  [ Logical Volume (LV)    ]  ==>  lvcreate (Flexible virtual partition sliced from pool)
```

### 1. Create a Physical Volume
```bash
sudo pvcreate /dev/sdX
```

### 2. Create a Volume Group
```bash
sudo vgcreate vg_name /dev/sdX
```

### 3. Create a Logical Volume
```bash
sudo lvcreate -L 10G -n lv_name vg_name
```

### 4. Format and Mount the Logical Volume
```bash
sudo mkfs.ext4 /dev/vg_name/lv_name
sudo mount /dev/vg_name/lv_name /mnt
```

> 💡 **DevOps Context:** In modern cloud platforms (AWS, Azure), data storage demands scale rapidly. LVM allows Platform Engineers to expand database directories on live production instances seamlessly without forcing server downtime or service disruptions.

---

## 🧠 Swap Management (Virtual Emergency RAM)

Swap space acts as a spillover partition on your disk. When physical RAM is 100% full, the kernel shifts inactive data to Swap to keep the system from crashing.

### Create a Swap Partition
```bash
sudo mkswap /dev/sdX
```

### Enable Swap
```bash
sudo swapon /dev/sdX
```

### Disable Swap
```bash
sudo swapoff /dev/sdX
```

> ⚠️ **DevOps Production Rule:** Kubernetes worker nodes explicitly require **Swap to be disabled (`swapoff -a`)** on the underlying Linux host. If Swap is left turned on, Kubernetes cannot accurately calculate container resource allocations, leading to unpredictable scheduling errors.

---

## 💡 Additional Technical Notes: Operational Blueprints

### Check Available Disks First
Before running partitioning or mount utilities, you must identify your system's raw storage paths:
```bash
lsblk
```

#### Example Lab Output:
```plaintext
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0  100G  0 disk 
├─sda1   8:1    0   96G  0 part /
└─sda2   8:2    0    4G  0 part [SWAP]
sdb      8:16   0   20G  0 disk 
```
* **`sda`**: Your primary system disk. It is already fully partitioned, hosting your root file system (`/`) and initial `[SWAP]` memory blocks.
* **`sdb`**: A completely fresh, unallocated 20GB disk attachment. It has no partitions (`part`) and no active mount points.

### Blueprint A: When to use `fdisk`
Use `fdisk` exclusively when dealing with raw, unassigned drives that lack a partition blueprint structure. This tool slices the disk into manageable logical targets like `/dev/sdb1` and `/dev/sdb2`.
1. Run `sudo fdisk /dev/sdb`
2. Press **`n`** ➡️ Create new partition
3. Press **`w`** ➡️ Write data mappings to disk
4. Confirm your fresh partition structural layouts are registered via `lsblk`.

### Blueprint B: When to Use `mount`
Use `mount` on devices that already contain a valid formatted partition scheme. This tool makes the storage block accessible at a target location inside your directory tree.
```bash
sudo mkdir -p /mnt/mydisk
sudo mount /dev/sdb1 /mnt/mydisk
```
Your storage expansion is now fully live and accessible at `/mnt/mydisk`!

### Blueprint C: When to Use fdisk + mount (The Complete Setup Pipeline)
Use the combined end-to-end `fdisk + mkfs + mount` pipeline when installing a brand-new raw disk expansion into a cloud infrastructure node from scratch:

```bash
# 1. Audit your block devices to target the new raw disk path
lsblk

# 2. Carve out a fresh partition scheme out of the raw drive
sudo fdisk /dev/sdb

# 3. Format the new partition slice with an active file system template
sudo mkfs.ext4 /dev/sdb1

# 4. Create an anchor directory path and lock the disk onto it
sudo mkdir -p /data
sudo mount /dev/sdb1 /data
```

> 🐳 **Docker Container Connection:** In production Docker environments, you never store persistent app data inside the container container layers. You use `mount` workflows to bind directories like `/data` (on your physical Linux host) directly to container mount points to ensure persistent database volumes are safely detached from container life cycles.

---

## 🚀 Quick Reference Summary Matrix

| Use Case / Intended Action | Command Configuration Tooling |
| :--- | :--- |
| **View active disks and mount mappings** | `lsblk` |
| **Slice a raw unallocated disk partition** | `fdisk /dev/sdX` |
| **Mount an existing, formatted block partition** | `mount /dev/sdX1 /path` |
| **Full configuration lifecycle (Raw disk deployment)** | `fdisk` ➡️ `mkfs` ➡️ `mount` |
