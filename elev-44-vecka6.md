### Ö1 – Decoder och regel ✓

En decoder tolkar en loggrad och delar upp informationen i olika fält, exempelvis `srcip` och `dstuser`. En regel kontrollerar informationen och avgör om ett larm ska skapas. Regeln kan även sätta nivå, beskrivning, grupp och ATT&CK-ID.

Regeln kan inte matcha ett fält som decodern inte har plockat ut, eftersom informationen då saknas.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget. Jag kompletterade med att regeln även avgör om ett larm ska skapas.
___

### Ö2 – Nivåer i Wazuh

En lyckad inloggning med level 3 behöver inte vara misstänkt i sig.
Men om det har skett 15 misslyckade inloggningsförsök från samma
IP-adress innan kan det tyda på en brute force-attack där någon
har testat flera lösenord och till slut lyckats logga in.

Om inloggningen dessutom sker utanför arbetstid och på ett
tjänstekonto blir händelsen ännu mer misstänkt.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget.
Jag kompletterade med att tidpunkt och typ av konto också
kan vara viktiga vid analysen.
___
### Ö3 – Syslog och dess fält

- **Timestamp:** Visar datum och tid när händelsen inträffade,
  inklusive tidszon när den finns angiven.
- **Hostname:** Namnet på datorn eller servern som skickar loggen.
- **App-name:** Vilken applikation eller tjänst som skickat loggen.
- **Severity:** Anger hur allvarlig händelsen är.
- **Message:** Beskriver vad som har hänt, exempelvis ett
  misslyckat eller lyckat inloggningsförsök.

**Självrättning: ✓** Jag förstod vad fälten används till,
men förtydligade att hostname är serverns namn och inte
användarkontot.
___
### Ö4 – Falskt positivt och falskt negativt

Falskt positivt innebär att Wazuh skickar ett larm trots
att det inte finns något verkligt hot.

Falskt negativt innebär att ett riktigt intrång sker
utan att Wazuh skickar något larm.

Jag tycker att falskt negativt är farligast för en SOC,
eftersom en attack kan pågå utan att någon upptäcker den.

Många falskt positiva larm kan också vara farliga eftersom
analytikerna kan bli överbelastade och missa riktiga hot.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget.
Jag kompletterade med att många falska larm kan leda till
alert fatigue.
___
### Ö5 – Brute force

Jag skulle kontrollera om samma IP-adress har gjort flera
misslyckade inloggningsförsök under en kort tidsperiod,
eventuellt mot flera olika användarkonton.

Jag skulle även undersöka om någon inloggning till slut
lyckades.

En vanlig feltryckning är oftast ett enstaka misslyckat
försök, medan brute force kan innebära många försök
från samma IP-adress på kort tid.

I Wazuh kan man använda exempelvis rule 5712 för att
upptäcka upprepade misslyckade inloggningsförsök.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget.
Jag kompletterade med att Wazuh kan använda regel 5712
för att upptäcka sådana mönster.
___
### Ö6 – Brusmätning

48 träffar under 24 timmar ger ett genomsnitt på
48 / 24 = 2 träffar per timme.

Jag tror inte att detektionen kommer att larma särskilt
ofta om gränsen är 5 träffar per timme, eftersom
genomsnittet bara är 2.

Men träffarna behöver inte vara jämnt fördelade.
Vissa timmar kanske inte har några träffar alls,
medan andra timmar kan ha exempelvis 7 träffar.

Därför behöver man undersöka när träffarna sker
och inte bara titta på genomsnittet.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget.
Jag förstod att genomsnittet inte visar hur träffarna
är fördelade över tid.
___
### Ö7 – Detektion vs testkörning

Om jag skapar en regel som bara fungerar för min egen
IP-adress och användare riskerar jag att missa riktiga
angrepp från andra IP-adresser eller mot andra konton.

Jag skulle istället undersöka avvikande beteenden,
exempelvis ovanliga IP-adresser eller flera misslyckade
inloggningsförsök under kort tid.

Jag skulle även jämföra med normaltrafiken och kontrollera
hur många falska larm regeln skapar, så att den inte blir
för bred eller för begränsad.

**Självrättning: ✓** Jag förstod att en regel inte ska
begränsas till min egen testinloggning. Jag kompletterade
med att jämföra mot normaltrafiken och brusmäta regeln.
___
### Ö8 – Triage

**1. Vad har hänt?**

Jag skulle undersöka loggarna för att se vilka
inloggningsförsök som gjorts, från vilken IP-adress,
när de inträffade och om någon SSH-inloggning lyckades.

**2. Vad har berörts?**

Jag skulle filtrera på IP-adressen och undersöka om
den även har försökt ansluta till andra användarkonton
eller servrar under samma tidsperiod.

**3. Vad ska göras?**

Jag skulle kontrollera om någon obehörig har lyckats
logga in eller om det finns en rimlig förklaring,
exempelvis att en användare har glömt sitt lösenord.

Beroende på vad loggarna visar får jag avgöra om
larmet ska eskaleras eller avfärdas. Beslutet ska
kunna motiveras med observationer från loggarna.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget.
Jag kompletterade med att kontrollera regel, tidpunkt
och dokumentera observationerna som stöd för beslutet.
___
### Ö9 – Att avfärda ett larm

Det räcker inte att bara skriva att det var jag som
loggade in, eftersom en annan analytiker inte kan
kontrollera om det stämmer.

Jag skulle dokumentera tidpunkten, användarkontot
och IP-adressen som inloggningen kom från.

Jag skulle även kontrollera andra loggar för att
bekräfta att händelsen hörde till ett förväntat
test eller en normal aktivitet.

På så sätt kan en annan analytiker kontrollera
varför larmet avfärdades.

**Självrättning: ✓** Jag förstod vilka uppgifter som
behöver dokumenteras. Jag kompletterade med att
beslutet även måste styrkas av en kontrollerbar
observation.
___
### Ö10 – Playbook och incidenthantering

De fyra faserna enligt kursens NIST 800-61-modell är:

1. Förberedelse
2. Detektion och analys
3. Begränsning, utrotning och återställning
4. Efterarbete

**Förberedelse:** Se till att övervakningssystem som Wazuh
är installerade, uppdaterade och fungerar som de ska.

**Detektion och analys:** Undersöka larm och loggar för
att avgöra vad som har hänt och vilka system som berörts.

**Begränsning, utrotning och återställning:** Isolera
drabbade system, ta bort eventuell skadlig kod och
återställa system från säkra backuper vid behov.

**Efterarbete:** Analysera och utvärdera hur incidenten
kunde inträffa och vad man kan förbättra för att
förhindra liknande incidenter i framtiden.

**Självrättning: ✓** Mitt svar stämde med lösningsförslaget.
Jag kompletterade med detektion och analys samt
förtydligade skillnaden mellan begränsning och återställning.



