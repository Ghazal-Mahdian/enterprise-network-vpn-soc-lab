# Validation

Status key:

- **Demonstrated**: visible in a supplied screenshot.
- **Partial**: shown, but with failures, missing steps, or only one direction.
- **Reported**: stated in the write-up; no supporting screenshot was supplied.
- **Not shown**: required or proposed; no evidence of completion.
- **Not implemented**: evidence shows a different approach was used.

## Part A: network

| Test | Expected outcome | Observed outcome | Evidence | Status |
|---|---|---|---|---|
| VLANs on Site A switch | VLAN 10 Sales and 20 HR with access ports | VLAN 10 Fa0/1–10, VLAN 20 Fa0/11–20 | `switch1-site-a-show-vlan-brief.png` | Demonstrated |
| VLANs on Site B switch | VLAN 30 IT and 40 Finance with access ports | VLAN 30 Fa0/1–10, VLAN 40 Fa0/11–20; extra empty VLAN 10 | `switch2-site-b-show-vlan-brief.png` | Demonstrated |
| Trunk to router | Uplink carries tagged VLANs | Uplink not listed as an access port; `show interfaces trunk` not captured | switch captures | Not shown |
| DHCP Site A | Clients in VLAN 10/20 receive leases | 4 bindings (10.2, 10.3, 20.2, 20.3); PC7 has 192.168.20.3, gw .1, DNS 8.8.8.8 | `router1-dhcp-bindings-and-interfaces.png`, `pc7-hr-vlan20-dhcp-ipconfig.png` | Demonstrated |
| DHCP Site B | Clients in VLAN 30/40 receive leases | 4 bindings (30.2, 30.3, 40.2, 40.3); PC3 has 192.168.30.3, gw .1 | `router2-dhcp-bindings-and-interfaces.png`, `pc3-it-vlan30-dhcp-ipconfig.png` | Demonstrated |
| Inter-VLAN routing (Site A) | HR host reaches Sales host | PC7 → 192.168.10.2: 4/4, TTL 127 | `pc7-ping-inter-vlan-and-cross-site.png` | Demonstrated |
| Inter-VLAN routing (Site B) | IT host reaches Finance host | No same-site test captured at Site B | — | Not shown |
| IKE phase 1 | ISAKMP SA up between WAN peers | QM_IDLE / ACTIVE on Router1 (conn 1084) and Router2 (conn 1014) | `router1-show-crypto-isakmp-sa.png`, `router2-show-crypto-isakmp-sa.png` | Demonstrated |
| IPsec phase 2 | SAs with non-zero encrypt/decrypt counters | Router1 HR→Finance 4 encaps / 0 decaps; HR→IT 3 / 0 with 1 send error; Router2 IT→Sales 0 encaps / 7 decaps; report images show 3 / 3 for Sales↔Finance | `router1-ipsec-sa-*.png`, `router2-ipsec-sa-it-to-sales.png`, Part B report pp. 2, 6 | Demonstrated (counters one-sided in some captures) |
| Cross-site reachability A → B | Site A host reaches Site B hosts | PC7 → 192.168.40.2: 4/4, TTL 126 | `pc7-ping-inter-vlan-and-cross-site.png` | Demonstrated |
| Cross-site reachability B → A | Site B host reaches Site A host | PC8 → 192.168.20.2: 4/4, TTL 126 | `pc8-ping-cross-site-to-hr.png` | Demonstrated |
| Cross-site first attempts | Pings succeed | PC5 → 192.168.30.2: 2/4 (50% loss); → 192.168.40.2: 3/4 (25% loss). Losses are on the first packets; cause not shown | `pc5-ping-cross-site-initial-loss.png` | Partial |
| Simulation PDU test | PC1 → PC3 succeeds | PDU list: PC1 → PC3 **Failed**, PC1 → Router1 Successful | `topology-two-site-ipsec-packet-tracer.png` | Partial (failed attempt recorded) |
| Internet traffic not encrypted | NAT for Internet-bound traffic; VPN only for inter-site | NAT ACLs deny inter-site pairs and permit local → any; no Internet destination tested | `router1-access-lists-nat-exemption.png`, `router2-access-lists-nat-exemption.png` | Partial (configuration only) |
| VPN encryption strength | AES-256 / SHA | `esp-aes esp-sha-hmac`; key length not shown | `router1-ipsec-sa-hr-to-it-send-error.png`; Part A report p. 1 | Reported (AES-256 not confirmed) |
| Firewall protection | ASA 5506-X inside/outside, NAT, VPN termination | No ASA in topology; VPN terminates on routers | `topology-two-site-ipsec-packet-tracer.png` | Not implemented |
| Expansion (VLAN 50) | Design for new department | Not addressed | — | Not shown |

## Part B: investigation deliverables

| Test | Expected outcome | Observed outcome | Evidence | Status |
|---|---|---|---|---|
| VLAN configuration screenshot | `show vlan brief` | Provided for both switches | Part B report p. 5 | Demonstrated |
| DHCP bindings screenshot | `show ip dhcp binding` | Provided for Router1 | Part B report pp. 2, 6 | Demonstrated |
| VPN status screenshot | `show crypto isakmp sa` / `ipsec sa` | `show crypto ipsec sa` on both routers | Part B report pp. 2, 6 | Demonstrated |
| Map suspect IP to MAC | 192.168.10.45 → MAC | 192.168.10.45 does not exist in the lab; the report uses MAC 00E0.F787.7708, which belongs to a lab host (192.168.10.3 in one capture, 192.168.10.2 in the report image) | `router1-dhcp-bindings-and-interfaces.png`; Part B report pp. 2, 6, 7 | Partial (stand-in host, not labelled as such) |
| Map MAC to switch port | MAC found on Switch1 Fa0/5 | MAC-table image is from a switch with VLANs 1/30/40 (Site B) and lists only 0060.7051.7901 | `report-b-mac-address-table-excerpt.png`; Part B report pp. 3, 7 | Reported (not supported by evidence) |
| Containment: shut suspect port | Port disabled | Proposed only | Part B report p. 3 | Not shown |
| Sales → Finance ACL | Traffic denied | Proposed only; ACL 100 still permits 10.0 ↔ 40.0 | Part B report p. 8; `router1-access-lists-nat-exemption.png` | Not shown |
| IDS, egress filtering, VPN syslog | Deployed | Proposed only | Part B report p. 8 | Not shown |
