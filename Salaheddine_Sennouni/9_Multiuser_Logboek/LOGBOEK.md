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
5. Configs geplakt op SW-CORE-RACK1, R-EDGE en R-EDGE2 (zie configs_v1/)

**IP-plan toegepast (van Aleksander):**

- 10.99.0.2/30 op SW-CORE Gig1/0/23 (naar Peer0)
- 10.99.0.73/30 op SW-CORE Gig1/0/24 (naar R-EDGE)
- 10.99.0.74/30 op R-EDGE Fa1/0
- 10.99.0.77/30 op SW-CORE Gig1/0/22 (naar R-EDGE2)
- 10.99.0.78/30 op R-EDGE2 Fa1/0

**Problemen opgelost:**

- Symptoom 1: R-EDGE2 hing in OSPF 2WAY/DROTHER state ipv FULL
- Oorzaak: OSPF stond in broadcast mode (default op Ethernet),
  wat een DR/BDR verkiezing veroorzaakte op een /30 point-to-point link
- Fix: `ip ospf network point-to-point` toegevoegd op alle inter-rack interfaces
- Result: beide R-EDGE en R-EDGE2 nu FULL/- (zonder DR rol = correct voor P2P)

**Bewijs:**

- show ip ospf neighbor op SW-CORE-RACK1:
  - 2.2.2.2 FULL/- 10.99.0.78 Gig1/0/22 (R-EDGE2)
  - 1.1.1.1 FULL/- 10.99.0.74 Gig1/0/24 (R-EDGE)
- show ip ospf neighbor op R-EDGE2:
  - 10.99.0.2 FULL/DR 10.99.0.77 Fa1/0 (SW-CORE)
- ping 10.99.0.77 vanaf R-EDGE2 = 100% success (5/5)

**Status:** eigen rack klaar, wacht op Aleksander voor inter-rack test.

---

## TODO — Volgende sessies

### Sessie 2 met Aleksander

- [ ] Peer0 config invullen: zijn IP, poort, wachtwoord
- [ ] Connection Status op Enabled zetten
- [ ] Aleksander zijn PT in Listen mode controleren
- [ ] Verifiëren: show ip ospf neighbor toont 3e neighbor (Aleksander, 10.99.0.1)
- [ ] Ping test: vanaf SW-CORE ping 10.99.0.1
- [ ] Ping test: vanaf PC in VLAN 110 (Rack 1) -> PC in Aleksander's rack
- [ ] Screenshot maken van beide kanten = bewijs voor coach
- [ ] Default route check: O*E2 0.0.0.0/0 via 10.99.0.1 in route table

### Tussentijdse acties

- [ ] Default-information originate weghalen op R-EDGE/R-EDGE2 (default moet van Aleksander komen)
- [ ] Redistribute tussen OSPF 1 en OSPF 25 testen
- [ ] Wireshark capture maken van OSPF hello packets (extra bewijs)

---

## Lessons learned (voor in eindrapport)

1. **OSPF op /30 = point-to-point mode forceren**
   - Symptom: 2WAY/DROTHER state, geen FULL
   - Fix: `ip ospf network point-to-point` op beide kanten
   - Reden: default broadcast mode doet DR verkiezing op /30 = onnodig en breekt sync

2. **Twee OSPF processen praten niet automatisch**
   - Process 1 (intern Rack 1) en process 25 (inter-rack) zijn geisoleerd
   - Fix: `redistribute ospf X subnets` in beide processen voor route exchange

3. **PT Multiuser = niet test-baar zonder remote partij**
   - Eigen kant kan volledig klaar staan
   - Maar pingen kan pas wanneer beide kanten tegelijk online zijn
   - Goed plannen met team is dus essentieel
