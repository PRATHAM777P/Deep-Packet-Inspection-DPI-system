<div align="center">

![DPI Engine Banner](https://capsule-render.vercel.app/api?type=waving&color=0:0d1117,100:1a3a5c&height=200&section=header&text=Deep%20Packet%20Inspection%20Engine&fontSize=32&fontColor=58a6ff&fontAlignY=40&desc=High-Performance%20Network%20Traffic%20Analysis%20in%20C%2B%2B&descAlignY=62&descColor=8b949e)

<img src="https://img.shields.io/badge/C%2B%2B-17-black?style=for-the-badge&logo=cplusplus&logoColor=00FF41"/>
<img src="https://img.shields.io/badge/DPI-Engine-black?style=for-the-badge&logo=wireshark&logoColor=00FF41"/>
<img src="https://img.shields.io/badge/TLS-SNI_Inspection-black?style=for-the-badge&logo=letsencrypt&logoColor=00FF41"/>
<img src="https://img.shields.io/badge/Multi--Threaded-14B8A6?style=for-the-badge&logo=processwire&logoColor=white"/>
<img src="https://img.shields.io/badge/Platform-Cross--Platform-0891B2?style=for-the-badge&logo=linux&logoColor=white"/>

<br/><br/>

> **A Deep Packet Inspection engine written in C++** that analyzes network traffic from PCAP files,
> identifies applications using packet metadata and TLS **SNI inspection**, and applies filtering rules
> to block specific **applications, domains, or IP addresses**.
>
> The system parses multiple network protocol layers, tracks connections using the **Five-Tuple**,
> and processes packets using a **multi-threaded architecture** to improve performance and scalability.

<br/>

[📌 Overview](#-project-overview) • [🚀 Features](#-key-features) • [🏗 Architecture](#-architecture) • [📂 Structure](#-project-structure) • [🛠 Build](#-building-the-project) • [▶️ Run](#️-running-the-engine) • [🔮 Roadmap](#-future-improvements)

</div>

---

## 📌 Project Overview

This project demonstrates how **modern network monitoring and security systems analyze traffic beyond traditional packet filtering**.

Captured network traffic is provided as a **PCAP file**, which is processed by the DPI engine. The engine analyzes each packet, applies filtering rules, and writes allowed traffic into a new **filtered PCAP output file**.

### ⚙️ Workflow

```
┌─────────────────┐        ┌──────────────────────┐        ┌──────────────────────┐
│   Input PCAP    │ ──────▶│     DPI Engine       │ ──────▶│  Filtered PCAP Out   │
└─────────────────┘        └──────────────────────┘        └──────────────────────┘
```

### 🔄 Core Processing Steps

| Step | Description |
|------|-------------|
| 📦 **Packet Parsing** | Parse raw bytes across protocol layers |
| 🔗 **Flow Identification** | Group packets into network flows |
| 🧠 **Application Detection** | Identify apps via metadata & SNI |
| 🚫 **Rule-based Filtering** | Apply block/allow rules |
| 📊 **Statistics Generation** | Output traffic analysis report |

---

## 🚀 Key Features

### 📦 Packet Parsing

The engine parses multiple network protocol layers:

```
┌──────────────┐
│   Ethernet   │  ← MAC addresses, EtherType
├──────────────┤
│    IPv4      │  ← Source/Destination IPs, TTL
├──────────────┤
│  TCP / UDP   │  ← Ports, flags, sequence numbers
├──────────────┤
│   Payload    │  ← Application-layer data
└──────────────┘
```

This enables extraction of critical **network metadata** for deeper inspection.

---

### 🔗 Flow Identification

Each connection is tracked using the **Five-Tuple**:

```
┌─────────────────────────────────────────┐
│           Five-Tuple Flow ID            │
│                                         │
│  🌐 Source IP        →  192.168.x.x     │
│  🌐 Destination IP   →  142.250.x.x     │
│  🔌 Source Port      →  54321           │
│  🔌 Destination Port →  443             │
│  📡 Protocol         →  TCP             │
└─────────────────────────────────────────┘
```

Packets sharing the same Five-Tuple belong to the **same network flow**, allowing stateful traffic analysis.

---

### 🔍 Deep Packet Inspection

The engine inspects packet payloads to detect applications.

For **HTTPS traffic**, it extracts the **Server Name Indication (SNI)** from the TLS handshake — revealing the domain name **before encryption begins**.

```
TLS ClientHello
  └── Extension: server_name
        └── SNI: www.youtube.com
                  │
                  ▼
        Detected App: YouTube ✅
```

---

### 🚫 Traffic Blocking

The system supports **rule-based traffic filtering**, allowing blocking by:

| Rule Type | Example |
|-----------|---------|
| 🧾 IP Address | `--block-ip 203.0.113.5` |
| 📱 Application | `--block-app YouTube` |
| 🌍 Domain Name | `--block-domain facebook` |

Packets matching blocking rules are **dropped** and not written to the output file.

---

## 🏗 Architecture

The project includes **two implementations**.

---

### 🧩 Single-Threaded Version

A simple implementation where packets are processed **sequentially**.

> Useful for learning packet inspection and debugging packet processing.

```
PCAP Reader ──▶ Packet Parser ──▶ Classifier ──▶ Rule Engine ──▶ Output
```

---

### ⚡ Multi-Threaded Version

A high-performance implementation that processes packets in **parallel**.

```
                ┌───────────────────────────────────────────┐
                │          Parallel Processing Pool         │
                │                                           │
PCAP Reader ──▶ │  Load Balancer ──▶ ⚙️ Worker  ──▶ ...    │──▶ Output Writer
                │                 ──▶ ⚙️ Worker  ──▶ ...    │
                │                 ──▶ ⚙️ Worker  ──▶ ...    │
                └───────────────────────────────────────────┘
```

| Thread | Role |
|--------|------|
| 📥 **Reader** | Reads packets from PCAP file |
| ⚖️ **Load Balancer** | Distributes packets across workers |
| ⚙️ **Worker (Fast Path)** | Inspects and classifies packets |
| 💾 **Output Writer** | Writes allowed packets to output |

> This architecture allows the system to **scale with available CPU cores**.

---

## 📂 Project Structure

```
deep-packet-inspector/
│
├── 📁 include/
│   └── Header files for packet parsing, flow tracking, and DPI logic
│       ├── connection_tracker.h
│       ├── dpi_engine.h
│       ├── fast_path.h
│       ├── load_balancer.h
│       ├── packet_parser.h
│       ├── pcap_reader.h
│       ├── platform.h
│       ├── rule_manager.h
│       ├── sni_extractor.h
│       ├── thread_safe_queue.h
│       └── types.h
│
├── 📁 src/
│   └── Core implementation of the DPI engine
│       ├── main.cpp / main_dpi.cpp / main_working.cpp
│       ├── dpi_engine.cpp / dpi_mt.cpp
│       ├── pcap_reader.cpp
│       ├── packet_parser.cpp
│       ├── sni_extractor.cpp
│       ├── connection_tracker.cpp
│       ├── rule_manager.cpp
│       ├── fast_path.cpp
│       ├── load_balancer.cpp
│       └── types.cpp
│
├── 🐍 generate_test_pcap.py    ← Generate sample network traffic
├── 📦 test_dpi.pcap            ← Example PCAP file for testing
├── ⚙️  CMakeLists.txt           ← Build configuration
├── 🪟 WINDOWS_SETUP.md         ← Windows build instructions
└── 📖 README.md
```

---

## 🔄 Packet Processing Pipeline

```
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  1️⃣  READ        Read packet from PCAP file              │
  │         │                                                │
  │         ▼                                                │
  │  2️⃣  PARSE       Extract MAC, IP, ports, protocol        │
  │         │                                                │
  │         ▼                                                │
  │  3️⃣  IDENTIFY    Generate Five-Tuple flow ID             │
  │         │                                                │
  │         ▼                                                │
  │  4️⃣  INSPECT     Extract SNI from TLS handshake          │
  │         │                                                │
  │         ▼                                                │
  │  5️⃣  EVALUATE    Check packet against blocking rules     │
  │         │                                                │
  │        / \                                               │
  │       ▼   ▼                                              │
  │      ✅   ❌                                              │
  │   Forward  Drop                                          │
  │   to PCAP                                                │
  └──────────────────────────────────────────────────────────┘
```

---

## 🛠 Building the Project

### 📋 Requirements

- C++17 compatible compiler (`g++`, `clang++`, or MSVC)
- Linux / macOS / Windows (MinGW)
- Python 3 *(optional — for generating test PCAP files)*

### 🔧 Compile

```bash
g++ -std=c++17 -O2 -I include \
    src/*.cpp \
    -o dpi_engine
```

> 🪟 **Windows users:** See [WINDOWS_SETUP.md](WINDOWS_SETUP.md) for detailed Visual Studio, MinGW, and WSL instructions.

---

## ▶️ Running the Engine

### Basic Usage

```bash
./dpi_engine input.pcap output.pcap
```

### Example with Blocking Rules

```bash
./dpi_engine input.pcap output.pcap \
  --block-app YouTube \
  --block-ip 192.168.1.50 \
  --block-domain facebook
```

---

## 📊 Example Output

```
╔══════════════════════════════════════════╗
║          DPI Engine — Results            ║
╠══════════════════════════════════════════╣
║  Total Packets   :   77                  ║
║  Forwarded       :   69   ✅             ║
║  Dropped         :    8   ❌             ║
╠══════════════════════════════════════════╣
║  Detected Applications:                  ║
║    • HTTPS                               ║
║    • YouTube                             ║
║    • Facebook                            ║
║    • DNS                                 ║
╚══════════════════════════════════════════╝
```

---

## 🔮 Future Improvements

- [ ] ➕ Adding more application signatures
- [ ] 📡 Supporting live network packet capture
- [ ] ⏱️ Implementing bandwidth throttling
- [ ] 🌐 Creating a web dashboard for monitoring
- [ ] ⚡ Adding support for QUIC / HTTP3 traffic

---

## 🎓 Educational Purpose

This project demonstrates important **network security and packet analysis concepts**:

| Concept | What It Shows |
|---------|--------------|
| 📦 Network packet structure | Multi-layer protocol parsing |
| 🔗 Flow tracking | Five-Tuple state machine |
| 🔎 Deep packet inspection | Payload-level application detection |
| 🔐 TLS handshake analysis | SNI extraction before encryption |
| ⚡ Multi-threaded systems | Thread-safe queues & load balancing |

It serves as a practical learning project for:

<div align="center">

**Network Security • Packet Analysis • Traffic Monitoring Systems**

![Footer](https://capsule-render.vercel.app/api?type=waving&color=0:1a3a5c,100:0d1117&height=100&section=footer)

</div>
