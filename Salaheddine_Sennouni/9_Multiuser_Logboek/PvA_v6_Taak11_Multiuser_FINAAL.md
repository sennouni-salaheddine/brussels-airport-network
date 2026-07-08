# Plan van Aanpak v6 — Taak 11 Multi-user PT (vervangt CML Prototype)

Deze sectie vervangt de oorspronkelijke Taak 11 "CML Prototype" uit PvA v5.
Te plakken in PvA v5 op de plaats waar nu Taak 11 staat.

---

# Taak 11 — Multi-user PT (Sprint 9)

## 11.1 Context en aanleiding

Bij deze omschakeling is Taak 11 oorspronkelijk gedefinieerd als "CML Prototype"
opnieuw ingevuld als Multi-user PT verificatie.

Tijdens de coach-meeting van 11 mei 2026 is in samenspraak met Yvan Rooseleer
besloten om de oorspronkelijk geplande Cisco Modeling Labs (CML) verificatie
te vervangen door Packet Tracer Multi-user. Reden: de Cisco DevNet Sandbox
voor CML bleek herhaaldelijk te falen tijdens provisioning ("Torque was not
able to complete launching your environment"), met als gevolg dat de geplande
verificatie niet kon doorgaan.

Multi-user PT biedt een gelijkwaardig alternatief: de vier afzonderlijke racks
worden via PT's ingebouwde Multiuser Connection feature met elkaar verbonden,
zodat het volledige Brussels Airport netwerk in één gesimuleerde omgeving werkt.

Op 17 juni 2026 heeft Yvan Rooseleer in een vervolgmeeting bevestigd dat
multi-user PT een sterke keuze is. Tijdens deze meeting motiveerde hij dat
de Multi-user opzet partnerbedrijven simuleert die samenwerken — dit motiveert
om PT Multiuser als verificatie te kiezen i.p.v. CML te re-proberen.

---

## 11.2 Architectuur

### Inter-rack design

```
Rack 1 (Salaheddine)  <-- Peer0 -->  Rack 2 (Aleksander)
```

Beslissingen op architectuurniveau:
- Aleksander hostet de Multiuser sessie (Listening mode)
- Inter-rack IP-bereik: 10.99.0.0/24 in /30 subnetten
- OSPF process 25 voor inter-rack (apart van eigen process 1 binnen rack)
- Nieuwe Multilayer Switch 3650 toegevoegd aan elk rack als inter-rack core
- Multilayer Switch in Rack 1: SW-CORE-RACK1
- Tweede SW-CORE-RACK2 aanwezig in topology voor toekomstige redundantie

Internet-uplink valt buiten scope van deze sprint. Default route wijst nu
naar het serial backup-pad tussen R-EDGE en R-EDGE2. Een echte WAN-verbinding
met NAT/PAT op de internet-edge wordt aangepakt in een volgende iteratie.

### Apparaten toegevoegd t.o.v. PT v4

| Apparaat | Type | Functie |
|----------|------|---------|
| SW-CORE-RACK1 | 3650-24PS | Inter-rack core, verbinding met Peer0 |
| LuchHaven002 | Peer node | Multiuser tunnel naar Aleksander's LuchHaven001 |
| SW-CORE-RACK2 | 3650-24PS | Tweede core voor redundantie (toekomstige uitbreiding) |

---

## 11.3 Configuratie wijzigingen

### NAT verwijderd (Yvan feedback 17/06/2026)

Reden: OSPF werkt niet over NAT. Voor interne communicatie tussen racks
moet pure routing gebruikt worden, geen address translation.

Wat verwijderd is op R-EDGE en R-EDGE2:
- ACL 15 + NAT pool RACK1_PUBLIC
- `ip nat inside` en `ip nat outside` op alle interfaces
- Statische routes naar publieke /29 ranges

### OSPF process 25 toegevoegd op SW-CORE-RACK1

```
router ospf 25
 router-id 10.99.0.2
 network 10.99.0.0 0.0.0.3 area 0
 network 10.99.0.72 0.0.0.3 area 0
 network 10.99.0.76 0.0.0.3 area 0
```

### Redistribute tussen OSPF process 1 en 25 op R-EDGE en R-EDGE2

```
router ospf 25
 redistribute ospf 1 subnets
router ospf 1
 redistribute ospf 25 subnets
```

### Point-to-point OSPF op alle /30 links

```
interface XYZ
 ip ospf network point-to-point
```

Probleem ontdekt: default broadcast mode op Ethernet doet onnodige DR/BDR
verkiezing op /30 links, wat de adjacency in 2WAY/DROTHER laat hangen.

---

## 11.4 Verificatie

### OSPF buurschap

| Apparaat | Neighbor ID | Address | State | Interface |
|----------|------------|---------|-------|-----------|
| R-EDGE | 10.1.199.1 | 10.1.199.1 | FULL/- | Fa0/0 (naar SW-DIST) |
| R-EDGE | 4.4.4.4 | 10.1.199.6 | FULL/- | Se2/0 (naar R-EDGE2) |
| R-EDGE | 10.99.0.2 | 10.99.0.73 | FULL/- | Fa1/0 (naar SW-CORE-RACK1) |
| SW-CORE-RACK1 | 2.2.2.2 | 10.99.0.78 | FULL/- | Gig1/0/22 (naar R-EDGE2) |
| SW-CORE-RACK1 | 1.1.1.1 | 10.99.0.74 | FULL/- | Gig1/0/24 (naar R-EDGE) |
| SW-CORE-RACK1 | (Aleksander) | 10.99.0.1 | FULL/- | Gig1/0/23 (naar Peer0 / Rack 2) |

### Route table verificatie

R-EDGE leert via OSPF process 1 alle interne VLANs:
- O 10.1.110.0/24 t/m 10.1.190.0/24 (alle 12 user VLANs)
- C 10.1.199.0/30 directly connected
- C 10.99.0.72/30 directly connected
- O 10.99.0.76/30 [110/2] via 10.99.0.73 (R-EDGE2 link)
- S* 0.0.0.0/0 via 10.1.199.6 (statisch op R-EDGE, verspreid via OSPF)
- Andere apparaten zien deze default route als O*E2 (OSPF extern type 2)

### Interne ping verificatie

| Bron | Bestemming | Resultaat |
|------|-----------|-----------|
| PC-A1 | 10.1.110.1 | Reply (interne VLAN gateway) |
| PC-A1 | 10.1.120.1 | Reply (cross-VLAN naar Airline B gateway) |
| PC-A1 | 10.1.130.1 | Reply (VoIP gateway) |
| PC-A1 | 10.1.190.1 | Reply (management) |
| PC-A1 | PC-B1 | Request timed out (ACL bewijs — Airline A/B geblokkeerd) |

### Multiuser link

- LuchHaven002 (eigen) <-> LuchHaven001 (Aleksander) = verbonden
- Gig1/0/23 op SW-CORE-RACK1 = up/up
- OSPF process 25 actief op deze interface

---

## 11.5 Lessons learned

1. **OSPF op /30 vereist altijd `ip ospf network point-to-point`** op beide kanten.
   Zonder dit blijven buren in 2WAY/DROTHER door onnodige DR verkiezing.

2. **Twee OSPF processen praten niet automatisch.** Redistribute in beide richtingen
   is verplicht om routes uit te wisselen.

3. **NAT en OSPF zijn onverenigbaar intern.** OSPF pakketten worden niet vertaald,
   dus NAT moet alleen op de internet-edge.

---

## 11.6 Status en planning

| Item | Status |
|------|--------|
| SW-CORE-RACK1 + Peer0 verbinding | Geverifieerd |
| OSPF process 25 inter-rack | Geverifieerd |
| Interne VLANs via OSPF gedeeld met andere racks | Geverifieerd |
| NAT verwijderd intern | Geverifieerd |
| Default route via OSPF | Geverifieerd |
| Multiuser link met Aleksander | Geverifieerd |
| End-to-end PC->PC ping inter-rack | In progress (samen met Aleksander) |
| HSRP tussen R-EDGE en R-EDGE2 op transit segment | Geconfigureerd maar geparkeerd — segmenten verschillen, hello's bereiken elkaar niet, bewust niet kritiek voor presentatie. Redundantie blijft via OSPF herconvergentie via serial backup-pad. |
| SW-CORE-RACK2 redundantie config | Toekomstige iteratie |

**Deadline presentatie:** 19/06/2026.
