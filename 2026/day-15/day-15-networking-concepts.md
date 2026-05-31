# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

**Author:** Syed Basit Aftab
**Date:** May 31, 2026
**Environment:** Amazon Linux 2023 | AWS EC2 (t2.micro)

---

## Task 1: DNS – How Names Become IPs

### What happens when you type google.com in a browser?

1. Browser checks its **local cache** first — if it visited google.com recently, it already knows the IP
2. If not cached, it asks the **OS resolver** which checks `/etc/resolv.conf` for the configured DNS server (on AWS EC2 this is `172.31.0.2` — the VPC DNS resolver)
3. The DNS resolver queries a **Root nameserver** → then a **TLD nameserver** (`.com`) → then **Google's authoritative nameserver**
4. The authoritative nameserver returns the **A record** (IP address), the browser connects to that IP over TCP port 80 or 443, and the page loads

The whole process typically takes under 5ms on a warm resolver.

---

### DNS Record Types

| Record | What it does |
|---|---|
| **A** | Maps a domain name to an IPv4 address (e.g., `google.com → 142.250.80.46`) |
| **AAAA** | Maps a domain name to an IPv6 address (e.g., `google.com → 2607:f8b0:4004::200e`) |
| **CNAME** | Alias — points one domain to another domain, not an IP (e.g., `www.example.com → example.com`) |
| **MX** | Mail Exchange — tells email servers where to deliver mail for a domain |
| **NS** | Nameserver — identifies which DNS servers are authoritative for a domain |

---

### dig google.com — Output

```bash
$ dig google.com

; <<>> DiG 9.16.11-RedHat <<>> google.com
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             207     IN      A       142.250.80.46

;; Query time: 1 msec
;; SERVER: 172.31.0.2#53(172.31.0.2)
;; WHEN: Sat May 31 13:00:00 UTC 2026
```

**Identified:**
- **A record:** `google.com → 142.250.80.46`
- **TTL:** `207 seconds` — after 207 seconds, the resolver discards the cached answer and queries again
- **DNS Server:** `172.31.0.2` — the AWS VPC resolver (always VPC CIDR base + 2)

---

## Task 2: IP Addressing

### What is an IPv4 address?

An IPv4 address is a **32-bit number** written as 4 groups of decimal numbers separated by dots — called octets.

```
192  .  168  .  1  .  10
 ^       ^      ^    ^
 8bits  8bits  8bits  8bits  =  32 bits total
```

Each octet ranges from **0–255**. The address has two parts:
- **Network portion** — identifies which network the device is on
- **Host portion** — identifies the specific device on that network

The split between network/host is defined by the **subnet mask**.

---

### Public vs Private IPs

| Type | Example | Who assigns it | Routable on internet? |
|---|---|---|---|
| **Public** | `142.250.80.46` | ISP / AWS | ✅ Yes |
| **Private** | `172.31.23.82` | You / RFC 1918 | ❌ No |

**Public IP** — globally unique, reachable from anywhere on the internet. Your EC2's public IP is assigned by AWS and NATted through the Internet Gateway.

**Private IP** — only routable within a private network (your VPC, home network, corporate LAN). Multiple organizations can use the same private IPs — they never collide on the internet.

---

### Private IP Ranges (RFC 1918)

| Range | CIDR | Common use |
|---|---|---|
| `10.0.0.0 – 10.255.255.255` | 10.0.0.0/8 | Large enterprise networks, AWS VPCs |
| `172.16.0.0 – 172.31.255.255` | 172.16.0.0/12 | AWS default VPC (172.31.0.0/16) |
| `192.168.0.0 – 192.168.255.255` | 192.168.0.0/16 | Home routers, small office networks |

---

### ip addr show — Identifying Private IPs

```bash
$ ip addr show
2: enX0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001
    inet 172.31.23.82/20 brd 172.31.31.255 scope global dynamic enX0
```

**Analysis:**
- `172.31.23.82` → falls in `172.16.0.0 – 172.31.255.255` range → **Private IP** ✅
- This is the EC2's internal VPC address — AWS's default VPC uses `172.31.0.0/16`
- The actual public IP (`34.203.201.230`) is NOT shown here — it exists only at the Internet Gateway level as a NAT mapping

---

## Task 3: CIDR & Subnetting

### What does /24 mean in 192.168.1.0/24?

The `/24` is the **prefix length** — it tells you how many bits of the 32-bit IP address are the **network portion**.

```
192.168.1.0/24

11000000.10101000.00000001.00000000
|--------- 24 bits network --------|---- 8 bits host ----|
```

- **24 bits** locked for the network = `192.168.1`
- **8 bits** free for hosts = `.0` through `.255`
- Subnet mask = `255.255.255.0`

The larger the prefix number, the **smaller** the network (fewer hosts available).

---

### Why do we subnet?

In my own words: imagine giving every device in the world one huge flat network — finding anything would be chaos, and one broadcast would hit every device on earth.

Subnetting solves this by **dividing a large IP space into smaller logical networks**:

- **Security** — separate subnets for public-facing servers vs internal databases (like AWS public vs private subnets)
- **Performance** — smaller broadcast domains mean less noise
- **Organization** — group devices by function, team, or location
- **Efficiency** — allocate only the IPs you need, don't waste the entire block

In AWS specifically: subnetting is how you isolate your EC2 web servers (public subnet) from your RDS databases (private subnet) — different CIDR blocks, different route tables, different security posture.

---

### CIDR Table

| CIDR | Subnet Mask | Total IPs | Usable Hosts |
|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 254 |
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /28 | 255.255.255.240 | 16 | 14 |

**Why usable = total - 2?**
- First IP = **Network address** (identifies the subnet itself)
- Last IP = **Broadcast address** (sends to all devices on the subnet)
- Both are reserved — you can't assign them to a host

**AWS note:** AWS actually reserves **5 IPs** per subnet (network, VPC router, DNS, future use, broadcast) — so a /24 in AWS gives you 251 usable IPs, not 254.

---

## Task 4: Ports – The Doors to Services

### What is a port? Why do we need them?

An IP address gets you to the right **machine**. A port gets you to the right **service** on that machine.

Think of it like an apartment building: the IP is the building address, the port is the apartment number. Without ports, your machine wouldn't know whether an incoming packet is meant for your web server, your SSH daemon, or your database.

Ports range from **0–65535**:
- **0–1023** = Well-known ports (reserved for standard services, need root to bind)
- **1024–49151** = Registered ports (apps like databases use these)
- **49152–65535** = Dynamic/ephemeral ports (used by clients for outbound connections)

---

### Common Ports

| Port | Service | What it does |
|---|---|---|
| **22** | SSH | Secure Shell — encrypted remote terminal access to servers |
| **80** | HTTP | Unencrypted web traffic — browser to web server |
| **443** | HTTPS | Encrypted web traffic — HTTP over TLS/SSL |
| **53** | DNS | Domain Name System — resolves hostnames to IPs (UDP primarily) |
| **3306** | MySQL | MySQL/MariaDB database connections |
| **6379** | Redis | Redis in-memory cache/database |
| **27017** | MongoDB | MongoDB NoSQL database connections |

---

### ss -tulpn — Matching Ports to Services

```bash
$ ss -tulpn
Netid  State   Local Address:Port   Process
tcp    LISTEN  0.0.0.0:22           users:(("sshd",pid=2132))
tcp    LISTEN  0.0.0.0:80           users:(("nginx",pid=4385))
tcp    LISTEN  [::]:22              users:(("sshd",pid=2132))
tcp    LISTEN  [::]:80              users:(("nginx",pid=4385))
```

**Matched:**
- Port **22** → `sshd` — SSH daemon, matches the well-known port table ✅
- Port **80** → `nginx` — web server serving HTTP, matches port table ✅

---

## Task 5: Putting It Together

### You run `curl http://myapp.com:8080` — what networking concepts are involved?

1. **DNS** — `myapp.com` is resolved to an IP via a DNS A record lookup (UDP port 53)
2. **IP Addressing** — the resolved IP is used to route the packet across the network (L3)
3. **Ports** — port `8080` is a non-standard HTTP port (custom app port, not the default 80). The OS makes a TCP connection specifically to port 8080 on the destination IP
4. **TCP** — a 3-way handshake (SYN/SYN-ACK/ACK) establishes the connection before any HTTP data is sent
5. **HTTP** — the actual GET request is sent at the application layer once TCP is established

All five concepts from today — DNS, IP, subnetting (routing decisions), ports, and protocol stack — fire in sequence for a single curl command.

---

### Your app can't reach a database at 10.0.1.50:3306 — what would you check first?

`10.0.1.50` is a **private IP** — this is an internal network issue, not a public routing problem. My first checks:

1. **Is the database service actually running?**
   ```bash
   sudo systemctl status mysql    # or mysqld
   ss -tulpn | grep 3306          # is it listening?
   ```

2. **Can I reach the IP at all?**
   ```bash
   ping 10.0.1.50                 # L3 reachability
   nc -zv 10.0.1.50 3306          # L4 port reachability
   ```

3. **Is it a firewall/security group issue?**
   - Check AWS Security Group — does the DB's inbound rules allow port 3306 from the app server's IP/SG?
   - Check if they're in the same VPC/subnet or if routing is needed between subnets

4. **Subnet routing** — are both instances in subnets that can communicate? Check route tables.

The order matters: confirm the service is up → confirm network path exists → confirm firewall allows it.

---

## 📚 What I Learned — 3 Key Points

1. **DNS is a distributed system, not a single server.** The resolution chain (Root → TLD → Authoritative) is why the internet can scale to billions of domains without a single point of failure. AWS's VPC resolver at `172.31.0.2` is just the first hop in that chain.

2. **Subnetting is not just math — it's architecture.** The /24 vs /16 vs /28 decision directly determines how you isolate workloads in AWS. A miscalculated CIDR block in a VPC can't be resized without rebuilding — getting it right upfront matters.

3. **A port is not just a number — it's a contract.** When nginx binds to port 80, it's making a promise that HTTP traffic on that port will be handled correctly. Understanding ports is what lets you debug "connection refused" vs "host unreachable" — completely different problems at different layers.

---

## 🔗 Resources

- [RFC 1918 — Private IP Ranges](https://tools.ietf.org/html/rfc1918)
- [CIDR Calculator](https://cidr.xyz)
- [AWS VPC Subnetting](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html)
- [Cloudflare — How DNS Works](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [IANA Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers)

---

*Part of my #90DaysOfDevOps journey | #DevOpsKaJosh | #TrainWithShubham*
