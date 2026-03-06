## VRF-Lite – Forklaring

I denne opgave arbejdede vi med VRF-Lite (Virtual Routing and Forwarding). Formålet med VRF er at kunne have flere separate routing tables på den samme router.
Det betyder i praksis, at en router kan fungere som om den var flere routere på samme tid.
Det bliver typisk brugt af internetudbydere eller større netværk, hvor man skal holde forskellige kunders netværk adskilt.

---

## Hvordan VRF fungerer

Normalt har en router kun en routing table, som alle interfaces bruger.
Når man bruger VRF, laver man i stedet flere routing tables, og hvert interface bliver tilknyttet en bestemt VRF.

Vi har i labbet oprettet:

* Customer_A
* Customer_B

Begge VRF'er har deres egen routing table, så de kan ikke se hinandens routes.
Det betyder også at man kan genbruge de samme IP-adresser, fordi de ligger i forskellige VRF'er.

---

## Oprettelse af VRF

Først opretter man VRF'erne på routeren.


ip vrf Customer_A
ip vrf Customer_B

Her opretter vi to separate routing tables.
RD (Route Distinguisher) bruges til at identificere VRF'en.

---

## Tilknytning af interfaces til VRF

Når VRF'erne er oprettet, skal interfaces kobles til dem.

Eksempel:

interface GigabitEthernet0/0/0
 ip vrf forwarding Customer_A
 ip address 10.1.2.1 255.255.255.0

Her betyder det at dette interface nu tilhører Customer_A VRF'en.
Det betyder også at al trafik der kommer ind på dette interface bliver behandlet i Customer_A's routing table.

---

## Routing i VRF

Fordi hver VRF har sin egen routing table, skal man også lave routes inde i VRF'en.

Eksempel:

ip route vrf Customer_A 192.168.2.0 255.255.255.0 10.1.2.2

Det betyder at denne route kun gælder i Customer_A VRF'en.
Customer_B kan have helt andre routes, selv hvis IP-adresserne er de samme.

---

## Fordele ved VRF

VRF bruges fordi det giver flere fordele:

**Netværksisolering**
Kunder eller afdelinger kan holdes helt adskilt.

**Samme IP-adresser kan bruges flere steder**
Fordi routing tables er separate.

**Bedre sikkerhed**
Netværk kan ikke kommunikere med hinanden uden speciel konfiguration.

**Skalerbarhed**
Det gør det muligt for en router at håndtere mange separate netværk.
