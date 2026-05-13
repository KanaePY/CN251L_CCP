# Week 1 Status Report
## CCP-CNS2026 | Problem 5: Retail Network Failover
**Team:** RetailNow-Team-05 | **Week:** 1 of 5 | **Date:** May 13, 2026  
**Prepared by:** Hasnaat Malik (Project Manager)

---

## 1. Project Summary

**Problem Statement:** RetailNow stores require an automatic WAN failover solution. The branch router must use Fiber (GigabitEthernet) as the primary WAN link and LTE (Ethernet) as a backup. When Fiber fails, traffic must automatically route through LTE within 60 seconds, and revert to Fiber automatically upon recovery.

**Objective for Week 1:** Complete all planning deliverables — team charter, logical topology, IP addressing plan, GitHub repository setup, and risk assessment — before proceeding to GNS3 implementation in Week 2.

---

## 2. Team Members

| Name | Role | Status |
|---|---|---|
| Muhammad Ibrahim | Network Architect | Active |
| Inshal Haider | Security Engineer | Active |
| Arham Lead | Automation Lead | Active |
| Jawaria | Testing/QA Engineer | Active |
| Azka Butt | Documentation Lead | Active |
| Hasnaat Malik | Project Manager | Active |

---

## 3. Week 1 Deliverables Checklist

| # | Deliverable | Owner | Status | Notes |
|---|---|---|---|---|
| 1 | Team Charter (PDF) | Hasnaat Malik | ✅ Complete | Signed by all 6 members |
| 2 | Logical Topology Diagram (draw.io/PDF) | Ahmed Raza | ✅ Complete | Exported to PDF; see attached |
| 3 | IP Addressing Plan (.xlsx) | Ahmed Raza | ✅ Complete | Covers all interfaces, subnets, routing |
| 4 | GitHub Repository Link | Arham | ✅ Complete | https://github.com/KanaePY/CN251L_CCP |
| 5 | Resource Acquisition Report | Jawaria | ✅ Complete | Screenshots of GNS3, IOS images attached |
| 6 | Risk Assessment Matrix (PDF) | Hasnaat Malik | ✅ Complete | 8 risks identified; 3 HIGH, 3 MEDIUM, 2 LOW |
| 7 | Week 1 Status Report (this document) | Hasnaat Malik | ✅ Complete | Submitted via LMS |

**Overall Week 1 Completion: 7/7 deliverables (100%)**

---

## 4. Technical Decisions Made This Week

### 4.1 Topology Design
- **3 Cisco Routers:** Branch-Router (store), ISP1-Router (Fiber), ISP2-Router (LTE)
- **1 VPCS client** simulating customer PC on LAN (192.168.10.0/24)
- ISP routers each have a Loopback interface (8.8.8.8 / 8.8.4.4) simulating Internet destinations
- All WAN links use /30 subnets to conserve IP space

### 4.2 Failover Mechanism
After research, the team decided to implement **IP SLA + Object Tracking + Floating Static Routes**:
- Primary route: `ip route 0.0.0.0 0.0.0.0 203.0.113.1 track 1` (AD = 1)
- Backup route: `ip route 0.0.0.0 0.0.0.0 198.51.100.1 10` (AD = 10, floating)
- IP SLA 1 sends ICMP echo to 8.8.8.8 every 10 seconds; if 2 consecutive probes fail, Track 1 goes down and primary route is removed

**Rationale:** This approach is native to Cisco IOS, requires no additional software, meets the 60-second failover requirement, and supports automatic recovery (preemption) without manual intervention.

### 4.3 GNS3 IOS Image Decision
The team will use **Cisco c3745 IOS 12.4** image, available from the university repository. Ahmed Raza confirmed the image supports IP SLA and has been tested locally.

---

## 5. Resources Acquired

| Resource | Version/Link | Acquired By | Status |
|---|---|---|---|
| GNS3 | v2.2.49 | Ahmed Raza | ✅ Installed |
| Cisco c3745 IOS | 12.4(25d) | Ahmed Raza | ✅ Downloaded |
| draw.io | Web (app.diagrams.net) | Azka Butt | ✅ Used |
| GitHub Repo | team05-retail-failover | Arham | ✅ Created |

---

## 6. GitHub Repository Structure

```
team05-retail-failover/
├── README.md
├── configs/           # Router running-configs (populated Week 2+)
├── diagrams/          # Topology diagrams (draw.io + PDF)
│   └── topology_v1.pdf
├── docs/              # Reports and deliverables
│   ├── team_charter.pdf
│   ├── ip_addressing_plan.xlsx
│   ├── risk_assessment.pdf
│   └── week1_status_report.md
├── scripts/           # Python automation scripts (Week 4)
├── pcaps/             # Wireshark capture files (Week 3+)
└── gns3/              # GNS3 project files (Week 2+)
```

---

## 7. Risks Identified This Week

| ID | Risk | Level | Action |
|---|---|---|---|
| R-01 | IP SLA may not detect failure within 60 seconds | HIGH | Tune to 5s frequency, test in Week 2 |
| R-02 | IOS image incompatible with IP SLA | HIGH | Confirmed c3745 supports IP SLA — mitigated |
| R-05 | Floating route may not activate on failure | HIGH | Will verify with 'debug ip routing' in Week 2 |
| R-03 | RAM insufficient for GNS3 | MEDIUM | Use Ahmed's 16 GB machine |
| R-04 | Team member unavailability | MEDIUM | All work documented in GitHub |

Full risk register: see `risk_assessment.pdf`

---

## 8. Planned Activities for Week 2

| Task | Owner | Target |
|---|---|---|
| Build GNS3 topology with all 3 routers | Ahmed Raza | Mon May 18 |
| Configure all interfaces + IP addresses | Ahmed Raza | Mon May 18 |
| Configure primary + floating static routes | Ahmed Raza | Tue May 19 |
| Configure IP SLA + object tracking | Ahmed Raza | Wed May 20 |
| Enable SSH on all routers | Inshal | Wed May 20 |
| Run all-to-all ping test matrix | Jawaria | Thu May 21 |
| Manually test failover (shut Gi0/0) | Jawaria | Thu May 21 |
| Capture routing table before/after failure | Jawaria | Thu May 21 |
| Export GNS3 project + running configs | Ahmed Raza | Fri May 22 |
| Write Week 2 status report | Hasnaat Malik | Fri May 22 |

---


*Report prepared by Hasnaat(Project Manager) and Azka Butt (Doc manager)*  
*CCP-CNS2026 | Problem 5: Retail Network Failover | Week 1*
