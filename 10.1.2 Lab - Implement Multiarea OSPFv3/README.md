# Multiarea OSPFv3 – Forklaring

I denne opgave arbejdede vi med OSPFv3 i et multiarea design.
Formålet var at konfigurere dynamisk routing for både IPv4 og IPv6 ved hjælp af OSPFv3 Address Families.

Normalt bruges:

* OSPFv2 til IPv4
* OSPFv3 til IPv6

Men i nyere Cisco IOS kan OSPFv3 også håndtere IPv4, ved hjælp af Address Family (AF).
Det betyder at en OSPFv3 process kan route både IPv4 og IPv6 samtidig.

---

## Hvordan OSPF fungerer

OSPF er et link-state routing protocol.
Det betyder at alle routere deler information om deres links med hinanden i form af LSA (Link-State Advertisements).
Alle routere gemmer disse informationer i en Link State Database (LSDB).
Ud fra LSDB beregner routerne den korteste vej gennem netværket ved hjælp af SPF algoritmen (Shortest Path First).

Fordelen ved OSPF er at:

* konvergens er hurtig
* routing er meget præcis
* netværket kan skaleres ved hjælp af areas

---

## Multi-Area OSPF

I dette lab brugte vi flere OSPF områder.

Vi havde:

* Area 0 (Backbone)
* Area 1
* Area 2

Area 0 er den centrale backbone som alle andre områder skal forbinde til.

Strukturen i netværket var derfor:

```text
Area 1 ---- R1 ----\
                    R2
Area 2 ---- R3 ----/
```

R1 forbinder Area 1 til backbone, og R3 forbinder Area 2 til backbone.
Dette design bruges i større netværk fordi det:

* reducerer routing traffic
* gør netværket mere skalerbart
* gør LSDB mindre i hvert område.

---

## IPv6 og OSPFv3

OSPFv3 kører oven på IPv6.
Det betyder at OSPFv3 control traffic bruger IPv6 link-local adresser.
Derfor skulle vi aktivere IPv6 routing på routerne:

```text
ipv6 unicast-routing
```

Alle interfaces fik også en link-local adresse, som OSPF bruger til neighbor communication.

---

## OSPFv3 Address Families

I labbet brugte vi OSPFv3 Address Families (AF).
Det gør at en OSPFv3 process kan håndtere både:

* IPv4 routing
* IPv6 routing

Eksempel:

```text
router ospfv3 123
 address-family ipv4 unicast
 address-family ipv6 unicast
```

Det betyder at routeren bruger samme OSPF process til begge protokoller.

---

## Aktivering af OSPF på interfaces

OSPFv3 bliver aktiveret direkte på interfaces.

Eksempel:

```text
interface g0/0/0
 ospfv3 123 ipv4 area 0
 ospfv3 123 ipv6 area 0
```

Her deltager interfacet i:

* IPv4 routing i OSPF
* IPv6 routing i OSPF

begge i Area 0.

---

## Neighbor Relationships

For at OSPF kan virke, skal routerne først blive neighbors.
Det sker ved at routerne sender Hello packets til hinanden.
Når neighbors er etableret, går de i FULL state, og de udveksler deres LSDB.
Dette kan verificeres med:

```text
show ospfv3 neighbor
```

---

## Routing Tables

Når OSPF neighbors er etableret, begynder routerne at lære routes.

IPv4 routes kan ses med:

```text
show ip route ospfv3
```

IPv6 routes kan ses med:

```text
show ipv6 route ospf
```

Routerne bruger disse routes til at finde den korteste vej gennem netværket.

---

## OSPF Database (LSDB)

OSPF gemmer alle netværksinformationer i Link State Database.

Dette kan ses med:

```text
show ospfv3 database
```

LSDB indeholder information om:

* routere
* netværk
* links mellem routere.

Routerne bruger LSDB til at beregne routing paths med SPF algoritmen.

---

## Passive Interfaces

Vi konfigurerede også passive interfaces.

Eksempel:

```text
passive-interface g1/0/23
```

Et passive interface:

* annoncerer stadig netværket i OSPF
* men sender "ikke hello packets"

Det bruges typisk på LAN interfaces, hvor der ikke findes andre routere.

Fordele:

* mindre routing traffic
* bedre sikkerhed.

---

## Route Summarization

Vi implementerede også route summarization.

Eksempel:

```text
area 1 range 2001:db8:acad:1000::/52
```

Det betyder at flere mindre netværk bliver samlet til en større route.

Fordele:

* mindre routing tables
* færre LSAs
* mere stabil routing.

---

## Point-to-Point Links

Ethernet interfaces bruger normalt broadcast network type, hvor OSPF vælger:

* DR (Designated Router)
* BDR (Backup Designated Router)

Men forbindelserne mellem routerne i labbet var point-to-point links.
Derfor ændrede vi network type til:

```text
ospfv3 network point-to-point
```

Fordelen er at der ikke længere vælges DR og BDR, hvilket gør OSPF simplere.

---

## Default Route Advertisement

Til sidst konfigurerede vi en default route på R2.

```text
ip route 0.0.0.0 0.0.0.0 lo0
ipv6 route ::/0 lo0
```

Derefter blev den annonceret i OSPF:

```text
default-information originate
```

Det betyder at alle routere i netværket lærer en default route via R2.
Hvis routerne ikke kender destinationen, sender de trafikken til R2.
