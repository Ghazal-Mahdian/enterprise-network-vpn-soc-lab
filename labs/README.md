# Packet Tracer file (not published)

The simulation file for this lab (`555-midterm-Third-draft.pkt`, about 83 KB, saved with Cisco Packet Tracer 9.0.0) is **not included** in this repository. It contains the IPsec pre-shared key and device passwords.

All configuration evidence in this repository comes from screenshots (see [docs/evidence-index.md](../docs/evidence-index.md)). The file is available to reviewers on request.

## Publishing a sanitized copy later

If you want to add the file back:

1. Open the `.pkt` in Packet Tracer and **Save As** a new file (keep the original private).
2. On Router1 and Router2, replace the ISAKMP pre-shared key with the same placeholder on both routers. Example: `crypto isakmp key LAB-PSK-REDACTED address <peer>`. Also replace enable secrets and console/VTY passwords.
3. Clear the Packet Tracer user profile (Options → User Profile).
4. Re-test before saving: `show crypto isakmp sa` should show QM_IDLE on both routers, and PC7 should still ping 192.168.40.2.
5. Save, then add the file as `labs/brightfuture-two-site-vpn.pkt` and update this README and the main README.
