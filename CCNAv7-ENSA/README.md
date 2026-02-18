# Project structure

- configs/base/: Full base configs for routers/switches. Paste these if you want a complete router config.
- configs/ospf/: Full OSPF blocks per-router for reference or bulk application.

Recent additions

- configs/access control and NAT/RT01-NAT.cfg: NAT/PAT and OSPF "no network" block for R1.
- configs/access control and NAT/RT02-NAT.cfg: NAT/PAT example for R2 (extracted/added).

TFTP backup guidance

- To push a running-config from a router to a TFTP server:

```text
copy running-config tftp
Address or name of remote host []? <TFTP_SERVER_IP>
Destination filename [running-config]? <filename>
```

How to apply a patch

1. Connect to the router in privileged exec.
2. Paste the desired patch file contents (copy the file and paste into the router terminal).
3. Verify with `show running-config | section router ospf` and `show ip ospf neighbor`.
