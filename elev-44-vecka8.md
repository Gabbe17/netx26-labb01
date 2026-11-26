# Vecka 8 – Instuderingsövningar

Elev: 44 – Gabriel Trpkoski

## Del A – Egna svar och självrättning

✓ = Rätt svar
? = Självrättning

## Ö1 – Normal eller avvikande TCP?

- a) ✓ Normalt. En TCP-anslutning upprättas, data överförs och anslutningen avslutas på ett ordnat sätt.
- b) ? Avvikande eftersom RST betyder reset.
  - Självrättning: Det är mängden portar under kort tid som tyder på portskanning, inte RST i sig.
- c) ✓ Normalt. TCP kan skicka om segment när en bekräftelse inte kommer i tid.
- d) ? Avvikande eftersom jag trodde att det skulle bli timeout.
  - Självrättning: RST är ett normalt svar från en stängd TCP-port.

## Ö2 – Avfärda bruset

✓ Alternativ 1, 2 och 3 är normala. Alternativ 4 och 5 är avvikande.

Många ARP-svar utan frågor kan tyda på ARP-spoofing. En lång slumpmässig subdomän kan tyda på DNS-tunnling.

## Ö3 – Fel port för protokollet

- a) ✓ HTTP på port 443 är avvikande eftersom porten normalt används för HTTPS. Det kan bero på en felkonfiguration.
- b) ✓ SSH använder normalt port 22 och inte port 80 som används för HTTP. Det kan bero på en felkonfigurerad tjänst.
- c) ? Ett DNS-svar brukar vara mindre och jag trodde att det kunde bero på spam.
  - Självrättning: Det kan tyda på DNS-amplifiering eller DNS-tunnling, men stora DNS-svar kan även vara legitima.

## Ö4 – Felkonfiguration eller angrepp?

✓ Troligtvis en felkonfigurerad tjänst eftersom den försöker ansluta till samma adress och port var femte sekund utan att lyckas. En portskanning riktar sig vanligtvis mot flera portar eller mål.

## Ö5 – MITRE ATT&CK

| Observation | Mitt svar | Självrättning |
|---|---|---|
| 1 | T1048 | ? T1046 |
| 2 | T1110 | ✓ |
| 3 | T1078 | ✓ |
| 4 | T1021 | ✓ |
| 5 | T1046 | ? T1048 |

Jag blandade ihop Network Service Discovery (T1046) och Exfiltration Over Alternative Protocol (T1048).

## Ö6 – Kryptering är inte exfiltration

? Mitt svar var IP-adresser, vem som skickar, datum och tidpunkt, handskakningar och portnummer.

Självrättning: De fyra centrala sakerna är motpart, riktning, datavolym och tidpunkt. Jag missade datavolymen.

Genom att jämföra metadata och beteendemönster med nätverkets normaltillstånd över tid kan jag identifiera avvikelser. Kryptering är inte i sig ett bevis på datastöld.

## Ö7 – Stämmer, fel eller ogrundat?

? Filtret är olämpligt eftersom HTTPS-trafiken är krypterad och POST inte går att läsa direkt. Jag bedömde hypotesen som fel.

Självrättning: Filtret är fel för ändamålet men hypotesen är ogrundad eftersom det saknas tillräckliga bevis för att bekräfta eller motbevisa den.

## Ö8 – Tidsstämplar

✓ Ja, det kan vara samma händelse.

- Loggpost: 09:14:03 +01:00 = 08:14:03 UTC.
- Paket: 08:14:03,402 UTC.

Båda inträffar under samma sekund. Man behöver känna till tidszonerna, fångstens starttid och eventuell klockdrift.

## Ö9 – Lucka eller ingen händelse?

✓ Jag kontrollerar följande i ordning:

1. Fel sökning.
2. Fel tidsfönster.
3. Ingen loggning.

Jag kan verifiera sökningen genom att söka efter en händelse som jag vet har inträffat.

## Ö10 – Från lucka till ändring

? Mitt förslag var att aktivera och konfigurera auditd för bättre spårbarhet.

Självrättning: Konfigurera rsyslog så att SSH-loggar (authpriv) vidarebefordras till den centrala loggservern. Skapa därefter en detektionsregel i Wazuh som larmar vid otillåtna SSH-anslutningar mellan interna servrar.
___
