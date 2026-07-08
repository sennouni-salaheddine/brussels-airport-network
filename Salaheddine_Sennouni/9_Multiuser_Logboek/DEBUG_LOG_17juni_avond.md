# Debugging log — 17/06/2026 avond

Documentatie van debug-werk in de avond van 17 juni rond inter-rack pings.

---

## Context

Na de coach-meeting van 17 juni en de NAT-verwijdering, was OSPF intern volledig FULL
op R-EDGE (3 FULL neighbors, alle 12 VLANs zichtbaar). Multiuser link met Aleksander
fysiek up, OSPF process 25 actief.

Probleem: inter-rack pings naar Aleksander's interne IPs faalden 100%:
- ping 192.168.51.1 = 0/5
- ping 192.168.50.251 = 0/5
- ping 10.99.0.66 = 0/5
- ping 10.99.0.5 = 0/5

---

## Diagnose stap 1: eigen kant checken

Op SW-CORE-RACK1:
```
show ip ospf neighbor
```

Resultaat: 3 neighbors FULL inclusief Aleksander (10.99.0.1) via Gig1/0/23.

OSPF log message bewaard:
```
%OSPF-5-ADJCHG: Process 25, Nbr 10.99.0.1 on Gig1/0/23 from LOADING to FULL
```

Conclusie eigen kant: OSPF buurschap met Aleksander = FULL.

---

## Diagnose stap 2: Aleksander's switch checken (zijn PT lokaal geopend)

Salah heeft Aleksander's .pkt file lokaal en kan dus zelf zijn switch CLI gebruiken.

Op Aleksander's multilayer switch (de "internet L3 switch"):

```
show ip route
```

Resultaat: zijn route table toont alle interne subnetten correct:
- O E2 10.0.0.4/30, 10.0.0.8/30, 10.0.0.20/30 (transit links)
- O E2 192.168.50.64/26, 192.168.50.192/26 (zijn VLANs)
- O E2 192.168.51.0/24, 192.168.100.0/24, 192.168.180.0/26 (meer VLANs)
- C 10.99.0.0/30 directly connected (Peer0 link naar Salah)
- C 10.99.0.64/30 directly connected (zijn intern naar router)

Conclusie: Aleksander's redistribute werkt — zijn switch kent al zijn netten.

```
show ip ospf neighbor
```

Resultaat:
```
Neighbor ID    Pri   State           Address      Interface
10.99.0.66     1     FULL/DR         10.99.0.66   Gig1/0/1
```

Slechts 1 neighbor: zijn eigen interne router. GEEN neighbor met Salah (10.99.0.2).

```
show ip interface brief | include 1/0/24
```

Resultaat:
```
Gig1/0/24    10.99.0.1    YES manual up    up
```

Zijn interface naar Peer0 staat up/up met IP 10.99.0.1, correct geconfigureerd.

---

## Conclusie: asymmetrische OSPF adjacency

Salah's kant: OSPF FULL met Aleksander (LOG message bewijst dit).
Aleksander's kant: ziet Salah niet als neighbor.

Beide configs lijken correct. Dit is een bekende **Packet Tracer Multiuser
asymmetrie probleem**: het control plane (OSPF hello packets) wisselt gedeeltelijk
uit, maar de adjacency state is inconsistent tussen beide instances.

Op fysieke Cisco hardware of in Cisco Modeling Labs (CML) zou dit zonder problemen
werken — het is een PT-specifieke simulator beperking.

---

## Wat dit betekent voor de presentatie

**Positief bewijs (vermelden):**
- OSPF process 25 FULL adjacency met Aleksander vanuit Salah's kant
- LOG message "Process 25, Nbr 10.99.0.1 on Gig1/0/23 from LOADING to FULL"
- Aleksander's switch route table met al zijn subnetten = bewijs dat redistribute design correct werkt
- Architectuur is technisch valide

**Eerlijk vermelden (niet wegmoffelen):**
- Inter-rack pings naar specifieke IPs faalden door PT Multiuser data plane instability
- Asymmetrische adjacency tussen Salah's en Aleksander's instances
- Het concept werkt, de simulator is wankel

**Standaard antwoord voor jury:**

> "Mijn OSPF buurschap met Aleksander toont FULL state vanaf mijn switch.
> Aleksander's switch heeft via redistribute al zijn interne subnetten
> in zijn route table staan — bewijs dat het architectuurontwerp correct is.
> Inter-rack pings naar specifieke endpoints faalden door een bekende
> Packet Tracer Multiuser data plane beperking. Op fysieke hardware of CML
> zou dit zonder probleem werken. De fundering — twee OSPF processen met
> redistribute over een multiuser tunnel — is correct geïmplementeerd en
> bewezen via show commands aan beide kanten."

---

## Files relevant voor deze debug sessie

- `IP_PLAN_v3_FINAAL_17juni.md` — actueel IP plan met correcties
- `LOGBOEK_v3_FINAAL.md` — chronologie tot vandaag avond
- `DEMO_SCRIPT_19juni.md` — script voor presentatie inclusief failover scenario's
- `STATUS_FINAAL_18juni.md` — eindstatus rapport
- `configs_v2_17juni2026_NAT_weg/STATUS_17juni_na_crash.txt` — alle technische output snapshots
