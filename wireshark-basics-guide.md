# Wireshark Basics & Traffic Investigation Guide

> A practical beginner-friendly guide to **Wireshark**, covering packet capture, interfaces, the toolbar, recent files, capture filters, display filters, packet dissection, navigation, packet coloring, and basic traffic investigation.

![Wireshark](https://www.wireshark.org/assets/images/wireshark-logo.png)

---

## 1. What Is Wireshark?

**Wireshark** is a network protocol analyzer. It can capture network traffic from supported interfaces and decode the packets so you can inspect what is happening at the network, transport, and application layers.

In simple terms:

```text
Network traffic
      |
      v
+----------------+
| Network        |
| Interface      |
+----------------+
      |
      v
+----------------+
| Packet Capture |
+----------------+
      |
      v
+----------------+
| Wireshark      |
| Protocol       |
| Dissection     |
+----------------+
      |
      v
+----------------+
| Investigation  |
| Filtering      |
| Statistics     |
| Streams        |
+----------------+
```

Wireshark can be used for:

- Network troubleshooting
- Protocol learning
- Incident investigation
- Security monitoring
- Malware/network behavior analysis
- DNS, HTTP, TLS, TCP and UDP troubleshooting
- Investigating suspicious connections
- Understanding how applications communicate
- Reviewing previously captured `.pcap` and `.pcapng` files

**Important:** Wireshark is an analyzer, not automatically an IDS or firewall. It shows you network evidence; the analyst interprets that evidence.

Official documentation:  
https://www.wireshark.org/docs/wsug_html/

---

# 2. How Wireshark Operates

A useful mental model is:

```text
                 LIVE TRAFFIC
                      |
                      v
              +---------------+
              | Network NIC   |
              | / Interface   |
              +---------------+
                      |
                      v
              +---------------+
              | Packet Capture|
              +---------------+
                      |
                Capture Filter
                      |
                      v
              +---------------+
              | Capture File  |
              | PCAP / PCAPNG |
              +---------------+
                      |
                Display Filter
                      |
                      v
              +---------------+
              | Packet List   |
              +---------------+
                      |
                      v
              +---------------+
              | Dissection    |
              | Details       |
              +---------------+
                      |
                      v
              +---------------+
              | Investigation |
              +---------------+
```

There are two important filtering stages:

### Capture filter

A **capture filter** controls what packets are captured in the first place.

Example:

```text
tcp port 443
```

This tells the capture process to capture traffic matching that condition.

### Display filter

A **display filter** controls which already-captured packets are shown.

Example:

```text
tcp.port == 443
```

The original packets remain in the capture file; the display filter only changes what you see.

This distinction is extremely important when investigating traffic.

Official reference:  
https://www.wireshark.org/docs/wsug_html/#ChWorkBuildDisplayFilter

---

# 3. Wireshark Welcome Screen

When Wireshark starts without an active capture or opened capture file, the Welcome Screen normally provides two important areas:

1. **Recent capture files**
2. **Available capture interfaces**

The interface list also shows activity indicators for interfaces that are currently generating traffic.

![Wireshark Capture Interfaces](https://gitlab.com/wireshark/wireshark/-/raw/wireshark-4.2.3/docbook/wsug_src/images/ws-capture-interfaces-main-win32.png)

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChCapInterfaceSection

---

# 4. Recent Files

The **Recent Files** area makes it easy to reopen packet captures that you have previously worked with.

Typical workflow:

```text
Wireshark
   |
   +--> Recent Capture
           |
           +--> suspicious-traffic.pcapng
           |
           +--> dns-investigation.pcapng
           |
           +--> web-traffic.pcap
```

This is particularly useful during investigations because analysts often revisit the same capture multiple times.

### PCAP vs PCAPNG

Two common capture formats are:

| Format | Description |
|---|---|
| `.pcap` | Traditional packet capture format |
| `.pcapng` | Newer and more flexible capture format |

PCAPNG can store additional information such as interface information and capture-file comments.

---

# 5. Capture Interfaces

A **capture interface** is the network interface from which Wireshark obtains packets.

Examples:

- Ethernet
- Wi-Fi
- VPN interfaces
- Loopback
- USB interfaces
- Bluetooth-related interfaces
- Virtual machine interfaces

On a Windows machine you may see interfaces such as:

```text
Ethernet
Wi-Fi
Npcap Loopback Adapter
VirtualBox Host-Only Network
VMware Network Adapter
```

The correct interface depends on where the traffic you want to investigate is flowing.

### How to identify the correct interface

A simple method:

1. Open Wireshark.
2. Look at the activity graph beside each interface.
3. Generate some traffic.
4. Watch which interface's activity increases.
5. Select that interface.
6. Start the capture.

For example:

```text
Wi-Fi       ▁▂▃▅▇▆▃▂▁
Ethernet    ▁▁▁▁▁▁▁▁▁
Loopback    ▁▁▁▁▁▁▁▁▁
```

The Wi-Fi interface would be the likely candidate if the computer is communicating through Wi-Fi.

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChCapInterfaceSection

---

# 6. The Main Wireshark Window

The main interface can be thought of as several areas:

```text
+-----------------------------------------------------------+
| MENU                                                      |
+-----------------------------------------------------------+
| MAIN TOOLBAR                                              |
+-----------------------------------------------------------+
| DISPLAY FILTER                                            |
+-----------------------------------------------------------+
| PACKET LIST                                               |
|                                                           |
| No. Time Source Destination Protocol Length Info           |
| 1   ...  10.0.0.1  10.0.0.5    TCP      ...   ...        |
| 2   ...  10.0.0.5  10.0.0.1    TCP      ...   ...        |
+-----------------------------------------------------------+
| PACKET DETAILS                                            |
|                                                           |
| > Frame                                                    |
| > Ethernet II                                             |
| > Internet Protocol                                       |
| > Transmission Control Protocol                            |
+-----------------------------------------------------------+
| PACKET BYTES                                              |
|                                                           |
| 0000  45 00 00 34 ...                  E..4               |
+-----------------------------------------------------------+
| STATUS BAR                                                |
+-----------------------------------------------------------+
```

![Wireshark Main Window](https://gitlab.com/wireshark/wireshark/-/raw/wireshark-4.2.3/docbook/wsug_src/images/ws-main.png)

The main window contains the menu, main toolbar, filter toolbar, packet list, packet details, packet bytes/diagram areas, and status bar.

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChUseMainWindowSection

---

# 7. Main Toolbar

The main toolbar provides quick access to common Wireshark functions.

Common actions include:

- Start capture
- Stop capture
- Restart capture
- Open a capture file
- Save a capture
- Close a capture
- Reload a capture
- Navigation controls
- Search/find functions

The exact toolbar appearance can vary by Wireshark version and operating system.

![Wireshark Main Toolbar](https://gitlab.com/wireshark/wireshark/-/raw/wireshark-4.2.3/docbook/wsug_src/images/ws-main-toolbar.png)

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChUseMainToolbar

---

# 8. Filter Toolbar / Display Filter

The **Display Filter toolbar** is one of the most important parts of Wireshark.

It allows you to tell Wireshark:

> "Show me only packets matching this condition."

![Wireshark Filter Toolbar](https://gitlab.com/wireshark/wireshark/-/raw/wireshark-4.2.3/docbook/wsug_src/images/ws-filter-toolbar.png)

The toolbar provides:

- Saved filter/bookmark controls
- Filter input
- Syntax checking
- Apply
- Clear
- Recently used filters
- Filter buttons

Wireshark validates filter syntax while you type. Invalid expressions are indicated by the filter field.

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChUseFilterToolbarSection

---

# 9. Display Filter Basics

Display filters are used **after packets have been captured or loaded**.

### Protocol filters

Show TCP:

```text
tcp
```

Show UDP:

```text
udp
```

Show DNS:

```text
dns
```

Show HTTP:

```text
http
```

Show TLS:

```text
tls
```

Show ICMP:

```text
icmp
```

---

## 10. Filtering by IP Address

Show packets involving an IP address:

```text
ip.addr == 192.168.1.10
```

Only source:

```text
ip.src == 192.168.1.10
```

Only destination:

```text
ip.dst == 192.168.1.10
```

Example:

```text
ip.addr == 10.0.0.25
```

This is useful when investigating a specific host.

---

# 11. Filtering by Port

HTTPS:

```text
tcp.port == 443
```

SSH:

```text
tcp.port == 22
```

DNS over UDP:

```text
udp.port == 53
```

A specific destination port:

```text
tcp.dstport == 443
```

A specific source port:

```text
tcp.srcport == 443
```

---

# 12. Combining Filters

Use logical operators.

### AND

```text
ip.addr == 192.168.1.10 and tcp
```

### OR

```text
dns or http
```

### NOT

```text
tcp and not tcp.port == 443
```

### Example investigation filter

```text
ip.addr == 192.168.1.10 and tcp.port == 443
```

This focuses on TCP/HTTPS-related traffic involving the host.

Official display-filter syntax:  
https://www.wireshark.org/docs/man-pages/wireshark-filter.html

---

# 13. Capture Filters

Capture filters are different from display filters.

Capture filters are applied **while capturing**.

Example:

```text
host 192.168.1.10
```

Only capture traffic involving the host.

Example:

```text
port 53
```

Capture traffic using port 53.

Example:

```text
tcp port 443
```

Capture TCP traffic on port 443.

Example:

```text
src host 192.168.1.10
```

Capture traffic originating from the specified host.

### Capture filter vs display filter

| Feature | Capture Filter | Display Filter |
|---|---|---|
| When applied | During capture | After capture |
| Main purpose | Reduce captured traffic | Narrow what you see |
| Syntax | BPF/libpcap style | Wireshark display-filter language |
| Can hide packets? | Packets are not captured | Packets remain in capture |
| Example | `port 443` | `tcp.port == 443` |

**Rule to remember:**

> Capture filters decide what enters the capture. Display filters decide what you see.

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChCapCaptureFilter

---

# 14. Starting a Traffic Capture

A basic live-capture workflow:

```text
1. Open Wireshark
       |
2. Identify active interface
       |
3. Select interface
       |
4. Optional: configure capture filter
       |
5. Start capture
       |
6. Generate network traffic
       |
7. Stop capture
       |
8. Save PCAPNG
       |
9. Investigate packets
```

Example traffic you can generate in a lab:

```bash
ping 8.8.8.8
```

or:

```bash
nslookup example.com
```

or browse to a website.

> Only capture traffic on networks and systems you own or are explicitly authorized to monitor.

---

# 15. Traffic Sniffing

"Sniffing" means observing network packets as they pass through an interface.

For a simple lab:

```text
Your Computer
      |
      | DNS request
      v
 DNS Server
      |
      | DNS response
      v
Your Computer
```

Wireshark can show the individual packets making up this exchange.

A typical investigation might look like:

```text
DNS Query
   ↓
DNS Response
   ↓
TCP Connection
   ↓
TLS Handshake
   ↓
Encrypted Application Traffic
```

The exact sequence depends on the application and protocol.

---

# 16. Packet List Pane

The packet list is the first major area used during analysis.

Typical columns include:

| Column | Meaning |
|---|---|
| No. | Packet/frame number |
| Time | Timestamp |
| Source | Source address |
| Destination | Destination address |
| Protocol | Highest-level protocol Wireshark identifies |
| Length | Packet/frame length |
| Info | Short description |

Example:

```text
No.   Time       Source          Destination     Protocol   Info
1     0.000000   192.168.1.10    192.168.1.1     DNS        Standard query
2     0.025321   192.168.1.1     192.168.1.10    DNS        Standard query response
3     0.031111   192.168.1.10    93.184.216.34   TCP        SYN
4     0.041111   93.184.216.34   192.168.1.10    TCP        SYN, ACK
```

Select a packet to populate the packet-details and packet-bytes panes.

---

# 17. Packet Dissection

Packet dissection is one of Wireshark's most important functions.

Wireshark takes raw packet bytes and uses protocol dissectors to interpret them.

For example:

```text
Ethernet
   |
   +-- IPv4
        |
        +-- TCP
             |
             +-- TLS
```

You can expand each layer.

Example:

```text
Frame
 └── Ethernet II
      └── Internet Protocol Version 4
           └── Transmission Control Protocol
                └── Transport Layer Security
```

Each layer exposes fields that can be investigated.

---

# 18. Understanding Encapsulation

A packet can contain multiple protocol layers.

For example:

```text
+-----------------------------------+
| Ethernet Header                   |
+-----------------------------------+
| IPv4 Header                       |
+-----------------------------------+
| TCP Header                        |
+-----------------------------------+
| TLS Data                          |
+-----------------------------------+
```

The protocol stack can therefore be thought of as:

```text
Application
     ↓
Transport
     ↓
Network
     ↓
Data Link
     ↓
Physical
```

Wireshark displays these layers in the Packet Details pane.

---

# 19. Packet Details Pane

When you click a packet, the Packet Details pane exposes the protocol fields.

Example:

```text
> Frame
> Ethernet II
> Internet Protocol Version 4
> Transmission Control Protocol
> Application Protocol
```

Click the arrow next to a protocol to expand it.

For example:

```text
Internet Protocol Version 4

    Version: 4
    Header Length: 20 bytes
    Total Length: 60
    Identification: ...
    Flags: ...
    Time to Live: 64
    Protocol: TCP
    Source Address: 192.168.1.10
    Destination Address: 93.184.216.34
```

This is where packet dissection becomes useful for investigations.

---

# 20. Packet Bytes Pane

The Packet Bytes pane displays the raw bytes of the selected packet.

Example:

```text
0000  00 11 22 33 44 55 66 77  88 99 aa bb 08 00 45 00
0010  00 34 12 34 40 00 40 06  aa bb c0 a8 01 0a 5d b8
0020  d8 22 c0 01 bb 00 00 00  01 00 00 00 00 50 02 ...
```

It typically provides:

- Byte offset
- Hexadecimal bytes
- ASCII representation

Selecting a field in Packet Details highlights the corresponding bytes.

This lets you connect:

```text
Protocol Field
      ↓
Raw Bytes
```

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChUsePacketBytesPane

---

# 21. Packet Navigation

You will frequently need to move through a capture.

Useful techniques include:

### Select a packet

Click it in the Packet List.

### Move to the next packet

Use the keyboard navigation controls or arrow keys depending on your platform/version.

### Jump to packet number

Use:

```text
Go → Go to Packet
```

Then enter a packet number.

Example:

```text
Packet 1500
```

### Search

Wireshark provides packet searching capabilities that can be used to locate strings, packet data, and other information.

### Follow a conversation

Right-click a packet and use the appropriate:

```text
Follow → TCP Stream
```

or another available protocol stream.

---

# 22. Following a TCP Stream

A TCP stream lets you examine the conversation associated with a TCP connection.

Typical workflow:

```text
Select TCP packet
      |
      v
Right-click
      |
      v
Follow
      |
      v
TCP Stream
```

This can help answer:

- Which client connected to which server?
- What data was exchanged?
- Was there an HTTP request?
- What sequence of application messages occurred?
- Which packets belong to the same TCP conversation?

For encrypted protocols such as modern HTTPS, the application content may remain encrypted unless appropriate decryption material is available.

---

# 23. Packet Coloring

Wireshark uses packet coloring to make traffic easier to recognize visually.

For example, packets may be colored according to protocol or a filter rule.

![Wireshark Coloring Rules](https://gitlab.com/wireshark/wireshark/-/raw/wireshark-4.2.3/docbook/wsug_src/images/ws-coloring-rules-dialog.png)

You can configure coloring rules under:

```text
View → Coloring Rules
```

A rule can be based on a display filter.

Example:

```text
tcp.flags.syn == 1
```

You could use this to make TCP SYN packets visually distinctive.

Another example:

```text
dns
```

could be used to highlight DNS traffic.

### Why coloring helps

During a large investigation:

```text
Thousands of packets
        ↓
Coloring Rules
        ↓
Visual patterns
        ↓
Faster identification
```

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChCustColorizationSection

---

# 24. Coloring Conversations

Wireshark can also temporarily color traffic associated with a conversation.

This can be useful when investigating:

```text
Client A
   ↕
Server B
```

Instead of manually identifying every packet belonging to that conversation, Wireshark can highlight the related traffic.

This is particularly useful when a capture contains many simultaneous connections.

---

# 25. A Simple DNS Investigation

Let's walk through a basic investigation.

### Step 1 — Capture traffic

Start a capture on the active interface.

### Step 2 — Generate DNS traffic

For example:

```bash
nslookup example.com
```

### Step 3 — Stop capture

Stop the capture.

### Step 4 — Apply a display filter

```text
dns
```

### Step 5 — Find the query

Look for:

```text
Standard query
```

### Step 6 — Inspect the packet

Expand:

```text
Frame
Ethernet
IP
UDP
DNS
```

### Step 7 — Investigate

Look at:

- Source IP
- Destination IP
- DNS server
- Query name
- Query type
- Response
- Response code
- Returned addresses

---

# 26. A Simple TCP Investigation

Start with:

```text
tcp
```

Then identify a connection.

Look for:

```text
SYN
SYN, ACK
ACK
```

This represents the basic TCP three-way handshake:

```text
Client                         Server
  |                              |
  | -------- SYN --------------> |
  |                              |
  | <------ SYN, ACK ----------- |
  |                              |
  | -------- ACK --------------> |
  |                              |
```

You can then inspect:

- TCP ports
- Sequence numbers
- Acknowledgement numbers
- TCP flags
- Window size
- Retransmissions
- Reset packets
- TCP stream

---

# 27. Useful Investigation Filters

## Find TCP SYN packets

```text
tcp.flags.syn == 1
```

## Find TCP resets

```text
tcp.flags.reset == 1
```

## Find retransmissions

```text
tcp.analysis.retransmission
```

## Find DNS traffic

```text
dns
```

## Find HTTP traffic

```text
http
```

## Find TLS traffic

```text
tls
```

## Find traffic to a specific host

```text
ip.addr == 192.168.1.10
```

## Find traffic between two hosts

```text
ip.addr == 192.168.1.10 and ip.addr == 192.168.1.20
```

## Find a specific TCP port

```text
tcp.port == 443
```

---

# 28. Reading the Status Bar

The status bar is located at the bottom of the Wireshark window.

It provides information about:

- Current packet/capture context
- Capture statistics
- Displayed packet count
- Configuration profile
- Expert information indicators
- Selected protocol fields
- Filter-related information

The middle area can provide information about the current capture file, while the right side identifies the active configuration profile.

![Wireshark Status Bar](https://gitlab.com/wireshark/wireshark/-/raw/wireshark-4.2.3/docbook/wsug_src/images/ws-statusbar.png)

Official documentation:  
https://www.wireshark.org/docs/wsug_html/#ChUseStatusbar

---

# 29. Capture Statistics

A capture should not be investigated only by looking at individual packets.

You can also use Wireshark's statistics features.

Useful areas include:

```text
Statistics
   |
   +-- Protocol Hierarchy
   |
   +-- Conversations
   |
   +-- Endpoints
   |
   +-- I/O Graphs
   |
   +-- Flow Graph
```

These can help you move from:

```text
Individual packets
```

to:

```text
Traffic patterns
```

---

# 30. A Practical SOC Investigation Workflow

A useful beginner workflow is:

```text
                 START
                   |
                   v
          Identify the capture
                   |
                   v
          Check capture scope
                   |
                   v
       Review Protocol Hierarchy
                   |
                   v
        Identify interesting hosts
                   |
                   v
          Filter the traffic
                   |
                   v
        Inspect packet details
                   |
                   v
        Follow conversations
                   |
                   v
        Check TCP/DNS/TLS behavior
                   |
                   v
       Look for anomalies/errors
                   |
                   v
       Build a timeline of events
                   |
                   v
              Document
```

---

# 31. What Should You Look For?

When investigating a capture, ask structured questions.

### Who?

```text
Who is communicating?
```

Look at:

- Source IP
- Destination IP
- MAC address
- Hostnames

### What?

```text
What protocol is being used?
```

Look for:

- DNS
- HTTP
- TLS
- TCP
- UDP
- ICMP
- SMB
- SSH
- DHCP

### When?

```text
When did the communication occur?
```

Use timestamps.

### Where?

```text
Which server or endpoint is involved?
```

Look at:

- IP addresses
- Ports
- DNS names
- SNI where available

### How?

```text
How is the communication behaving?
```

Look for:

- Retransmissions
- Resets
- Unusual ports
- Repeated connections
- Failed DNS requests
- Large transfers
- Unexpected protocols

---

# 32. Example Security Investigation

Imagine you receive:

```text
suspicious-host.pcapng
```

Start with:

```text
ip
```

Then inspect:

```text
Statistics → Endpoints
```

Identify interesting hosts.

Next:

```text
Statistics → Conversations
```

Look for significant communications.

Then apply:

```text
ip.addr == <interesting-ip>
```

Next investigate DNS:

```text
dns
```

Then TCP:

```text
tcp
```

Then suspicious ports:

```text
tcp.port == 4444
```

Then inspect individual packets and follow relevant streams.

This produces a progressively narrower investigation:

```text
Full PCAP
   ↓
Endpoints
   ↓
Interesting Host
   ↓
Protocol
   ↓
Conversation
   ↓
Packet
   ↓
Field
   ↓
Evidence
```

---

# 33. Sample PCAP Files

Wireshark provides an official collection of sample capture files.

These are excellent for practicing without having to generate your own traffic.

Official Sample Captures:

https://wiki.wireshark.org/SampleCaptures

Examples include captures covering:

- DNS
- DHCP
- HTTP
- TCP
- SSH
- SFTP
- Wireless LAN
- TLS
- Protobuf
- gRPC
- NetBIOS
- Many other protocols

A good learning method is:

```text
Download sample PCAP
        ↓
Open in Wireshark
        ↓
Identify protocols
        ↓
Apply display filters
        ↓
Select packets
        ↓
Expand protocol layers
        ↓
Follow conversations
        ↓
Document findings
```

---

# 34. Suggested Beginner Lab

Create a small home lab.

### Lab 1 — DNS

Generate:

```bash
nslookup example.com
```

Investigate:

```text
dns
```

Find:

- DNS server
- Query name
- Query type
- Response
- Returned IP

### Lab 2 — ICMP

Run:

```bash
ping 8.8.8.8
```

Filter:

```text
icmp
```

Investigate:

- Echo request
- Echo reply
- Source
- Destination
- TTL

### Lab 3 — TCP

Open a TCP-based connection and filter:

```text
tcp
```

Look for:

```text
SYN
SYN, ACK
ACK
```

### Lab 4 — TLS

Filter:

```text
tls
```

Investigate:

- Client Hello
- Server Hello
- TLS version
- Extensions
- Server Name Indication where available
- Certificate-related information where visible

---

# 35. Wireshark Investigation Cheat Sheet

| Goal | Filter / Action |
|---|---|
| Show TCP | `tcp` |
| Show UDP | `udp` |
| Show DNS | `dns` |
| Show HTTP | `http` |
| Show TLS | `tls` |
| Show ICMP | `icmp` |
| Specific IP | `ip.addr == 192.168.1.10` |
| Source IP | `ip.src == 192.168.1.10` |
| Destination IP | `ip.dst == 192.168.1.10` |
| TCP port | `tcp.port == 443` |
| UDP port | `udp.port == 53` |
| TCP SYN | `tcp.flags.syn == 1` |
| TCP RST | `tcp.flags.reset == 1` |
| Retransmissions | `tcp.analysis.retransmission` |
| Follow conversation | Right-click → Follow |
| Coloring | View → Coloring Rules |
| Statistics | Statistics menu |
| Capture filter | Capture options/interface |
| Display filter | Filter toolbar |

---

# 36. Capture Filter Examples

```text
host 192.168.1.10
```

```text
src host 192.168.1.10
```

```text
dst host 192.168.1.10
```

```text
port 53
```

```text
tcp port 443
```

```text
udp port 53
```

```text
net 192.168.1.0/24
```

Capture filters use BPF/libpcap-style syntax. Always verify syntax in your installed Wireshark version before relying on a complex expression.

---

# 37. Display Filter Examples

```text
ip
```

```text
tcp
```

```text
udp
```

```text
dns
```

```text
ip.addr == 10.0.0.5
```

```text
tcp.port == 443
```

```text
dns.qry.name == "example.com"
```

```text
tcp.flags.syn == 1
```

```text
tcp.analysis.retransmission
```

```text
http.request
```

```text
tls.handshake
```

---

# 38. Common Beginner Mistakes

### Mistake 1 — Confusing capture and display filters

Remember:

```text
Capture filter = what gets captured
Display filter = what gets displayed
```

### Mistake 2 — Capturing on the wrong interface

Always verify interface activity before starting.

### Mistake 3 — Assuming encrypted traffic is useless

Even when payloads are encrypted, metadata such as:

- IP addresses
- Ports
- Timing
- Packet sizes
- TLS handshake information
- Connection patterns

can still provide useful investigative evidence.

### Mistake 4 — Looking at packets individually without context

Use:

```text
Endpoints
Conversations
Statistics
Streams
```

to understand the bigger picture.

### Mistake 5 — Treating every unusual packet as malicious

An unusual packet is an observation, not automatically proof of malicious activity.

Investigate context before drawing conclusions.

---

# 39. Recommended Investigation Mindset

When using Wireshark, avoid simply asking:

> "What does this packet mean?"

Instead ask:

```text
What happened?
Who communicated?
When did it happen?
Which protocol was used?
What sequence of events occurred?
Is the behavior expected?
What evidence supports the conclusion?
```

This mindset is particularly useful when using Wireshark as part of SOC or incident-response work.

---

# 40. Final Mental Model

If you remember only one workflow, remember this:

```text
                 WIRESHARK
                     |
          +----------+----------+
          |                     |
       Capture               Open PCAP
          |                     |
          +----------+----------+
                     |
                     v
              PACKET LIST
                     |
                     v
              DISPLAY FILTER
                     |
                     v
              SELECT PACKET
                     |
                     v
             PACKET DISSECTION
                     |
          +----------+----------+
          |                     |
     Packet Details        Packet Bytes
          |
          v
      Conversation
          |
          v
      Statistics
          |
          v
     Investigation
          |
          v
        Evidence
```

The key skills to develop are:

1. Identify the correct interface.
2. Understand capture vs display filters.
3. Read the packet list.
4. Understand protocol encapsulation.
5. Dissect packets layer by layer.
6. Navigate efficiently through captures.
7. Follow conversations.
8. Use coloring to highlight patterns.
9. Use statistics to understand the bigger picture.
10. Build conclusions from multiple pieces of network evidence.

---

# 41. Official Resources

### Wireshark

https://www.wireshark.org/

### Wireshark User's Guide

https://www.wireshark.org/docs/wsug_html/

### Display Filter Reference

https://www.wireshark.org/docs/dfref/

### Display Filter Manual

https://www.wireshark.org/docs/man-pages/wireshark-filter.html

### Sample Captures

https://wiki.wireshark.org/SampleCaptures

### Wireshark Wiki

https://wiki.wireshark.org/

---

## Author Notes

This repository is intended as a **beginner-to-intermediate Wireshark learning resource**, with an emphasis on practical packet analysis and SOC-style investigation.

Recommended progression:

```text
Wireshark Basics
      ↓
Capture Interfaces
      ↓
Capture Filters
      ↓
Display Filters
      ↓
Packet Dissection
      ↓
TCP / UDP / DNS / HTTP / TLS
      ↓
Streams & Conversations
      ↓
Statistics
      ↓
Traffic Investigation
      ↓
SOC / Incident Response
```

> **Lab safety:** Only capture and analyze traffic that you are authorized to monitor. Network captures can contain credentials, cookies, personal information, internal IP addresses, and other sensitive data. Treat PCAP files as potentially sensitive evidence.

---

**Confidence:** High — this guide is based primarily on the official Wireshark User's Guide and Wireshark's official sample-capture documentation.
