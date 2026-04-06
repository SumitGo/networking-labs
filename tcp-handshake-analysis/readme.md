# TCP 3-Way Handshake Analysis using Wireshark

## Objective
To capture and analyze the TCP 3-way handshake process using Wireshark and understand how a connection is established between a client and server.

## Tools Used
- Wireshark
- Web Browser
- Linux System

## Steps Performed
1. Started packet capture in Wireshark
2. Opened a browser and visited example.com
3. Stopped capture after a few seconds
4. Applied filter: tcp
5. Identified TCP handshake packets

## Packet Analysis

### 1. SYN (Client → Server)
* **Action:** The client initiates the connection to the web server.
* **Ports:** The client generates a randomized ephemeral port (e.g., `37328`) as the Source Port, targeting Destination Port `443` (HTTPS) on the server.
* **Flags:** The `SYN` (Synchronize) bit is set to `1`.
* **Mechanics:** The client sets its Initial Sequence Number (Relative Seq = `0`) to begin tracking the data flow. 

![SYN Packet](screenshot/SYN.png)

---

### 2. SYN-ACK (Server → Client)
* **Action:** The server acknowledges the client's request and sends its own synchronization parameter.
* **Ports:** The Source Port is now `443`, and the Destination Port is the client's ephemeral port (`37328`).
* **Flags:** Both the `SYN` and `ACK` (Acknowledgment) bits are set to `1`.
* **Mechanics:** The server sets its own Initial Sequence Number (Relative Seq = `0`). Crucially, it sets the Acknowledgment Number to `1` (which is the client's Initial Sequence Number + `1`), confirming receipt of the client's SYN packet.

![SYN-ACK Packet](screenshot/SYN_ACK.png)

---

### 3. ACK (Client → Server)
- * **Action:** The client confirms receipt of the server's synchronization. The TCP socket is now in an ESTABLISHED state.
* **Flags:** Only the `ACK` bit is set to `1`.
* **Mechanics:** The client increments its Sequence Number to `1`. It sets its Acknowledgment Number to `1` (the server's Initial Sequence Number + `1`). Data transmission (like a TLS handshake or HTTP GET request) can now safely begin.

![ACK Packet](screenshot/ACK.png)

## Key Learnings & Real-World Application

* **Stateful Connections:** TCP uses this mandatory 3-step handshake to ensure both the client and server are ready to transmit and receive data reliably.
* **Port Allocation:** Practically observed how operating systems assign random high-numbered ports to clients while connecting to standardized well-known server ports (like 443).
* **Troubleshooting Utility:** Understanding this exact packet flow is critical for frontline technical support and network operations. By analyzing this handshake in Wireshark:
    * If a client sends a **SYN** but never receives a **SYN-ACK**, it immediately isolates the issue to a downed server, a routing failure, or a firewall dropping the packet.
    * If the full handshake completes but subsequent data is delayed, it indicates application-level latency rather than a core connectivity issue.
