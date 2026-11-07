# Part A: Network Design and Case-Study Answers

*Author: Ghazal Mahdian (individual work). CYB555 midterm, Part A.*

> **About this version.** This is a web version of the submitted Part A report. The instructor's case-study questions are **paraphrased** rather than reproduced, and the answers keep the original wording except for the edits listed in [Editorial changes](#editorial-changes). `[p. N]` markers give the page numbers of the original PDF, which is kept privately and not published.

---

## Network Design Explanation `[p. 1]`

### 1. Physical topology and infrastructure

The network is made up of two geographically independent sites: Site A (San Diego) and Site B (Phoenix).

- Both sites use Cisco ISR 4331 routers for their primary gateways.
- In the center, an Internet Router acts as a Service Provider, routing public IP traffic (the 209.165.x.x range) between branches.
- Each site has a Layer 2 switch that connects different departments (represented by PCs).

### 2. Segmentation and addressing (VLANs/DHCP)

To ensure security and organization, each site is separated into VLANs.

- To prevent illegal access and reduce network congestion, departments are separated by broadcast domains.
- The Router-on-a-Stick uses sub-interfaces (e.g. G0/0/0.10) as the default gateway for each VLAN. DHCP Pools were set up on the routers to automatically give IP addresses to PCs, ensuring scalability.

### 3. Security and secure connectivity (VPN/NAT)

The primary goal of this design was to secure the communication path between Sites A and B.

- Private IP addresses (192.168.x.x) cannot be sent directly across the public "Internet Router" due to the requirement for public traffic.
- NAT and NAT Exemption: Network Address Translation (NAT) enables internal PCs to access the internet. However, I enabled NAT Exemption (NAT Bypass) to ensure that traffic destined for the other office remains in its original private condition, allowing the VPN to "catch" and encrypt it.
- Established a secure IPsec Site-to-Site VPN tunnel with AES-256 encryption and SHA hashing. Phase 1 (ISAKMP) authenticates routers with a Pre-Shared Key (PSK). In Phase 2 (IPsec), departmental data is encapsulated and encrypted to prevent interception by ISPs.

### 4. Design summary `[p. 2]`

| Feature | Implementation | Purpose |
|---|---|---|
| Routing | Static Routes | Directing traffic to the ISP and specific VPN peers. |
| VLANs | 802.1Q Tagging | Departmental isolation and security. |
| VPN | IPsec Tunnel | Secure, encrypted communication over the public web. |
| NAT | PAT (Overload) | Allowing internal hosts to share a single public IP. |
| Exemption | Extended ACLs | Preventing VPN traffic from being NAT'ed. |

---

## Case-Study Answers

### Part 1: Network segmentation `[p. 2]`

**Value of VLANs in an organization.** They enable you to logically organize users by department, regardless of geographical location, lowering the number of broadcast domains while boosting security.

**Risks of a single flat network.** Excessive broadcast traffic (ARP storms) can decrease network speed, and any user can "sniff" or access traffic from any other department (e.g., Sales viewing Finance data).

**Device responsible for VLAN membership.** A Layer 2 Switch assigns membership.

**Access vs. trunk ports.** Access ports link to a single VLAN (e.g., PCs), while trunk ports use tagging to connect many VLANs (e.g., switches or routers).

### Part 2: DHCP `[pp. 2–3]`

**Advantages over static addressing.** Reduces manual configuration errors, prevents IP conflicts, and enables centralized management of IP addresses.

**Typical parameters a DHCP server hands out.** IP address, subnet mask, default gateway, and DNS server.

**Reason to keep gateway addresses out of the pool.** To prevent IP address clashes. If the DHCP server allocates the Gateway's IP address to a PC, the entire subnet loses internet connectivity.

**Effect of two uncoordinated DHCP servers.** Two uncoordinated DHCP servers might generate "Rogue DHCP" difficulties, resulting in improper IP settings, inconsistent connectivity, or network loss. `[p. 3]`

### Part 3: Inter-VLAN routing `[p. 3]`

**Why router-on-a-stick fits this design.** Using virtual subinterfaces, it is possible to route traffic between numerous VLANs from a single physical router interface.

**Role of `encapsulation dot1Q`.** It instructs the router to search for and associate a specific VLAN tag (802.1Q) with the subinterface.

**Need for subinterfaces.** A physical interface can only connect to one network. Subinterfaces enable the router to support several "virtual" gateways (one for each VLAN) over a single cable.

**How the router identifies a frame's VLAN.** It examines the 802.1Q tag injected into the Ethernet frame header by the switch.

### Part 4: Firewall security `[p. 3]`

**Firewall's function.** A firewall monitors and controls network traffic according to established security rules (Access Control Lists).

**Inside vs. outside interfaces.** Inside refers to a trusted private network, while Outside refers to an untrusted public network, such as the Internet.

**Purpose of NAT at the edge.** To mask private internal IP addresses (RFC 1918) behind a single public-facing IP address, so preserving IP space and adding a layer of anonymity.

**Why VPN traffic is exempted from NAT.** VPNs rely on precise source/destination IP matching. If the source IP is changed by NAT, the VPN "interesting traffic" ACL will no longer match, causing the tunnel to fail to create or pass data.

### Part 5: Site-to-site VPN `[pp. 3–4]`

**Benefit over unencrypted Internet traffic.** A VPN ensures confidentiality (encryption) and integrity (hashing), preventing critical corporate data from being read or altered if intercepted via the public internet.

**The two IPsec phases.** `[p. 4]` Phase 1 (ISAKMP/IKE) establishes a secure management channel (the "handshake"). Phase 2 (IPsec) involves negotiating encryption parameters for the data tunnel.

**Role of the pre-shared key.** A Pre-Shared Key (PSK) is a shared secret password that authenticates the identity of two VPN endpoints.

**What brings the tunnel up.** Interesting traffic refers to packets that match allowed statements in the VPN Access Control List.

### Part 6: Security analysis `[p. 4]`

**Remaining risks.** Insider attacks, unpatched software, weak passwords, and physical access to network gear.

**VLANs as a security control.** VLANs provide security control by implementing the Principle of Least Privilege at the network layer, allowing users to access just the resources necessary for their job roles.

**Further technologies.**
- IDS/IPS, which detect and block active assaults.
- MFA (Multi-Factor Authentication) for admin access to routers and firewalls.
- Endpoint Security: Antivirus/EDR for PCs.

**Should HR and Finance be able to talk to each other?** Generally, no (or very few). This is known as "Zero Trust" architecture; segmenting them keeps a breach in one area from spreading to the other.

### Part 7: Troubleshooting `[p. 4]`

**Scenario 1: Sales cannot reach HR.**
- Commands: `show vlan brief` and `show interface trunk`.
- Likely issue: the VLAN may not be created on the switch, or the port may be assigned to the incorrect VLAN.

**Scenario 2: Site A cannot reach Site B.**
- Commands: `show crypto isakmp sa` (Phase 1) and `show crypto ipsec sa` (Phase 2).
- Likely issues: mismatched pre-shared keys (Phase 1 fails) and NAT overlap (Phase 2 fails / 0 packets encapsulated).

**Scenario 3: Hosts not receiving addresses.**
- Commands: `show ip dhcp binding` and `show ip dhcp pool`.
- Likely issue: missing IP helper address on the router subinterface when the DHCP server is in a separate VLAN.

---

## Editorial changes

Changes from the submitted PDF:

1. The instructor's question wording was replaced by short paraphrased prompts (bold labels).
2. "we enabled NAT Exemption" was changed to "I enabled NAT Exemption", because this was individual work.
3. In Scenario 2, "Issues seen in your lab include…" was changed to "Likely issues:", matching the other scenarios. No screenshot documents these failures in this lab.
4. In Scenario 3, "reveal IP DHCP binding and pool" was written out as the actual IOS commands. In Scenario 2, "Display crypto isakmp sa" was written as `show crypto isakmp sa`.
5. PDF text-extraction artefacts were fixed ("traKic" → "traffic", "diKerent" → "different", "oKice" → "office").
6. The submitted report does not answer Part 8 (the VLAN 50 expansion). Nothing was added.

**Evidence note:** the screenshots show `esp-aes` with no key length, so the AES-256 statement in section 3 is not confirmed by the evidence (see [validation](../docs/validation.md)).
