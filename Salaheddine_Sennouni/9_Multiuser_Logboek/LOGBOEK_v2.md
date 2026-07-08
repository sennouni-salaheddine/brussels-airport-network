# LOGBOEK Multi-user PT — Rack 1

Chronologisch overzicht van werkzaamheden, beslissingen en lessons learned.
Datums in NL-formaat (DD/MM/JJJJ).

---

## 11/05/2026 — Coach-meeting, beslissing CML -> Multi-user

**Wat:** Tijdens coach-meeting met Yvan Rooseleer is besloten om CML te vervangen
door PT Multi-user, na herhaaldelijk falen van Cisco DevNet Sandbox provisioning.

**Deadline:** 14 dagen vanaf 11/05/2026 = 25/05/2026.

**Beslissingen:**
- Aleksander hostet de Multiuser sessie (Listening mode)
- Aleksander hostet ook internet (via default-information originate)
- Inter-rack IP-bereik: 10.99.0.0/24 in /30 subnetten
- OSPF process 25 voor inter-rack (apart van eigen process 1 binnen rack)
- Nieuwe Multilayer Switch 3650 toegevoegd aan elk rack als inter-rack core

---

## 13/05/2026 — Rack 1 multi-user setup voltooid

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

**Problemen opgelost:**
- R-EDGE2 hing in OSPF 2WAY/DROTHER state
- Oorzaak: default broadcast mode op Ethernet doet DR/BDR verkiezing op /30 (onnodig)
- Fix: `ip ospf network point-to-point` op alle inter-rack interfaces
- Resultaat: beide R-EDGE en R-EDGE2 FULL/- (correct voor P2P)

**Bewijs:**
- show ip ospf neighbor SW-CORE-RACK1: 2.2.2.2 FULL/- + 1.1.1.1 FULL/-
- ping 10.99.0.77 vanaf R-EDGE2 = 100% success (5/5)

---

## ~23/05/2026 — Inter-rack multiuser verbinding tot stand gebracht

**Wat gedaan:**
- Peer0 geconfigureerd met Aleksander's gegevens (LuchtHaven002 -> LuchtHaven001)
- Multiuser PT sessie actief: Peer0 link UP
- OSPF process 25: derde neighbor verscheen: Aleksander (10.99.0.1) FULL op Gig1/0/23
- NAT geconfigureerd op R-EDGE:
  - ACL 15: permit any
  - ip nat inside op Gi0/0 + Se2/0
  - ip nat outside op Fa1/0
  - NAT pool RACK1_PUBLIC: 10.200.0.17 - 10.200.0.22 /29
  - ip nat inside source list 15 pool RACK1_PUBLIC overload
  - Statische route: 10.200.0.8/29 via 10.99.0.73 (Aleksander's publiek deel)

**Bewijs:**
- show ip ospf neighbor SW-CORE-RACK1: 3 neighbors (R-EDGE, R-EDGE2, Aleksander)
- OSPF log: "Process 25, Nbr 10.99.0.1 on Gig1/0/23 from LOADING to FULL"
- show ip nat translations: actieve vertalingen 10.200.0.17 <-> 10.200.0.9
- ping 10.99.0.1 van SW-CORE = 100% success
- ping 10.200.0.9 gedeeltelijk (inter-rack infra werkt, end-to-end PC->PC niet)

**Status:** inter-rack routing werkt. End-to-end PC->PC ping nog niet opgelost.

---

## 17/06/2026 — Coach-meeting + cruciale feedback Yvan Rooseleer

**Aanwezig:** Volledige groep + coach Yvan

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
   - Tekst is belangrijker dan screenshots — screenshot dient als bewijs dat het echt is
   - Bewijs dat het werkt (show commands + pings)

**Deadline:** 19 juni 2026 — presentatie voor jury.

---

## TODO — Kritieke stappen voor 19/06/2026

### Stap 1: NAT/PAT uitzetten op R-EDGE (URGENT)
```
no ip nat inside source list 15 pool RACK1_PUBLIC overload
no ip nat pool RACK1_PUBLIC
no access-list 15
interface GigabitEthernet0/0
 no ip nat inside
interface Serial2/0
 no ip nat inside
interface FastEthernet1/0
 no ip nat outside
no ip route 10.200.0.8 255.255.255.248 10.99.0.73
end
write memory
```
Verificatie: show ip nat translations = leeg

### Stap 2: OSPF redistribute uitbreiden zodat interne netten zichtbaar zijn
Op R-EDGE en R-EDGE2:
- router ospf 25 -> redistribute ospf 1 subnets (interne netten naar inter-rack)
- router ospf 1 -> redistribute ospf 25 subnets (inter-rack routes naar intern)
Doel: Aleksander kan pingen naar 10.1.110.x, 10.1.120.x, etc.

### Stap 3: Testen na NAT weg
- ping van SW-CORE-RACK1 naar Aleksander's interne IP
- ping van PC-A1 (VLAN 110) naar Aleksander's PC
- show ip route op R-EDGE: moet Aleksander's interne subnetten zien

### Stap 4: Internet edge routers met HSRP
- R-EDGE en R-EDGE2 als HSRP paar richting internet
- NAT/PAT alleen hier (richting internet, niet intern)
- Alle racks gaan via dit HSRP paar naar buiten

### Stap 5: Documentatie afwerken
- Screenshots van werkende states in map screenshots/
- Naming: YYYYMMDD_apparaat_test.png
- Elke screenshot heeft uitleg (tekst is belangrijker dan de screenshot zelf)
- Topologie-diagram bijwerken met nieuwe apparaten (SW-CORE-RACK1)

### Stap 6: Presentatie klaarmaken
- Inleiding: multiuser simuleert samenwerking tussen partnerbedrijven (Brussels Airport)
- Aantonen dat je het begrijpt, niet alleen dat het werkt
- Diagrammen: netwerktopologie, VLAN-schema, inter-rack architectuur

---

## Lessons learned (cumulatief)

1. OSPF op /30 = altijd `ip ospf network point-to-point` op beide kanten
   - Zonder: 2WAY/DROTHER, geen routes uitgewisseld
   - Met: FULL/-, direct adjacency, geen DR verkiezing

2. Twee OSPF processen praten niet automatisch
   - Redistribute nodig in beide richtingen
   - `redistribute ospf 1 subnets` in process 25 en vice versa

3. NAT en OSPF zijn onverenigbaar intern
   - OSPF pakketten worden niet vertaald door NAT
   - Oplossing: NAT alleen op internet-edge, niet intern

4. PT Multiuser vereist beide kanten tegelijk online
   - Eigen kant klaar is niet genoeg
   - Team-planning is cruciaal

5. Presentatie: tekst > screenshots
   - Screenshot bewijst dat het echt is
   - Maar de uitleg maakt het verschil bij de jury
