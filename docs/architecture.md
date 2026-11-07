# Architecture

## Requirements (summarized from the assignment)

The Part A assignment asked for:

1. Department separation with VLANs (Site A: Sales VLAN 10, HR VLAN 20; Site B: IT VLAN 30, Finance VLAN 40).
2. Automatic addressing with DHCP.
3. Inter-VLAN routing within each office (router-on-a-stick).
4. Encrypted communication between offices through a site-to-site IPsec VPN.
5. Internet traffic left unencrypted; inter-office traffic encrypted.
6. Room for future departments (a VLAN 50 Engineering expansion question).
7. Cisco ASA 5506-X firewalls at each site, with the VPN built between the firewalls. Suggested outside addresses were 209.165.200.1 (Site A) and 209.165.200.2 (Site B).

## What was built

| Requirement | Built design | Evidence |
|---|---|---|
| VLAN segmentation | VLAN 10/20 on Switch1 and VLAN 30/40 on Switch2, access ports Fa0/1–10 and Fa0/11–20 | `switch1-site-a-show-vlan-brief.png`, `switch2-site-b-show-vlan-brief.png` |
| DHCP | Pools on Router1 and Router2; clients get gateway `.1` and DNS 8.8.8.8 | `router1-dhcp-bindings-and-interfaces.png`, `router2-…`, `pc7-…ipconfig.png`, `pc3-…ipconfig.png` |
| Inter-VLAN routing | Router-on-a-stick sub-interfaces `G0/0/0.10/.20` (Router1) and `.30/.40` (Router2) | `show ip interface brief` captures |
| Site-to-site VPN | IPsec between Router1 (209.165.200.226) and Router2 (209.165.200.229), crypto map `CMAP` on G0/0/1 | `router*-show-crypto-isakmp-sa.png`, `router*-ipsec-sa-*.png` |
| Internet unencrypted | NAT ACLs permit local subnets to `any` after denying inter-site pairs | `router*-access-lists-nat-exemption.png` |
| Firewalls (ASA 5506-X) | **Not implemented.** VPN terminates on ISR 4331 routers; no ASA in topology | `topology-two-site-ipsec-packet-tracer.png`; Part A report p. 1 ("Both sites use Cisco ISR 4331 routers") |
| Scalability (VLAN 50 Engineering expansion, assignment Part 8) | Not built, and not addressed in the submitted Part A report (the report ends at Part 7) | Part A report pp. 1–4 |

## Topology

```
                         ┌───────────────────────┐
                         │ Internet Router (4331)│
                         └──────┬─────────┬──────┘
             209.165.200.226    │         │    209.165.200.229
                    ┌───────────┴──┐   ┌──┴───────────┐
                    │ Router1 4331 │◄═►│ Router2 4331 │   IPsec, crypto map CMAP
                    │ DHCP, NAT    │   │ DHCP, NAT    │
                    └──────┬───────┘   └──────┬───────┘
                   G0/0/0 trunk (sub-ifs)   G0/0/0 trunk (sub-ifs)
                    ┌──────┴───────┐   ┌──────┴───────┐
                    │   Switch1    │   │   Switch2    │
                    └──┬────────┬──┘   └──┬────────┬──┘
                 VLAN 10      VLAN 20   VLAN 30      VLAN 40
                 Sales        HR        IT           Finance
                 .10.0/24     .20.0/24  .30.0/24     .40.0/24
```

The Internet Router's own interface addresses were not captured. The WAN addresses differ from the assignment's suggested 209.165.200.1/.2, which the assignment assigned to firewall outside interfaces that were not built.

## Addressing (observed)

| Device | Interface | Address | Source |
|---|---|---|---|
| Router1 | G0/0/0.10 | 192.168.10.1 | `show ip interface brief` |
| Router1 | G0/0/0.20 | 192.168.20.1 | same |
| Router1 | G0/0/1 | 209.165.200.226 | same |
| Router2 | G0/0/0.30 | 192.168.30.1 | same |
| Router2 | G0/0/0.40 | 192.168.40.1 | same |
| Router2 | G0/0/1 | 209.165.200.229 | same |
| PC7 (HR) | DHCP | 192.168.20.3 / gw 192.168.20.1 / DNS 8.8.8.8 | `pc7-hr-vlan20-dhcp-ipconfig.png` |
| PC3 (IT) | DHCP | 192.168.30.3 / gw 192.168.30.1 / DNS 8.8.8.8 | `pc3-it-vlan30-dhcp-ipconfig.png` |

## Design observations

- **The tunnel permits every subnet pair.** ACL `100` includes Sales↔IT, Sales↔Finance, HR↔IT, and HR↔Finance. This is the condition the Part B report later names as a weakness.
- **VLAN 10 also exists on Switch2** with no assigned ports (`VLAN0010`). It does not affect the design but is unexplained.
- **Two NAT-related extended ACLs exist on Router1** (`NAT_EXEMPT` and `NAT_ACL`) with overlapping entries; `NAT_EXEMPT` has sequence numbers 3 and 4 inserted ahead of 10. Without the running configuration it is not possible to confirm which list is bound to the NAT statement. Hit counters appear on both.
- **No firewall layer.** Because the ASA requirement was not met, security between the inside networks and the Internet relies on router NAT only.
