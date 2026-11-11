# Network Scanning with Zenmap: Host Discovery on a Local Network

* **Author:** Delon Omusi Owiti
* **Program/Batch:** B084
* **Module:** W2-PM5 (Network Scanning with Zenmap)
* **Report Date:** 11 November 2026
* **Scan Date & Time:** 11 November 2026, 16:12 EAT
* **Target Subnet:** 192.168.88.0/24

---

## 1. Disclaimer & Authorization
This lab was performed exclusively on a home network using owned devices and family-owned devices with explicit consent. The tools and techniques are documented for educational and ethical cybersecurity practice only and must not be used against unauthorized systems.

---

## 2. Objective
The primary objective was to perform host discovery (a ping scan using Zenmap/Nmap) to identify live hosts on a local subnet without executing port, service, or software version scans. Host discovery forms part of the intelligence gathering and reconnaissance stage of a penetration test, aligning with the Penetration Testing Execution Standard (PTES).

---

## 3. Scope & Environment

| Item | Detail |
| :--- | :--- |
| **Network (Subnet)** | `192.168.88.0/24` |
| **Subnet Mask** | `255.255.255.0` |
| **Device IP** | `192.168.88.231` (DHCP assigned) |
| **Device MAC** | `02-11-22-33-44-55` |
| **Router / Gateway** | `192.168.88.1` (`router.lan`) |
| **Network Adapter** | Intel(R) Dual Band Wireless-AC 8265 |
| **Operating System** | Windows 11 Pro |
| **Tool & Version** | Zenmap / Nmap 7.80 |
| **Scan Command** | `nmap -sn 192.168.88.1/24` (Ping Scan profile) |

---

## 4. Procedure & Results

### Step 1: Installation
Zenmap 7.80 was installed using default components from the official Nmap distribution.

### Step 2: Local Interface Enumeration
Running `ipconfig /all` via Windows Command Prompt identified the active wireless adapter configuration, confirming the subnet mask (`255.255.255.0`) and local IPv4 address (`192.168.88.231`).

### Step 3: Execution
The target range `192.168.88.1/24` was scanned using the Ping scan profile (`nmap -sn`), checking 256 addresses in 17.78 seconds.

### Step 4: Discovered Hosts
Nmap identified 5 live hosts on the subnet:

| IP Address | MAC Address | Notes |
| :--- | :--- | :--- |
| `192.168.88.1` (`router.lan`) | `18:FD:74:8A:B3:CF` | Gateway / Router |
| `192.168.88.227` | `92:DA:AD:FB:0D:54` | Active client device |
| `192.168.88.229` | `D6:9C:B3:13:D5:6B` | Active client device |
| `192.168.88.237` | `8E:63:CD:B7:BB:80` | Active client device |
| `192.168.88.231` | `02-11-22-33-44-55` | Scanning host (local machine) |

---

## 5. Analysis & Key Findings
* **Locally Administered Addresses:** Four of the five active hosts exhibited locally administered MAC addresses (randomized/private Wi-Fi MAC addresses), making vendor identification via OUI lookup inconclusive.
* **Dynamic IP Allocation:** Comparative scans across multiple days demonstrated that DHCP lease assignments shift host IP addresses dynamically, proving that IP addresses alone are unreliable for long-term device tracking.
* **Snapshot Limitations:** A single host discovery scan only captures network state at a fixed moment; devices offline during the scan window are omitted.

---

## 6. Recommendations
1. Maintain an updated device inventory mapped primarily by persistent attributes like MAC addresses or device names rather than dynamic IPs.
2. Investigate intermittent or unrecognized devices discovered during baseline sweeps.
3. Utilize static DHCP reservations on the router to streamline device identification on trusted local networks.

---

## 7. References
* [Penetration Testing Execution Standard (PTES) - Intelligence Gathering](https://www.geeksforgeeks.org/software-engineering/penetration-testing-execution-standard-ptes/)
* [Nmap Dev Seclists Discussion on Host Discovery](https://seclists.org/nmap-dev/2015/q4/179)
