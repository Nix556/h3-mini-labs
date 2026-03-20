# CCNAv7 ENSA Skills Assessment – Forklaring

I denne opgave arbejdede vi med en samlet “final exam”, hvor vi skulle konfigurere et helt netværk fra bunden.

Formålet var at få alle enheder til at fungere sammen med:

- IPv4 connectivity
- Dynamisk routing (OSPF)
- Sikkerhed (SSH + ACL)
- NAT (PAT)
- Backup af konfiguration

Netværket bestod af:

- 2 routere (R1 og R2)
- 2 switches (S1 og S2)
- 2 PCs

---

## Part 1 – Basic Setup

Først nulstillede vi alle devices:

- startup-config blev slettet
- VLAN database blev slettet på switches
- devices blev genstartet

Derefter lavede vi grundkonfiguration på alle enheder.

---

### Routere

Vi konfigurerede:

- Hostname (R1, R2)
- Domain name (ccna-lab.com)
- Enable password
- Console password
- Lokal bruger (admin)
- SSH (kun SSH adgang på VTY)
- Password encryption
- MOTD banner

Interfaces blev sat op med:

- IP adresser
- Description
- no shutdown

Derudover:

- Loopback interface
- RSA keys (til SSH)

---

### Switches

På switches konfigurerede vi:

- Hostname
- Domain name
- Passwords
- SSH adgang
- Lokal bruger

Derudover:

- Shutdown af alle unused ports (sikkerhed)
- Management IP på VLAN 1
- Default gateway

---

## Part 2 – OSPF (Single Area)

Vi konfigurerede OSPF mellem R1 og R2.

- Process ID: 1
- Router-ID blev sat manuelt
- Netværk blev annonceret med wildcard masks

R2’s loopback blev ikke inkluderet i OSPF.

Formålet er at få dynamisk routing mellem alle netværk.

---

## Part 3 – OSPF Optimering

Her finjusterede vi OSPF.

---

### R1

- Passive interfaces på LAN og loopback
- Reference bandwidth sat til 1000 Mbps
- Loopback sat som point-to-point
- Hello interval sat til 30 sek

---

### R2

- Passive interfaces
- Reference bandwidth = 1000 Mbps

Default route:

- Statisk default route via loopback
- Annonceret i OSPF med `default-information originate`

Derudover:

- Hello interval = 30 sek
- OSPF priority = 50 (for at blive DR)

---

## Part 4 – ACL, NAT og Backup

---

### PC konfiguration

Vi satte IP konfiguration på:

- PC-A: 192.168.1.50
- PC-B: 10.67.1.50

Testede:

- Ping mellem PC’er
- SSH
- HTTPS

---

### ACL på R2

Vi lavede en extended ACL:

Formål:

- Styre adgang til server (209.165.201.1)

Regler:

- HTTPS blokkeret
- SSH blokkeret
- Alt andet trafik tilladt

ACL blev applied inbound på interface mod R1.

---

### NAT (PAT) på R1

R1’s LAN bruger 192.168.1.0/24, som ikke er en del af 10.0.0.0/8 → derfor NAT.

Vi gjorde følgende:

- Fjernede LAN fra OSPF
- Lavede ACL til NAT
- Konfigurerede PAT (overload)

Interfaces:

- G0/0/1 = inside
- G0/0/0 = outside

Formålet er at oversætte private adresser til en gyldig adresse ud mod netværket.

---

### Backup med TFTP

Vi tog backup af alle devices:

```text

copy running-config tftp

```

TFTP server var PC-B, som kørte Tftpd64.

---

## Konklusion

Vi har sat et netværk op fra bunden med:

- Dynamisk routing (OSPF)
- Trafik kontrol (ACL)
- Adresse oversættelse (NAT)
- Sikker remote adgang (SSH)
- Backup (TFTP)

Opgaven viser hvordan flere teknologier arbejder sammen for at få et netværk til at fungere stabilt og sikkert.
