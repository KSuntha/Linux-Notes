# Linux Networking Commands: Troubleshooting & Data Transfer

In a DevOps ecosystem, applications rarely run in isolation. Microservices, databases, and continuous delivery systems must constantly talk to each other across secure networks. Mastering these fundamental terminal utilities allows you to debug connectivity drops, inspect active listening gates, and stream data across cloud horizons.

---

## 🔍 1. Interface Discovery & IP Configurations

Before sending data across a network, you must audit your own system's network adapters and addresses.

```bash
ip a                      # IP Address: Displays all active physical and virtual network interfaces along with their assigned IP addresses.
ip route show             # Route Mapping: Displays the system's active routing table, showing exactly where outbound packets are sent.
ifconfig                  # Legacy Display: An older network interface utility. (Deprecated on modern systems; always prefer using the modern `ip` tool).
```

---

## 🛠️ 2. Connectivity & Connection Auditing

Use these diagnostic tools to check if a remote server is reachable or to see what services are opening ports on your local host.

| Command Syntax | Core Diagnostic Action | DevOps Practical Context 💡 |
| :--- | :--- | :--- |
| `ping google.com` | Sends continuous ICMP Echo requests to verify remote server contact. | Use `ping -c 4 host` to stop automatically after 4 packets inside automation scripts. |
| `netstat -tulnp` | Lists all active internet socket connections and listening ports. | Essential for troubleshooting "Port already in use" errors when launching containers or web servers. |

```plaintext
💡 Quick Reference for Netstat Flags (-tulnp):
  -t : Show TCP ports      -u : Show UDP ports      -l : Show Listening sockets
  -n : Show numeric IPs    -p : Show PID/Program names handling the connection
```

---

## 🌍 3. Cloud-Native Web Transfers (`curl` vs `wget`)

DevOps engineers frequently interact with APIs, pull remote installation scripts, and download software artifacts straight from the command line using these two power tools:

### The API & Script Streamer (`curl`)
Designed to fetch or send data to web servers. By default, it prints the raw content output directly onto your terminal screen.
```bash
curl https://example.com              # Dumps the raw HTML/text data of a web page right to the terminal.
curl -I https://example.com           # Headers Only: Peeks at web server status codes (like 200 OK or 404 Not Found).
curl -sS https://docker.com | sh  # Automation Move: Streams an online installer script directly into a bash interpreter shell.
```

### The Background Downloader (`wget`)
Built strictly to pull down entire files or directories from the internet straight into your local directory.
```bash
wget https://example.com/file.zip     # Downloads the target payload file and displays a real-time progress bar.
wget -c https://example.com/file.zip  # Resume Support: Picks up a broken download right where it left off after a network failure.
```

---

## 🏃‍♂️ Real-World DevOps Example: Diagnosing a Silent Web App Failure

When a newly deployed application fails to answer external browser queries, apply this progressive network troubleshooting chain to isolate the bug:

1. Confirm your local adapter configurations are active and hold a valid IP: `ip a`
2. Test if your machine can reach the external network at all: `ping -c 4 google.com`
3. Check if your web server process is actually running and listening for incoming web traffic: `netstat -tulnp`
4. Use local loopback queries to test if the web platform responds locally: `curl -I http://127.0.0.1:8080`
