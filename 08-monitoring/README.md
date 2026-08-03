# Linux System Monitoring: Resource Tracking & Performance Diagnostics

Monitoring system resources is essential to guarantee application uptime, prevent resource exhaustion, and isolate system bottlenecks. A DevOps engineer must constantly audit four core hardware pillars: CPU computation, RAM memory health, Disk I/O storage capacity, and Network data streams.

---

## 🧠 1. CPU & Memory Metrics (Compute Health)

When applications slow down or stop responding, use these monitoring utilities to measure your compute footprint:

```bash
free -h        # Human-Readable Memory: Displays total, used, free, and cached RAM capacity using easy units (GB, MB).
vmstat 1 5     # Virtual Memory Stats: Streams a grid summarizing processes, RAM usage, swap space swaps, and CPU traps.
top            # Real-Time Master: The native, real-time interactive task manager checking overall system load averages.
htop           # Modern Dashboard: Visual dashboard tracking multi-core CPU balances and memory leaks.
```

*Note: Always prefer `free -h` over `free -m`. Using `-h` automatically adjusts to the most logical unit size (Giga, Mega) so human brains can scan it instantly.*

---

## 🎛️ 2. Disk Space & Storage I/O (Capacity Tracking)

Storage leaks can quietly halt database operations or crush logging mechanisms. Audit your persistent storage volumes using these tools:

| Command Syntax | Core Monitoring Objective | DevOps Context & Importance 💡 |
| :--- | :--- | :--- |
| `df -h` | **Disk Free**: Displays storage health, mount mappings, and percentage used metrics across all attached volumes. | Always run this first if a system suddenly starts throwing write errors. |
| `du -sh /path/*` | **Disk Usage**: Tallies and outputs a summary total of the exact block space consumed by a specific folder path. | Perfect for tracking down exactly which project folder is wasting server disk storage. |
| `iostat 1 3` | **Input/Output Stats**: Monitors real-time read/write speeds, data transfer loads, and physical drive strain levels. | Used to detect if a slow database is causing an input/output bottleneck. |

---

## 🌐 3. Network Interfaces & Port Diagnostics (Connection Traffic)

Use these commands to trace how traffic flows into your system or troubleshoot why microservices cannot establish connections:

```bash
ip a           # IP Address: Displays active network interfaces, hardware MAC links, and assigned IP configurations.
netstat -tulnp # Network Status: Lists all active internet sockets, showing exactly which processes occupy specific local ports.
ss -tulnp      # Socket Statistics: The modernized, faster alternative replacement utility for the legacy `netstat` command stack.
ping -c 4 host # Packet Test: Transmits 4 basic echo requests to confirm network layer contact with a target remote machine.
traceroute host# Path Trace: Maps out every network router hop your data passes through to navigate to a target system destination.
nslookup domain# DNS Lookup: Queries nameservers directly to confirm if a domain translates correctly to its destination IP address.
```

```plaintext
💡 Deconstructing Socket Flags (-tulnp):
  -t : Show TCP sockets    -u : Show UDP sockets    -l : Show Listening ports
  -n : Show numeric IPs    -p : Show Process PID mapping details
```

---

## 📄 4. Production Log Triage (System Auditing)

When background application logic breaks without throwing an interactive terminal error, use log streams to analyze historical event lines:

```bash
tail -f /var/log/syslog      # Follow Logs: Keeps the file stream open on your screen, updating live as system events occur.
journalctl -f                # Systemd Journal: Streams the live master logs handled by the systemd service controller framework.
dmesg | tail -n 20           # Device Messages: Peeks inside the kernel's hardware rings to diagnostic drivers or memory failures.
```

---

## 🏃‍♂️ 5. Real-World DevOps Scenario: Resolving an Out-of-Disk Space Outage

When automated cloud monitor alarms alert you that a production application server has hit **100% Disk Space Capacity**, execute this quick tactical isolation chain:

1. Map out the high-level capacity breakdown: `df -h`
2. Identify which specific mount point or storage drive partition is clogged at 100% capacity.
3. Drill down inside the bloated drive area to catch the problem folder: `du -sh /var/* | sort -h`
4. Isolate the runaway storage glutton (e.g., finding `/var/log/` is using 40GB).
5. Peer into the dynamic directory to catch the target file: `ls -lhS /var/log/` (Sorts items from biggest to smallest).
6. Investigate the live data write streams hitting that massive text block: `tail -f /var/log/nginx/access.log`
7. Safely clear the data or scale up your partition volume to restore production app system uptime!
