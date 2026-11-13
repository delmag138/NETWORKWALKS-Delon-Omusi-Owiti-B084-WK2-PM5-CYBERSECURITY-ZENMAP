# W2-PM-FINAL: Footprinting & Network Scanning — Final Report

**Cybersecurity & Ethical Hacking Internship — Networkwalks**

| | |
|---|---|
| **Pentester name** | Delon Omusi Owiti |
| **Program/Batch** | B084 — Networkwalks |
| **Report date** | 13 November 2026 |
| **Modules completed** | W2-PM1 (Footprinting with Multiple Kali Tools)<br>W2-PM5 (Zenmap Network Scanning) |
| **Client/Target** | 1. networkwalks.com (training target named by the instructor's guide)<br>2. My own home local LAN network |
| **Permission secured from client?** | Yes — networkwalks.com is the instructor-assigned training domain for this module; the LAN is my own network |
| **Phases covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery<br>Phase 3–5: Not yet covered |

## Contents

1. [Liability Disclaimer](#1-liability-disclaimer)
2. [Introduction](#2-introduction)
3. [Tools Used](#3-tools-used)
4. [Activities Performed](#4-activities-performed)
   - [4.1 Footprinting & Reconnaissance (W2-PM1)](#41-footprinting--reconnaissance-w2-pm1--target-networkwalkscom)
   - [4.2 Network Scanning with Zenmap (W2-PM5)](#42-network-scanning-with-zenmap-w2-pm5--target-my-own-home-lan)
5. [Risk Analysis / Impact](#5-risk-analysis--impact)
6. [Recommendations](#6-recommendations)
7. [Conclusion](#7-conclusion)
8. [Issues Faced & Troubleshooting](#8-issues-faced--troubleshooting)
9. [Appendix: Evidence Collected](#appendix-evidence-collected)

---

## 1. Liability Disclaimer

These materials are for education and research purposes only. I have performed these activities only on the training domain assigned by the instructor (networkwalks.com) and on my own home network and devices. All tools used only read information that was already public or performed passive host discovery; no exploitation, login attempt or unauthorized access was carried out. Do not use anything from this report to break the law. The instructor, the authors and Networkwalks are not responsible for what anyone does with this knowledge. Every action taken is my own responsibility. Misuse of these techniques can lead to criminal charges, heavy fines, loss of employment and a permanent record. In most countries, unauthorised access is a crime even when nothing is damaged.

## 2. Introduction

This report covers two modules completed in Week 2 of my Cybersecurity & Ethical Hacking internship at Networkwalks: footprinting the networkwalks.com domain using six Kali Linux tools (W2-PM1), and scanning my own local network with Zenmap (W2-PM5). The first module covers the reconnaissance and footprinting phase, and the second covers the scanning and network discovery phase, so together they show how a tester moves from gathering public information about a target to mapping the live hosts on a network.

All W2-PM1 commands were run in Kali Linux (running inside VirtualBox) against the live website networkwalks.com. All W2-PM5 steps were run on a Windows PC with Zenmap installed, against my own home Wi-Fi network. Every step below includes the exact command used, a screenshot as evidence, the result observed, and a short note on why the finding matters.

## 3. Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux (VirtualBox) & Windows | Operating systems used for the reconnaissance and scanning activities |
| whois | Find domain registration details (registrar, dates, name servers) |
| whatweb | Fingerprint web technologies (server, CMS, plugins, IP) |
| nslookup | Resolve the domain name to its IP address using DNS |
| curl -I | Read the HTTP response headers of the website |
| wafw00f | Detect whether a Web Application Firewall protects the site |
| dnsrecon | Enumerate DNS records (NS, MX, SPF, TXT, SRV) |
| Zenmap (Nmap GUI) | Scan the local subnet to find live hosts, IPs and MAC addresses |
| Windows CMD (ipconfig) | Identify my local IP address, subnet mask and MAC address |

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance (W2-PM1) — Target: networkwalks.com

I performed reconnaissance against the networkwalks.com domain, the instructor-assigned training target, using six built-in Kali Linux tools. The lab was carried out starting at 14:01 EAT on 12 November 2026, running through to approximately 20:38 EAT as later tools were completed. Each tool was used to collect a different type of public information about the target, and the results are recorded exactly as observed, not copied from the lab guide's example output.

#### Task 1 — whois: domain registration details

`whois networkwalks.com` was run to find who owns the domain, its registrar, and its name servers. The query returned results from two different WHOIS servers: the registry server (Verisign) gave a short record with only the two Hostgator name servers, while the registrar's own server (whois.godaddy.com) gave a fuller record showing the domain is privacy-protected through "Domains By Proxy, LLC" and listing four name servers, two at Hostgator and two at DomainControl. Both records agree on the key facts:

- **Registrar:** GoDaddy.com, LLC
- **Creation date:** 6 November 2019
- **Expiry date:** 6 November 2027
- **Name servers:** NS6135.HOSTGATOR.COM, NS6136.HOSTGATOR.COM (plus NS29/NS30.DOMAINCONTROL.COM on the registrar record)

The registrar's WHOIS server also returned a notice that it is being retired in favour of RDAP, which is noted as a limitation below.

<p align="center">
  <img width="624" height="615" alt="image" src="https://github.com/user-attachments/assets/7a38dbfc-e238-4c26-9315-e49adc7be40c" />

</p>

**How this matters:** whois reveals the registrar, registration/expiry dates, and name servers. The name servers point to Hostgator, so this instantly reveals the hosting provider. The registrant's own contact details are hidden behind a privacy proxy, so no personal information was exposed.

#### Task 2 — whatweb: fingerprint web technologies

`whatweb networkwalks.com` was run to identify the technologies running on the site.

<p align="center">
  <img width="624" height="439" alt="image" src="https://github.com/user-attachments/assets/8fed29db-12f1-4791-b8a6-785c3a7db7e6" />
  <br><em>Figure 2: whatweb output for networkwalks.com</em>
</p>

The result identified **WordPress 7.1.4** and **WordPress Download Manager 3.3.76**, the server software (Apache), jQuery 3.7.1, the public contact email info@networkwalks.com, and the server IP 192.232.216.135.

**How this matters:** whatweb exposes the exact CMS and plugin versions in use. An attacker would check these specific version numbers against public vulnerability databases to look for known exploits. It also leaks a contact email and the real server IP.

#### Task 3 — nslookup: resolve the domain to an IP address

`nslookup networkwalks.com` was run to resolve the domain name using DNS.

<p align="center">
  <img width="624" height="307" alt="image" src="https://github.com/user-attachments/assets/212005d0-aa27-49f9-bff8-d652980cce1d" />
  <br><em>Figure 3: nslookup output for networkwalks.com</em>
</p>

The result returned IP address **192.232.216.135**, which matches the IP that whatweb reported independently in Task 2, confirming the finding.

**How this matters:** nslookup turns a domain name into its real IP address. Knowing the IP lets an attacker scan the server directly or look up other sites hosted on the same address.

#### Task 4 — curl -I: read HTTP response headers

`curl -I https://networkwalks.com` was run to inspect the HTTP response headers without downloading the page body.

<p align="center">
   <img width="624" height="447" alt="image" src="https://github.com/user-attachments/assets/a22c0f4b-8b3b-4d54-a93a-b51aa08a0b20" />
  <br><em>Figure 4: curl -I output for networkwalks.com (run inside the VirtualBox Kali VM)</em>
</p>

The response confirmed an **Apache** server, and exposed the WordPress REST API endpoint `/wp-json/` and a session cookie (`__wpdm_client`) tied to the Download Manager plugin found in Task 2.

**How this matters:** HTTP headers leak the web server software and hidden endpoints such as the REST API. An attacker can fingerprint the stack and find entry points without ever loading the full page.

#### Task 5 — wafw00f: detect a Web Application Firewall

`wafw00f networkwalks.com` was run to check whether a firewall is protecting the site.

<p align="center">
  <img width="624" height="415" alt="image" src="https://github.com/user-attachments/assets/c717b284-7c0e-4122-8378-4be885cb2c2f" />
  <br><em>Figure 5: wafw00f output for networkwalks.com</em>
</p>

The result identified **ModSecurity (SpiderLabs)** as the active WAF, using 2 requests to confirm it.

**How this matters:** knowing a WAF is present shapes the whole approach of an attacker: naive attempts will be blocked or logged, so any further testing would need to be adapted. This is also valuable for a defender to confirm the WAF is actually active.

#### Task 6 — dnsrecon: enumerate DNS records

`dnsrecon -d networkwalks.com` was run to enumerate the domain's DNS records.

<p align="center">
  <img width="624" height="606" alt="image" src="https://github.com/user-attachments/assets/335b428d-e20d-4299-b181-8695e767d837" />
  <br><em>Figure 6: dnsrecon output for networkwalks.com</em>
</p>

The scan found **8 records**, including the mail server (mail.networkwalks.com, same IP as the main site), an SPF record authorizing the mail provider, a Google site-verification TXT record, the BIND DNS software version (9.16.23-RH) on both name servers, and 8 SRV records pointing autodiscover traffic to cPanel's email discovery service across several IPs. One of the two name servers resolved to a different IP (192.232.216.131) than the main site and mail server (192.232.216.135).

**How this matters:** dnsrecon maps the target's full DNS footprint, including the DNS software version, which could itself be checked against known vulnerabilities, plus mail and hosting infrastructure. Each record is a potential piece of the bigger infrastructure picture.

### 4.2 Network Scanning with Zenmap (W2-PM5) — Target: my own home LAN

For the second activity, I used Zenmap to perform host discovery on my own home network (192.168.88.0/24). This required identifying my local IP address and subnet, discovering live hosts, identifying their IP and MAC addresses, and generating a network topology.

I first used `ipconfig /all` to identify my local IP address and LAN subnet, then entered the subnet into Zenmap and ran the Ping scan profile (`nmap -sn`) to identify active hosts. I ran this scan four times over two days while confirming my results; the scan below, at 16:12 EAT on 11 November 2026, is the one used for this report.

<p align="center">
  <img width="500" height="229" alt="image" src="https://github.com/user-attachments/assets/703d6231-c123-4df2-b80d-b1061d88ae92" />
  <br><em>Figure 7: ipconfig /all output showing my IPv4 address and subnet mask</em>
</p>

<p align="center">
  <img width="400" height="396" alt="image" src="https://github.com/user-attachments/assets/3023faee-c607-48df-bc1d-6695926d056d" />
  <br><em>Figure 8: Zenmap ping scan output (nmap -sn 192.168.88.1/24)</em>
</p>

The scan found **5 live hosts** on the subnet:

| IP address | MAC address |
|---|---|
| 192.168.88.1 (router.lan) | 18:FD:74:8A:B3:CF |
| 192.168.88.227 | 92:DA:AD:FB:0D:54 |
| 192.168.88.229 | D6:9C:B3:13:D5:6B |
| 192.168.88.237 | 8E:63:CD:B7:BB:80 |
| 192.168.88.231 | 02-11-22-33-44-55 (my own device; taken from `ipconfig /all`, since Nmap does not report the MAC of the machine running the scan) |

Checking each MAC address, only the router's (18:FD:74:8A:B3:CF) is a standard manufacturer-assigned address, identified as Routerboard.com. The other four, including my own, are **locally administered** (randomized) addresses, which is why Nmap could not identify any vendor for them. Comparing this against the three earlier scans I ran, I found that the same physical devices appeared under different IP addresses from one day to the next (for example, the MAC 92:DA:AD:FB:0D:54 appeared as .230 and later as .227), confirming the router was reassigning addresses by DHCP, and that a single scan is only a snapshot in time.

Finally, I opened the Topology tab, enabled the legend, and saved the network topology in PDF format as required by the practical task.

<p align="center">
  <img width="480" height="325" alt="image" src="https://github.com/user-attachments/assets/5a05a59f-db33-446a-b2a8-a063742f6da2" />
  <br><em>Figure 9: Zenmap topology of the five live hosts</em>
</p>

**How this matters:** a ping scan does not exploit anything; it only shows which hosts are up. But host discovery is the essential first step before any further scanning or mapping of a network, and it already revealed that most devices on this home network use randomized MAC addresses, meaning IP and MAC alone are not reliable ways to identify a device over time.

## 5. Risk Analysis / Impact

Based on the information collected during the footprinting and network scanning activities, I identified the following observations.

| # | Risk / Finding | Evidence / Observation | Potential Impact | Level |
|---|---|---|---|---|
| 1 | Hosting provider and name servers exposed | whois identified Hostgator name servers and GoDaddy as registrar | Reveals hosting infrastructure, useful for an attacker's planning | 🟢 Low |
| 2 | Exact CMS and plugin versions exposed | whatweb identified WordPress 7.1.4 and WP Download Manager 3.3.76 | Specific versions can be checked against public vulnerability databases for known exploits | 🟡 Medium |
| 3 | Server IP address identifiable | nslookup and whatweb both resolved 192.232.216.135 | Allows direct scanning of the server and correlation with other sites on the same IP | 🟢 Low |
| 4 | REST API endpoint and plugin cookie exposed | curl -I revealed /wp-json/ and the __wpdm_client cookie | May assist further technology fingerprinting and enumeration | 🟢 Low |
| 5 | DNS infrastructure and software version exposed | dnsrecon found 8 records including BIND 9.16.23-RH, mail server, SPF and cPanel SRV records | DNS software version and mail/hosting details can help build a broader attack picture | 🟡 Medium |
| 6 | Multiple live hosts visible on home network | Zenmap identified 5 live hosts on my own 192.168.88.0/24 network | Unknown or unauthorized devices on a network could go unnoticed without regular discovery | 🟡 Medium |
| 7 | Devices identifiable only by randomized MAC addresses | 4 of 5 hosts on the LAN used locally administered MACs; vendor lookup failed for all of them | Harder for the device owner to inventory or trust their own device list; also harder for an attacker to fingerprint specific devices | 🟢 Low |

The risks above are observations from the footprinting and scanning exercises, not confirmed vulnerabilities. The practical exercises primarily involved information gathering and host discovery. No exploitation or vulnerability validation was performed as part of these two modules. Therefore, the presence of information such as a software version, IP address or DNS record does not by itself mean that the system is vulnerable. Further authorized security testing would be required to confirm any actual vulnerability.

## 6. Recommendations

1. **Review publicly exposed technology information.** Organizations should regularly review what their WHOIS, HTTP headers and web technologies reveal about their stack.
2. **Keep software updated.** CMS platforms and plugins (WordPress, WP Download Manager) should be kept current and reviewed against security advisories; this also applies to DNS server software versions revealed by tools like dnsrecon.
3. **Review HTTP headers and exposed endpoints.** Headers and endpoints such as /wp-json/ should be reviewed to confirm no unnecessary technical detail is exposed.
4. **Review DNS records regularly.** DNS, mail and service records should be checked periodically to ensure only required information is publicly exposed.
5. **Keep the WAF properly configured and monitored.** The ModSecurity WAF already found should be kept enabled and tuned.
6. **Perform regular internal network discovery and keep a device inventory.** Because MAC addresses on this network are mostly randomized, devices should be tracked by name or a fixed DHCP reservation rather than IP or MAC alone.
7. **Scan and footprint only with authorization.** All reconnaissance and scanning should only be performed against systems and networks where appropriate authorization has been given, as was done here (the instructor-assigned training domain and my own home network).

## 7. Conclusion

During Week 2 of my Cybersecurity & Ethical Hacking internship, I completed two practical activities covering footprinting, reconnaissance and network scanning.

In the footprinting activity (W2-PM1), I used six Kali Linux tools against the training domain networkwalks.com. I learned how whois reveals registration and hosting details, whatweb identifies the exact CMS and plugin versions in use, nslookup and dnsrecon map a domain's IP and DNS infrastructure, curl exposes HTTP-level details and hidden endpoints, and wafw00f reveals whether a Web Application Firewall is present.

In the network scanning activity (W2-PM5), I used Zenmap to identify my local network configuration and discover the active hosts on my own home network, collecting IP and MAC address information and generating a network topology. Running the scan multiple times also showed me that a single scan is only a snapshot, since the same devices appeared under different IP addresses over time.

Both exercises showed me that information gathering, whether from public domain records or from a simple ping scan, can reveal a significant amount about an environment before any exploitation is attempted. I also learned that a good cybersecurity report should explain what was performed, what was discovered, what the observation means, what risk it may create, and what can be done to reduce that risk. Finally, I learned that reconnaissance and scanning must always be performed within an authorized scope; every activity in this report was carried out either against the instructor-assigned training domain or against my own home network.

## 8. Issues Faced & Troubleshooting

- The registrar's WHOIS server (whois.godaddy.com) returned a notice that it is being retired in favour of RDAP. I noted this rather than treating it as an error, since the query still returned a full, valid record.
- A single nmap ping scan is only a snapshot. Running it four times over two days produced different host counts (4 or 5) and showed some devices reassigned to different IP addresses, so I compared all four runs before settling on one as the official scan for this report.
- Four of the five MAC addresses on my LAN are locally administered (randomized), so no vendor could be identified for them through a normal OUI lookup. I treated this as a genuine finding rather than a tool failure, and explained it in the Analysis.

---

## Appendix: Evidence Collected

### A. W2-PM1 — Footprinting networkwalks.com

<p align="center">
  <img width="624" height="493" alt="image" src="https://github.com/user-attachments/assets/4f0df5e2-b9bd-416c-bc3b-f81406eece59" />
  <br><em>A1: date confirming the lab start time (14:01 EAT, 12 Nov 2026)</em>
</p>

```text
whois networkwalks.com — full output saved as whois.txt (see Figure 1)
whatweb networkwalks.com — full output saved as whatweb.txt (see Figure 2)
nslookup networkwalks.com — full output saved as nslookup.txt (see Figure 3)
curl -I https://networkwalks.com — full output saved as curl.txt (see Figure 4)
wafw00f networkwalks.com — full output saved as wafw00f.txt (see Figure 5)
```

**Full dnsrecon output (12 November 2026):**

```text
2026-11-12T20:37:41+0300 INFO Starting enumeration for domain: networkwalks.com
2026-11-12T20:37:42+0300 ERROR No answer for DNSSEC query for networkwalks.com
2026-11-12T20:37:42+0300 INFO     SOA ns6135.hostgator.com 50.87.144.87
2026-11-12T20:37:43+0300 INFO     NS ns6135.hostgator.com 50.87.144.87
2026-11-12T20:37:44+0300 INFO     Bind Version for 50.87.144.87 "9.16.23-RH"
2026-11-12T20:37:44+0300 INFO     NS ns6136.hostgator.com 192.232.216.131
2026-11-12T20:37:45+0300 INFO     Bind Version for 192.232.216.131 "9.16.23-RH"
2026-11-12T20:37:49+0300 INFO     MX mail.networkwalks.com 192.232.216.135
2026-11-12T20:37:50+0300 INFO     A networkwalks.com 192.232.216.135
2026-11-12T20:37:51+0300 INFO     TXT networkwalks.com google-site-verification=rr04eRmqHoWY3XemnizDNVK4q75X-Ij-mjgEeg-UsYI
2026-11-12T20:37:51+0300 INFO     TXT networkwalks.com v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all
2026-11-12T20:37:52+0300 INFO Enumerating SRV Records
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.204.15 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.204.14 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.203.14 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.204.9 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.203.9 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.204.11 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.203.11 443
2026-11-12T20:37:59+0300 INFO     SRV _autodiscover._tcp.networkwalks.com cpanelemaildiscovery.cpanel.net 184.94.203.15 443
2026-11-12T20:37:59+0300 INFO 8 Records Found
```

### B. W2-PM5 — Zenmap scanning of my home LAN

```text
Starting Nmap 7.80 ( https://nmap.org ) at 2026-11-11 16:12 E. Africa Standard Time
Nmap scan report for router.lan (192.168.88.1) — Host is up (0.0049s latency). MAC: 18:FD:74:8A:B3:CF (Unknown)
Nmap scan report for 192.168.88.227 — Host is up (0.054s latency). MAC: 92:DA:AD:FB:0D:54 (Unknown)
Nmap scan report for 192.168.88.229 — Host is up (0.026s latency). MAC: D6:9C:B3:13:D5:6B (Unknown)
Nmap scan report for 192.168.88.237 — Host is up (0.024s latency). MAC: 8E:63:CD:B7:BB:80 (Unknown)
Nmap scan report for 192.168.88.231 — Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 17.78 seconds
```

<p align="center">
  <img width="260" height="324" alt="image" src="https://github.com/user-attachments/assets/65d904fa-a43d-4324-9122-e7cbeaa26844" />
  <br><em>B1: Zenmap Help &gt; About, confirming version 7.80</em>
</p>

<p align="center">
  <img width="480" height="379" alt="image" src="https://github.com/user-attachments/assets/6345b1a3-d861-4727-9282-0baed53324f1" />
  <br><em>B2: full ipconfig /all output</em>
</p>
