### Ö1 – Vad går förlorat?

**Fråga:** Nämn tre saker som går förlorade när loggarna bara finns lokalt på servern.

**Svar:**

1. **Bevis:** Om en angripare får tillgång till servern kan personen ändra eller radera loggar för att dölja sina spår.
2. **Tillgänglighet:** Om servern kraschar eller utsätts för ransomware kan loggarna bli otillgängliga eller gå förlorade.
3. **Samband:** Det blir svårare att jämföra händelser från flera servrar om loggarna inte samlas på ett gemensamt ställe.
___
### Ö2 – Läs en syslograd

**Fråga:** Vad betyder varje del av följande syslograd?

`<38>1 2026-11-04T10:15:02.123+01:00 elev-07 sshd 1234 - - Accepted publickey for student from 172.20.1.2 port 51234 ssh2`

**Svar:**

- **<38>:** Loggens prioritet, auth.info.
- **1:** Versionen av syslogformatet.
- **2026-11-04T10:15:02.123+01:00:** Datum, tid, millisekunder och tidszon.
- **elev-07:** Servern som skickade loggen.
- **sshd:** SSH-tjänsten som hanterade inloggningen.
- **1234:** Process-ID för SSH-tjänsten.
- **- -:** Information saknas.
- **Accepted publickey:** Inloggningen lyckades med en SSH-nyckel.
- **student:** Användaren som loggade in.
- **172.20.1.2:** IP-adressen som anslutningen kom från.
- **51234:** Porten som man använde.
- **ssh2:** SSH-protokoll version 2.
___
### Ö3 – Räkna PRI

**Fråga:** PRI = facility × 8 + severity. Vilken facility och severity har PRI 13, 86 och 166? Vilket PRI får local5.info (local5 = 21, info = 6)?

**Svar:**

Formel: PRI = facility × 8 + severity.

- **PRI 13:** 1 × 8 + 5 = 13 → user.notice
- **PRI 86:** 10 × 8 + 6 = 86 → authpriv.info
- **PRI 166:** 20 × 8 + 6 = 166 → local4.info
- **local5.info:** 21 × 8 + 6 = 174
___
### Ö4 – RFC 3164 mot RFC 5424

**Fråga:** Nämn två saker i tidsstämpeln som RFC 5424 har men RFC 3164 saknar, och varför de spelar roll när loggar från flera värdar ska läggas i ordning.

**Svar:**

RFC 5424 visar årtal och tidszon, vilket RFC 3164 inte gör. Det är viktigt för att kunna jämföra loggar från olika servrar och se i vilken ordning händelserna inträffade.
___
### Ö5 – Vad skyddar TLS?

**Fråga:** Vilka tre hot mot loggtrafiken skyddar TLS mot enligt RFC 5425? Nämn ett hot som TLS på transporten inte skyddar mot.

**Svar:**

1. **Avlyssning:** TLS krypterar trafiken så att obehöriga inte kan läsa loggarna under överföringen.
2. **Identitetsförfalskning:** TLS använder digitala certifikat för att verifiera identiteten och minska risken att någon utger sig för att vara en annan server.
3. **Modifiering:** TLS använder kryptografiska metoder för att upptäcka om information ändras under överföringen.

TLS skyddar däremot inte mot en komprometterad server. Om en angripare får root-behörighet kan personen läsa, ändra eller radera lokala loggar.
___
### Ö6 – Journal och fil

**Fråga:** Var lagras journald:s journal om /var/log/journal saknas, och vad händer då vid omstart? Varför finns samma sshd-händelse både i journalen och i /var/log/secure?

**Svar:**

Om /var/log/journal saknas och Storage=auto används sparas loggarna tillfälligt i /run/log/journal (RAM-minnet) och de försvinner vid en omstart.

Samma SSH-händelse finns på båda ställena eftersom journald samlar in loggar från systemet och rsyslog läser dem och sparar säkerhets- och inloggningshändelser i /var/log/secure.
___
### Ö7 – Regeln för messages

**Fråga:** Vad betyder `*.info;mail.none;authpriv.none;cron.none /var/log/messages`?

**Svar:**

Regeln betyder att alla loggkällor med severity info eller allvarligare sparas i /var/log/messages, förutom mail, authpriv och cron.

Dessa tre loggkällor undantas eftersom de har egna loggfiler, till exempel /var/log/secure för authpriv.
___
### Ö8 – Kö

**Fråga:** Vad är skillnaden mellan en minneskö och en diskstödd kö i rsyslog, och vilken parameter gör kön diskstödd? Vad gör action.resumeRetryCount="-1"?

**Svar:**

En minneskö lagrar loggar i RAM-minnet och de kan försvinna vid omstart. En diskstödd kö kan spara loggar på disken så att de inte försvinner vid omstart, om den är rätt konfigurerad.

- **queue.filename:** Gör kön diskstödd.
- **queue.saveOnShutdown="on":** Sparar kön vid omstart.
- **action.resumeRetryCount="-1":** Gör att rsyslog fortsätter försöka skicka loggarna utan någon gräns för antalet försök.
___
### Ö10 – Fråga eller beställning?

**Fråga:** Vilka av följande är frågor som kan styra ett urval, och vilka är beställningar av en loggkälla? Skriv om beställningarna till frågor.

**Svar:**

**1. "Vi vill ha brandväggsloggen."**

Det är en beställning eftersom det inte framgår vad vi vill undersöka.

Omformulerad fråga:
"Vilka anslutningsförsök från elev 10 blockerades av brandväggen idag mellan kl. 10:00 och 10:15?"

**2. "Vem loggade in som root på elev-07 mellan kl. 02 och 04 natten till 5 november?"**

Det är en fråga som kan styra ett urval eftersom vi söker efter en specifik inloggningshändelse på en viss server och under en bestämd tidsperiod.

**3. "Spara allt från sshd."**

Det är en beställning eftersom det inte framgår vad vi vill undersöka.

Omformulerad fråga:
"Vilka IP-adresser försökte ansluta via SSH under natten, och användes lösenord eller SSH-nyckel vid inloggningsförsöken?"

**4. "När blockerades senast ett försök att nå loggservern på port 6514 från webbzonen?"**

Det är en fråga som kan styra ett urval eftersom vi söker efter en specifik händelse i brandväggsloggarna.
___


