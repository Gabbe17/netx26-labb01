# Vecka 3 – Instuderingsövningar Ö1–Ö10

### Ö1 – Principerna

**1. All kommunikation ska skyddas** ✅

Trafiken ska skyddas även inom det interna nätverket så att obehöriga inte kan komma åt informationen.

Exempel: Trafiken mellan labbservern och loggservern kan skyddas med TLS.

**2. Åtkomst ska prövas för varje session**

Varje gång jag loggar in måste jag verifiera min identitet för att säkerställa att det är jag och inte någon obehörig.

Exempel: När jag loggar in via SSH måste jag autentisera mig på nytt.

**3. Alla resurser ska skyddas** 

Alla servrar behöver skyddas, även om de inte innehåller känsliga uppgifter.

Exempel: Loggservern behöver skyddas så att ingen obehörig kan ändra eller radera loggar. Jump-servern behöver skyddas eftersom den används för att komma åt andra servrar.
___

### Ö2 – Vem gör vad? ✅

**A. Policy Engine (PE) – 2**
Fattar beslut om åtkomst utifrån regler och säkerhetsinformation.

**B. Policy Administrator (PA) – 3**
Verkställer beslutet genom att exempelvis öppna åtkomsten eller dela ut en token.

**C. Policy Enforcement Point (PEP) – 1**
Släpper igenom eller stoppar själva trafiken.

**D. Subjekt – 4**
Användaren eller tjänsten som begär åtkomst.
___

### Ö3 - Hitta den implicita tilliten. ❌
**1. Ja, implicit tillit.**

Alla enheter i det interna nätverket får komma åt
loggservern på alla portar bara för att de tillhör
samma nätverk.

**2. Ja, delvis implicit tillit.**

Jump-servern får SSH-ansluta till alla servrar.
Det innebär att den har för bred åtkomst.
Åtkomsten borde begränsas till de servrar
som den faktiskt behöver nå.

**3. Nej, inte implicit tillit.**

Trafiken är begränsad till tcp/6514 och skyddas
med TLS. Serverns identitet verifieras med certifikat.

**4. Ja, implicit tillit.** 

Man litar på all trafik inom det interna nätverket
utan att filtrera eller kontrollera den.

**5. Nej, inte implicit tillit.**

Åtkomsten kräver en giltig token för en specifik
resurs och är begränsad till två minuter.
___
### Ö4 – Beslut per begäran ✅

Fyra signaler som kan kontrolleras:

1. Identitet – vilken användare försöker få åtkomst?
2. Enhet – är datorn säker och uppdaterad?
3. Resurs – vilken information vill användaren komma åt?
4. Kontext – från vilken adress och när sker anslutningen?

**Exempel på nekad åtkomst:**

En användare kan ha rätt lösenord men ändå nekas
åtkomst om personen försöker ändra information
som den inte har behörighet till.
___
### Ö5 – Mognad ✅

**Traditional Network Segmentation:**

Ett traditionellt nätverk har stora zoner där servrar
ofta kan kommunicera med varandra utan särskilda
begränsningar.

**Advanced Network Segmentation:**

Nätverket delas upp i mindre zoner där trafiken
begränsas och kontrolleras med tydligare regler.

**Var passar ett platt nätverk?**

Ett platt nätverk tillhör Traditional eller en lägre
mognadsnivå eftersom alla servrar kan kommunicera
med varandra utan tydliga begränsningar.
___

### Ö6 – Makro eller mikro? ✅

**1. Klientzonen får inte nå serverzonen, utom webbservern på tcp/443.**

Makrosegmentering eftersom regeln begränsar trafiken
mellan två större zoner. Webbservern på tcp/443 är
ett undantag.

**2. Bara appservern 10.40.1.20 får nå databasen 10.40.1.30 på tcp/5432.**

Mikrosegmentering eftersom endast en specifik
appserver får kommunicera med databasen på tcp/5432.

**3. Driftzonen får nå allt.**

Makrosegmentering eftersom regeln gäller en hel zon.
Det innebär också implicit tillit och en säkerhetsrisk.
Om en angripare får tillgång till driftzonen kan
den komma åt andra servrar och känslig information.
___

### Ö7 – Var filtreras flödet? ✅

**1. Internet → webbserver tcp/80**

Både Hetzner Cloud Firewall och serverns egen brandvägg
kan filtrera trafiken eftersom den kommer från internet.

**2. Jump-servern (172.20.1.2) → min server tcp/22**

Trafiken filtreras av serverns egen brandvägg eftersom
den går via det interna nätverket.

**3. Min server → loggservern (172.20.1.58) tcp/6514**

Trafiken filtreras av serverns egen brandvägg eftersom
kommunikationen sker via det interna nätverket.

**4. Min server → uppdateringsserver på internet tcp/443**

Både Hetzner Cloud Firewall och serverns egen brandvägg
kan filtrera trafiken eftersom den går ut mot internet.
___

### Ö8 – Default deny i båda riktningarna ✅

**1. Varför räcker det inte att bara blockera inkommande trafik?**

Angrepp kan även komma inifrån nätverket.
Om en angripare redan har fått tillgång till en server
kan den göra skada eller skicka känslig information
ut från nätverket.

**2. Ge ett exempel på en attack som kan stoppas genom att blockera utgående trafik.**

Om en angripare har tagit sig in på servern och
försöker skicka känslig information till internet
kan utgående brandväggsregler blockera trafiken.

**3. Vad händer när den första utgående regeln skapas i Hetzner Cloud Firewall?**

När man skapar den första utgående regeln i
Hetzner Cloud Firewall blockeras all annan
utgående trafik som standard.

Man måste därför tillåta den trafik som behövs,
exempelvis DNS och uppdateringar.
___

### Ö9 – En rad i flödestabellen ❌

**Källzon:** Webbserver (10.40.1.10)

**Destinationszon:** Appserver (10.40.1.20)

**Protokoll:** TCP

**Port:** 8080

**Riktning:** Webbserver -> Appserver

**Verksamhetsmotivering:**
Webbservern behöver hämta orderdata från appservern
för att kunna visa kundernas beställningar.

**Filtrering:**
Trafiken filtreras av serverns egen brandvägg (host)
eftersom kommunikationen sker via det interna nätverket.

**Varför räcker inte "samma nätverk" som motivering?**

Att servrarna finns i samma nätverk betyder inte att
de automatiskt ska få kommunicera med varandra.
Det måste finnas ett behov av kommunikationen.
___

### Ö10 – Policy i tabellform ✅

**Policy 1 – SSH till jump-servern**

- **Subjekt:** Jag som användare.
- **Action:** Logga in via SSH.
- **Target:** Jump-servern.
- **Condition:** Jag måste ha rätt SSH-nyckel och vara behörig.

**Policy 2 – Skicka loggar till loggservern**

- **Subjekt:** Labbservern.
- **Action:** Skicka loggar.
- **Target:** Loggservern (172.20.1.58).
- **Condition:** Trafiken ska gå via tcp/6514 med TLS
  och verifierat servercertifikat.

**Varför begränsa trafiken till tcp/6514?**

Genom att endast tillåta den port som behövs
minskar vi attackytan och risken för obehörig åtkomst.
___
### Förberedelsefrågan – Implicit tillit

Vårt labbnät litar bland annat på jump-servern
eftersom den används för att ansluta till labbservrarna.

Ett exempel på implicit tillit är att jump-servern
får försöka ansluta via SSH till alla labbservrar.

Det är implicit tillit eftersom jump-servern får
bred åtkomst på grund av sin roll i nätverket.

Om en angripare får tillgång till jump-servern
kan den försöka komma åt andra labbservrar.


