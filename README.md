# AI-Enhanced SSH Honeypot with Cowrie, Ollama, and UFW

This project builds a small lab with **three VMs**:

- **Honeypot VM (Ubuntu)** – runs [Cowrie](https://github.com/cowrie/cowrie) on port `2222` and a real SSH server on port `22`.
- **Attacker VM (Kali)** – generates brute-force style SSH traffic and “malicious” commands.
- **Normal User VM (Ubuntu)** – generates benign SSH activity.
- **AI Script (Bash + Ollama)** – reads Cowrie logs, summarizes each IP’s behavior, and helps decide firewall rules.
- **Firewall (ufw)** – uses AI decisions to “carve the path”:
  - attackers stay in the **honeypot** or are blocked
  - trusted IPs get access to the **real SSH server** on port `22`

> ⚠️ **Legal / ethical note:**  
> Only run this lab on your own machines and private virtual networks.  
> Do **not** attack systems you don’t own or don’t have written permission to test.

---

## 1. Lab Topology

Example IPs (you can change these, but keep them consistent):

| VM               | Role          | Example IP   |
|------------------|--------------|--------------|
| Ubuntu Honeypot  | Cowrie + SSH | `10.0.2.15`  |
| Kali             | Attacker     | `10.0.2.14`  |
| Ubuntu User      | Normal User  | `10.0.2.10`  |

All three VMs are on the **same internal/NAT network** in VirtualBox/VMware and can ping each other.

High-level flow:

```text
Attacker / User → SSH → Honeypot VM (firewall)

Firewall (ufw):
  - Everyone: allowed to Cowrie on port 2222
  - Trusted IPs (after AI): allowed to real SSH on port 22
  - Attacker IPs (after AI): can be blocked entirely
