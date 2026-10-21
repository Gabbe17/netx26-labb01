# Incidentrapport – eMudhra (oktober 2026)

**Incidenttyp:** Komprometterade TLS-certifikat
**Allvarlighetsgrad:** Kritisk

## Vad hände?
Åtta mellanliggande CA-certifikat från företaget eMudhra
hade privata nycklar som kunde återskapas på grund av
bristande slumpmässighet vid nyckelgenereringen.

Problemet upptäcktes av en säkerhetsforskare i oktober 2026.

## Vilka risker fanns?
Angripare kunde använda nycklarna för att skapa falska
TLS-certifikat och utge sig för att vara legitima webbplatser.

Det kunde möjliggöra Man-in-the-Middle-attacker (MITM),
där angripare avlyssnar eller manipulerar trafik.

## Vilka åtgärder vidtogs?
- De komprometterade certifikaten återkallades.
- Webbläsarleverantörer blockerade certifikaten.
- Säkerhetsuppdateringar behövs för berörda system.

## Koppling till CIA-triaden
- **Konfidentialitet:** Känslig information kan avlyssnas.
- **Integritet:** Trafik och information kan manipuleras.
- **Tillgänglighet:** Återkallade certifikat kan orsaka
  problem med åtkomst till vissa tjänster.

## Slutsats och lärdom
Incidenten visar vikten av säker nyckelgenerering,
korrekt certifikathantering och snabba säkerhetsuppdateringar.

HTTPS innebär inte automatiskt att en anslutning är säker
om certifikatutfärdarens nycklar har komprometterats.

## Källor
1. [eMudhra – Incidentinformation](https://emudhra.we-have-a-problem.com/)
2. [Ars Technica – 8 compromised TLS certificates](https://arstechnica.com/security/2026/10/the-internet-goes-on-high-alert-after-discovery-of-8-compromised-tls-certs/)
