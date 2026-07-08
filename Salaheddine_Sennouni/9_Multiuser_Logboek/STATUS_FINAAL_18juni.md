# STATUS FINAAL — Rack 1 — 17 juni 2026

Document opgesteld einde 17 juni 2026 als referentie voor presentatie 19 juni.

---

## Huidige PT bestand

**Bestandsnaam:** `Rack1_v7_17juni.pkt`
**Locatie lokaal:** bureaublad (te uploaden naar Drive 7_Packet_Tracer)

---

## Wat WERKT (bevestigd op 17/06/2026)

### Interne netwerk Rack 1
- 13 VLANs aangemaakt en geconfigureerd
- DHCP, NTP, OSPF, HSRP volledig operationeel (uit PT v4)
- ACLs actief: Airline A en B gescheiden, Guest WiFi geïsoleerd
- Server in VLAN 190 verstrekt DHCP/DNS/NTP aan alle VLANs

### OSPF status R-EDGE
- 3 FULL neighbors:
  - 10.1.199.1 (SW-DIST) op FastEthernet0/0
  - 4.4.4.4 (R-EDGE2) op Serial2/0
  - 10.99.0.2 (SW-CORE-RACK1) op FastEthernet1/0

### Route table R-EDGE
- Alle 12 interne VLANs (10.1.110.0 - 10.1.190.0) zichtbaar via OSPF
- 10.99.0.72/30 directly connected (link naar SW-CORE)
- 10.99.0.76/30 via OSPF (link via SW-CORE naar R-EDGE2)
- 0.0.0.0/0 default route via 10.1.199.6 (Serial backup)

### Multiuser verbinding
- LuchHaven002 (eigen Peer) verbonden met Aleksander's LuchHaven001
- OSPF process 25 actief op SW-CORE-RACK1 Gig1/0/23
- Status: link UP, fysiek OK

### Verificatie pings (intern)
- PC-A1 -> 10.1.110.1: success
- PC-A1 -> 10.1.120.1: success
- PC-A1 -> 10.1.130.1: success
- PC-A1 -> 10.1.190.1: success

---

## Wat NIET werkt (eerlijk gedocumenteerd)

### Inter-rack ping naar Aleksander
- Status: 0% success op pings naar 192.168.51.1, 192.168.50.251, 10.99.0.66, 10.99.0.5
- Mogelijke oorzaken:
  - Aleksander's OSPF niet volledig FULL aan zijn kant
  - Verschil in IP-plan tussen onze racks
- Volgende stap: synchroniseren met Aleksander voor 19/06

### HSRP op transit segment R-EDGE/R-EDGE2
- Probleem: R-EDGE en R-EDGE2 zitten op verschillende segmenten
- HSRP hello's bereiken elkaar niet
- Bewust uitgesteld — geen impact op core functionaliteit
- Redundantie blijft aanwezig via OSPF herconvergentie

### SW-CORE-RACK2 toegevoegd in topology
- Tweede 3650 aanwezig voor toekomstige redundantie
- Niet volledig geconfigureerd (geen tijd voor)
- Kan in volgende iteratie afgewerkt worden

---

## Configuratie samenvatting

### Wat is anders vs PT v4 (origineel)

| Wijziging | Reden |
|-----------|-------|
| SW-CORE-RACK1 toegevoegd | Inter-rack core switch voor multiuser |
| OSPF process 25 op SW-CORE en routers | Apart van intern OSPF process 1 |
| Redistribute tussen OSPF 1 en 25 | Routes uitwisselen tussen intern en inter-rack |
| `ip ospf network point-to-point` op alle /30 links | FULL adjacency, geen DR verkiezing |
| NAT volledig verwijderd | Yvan feedback: OSPF werkt niet over NAT |
| Default route via OSPF E2 | Komt nu via OSPF redistribute, niet meer statisch geconfigureerd |

---

## Drive structuur na 17/06

```
Salaheddine_Sennouni/
├── 1_HOME_INDEX/
├── 2_MEETINGS/                    # Meeting notes
├── 3_PLAN_VAN_AANPAK/             # PvA v5 (v6 update nodig)
├── 4_LEARNING_RESEARCH/
├── 5_PLANNING_ANALYSE/
├── 6_DETAILPLAN_DESIGN/
├── 7_IMPLEMENTATIE/
├── 7_Packet_Tracer/               # Rack1_v7_17juni.pkt komt hier
│   └── CML_configs/               # CML poging uit mei (geannuleerd)
├── 8_TESTING_EVALUATIE/
└── 9_Multiuser_Logboek/           # Multiuser werk
    ├── README.md
    ├── LOGBOEK.md                 # OUD - tot 13 mei
    ├── LOGBOEK_v2.md              # tot 17 juni middag
    ├── LOGBOEK_v3_FINAAL.md       # tot 17 juni einde dag
    ├── IP_PLAN_MULTIUSER.md       # OUD
    ├── IP_PLAN_v2_17juni.md       # ACTUEEL
    ├── team_communicatie.md
    ├── STATUS_17juni_na_crash.txt # technical snapshot
    ├── STATUS_FINAAL_18juni.md    # dit document
    ├── configs_v1_13mei2026/      # eerste werkende configs
    ├── configs_v2_17juni2026_NAT_weg/  # NAT weg + redistribute
    │   ├── R-EDGE_v2_delta.txt
    │   ├── R-EDGE2_v2_delta.txt
    │   ├── HSRP_edge_routers.txt
    │   └── STATUS_17juni_na_crash.txt
    └── screenshots/               # nog aan te vullen
```

---

## Resterende taken (avond 17/06 + 18/06)

1. ☐ Upload Rack1_v7_17juni.pkt naar Drive 7_Packet_Tracer
2. ☐ Screenshots maken van werkende states
3. ☐ Test inter-rack ping met Aleksander
4. ☐ Plan van Aanpak v6 met nieuwe multiuser sectie
5. ☐ Eindverslag samenstellen voor jury 19/06
