---
layout: default
---

# Installing Ollama on Linux

This chapter explains how to install and configure Ollama on a Linux system so it can serve as the local LLM backend for your autonomous pentesting agent.

---

## Requirements

- 64‑bit Linux distribution (Ubuntu, Debian, etc.)  
- At least 8 GB RAM (16+ recommended)  
- Optional but recommended: GPU with recent drivers  
- Internet connection for downloading models  

---

## Installation

Run the official installation script:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```


## Starting services
``` bash
sudo systemctl enable ollama
sudo systemctl start ollama
```
## Check status and verify installation
```bash
sudo systemctl enable ollama
sudo systemctl start ollama
sudo systemctl status ollama
● ollama.service - Ollama Service
     Loaded: loaded (/etc/systemd/system/ollama.service; enabled; preset: enabled)
     Active: active (running) since Tue 2026-02-10 03:11:32 CET; 6 days ago
   Main PID: 1835 (ollama)
      Tasks: 23 (limit: 18865)
     Memory: 450.1M (peak: 11.2G swap: 742.4M swap peak: 954.5M)
        CPU: 2d 19h 50min 51.582s
     CGroup: /system.slice/ollama.service
             └─1835 /usr/local/bin/ollama serve

feb 15 20:19:39 f0ns1-GF63-Thin-10SCXR ollama[1835]: llama_context: graph splits = 4 (with bs=512), 3 (with bs=1)
feb 15 20:19:39 f0ns1-GF63-Thin-10SCXR ollama[1835]: time=2026-02-15T20:19:39.905+01:00 level=INFO source=server.go:1388 msg="llam>
feb 15 20:19:39 f0ns1-GF63-Thin-10SCXR ollama[1835]: time=2026-02-15T20:19:39.906+01:00 level=INFO source=sched.go:537 msg="loaded>
feb 15 20:19:39 f0ns1-GF63-Thin-10SCXR ollama[1835]: time=2026-02-15T20:19:39.906+01:00 level=INFO source=server.go:1350 msg="wait>
feb 15 20:19:39 f0ns1-GF63-Thin-10SCXR ollama[1835]: time=2026-02-15T20:19:39.906+01:00 level=INFO source=server.go:1388 msg="llam>
feb 15 20:45:21 f0ns1-GF63-Thin-10SCXR ollama[1835]: [GIN] 2026/02/15 - 20:45:21 | 200 |        25m46s |       127.0.0.1 | POST   >
feb 15 20:45:44 f0ns1-GF63-Thin-10SCXR ollama[1835]: [GIN] 2026/02/15 - 20:45:44 | 200 |  5.340698813s |       127.0.0.1 | POST   >
feb 15 20:46:28 f0ns1-GF63-Thin-10SCXR ollama[1835]: [GIN] 2026/02/15 - 20:46:28 | 200 | 27.923653063s |       127.0.0.1 | POST   >
feb 15 20:51:28 f0ns1-GF63-Thin-10SCXR ollama[1835]: time=2026-02-15T20:51:28.658+01:00 level=INFO source=server.go:431 msg="start>
feb 15 20:51:29 f0ns1-GF63-Thin-10SCXR ollama[1835]: time=2026-02-15T20:51:29.994+01:00 level=INFO source=server.go:431 msg="start>

ollama --version
ollama version is 0.15.6

```


[back](./)