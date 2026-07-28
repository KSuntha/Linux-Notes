# Linux Directory Structure (The Filesystem Hierarchy)

In Linux, everything starts from the root directory, represented by a single forward slash (`/`). Unlike Windows, which uses separate drives (`C:`, `D:`), Linux organizes all files, folders, hardware devices, and network shares into a single unified tree structure.

---

## 🏛️ The Main Entry Point

* **`/` (Root)**: The absolute top-level directory of the filesystem. Every single file and folder on your Linux system lives inside this directory.

---

## 🔗 The Symbolic Links (The Modern Shortcuts)

In modern Linux distributions (like Ubuntu and Rocky Linux), many traditional root folders are now "symlinked" (shortcuts) to the `/usr` directory to keep the system organized.

| Directory | Core Purpose (Human-Friendly Description) |
| :--- | :--- |
| `/bin -> /usr/bin` | **Essential Commands**: Stores daily terminal commands that every user can run (like `ls`, `mkdir`, `grep`). |
| `/sbin -> /usr/sbin` | **Admin Commands**: System binaries reserved for administrators and root tasks (like `iptables`, `ip`, `reboot`). |
| `/lib -> /usr/lib` | **System Libraries**: Shared code libraries that programs need to run cleanly behind the scenes. |

---

## ⚙️ The Critical System Folders (Most Important for DevOps)

As a DevOps engineer, you will spend 90% of your time interacting with these four crucial directories:

| Directory | Core Purpose (Human-Friendly Description) | DevOps Context 💡 |
| :--- | :--- | :--- |
| `/etc` | **Configuration Hub**: Contains plain-text settings files for the system and apps (like Nginx configs or network settings). | You will edit files here constantly to configure servers. |
| `/var` | **Variable Data**: Holds dynamic files that change constantly while the system runs, like application logs and caches. | This is where you go to check application logs (`/var/log`). |
| `/usr` | **User Applications**: Contains user-installed software, documentation, and shared source files. | The destination folder for standard non-system applications. |
| `/boot` | **Startup Files**: Stores the Linux kernel, bootloader configs, and files needed to turn the machine on. | Often empty or hidden inside lightweight Docker containers. |

---

## 👤 User & Application Workspaces

| Directory | Core Purpose (Human-Friendly Description) |
| :--- | :--- |
| `/home` | **Regular Users**: The personal workspace folder for non-root users (e.g., `/home/bob`). Holds personal downloads and configs. |
| `/root` | **The Superuser**: The isolated, private home directory reserved exclusively for the root administrator account. |
| `/opt` | **Optional Software**: The standard landing spot for installing third-party tools that don't use native package managers. |
| `/srv` | **Service Data**: Intended to hold site-specific data served by the system, like raw files for web servers. |

---

## 🧠 Virtual & Volatile Folders (Memory & Processes)

These folders don't actually exist on your hard drive. They are created dynamically by the Linux Kernel inside your system memory (RAM):

| Directory | Core Purpose (Human-Friendly Description) |
| :--- | :--- |
| `/proc` | **Process Info**: A virtual window into the kernel's brain. Contains folders named after active process IDs (PIDs). |
| `/sys` | **System/Hardware Info**: A virtual directory that displays structured information about your physical hardware and drivers. |
| `/dev` | **Device Pointers**: Treats hardware components (like hard drives or terminals) as plain files (e.g., `/dev/sda` for disk 1). |
| `/run` | **Runtime Data**: Stores temporary data used by active background services since the system last booted. |
| `/tmp` | **Temporary Files**: A scratchpad for programs to write quick temporary logs. Files are automatically deleted on reboot. |

---

## 🔌 Storage Mount Points

| Directory | Core Purpose (Human-Friendly Description) |
| :--- | :--- |
| `/mnt` | **Manual Mounts**: The standard temporary folder used by sysadmins to manually attach external filesystems or network drives. |
| `/media` | **Removable Media**: The automated landing spot where plug-and-play devices like USB drives or CDs appear. |
| `/data` | **Custom Storage**: A typical user-created directory frequently used to mount persistent storage volumes from host systems (like Windows `C:/ubuntu-data`). |

---

## 🏃‍♂️ Real-World DevOps Example: Checking an Nginx Crash

When a web server crashes or behaves weirdly, you will use these folders to investigate the issue:

1. Look in **`/etc/nginx/nginx.conf`** to inspect the settings files for typos or syntax errors.
2. Check the logs in **`/var/log/nginx/error.log`** to read the error message printed by the application.
3. Verify if the background execution process is healthy by reading dynamic details inside the **`/proc`** directory.
