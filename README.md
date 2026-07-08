# Brussels Airport Enterprise Network

> **Groepsproject** — Odisee Campus Brussel · Toegepaste Informatica · Cybersecurity & Network Specialist · 2024–2025

Ontwerp en volledige implementatie van een enterprise-netwerkinfrastructuur voor een luchthavenopmgeving met meerdere zones en gebouwen. Het project werd gerealiseerd als groepswerk met een gestructureerde projectmethodologie (planning, design, implementatie, testing).

---

## Technologieën

| Domein | Technologie |
|--------|-------------|
| Routering | OSPF (multi-area) |
| Redundantie | HSRP (First Hop Redundancy Protocol) |
| Segmentatie | VLANs + Inter-VLAN routing |
| Adressering | IPv4 / IPv6 dual-stack |
| Beveiliging | ACLs, Port Security, DHCP Snooping |
| Beheer | SSH v2, AAA |
| Simulatie | Cisco Packet Tracer |

---

## Projectstructuur

```
Salaheddine_Sennouni/
├── 1_HOME_INDEX/          # Projectoverzicht en navigatiedocument
├── 2_MEETINGS/            # Vergaderverslagen
├── 3_PLAN_VAN_AANPAK/     # Projectplan (v5, v6) + requirements interview
├── 4_LEARNING_RESEARCH/   # Onderzoeksdocumenten
├── 5_PLANNING_ANALYSE/    # VLAN-plan, IPv6-plan, naamgeving, rack layout, topologie
├── 6_DETAILPLAN_DESIGN/   # Netwerk design, ACL-matrix, DHCP/DNS, switchports
├── 7_IMPLEMENTATIE/       # Implementatiestatus + volledige device configs
├── 7_Packet_Tracer/       # Packet Tracer bestanden (v1 t/m v8)
├── 8_TESTING_EVALUATIE/   # Testplan, testresultaten, evaluatierapport, screenshots
└── 9_Multiuser_Logboek/   # Weeklogboek groepswerk
```

---

## Hoogtepunten

- **8 iteraties** van de Packet Tracer topologie (v1 → v8), gedocumenteerd per versie
- **VLAN- en IPv6-planning** uitgewerkt in Excel met volledige adresseringstabellen
- **ACL-matrix** voor inter-zone beveiliging tussen luchthavensegmenten
- **Verificatie** met screenshots en een gestructureerd testrapport
- **DHCP Snooping** en port security als extra beveiligingslaag op access-niveau

---

## Badges

![Cisco](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?logo=cisco&logoColor=white)
![OSPF](https://img.shields.io/badge/Routing-OSPF%20Multi--Area-blue)
![IPv6](https://img.shields.io/badge/Adressering-IPv4%2FIPv6%20Dual--Stack-informational)
![HSRP](https://img.shields.io/badge/Redundantie-HSRP-success)
![ACL](https://img.shields.io/badge/Beveiliging-ACLs%20%2B%20Port%20Security-critical)

