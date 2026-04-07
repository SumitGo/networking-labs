# Real-World Network Performance Analysis (Latency & Packet Loss)

## 📌 Project Overview
This project analyzes the real-world impact of latency and packet loss on network performance. Instead of using artificial simulations, this analysis relies on live `ping` diagnostics across various global routing paths to observe how physical distance and network congestion affect data transmission.

## 🛠 Tools & Environment
* **Diagnostic Tool:** `ping` (ICMP Echo Requests)
* **Operating System:** Linux
* **Target Traffic:** Live Internet routing paths

## 🔍 Methodology & Analysis

### 1. Normal Network Behavior (Baseline)
To establish a baseline, traffic was routed to a highly optimized, geographically close CDN edge server.
* **Target:** `google.com`
* **Observation:** Low latency (~44ms average RTT), stable connection, and 0% packet loss.

```text
$ ping google.com
PING google.com (2404:6800:4002:804::200e) 56 data bytes
64 bytes from del03s09-in-x0e.1e100.net ... time=14.6 ms
64 bytes from del03s09-in-x0e.1e100.net ... time=59.7 ms
64 bytes from del03s09-in-x0e.1e100.net ... time=33.9 ms
64 bytes from del03s09-in-x0e.1e100.net ... time=55.9 ms
64 bytes from del03s09-in-x0e.1e100.net ... time=49.0 ms
64 bytes from del03s09-in-x0e.1e100.net ... time=52.7 ms

--- google.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5007ms
rtt min/avg/max/mdev = 14.575/44.315/59.735/15.587 ms
```
### 2.High Latency & Jitter Analysis
To observe natural network delay, traffic was routed to a server requiring extensive hops and global routing.
* **Target:** `guide.handmadehero.org`
* **Observation:** High base latency (~402ms average) with significant jitter (varying from 304ms to 536ms). This demonstrates the delay introduced by long-distance fiber runs and multiple router hops.

```
$ ping guide.handmadehero.org
PING guide.handmadehero.org (64:ff9b::8a44:177a) 56 data bytes
64 bytes from 64:ff9b::8a44:177a ... time=488 ms
64 bytes from 64:ff9b::8a44:177a ... time=395 ms
64 bytes from 64:ff9b::8a44:177a ... time=327 ms
...
--- guide.handmadehero.org ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9324ms
rtt min/avg/max/mdev = 304.730/402.474/536.280/74.159 ms
```

### 3.Packet Loss Analysis
To observe packet drops, traffic was routed to a corporate server path experiencing ICMP filtering or transient route congestion.
* **Target:** `cisco.com`
* **Observation:** Noticeable packet drops (11.1% loss) resulting in missing sequence numbers (`icmp_seq`). The connection demonstrates instability that would trigger TCP retransmissions.

```
$ ping cisco.com
PING cisco.com (2001:420:1101:1::185) 56 data bytes
64 bytes from 2001:420:1101:1::185: icmp_seq=1 ... time=405 ms
64 bytes from 2001:420:1101:1::185: icmp_seq=2 ... time=409 ms
64 bytes from 2001:420:1101:1::185: icmp_seq=3 ... time=435 ms
...
--- cisco.com ping statistics ---
9 packets transmitted, 8 received, 11.1111% packet loss, time 8435ms
rtt min/avg/max/mdev = 318.667/430.175/549.615/73.125 ms
```

## Key Learnings & Real-World Impact
* **Latency vs. Jitter:** While high latency creates a consistent delay, the fluctuation in response times (jitter) is often more disruptive to real-time protocols.
* **TCP Retransmissions:** A packet loss rate of even 11% forces TCP to constantly retransmit lost segments, drastically reducing overall bandwidth and throughput.
* **Impact on Streaming Services:** High Latency creates noticeable delays in live broadcasting (e.g., VoIP, live sports, satellite feeds).
* **Packet Loss** directly causes buffering, artifacts, and frame drops in media systems relying on UDP, making protocols like HLS or tools like FFmpeg vital for adaptive bitrate streaming.
