# Project overview and usage

**Project Structure:**

- **configs/base/**: Full base configs for routers and switches (complete router/switch configs you can paste into devices).
- **configs/ospf/**: Per-router OSPF full blocks for reference or bulk application.
- **configs/access control and NAT/**: Access control lists, NAT-only snippets, and backup helper files.

## What you built in this lab

- `configs/access control and NAT/RT01-NAT.cfg`: NAT/PAT config for RT01 and a `router ospf 1` `no network 192.168.1.0 0.0.0.255 area 0` block to remove the R1 LAN from OSPF.
- `configs/access control and NAT/RT01-BACKUP.cfg`: sets `ip tftp source-interface GigabitEthernet0/0/0` and `GigabitEthernet0/0/1` (RT01 backup helper).
- `configs/access control and NAT/RT02-BACKUP.cfg`: sets `ip tftp source-interface GigabitEthernet0/0/1` (RT02 backup helper).
- `configs/access control and NAT/RT02-SECURITY.cfg`: access-list used to control access to the web server (adjusted to allow LAN traffic and TFTP where needed).

Quick file links:

- [configs/access control and NAT/RT01-NAT.cfg](configs/access%20control%20and%20NAT/RT01-NAT.cfg)
- [configs/access control and NAT/RT01-BACKUP.cfg](configs/access%20control%20and%20NAT/RT01-BACKUP.cfg)
- [configs/access control and NAT/RT02-BACKUP.cfg](configs/access%20control%20and%20NAT/RT02-BACKUP.cfg)
- [configs/access control and NAT/RT02-SECURITY.cfg](configs/access%20control%20and%20NAT/RT02-SECURITY.cfg)

## How to apply a patch file

1. Connect to the router and enter privileged exec (`enable`).
2. Enter global configuration mode: `configure terminal`.
3. Paste the contents of the desired file (copy/paste the CLI commands from the file into the router session).
4. Exit and save: `end` then `write memory`.

## TFTP backup guidance

- To push a running-config from a router to a TFTP server:

```text
copy running-config tftp
Address or name of remote host []? <TFTP_SERVER_IP>
Destination filename [running-config]? <filename>
```

- If the router can't complete the transfer:
- On the router set the source interface used for TFTP replies (example for RT1):

```text
configure terminal
ip tftp source-interface GigabitEthernet0/0/0
end
write memory
```

- Ensure the TFTP server (SolarWinds TFTP or other) is running with a writable root folder and the host firewall allows UDP 69 and the tftp server program.
- If ACLs exist on routers in the path, permit UDP 69 and ephemeral UDP replies between the router and the TFTP host.

## Useful verification commands (on routers)

- `show running-config | section router ospf`
- `show ip ospf neighbor`
- `show access-lists RT1-NAT` (or RT2-SECURITY)
- `show running-config interface GigabitEthernet0/0/0`
- `show ip nat translations` and `show ip nat statistics` (after generating traffic)
