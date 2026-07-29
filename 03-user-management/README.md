# Linux User & Group Management: Securing System Access

Linux is a multi-user operating system. Proper user and group management ensures system security, resource isolation, and explicit access control. 

---

## 📁 The 4 Critical Security Files

Every user, group, and password configuration on a Linux system is stored in these four plain-text files:

| File Path | Core Contents & Purpose | DevOps Importance 💡 |
| :--- | :--- | :--- |
| `/etc/passwd` | Stores account details like User IDs (UID), Group IDs (GID), home paths, and default shells. | Readable by everyone; useful for checking if a user exists. |
| `/etc/shadow` | Holds the actual encrypted account passwords and expiration policies. | Super secure. Only readable by root. |
| `/etc/group` | Defines all system groups and lists which users belong to them. | Used to quickly check team permissions. |
| `/etc/gshadow` | Contains secure, encrypted group passwords and administrator settings. | Rarely modified manually in modern cloud setups. |

---

## 👤 Creating & Customizing Users

There are two primary commands to create accounts, depending on your distribution family:

### 1. The Low-Level Script Tool (`useradd`)
Used across all Linux environments and ideal for automated shell scripts.
```bash
sudo useradd bob              # Creates a basic user account
sudo useradd -m alice         # Explicitly forces creation of a home directory (/home/alice)
sudo useradd -s /bin/bash sam # Specifies a default login shell for the user
```

### 2. The Interactive High-Level Tool (`adduser`)
A user-friendly, interactive wrapper script common on Debian and Ubuntu systems.
```bash
sudo adduser charlie          # Automatically creates home folder, prompts for passwords, and sets details
```

---

## 🔒 Managing Passwords & Account Locking

```bash
sudo passwd username          # Set or change a user's password
sudo chage -M 90 username     # Force the user to change their password every 90 days
sudo passwd -l username      # Lock the account (stops user from logging in)
sudo passwd -u username      # Unlock the account (restores login access)
```

---

## 🛠️ Modifying & Deleting Accounts (`usermod` / `userdel`)

### Modifying Accounts
```bash
sudo usermod -l newname oldname           # Change the account login username
sudo usermod -s /bin/zsh username         # Switch the user's default shell utility
sudo usermod -d /custom/home -m username   # Move the user to a new home directory layout
```

### Deleting Accounts
```bash
sudo userdel username        # Deletes the user account but leaves their home directory files behind
sudo userdel -r username     # Clean Sweep: Deletes the user AND completely erases their home folder
```

---

## 👥 Managing Collaborative Groups

Groups allow you to apply the same read/write permissions to multiple team members at the exact same time.

```bash
sudo groupadd devops                  # Create a brand new group
sudo usermod -aG devops username     # Add an existing user to the devops group
sudo groups username                  # View all groups a specific user belongs to
sudo usermod -g new_primary username  # Change a user's core Primary Group
```

⚠️ **The Golden Rule of Group Modification:** Always include the lowercase `-a` (append) flag alongside `-G`. Running `usermod -G groupname username` without `-a` will accidentally **remove** the user from all their other existing groups!

---

## 👑 Sudo Access & Privilege Escalation

The `sudo` command lets regular users execute administrative actions safely without logging into the dangerous raw root account.

### Granting Global Admin Rights
Instead of editing configuration files manually, add users directly to your distro's default administrator group:
* **Ubuntu/Debian systems**: `sudo usermod -aG sudo username`
* **RHEL/Rocky Linux systems**: `sudo usermod -aG wheel username`

### Granting Specific Automation Privileges (`visudo`)
To let an automation tool or user run a single, specific administrative command without prompting them for a password, run:
```bash
sudo visudo
```
Scroll to the bottom of the file and insert this exact layout block:
```plaintext
username ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx
```

---

## 🏃‍♂️ Real-World DevOps Scenario: Provisioning a New Engineer

When a new cloud engineer joins your development squad, you automate their onboarding using these exact system blocks:

1. Create their workspace securely: `sudo useradd -m -s /bin/bash developer1`
2. Set a temporary initial login key: `sudo passwd developer1`
3. Attach them to the deployment group so they can edit code: `sudo usermod -aG devops developer1`

