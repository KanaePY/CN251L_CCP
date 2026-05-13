# 🔁 CN251L — CCP Problem 5: Retail Network Failover

> **Course:** Computer Networks (CN251L) — CCP-CNS2026  
> **Problem:** #5 — Retail Network Failover  
> **Instructor:** Muhammad Fahad Irshad  
> **Duration:** 5 Weeks  

---

## 📋 Table of Contents

- [Scenario](#scenario)
- [Network Requirements](#network-requirements)
- [Topology](#topology)
- [Repository Structure](#repository-structure)
- [Team & Roles](#team--roles)
- [Weekly Progress](#weekly-progress)
- [Deliverables Checklist](#deliverables-checklist)
- [Tools & Resources](#tools--resources)
- [How to Run](#how-to-run)


---

## 🏪 Scenario

**RetailNow** stores operate with two simultaneous Internet connections:
- **Fiber** (primary — fast, GigabitEthernet)
- **LTE** (backup — slow, Serial or Ethernet)

The goal is to configure the store router so that:
- All traffic flows through Fiber by default
- When Fiber fails, traffic **automatically** switches to LTE within **60 seconds**
- When Fiber recovers, traffic **automatically** falls back to Fiber

---

## ⚙️ Network Requirements

| Requirement | Description |
|---|---|
| Two WAN links | Simulate Fiber (GigabitEthernet) and LTE (Serial or Ethernet) |
| Primary route | All traffic uses Fiber by default |
| Backup route | LTE used only when Fiber is down |
| Tracking | Router must detect when Fiber link fails |
| Automatic failover | Switch to LTE within 60 seconds of fiber failure |
| Automatic recovery | Switch back to Fiber when it comes back up |

---

## 🗺️ Topology

Built in **GNS3**.

| Device | Quantity | Role |
|---|---|---|
| Cisco Router (Branch) | 1 | Store router with two WAN links |
| Cisco Router (ISP1) | 1 | Simulates Fiber ISP |
| Cisco Router (ISP2) | 1 | Simulates LTE ISP |
| VPCS | 1 | Customer PC browsing the Internet |

> Topology diagrams are available in the [`/diagrams`](./diagrams/) folder.

---

## 📁 Repository Structure

```
CN251L_CCP/
├── README.md
├── configs/           # Router running-configs (populated Week 2+)
├── diagrams/          # Topology diagrams (draw.io + PDF)
│   └── topology_v1.pdf
├── docs/              # Reports and deliverables
│   ├── status_reports/
│   │   └── week1_status_report.md 
│   ├── team_charter.pdf
│   ├── ip_addressing_plan.xlsx
│   └── risk_assessment.pdf
├── scripts/           # Python automation scripts (Week 4)
├── pcaps/             # Wireshark capture files (Week 3+)
└── gns3/              # GNS3 project files (Week 2+)
```

---

## 👥 Team & Roles

| # | Name | Role |
|---|---|---|
| 1 | *Muhammad Ibrahim* | Network Architect |
| 2 | *Inshal Haider* | Security Engineer |
| 3 | *Arham Sarfraz* | Automation Lead |
| 4 | *Jawaria* | Testing / QA Engineer |
| 5 | *Azka Butt* | Documentation Lead |
| 6 | *Hasnaat Malik* | Project Manager |

---

## 📅 Weekly Progress

| Week | Focus | Status |
|---|---|---|
| Week 1 | Requirements analysis, topology design, IP addressing plan | ✅ Complete |
| Week 2 | GNS3 implementation, basic connectivity, running configs | ⬜ Not Started |
| Week 3 | Security implementation, L2 configs, ACLs | ⬜ Not Started |
| Week 4 | NAT (if applicable), performance baseline, final integration | ⬜ Not Started |
| Week 5 | Final report, video demo, presentation | ⬜ Not Started |

> Update statuses to: ✅ Complete / 🔄 In Progress / ⬜ Not Started

---

## ✅ Deliverables Checklist

### Week 1
- [c] Team charter signed (PDF)
- [c] Logical topology diagram (draw.io / PDF)
- [c] IP addressing plan (.xlsx or .csv)
- [c] GitHub repository link
- [c] Resource acquisition report (screenshots)
- [c] Risk assessment matrix (PDF)
- [c] Week 1 status report (Markdown / PDF)

### Week 2
- [ ] GNS3 project file (`.gns3` + image folder zipped)
- [ ] Running-configs of all devices (`.txt` in `configs/`)
- [ ] Ping test results (all-to-all matrix)
- [ ] SSH login proof (from Kali)
- [ ] Week 2 status report

### Week 3
- [ ] L2 security configs + verification screenshots
- [ ] ACL configurations with hit counts
- [ ] Attack execution script & logs
- [ ] PCAP files
- [ ] Week 3 status report

### Week 4
- [ ] NAT configuration (if applicable)
- [ ] Baseline performance report (throughput, latency)
- [ ] Final GNS3 project v3 + all configs
- [ ] Final test results (test matrix with pass/fail)
- [ ] Week 4 status report

### Week 5 (Final)
- [ ] Working GNS3 topology file with all devices configured
- [ ] Running-configs of branch router (`configs/branch_router.txt`)
- [ ] Screenshots / 2-min video showing:
  - [ ] Normal operation — traffic via Fiber
  - [ ] Fiber link manually shut down
  - [ ] Traffic automatically switching to LTE
  - [ ] Fiber restored, traffic returning to Fiber
- [ ] Brief report (4 pages) explaining failover mechanism
- [ ] Final report (15–20 pages)
- [ ] Video demonstration (8–10 min, narrated, YouTube unlisted)
- [ ] Team presentation slides + recording (12 min)
- [ ] Peer evaluation forms (individually submitted)

---

## 🛠️ Tools & Resources

| Tool | Purpose | Link |
|---|---|---|
| GNS3 | Network simulation | https://gns3.com/software/download |
| Wireshark | Packet capture & analysis | https://www.wireshark.org/download.html |
| draw.io | Topology diagrams | https://app.diagrams.net/ |
| iPerf3 | Performance/throughput testing | https://iperf.fr/ |
| Kali Linux | Security testing VM | https://www.kali.org/get-kali/#kali-virtual-machines |
| OBS Studio | Screen/video recording | https://obsproject.com/ |
| GitHub | Version control & submission | https://github.com |

---

## 🚀 How to Run

1. **Clone the repository**
   ```bash
   git clone https://github.com/KanaePY/CN251L_CCP.git
   cd CN251L_CCP
   ```

2. **Open GNS3 topology**  
   Launch GNS3 and open the `.gns3` project file from the root or `configs/` folder.

3. **Load device configurations**  
   Apply the running-configs from `configs/` to each respective device via the GNS3 console.

4. **Test failover**
   - Verify normal operation: `show ip route` on branch router (Fiber as primary)
   - Simulate failure: `shutdown` the Fiber-facing interface on Branch Router
   - Observe automatic failover to LTE within 60 seconds
   - Restore Fiber: `no shutdown` and verify traffic returns to primary path

---
