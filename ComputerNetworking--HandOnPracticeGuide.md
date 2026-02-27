# 🌐 Computer Networks — Hands-On Practice Repository
### *Labs, Exercises, Commands, and Subnetting Drills — From Zero to Practitioner*

> **"You don't understand networking until you've watched a packet in Wireshark."**
> This repository contains 8 progressive lab modules using free tools — Cisco Packet Tracer, Wireshark, and your own terminal. Complete all modules and you will have genuine hands-on networking ability.

---

## 📌 Tools Required (All Free)

| Tool | Purpose | Download |
|------|---------|---------|
| **Cisco Packet Tracer** | Network simulation — build virtual networks | netacad.com (free account) |
| **Wireshark** | Packet capture and analysis | wireshark.org |
| **Command Prompt / Terminal** | Network commands | Built into Windows/Linux/Mac |
| **Pencil and Paper** | Subnetting exercises | Your desk |
| **Subnet calculator** | Verify your answers only | subnet-calculator.com |

---

## Module 1: Command-Line Networking Essentials

**Time:** 1–2 hours
**Tools:** Windows CMD or Linux/Mac Terminal
**No simulation needed — use your own computer**

---

### 1.1 — ipconfig / ifconfig — Know Your Own Network

On **Windows**, open Command Prompt (Win+R → cmd → Enter):
```
ipconfig /all
```

On **Linux/Mac**, open Terminal:
```
ifconfig
```
or (modern Linux):
```
ip addr show
```

**What to look for and understand:**

| Field | What It Means |
|-------|--------------|
| IPv4 Address | Your device's IP address on the local network |
| Subnet Mask | Defines your network boundary |
| Default Gateway | Your router's IP — where traffic exits to the internet |
| DNS Servers | The servers resolving domain names for you |
| Physical Address (MAC) | Your NIC's hardware address |
| DHCP Enabled | Whether your IP was assigned automatically |
| Lease Obtained / Expires | When your DHCP lease started and when it expires |

**Exercise 1.1:** Write down your answers:
- What is your IP address? Is it private or public?
- What is your subnet mask? What prefix length (/XX) does it correspond to?
- What is your default gateway? (This is your router's IP)
- Are you on 192.168.x.x or 10.x.x.x or 172.16-31.x.x? Which RFC 1918 range is it?
- What is your MAC address? Look up the first 3 bytes (OUI) at macvendors.com — who made your NIC?

---

### 1.2 — ping — Test Connectivity

Ping uses ICMP Echo Request and Echo Reply to test whether a host is reachable and measure round-trip time.

```cmd
:: Windows
ping 8.8.8.8
ping google.com
ping 127.0.0.1
ping <your default gateway IP>
```

```bash
# Linux/Mac
ping -c 4 8.8.8.8
ping -c 4 google.com
ping -c 1 127.0.0.1
```

**Exercise 1.2 — Ping Tests:**

Run each ping and record the results:

| Target | Command | Result | What It Tests |
|--------|---------|--------|--------------|
| 127.0.0.1 | ping 127.0.0.1 | __ ms | Loopback — is TCP/IP stack working? |
| Your gateway | ping 192.168.x.1 | __ ms | Can you reach your router? |
| 8.8.8.8 | ping 8.8.8.8 | __ ms | Can you reach the internet? (by IP) |
| google.com | ping google.com | __ ms | Is DNS working? |

**Interpreting Results:**
- If 127.0.0.1 fails → TCP/IP stack problem on your device
- If gateway fails but loopback works → Local network problem
- If 8.8.8.8 fails but gateway works → ISP or routing problem
- If google.com fails but 8.8.8.8 works → DNS problem

**Exercise 1.3 — Geography of Latency:**
Ping the following servers and observe the latency differences:
```
ping 1.1.1.1          (Cloudflare — likely closest to India)
ping 8.8.8.8          (Google DNS — US servers)
ping speedtest.net
```
From Bhopal, India, you should typically see:
- 1.1.1.1: 5–20 ms (Cloudflare has servers in India)
- 8.8.8.8: 20–50 ms (Google has Indian data centers)

The latency difference demonstrates the physical reality of network distance.

---

### 1.3 — traceroute / tracert — See the Path

Traceroute reveals every router hop between you and a destination.

```cmd
:: Windows
tracert google.com
tracert 8.8.8.8
```

```bash
# Linux
traceroute google.com

# Mac
traceroute google.com
```

**Exercise 1.4 — Analyze a Traceroute:**

Run `tracert google.com` and analyze the output:

```
Tracing route to google.com [142.250.195.78]

  1    2 ms    1 ms    2 ms  192.168.1.1        ← Your router
  2   12 ms   11 ms   12 ms  10.x.x.x           ← Your ISP's first router
  3   15 ms   14 ms   15 ms  111.x.x.x          ← ISP backbone
  4   22 ms   23 ms   21 ms  74.x.x.x           ← Peering point
  5   28 ms   27 ms   28 ms  142.250.x.x        ← Google's network
  6   29 ms   28 ms   29 ms  142.250.195.78     ← Destination
```

For each hop, identify:
- Hop 1: Always your default gateway (router)
- Hops 2–3: Your ISP's internal network
- Later hops: Upstream providers and eventually the destination
- `* * *`: Router not responding to ICMP (firewall blocking traceroute) — not a failure

Note: How many hops to reach Google? Typically 8–15 from India. Record the hop where latency jumps significantly — that is often where your traffic enters a different network or crosses a long-distance link.

---

### 1.4 — nslookup / dig — DNS Queries

```cmd
:: Windows
nslookup google.com
nslookup google.com 8.8.8.8
nslookup -type=MX gmail.com
nslookup -type=NS google.com
```

```bash
# Linux/Mac — dig is more powerful
dig google.com
dig google.com @8.8.8.8
dig MX gmail.com
dig NS google.com
dig google.com +trace
```

**Exercise 1.5 — DNS Investigation:**

Answer these questions using nslookup/dig:
1. What IP address does `google.com` resolve to?
2. What are the mail servers (MX records) for `gmail.com`? Note the priority numbers.
3. What are the name servers (NS records) for `google.com`?
4. Run `nslookup google.com 1.1.1.1` — did you get the same IP as with the default resolver?
5. (Linux/Mac) Run `dig google.com +trace` — watch the entire DNS resolution from root servers down to the authoritative answer.

---

### 1.5 — arp — View the ARP Table

The ARP table shows the mapping between IP addresses and MAC addresses your device has learned.

```cmd
:: Windows
arp -a
```

```bash
# Linux/Mac
arp -n
```

**Exercise 1.6:**
- How many entries are in your ARP table?
- Can you identify your router's entry? (It should be your default gateway IP)
- Note the MAC address of your router — the first 3 bytes identify the manufacturer

---

### 1.6 — netstat — Active Connections

```cmd
:: Windows
netstat -an
netstat -b          (show which program owns each connection — run as admin)
```

```bash
# Linux/Mac
netstat -tuln
ss -tuln            (modern alternative)
```

**Exercise 1.7:**
Run `netstat -an` while a webpage is open in your browser.
- Find connections with state ESTABLISHED — these are active TCP connections
- Note the remote IP addresses — these are the servers your browser is connected to
- Note the port numbers — port 443 = HTTPS connections
- Find LISTEN entries — these are services on your computer waiting for connections

---

## Module 2: Subnetting Drills — 50 Problems

**Time:** 3–4 hours spread over multiple sessions
**Tools:** Pencil and paper (verify answers with subnet calculator after completing each section)

---

### The Method — Solve Every Subnetting Problem in 5 Steps

**Step 1:** Convert the subnet mask to prefix length (or vice versa)
**Step 2:** Find the block size = 256 – value of the interesting octet in the mask
**Step 3:** Find the network address (round down to the nearest multiple of block size)
**Step 4:** Find the broadcast address (next multiple of block size – 1)
**Step 5:** First host = Network + 1, Last host = Broadcast – 1, Usable hosts = 2^(host bits) – 2

---

### Section A — Basic: Given IP and Mask, Find Network Info (Problems 1–20)

For each problem, find: Network Address, Broadcast Address, First Usable Host, Last Usable Host, Number of Usable Hosts.

| # | IP Address | Subnet Mask | 
|---|-----------|-------------|
| 1 | 192.168.1.50 | 255.255.255.0 |
| 2 | 10.0.0.100 | 255.0.0.0 |
| 3 | 172.16.5.200 | 255.255.0.0 |
| 4 | 192.168.10.75 | 255.255.255.128 |
| 5 | 192.168.10.200 | 255.255.255.128 |
| 6 | 172.20.100.50 | 255.255.255.192 |
| 7 | 10.10.10.130 | 255.255.255.192 |
| 8 | 192.168.5.85 | 255.255.255.224 |
| 9 | 192.168.5.140 | 255.255.255.224 |
| 10 | 192.168.5.200 | 255.255.255.224 |
| 11 | 172.16.50.100 | 255.255.255.240 |
| 12 | 10.5.6.7 | 255.255.255.248 |
| 13 | 192.168.100.25 | 255.255.255.252 |
| 14 | 172.31.255.100 | 255.255.128.0 |
| 15 | 10.50.100.200 | 255.255.192.0 |
| 16 | 192.168.25.130 | 255.255.255.128 |
| 17 | 172.16.0.50 | 255.255.254.0 |
| 18 | 10.0.50.100 | 255.255.240.0 |
| 19 | 192.168.15.200 | 255.255.255.192 |
| 20 | 172.20.30.50 | 255.255.252.0 |

---

### Answer Key — Section A

| # | Network Address | Broadcast | First Host | Last Host | Usable Hosts |
|---|----------------|-----------|-----------|----------|-------------|
| 1 | 192.168.1.0 | 192.168.1.255 | 192.168.1.1 | 192.168.1.254 | 254 |
| 2 | 10.0.0.0 | 10.255.255.255 | 10.0.0.1 | 10.255.255.254 | 16,777,214 |
| 3 | 172.16.0.0 | 172.16.255.255 | 172.16.0.1 | 172.16.255.254 | 65,534 |
| 4 | 192.168.10.0 | 192.168.10.127 | 192.168.10.1 | 192.168.10.126 | 126 |
| 5 | 192.168.10.128 | 192.168.10.255 | 192.168.10.129 | 192.168.10.254 | 126 |
| 6 | 172.20.100.0 | 172.20.100.63 | 172.20.100.1 | 172.20.100.62 | 62 |
| 7 | 10.10.10.128 | 10.10.10.191 | 10.10.10.129 | 10.10.10.190 | 62 |
| 8 | 192.168.5.64 | 192.168.5.95 | 192.168.5.65 | 192.168.5.94 | 30 |
| 9 | 192.168.5.128 | 192.168.5.159 | 192.168.5.129 | 192.168.5.158 | 30 |
| 10 | 192.168.5.192 | 192.168.5.223 | 192.168.5.193 | 192.168.5.222 | 30 |
| 11 | 172.16.50.96 | 172.16.50.111 | 172.16.50.97 | 172.16.50.110 | 14 |
| 12 | 10.5.6.0 | 10.5.6.7 | 10.5.6.1 | 10.5.6.6 | 6 |
| 13 | 192.168.100.24 | 192.168.100.27 | 192.168.100.25 | 192.168.100.26 | 2 |
| 14 | 172.31.128.0 | 172.31.255.255 | 172.31.128.1 | 172.31.255.254 | 32,766 |
| 15 | 10.50.64.0 | 10.50.127.255 | 10.50.64.1 | 10.50.127.254 | 16,382 |
| 16 | 192.168.25.128 | 192.168.25.255 | 192.168.25.129 | 192.168.25.254 | 126 |
| 17 | 172.16.0.0 | 172.16.1.255 | 172.16.0.1 | 172.16.1.254 | 510 |
| 18 | 10.0.48.0 | 10.0.63.255 | 10.0.48.1 | 10.0.63.254 | 4,094 |
| 19 | 192.168.15.192 | 192.168.15.255 | 192.168.15.193 | 192.168.15.254 | 62 |
| 20 | 172.20.28.0 | 172.20.31.255 | 172.20.28.1 | 172.20.31.254 | 1,022 |

---

### Section B — Design: Create Subnets (Problems 21–35)

For each problem, design the subnet scheme and list all subnets with their ranges.

**Problem 21:** Divide `192.168.5.0/24` into 2 equal subnets. List both subnets.

**Problem 22:** Divide `192.168.5.0/24` into 4 equal subnets. List all 4.

**Problem 23:** Divide `192.168.5.0/24` into 8 equal subnets. List all 8.

**Problem 24:** Divide `10.0.0.0/8` into subnets of exactly 254 usable hosts each. What prefix length do you use? How many subnets can you create?

**Problem 25:** You need a subnet from `172.16.0.0/16` that accommodates exactly 500 hosts. What is the smallest subnet that works? What prefix length?

**Problem 26:** A company has `192.168.20.0/24`. They need:
- Subnet A: 60 hosts (Sales)
- Subnet B: 30 hosts (IT)
- Subnet C: 14 hosts (Management)
- Subnet D: 6 hosts (Servers)
Assign subnets using VLSM, starting from .0 and not wasting addresses.

**Problem 27:** Two routers need a point-to-point link. You want to use the smallest possible subnet. What prefix length do you use? How many usable hosts? What are the only two valid host addresses?

**Problem 28:** Given `10.10.0.0/16`, create 16 equal-sized subnets. What is the prefix length? What is the block size? List the first 4 subnets.

**Problem 29:** Is host `192.168.1.130` in the same subnet as `192.168.1.100` if the mask is `255.255.255.128`? Show your working.

**Problem 30:** Is host `10.5.10.100` in the same subnet as `10.5.10.200` if the mask is `255.255.255.0`? How about with mask `255.255.0.0`?

**Problems 31–35 — Same or Different Subnet?**

For each pair, determine if they are on the same subnet with the given mask:

| # | IP 1 | IP 2 | Mask | Same Subnet? |
|---|------|------|------|-------------|
| 31 | 192.168.1.50 | 192.168.1.180 | 255.255.255.128 | ? |
| 32 | 172.16.5.100 | 172.16.6.100 | 255.255.254.0 | ? |
| 33 | 10.0.0.255 | 10.0.1.0 | 255.255.254.0 | ? |
| 34 | 192.168.10.64 | 192.168.10.127 | 255.255.255.192 | ? |
| 35 | 192.168.10.63 | 192.168.10.64 | 255.255.255.192 | ? |

**Answers 31–35:** 31: No (50 is in .0/25, 180 is in .128/25). 32: Yes (both in 172.16.4.0/23). 33: Yes (both in 10.0.0.0/23). 34: Yes (both in 192.168.10.64/26). 35: No (63 is in .0/26, 64 is in .64/26).

---

### Section C — Applied Problems (Problems 36–50)

**Problem 36:** A router interface has IP `172.16.45.130/26`. What is the network address? What is the valid host range? Could `172.16.45.190` be on the same network?

**Problem 37:** A network administrator has `192.168.100.0/24` and needs to create subnets for these departments:
- HR: 25 employees
- Finance: 12 employees
- Engineering: 50 employees
- Guest Wi-Fi: 100 guests
Assign using VLSM. Start from .0. Minimize wasted addresses. Show each subnet's network address, mask, range, and usable hosts.

**Problem 38:** Convert the following to CIDR notation:
- 255.255.255.0 = /___
- 255.255.255.128 = /___
- 255.255.255.192 = /___
- 255.255.255.224 = /___
- 255.255.255.240 = /___
- 255.255.0.0 = /___
- 255.255.128.0 = /___
- 255.255.192.0 = /___

**Problem 39:** Convert the following to dotted decimal masks:
- /17 = 255.255.___.0
- /19 = 255.255.___.0
- /21 = 255.255.___.0
- /27 = 255.255.255.___
- /28 = 255.255.255.___
- /29 = 255.255.255.___

**Problem 40:** A company has a Class B address `172.20.0.0/16`. The network admin wants to create subnets supporting up to 1,022 hosts each. What mask is needed? How many such subnets can be created from this block?

**Problem 41:** You are given `10.1.0.0/16` for a branch office. The branch has 3 departments needing connectivity:
- Dept A needs 200 hosts
- Dept B needs 100 hosts
- Dept C needs 50 hosts
- Router links (2 point-to-point links needed)
Design a VLSM addressing scheme. List every subnet with network address, mask, and usable range.

**Problem 42:** What is the subnet address of `192.168.255.200/25`?

**Problem 43:** A host has IP `10.100.50.75` and mask `255.255.252.0`. What is the broadcast address of its subnet?

**Problem 44:** How many hosts can a /22 network support?

**Problem 45:** You see an IP address `169.254.10.50` on a Windows machine. What does this indicate? Is this a normal configuration?

**Problem 46:** A server needs to be accessible from the internet. Should it have a private IP or a public IP configured on its internet-facing interface? What mechanism allows devices with private IPs to access the internet?

**Problem 47:** Supernetting (route summarization): A router has routes for `192.168.0.0/24`, `192.168.1.0/24`, `192.168.2.0/24`, and `192.168.3.0/24`. Can these be summarized into a single route? What would it be?

**Problem 48:** A company has 6 branch offices, each needing 30 hosts. They have been given `172.31.0.0/24`. Design the 6 subnets.

**Problem 49:** True or False and explain: Two devices with IPs `192.168.1.1/24` and `192.168.2.1/24` can communicate without a router.

**Problem 50:** A company's IT policy requires that servers (10 of them), printers (5 of them), management stations (3 of them), and user workstations (200 of them) be in separate subnets from `10.0.0.0/24`. Design the four subnets using VLSM.

---

## Module 3: Cisco Packet Tracer — Lab 1: Basic Network

**Time:** 2 hours
**Tool:** Cisco Packet Tracer
**Topology:** Star LAN with DHCP

---

### Objective

Build a basic star topology network, configure a DHCP server, and verify connectivity between all devices.

---

### Step-by-Step Lab

**Step 1: Launch Packet Tracer and Add Devices**

Open Packet Tracer. In the bottom-left panel, you'll see device categories. Add the following to the workspace:

- 1 × **2960 Switch** (Switches → 2960)
- 1 × **Server-PT** (End Devices → Server)
- 4 × **PC-PT** (End Devices → PC)

Arrange them in a star: switch in the center, all other devices connected to it.

**Step 2: Connect the Devices**

Use **Copper Straight-Through** cables (Connections → Lightning bolt auto-select works too).
Connect:
- Server → Switch (FastEthernet0 → FastEthernet0/1)
- PC0 → Switch (FastEthernet0 → FastEthernet0/2)
- PC1 → Switch FastEthernet0/3
- PC2 → Switch FastEthernet0/4
- PC3 → Switch FastEthernet0/5

After connecting, the link indicators will turn green (may take a moment — STP is converging).

**Step 3: Configure the DHCP Server**

Click the Server → Desktop tab → IP Configuration.
Set a static IP manually:
- IP Address: 192.168.1.1
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.1.1 (server IS the gateway for this lab)

Now click the Services tab → DHCP.
Turn DHCP Service: **On**
Configure:
- Default Gateway: 192.168.1.1
- DNS Server: 192.168.1.1
- Start IP Address: 192.168.1.10
- Subnet Mask: 255.255.255.0
- Maximum number of users: 50

Click Save.

**Step 4: Configure PCs to Use DHCP**

Click PC0 → Desktop → IP Configuration → Select **DHCP**.
Watch the IP, Subnet Mask, Default Gateway, and DNS fields populate automatically.

Repeat for PC1, PC2, and PC3.

**Step 5: Verify Connectivity**

On PC0 → Desktop → Command Prompt:
```
ipconfig
```
Note the assigned IP (should be 192.168.1.10, 11, 12, 13).

```
ping 192.168.1.1
```
Should reply — PC can reach the server.

```
ping 192.168.1.11
```
Should reply — PC0 can reach PC1 (substitute the actual IP of PC1).

**Step 6: Check the Switch's MAC Address Table**

Click the Switch → CLI tab.
Type:
```
enable
show mac address-table
```

You should see entries for each PC's MAC address mapped to the port it's connected to. This is the switch's learned MAC table. Note which MACs are on which ports.

**Step 7: Observe a Ping in Simulation Mode**

Switch Packet Tracer from Realtime to **Simulation mode** (bottom right — clock icon).

From PC0's Command Prompt, ping PC1's IP.

Click **Play** in the simulation panel. Watch the ICMP packets travel from PC0 → Switch → PC1 → Switch → PC0.

Click on any packet envelope to see its contents at each layer. This is encapsulation in action — you can see the Ethernet frame with MAC addresses at Layer 2 and the IP packet with IP addresses at Layer 3.

---

### Lab 1 Verification

- [ ] All 4 PCs received IP addresses via DHCP in the 192.168.1.10–192.168.1.59 range
- [ ] PC0 can ping PC1, PC2, and PC3
- [ ] PC0 can ping the server (192.168.1.1)
- [ ] Switch MAC table shows all 5 devices
- [ ] Simulation mode shows correct packet flow

---

## Module 4: Cisco Packet Tracer — Lab 2: Routing Between Networks

**Time:** 3 hours
**Tool:** Cisco Packet Tracer
**Topology:** Two LANs connected by a router

---

### Objective

Build two separate networks connected by a router. Configure IP addresses. Verify that devices on different networks can communicate through the router.

---

### Topology

```
Network A: 192.168.1.0/24          Network B: 192.168.2.0/24
                                   
PC-A1 ──┐                          ┌── PC-B1
         ├── Switch-A ── Router ── Switch-B ──┤
PC-A2 ──┘                          └── PC-B2

Router:
  Fa0/0: 192.168.1.1/24  (connects to Switch-A)
  Fa0/1: 192.168.2.1/24  (connects to Switch-B)
```

---

### Step-by-Step Lab

**Step 1: Add Devices**

Add to workspace:
- 1 × **Router 2911** (Routers → 2911)
- 2 × **Switch 2960**
- 4 × **PC-PT**

Connect: PC-A1 and PC-A2 to Switch-A. PC-B1 and PC-B2 to Switch-B. Switch-A to Router Fa0/0. Switch-B to Router Fa0/1.

**Step 2: Configure Router Interfaces**

Click Router → CLI tab.

```
enable
configure terminal

interface FastEthernet0/0
  ip address 192.168.1.1 255.255.255.0
  no shutdown
  exit

interface FastEthernet0/1
  ip address 192.168.2.1 255.255.255.0
  no shutdown
  exit

end
show ip interface brief
```

The output should show both interfaces as **up/up** with their configured IP addresses.

**Step 3: Configure PC IP Addresses**

On each PC, click → Desktop → IP Configuration → **Static** (to practice manual configuration):

| PC | IP Address | Subnet Mask | Default Gateway |
|----|-----------|-------------|----------------|
| PC-A1 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC-A2 | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| PC-B1 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |
| PC-B2 | 192.168.2.11 | 255.255.255.0 | 192.168.2.1 |

**Step 4: Test Connectivity**

From PC-A1 → Desktop → Command Prompt:

```
:: Same network (should work)
ping 192.168.1.11

:: Different network (should work via router)
ping 192.168.2.10
ping 192.168.2.11
ping 192.168.2.1   (router's Fa0/1)
ping 192.168.1.1   (router's Fa0/0)
```

**Step 5: View the Routing Table**

On the Router CLI:
```
show ip route
```

Output will look like:
```
C    192.168.1.0/24 is directly connected, FastEthernet0/0
C    192.168.2.0/24 is directly connected, FastEthernet0/1
```

`C` means "Connected" — the router knows about these networks because its interfaces are directly connected to them. It doesn't need any routing protocol here — it automatically knows both networks.

**Step 6: Understand What Happens During a Cross-Network Ping**

In Simulation mode, watch PC-A1 ping PC-B1. Follow the packet:

1. PC-A1 creates ICMP Echo Request: Src IP 192.168.1.10, Dst IP 192.168.2.10
2. PC-A1 checks: Is 192.168.2.10 in my network (192.168.1.0/24)? No. Send to default gateway.
3. PC-A1 needs the MAC of the gateway (192.168.1.1). Sends ARP Request (broadcast).
4. Router responds with ARP Reply — its MAC address.
5. PC-A1 sends the frame to the router's MAC, with the packet's destination IP as 192.168.2.10.
6. Router receives frame, strips Layer 2, sees IP destination 192.168.2.10. Looks up routing table: connected to Fa0/1.
7. Router sends new ARP for 192.168.2.10's MAC.
8. PC-B1 responds with its MAC.
9. Router forwards the packet in a new frame to PC-B1.

This is the fundamental routing process. Layer 2 changes at every hop. Layer 3 remains the same end-to-end.

---

### Lab 2 Verification

- [ ] Router shows both interfaces up/up with correct IPs
- [ ] PC-A1 can ping PC-A2 (same subnet)
- [ ] PC-A1 can ping PC-B1 (different subnet — goes through router)
- [ ] Show ip route shows two connected routes
- [ ] Simulation mode shows ARP then ICMP traffic flow

---

## Module 5: Cisco Packet Tracer — Lab 3: VLANs and Trunking

**Time:** 3 hours
**Tool:** Cisco Packet Tracer
**Topology:** Single switch with 3 VLANs + router-on-a-stick

---

### Objective

Configure VLANs on a switch to isolate three departments. Configure a trunk link to a router. Use Router-on-a-Stick to enable inter-VLAN routing.

---

### Network Design

```
VLANs:
  VLAN 10: Sales       192.168.10.0/24  default gateway: 192.168.10.1
  VLAN 20: HR          192.168.20.0/24  default gateway: 192.168.20.1
  VLAN 30: IT          192.168.30.0/24  default gateway: 192.168.30.1

Switch Port Assignments:
  Fa0/1 → PC-Sales1    VLAN 10
  Fa0/2 → PC-Sales2    VLAN 10
  Fa0/3 → PC-HR1       VLAN 20
  Fa0/4 → PC-IT1       VLAN 30
  Fa0/24 → Router      TRUNK (carries all VLANs)
```

---

### Step-by-Step Lab

**Step 1: Add and Connect Devices**

Add: 1 × Switch 2960, 1 × Router 2911, 4 × PC-PT.
Connect: PC-Sales1 to Fa0/1, PC-Sales2 to Fa0/2, PC-HR1 to Fa0/3, PC-IT1 to Fa0/4, Router to Fa0/24 (this will be the trunk).

**Step 2: Configure Switch — Create VLANs**

Click Switch → CLI:

```
enable
configure terminal

vlan 10
  name Sales
  exit

vlan 20
  name HR
  exit

vlan 30
  name IT
  exit

show vlan brief
```

The `show vlan brief` output should show VLANs 10, 20, and 30 with their names.

**Step 3: Assign Access Ports to VLANs**

```
interface FastEthernet0/1
  switchport mode access
  switchport access vlan 10
  exit

interface FastEthernet0/2
  switchport mode access
  switchport access vlan 10
  exit

interface FastEthernet0/3
  switchport mode access
  switchport access vlan 20
  exit

interface FastEthernet0/4
  switchport mode access
  switchport access vlan 30
  exit
```

**Step 4: Configure Trunk Port**

```
interface FastEthernet0/24
  switchport mode trunk
  switchport trunk encapsulation dot1q
  exit

show interfaces trunk
```

The trunk interface should show VLANs 10, 20, 30 as allowed.

**Step 5: Configure Router — Subinterfaces (Router-on-a-Stick)**

On the router, one physical interface carries traffic for all 3 VLANs using subinterfaces:

```
enable
configure terminal

interface FastEthernet0/0
  no shutdown
  exit

interface FastEthernet0/0.10
  encapsulation dot1Q 10
  ip address 192.168.10.1 255.255.255.0
  exit

interface FastEthernet0/0.20
  encapsulation dot1Q 20
  ip address 192.168.20.1 255.255.255.0
  exit

interface FastEthernet0/0.30
  encapsulation dot1Q 30
  ip address 192.168.30.1 255.255.255.0
  exit

show ip interface brief
```

**Step 6: Configure PC IP Addresses**

| PC | IP | Mask | Gateway |
|----|-----|------|---------|
| PC-Sales1 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC-Sales2 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| PC-HR1 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC-IT1 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |

**Step 7: Test VLAN Isolation and Inter-VLAN Routing**

From PC-Sales1:
```
:: Same VLAN — should work
ping 192.168.10.11

:: Different VLAN — should fail WITHOUT router, should work WITH router
ping 192.168.20.10
ping 192.168.30.10
```

All pings should succeed because the router is routing between VLANs. To prove VLAN isolation without the router, temporarily remove the router — then same-VLAN pings work but cross-VLAN pings fail.

---

### Lab 3 Verification

- [ ] `show vlan brief` shows VLANs 10, 20, 30
- [ ] `show interfaces trunk` shows Fa0/24 as trunk
- [ ] PC-Sales1 can ping PC-Sales2 (same VLAN)
- [ ] PC-Sales1 can ping PC-HR1 (inter-VLAN via router)
- [ ] PC-Sales1 can ping PC-IT1 (inter-VLAN via router)

---

## Module 6: Cisco Packet Tracer — Lab 4: Static and Dynamic Routing

**Time:** 3–4 hours
**Tool:** Cisco Packet Tracer
**Topology:** Three routers in a chain

---

### Topology

```
PC-A ── Router-1 ── Router-2 ── Router-3 ── PC-B

Network segments:
  192.168.1.0/24 : PC-A and Router-1 Fa0/0
  10.0.12.0/30   : Router-1 Fa0/1 ↔ Router-2 Fa0/0 (point-to-point)
  10.0.23.0/30   : Router-2 Fa0/1 ↔ Router-3 Fa0/0 (point-to-point)
  192.168.3.0/24 : Router-3 Fa0/1 and PC-B
```

**IP Address Plan:**
| Interface | IP Address |
|-----------|-----------|
| Router-1 Fa0/0 | 192.168.1.1/24 |
| Router-1 Fa0/1 | 10.0.12.1/30 |
| Router-2 Fa0/0 | 10.0.12.2/30 |
| Router-2 Fa0/1 | 10.0.23.1/30 |
| Router-3 Fa0/0 | 10.0.23.2/30 |
| Router-3 Fa0/1 | 192.168.3.1/24 |
| PC-A | 192.168.1.10/24 GW: 192.168.1.1 |
| PC-B | 192.168.3.10/24 GW: 192.168.3.1 |

---

### Part A: Static Routing

**Configure all router interfaces** (same as Lab 2 — set each interface IP and `no shutdown`).

**Configure static routes on each router:**

On **Router-1:**
```
ip route 10.0.23.0 255.255.255.252 10.0.12.2
ip route 192.168.3.0 255.255.255.0 10.0.12.2
show ip route
```

On **Router-2:**
```
ip route 192.168.1.0 255.255.255.0 10.0.12.1
ip route 192.168.3.0 255.255.255.0 10.0.23.2
show ip route
```

On **Router-3:**
```
ip route 10.0.12.0 255.255.255.252 10.0.23.1
ip route 192.168.1.0 255.255.255.0 10.0.23.1
show ip route
```

**Test:** PC-A pings PC-B. Should work through all three routers.

Use `tracert 192.168.3.10` from PC-A — you should see 3 hops (Router-1, Router-2, Router-3) before reaching PC-B.

---

### Part B: Replace Static Routes with OSPF

Remove all static routes and configure OSPF instead.

On **Router-1:**
```
no ip route 10.0.23.0 255.255.255.252 10.0.12.2
no ip route 192.168.3.0 255.255.255.0 10.0.12.2

router ospf 1
  network 192.168.1.0 0.0.0.255 area 0
  network 10.0.12.0 0.0.0.3 area 0
  exit
```

On **Router-2:**
```
router ospf 1
  network 10.0.12.0 0.0.0.3 area 0
  network 10.0.23.0 0.0.0.3 area 0
  exit
```

On **Router-3:**
```
router ospf 1
  network 10.0.23.0 0.0.0.3 area 0
  network 192.168.3.0 0.0.0.255 area 0
  exit
```

Wait 10–20 seconds for OSPF to converge, then:

```
show ip route
show ip ospf neighbor
```

`show ip route` should now show OSPF routes (marked with `O`) for all networks. `show ip ospf neighbor` shows the OSPF adjacencies — Router-1 and Router-2 should be neighbors, Router-2 and Router-3 should be neighbors.

**Test:** PC-A pings PC-B. Should still work — but now routes were learned automatically.

**Observe OSPF's Self-Healing:** In Packet Tracer, delete the link between Router-1 and Router-2. In a topology without redundant paths, routing will fail (there is no alternate path). Add a third router creating a redundant path and see OSPF re-route automatically.

---

### Lab 4 Verification

- [ ] Static routing: PC-A pings PC-B through 3 hops
- [ ] tracert shows all intermediate routers
- [ ] OSPF: `show ip route` shows `O` routes learned via OSPF
- [ ] `show ip ospf neighbor` shows neighbors in Full state

---

## Module 7: Wireshark Analysis — Reading Real Traffic

**Time:** 2–3 hours
**Tool:** Wireshark installed on your computer
**No Packet Tracer needed — use your real network**

---

### Setup

Download and install Wireshark from wireshark.org. Launch it. You will see a list of network interfaces. Select your active interface (usually "Wi-Fi" or "Ethernet"). Click the blue shark fin (Start Capture).

**Important:** You will capture ALL traffic on your interface. You may see traffic from many applications. That is normal and educational.

---

### Exercise 7.1 — Capture and Analyze a DNS Query

**Step 1:** Start Wireshark capture.

**Step 2:** Open a browser and navigate to a website you haven't visited recently (clear browser cache if needed, or use incognito mode).

**Step 3:** Stop the capture after the page loads.

**Step 4:** In the filter bar, type `dns` and press Enter. You will see only DNS traffic.

**Step 5:** Find a DNS query for the domain you visited. Click on it. In the packet details panel:

- **Frame:** Physical layer info — size, interface
- **Ethernet II:** Layer 2 — source and destination MAC addresses
- **Internet Protocol:** Layer 3 — source and destination IP addresses. Note the destination IP — it is your DNS server (8.8.8.8, 1.1.1.1, or your router's IP)
- **User Datagram Protocol:** Layer 4 — source port (random ephemeral port), destination port (53). This shows DNS uses UDP.
- **Domain Name System:** Application layer — the actual DNS query. Type A (IPv4 address request). The domain name being queried.

Click on the DNS response packet (response has "Standard query response" in info column). Expand the DNS layer — you can see the answer section with the resolved IP address.

**Questions to answer:**
1. What was the source port of the DNS query?
2. What was the destination port? (Should be 53)
3. What IP was the query sent to? (Your DNS server)
4. What IP address was in the DNS response for your domain?
5. What transport protocol was used — TCP or UDP?

---

### Exercise 7.2 — Capture and Analyze a TCP Handshake

**Step 1:** Start a new capture.

**Step 2:** Open a browser, navigate to an HTTP website (try `http://neverssl.com` — a website intentionally without HTTPS).

**Step 3:** Stop capture.

**Step 4:** Filter: `tcp and ip.addr == <IP of the website>` (replace with the actual IP from DNS lookup).

**Step 5:** Find the TCP three-way handshake. Look for three consecutive packets:
- `[SYN]` — your computer to the server
- `[SYN, ACK]` — server to your computer
- `[ACK]` — your computer to server

Click on the SYN packet. In the TCP layer, expand and find:
- Source port (your ephemeral port, e.g., 54321)
- Destination port (80 for HTTP)
- Sequence number (e.g., 0 — shown as relative)
- SYN flag = 1, ACK flag = 0

Click on SYN-ACK. Find:
- Acknowledgment number (your sequence number + 1)
- SYN flag = 1, ACK flag = 1

Click on ACK. Find:
- Acknowledgment number (server's sequence number + 1)

**Questions:**
1. What was your computer's source port for this TCP connection?
2. What was the destination port?
3. What is the server's IP address?
4. After the three-way handshake, what is the next packet? (Should be an HTTP GET request)

---

### Exercise 7.3 — View an HTTP GET Request

Continuing from the previous capture (HTTP site):

**Step 4:** Filter: `http`

**Step 5:** Find the HTTP GET request. Click on it. Expand the HTTP layer:
- Method: GET
- Request URI: the path requested (e.g., "/" for the homepage)
- HTTP Version: HTTP/1.1
- Host header: the domain name
- User-Agent: your browser identification

Find the HTTP 200 OK response. Expand:
- Status Code: 200
- Content-Type: text/html (or similar)
- Content-Length: size of the response body

Note: For HTTPS sites, you cannot read the HTTP content in Wireshark — it is encrypted. You can only see the TLS handshake. This is the whole point of HTTPS.

---

### Exercise 7.4 — Analyze ARP

**Step 1:** Start a new capture.

**Step 2:** In Command Prompt: `arp -d *` (Windows — clears ARP cache, requires admin) or `ip neigh flush all` (Linux).

**Step 3:** ping your default gateway.

**Step 4:** Stop capture. Filter: `arp`

You should see:
1. **ARP Request** (broadcast): "Who has 192.168.1.1? Tell 192.168.1.x"
   - Destination MAC: FF:FF:FF:FF:FF:FF (broadcast)
   - Opcode: Request (1)
   
2. **ARP Reply** (unicast): "192.168.1.1 is at AA:BB:CC:DD:EE:FF"
   - Destination MAC: your computer's MAC
   - Opcode: Reply (2)

This is how your computer learned the router's MAC address before it could send any traffic to it.

---

### Exercise 7.5 — Capture DHCP (DORA)

**Step 1:** Start capture.

**Step 2:** On Windows CMD (as admin): `ipconfig /release` then `ipconfig /renew`

**Step 3:** Stop capture. Filter: `dhcp` or `bootp`

Find the four DHCP messages:
1. **DHCP Discover:** Broadcast (255.255.255.255), source port 68, destination port 67
2. **DHCP Offer:** Server's proposed IP, lease time
3. **DHCP Request:** Client accepting the offer
4. **DHCP ACK:** Server confirming the lease

Click on the DHCP Offer and expand the DHCP layer. Find:
- Your Offered IP (yiaddr field)
- Server Identifier (which DHCP server)
- Option 51: Lease time
- Option 3: Default gateway
- Option 6: DNS servers

---

## Module 8: Comprehensive Network Design Challenge

**Time:** 4–5 hours
**Tools:** Cisco Packet Tracer + paper for design

---

### The Scenario

You are the network engineer for **Sunrise Institute of Technology**, a college in Bhopal with 4 departments. Design and implement their entire network from scratch.

**Requirements:**
- 4 departments, each in a separate VLAN
- All departments must communicate with each other
- Internet connectivity through a single router
- DHCP for all user devices
- Separate server network

**Department Details:**

| Department | VLAN | Required Hosts | Subnet |
|-----------|------|---------------|--------|
| Computer Science | VLAN 10 | 60 students | 192.168.10.0/26 |
| Electronics | VLAN 20 | 30 students | 192.168.20.0/27 |
| Administration | VLAN 30 | 14 staff | 192.168.30.0/28 |
| Servers | VLAN 40 | 6 servers | 192.168.40.0/29 |
| Router-Switch Link | VLAN 99 (native) | — | 192.168.99.0/30 |

---

### Design Phase (Do Before Opening Packet Tracer)

**Task D1:** Verify that each subnet has enough usable hosts:
- VLAN 10: /26 gives ___ usable hosts. Is 60 sufficient?
- VLAN 20: /27 gives ___ usable hosts. Is 30 sufficient?
- VLAN 30: /28 gives ___ usable hosts. Is 14 sufficient?
- VLAN 40: /29 gives ___ usable hosts. Is 6 sufficient?

**Task D2:** Assign router subinterface IPs (use the first host in each subnet as the gateway):
- VLAN 10 gateway: ___________
- VLAN 20 gateway: ___________
- VLAN 30 gateway: ___________
- VLAN 40 gateway: ___________

**Task D3:** Assign server static IPs (use the last 6 usable hosts in VLAN 40's subnet):
- Web Server: ___________
- DNS Server: ___________
- DHCP Server: ___________
- File Server: ___________

**Task D4:** Draw a network diagram showing all devices, connections, VLANs, and IP assignments before building in Packet Tracer.

---

### Build Phase

**Devices needed:**
- 1 × Router 2911
- 2 × Switch 2960 (one for access layer, one for distribution)
- 4 × Server-PT (Web, DNS, DHCP, File)
- 2 × PC per VLAN (8 PCs total)

**Build Sequence:**
1. Add and connect all devices per your diagram
2. Create VLANs on both switches
3. Configure access ports per VLAN assignment
4. Configure trunk link between switches
5. Configure trunk link from switch to router
6. Configure router subinterfaces
7. Configure DHCP server for each VLAN pool
8. Configure servers with static IPs
9. Configure PCs for DHCP
10. Test connectivity

---

### Testing Checklist

```
From CS-PC1 (VLAN 10):
  ping 192.168.10.1        (own gateway)      ✓/✗
  ping 192.168.20.10       (Electronics PC)   ✓/✗
  ping 192.168.30.10       (Admin PC)         ✓/✗
  ping 192.168.40.(websvr) (Web Server)       ✓/✗

From Admin-PC1 (VLAN 30):
  ping 192.168.10.10       (CS PC)            ✓/✗
  ping 192.168.40.(websvr) (Web Server)       ✓/✗

Show switch commands:
  show vlan brief                             (all VLANs visible)
  show interfaces trunk                       (trunk ports active)

Show router commands:
  show ip interface brief                     (all subinterfaces up)
  show ip route                               (all subnets in routing table)
```

---

### Final Challenge Question

After successfully completing the network, answer these questions based on what you built and observed:

1. When CS-PC1 sends a ping to Admin-PC1, at which device does Layer 2 change? Why?
2. If VLAN 10 had 70 students instead of 60, what change would you need to make? What impact would it have?
3. If the DHCP server goes down, what happens to PCs that already have leases? What happens to PCs trying to join the network?
4. A student connects an unauthorized switch to one of the access ports. What threat does this create? How could you prevent it (Google "port security Cisco")?
5. The college wants all internet traffic to pass through a content filter. Where in this topology would the content filter be placed?

---

## Quick Reference Card

### Essential IOS Commands (Cisco)

```
enable                          Enter privileged exec mode
configure terminal              Enter global config mode
show running-config             View current configuration
show ip interface brief         Status of all interfaces
show ip route                   Routing table
show mac address-table          Switch MAC table
show vlan brief                 VLAN list
show interfaces trunk           Trunk port status
show ip ospf neighbor           OSPF neighbors
show arp                        ARP table
ping X.X.X.X                   Ping from router
traceroute X.X.X.X             Trace from router
```

### Essential Windows/Linux Commands

```cmd
:: Windows
ipconfig /all                   Full network info
ipconfig /release               Release DHCP lease
ipconfig /renew                 Renew DHCP lease
ipconfig /flushdns              Clear DNS cache
ping -t X.X.X.X                Continuous ping
tracert X.X.X.X                Trace route
nslookup domain.com             DNS lookup
arp -a                          View ARP table
netstat -an                     Active connections
route print                     Windows routing table
```

```bash
# Linux/Mac
ip addr show                    Interface info
ip route show                   Routing table
ping -c 4 X.X.X.X              Ping 4 times
traceroute X.X.X.X             Trace route
dig domain.com                  DNS lookup
arp -n                          ARP table
ss -tuln                        Listening ports
nmap -sn 192.168.1.0/24        Discover hosts on subnet
```

---

## Subnetting Quick Reference

| You need X hosts | Use prefix | Usable hosts |
|-----------------|-----------|-------------|
| 2 hosts | /30 | 2 |
| 2–6 hosts | /29 | 6 |
| 7–14 hosts | /28 | 14 |
| 15–30 hosts | /27 | 30 |
| 31–62 hosts | /26 | 62 |
| 63–126 hosts | /25 | 126 |
| 127–254 hosts | /24 | 254 |
| 255–510 hosts | /23 | 510 |
| 511–1022 hosts | /22 | 1022 |
| 1023–2046 hosts | /21 | 2046 |

---

## Self-Assessment — Are You Ready?

After completing all 8 modules, test yourself:

1. Draw the OSI model from memory with layer numbers, names, protocols, data units, and devices at each layer.

2. You are given `172.16.0.0/16`. Subnet it into 32 equal subnets. What is the prefix length? What is the range of the 10th subnet?

3. A device has IP `192.168.5.200` and mask `255.255.255.192`. What is its network address? Broadcast? Can it communicate with `192.168.5.140` without a router?

4. Explain what happens — step by step, layer by layer — when you type `google.com` in a browser on a fresh boot. Include DNS, ARP, TCP handshake, HTTP, and routing.

5. What is the difference between a hub and a switch? Between a switch and a router?

6. A router has the following routing table: Direct connected 192.168.1.0/24, Static route 10.0.0.0/8 via 192.168.1.2, OSPF route 10.5.0.0/16 via 192.168.1.3. A packet arrives destined for 10.5.10.100. Which route does the router use and why?

7. What is the purpose of the TTL field in an IP header? What happens when it reaches 0?

8. Why does the TCP three-way handshake require 3 messages instead of 2?

9. In Wireshark, you see a DNS query going to 8.8.8.8 on UDP port 53. The response comes back. Then you see a SYN to the resolved IP on TCP port 443. Describe what application is doing what.

10. You configure VLAN 10 (Sales) and VLAN 20 (HR) on a switch. A Sales PC tries to ping an HR PC. The ping fails. What is the reason, and what additional configuration is needed to make it succeed?

---

*Document Version 1.0 | Created for GitHub Repository | February 2026*
*All Packet Tracer labs designed for Cisco Packet Tracer 8.x — download free at netacad.com*
*Wireshark exercises require a real computer with internet connectivity*
