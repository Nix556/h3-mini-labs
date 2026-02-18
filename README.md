# Project structure

- configs/base/: Full base configs for routers/switches. Paste these if you want a complete router config.
- configs/ospf/: Full OSPF blocks per-router for reference or bulk application.

How to apply a patch

1. Connect to the router in privileged exec.
2. Paste the desired patch file contents (copy the file and paste into the router terminal).
3. Verify with `show running-config | section router ospf` and `show ip ospf neighbor`.
