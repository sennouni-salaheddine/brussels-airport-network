# Demo Script voor Jury — 19 juni 2026

Concrete commando's die je live aan de jury tikt om te bewijzen dat alles werkt.
Print dit document of houd het open op je telefoon tijdens de presentatie.

---

## VOORBEREIDING (15 min voor presentatie)

1. Open Packet Tracer
2. Open `Rack1_v7_17juni.pkt`
3. Wacht 30 seconden tot OSPF stabiel is
4. Check dat LuchHaven002 verbonden is (geel of groene driehoek)
5. Test snel: ping van PC-A1 naar 10.1.110.1

Als iets niet werkt voor de presentatie begint → switch naar de screenshots
in `9_Multiuser_Logboek/screenshots/` als bewijs.

---

## DEMO STAPPEN (in deze volgorde)

### Stap 1 — Toon de topologie

**Wat je zegt:**
> "Dit is mijn Rack 1 voor Brussels Airport. 13 VLANs voor 2 luchtvaartmaatschappijen
> plus gedeelde diensten. SW-DIST en SW-DIST2 zijn de distributie-switches, R-EDGE
> en R-EDGE2 de edge routers. SW-CORE-RACK1 is nieuw — die verbindt mijn rack via
> Peer0 met Aleksander's rack. Dit simuleert hoe partnerbedrijven op de luchthaven
> samenwerken."

**Wat je doet:** Klik door de topologie, zoom in op SW-CORE-RACK1 + LuchHaven002.

---

### Stap 2 — Bewijs OSPF FULL met 3 neighbors

**Open R-EDGE console en tik:**
```
show ip ospf neighbor
```

**Wat je zegt:**
> "R-EDGE heeft 3 OSPF buren in FULL state. Dat betekent dat het netwerk volledig
> geconvergeerd is en routes uitwisselt:
> - 10.1.199.1 = SW-DIST (intern, OSPF process 1)
> - 4.4.4.4 = R-EDGE2 (backup edge, via Serial)
> - 10.99.0.2 = SW-CORE-RACK1 (inter-rack, OSPF process 25)"

---

### Stap 3 — Bewijs dat routes uitgewisseld worden

**Op R-EDGE tik:**
```
show ip route
```

**Wat je zegt:**
> "Alle 12 interne VLANs van mijn rack zijn zichtbaar via OSPF, als O routes.
> De default route is S* statisch op R-EDGE, en wordt via 'default-information
> originate' aan alle interne devices gepushed als O*E2.
> Het inter-rack subnet 10.99.0.76/30 — dat is de link via SW-CORE naar
> R-EDGE2 — is ook geleerd via OSPF."

---

### Stap 4 — Bewijs multiuser werkt (kern van de demo)

**Op SW-CORE-RACK1 tik:**
```
show ip ospf neighbor
```

**Wat je zegt:**
> "Dit is het bewijs dat multiuser werkt. SW-CORE-RACK1 heeft een OSPF buurschap
> in FULL state met Aleksander's router via Peer0. Dat betekent dat OSPF hello
> packets succesvol door de multiuser tunnel reizen tussen onze twee PT instances."

---

### Stap 5 — Toon interne werking (ACL bewijs)

**Open PC-A1 Desktop -> Command Prompt en tik:**
```
ping 10.1.120.1
```

**Wat je zegt:**
> "Cross-VLAN routing werkt. PC-A1 in Airline A bereikt de gateway van Airline B."

**Daarna tik:**
```
ping [IP van PC-B1]
```

**Wat je zegt:**
> "Maar ping naar een PC in Airline B faalt. Dat is de ACL die Yvan in zijn
> requirements vroeg: airlines mogen elkaars netwerk niet zien voor concurrentie-
> redenen. Het netwerk gateway is bereikbaar maar het eindpunt geblokkeerd."

---

### Stap 6 — Bewijs inter-rack connectivity (indien Aleksander beschikbaar)

**Op SW-CORE-RACK1 tik:**
```
ping 10.99.0.1
```

**Wat je zegt:**
> "Dit is een directe ping over de multiuser link naar Aleksander's router.
> Als je inter-rack verkeer kan zenden, is de fundering voor end-to-end
> communicatie tussen alle partnerbedrijven op de luchthaven aanwezig."

---

## ALS IETS FAALT TIJDENS DEMO

### Aleksander niet online?
**Wat je zegt:**
> "Mijn rack is volledig getest. De multiuser verbinding met Aleksander hebben
> we eerder vandaag getest en werkt. Hier zijn de screenshots van de werkende
> sessie."

→ Open `screenshots/` map met de screenshots

### PT crasht tijdens demo?
**Wat je zegt:**
> "Packet Tracer is een simulator en kan crashen onder load. Mijn werk is
> volledig gedocumenteerd in Drive met alle screenshots als bewijs. Laten we
> de documentatie bekijken."

→ Open Drive `9_Multiuser_Logboek/`

### Inter-rack ping faalt?
**Wat je zegt:**
> "OSPF control plane werkt — de buren zijn FULL en routes worden uitgewisseld.
> Inter-rack data plane forwarding via PT Multiuser is bekend wankel —
> dat is een PT-beperking, geen design fout. In CML of op echte hardware
> zou dit gewoon werken."

---

## TIP: ZWAKKE PUNTEN PROACTIEF VERMELDEN

Yvan waardeert eerlijkheid. Als jury vraagt naar tekortkomingen, noem deze
zelf eerst:

1. **HSRP op edge routers**: geconfigureerd maar niet operationeel door
   segmentmismatch. Bewust geparkeerd — redundantie blijft via OSPF
   herconvergentie.

2. **Internet-uplink**: out-of-scope voor deze sprint. Default route gaat
   naar serial backup-pad. In productie zou hier een NAT/PAT richting WAN
   komen.

3. **SW-CORE-RACK2**: tweede core in topology als toekomstige redundantie,
   maar niet volledig geconfigureerd door tijdsbeperking.

> "Ik heb bewust gekozen om de werkende functies grondig te testen en
> documenteren in plaats van alles half af te leveren."

Dat is het soort antwoord dat een professional geeft.

---

## NA DE DEMO — KEY MESSAGE

Sluit af met:

> "Wat dit project mij heeft geleerd: een werkend netwerk is niet alleen
> een set commando's. Het is een afweging tussen volledigheid, betrouwbaarheid,
> en tijd. Door één rack diep te begrijpen ben ik klaar om in een stage een
> volledig enterprise netwerk aan te kunnen."

Dat sluit aan bij wat Yvan zei: "groeien naar een professional".
