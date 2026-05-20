# Week 2 Status Report
## CCP-CNS2026 | Problem 5: Retail Network Failover
**Team:** RetailNow-Team-05 | **Week:** 2 of 5 | **Date:** May 21, 2026  
**Prepared by:** Hasnaat Malik (Project Manager)

---

## 1. Summary

Week 2 focused on building and configuring the full GNS3 topology. All devices were configured, connectivity was verified, and the automatic WAN failover was successfully demonstrated. A key topology change was made from the Week 1 design — an Internet-Router was added to accurately simulate real-world failover behavior.

---

## 2. Topology Change — v1.0 → v2.0

| | Week 1 Plan (v1.0) | Week 2 Implementation (v2.0) |
|---|---|---|
| Internet Simulation | Separate Loopbacks on ISP1 (8.8.8.8) and ISP2 (8.8.4.4) | Single Internet-Router with one Loopback (8.8.8.8) |
| ISP Routers | 2 devices | 2 devices + 1 Internet-Router |
| Total Routers | 3 | 4 |
| Failover Target | Different IPs per ISP | Same IP (8.8.8.8) reachable via both ISPs |

**Reason for change:** The original design had separate Loopback IPs on each ISP router. This meant IP SLA would always succeed regardless of which ISP was active, making failover untestable. The correct approach is to have one Internet-Router that both ISPs connect to, hosting a single IP (8.8.8.8). This way IP SLA accurately detects when the Fiber path to the Internet is broken.

---

## 3. GNS3 Topology

**Devices:**
- Internet-Router (c3725) — simulates the Internet core
- ISP1-Router (c3725) — simulates Fiber ISP
- ISP2-Router (c3725) — simulates LTE ISP
- Branch-Router (c3725) — RetailNow store router
- Client-PC (VPCS) — customer PC

**Connections:**
```
         Internet-Router
          (Lo0: 8.8.8.8)
         f0/0        f0/1
          |              |
    10.0.0.1        10.0.0.5
    10.0.0.2        10.0.0.6
         |              |
     ISP1-Router    ISP2-Router
     203.0.113.1    198.51.100.1
     203.0.113.2    198.51.100.2
          \              /
           Branch-Router
           f1/0: 192.168.10.1
                 |
            Client-PC
         192.168.10.10
```

---

## 4. Week 2 Deliverables Checklist

| # | Deliverable | Owner | Status | Notes |
|---|---|---|---|---|
| 1 | GNS3 project file (.gns3 + configs zipped) | Muhammad Ibrahim | ✅ Complete | Saved and exported |
| 2 | Running-configs of all devices (.txt) | Muhammad Ibrahim | ✅ Complete | See configs/ folder in GitHub |
| 3 | Ping test matrix (all-to-all) | Jawaria | ✅ Complete | See Section 5 below |
| 4 | Failover test screenshots | Jawaria | ✅ Complete | See Section 6 below |
| 5 | SSH login proof | Inshal Haider | ✅ Complete | SSH configured on all routers |
| 6 | Week 2 status report | Hasnaat Malik | ✅ Complete | This document |

---

## 5. Ping Test Matrix

### 5.1 Normal Operation (Fiber UP)

| Source | Destination | IP | Result | Path |
|---|---|---|---|---|
| Branch-Router | ISP1-Router | 203.0.113.1 | ✅ 100% | Fiber |
| Branch-Router | ISP2-Router | 198.51.100.1 | ✅ 100% | LTE |
| Branch-Router | Internet-Router | 10.0.0.1 | ✅ 100% | Via ISP1 |
| Branch-Router | Internet (8.8.8.8) | 8.8.8.8 | ✅ 100% | Via Fiber |
| Client-PC | Internet (8.8.8.8) | 8.8.8.8 | ✅ 100% | Via Fiber |

### 5.2 Failover (Fiber DOWN — f0/0 shutdown)

| Source | Destination | IP | Result | Path |
|---|---|---|---|---|
| Branch-Router | Internet (8.8.8.8) | 8.8.8.8 | ✅ 100% | Via LTE |
| Client-PC | Internet (8.8.8.8) | 8.8.8.8 | ✅ 100% | Via LTE |
| Branch-Router | ISP1-Router | 203.0.113.1 | ❌ 0% | Link down (expected) |

---

## 6. Failover Test Results

### Test 1: Normal Operation
- Routing table shows: `S* 0.0.0.0/0 [1/0] via 203.0.113.1` (Primary Fiber active)
- Track 1: **Reachability is Up**
- Client-PC ping to 8.8.8.8: **100% success**

### Test 2: Fiber Failure Simulation
- Command: `interface f0/0` → `shutdown` on Branch-Router
- Routing table changes to: `S* 0.0.0.0/0 [10/0] via 198.51.100.1` (LTE backup active)
- Failover time: **~15-30 seconds** (IP SLA frequency = 5 seconds)
- Client-PC ping to 8.8.8.8: **100% success via LTE** ✅
- **Requirement met: failover within 60 seconds** ✅

### Test 3: Fiber Recovery
- Command: `interface f0/0` → `no shutdown` on Branch-Router
- Track 1 returns to **Reachability is Up**
- Routing table reverts to: `S* 0.0.0.0/0 [1/0] via 203.0.113.1`
- **Automatic recovery confirmed** ✅

---

## 7. IP SLA Configuration Summary

```
ip sla 1
 icmp-echo 8.8.8.8 source-ip 203.0.113.2
 frequency 5
ip sla schedule 1 life forever start-time now
track 1 rtr 1 reachability
```

- IP SLA probes 8.8.8.8 every 5 seconds via Fiber interface
- Track 1 monitors probe results
- Primary route tied to Track 1 — removed when Track goes DOWN
- Floating static route (AD=10) activates automatically

---

## 8. Issues Encountered & Resolved

| Issue | Cause | Resolution |
|---|---|---|
| IP SLA Track always DOWN | Internet-Router had no return route to Branch subnets | Added static routes on Internet-Router |
| Ping failed during failover from Client-PC | Internet-Router was replying via ISP1 (broken path) | Forced Internet-Router to reply via ISP2 only |
| Branch-Router only had 2 ports | c3725 default has 1 slot with 2 ports | Added NM-1FE-TX module in Slot 1 for f1/0 |
| Configs lost after accidental reload | Devices reloaded before `wr` was run | Re-entered all configs; now saving after every change |

---

## 9. Running Config Summary

### Branch-Router (key config)
```
interface FastEthernet0/0
 ip address 203.0.113.2 255.255.255.252
interface FastEthernet0/1
 ip address 198.51.100.2 255.255.255.252
interface FastEthernet1/0
 ip address 192.168.10.1 255.255.255.0
ip route 0.0.0.0 0.0.0.0 203.0.113.1 track 1
ip route 0.0.0.0 0.0.0.0 198.51.100.1 10
ip sla 1
 icmp-echo 8.8.8.8 source-ip 203.0.113.2
 frequency 5
ip sla schedule 1 life forever start-time now
track 1 rtr 1 reachability
```

---


*Report prepared by Hasnaat Malik (Project Manager)*  
*CCP-CNS2026 | Problem 5: Retail Network Failover | Week 2*
