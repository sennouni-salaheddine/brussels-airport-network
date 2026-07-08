# IP-plan Multi-user — Rack 1

Versie: v1 (13 mei 2026)
Eigenaar: Salaheddine Sennouni
Status: actief

---

## Inter-rack subnet 10.99.0.0/24

Aleksander reserveerde 10.99.0.0 - 10.99.0.63 voor "intern" gebruik per rack,
en 10.99.0.64 - 10.99.0.255 voor inter-rack point-to-point links.

---

## Rack 1 IP-toewijzing

### Inter-rack links (richting Peer0 / Aleksander)

| Link | Subnet /30 | Mijn IP | Aleksander IP |
|------|-----------|---------|---------------|
| SW-CORE-RACK1 -> Peer0 | 10.99.0.0/30 | 10.99.0.2 | 10.99.0.1 |

### Intern Rack 1 (SW-CORE naar mijn routers)

| Link | Subnet /30 | SW-CORE IP | Router IP |
|------|-----------|-----------|-----------|
| SW-CORE Gig1/0/24 -> R-EDGE Fa1/0 | 10.99.0.72/30 | 10.99.0.73 | 10.99.0.74 |
| SW-CORE Gig1/0/22 -> R-EDGE2 Fa1/0 | 10.99.0.76/30 | 10.99.0.77 | 10.99.0.78 |

### Loopbacks (OSPF router-id)

| Apparaat | Loopback0 | OSPF router-id |
|----------|-----------|----------------|
| SW-CORE-RACK1 | n.v.t. | 10.99.0.2 |
| R-EDGE | 1.1.1.1/32 | 1.1.1.1 |
| R-EDGE2 | 2.2.2.2/32 | 2.2.2.2 |

---

## Andere racks (zoals begrepen — bevestigen met team)

| Rack | Eigenaar | Subnet /30 | Verwachte IP |
|------|----------|-----------|--------------|
| Rack 2 | Aleksander | 10.99.0.0/30 | .1 (kant naar mij) |
| Rack 3 | ? | 10.99.0.4/30 of 10.99.0.8/30 | ? |
| Rack 4 | ? | volgende /30 | ? |

> Vragen aan Aleksander: welk subnet krijgen Rack 3 en Rack 4?

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
