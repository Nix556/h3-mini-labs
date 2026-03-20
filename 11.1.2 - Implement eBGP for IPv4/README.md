# eBGP for IPv4 – Forklaring

## Overblik

I denne opgave arbejdede vi med **eBGP (External Border Gateway Protocol)** for IPv4.

Formålet var at konfigurere routing mellem forskellige autonome systemer (AS), så routere i forskellige netværk kan udveksle routes med hinanden.

Vi havde 3 routere:

* R1 i AS 1000
* R2 i AS 500
* R3 i AS 300

Alle routere skulle kunne kommunikere og udveksle routes via eBGP.

---

## Hvad vi lavede

### Grundopsætning

Først satte vi netværket op:

* Tildelte IP-adresser på alle interfaces
* Konfigurerede loopback interfaces
* Aktiverede interfaces med `no shutdown`

Det gav os et fungerende grundnetværk.

---

### eBGP konfiguration

Vi konfigurerede BGP på alle routere med hver deres AS nummer:

* R1 - AS 1000
* R2 - AS 500
* R3 - AS 300

Derefter opsatte vi neighbor relationships mellem routerne:

* R1 - R2
* R2 - R3
* R1 - R3

Vi brugte `neighbor <ip> remote-as <as>` til at definere forbindelserne.

Til sidst annoncerede vi vores egne netværk med `network` kommandoen.

---

### Problem med R3

R3 virkede ikke i starten.

Det skyldtes at vi brugte:

```text
no bgp default ipv4-unicast
```

Det betyder at:

* BGP ikke automatisk aktiverer IPv4
* Vi selv skal aktivere neighbors i address-family

Vi løste det ved at gå ind i:

```text
address-family ipv4
```

Og derefter:

```text
neighbor X activate
```

Samt tilføje `network` kommandoer derinde.

Efter det blev alle BGP sessions **Established**.

---

### Verificering

Vi brugte kommandoer som:

* `show ip route bgp`
* `show ip bgp`
* `show ip bgp neighbors`

Her kunne vi se:

* Routes fra andre AS’er
* Hvilken vej trafikken går (next-hop)
* Hvilken route der er valgt som best (`>`)

---

## Route Summarization

Vi arbejdede også med route summarization for at gøre routing tabellen mindre.

På R1 og R3 lavede vi en summary route:

```text
aggregate-address 192.168.X.0 255.255.255.0 summary-only
```

Det betyder:

* Mindre routing tabel
* Bedre performance
* Mere stabilt netværk

De specifikke routes blev skjult (suppressed).

---

### Null0 route

Når vi laver summarization, bliver der automatisk lavet en route til **Null0**.

Det bruges til:

* At droppe trafik der ikke matcher en specifik route
* Undgå routing loops

---

## AS-Set

Vi testede også summarization med:

```text
aggregate-address ... as-set
```

Forskellen er:

* Uden as-set - kun ét AS i path
* Med as-set - hele AS path bliver bevaret

Det giver mere korrekt routing information.

---

## Default Route

Til sidst konfigurerede vi en default route fra R2 til R1:

```text
neighbor 10.1.2.1 default-originate
```

Det betyder:

* R1 sender al ukendt trafik til R2
* R2 fungerer som gateway

Vi kunne verificere det med:

```text
0.0.0.0/0
```

---

## Konklusion

Vi fik et fuldt fungerende eBGP netværk, hvor alle routere kunne udveksle routes korrekt.

Vi arbejdede både med:

* Grundlæggende eBGP
* Fejlfinding
* Route optimering

Det gav en god forståelse for hvordan BGP fungerer i praksis i større netværk.
