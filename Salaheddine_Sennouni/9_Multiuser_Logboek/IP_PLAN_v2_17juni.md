# IP-plan Multi-user — Rack 1 — VERSIE 2

Versie: v2 (17 juni 2026)
Eigenaar: Salaheddine Sennouni
Status: actief — gebruikt in Rack1_v7_17juni.pkt

---

## Wijzigingen t.o.v. v1 (13 mei)

- NAT volledig verwijderd op R-EDGE en R-EDGE2 (Yvan feedback 17/06)
- OSPF redistribute uitgebreid: interne 10.1.x.x nu zichtbaar voor andere racks
- Tweede SW-CORE-RACK2 toegevoegd in topology (nog niet volledig geconfigureerd)
- Multiuser actief: LuchHaven002 verbonden met Aleksander's LuchHaven001

---

## Inter-rack subnet 10.99.0.0/24

Aleksander reserveerde 10.99.0.0 - 10.99.0.63 voor inter-rack point-to-point,
en 10.99.0.64 - 10.99.0.255 voor intern gebruik per rack.

---

## Rack 1 IP-toewijzing (actief)

### Inter-rack links (richting Peer0 / Aleksander)

| Link | Subnet /30 | Mijn IP | Aleksander IP |
|------|-----------|---------|---------------|
| SW-CORE-RACK1 -> Peer0 | 10.99.0.0/30 | 10.99.0.2 | 10.99.0.1 |

### Intern Rack 1 (SW-CORE naar mijn routers)

| Link | Subnet /30 | SW-CORE IP | Router IP |
|------|-----------|-----------|-----------|
| SW-CORE Gig1/0/24 -> R-EDGE Fa1/0 | 10.99.0.72/30 | 10.99.0.73 | 10.99.0.74 |
| SW-CORE Gig1/0/22 -> R-EDGE2 Fa1/0 | 10.99.0.76/30 | 10.99.0.77 | 10.99.0.78 |

### Transit links (intern naar SW-DIST)

| Link | Subnet | IP |
|------|--------|-----|
| R-EDGE Fa0/0 -> SW-DIST Gig1/0/4 | 10.1.199.0/30 | R-EDGE = .2, SW-DIST = .1 |
| R-EDGE Serial2/0 -> R-EDGE2 Se2/0 | 10.1.199.4/30 | R-EDGE = .5, R-EDGE2 = .6 |

### Loopbacks (OSPF router-id)

| Apparaat | Router-ID |
|----------|-----------|
| SW-CORE-RACK1 | 10.99.0.2 (= interface IP) |
| R-EDGE | 1.1.1.1 |
| R-EDGE2 | 2.2.2.2 |
| SW-DIST | 10.1.199.1 |
| SW-DIST2 | 4.4.4.4 |

---

## OSPF processen

### Process 1 (intern Rack 1)
- Tussen SW-DIST, SW-DIST2, R-EDGE, R-EDGE2
- Adverteert alle interne VLANs (10.1.110.x - 10.1.190.x)
- Redistribute van process 25 (om externe routes te leren)

### Process 25 (inter-rack)
- Tussen SW-CORE-RACK1, R-EDGE, R-EDGE2, en Aleksander
- Adverteert 10.99.0.x subnetten
- Redistribute van process 1 (om interne netten te delen met andere racks)
- Default route komt via E2 van R-EDGE2 (Serial backup pad)

---

## Interne netwerk Rack 1 (10.1.0.0/16, ongewijzigd t.o.v. PT v4)

| VLAN | Naam | Subnet | Gateway (HSRP VIP) |
|------|------|--------|---------------------|
| 110 | R1_A_OFFICE | 10.1.110.0/24 | 10.1.110.1 |
| 111 | R1_A_CHECKIN | 10.1.111.0/24 | 10.1.111.1 |
| 112 | R1_A_GATES | 10.1.112.0/24 | 10.1.112.1 |
| 120 | R1_B_OFFICE | 10.1.120.0/24 | 10.1.120.1 |
| 121 | R1_B_CHECKIN | 10.1.121.0/24 | 10.1.121.1 |
| 122 | R1_B_GATES | 10.1.122.0/24 | 10.1.122.1 |
| 130 | R1_VOIP | 10.1.130.0/24 | 10.1.130.1 |
| 131 | R1_IOT | 10.1.131.0/24 | 10.1.131.1 |
| 132 | R1_PRINTERS | 10.1.132.0/24 | 10.1.132.1 |
| 140 | R1_WIFI_STAFF | 10.1.140.0/24 | 10.1.140.1 |
| 141 | R1_WIFI_GUEST | 10.1.141.0/24 | 10.1.141.1 |
| 190 | R1_NET_MGMT | 10.1.190.0/24 | 10.1.190.1 |
| 199 | R1_TRANSIT | 10.1.199.0/24 (in /30 stukken) | n.v.t. |

---

## IPs die andere racks kunnen pingen (delen met team)

**Inter-rack:** 10.99.0.2 (SW-CORE-RACK1)

**Interne VLANs via OSPF:**
- 10.1.110.1 - 10.1.141.1 (alle VLAN gateways)
- 10.1.190.1 (management)

**Andere racks beschikbaar (vragen aan team):**
- Rack 2 (Aleksander): 10.99.0.1
- Rack 3 (?): nog te bevestigen
- Rack 4 (?): nog te bevestigen
