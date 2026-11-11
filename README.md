# Zenmap Lab Report: Host Discovery on a Local Network

**W2-PM5: Network Scanning with Zenmap** | Cybersecurity & Ethical Hacking Internship, Networkwalks

| | |
|---|---|
| **Name** | Delon Omusi Owiti |
| **Program/Batch** | B084 |
| **Report date** | 11 November 2026 |
| **Module** | W2-PM5 (Network Scanning with Zenmap) |
| **Scan date and time** | 11 November 2026, 16:12 EAT |
| **Target** | Local subnet 192.168.88.0/24 |
| **Authorization** | Own home network; own devices and family-owned devices |

## Contents

1. [Disclaimer & Authorization](#1-disclaimer--authorization)
2. [Objective](#2-objective)
3. [Scope & Environment](#3-scope--environment)
4. [Tools & Method](#4-tools--method)
5. [Procedure & Results](#5-procedure--results)
6. [Analysis](#6-analysis)
7. [Limitations](#7-limitations)
8. [Recommendations](#8-recommendations)
9. [Conclusion](#9-conclusion)
10. [References](#references)
11. [Appendix: Evidence](#appendix-evidence)

---

## 1. Disclaimer & Authorization

This lab was performed only on my own home network, using devices that I own and devices owned by members of my family with their consent. The tools and techniques were used for educational purposes only. They must not be used against any system or network without the owner's permission.

## 2. Objective

The objective was to identify the live hosts on my local subnet using host discovery (a ping scan with Zenmap/Nmap). The scan did not check ports, services or software versions. Host discovery is part of the scanning and network discovery stage of a penetration test; PTES (the Penetration Testing Execution Standard) covers this under Intelligence Gathering [[1]](#references).

## 3. Scope & Environment

| Item | Detail |
|---|---|
| **Network (subnet)** | 192.168.88.0/24 |
| **Subnet mask** | 255.255.255.0 |
| **My device IP** | 192.168.88.231 (assigned by DHCP) |
| **My device MAC** | 02-11-22-33-44-55 (from `ipconfig /all`) |
| **Router** | 192.168.88.1 (reported by Nmap as `router.lan`) |
| **Adapter used** | Intel(R) Dual Band Wireless-AC 8265 |
| **Operating system** | Windows 11 Pro |
| **Tool and version** | Zenmap / Nmap 7.80 |
| **Network type** | Wi-Fi |
| **Target typed in Zenmap** | `192.168.88.1/24` (covers the same 256 addresses as 192.168.88.0/24) |
| **Scan used in this report** | 11 November 2026, 16:12 EAT (duration 17.78 seconds) |

My computer also has a second network adapter on 192.168.223.x. It was not part of this scan.

## 4. Tools & Method

| Tool | Purpose |
|---|---|
| Zenmap (Nmap GUI) | Scan the local subnet to find live hosts |
| Windows CMD | Identify my local IP address, subnet mask and MAC address |

| Command | Purpose |
|---|---|
| `ipconfig /all` | Show the IP address, subnet mask and MAC address of my adapter |
| `nmap -sn 192.168.88.1/24` | Ping scan (host discovery only; no port scan) of the local subnet, run through the Zenmap "Ping scan" profile |

## 5. Procedure & Results

### Step 1: Install Zenmap

I downloaded the installer from the official Nmap website ([nmap.org](https://nmap.org)) and installed Zenmap with the default components. The installed version is Zenmap 7.80.

<p align="center">
  <img src="images/01-zenmap-version.png" alt="Zenmap Help > About window showing version 7.80" width="300">
  <br><em>Figure 1: Help > About in Zenmap, showing version 7.80</em>
</p>

### Step 2: Identify my IP address and subnet

I ran `ipconfig /all` in Command Prompt. On the Wi-Fi adapter my IPv4 address is 192.168.88.231 with subnet mask 255.255.255.0, which is /24. This gives the target subnet 192.168.88.0/24.

<p align="center">
  <img src="images/02-ipconfig-wifi-adapter.png" alt="ipconfig /all output for the Wi-Fi adapter" width="600">
  <br><em>Figure 2: ipconfig /all output for the Wi-Fi adapter</em>
</p>

### Step 3: Run the ping scan

In Zenmap I entered `192.168.88.1/24` in the Target box and selected the Ping scan profile, which runs `nmap -sn 192.168.88.1/24`. Because of the /24 mask, Nmap scanned the whole 192.168.88.0/24 network (256 addresses). The scan started at 16:12 EAT on 11 November 2026 and completed in 17.78 seconds.

<p align="center">
  <img src="images/03-zenmap-ping-scan-output.png" alt="Zenmap Nmap Output tab showing the ping scan results" width="500">
  <br><em>Figure 3: Zenmap ping scan output</em>
</p>

### Step 4: Results

Nmap scanned 256 addresses and found **5 hosts up**.

| IP address | MAC address |
|---|---|
| 192.168.88.1 (`router.lan`) | 18:FD:74:8A:B3:CF |
| 192.168.88.227 | 92:DA:AD:FB:0D:54 |
| 192.168.88.229 | D6:9C:B3:13:D5:6B |
| 192.168.88.237 | 8E:63:CD:B7:BB:80 |
| 192.168.88.231 | 02-11-22-33-44-55 (my own device; taken from `ipconfig /all`) |

Nmap did not report a MAC address for 192.168.88.231 because that is the machine running the scan.

### Step 5: Save the topology

I opened the Topology tab, enabled the Legend and used Save Graphic to save the topology as a PDF. The topology is shown in [Appendix C](#c-network-topology).

## 6. Analysis

Nmap listed the vendor of every MAC address as "Unknown", so I used an OUI lookup for the router and checked the address type of each MAC. The second hexadecimal digit of the first byte shows whether an address is manufacturer-assigned or locally administered (digits 2, 6, A and E mean locally administered).

| IP address | Vendor (OUI lookup) | Address type | Observation |
|---|---|---|---|
| 192.168.88.1 | Routerboard.com | Manufacturer-assigned | Reported by Nmap as `router.lan`; most likely the router/gateway. |
| 192.168.88.227 | Not applicable | Locally administered | Consistent with a device using a randomized (private) Wi-Fi MAC address, so the vendor cannot be identified. |
| 192.168.88.229 | Not applicable | Locally administered | Same as above. |
| 192.168.88.237 | Not applicable | Locally administered | Same as above. |
| 192.168.88.231 | Not applicable | Locally administered | My own device. The address 02-11-22-33-44-55 follows a simple sequential pattern, so it does not look manufacturer-assigned. |

Four of the five hosts use locally administered MAC addresses. This means that MAC address and vendor alone are not enough to identify most devices on this network. Hostname, owner confirmation or the router's device list would be needed.

## 7. Limitations

- **Single point in time.** I ran four scans over two days and the results differed (table below). This report uses the last scan.

  | Scan (EAT) | Hosts up | Duration | Note |
  |---|---|---|---|
  | 10 Nov, 11:50 | 5 | Not recorded | Hosts: .1, .230, .231, .232, .237 |
  | 11 Nov, 00:02 | 5 | 118.48 s | Only scan that found 192.168.88.245 (MAC 44:37:0B:E4:4F:CB) |
  | 11 Nov, 09:03 | 4 | 25.50 s | 92:DA:AD:FB:0D:54 not seen |
  | 11 Nov, 16:12 | 5 | 17.78 s | Scan used in this report |

- **IP addresses change.** The MAC 92:DA:AD:FB:0D:54 appeared as 192.168.88.230 on 10 November and as 192.168.88.227 on 11 November. D6:9C:B3:13:D5:6B appeared as 192.168.88.232 and then 192.168.88.229. This suggests the router reassigns addresses through DHCP, so an IP address alone is not a reliable way to identify a device.
- **Devices that do not respond.** Devices that are asleep, out of range or using Wi-Fi power saving may not appear. With randomized MAC addresses, one device may also appear under different addresses at different times.
- **Discovery method.** On a local subnet Nmap normally uses ARP requests for host discovery, so blocked ICMP is less of a concern here [[2]](#references). Router features such as client isolation could still hide devices.
- **Scope of the scan.** A ping scan only shows which hosts are up. It does not test ports, services, operating systems or vulnerabilities, so no risk rating is given in this report.

## 8. Recommendations

1. **Keep a device inventory.** Record the MAC address, name and owner of each device, and use the MAC rather than the IP to track it, because IP addresses change.
2. **Investigate unrecognized devices.** 192.168.88.245 (MAC 44:37:0B:E4:4F:CB) appeared in one scan and not in the others. Its owner should be identified.
3. **Make devices easier to identify.** Where the router allows it, assign device names or fixed DHCP addresses to known devices.
4. **Repeat discovery at different times and compare the results.** A single scan misses devices that are offline at that moment.
5. **Scan only with authorization.** Only scan networks and devices that I own or have permission to test.

## 9. Conclusion

In this lab I used Zenmap to find the live hosts on my local subnet. The final ping scan found five hosts on 192.168.88.0/24: the router, my own device and three other devices. Four of the five use locally administered MAC addresses, so they cannot be identified by vendor. Comparing four scans showed that a single host discovery scan is only a snapshot, and that device IP addresses change over time. I learned that host discovery is the first step of mapping a network, and that its results must be checked and documented carefully before any further testing.

## References

1. [Penetration Testing Execution Standard (PTES), GeeksforGeeks](https://www.geeksforgeeks.org/software-engineering/penetration-testing-execution-standard-ptes/)
2. [nmap-dev mailing list, 2015 Q4](https://seclists.org/nmap-dev/2015/q4/179)

---

## Appendix: Evidence

### A. Raw Nmap output (11 November 2026, 16:12 EAT)

```text
Starting Nmap 7.80 ( https://nmap.org ) at 2026-11-11 16:12 E. Africa Standard Time
Nmap scan report for router.lan (192.168.88.1)
Host is up (0.0049s latency).
MAC Address: 18:FD:74:8A:B3:CF (Unknown)
Nmap scan report for 192.168.88.227
Host is up (0.054s latency).
MAC Address: 92:DA:AD:FB:0D:54 (Unknown)
Nmap scan report for 192.168.88.229
Host is up (0.026s latency).
MAC Address: D6:9C:B3:13:D5:6B (Unknown)
Nmap scan report for 192.168.88.237
Host is up (0.024s latency).
MAC Address: 8E:63:CD:B7:BB:80 (Unknown)
Nmap scan report for 192.168.88.231
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 17.78 seconds
```

### B. ipconfig /all output

<p align="center">
  <img src="images/04-ipconfig-all-full.png" alt="Full ipconfig /all output showing the Wi-Fi adapter details" width="600">
  <br><em>Figure 4: ipconfig /all output showing the Wi-Fi adapter details</em>
</p>

### C. Network topology

<p align="center">
  <img src="images/05-zenmap-topology.png" alt="Zenmap topology view showing the five live hosts connected to localhost" width="600">
  <br><em>Figure 5: Zenmap topology of the five live hosts</em>
</p>
