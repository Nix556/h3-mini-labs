# CHANGELOG

All notable changes to this lab repository are recorded here.

## 2026-02-18 - OSPF setup and optimizations
- Added OSPF configuration snippets for RT1 and RT2:
  - `patches/01-ospf-RT01.cli` — OSPF process, networks, passive interfaces, auto-cost, loopback p2p, hello interval
  - `patches/01-ospf-RT02.cli` — OSPF process, networks, passive interfaces, auto-cost, default origination, hello interval, DR priority, static default route
- Copied full base configs to `configs/base/RT01.cfg` and `configs/base/RT02.cfg` for easy reference.
- Copied OSPF snippets to `configs/ospf/` for per-router OSPF full blocks.

