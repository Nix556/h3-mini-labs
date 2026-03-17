# ENCOR Skills Assessment – Scenario 1

## Overblik

I denne opgave har vi arbejdet med en større enterprise netværksopsætning, hvor vi skulle få hele netværket til at fungere end-to-end.

Formålet var:

* Fuld connectivity mellem alle enheder (IPv4 + IPv6)
* Redundans på default gateway (HSRP)
* Dynamisk routing (OSPF + BGP)
* Sikkerhed (AAA + RADIUS)
* Netværks management (NTP, Syslog, SNMP)

Netværket er delt op i:

* **Company Network** (R1, R3, D1, D2)
* **ISP Network** (R2)

---

## Part 1 – Basic Setup

Her satte vi alle devices op med:

* Hostnames
* IPv4 og IPv6 adresser
* Interfaces (no shutdown)
* Basic settings (console, banner osv.)

Der er både:

* Layer 3 routing interfaces
* VLAN interfaces (SVI)
* Loopback interface på R2 (bruges til BGP)

---

## Part 2 – Layer 2 (Switching)

### Trunk links (802.1Q)

Vi lavede trunk links mellem alle switches:

* D1 - D2
* D1 - A1
* D2 - A1

Native VLAN blev sat til **VLAN 999** for sikkerhed.

---

### EtherChannel (LACP)

Vi brugte LACP til at samle links:

* D1–D2 - Port-channel 12
* D1–A1 - Port-channel 1
* D2–A1 - Port-channel 2

Formålet er:

* Mere båndbredde
* Redundans

---

### Spanning Tree (RSTP)

Vi brugte **Rapid PVST+**.

Root bridge:

* D1 = root for VLAN 100 og 102
* D2 = root for VLAN 101

Så hvis en switch fejler, tager den anden over.

---

### Access ports

Ports til PC’er blev sat som:

* Access ports i korrekt VLAN
* PortFast enabled - hurtigere opstart

---

### DHCP

D1 og D2 fungerer som DHCP servere for:

* VLAN 101
* VLAN 102

PC2 og PC3 får automatisk IP via DHCP + IPv6 via SLAAC.

---

## Part 3 – Routing

### OSPFv2 (IPv4)

Vi brugte **single-area OSPF (Area 0)**.

* Process ID: 4
* Alle interne netværk annonceres
* R1 sender default route videre (fra BGP)

På D1 og D2:

* Alle interfaces er passive undtagen uplink (G1/0/11)

---

### OSPFv3 (IPv6)

Samme setup som IPv4 bare med IPv6:

* Process ID: 6
* Area 0
* IPv6 routing mellem alle enheder

---

### BGP (ISP forbindelse)

#### R2 (ISP)

* ASN: 500
* Sender:

  * Default route (0.0.0.0 / ::/0)
  * Loopback (2.2.2.2)

#### R1 (Company edge)

* ASN: 300
* Sender:

  * 10.0.0.0/8 (summary route)
  * 2001:db8:100::/48 (IPv6 summary)

BGP bruges til forbindelse til “internettet”, mens OSPF bruges internt.

---

## Part 4 – First Hop Redundancy (HSRP)

Vi konfigurerede **HSRPv2** på D1 og D2.

### Formål

At give en **virtuel default gateway**, så hosts altid har en gateway selv hvis en switch går ned.

---

### Active / Standby

* D1 = aktiv for VLAN 100 og 102
* D2 = aktiv for VLAN 101

Virtual IP:

* 10.0.100.254
* 10.0.101.254
* 10.0.102.254

---

### IP SLA + Tracking

Vi brugte IP SLA til at overvåge routere:

* D1 tracker R1
* D2 tracker R3

Hvis forbindelsen fejler:

* HSRP priority sænkes
* Den anden switch overtager

Det giver **automatisk failover**

---

## Part 5 – Security

### Sikker adgang

* Enable password med **SCRYPT encryption**
* Lokal bruger:

  * username: `sadmin`
  * privilege 15

---

### AAA + RADIUS

Vi aktiverede AAA på alle devices (undtagen R2):

* Login går først til **RADIUS server**
* Hvis den fejler - fallback til lokal bruger

RADIUS server:

* IP: 10.0.100.6
* Ports: 1812 / 1813

---

## Part 6 – Network Management

### NTP (tidssynkronisering)

* R2 = NTP master (stratum 3)
* R1 syncer med R2
* Resten syncer via R1 eller R3

Alle devices har samme tid (vigtigt for logs)

---

### Syslog

Alle devices sender logs til:

* PC1 (10.0.100.5)

Level:

* warnings

---

### SNMP

Vi konfigurerede SNMPv2c:

* Read-only adgang
* Kun PC1 må tilgå (ACL)
* Community: `ENCORSA`

Traps:

* OSPF
* BGP (kun R1)
* Config changes

---

## Samlet forståelse

Denne lab kombinerer mange teknologier:

* **Layer 2** - VLAN, trunk, STP, EtherChannel
* **Layer 3** - OSPF + BGP
* **Redundans** - HSRP + IP SLA
* **Security** - AAA + RADIUS
* **Management** - NTP, Syslog, SNMP

Det er basically en “mini enterprise network” hvor alt spiller sammen.

---

## Konklusion

Vi har sat et fuldt redundant netværk op med:

* Dynamisk routing (OSPF + BGP)
* Gateway redundans (HSRP)
* Automatisk failover (IP SLA)
* Central authentication (AAA)
* Overvågning og logging

Alt er bygget sådan, at netværket stadig virker selv hvis en enhed fejler.
