# 9_Multiuser_Logboek

Werkmap voor de multi-user PT verificatie van Rack 1 (Salaheddine Sennouni).
Doel: alle wijzigingen, configs, tests en lessons learned op één plek houden,
met versie-nummering zodat niets verloren gaat.

---

## Inhoud van deze map

| Bestand | Doel |
|---------|------|
| `README.md` | Dit overzicht — eerst lezen |
| `LOGBOEK.md` | Chronologisch logboek van wat er gebeurt (datum + acties) |
| `IP_PLAN_MULTIUSER.md` | Single source of truth voor inter-rack IP-plan |
| `configs_v1/` | Eerste werkende versie van multi-user configs (13 mei 2026) |
| `configs_vN/` | Latere versies (worden toegevoegd bij wijzigingen) |
| `screenshots/` | Bewijs van werkende states (OSPF FULL, ping success, etc.) |
| `team_communicatie.md` | Wat is afgesproken met Aleksander/team |

---

## Versie-conventie

- **v1** = eerste werkende versie van eigen rack (13 mei 2026)
- **v2** = na eerste team-sessie met Aleksander (datum invullen)
- **v3** = na test met derde rack
- **vN** = telkens een nieuwe versie bij significante wijziging

> Oude versies NIET wissen — kopiëren naar nieuwe submap en daar aanpassen.

---

## Status overzicht (laatst bijgewerkt: 13 mei 2026)

| Component | Status |
|-----------|--------|
| SW-CORE-RACK1 (3650) | ✅ Geconfigureerd, OSPF 25 actief |
| R-EDGE Fa1/0 → SW-CORE | ✅ FULL neighbor, point-to-point |
| R-EDGE2 Fa1/0 → SW-CORE | ✅ FULL neighbor, point-to-point |
| Peer0 link → Aleksander | ⏸️ Wacht op Aleksander's listening sessie |
| Inter-rack ping Rack 1 ↔ Rack 2 | ⏸️ Pending |
| Volledige 4-rack sessie | ⏸️ Pending |

---

## Snelle referentie: jouw IP's in 10.99.0.0/24

| Apparaat | Interface | IP |
|----------|-----------|-----|
| SW-CORE-RACK1 | Gig1/0/23 → Peer0 | 10.99.0.2/30 |
| SW-CORE-RACK1 | Gig1/0/24 → R-EDGE | 10.99.0.73/30 |
| SW-CORE-RACK1 | Gig1/0/22 → R-EDGE2 | 10.99.0.77/30 |
| R-EDGE | Fa1/0 → SW-CORE | 10.99.0.74/30 |
| R-EDGE2 | Fa1/0 → SW-CORE | 10.99.0.78/30 |
| Aleksander (Rack 2) | naar Peer0 | 10.99.0.1/30 |

---

## Volgende stappen

1. Wachten op Aleksander's listen-sessie
2. Peer0 config invullen met zijn IP/poort/wachtwoord
3. Inter-rack OSPF FULL bevestigen met Aleksander
4. Ping-tests Rack 1 ↔ Rack 2
5. Documentatie aanvullen in `LOGBOEK.md` na elke sessie
