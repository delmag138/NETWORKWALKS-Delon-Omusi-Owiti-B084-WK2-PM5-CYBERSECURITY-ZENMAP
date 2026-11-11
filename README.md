
# Network Scanning with Zenmap: Host Discovery on a Local Network

* **Author:** Delon Omusi Owiti[cite: 1]
* **Program/Batch:** B084[cite: 1]
* **Module:** W2-PM5 (Network Scanning with Zenmap)[cite: 1]
* **Report Date:** 11 November 2026[cite: 1]
* **Scan Date & Time:** 11 November 2026, 16:12 EAT[cite: 1]
* **Target Subnet:** 192.168.88.0/24[cite: 1]

---

## 1. Disclaimer & Authorization
This lab was performed exclusively on a home network using owned devices and family-owned devices with explicit consent. The tools and techniques are documented for educational and ethical cybersecurity practice only and must not be used against unauthorized systems[cite: 1].

---

## 2. Objective
The primary objective was to perform host discovery (a ping scan using Zenmap/Nmap) to identify live hosts on a local subnet without executing port, service, or software version scans[cite: 1]. Host discovery forms part of the intelligence gathering and reconnaissance stage of a penetration test, aligning with the Penetration Testing Execution Standard (PTES)[cite: 1].

---

## 3. Scope & Environment

| Item | Detail |
| :--- | :--- |
| **Network (Subnet)** | `192.168.88.0/24`[cite: 1] |
| **Subnet Mask** | `255.255.255.0`[cite: 1] |
| **Device IP** | `192.168.88.231` (DHCP assigned)[cite: 1] |
| **Device MAC** | `02-11-22-33-44-55`[cite: 1] |
| **Router / Gateway** | `192.168.88.1` (`router.lan`)[cite: 1] |
| **Network Adapter** | Intel(R) Dual Band Wireless-AC 8265[cite: 1] |
| **Operating System** | Windows 11 Pro[cite: 1] |
| **Tool & Version** | Zenmap / Nmap 7.80[cite: 1] |
| **Scan Command** | `nmap -sn 192.168.88.1/24` (Ping Scan profile)[cite: 1] |

---

## 4. Procedure & Results

### Step 1: Installation
Zenmap 7.80 was installed using default components from the official Nmap distribution[cite: 1].

### Step 2: Local Interface Enumeration
Running `ipconfig /all` via Windows Command Prompt identified the active wireless adapter configuration, confirming the subnet mask (`255.255.255.0`) and local IPv4 address (`192.168.88.231`)[cite: 1].

### Step 3: Execution
The target range `192.168.88.1/24` was scanned using the Ping scan profile (`nmap -sn`), checking 256 addresses in 17.78 seconds[cite: 1].

### Step 4: Discovered Hosts
Nmap identified 5 live hosts on the subnet[cite: 1]:

| IP Address | MAC Address | Notes |
| :--- | :--- | :--- |
| `192.168.88.1` (`router.lan`)[cite: 1] | `18:FD:74:8A:B3:CF`[cite: 1] | Gateway / Router[cite: 1] |
| `192.168.88.227`[cite: 1] | `92:DA:AD:FB:0D:54`[cite: 1] | Active client device[cite: 1] |
| `192.168.88.229`[cite: 1] | `D6:9C:B3:13:D5:6B`[cite: 1] | Active client device[cite: 1] |
| `192.168.88.237`[cite: 1] | `8E:63:CD:B7:BB:80`[cite: 1] | Active client device[cite: 1] |
| `192.168.88.231`[cite: 1] | `02-11-22-33-44-55`[cite: 1] | Scanning host (local machine)[cite: 1] |

---

## 5. Analysis & Key Findings
* **Locally Administered Addresses:** Four of the five active hosts exhibited locally administered MAC addresses (randomized/private Wi-Fi MAC addresses), making vendor identification via OUI lookup inconclusive[cite: 1].
* **Dynamic IP Allocation:** Comparative scans across multiple days demonstrated that DHCP lease assignments shift host IP addresses dynamically, proving that IP addresses alone are unreliable for long-term device tracking[cite: 1].
* **Snapshot Limitations:** A single host discovery scan only captures network state at a fixed moment; devices offline during the scan window are omitted[cite: 1].

---

## 6. Recommendations
1. Maintain an updated device inventory mapped primarily by persistent attributes like MAC addresses or device names rather than dynamic IPs[cite: 1].
2. Investigate intermittent or unrecognized devices discovered during baseline sweeps[cite: 1].
3. Utilize static DHCP reservations on the router to streamline device identification on trusted local networks[cite: 1].

---

## 7. References
* [Penetration Testing Execution Standard (PTES) - Intelligence Gathering](https://www.geeksforgeeks.org/software-engineering/penetration-testing-execution-standard-ptes/)[cite: 1]
* [Nmap Dev Seclists Discussion on Host Discovery](https://seclists.org/nmap-dev/2015/q4/179)[cite: 1]
