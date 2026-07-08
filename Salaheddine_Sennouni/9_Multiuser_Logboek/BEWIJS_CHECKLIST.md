# Bewijs checklist — Rack 1 Multi-user

Screenshots die nog gemaakt en in `screenshots/` map gezet moeten worden.

Naming convention: `YYYYMMDD_apparaat_test.png`

---

## Categorie 1: OSPF buurschap (KRITIEK BEWIJS)

- [ ] `20260617_R-EDGE_ospf_neighbor_3_FULL.png`
      Inhoud: show ip ospf neighbor op R-EDGE, toont 3 FULL neighbors
- [ ] `20260617_SW-CORE-RACK1_ospf_neighbor.png`
      Inhoud: show ip ospf neighbor op SW-CORE, toont R-EDGE en R-EDGE2 FULL
- [ ] `20260617_R-EDGE2_ospf_neighbor.png`
      Inhoud: show ip ospf neighbor op R-EDGE2

---

## Categorie 2: Route tables (BEWIJS DAT OSPF ROUTES UITWISSELT)

- [ ] `20260617_R-EDGE_show_ip_route.png`
      Inhoud: alle 12 VLANs via OSPF, default route, inter-rack subnets
- [ ] `20260617_SW-CORE-RACK1_show_ip_route.png`
      Inhoud: route table na multiuser verbinding
- [ ] `20260617_R-EDGE2_show_ip_route.png`

---

## Categorie 3: Interne pings (BEWIJS DAT INTERN WERKT)

- [x] `20260617_PCA1_ping_10.1.110.1.png` (al gemaakt — toont reply)
- [x] `20260617_PCA1_ping_10.1.120.1.png` (al gemaakt — cross-VLAN naar Airline B)
- [ ] `20260617_PCA1_ping_PCB1.png`
      Inhoud: ping van PC-A1 naar PC-B1 — moet falen vanwege ACL
- [ ] `20260617_PCA1_ping_10.1.190.10.png`
      Inhoud: ping naar Server

---

## Categorie 4: Multiuser bewijs (KRITIEK)

- [ ] `20260617_topology_LuchHaven002_groene_driehoek.png`
      Inhoud: PT topology screenshot waarop LuchHaven002 cloud geel/verbonden is
- [ ] `20260617_beide_PT_vensters.png`
      Inhoud: jouw PT + Aleksander's PT naast elkaar (printscreen volledige scherm)
- [ ] `20260617_SW-CORE_log_OSPF_neighbor_aleksander_FULL.png`
      Inhoud: OSPF log message: "Process 25, Nbr X.X.X.X on Gig1/0/23 from LOADING to FULL"

---

## Categorie 5: Inter-rack pings (NA TEST MET ALEKSANDER)

- [ ] `20260617_SW-CORE_ping_aleksander_10.99.0.1.png`
      Inhoud: ping naar Aleksander's directe IP
- [ ] `20260617_PCA1_ping_aleksanderPC.png`
      Inhoud: ping van PC-A1 naar een PC in Aleksander's rack (eind-tot-eind)

---

## Categorie 6: Configuraties (REFERENTIE)

- [ ] `20260617_R-EDGE_running_config.png`
      Inhoud: show running-config | begin router ospf
- [ ] `20260617_SW-CORE_running_config.png`

---

## Categorie 7: Topologie overzicht (VOOR PRESENTATIE)

- [ ] `20260617_topology_overview_full.png`
      Inhoud: volledig PT canvas met alle apparaten zichtbaar
- [ ] `20260617_topology_zoom_inter_rack.png`
      Inhoud: zoom op SW-CORE-RACK1, R-EDGE, R-EDGE2, Peer0

---

## Hoe screenshots maken in Packet Tracer

1. Open het CLI venster van het apparaat
2. Druk Windows+Shift+S voor snipping
3. Selecteer het console venster
4. Save als PNG met de juiste naamgeving
5. Upload naar Drive `9_Multiuser_Logboek/screenshots/`

Of voor topology:
1. PT bovenste menu -> File -> Print Preview
2. Of gewoon Windows+Shift+S op het canvas

---

## Status overzicht

| Categorie | Aantal nodig | Gemaakt | Status |
|-----------|--------------|---------|--------|
| OSPF buurschap | 3 | 0 | TODO |
| Route tables | 3 | 0 | TODO |
| Interne pings | 4 | 2 | 50% |
| Multiuser | 3 | 0 | TODO |
| Inter-rack | 2 | 0 | TODO (met Aleksander) |
| Configs | 2 | 0 | TODO |
| Topologie | 2 | 0 | TODO |
| **TOTAAL** | **19** | **2** | **11%** |
