# Multiple Spanning Tree (MST) – Forklaring

I denne opgave arbejdede vi med Multiple Spanning Tree (MST).
Formålet var at optimere Spanning Tree i et netværk med flere VLANs, så vi ikke skal køre en STP instans pr. VLAN, som man gør med PVST+.

MST gør det muligt at mappe flere VLANs til den samme spanning tree instans.
Det betyder mindre CPU load på switchene og bedre udnyttelse af netværket.

---

## Hvordan Spanning Tree fungerer

Spanning Tree bruges til at undgå loops i et Layer 2 netværk.

Hvis der er flere forbindelser mellem switches, kan der opstå loops, som skaber broadcast storms.
Spanning Tree løser dette ved at:

* vælge en **root bridge**
* finde den bedste vej til root bridge
* blokere redundant links

Alle beslutninger bliver taget ud fra nogle værdier (cost, priority osv.).

---

## Forskellen på PVST+ og MST

PVST+:

* en spanning tree instans pr. VLAN
* høj CPU load ved mange VLANs
* Cisco proprietary

MST:

* flere VLANs kan dele samme instans
* færre spanning tree beregninger
* standardiseret (IEEE)

---

## MST Instances og VLAN Mapping

I labbet lavede vi følgende mapping:

* MST0 (default): VLAN 1 og resten
* MST1: VLAN 2-3
* MST2: VLAN 4-5

Det betyder at:

* VLAN 2 og 3 bruger samme spanning tree
* VLAN 4 og 5 bruger en anden
* resten bruger MST0

Fordelen er at man kan lave load balancing mellem VLANs.

---

## MST Region

For at MST virker korrekt, skal alle switches have samme:

* navn
* revision
* VLAN mapping

Eksempel:

```text
spanning-tree mst configuration
 name CCNPv8
 revision 2
 instance 1 vlan 2-3
 instance 2 vlan 4-5
```

Hvis noget ikke matcher, kommer switchen i en anden region.

Switchene udveksler en “digest”, som er en slags hash af konfigurationen.
Hvis digest er forskellig betyder det at de er ikke i samme MST region.

---

## Root Bridge

Spanning Tree vælger en root bridge.

Valget er baseret på Bridge ID:

```text
Priority + MAC adresse
```

Laveste værdi vinder.

Standard priority er:

```text
32768
```

I labbet styrede vi root bridges manuelt.

Eksempel:

```text
spanning-tree mst 1 root primary
spanning-tree mst 2 root secondary
```

Resultat:

* D1 blev root for MST1
* D2 blev root for MST2

Det gør at trafikken bliver fordelt i netværket.

---

## Root Port og Designated Port

Når root bridge er valgt:

* hver switch vælger en **root port** (bedste vej til root)
* hver forbindelse har en **designated port**
* resten bliver **blocked**

---

## Port Cost

Port cost bestemmer hvilken vej der er bedst til root.

Jo lavere cost - jo bedre.

Standard værdier afhænger af hastighed:

* FastEthernet: 200000
* Gigabit: 20000

Hvis en switch har flere veje til root med samme cost, bruges andre værdier.

I labbet ændrede vi cost:

```text
interface G1/0/2
spanning-tree mst 1 cost 1000
```

Det gjorde at den port blev valgt som root port, fordi den havde lavest cost.

---

## Port Priority

Hvis cost er ens, kigger Spanning Tree på port ID:

```text
Priority + Port number
```

Standard priority er:

```text
128
```

Lavere priority = bedre.

I labbet ændrede vi priority på D2:

```text
interface g1/0/6
spanning-tree mst 2 port-priority 64
```

Det gjorde at downstream switch (A1) valgte den port som root port.

---

## Cost vs Priority

Der er forskel på hvordan de bruges:

### Port Cost (lokal beslutning)

* ændres på den lokale switch
* påvirker hvilken vej den selv vælger

### Port Priority (upstream påvirkning)

* ændres på upstream switch
* påvirker hvilken vej andre vælger

---

## Spanning Tree beslutning (rækkefølge)

Når en switch vælger root port:

1. Laveste path cost
2. Laveste upstream Bridge ID
3. Laveste upstream port ID
4. Laveste lokale port ID

---

## Verifikation

Vi brugte følgende kommandoer:

```text
show spanning-tree mst
show spanning-tree root
show spanning-tree blockedports
```

De viser:

* hvem der er root
* hvilke porte der er forwarding eller blocking
* hvilke VLANs der er i hver MST instance

---

## Konklusion

MST gør det muligt at optimere Spanning Tree i større netværk.

I stedet for en instans pr. VLAN, kan man:

* reducere CPU load
* styre trafikken bedre
* lave load balancing

Spanning Tree handler i bund og grund om at sammenligne værdier og vælge den bedste vej.
Ved at ændre cost og priority kan man styre hvordan trafikken løber i netværket.
