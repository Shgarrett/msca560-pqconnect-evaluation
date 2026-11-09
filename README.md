# msca560-pqconnect-evaluation
MSCA 560: Distributed Systems and Network Security Individual Term Project — Fall 2026. 

----------------------------------------------------------------------------------------------------


Evaluating Automated Post-Quantum Network Tunnels: PQConnect Reproduction

MSCA 560: Distributed Systems and Network Security  
School of Applied Computational Sciences, Meharry Medical College  
Sterling H. Garrett
Term Project

---

Project Overview & Research Question

Deploying Post-Quantum Cryptography (PQC) at the application layer (such as PQ-TLS) is a slow and fragmented process across enterprise and healthcare systems. Legacy mail relays, database connections, and proprietary protocols cannot easily be recompiled or refactored. 

This project reproduces and evaluates **PQConnect** (NDSS 2025), an automated network-layer tunneling system. PQConnect inspects standard DNS lookups, discovers whether a server supports post-quantum encryption, fetches the necessary public keys, and transparently encapsulates raw IP traffic into authenticated UDP tunnels without requiring software modifications or manual peer-to-peer VPN configurations.

Research Question
Can an automated network-layer tunnel transparently protect unmodified application traffic against quantum eavesdropping without per-peer configuration, and what are the practical operational trade-offs (packet expansion, MTU limits, and key streaming overhead) across standard network links?

---

Experimental Approach

The evaluation follows three structured steps:

1. Formal Handshake Verification:
   * Model and verify the 0-RTT initiation handshake using the Tamarin Prover.
   * Verify core lemmas for handshake executability, forward secrecy under long-term and ephemeral key compromise, and responder authentication.

2. Traffic Interception & Application Transparency:
   * Run the PQConnect Linux client daemon using `netfilter` hooks.
   * Send unencrypted HTTP traffic (`wget`) to a supporting server (`www.pqconnect.net`) and verify via `tcpdump` that plain text never touches the physical interface.
   * Verify fallback behavior against non-supporting servers (`testwithout.pqconnect.net`) to ensure traffic passes without drops or spurious probe packets.

3. Network Performance & Operational Analysis:
   * Evaluate the 3-RTT Merkle tree streaming protocol used to transfer the 1 MB Classic McEliece (`mceliece6960119`) public key.
   * Measure the 56-byte per-packet encapsulation overhead and analyze its operational impact on Ethernet MTUs (1500 bytes) and router MSS clamping.

---
Repository Structure

```text
├── proofs/
│   ├── handshake.spthy         # Formal Tamarin specification of the 0-RTT handshake
│   └── tamarin_output.txt      # Execution trace logs and verification outputs
├── captures/
│   ├── pqconnect_tunnel.pcap   # Wireshark trace of encapsulated UDP port 42424 traffic
│   ├── plain_fallback.pcap     # Capture showing unencrypted fallback for non-PQ hosts
│   └── key_fetch_stream.pcap   # Merkle tree leaf exchange across 3-RTTs
├── scripts/
│   ├── run_tamarin.sh          # Script to reproduce the Tamarin security proofs
│   └── capture_traffic.sh      # tcpdump script filtered for tunnel verification
├── docs/
│   ├── Project_Proposal.pdf    # Stage 1 Deliverable (1-2 pages, no references)
│   ├── Presentation_Deck.pdf   # Stage 3 Deliverable (Slide deck and speaking notes)
│   └── Final_Report.pdf        # Stage 4 Deliverable (6-8 page IEEE format paper)
└── README.md                   # Project documentation and reproduction instructions


***Disclaimer: This project is a work in progress. Timelines are projected and not matter of fact. System requirements may restrict the progress of some or all steps during the evaluation of this project which may lead to incomplete results.***
