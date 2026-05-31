# Day 14 – Networking Fundamentals & Hands-on Checks

**Author:** Syed Basit Aftab
**Date:** May 31, 2026
**Environment:** Amazon Linux 2023 | AWS EC2 (t2.micro)
**Target Host:** google.com

---

## 🧠 Quick Concepts

### OSI Model (L1–L7) vs TCP/IP Stack

| OSI Layer | Name | TCP/IP Layer | Real Example |
|---|---|---|---|
| L7 | Application | Application | HTTP, HTTPS, DNS, SSH |
| L6 | Presentation | Application | TLS/SSL encryption |
| L5 | Session | Application | Session management |
| L4 | Transport | Transport | TCP, UDP |
| L3 | Network | Internet | IP, ICMP |
| L2 | Data Link | Link | Ethernet, MAC addresses |
| L1 | Physical | Link | Cables, signals, NICs |

**In my own words:**
- **OSI** is the theoretical 7-layer model — useful for troubleshooting because it tells you *where* in the stack a problem lives
- **TCP/IP** is the practical 4-layer model — what the internet actually runs on. OSI L5/L6/L7 all collapse into TCP/IP's Application layer

### Where Protocols Sit

- **IP** → L3 (Network) — handles addressing and routing packets between machines
- **TCP/UDP** → L4 (Transport) — TCP is reliable (handshake, retransmit), UDP is fast (no guarantee)
- **HTTP/HTTPS** → L7 (Application) — the actual web request/response
- **DNS** → L7 (Application) — but it uses UDP at L4 to resolve names to IPs

### One Real Example

```
curl https://google.com
```

**What actually happens layer by layer:**
1. **L7 (App):** curl constructs an HTTP GET request
2. **L7 (App):** DNS resolves `google.com` → `142.250.80.46` via UDP:53
3. **L6 (Presentation):** TLS handshake happens — certificate verified, encryption negotiated
4. **L4 (Transport):** TCP 3-way handshake to port 443 (SYN → SYN-ACK → ACK)
5. **L3 (Network):** IP packets routed from my EC2 (172.31.23.82) → Google's server
6. **L2/L1 (Link/Physical):** Ethernet frames leave the NIC, travel through AWS backbone

---

## 🛠️ Hands-on Checklist

> **Target:** `google.com` used consistently across all checks

---

### 1. Identity — `ip addr show`

```bash
$ hostname -I
172.31.23.82

$ ip addr show
...
2: enX0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001
    inet 172.31.23.82/20 brd 172.31.31.255 scope global dynamic enX0
```

**Observation:**
- Private IP is `172.31.23.82` — this is the internal AWS VPC address (172.31.0.0/16 is the default VPC range)
- MTU is 9001 — AWS uses jumbo frames inside the VPC, higher than standard 1500
- No public IP shown here — public IP is handled by AWS's IGW NAT, not assigned to the interface directly

---

### 2. Reachability — `ping google.com`

```bash
$ ping -c 5 google.com
PING google.com (142.250.80.46) 56(84) bytes of data.
64 bytes from lga34s32-in-f14.1e100.net (142.250.80.46): icmp_seq=1 ttl=108 time=1.23 ms
64 bytes from lga34s32-in-f14.1e100.net (142.250.80.46): icmp_seq=2 ttl=108 time=1.19 ms
64 bytes from lga34s32-in-f14.1e100.net (142.250.80.46): icmp_seq=3 ttl=108 time=1.21 ms
64 bytes from lga34s32-in-f14.1e100.net (142.250.80.46): icmp_seq=4 ttl=108 time=1.18 ms
64 bytes from lga34s32-in-f14.1e100.net (142.250.80.46): icmp_seq=5 ttl=108 time=1.20 ms

--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 1.18/1.20/1.23/0.018 ms
```

**Observation:**
- **0% packet loss** — clean connectivity from EC2 to Google
- **~1.2ms average latency** — extremely low because this EC2 is in us-east-1 (N. Virginia), and Google has major infrastructure in the same region
- TTL of 108 means the packet traversed roughly 20 hops (128 - 108 = 20, or 128 starting TTL)
- ICMP operates at **L3 (Network layer)** — ping success means IP routing is working correctly

---

### 3. Path — `traceroute google.com`

```bash
$ traceroute google.com
traceroute to google.com (142.250.80.46), 30 hops max, 60 byte packets
 1  * * *                          # AWS internal hop (filtered)
 2  * * *                          # AWS backbone (filtered)
 3  52.93.24.1  0.456 ms  0.471 ms  0.461 ms
 4  52.93.24.14 0.389 ms  0.401 ms  0.398 ms
 5  * * *                          # filtered hop
 6  108.170.246.33  0.812 ms       # Google peering point
 7  142.251.49.149  1.102 ms       # Google backbone
 8  142.250.80.46   1.21 ms        # Destination
```

**Observation:**
- First 2 hops are `* * *` — AWS intentionally filters/blocks ICMP TTL-exceeded responses on internal hops for security. This is normal, not a problem
- Traffic enters Google's network at hop 6 (108.170.x.x is Google's ASN) — very few hops before reaching Google because AWS us-east-1 and Google peer directly in Northern Virginia
- No long hops or timeouts — path is clean and fast
- Total of ~8 hops to reach Google — efficient routing

---

### 4. Ports — `ss -tulpn`

```bash
$ ss -tulpn
Netid  State   Recv-Q  Send-Q  Local Address:Port  Peer Address:Port  Process
tcp    LISTEN  0       128     0.0.0.0:22           0.0.0.0:*          users:(("sshd",pid=2132))
tcp    LISTEN  0       128     0.0.0.0:80           0.0.0.0:*          users:(("nginx",pid=3201))
tcp    LISTEN  0       128     [::]:22              [::]:*             users:(("sshd",pid=2132))
tcp    LISTEN  0       128     [::]:80              [::]:*             users:(("nginx",pid=3201))
```

**Observation:**
- **Port 22 (SSH):** `sshd` is listening on all interfaces (0.0.0.0) — this is how EC2 Instance Connect reaches the server
- **Port 80 (HTTP):** `nginx` is listening — web server is running and ready to serve traffic
- `ss` is the modern replacement for `netstat` — faster, reads directly from kernel socket tables
- `0.0.0.0` means listening on all IPv4 interfaces; `[::]` means all IPv6 interfaces

---

### 5. Name Resolution — `dig google.com`

```bash
$ dig google.com

; <<>> DiG 9.16.11-RedHat-9.16.11 <<>> google.com
;; ANSWER SECTION:
google.com.     207     IN      A       142.250.80.46

;; Query time: 1 msec
;; SERVER: 172.31.0.2#53
```

**Observation:**
- `google.com` resolves to `142.250.80.46`
- DNS query answered in **1ms** — query went to `172.31.0.2` which is the **AWS VPC DNS resolver** (always at VPC_CIDR + 2). AWS handles DNS internally, not going out to 8.8.8.8
- TTL of 207 seconds — Google rotates IPs frequently for load balancing, short TTL ensures clients get fresh IPs
- DNS uses **UDP port 53** at L4, application layer protocol at L7

---

### 6. HTTP Check — `curl -I https://google.com`

```bash
$ curl -I https://google.com
HTTP/2 301
location: https://www.google.com/
content-type: text/html; charset=UTF-8
server: gws
x-xss-protection: 0
x-frame-options: SAMEORIGIN
alt-svc: h3=":443"; ma=2592000
```

**Observation:**
- **HTTP 301** — permanent redirect from `google.com` → `www.google.com`. This is expected — Google redirects the apex domain to www
- **HTTP/2** is being used — not HTTP/1.1. HTTP/2 is multiplexed (multiple requests over one TCP connection)
- `server: gws` — Google Web Server (Google's custom web server, not nginx/apache)
- `-I` flag = HEAD request only (fetches headers, not body) — useful for quick HTTP checks without downloading content
- This entire exchange happens at **L7 (Application layer)**, over TLS at L6, over TCP at L4

---

### 7. Connections Snapshot — `netstat -an | head`

```bash
$ netstat -an | head -20
Active Internet connections (servers and established)
Proto  Recv-Q  Send-Q  Local Address       Foreign Address     State
tcp    0       0       0.0.0.0:22          0.0.0.0:*           LISTEN
tcp    0       0       0.0.0.0:80          0.0.0.0:*           LISTEN
tcp    0       0       172.31.23.82:22     18.206.107.28:56411 ESTABLISHED
tcp    0       0       172.31.23.82:22     18.206.107.28:56744 ESTABLISHED
tcp6   0       0       :::22               :::*                LISTEN
tcp6   0       0       :::80               :::*                LISTEN
```

**Observation:**
- **LISTEN:** 4 entries — sshd (port 22 IPv4 + IPv6), nginx (port 80 IPv4 + IPv6)
- **ESTABLISHED:** 2 entries — both are active SSH sessions from `18.206.107.28` (AWS EC2 Instance Connect IP range)
- 0 connections to port 80 currently — nginx is ready but no web traffic hitting it right now
- `netstat` is being deprecated in favor of `ss`, but still works and is widely used in the field

---

## 🔍 Mini Task: Port Probe & Interpret

**Identified port:** SSH on port 22 (from `ss -tulpn` output above)

```bash
$ nc -zv localhost 22
Connection to localhost (127.0.0.1) 22 port [tcp/ssh] succeeded!

$ curl -I http://localhost:80
HTTP/1.1 200 OK
Server: nginx/1.24.0
Date: Sat, 31 May 2026 13:45:00 GMT
Content-Type: text/html
```

**Result:**
- Port 22: ✅ Reachable — `nc` connected successfully, SSH is healthy
- Port 80: ✅ Reachable — nginx returned HTTP 200, web server is serving correctly

**If port 22 was NOT reachable, next checks would be:**
1. `sudo systemctl status sshd` — is the service actually running?
2. Check security group inbound rules in AWS console — is port 22 allowed from this source IP?
3. `sudo iptables -L` — is a local firewall blocking it?

---

## 💭 Reflection

### Which command gives you the fastest signal when something is broken?

**`ping`** — it's the first thing I run. One command tells you immediately:
- Is the host reachable at all? (L3 connectivity)
- Is there packet loss? (network stability)
- What's the latency? (rough performance baseline)

If ping fails → you know it's a routing/network issue before you waste time checking app config.

### What layer would you inspect if DNS fails?

- DNS fails → start at **L7 (Application)**: check `/etc/resolv.conf`, test with `dig @8.8.8.8 domain.com` to bypass local resolver
- If that works → the issue is your configured DNS server (L7 config problem)
- If that fails → go to **L3 (Network)**: can you reach 8.8.8.8 at all? (`ping 8.8.8.8`)
- Still failing → check **L2/L1**: interface up? Route table correct?

### What layer if HTTP 500 shows up?

HTTP 500 = server-side error → stays at **L7 (Application layer)**:
- Check app logs: `journalctl -u nginx` or `/var/log/nginx/error.log`
- Check if backend service is running (db, app server)
- Check file permissions, config syntax errors
- Network layer is fine — the request reached the server, the server just broke while processing it

### Two follow-up checks in a real incident

```bash
# 1. Check if the service is actually running
sudo systemctl status <service>

# 2. Tail the logs in real time while reproducing the issue
sudo journalctl -u <service> -f
```

---

## 📚 Commands Reference Card

| Command | What it checks | OSI Layer |
|---|---|---|
| `ping` | Reachability, latency, packet loss | L3 |
| `traceroute` | Path to destination, where delays/drops occur | L3 |
| `dig` / `nslookup` | DNS resolution | L7 |
| `curl -I` | HTTP response, status codes, headers | L7 |
| `ss -tulpn` | Listening ports and bound services | L4 |
| `netstat -an` | All active connections + states | L4 |
| `nc -zv` | Port connectivity test | L4 |
| `ip addr show` | Interface IPs, MTU, state | L2/L3 |

---

## 🔗 Resources

- [AWS VPC DNS Resolution](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)
- [ss command man page](https://man7.org/linux/man-pages/man8/ss.8.html)
- [Cloudflare — How DNS works](https://www.cloudflare.com/learning/dns/what-is-dns/)

---

*Part of my #90DaysOfDevOps journey | #DevOpsKaJosh | #TrainWithShubham*
