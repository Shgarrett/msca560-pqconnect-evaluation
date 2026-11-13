# MSCA 560: Experimental Replication and Analysis of PQConnect

**Author:** Sterling H. Garrett  
**Course:** MSCA 560 – Distributed Systems and Network Security (Fall 2026)  
**Institution:** School of Applied Computational Sciences, Meharry Medical College  
**Faculty:** Prof. Firdous Kausar  

## Research Question
Can an automated network-layer tunneling approach (PQConnect, NDSS 2025) protect existing application traffic with post-quantum cryptography without requiring changes to applications or manual VPN setup, and what are the resulting impacts on network performance (latency, packet overhead, and setup time)?

## Repository Structure
- `README.md` — Project overview, testbed specifications, and reproduction instructions.
- `patches/python314-fork-start-method.patch` — Compatibility patch explicitly setting `multiprocessing.set_start_method("fork")` for Python 3.14+ (`Ubuntu 26.04.1 LTS`, kernel `7.0.0-43-generic`), preventing `NetfilterQueue` Cython pickling failures under `forkserver`.
- `results/pqconnect-client.log` — Live client daemon execution logs showing privilege separation (`uid 1001`), DNS `pq1...` CNAME detection, virtual IP rewriting (`10.43.0.2`), and cold vs. cached tunnel establishment timings.
- `results/tcpdump-capture.txt` — Wire-level packet capture (`enp0s3`) demonstrating cleartext TCP/80 transmission for `testwithout.pqconnect.net` alongside UDP/42424 encapsulated ciphertext for `www.pqconnect.net`.
- `results/handshake.spthy` — Tamarin Prover symbolic verification model defining `0_RTT_executable`, `0_RTT_FS_confidential`, and `responder_client_auth`.

## Reproduction Steps (Ubuntu 26.04.1 LTS)
1. Clone upstream PQConnect (`v1.2.3`) and apply `patches/python314-fork-start-method.patch`.
2. Run `sudo scripts/install-pqconnect` to build `libcpucycles`, `librandombytes`, `libmceliece`, `libntruprime`, and `lib25519`.
3. Launch the client daemon via `sudo scripts/run-client &` and verify interface `pqccli0` (`10.43.0.1/16`, MTU 1200).
4. Execute `dig +short www.pqconnect.net`, `curl https://www.pqconnect.net/test.html`, `ping -nc 15 www.pqconnect.net`, and `tcpdump -i enp0s3 -nn -X host 131.193.32.108`.
