# Team-communicatie & afspraken

Houd hier bij wat er met de teamgenoten is afgesproken — voorkomt
miscommunicatie tussen sessies.

---

## Team-leden

| Naam | Rack | Rol |
|------|------|-----|
| Salaheddine Sennouni | Rack 1 | Client (verbindt naar Aleksander) |
| Aleksander Cornette | Rack 2 | Host (Listening sessie + internet) |
| Mehdi Benramdane | Rack ? | Client |
| Adil Toallati | Rack ? | Client |
| Soulaymane Chaara | Rack ? | Client |

Coach: Yvan Rooseleer

---

## Afspraken sessie 11/05/2026 (coach-meeting)

1. CML vervangen door PT Multi-user
2. Deadline: 25/05/2026 voor werkende inter-rack demo
3. Aleksander hostet (heeft beste internetverbinding en setup)
4. IP-plan 10.99.0.0/24 zoals door Aleksander gedeeld

---

## Wachtende info van Aleksander

| Info | Status | Datum gevraagd |
|------|--------|----------------|
| Publiek IP (whatismyip.com) | ? | nog vragen |
| Poort nummer Peer (default 38000?) | ? | nog vragen |
| Wachtwoord Peer | ? | nog vragen |
| Wanneer is hij online voor test? | ? | nog vragen |
| Heeft hij port forwarding gedaan? | ? | nog vragen |
| Welke IPs zijn voor Rack 3/4? | ? | nog vragen |

---

## Geplande sessies

| Datum | Aanwezigen | Doel | Status |
|-------|------------|------|--------|
| (in te vullen) | Aleksander + Salah | Eerste bilaterale ping-test | Te plannen |
| (in te vullen) | Volledig team | 4-rack volledige test | Te plannen |
| (in te vullen) | + coach | Demo aan Yvan | Te plannen |

---

## Te delen met team (whatsapp/teams bericht)

Suggestie tekst:

> "Mijn rack 1 staat klaar voor multi-user. OSPF process 25 actief,
> beide R-EDGE en R-EDGE2 FULL met SW-CORE-RACK1. IP plan dat ik gebruik
> (van Aleksander):
> - SW-CORE Gig1/0/23 -> Peer0 = 10.99.0.2/30
> - SW-CORE Gig1/0/24 -> R-EDGE = 10.99.0.73/30
> - SW-CORE Gig1/0/22 -> R-EDGE2 = 10.99.0.77/30
>
> Aleksander, kun je laten weten:
> 1. Wanneer je online bent voor een test?
> 2. Je publiek IP + poort + wachtwoord voor Peer0?
> 3. Of port forwarding 38000 in orde is op je router?
>
> Eens we online zijn moet ping naar 10.99.0.1 lukken vanaf mijn switch."
