# 🐧 Linux IPTables Lab

In this lab, I learned how to **secure a Linux environment using IPTables**. The lab involved installing IPTables, managing firewall rules, and configuring both incoming and outgoing traffic for a two-tier application environment.

---

## 📋 Lab Overview

<img width="1235" height="953" alt="CALESTON TECHNOLOGY" src="https://github.com/user-attachments/assets/dd6f5111-35a9-4e32-821a-e71f478d78e9" />

**Goal:**

* Install and verify IPTables on multiple servers
* Configure firewall rules for **SSH, HTTP, HTTPS, and custom ports**
* Allow traffic from specific sources while blocking all other connections
* Apply rules to both **incoming and outgoing traffic**

**Learning Outcomes:**

* Use `iptables` to manage network security on Linux
* Add, modify, and delete firewall rules
* Understand default policies and rule ordering
* Permit or block specific traffic based on **IP addresses and ports**

---

## 🛠 Step-by-Step Journey

### Step 1: Initial Setup

1. SSH into each server as user `<username>`:

```bash
ssh <username>@<server_name>
```

2. Switch to root to avoid repeated password prompts:

```bash
sudo su
# or sudo -s
```

3. Update the package list:

```bash
sudo apt update
```

4. Install IPTables:

```bash
sudo apt install iptables
```

5. Verify installation (optional):

```bash
sudo iptables -L
```

> ✅ Shows the default chains: INPUT, OUTPUT, FORWARD

---

### Step 2: Check Existing Rules

* List existing rules on each server:

```bash
sudo iptables -L
```

* Determine if any rules are already configured.

---

### Step 3: Configure Incoming Rules (devapp01)

**Permit SSH (22) and HTTP (80) from a specific source**:

```bash
sudo iptables -A INPUT -p tcp -s <source_ip> --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp -s <source_ip> --dport 80 -j ACCEPT
```

**Block all other incoming traffic**:

```bash
sudo iptables -A INPUT -j DROP
```

> 💡 Always add **accept rules first** before adding drop rules to avoid losing connectivity.

---

### Step 4: Configure Outgoing Rules (devapp01)

**Permit outgoing access to specific destinations/ports**:

```bash
sudo iptables -A OUTPUT -p tcp -d <devdb01_ip> --dport 5432 -j ACCEPT
sudo iptables -A OUTPUT -p tcp -d <repo_ip> --dport 80 -j ACCEPT
sudo iptables -A OUTPUT -p tcp -d google.com --dport 443 -j ACCEPT
```

**Block all other outgoing traffic to HTTP/HTTPS**:

```bash
sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP
sudo iptables -A OUTPUT -p tcp --dport 443 -j DROP
```

> 💡 **Note:** Omitting `-p` applies the rule to **all protocols**.

---

### Step 5: Verify Rules

* Check all configured rules:

```bash
sudo iptables -L -v
```

* Ensure rules are applied in correct order and match desired policy.

---

## ✅ Key Commands Summary

| Task                       | Command / Notes                                                   |
| -------------------------- | ----------------------------------------------------------------- |
| SSH into server            | `ssh <user>@<server>`                                             |
| Switch to root             | `sudo su` or `sudo -s`                                            |
| Update package list        | `sudo apt update`                                                 |
| Install IPTables           | `sudo apt install iptables`                                       |
| List IPTables rules        | `sudo iptables -L -v`                                             |
| Allow incoming TCP traffic | `sudo iptables -A INPUT -p tcp -s <IP> --dport <PORT> -j ACCEPT`  |
| Drop all incoming traffic  | `sudo iptables -A INPUT -j DROP`                                  |
| Allow outgoing TCP traffic | `sudo iptables -A OUTPUT -p tcp -d <IP> --dport <PORT> -j ACCEPT` |
| Drop outgoing HTTP/HTTPS   | `sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP`               |

---

## 💡 Notes / Tips

* **Order matters:** Accept rules should be added **before** drop rules.
* **Default policies:** IPTables processes rules from top to bottom; unmatched traffic follows the default policy.
* **Source/Destination:** Use `-s <source>` and `-d <destination>` to control traffic.
* **Testing:** Always test SSH connectivity before applying drop rules to avoid lockouts.

---

## 📌 Lab Summary

| Step                            | Status | Key Takeaways                                           |
| ------------------------------- | ------ | ------------------------------------------------------- |
| Install IPTables                | ✅      | Installed and verified on multiple servers              |
| Incoming firewall rules         | ✅      | Allowed SSH & HTTP from specific source                 |
| Drop remaining incoming traffic | ✅      | Secured server from unwanted connections                |
| Outgoing firewall rules         | ✅      | Allowed traffic to DB, repo, and Google HTTPS           |
| Drop other outgoing HTTP/HTTPS  | ✅      | Restricted all other web traffic                        |
| Verify rules                    | ✅      | Confirmed rules applied correctly with `iptables -L -v` |

---

## ✅ References

* [IPTables Tutorial](https://linuxconfig.org/iptables-tutorial)
* [Ubuntu IPTables Manual](https://help.ubuntu.com/community/IptablesHowTo)
* [Network Security Basics](https://linuxize.com/post/how-to-use-iptables-to-set-up-firewall-rules-on-ubuntu/)
