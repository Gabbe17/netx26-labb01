# Vecka 3 – Instuderingsövningar Ö1–Ö10

### Ö1 – Principerna

**1. All kommunikation ska skyddas**

Trafiken ska skyddas även inom det interna nätverket så att obehöriga inte kan komma åt informationen.

Exempel: Trafiken mellan labbservern och loggservern kan skyddas med TLS.

**2. Åtkomst ska prövas för varje session**

Varje gång jag loggar in måste jag verifiera min identitet för att säkerställa att det är jag och inte någon obehörig.

Exempel: När jag loggar in via SSH måste jag autentisera mig på nytt.

**3. Alla resurser ska skyddas**

Alla servrar behöver skyddas, även om de inte innehåller känsliga uppgifter.

Exempel: Loggservern behöver skyddas så att ingen obehörig kan ändra eller radera loggar. Jump-servern behöver skyddas eftersom den används för att komma åt andra servrar.
___

### Ö2 – Vem gör vad?

**A. Policy Engine (PE) – 2**
Fattar beslut om åtkomst utifrån regler och säkerhetsinformation.

**B. Policy Administrator (PA) – 3**
Verkställer beslutet genom att exempelvis öppna åtkomsten eller dela ut en token.

**C. Policy Enforcement Point (PEP) – 1**
Släpper igenom eller stoppar själva trafiken.

**D. Subjekt – 4**
Användaren eller tjänsten som begär åtkomst.
___

### Ö3 - Hitta den implicita tilliten.
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
### Ö4 – Beslut per begäran

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
### Ö5 – Mognad

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






