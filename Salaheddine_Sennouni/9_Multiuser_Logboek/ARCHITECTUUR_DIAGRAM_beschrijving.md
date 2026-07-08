# Architectuurdiagram Rack 1 — beschrijving om te tekenen

Dit document beschrijft exact wat in het architectuurdiagram moet staan, zodat
je het in draw.io (of PT zelf) snel kan natekenen. Yvan vroeg expliciet om
diagrammen — dit hoort in de Design-sectie van de presentatie.

============================================================
LAAG-STRUCTUUR (teken van boven naar beneden)
============================================================

NIVEAU 1 — INTER-RACK CORE (bovenaan)
  [Peer0 / LuchHaven002]  -- multiuser tunnel naar Aleksander (Rack 2)
        |
  [SW-CORE-RACK1]  (Cisco 3650)
     - Gig1/0/23 -> Peer0        = 10.99.0.2/30
     - Gig1/0/24 -> R-EDGE       = 10.99.0.73/30
     - Gig1/0/22 -> R-EDGE2      = 10.99.0.77/30
     - Gig1/0/21 -> Mehdi (R3)   = 10.99.0.17/30  (voorbereid)
     - Gig1/0/20 -> Adil         = 10.99.0.21/30  (voorbereid)
     - OSPF process 25

NIVEAU 2 — EDGE ROUTERS
  [R-EDGE]  (router)              [R-EDGE2]  (router)
     - router-id 1.1.1.1             - router-id 2.2.2.2
     - Fa1/0 -> SW-CORE 10.99.0.74   - Fa1/0 -> SW-CORE 10.99.0.78
     - Fa0/0 -> SW-DIST 10.1.199.2   - Se2/0 -> R-EDGE 10.1.199.6
     - Se2/0 -> R-EDGE2 10.1.199.5
     - default-information originate  (alleen op R-EDGE)
     - OSPF process 1 + 25 (redistribute met tags)

  Teken een serial-link tussen R-EDGE en R-EDGE2 (10.1.199.4/30).

NIVEAU 3 — DISTRIBUTION (collapsed core)
  [SW-DIST]  (Cisco 3650)         [SW-DIST2]  (Cisco 3650)
     - router-id 10.1.199.1          - router-id 4.4.4.4
     - 13 SVIs (gateways)            - 13 SVIs (HSRP standby)
     - HSRP Active priority 110      - HSRP Standby priority 100
     - STP root bridge priority 4096
     - OSPF process 1

  Teken een trunk-interlink tussen SW-DIST en SW-DIST2.

NIVEAU 4 — ACCESS
  [SW1]        [SW2]        [SW3]   (Cisco 2960)
     - access-poorten per VLAN
     - PortFast + BPDU Guard
     - port security sticky MAC

NIVEAU 5 — EINDAPPARATEN (onderaan, gegroepeerd per zone met kleur)
  Airline A (blauw):   PC-A1, PC-A2   (VLAN 110/111/112)
  Airline B (oranje):  PC-B1, PC-B2   (VLAN 120/121/122)
  Gedeeld (groen):     PC-VOIP, PC-GUEST, printers, IoT (VLAN 130/131/132/140/141)
  Management:          Server (10.1.190.10) in VLAN 190

============================================================
KLEURENLEGENDE (zoals in je PT topology)
============================================================
  Blauw  = Airline A
  Oranje = Airline B
  Groen  = Gedeelde diensten
  Grijs  = Infrastructuur (switches, routers)

============================================================
PIJLEN / LABELS OM TOE TE VOEGEN
============================================================
- Label elke link met het /30 subnet en de IPs
- Markeer OSPF process 1 (intern) in één kleur lijn
- Markeer OSPF process 25 (inter-rack) in een andere kleur lijn
- Zet een wolk-symbool bij Peer0 met tekst "Multiuser -> Rack 2 (Aleksander)"
- Zet een sterretje bij R-EDGE met "default route origin"

============================================================
TITEL VAN HET DIAGRAM
============================================================
"Rack 1 — Nieuwe Vliegtuigmaatschappijen — Logische topologie
 Brussels Airport Enterprise Network — Groep 10"
