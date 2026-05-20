# Device Configurations — Problem 5: Retail Network Failover
## CCP-CNS2026 | RetailNow-Team-05

---

## Files in This Folder

| File | Device | Role |
|---|---|---|
| `Internet-Router.txt` | Internet-Router | Simulated Internet (Lo0: 8.8.8.8) |
| `ISP1-Router.txt` | ISP1-Router | Fiber ISP — Primary WAN |
| `ISP2-Router.txt` | ISP2-Router | LTE ISP — Backup WAN |
| `Branch-Router.txt` | Branch-Router | Store router with dual WAN + IP SLA |
| `Client-PC.txt` | Client-PC (VPCS) | Customer PC on LAN |

---

## How to Restore Configs in GNS3

1. Start all devices in GNS3
2. Open console for each router
3. Type `en` then `conf t`
4. Paste the config (excluding the `!` comment lines at top and `end`)
5. Type `end` then `wr`

---

## Key IP Addresses

| Device | Interface | IP |
|---|---|---|
| Internet-Router | f0/0 | 10.0.0.1/30 |
| Internet-Router | f0/1 | 10.0.0.5/30 |
| Internet-Router | Lo0 | 8.8.8.8/32 |
| ISP1-Router | f0/0 | 203.0.113.1/30 |
| ISP1-Router | f0/1 | 10.0.0.2/30 |
| ISP2-Router | f0/0 | 198.51.100.1/30 |
| ISP2-Router | f0/1 | 10.0.0.6/30 |
| Branch-Router | f0/0 | 203.0.113.2/30 (Fiber) |
| Branch-Router | f0/1 | 198.51.100.2/30 (LTE) |
| Branch-Router | f1/0 | 192.168.10.1/24 (LAN) |
| Client-PC | e0 | 192.168.10.10/24 |

---

## Failover Logic

```
Normal:   Client-PC → Branch-Router → ISP1 → Internet-Router → 8.8.8.8
Failover: Client-PC → Branch-Router → ISP2 → Internet-Router → 8.8.8.8
```

Trigger: IP SLA on Branch-Router pings 8.8.8.8 every 5 seconds via f0/0.
If probe fails → Track 1 DOWN → primary route removed → LTE route activates.
