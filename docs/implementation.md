# Implementation

No configuration files were supplied. Everything below is **transcribed from screenshots** of `show` command output. It is not the original configuration. Each block names its source file, and anything that could not be read or was not captured is marked.

## 1. VLANs (switches)

Source: `assets/screenshots/switch1-site-a-show-vlan-brief.png`

```text
Switch#show vlan brief            (transcription, Switch1 / Site A)
VLAN Name     Status  Ports
1    default  active  Fa0/21, Fa0/22, Fa0/23, Fa0/24, Gig0/2
10   Sales    active  Fa0/1 – Fa0/10
20   HR       active  Fa0/11 – Fa0/20
```

Source: `assets/screenshots/switch2-site-b-show-vlan-brief.png`

```text
Switch#show vlan brief            (transcription, Switch2 / Site B)
VLAN Name      Status  Ports
1    default   active  Fa0/21 – Fa0/24, Gig0/2
10   VLAN0010  active  (no ports)
30   IT        active  Fa0/1 – Fa0/10
40   Finance   active  Fa0/11 – Fa0/20
```

Gig0/1 does not appear under any VLAN on either switch, which is consistent with it being the trunk to the router. `show interfaces trunk` was not captured, so this is inferred, not shown.

## 2. Router sub-interfaces and DHCP

Source: `assets/screenshots/router1-dhcp-bindings-and-interfaces.png`

```text
Router#show ip dhcp binding       (transcription, Router1)
IP address     Hardware address   Type
192.168.10.3   00E0.F787.7708     Automatic
192.168.10.2   000C.85E8.DA5E     Automatic
192.168.20.3   0001.97B9.977D     Automatic
192.168.20.2   00E0.F995.3509     Automatic

Router#show ip interface brief
GigabitEthernet0/0/0      unassigned       up    up
GigabitEthernet0/0/0.10   192.168.10.1     up    up
GigabitEthernet0/0/0.20   192.168.20.1     up    up
GigabitEthernet0/0/1      209.165.200.226  up    up
GigabitEthernet0/0/2      unassigned       administratively down
Vlan1                     unassigned       administratively down
```

Source: `assets/screenshots/router2-dhcp-bindings-and-interfaces.png`

```text
Router#show ip dhcp binding       (transcription, Router2)
192.168.30.3   0006.2A87.83D3
192.168.30.2   00E0.B0CE.CC68
192.168.40.2   0002.4A01.9346
192.168.40.3   00E0.B09D.BDEB

Router#show ip interface brief
GigabitEthernet0/0/0.30   192.168.30.1     up    up
GigabitEthernet0/0/0.40   192.168.40.1     up    up
GigabitEthernet0/0/1      209.165.200.229  up    up
```

The Router2 binding list is partly scrolled in the capture; the four entries above are the ones visible across the two Router2 captures. DHCP pool definitions, excluded addresses, and `encapsulation dot1Q` lines are not visible in any screenshot.

## 3. Access lists (crypto ACL and NAT exemption)

Source: `assets/screenshots/router1-access-lists-nat-exemption.png`

```text
Router#show access-lists          (transcription, Router1)
Standard IP access list 1
    10 permit 192.168.10.0 0.0.0.255 (8 match(es))
    20 permit 192.168.20.0 0.0.0.255
Extended IP access list 100
    10 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 (5 match(es))
    20 permit ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255
    30 permit ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255 (3 match(es))
    40 permit ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255
Extended IP access list NAT_EXEMPT
    3 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255 (12 match(es))
    4 deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255 (4 match(es))
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 (17 match(es))
    20 deny ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255
    30 permit ip 192.168.10.0 0.0.0.255 any
Extended IP access list NAT_ACL
    10 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 (4 match(es))
    20 deny ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255
    30 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255 (12 match(es))
    40 deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255 (4 match(es))
    50 permit ip 192.168.10.0 0.0.0.255 any
    60 permit ip 192.168.20.0 0.0.0.255 any (16 match(es))
```

Router2 (`router2-access-lists-nat-exemption.png`) holds the mirror image: ACL `100` permits 30/40 → 10/20, and both `NAT_EXEMPT` and `NAT_ACL` deny those pairs and then permit 192.168.30.0/24 and 192.168.40.0/24 to `any`. Router2 has no standard ACL 1 in the capture.

ACL `100` is the interesting-traffic list for crypto map `CMAP` (the map name appears in `show crypto ipsec sa`). The `crypto map` match statement itself and the `ip nat inside source` statement are not visible, so which NAT ACL is in use is not confirmed.

## 4. IPsec

Sources: `router1-show-crypto-isakmp-sa.png`, `router2-show-crypto-isakmp-sa.png`

```text
Router1#show crypto isakmp sa     (transcription)
dst              src              state     conn-id slot status
209.165.200.229  209.165.200.226  QM_IDLE   1084    0    ACTIVE

Router2#show crypto isakmp sa     (transcription)
209.165.200.226  209.165.200.229  QM_IDLE   1014    0    ACTIVE
```

Sources: `router1-ipsec-sa-hr-to-finance.png`, `router1-ipsec-sa-hr-to-it-send-error.png`, `router2-ipsec-sa-it-to-sales.png`

| Router | Local → remote ident | Encaps | Decaps | Send err | Other visible fields |
|---|---|---|---|---|---|
| Router1 | 192.168.20.0/24 → 192.168.40.0/24 | 4 | 0 | 0 | crypto map CMAP, conn id 2006, Status ACTIVE |
| Router1 | 192.168.20.0/24 → 192.168.30.0/24 | 3 | 0 | 1 | transform `esp-aes esp-sha-hmac` |
| Router2 | 192.168.30.0/24 → 192.168.10.0/24 | 0 | 7 | — | crypto map CMAP, local addr 209.165.200.229 |
| Router1 (report p. 6) | 192.168.10.0/24 → 192.168.40.0/24 | 3 | 3 | 1 | from image embedded in Part B report |
| Router2 (report p. 2) | 192.168.40.0/24 → 192.168.10.0/24 | 3 | 3 | — | from image embedded in Part B report |

**Not captured:** ISAKMP policy (encryption, hash, authentication, DH group, lifetime), the pre-shared key, the transform-set definition, and `crypto map` configuration lines. The Part A report states AES-256 with SHA (p. 1). The screenshots show `esp-aes` with no key length, so AES-256 cannot be confirmed from the evidence.

## 5. Not implemented

- Cisco ASA 5506-X firewalls, ASA NAT, and ASA access policy.
- The VLAN 50 Engineering expansion.
