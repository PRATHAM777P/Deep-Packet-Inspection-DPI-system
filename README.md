# 🔎 Deep Packet Inspection (DPI) Engine

A **Deep Packet Inspection (DPI) engine written in C++** that analyzes network traffic from PCAP files, identifies applications using packet metadata and TLS **SNI inspection**, and applies filtering rules to block specific **applications, domains, or IP addresses**.

The system parses multiple network protocol layers, tracks connections using the **Five-Tuple**, and processes packets using a **multi-threaded architecture** to improve performance and scalability.

---

# 📌 Project Overview

This project demonstrates how **modern network monitoring and security systems analyze traffic beyond traditional packet filtering**.

Captured network traffic is provided as a **PCAP file**, which is processed by the DPI engine. The engine analyzes each packet, applies filtering rules, and writes allowed traffic into a new **filtered PCAP output file**.

### ⚙️ Workflow

```
Input PCAP → DPI Engine → Filtered PCAP Output
```

### 🔄 Core Processing Steps

* 📦 Packet parsing
* 🔗 Flow identification
* 🧠 Application detection
* 🚫 Rule-based filtering
* 📊 Traffic statistics generation

---

# 🚀 Key Features

## 📦 Packet Parsing

The engine parses multiple network protocol layers:

* Ethernet
* IPv4
* TCP / UDP
* Application payload

This enables extraction of critical **network metadata** for deeper inspection.

---

## 🔗 Flow Identification

Each connection is tracked using the **Five-Tuple**:

* 🌐 Source IP address
* 🌐 Destination IP address
* 🔌 Source port
* 🔌 Destination port
* 📡 Protocol

Packets sharing the same Five-Tuple belong to the **same network flow**, allowing stateful traffic analysis.

---

## 🔍 Deep Packet Inspection

The engine inspects packet payloads to detect applications.

For **HTTPS traffic**, it extracts the **Server Name Indication (SNI)** from the TLS handshake.

SNI reveals the domain name being accessed **before encryption begins**.

### Example

```
SNI: www.youtube.com
Detected Application: YouTube
```

---

## 🚫 Traffic Blocking

The system supports **rule-based traffic filtering**, allowing blocking by:

* 🧾 IP address
* 📱 Application type
* 🌍 Domain name

Packets matching blocking rules are **dropped** and not written to the output file.

---

# 🏗 Architecture

The project includes **two implementations**.

---

## 🧩 Single-Threaded Version

A simple implementation where packets are processed sequentially.

Useful for:

* Learning packet inspection
* Debugging packet processing

```
PCAP Reader → Packet Parser → Classifier → Rule Engine → Output
```

---

## ⚡ Multi-Threaded Version

A high-performance implementation that processes packets in **parallel**.

### Threads Used

* 📥 Reader thread → Reads packets from PCAP
* ⚖️ Load balancer threads → Distribute packets
* ⚙️ Worker threads (Fast Path) → Process packets
* 💾 Output writer thread → Writes filtered packets

```
Reader → Load Balancer → Worker Threads → Output Writer
```

This architecture allows the system to **scale with available CPU cores**.

---

# 📂 Project Structure

```
deep-packet-inspector
│
├── include/
│   Header files for packet parsing, flow tracking, and DPI logic
│
├── src/
│   Core implementation of the DPI engine
│
├── generate_test_pcap.py
│   Script used to generate sample network traffic
│
├── test_dpi.pcap
│   Example PCAP file used for testing
│
├── CMakeLists.txt
│   Build configuration file
│
├── WINDOWS_SETUP.md
│   Windows build instructions
│
└── README.md
```

---

# 🔄 Packet Processing Pipeline

Packets pass through several stages during analysis.

### 1️⃣ Packet Reading

Packets are sequentially read from the PCAP file.

### 2️⃣ Protocol Parsing

Network headers are parsed to extract:

* MAC addresses
* IP addresses
* Ports
* Protocol type

### 3️⃣ Flow Identification

The system generates a **Five-Tuple** to track each connection.

### 4️⃣ Payload Inspection

For HTTPS packets, the TLS handshake is inspected to extract the **SNI hostname**.

### 5️⃣ Rule Evaluation

Blocking rules are checked against the connection.

### 6️⃣ Forward or Drop

* ✅ Allowed packets → written to output PCAP
* ❌ Blocked packets → dropped

---

# 🛠 Building the Project

### 📋 Requirements

* C++17 compatible compiler
* Linux / macOS / Windows (MinGW)
* Python (optional, for generating test PCAP)

### 🔧 Compile

Example build command:

```
g++ -std=c++17 -O2 -I include \
src/*.cpp \
-o dpi_engine
```

---

# ▶️ Running the Engine

### Basic Usage

```
./dpi_engine input.pcap output.pcap
```

### Example with Blocking Rules

```
./dpi_engine input.pcap output.pcap \
--block-app YouTube \
--block-ip 192.168.1.50 \
--block-domain facebook
```

---

# 📊 Example Output

```
Total Packets: 77
Forwarded: 69
Dropped: 8

Detected Applications:
HTTPS
YouTube
Facebook
DNS
```

The output displays **traffic statistics and application detection results**.

---

# 🔮 Future Improvements

Possible enhancements include:

* ➕ Adding more application signatures
* 📡 Supporting live network packet capture
* ⏱ Implementing bandwidth throttling
* 🌐 Creating a web dashboard for monitoring
* ⚡ Adding support for QUIC / HTTP3 traffic

---

# 🎓 Educational Purpose

This project demonstrates important **network security and packet analysis concepts**:

* 📦 Network packet structure
* 🔗 Flow tracking using Five-Tuple
* 🔎 Deep packet inspection techniques
* 🔐 TLS handshake analysis
* ⚡ Multi-threaded packet processing

It serves as a practical learning project for:

**Network Security • Packet Analysis • Traffic Monitoring Systems**


