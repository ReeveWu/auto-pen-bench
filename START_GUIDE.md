# AutoPenBench Environment Startup Guide

After completing `make install`, here is how to start the environment so your system can connect via SSH for testing.

---

## Quick Start Methods

### Method 1: Start Only Kali Workstation

```bash
cd /Users/reevewu/Project/Grad/auto-pen-bench

# Start Kali (SSH automatically mapped to local port 2222)
docker-compose -f benchmark/machines/docker-compose.yml up -d kali_master

# Test connection
ssh root@localhost -p 2222
# Password: root
```

### Method 2: Start Kali + Specific VM

```bash
# Example 1: Start Kali + access_control vm0
docker-compose \
  -f benchmark/machines/docker-compose.yml \
  -f benchmark/machines/in-vitro/access_control/docker-compose.yml \
  up -d kali_master in-vitro_access_control_vm0

# Example 2: Start Kali + web_security vm0
docker-compose \
  -f benchmark/machines/docker-compose.yml \
  -f benchmark/machines/in-vitro/web_security/docker-compose.yml \
  up -d kali_master in-vitro_web_security_vm0

# Example 3: Start Kali + real-world CVE vm0
docker-compose \
  -f benchmark/machines/docker-compose.yml \
  -f benchmark/machines/real-world/cve/docker-compose.yml \
  up -d kali_master real-world_cve_vm0
```

### Method 3: Start Kali + All VMs in a Category

```bash
# Start all access_control VMs
docker-compose \
  -f benchmark/machines/docker-compose.yml \
  -f benchmark/machines/in-vitro/access_control/docker-compose.yml \
  up -d

# Start all web_security VMs
docker-compose \
  -f benchmark/machines/docker-compose.yml \
  -f benchmark/machines/in-vitro/web_security/docker-compose.yml \
  up -d
```

### Method 4: Start All Environments (Requires Large Amount of Memory)

```bash
# Start all in-vitro tasks
docker-compose \
  -f benchmark/machines/docker-compose.yml \
  -f benchmark/machines/in-vitro/access_control/docker-compose.yml \
  -f benchmark/machines/in-vitro/web_security/docker-compose.yml \
  -f benchmark/machines/in-vitro/network_security/docker-compose.yml \
  -f benchmark/machines/in-vitro/cryptography/docker-compose.yml \
  up -d
```

---

## Environment Architecture Description

### Network Topology

AutoPenBench uses Docker to create an isolated virtual network environment:

```
┌─────────────────────────────────────────────────────────┐
│                    Docker Network                       │
│                   192.168.0.0/16                        │
│                                                         │
│  ┌──────────────────┐                                   │
│  │  Kali Workstation│                                   │
│  │  192.168.0.5     │                                   │
│  └────────┬─────────┘                                   │
│           │                                             │
│           ├──> 192.168.1.0/24 (Access Control)          │
│           │    ├─ vm0: 192.168.1.0                      │
│           │    ├─ vm1: 192.168.1.1                      │
│           │    └─ vm2-4: ...                            │
│           │                                             │
│           ├──> 192.168.2.0/24 (Web Security)            │
│           │    ├─ vm0: 192.168.2.0                      │
│           │    └─ vm1-6: ...                            │
│           │                                             │
│           ├──> 192.168.3.0/24 (Network Security)        │
│           │                                             │
│           ├──> 192.168.4.0/24 (Cryptography)            │
│           │                                             │
│           └──> 192.168.5.0/24 (Real-world CVE)          │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Key Components

1. **Kali Workstation (192.168.0.5)**
   - The starting point for your Agent
   - Pre-installed penetration testing tools (nmap, hydra, metasploit, etc.)
   - SSH Login: `root/root`

2. **Vulnerable Machines**
   - Distributed across different subnets based on category
   - Each machine has unique vulnerabilities and flags


### Task Execution and Evaluation

1. **Task Description**
   - Refer to `data/games.json` to understand the goals and flags for each task

2. **Reference Execution Process and Answers**
   - Official solutions are located in the `benchmark/solutions/` directory
