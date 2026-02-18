# CHANGELOG

All notable changes to this lab repository are recorded here.

## 17-02-2026

- Configured the base for RT01, RT02, SW01 and SW02.

## 18-02-2026 - OSPF setup and optimizations

- Added OSPF configuration snippets for RT1 and RT2:
  - `patches/01-ospf-RT01.cli` — OSPF process, networks, passive interfaces, auto-cost, loopback p2p, hello interval
  - `patches/01-ospf-RT02.cli` — OSPF process, networks, passive interfaces, auto-cost, default origination, hello interval, DR priority, static default route
- Copied full base configs to `configs/base/RT01.cfg` and `configs/base/RT02.cfg` for easy reference.
- Copied OSPF snippets to `configs/ospf/` for per-router OSPF full blocks.

## 18-02-2026 - NAT / OSPF adjustments and backups

- Added NAT-only configs for RT01 and RT02:
  - `configs/access control and NAT/RT01-NAT.cfg` — adds `ip access-list extended RT1-NAT`, marks `GigabitEthernet0/0/1` as `ip nat inside`, `GigabitEthernet0/0/0` as `ip nat outside`, and configures `ip nat inside source list RT1-NAT interface GigabitEthernet0/0/0 overload`. Also includes a `router ospf 1` block with `no network 192.168.1.0 0.0.0.255 area 0` so the file can be applied to remove the RT1 LAN from OSPF.
  - `configs/access control and NAT/RT02-NAT.cfg` — NAT/PAT example for RT02 and interface direction notes (extracted from RT02-SECURITY.cfg).

- Documentation: updated `README.md` with TFTP backup instructions and a note about the new NAT files.

## 18-02-2026 - Access control, NAT, and backup helpers

- Security / ACLs:
  - `configs/access control and NAT/RT02-SECURITY.cfg` — created/updated ACL to control access to the web server at `209.165.201.1`. ACLs were tuned so LAN hosts can reach required services while remote/unauthorized SSH/HTTPS is denied. The ACL is applied on the upstream interface to avoid filtering internal LAN traffic.

- NAT:
  - `configs/access control and NAT/RT01-NAT.cfg` — NAT/PAT config for RT01 to translate `192.168.1.0/24` to the RT1 outside interface with overload.

- Backup/TFTP helpers:
  - `configs/access control and NAT/RT01-BACKUP.cfg` — sets TFTP source interfaces on RT01 (`ip tftp source-interface GigabitEthernet0/0/0` and `GigabitEthernet0/0/1`) to ensure the TFTP server replies route back correctly.
  - `configs/access control and NAT/RT02-BACKUP.cfg` — sets TFTP source interface on RT02 (`ip tftp source-interface GigabitEthernet0/0/1`).

All files are located under `configs/access control and NAT/` and `configs/ospf/` and were added on 18-02-2026 as part of the lab completion.
