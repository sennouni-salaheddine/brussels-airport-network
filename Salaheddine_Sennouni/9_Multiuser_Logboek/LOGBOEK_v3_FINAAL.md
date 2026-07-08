# LOGBOEK Multi-user PT — Rack 1 — VERSIE 3 (FINAAL)

Chronologisch overzicht van werkzaamheden, beslissingen en lessons learned.
Datums in NL-formaat (DD/MM/JJJJ).

---

## 11/05/2026 — Coach-meeting, beslissing CML -> Multi-user

**Wat:** Tijdens coach-meeting met Yvan Rooseleer is besloten om CML te vervangen
door PT Multi-user, na herhaaldelijk falen van Cisco DevNet Sandbox provisioning.

**Beslissingen:**
- Aleksander hostet de Multiuser sessie (Listening mode)
- Aleksander hostet ook internet (via default-information originate)
- Inter-rack IP-bereik: 10.99.0.0/24 in /30 subnetten
- OSPF process 25 voor inter-rack (apart van eigen process 1 binnen rack)
- Nieuwe Multilayer Switch 3650 toegevoegd aan elk rack als inter-rack core

---

## 13/05/2026 — Rack 1 multi-user setup voltooid (versie 1)

**Wat gedaan:**
1. SW-CORE-RACK1 (Multilayer Switch 3650-24PS) toegevoegd aan PT topologie
2. SW-CORE-RACK1 verbonden met Peer0 op Gig1/0/23
3. SW-CORE-RACK1 verbonden met R-EDGE Fa1/0 op Gig1/0/24
4. SW-CORE-RACK1 verbonden met R-EDGE2 Fa1/0 op Gig1/0/22
5. Configs geplakt op SW-CORE-RACK1, R-EDGE en R-EDGE2

**IP-plan toegepast (van Aleksander):**
- 10.99.0.2/30 op SW-CORE Gig1/0/23 (naar Peer0)
- 10.99.0.73/30 op SW-CORE Gig1/0/24 (naar R-EDGE)
- 10.99.0.74/30 op R-EDGE Fa1/0
- 10.99.0.77/30 op SW-CORE Gig1/0/22 (naar R-EDGE2)
- 10.99.0.78/30 op R-EDGE2 Fa1/0

**Probleem opgelost:**
- R-EDGE2 hing in OSPF 2WAY/DROTHER state ipv FULL
- Oorzaak: default broadcast mode op Ethernet doet DR/BDR verkiezing op /30 (onnodig)
- Fix: `ip ospf network point-to-point` op alle inter-rack interfaces
- Resultaat: beide R-EDGE en R-EDGE2 FULL/- (correct voor P2P)

---

## ~23/05/2026 — Inter-rack multiuser verbinding tot stand gebracht

**Wat gedaan:**
- Peer0 geconfigureerd met Aleksander's gegevens (LuchHaven002 -> LuchHaven001)
- Multiuser PT sessie actief: Peer0 link UP
- OSPF process 25: derde neighbor verscheen: Aleksander (10.99.0.1) FULL op Gig1/0/23
- NAT geconfigureerd op R-EDGE (later weer verwijderd, zie 17/06)

**Bewijs:**
- show ip ospf neighbor: 3 neighbors (R-EDGE, R-EDGE2, Aleksander)
- OSPF log: "Process 25, Nbr 10.99.0.1 on Gig1/0/23 from LOADING to FULL"

**Status einde mei:** inter-rack OSPF werkt, end-to-end PC->PC ping nog niet opgelost.

---

## 17/06/2026 — Coach-meeting + cruciale feedback Yvan Rooseleer

**Feedback Yvan (samenvatting):**

1. NAT/PAT uitzetten intern
   - OSPF werkt niet over NAT
   - NAT mag alleen op de edge router richting internet (niet tussen racks)
   - Intern communiceren alle racks via pure OSPF routing (geen NAT)

2. Gewenste eindarchitectuur:
   - Alle racks verbonden via OSPF (inter-rack process 25)
   - OSPF adverteert interne netten (10.1.x.x) naar alle andere racks
   - 1 of 2 edge routers met HSRP/VRRP als gateway naar internet
   - NAT/PAT alleen op die internet-edge routers (richting WAN)

3. Multiuser is een sterk punt voor de presentatie
   - "De multiuser faciliteit van PT simuleert partnerbedrijven die met elkaar samenwerken"
   - Dit vermelden in de inleiding van het project

4. Vereisten voor presentatie 19 juni:
   - Elke student begrijpt zijn eigen rack volledig
   - PT topologie mooi en correct gemaakt
   - Documentatie met diagrammen + screenshots met uitleg
   - Tekst is belangrijker dan screenshots — screenshot dient als bewijs
   - Bewijs dat het werkt (show commands + pings)

---

## 17/06/2026 — Implementatie Yvan's feedback (versie 2)

**Wat gedaan:**
1. NAT volledig verwijderd op R-EDGE
   - `no ip nat inside source list 15 pool RACK1_PUBLIC overload`
   - `no ip nat pool RACK1_PUBLIC`
   - `no access-list 15`
   - `no ip nat inside/outside` op alle interfaces
   - Statische route naar 10.200.0.8/29 verwijderd

2. OSPF redistribute uitgebreid op R-EDGE en R-EDGE2
   - `redistribute ospf 1 subnets` in router ospf 25
   - `redistribute ospf 25 subnets` in router ospf 1
   - Resultaat: interne 10.1.x.x VLANs nu zichtbaar voor andere racks

3. HSRP geprobeerd op R-EDGE Fa0/0 met VIP 10.1.199.1
   - Werkt op R-EDGE (Active priority 110)
   - Werkt op R-EDGE2 (Listen priority 100)
   - Maar: beide zitten op verschillende segmenten — HSRP hello's bereiken elkaar niet
   - Beslissing: HSRP laten zoals het is, niet kritiek voor presentatie

4. PT crash + recovery
   - PT crashte tijdens werkzaamheden
   - Herstart vanaf Rack1_v6.pkt uit Drive backup
   - Snel opnieuw geconfigureerd: NAT weg + redistribute + point-to-point OSPF

5. Tweede SW-CORE-RACK2 toegevoegd voor redundantie
   - Niet volledig geconfigureerd (geen tijd voor)
   - Topology aanwezig in PT als toekomstige uitbreiding

6. Multiuser test succesvol
   - LuchHaven002 verbonden met Aleksander's LuchHaven001
   - OSPF FULL neighbor met Aleksander op 10.99.0.1
   - Interne pings tussen VLANs (PC-A1 -> 10.1.110.1, 10.1.120.1, 10.1.130.1) = WERKT
   - Inter-rack pings naar specifieke IPs nog te testen samen met Aleksander

**Eindstatus 17/06:**
- ✅ R-EDGE = 3 FULL OSPF neighbors (SW-DIST, R-EDGE2, SW-CORE)
- ✅ Alle 12 interne VLANs zichtbaar in route table via OSPF
- ✅ Default route via OSPF E2 van R-EDGE2 (Serial backup pad)
- ✅ NAT volledig weg (zoals Yvan vroeg)
- ✅ Multiuser verbinding actief met Aleksander
- ⏸️ End-to-end inter-rack PC->PC ping nog te testen met Aleksander
- ⏸️ HSRP op transit niet operationeel (bewust uitgesteld)

---

## TODO voor 19/06/2026 (presentatie)

1. Screenshots maken van werkende states:
   - show ip ospf neighbor (3 FULL)
   - show ip route (alle VLANs)
   - Interne ping PC-A1 -> 10.1.110.1
   - Topologie diagram met SW-CORE-RACK1 + LuchHaven002
   - Multiuser verbinding actief

2. Test eind-tot-eind met Aleksander:
   - Aleksander geeft IPs van zijn PCs
   - Salah pingt vanaf PC-A1 naar Aleksander's PC
   - Screenshot van succesvolle ping = bewijs

3. Presentatie voorbereiden:
   - Inleiding: multiuser simuleert samenwerking partnerbedrijven Brussels Airport
   - Architectuur uitleggen
   - Lessons learned tonen (zie sectie hieronder)

---

## Lessons learned (cumulatief — voor in eindrapport en presentatie)

### 1. OSPF op /30 = altijd point-to-point mode forceren
- Symptom: 2WAY/DROTHER state, geen FULL adjacency
- Fix: `ip ospf network point-to-point` op beide kanten
- Reden: default broadcast mode doet DR/BDR verkiezing op /30 = onnodig en breekt sync
- Geleerd: bij elke nieuwe /30 link in PT direct point-to-point zetten

### 2. Twee OSPF processen praten niet automatisch
- Process 1 (intern Rack 1) en process 25 (inter-rack) zijn geïsoleerd
- Fix: `redistribute ospf X subnets` in beide processen
- Resultaat: interne 10.1.x.x VLANs zichtbaar voor andere racks

### 3. NAT en OSPF zijn onverenigbaar intern
- OSPF pakketten worden niet vertaald door NAT
- Oplossing: NAT alleen op internet-edge, niet intern
- Geleerd: ontwerp je netwerk eerst zonder NAT en voeg NAT pas toe aan de rand

### 4. PT Multiuser vereist beide kanten tegelijk online
- Eigen kant klaar is niet genoeg
- Team-planning is cruciaal
- Lokaal testen (beide PT files op één laptop, localhost) is een goede backup

### 5. PT crash = leerles in backup-discipline
- Save vaak (Ctrl+S)
- Houd versies in Drive (v1, v2, v3...)
- Een crash mid-werk kost tijd, maar met backups is herstel snel

### 6. Multiuser feature = sterk verkoopargument
- Het simuleert partnerbedrijven die samenwerken
- Gebruiken in inleiding presentatie
- Yvan: "indrukwekkend voor de jury"

### 7. Presentatie: tekst is belangrijker dan screenshots
- Screenshot bewijst dat het echt is gebeurd
- Maar de uitleg maakt het verschil bij de jury
- Per screenshot een korte technische uitleg
