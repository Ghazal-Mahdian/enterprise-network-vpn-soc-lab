# Evidence Index

Maps each claim in this repository to its source. Original filenames are the screenshot timestamps from the course archive (`InfoAssurance555-Midterm.zip`), recorded here so the source can be traced. All original screenshots are dated 29 March 2026.

## Published screenshots

| Published file | Original file | Shows | Used for | Redaction |
|---|---|---|---|---|
| `topology-two-site-ipsec-packet-tracer.png` | Screenshot 10.24.46 PM | Full topology; PDU list (PC1→PC3 Failed, PC1→Router1 Successful) | Device models, layout, failed PDU | macOS file path in title bar |
| `switch1-site-a-show-vlan-brief.png` | 10.49.09 PM | Switch1 VLAN 10 Sales / 20 HR | VLANs Site A | — |
| `switch2-site-b-show-vlan-brief.png` | 10.49.35 PM | Switch2 VLAN 30 IT / 40 Finance / empty VLAN 10 | VLANs Site B | — |
| `router1-dhcp-bindings-and-interfaces.png` | 10.51.54 PM | Router1 DHCP bindings + `show ip interface brief` | DHCP, sub-interfaces, WAN IP | — |
| `router2-dhcp-bindings-and-interfaces.png` | 10.51.37 PM | Router2 DHCP bindings (partly scrolled) + interfaces | DHCP, sub-interfaces, WAN IP | — |
| `pc7-hr-vlan20-dhcp-ipconfig.png` | 10.33.39 PM | PC7 DHCP lease 192.168.20.3 | DHCP client | — |
| `pc3-it-vlan30-dhcp-ipconfig.png` | 10.33.57 PM | PC3 DHCP lease 192.168.30.3 | DHCP client | — |
| `router1-show-crypto-isakmp-sa.png` | 10.52.48 PM | ISAKMP SA QM_IDLE ACTIVE | Phase 1 | — |
| `router2-show-crypto-isakmp-sa.png` | 10.53.05 PM | ISAKMP SA QM_IDLE ACTIVE + interfaces | Phase 1 | — |
| `router1-ipsec-sa-hr-to-finance.png` | 10.26.20 PM | SA 20.0→40.0, 4 encaps, CMAP | Phase 2 | — |
| `router1-ipsec-sa-hr-to-it-send-error.png` | 10.26.39 PM | SA 20.0→30.0, 3 encaps, 1 send error, transform | Phase 2, transform | — |
| `router2-ipsec-sa-it-to-sales.png` | 10.30.41 PM | SA 30.0→10.0, 7 decaps | Phase 2 | — |
| `router1-access-lists-nat-exemption.png` | 10.29.28 PM | ACL 1, 100, NAT_EXEMPT, NAT_ACL | Crypto ACL, NAT exemption | — |
| `router2-access-lists-nat-exemption.png` | 10.31.25 PM | Mirror ACLs on Router2 | Crypto ACL, NAT exemption | — |
| `pc7-ping-inter-vlan-and-cross-site.png` | 10.33.06 PM | PC7 → 10.2 (TTL 127), → 40.2 (TTL 126) | Inter-VLAN + VPN reachability | — |
| `pc8-ping-cross-site-to-hr.png` | 10.32.00 PM | PC8 → 20.2, 4/4 | Reverse-direction reachability | — |
| `pc5-ping-cross-site-initial-loss.png` | 10.25.49 PM | PC5 → 30.2 (50% loss), → 40.2 (25% loss) | Partial result | — |
| `report-b-mac-address-table-excerpt.png` | Image extracted from Part B report p. 3 | `show mac-address-table` (Site B switch) | Evidence-chain discrepancy | — |
| `report-b-router2-ipsec-sa-finance-to-sales.png` | Image from Part B report p. 2 | Router2 SA 40.0→10.0, 3/3 | Phase 2 (report) | — |
| `report-b-router1-dhcp-binding-p2.png` | Image from Part B report p. 2 | Router1 DHCP bindings | DHCP evidence chain | — |
| `report-b-router1-dhcp-binding-p6.png` | Image from Part B report p. 6 | Router1 DHCP bindings (10.2 ↔ 00E0.F787.7708) | DHCP evidence chain | — |
| `report-b-router1-ipsec-sa-sales-to-finance.png` | Image from Part B report p. 6 | Router1 SA 10.0→40.0, 3/3, 1 send error | Phase 2 (report) | — |

## Original screenshots not published

| Original file | Reason |
|---|---|
| Screenshot 10.29.19 PM | 24 × 30 px fragment with no content |
| Screenshot 10.47.30 PM | Near-duplicate of the 10.24.46 topology without the PDU list; also shows the local file path |
| Screenshot 10.50.28 PM | Router1 DHCP bindings; superseded by 10.51.54, which adds `show ip interface brief` |
| Screenshot 10.50.54 PM | Router2 DHCP bindings; content covered by 10.51.37 and the evidence index |
| Screenshot 10.29.56 PM | Router1 ACLs + ISAKMP SA; covered by 10.29.28 and 10.52.48 |
| Screenshot 10.30.57 PM | Router2 SA 30.0→20.0 (2 encaps, 0 decaps); adds little beyond the published SAs |

## Reports

| File | Pages | Content | Used for |
|---|---|---|---|
| `reports/part-a-network-design-report.md` | 4 (original PDF) | Design explanation (p. 1), summary table (p. 2), case-study answers Parts 1–7 (pp. 2–4) | Design intent, AES-256 claim (p. 1), ISR 4331 (p. 1), NAT exemption reasoning (p. 3), troubleshooting (p. 4) |
| `reports/part-b-soc-investigation-report.md` | 8 (original PDF) | Question answers (pp. 1–4), deliverable screenshots (pp. 2, 3, 5, 6), SOC report and attack narrative (p. 7), recommendations (p. 8) | Investigation narrative, evidence chain, recommendations |

The original PDFs repeat the instructor's question wording, so they are kept privately. The published Markdown versions paraphrase the questions, keep the answers, and carry `[p. N]` markers so that page citations in these docs still point to the right place. Each version lists its edits at the end.

## Simulation file

| File | Original | Verified? |
|---|---|---|
| Not published | `555-midterm-Third-draft.pkt` | Contains the VPN pre-shared key and device passwords; see `labs/README.md` |

## Specific claim → source

| Claim | Source |
|---|---|
| ISR 4331 routers, no ASA | `topology-two-site-ipsec-packet-tracer.png`; Part A report p. 1 |
| WAN 209.165.200.226 / .229 | `router1-dhcp-bindings-and-interfaces.png`, `router2-dhcp-bindings-and-interfaces.png` |
| Crypto map `CMAP` | `router1-ipsec-sa-hr-to-finance.png`, `router2-ipsec-sa-it-to-sales.png` |
| Transform `esp-aes esp-sha-hmac` | `router1-ipsec-sa-hr-to-it-send-error.png` |
| AES-256 (claim only) | Part A report p. 1 |
| Crypto ACL permits all inter-site pairs | `router1-access-lists-nat-exemption.png` |
| "VPN … allow all communication" | Part B report p. 7 |
| MAC 00E0.F787.7708 ↔ 192.168.10.3 | `router1-dhcp-bindings-and-interfaces.png` |
| MAC 00E0.F787.7708 ↔ 192.168.10.2 | Part B report p. 6 (embedded image) |
| "Traced … to Switch 1, Port Fa0/5" (unsupported) | Part B report p. 7 vs. p. 3 image |
