# Vecka 7 – Instuderingsövningar Ö1–Ö10

**Elev:** elev-44  
**Ämne:** Härdning med Mandatory Access Control (SELinux)

Jag har först svarat på frågorna med egna ord och sedan jämfört mina svar med lösningsförslagen.

**✓** = mitt svar stämde.  
**?** = något saknades eller behövde rättas. Jag har skrivit vad jag lärde mig under svaret.

## Ö1 – DAC eller MAC?

**Mitt svar ✓**  
I DAC bestäms åtkomsten av filens ägare och behörighetsinställningar, medan den i MAC bestäms av centrala systemregler och säkerhetsetiketter som inte ens admin kan kringgå.

**Det jag lärde mig:** MAC kan begränsa skadan om någon tar över en process, eftersom SELinux fortfarande kan neka åtkomst även om processen kör som root.

## Ö2 – SELinux olika lägen

**Mitt svar ✓**  
Enforcing är standard och håller säkerhetspolicyn fullt ut. Permissive blockerar inget men loggar allt som skulle ha blockerats i Enforcing-läget. Disabled betyder att SELinux är avstängt och inget loggas. Det kan vara användbart att sätta SELinux i Permissive vid felsökning, för då får man fram loggarna.

**Förtydligande:** Det är SELinux-händelser som skulle ha nekats som loggas i Permissive, inte automatiskt alla loggar i systemet.

## Ö3 – Nu och efter omstart

**Mitt svar ✓**

1. Efter en omstart kommer SELinux att vara i Enforcing.
2. Läget kan ha ändrats temporärt under körningen.
3. Aktuellt läge kan man se med `getenforce`, och konfigurationen kan man se i `/etc/selinux/config` eller med `sestatus`.

**Det jag lärde mig:** `setenforce 0` kan ändra det aktuella läget till Permissive utan att ändra vad som gäller efter omstart.

## Ö4 – Förstå en SELinux-kontext

Kontexten är `system_u:object_r:httpd_sys_content_t:s0`.

**Mitt svar ✓**

1. Användare – vilken roll – vilken typ – känslighet/säkerhetsnivå.
2. Typen, till exempel `httpd_sys_content_t`, är oftast viktigast.
3. `httpd_t` har rätt att läsa men inte skriva till filerna.

**Förtydligande:** `httpd_sys_content_t` visar att filen är märkt som webbinnehåll. Apache får normalt läsa sådana filer, men vad som faktiskt tillåts styrs av SELinux-policyn.

## Ö5 – Tolka en AVC-logg

Loggen visar `comm="httpd"`, `src=8088`, `scontext=...:httpd_t:s0`, `tcontext=...:unreserved_port_t:s0` och `denied { name_bind }`.

**Mitt svar ✓**

1. `httpd`.
2. Processen försökte binda en port.
3. Port 8088.
4. Det nekades eftersom `httpd_t` inte hade behörighet. Apache får normalt lyssna på vissa webbportar, till exempel 80, 443 och 8080.
5. Ändra portens typ till `http_port_t`.

**Komplettering:** Jag kan först kontrollera befintliga porttyper och sedan koppla TCP-port 8088 till rätt typ med:

```bash
sudo semanage port -a -t http_port_t -p tcp 8088
```

Då behöver jag inte stänga av SELinux.

## Ö6 – Välj rätt SELinux-åtgärd

**Mitt svar ✓**

1. Apache kan inte läsa filer i `/srv/intranat` – **filkontext**.
2. Apache kan inte ansluta till en annan server – **boolean**.
3. SSH ska använda port 2222 – **porttyp**.
4. En egen tjänst ska begränsas – **egen policy**.

**Det jag lärde mig:** Jag ska välja åtgärd efter vad som faktiskt nekas: fil, nätverksanslutning, port eller tjänstens rättigheter.

## Ö7 – `chcon` eller `semanage fcontext`?

**Mitt svar ✓**

1. `chcon` är bara en temporär lösning.
2. `chcon` ändrar SELinux-etiketten direkt, medan `semanage fcontext` gör så att regeln blir permanent.
3. Jag skulle använda `semanage fcontext`.

**Komplettering:** Jag behöver även köra `restorecon` efter `semanage fcontext` för att den sparade regeln ska användas på filerna. `restorecon` kan skriva över en ändring som bara gjorts med `chcon`.

## Ö8 – Permissive för en domän eller hela systemet?

**Mitt svar ✓**  
Jag skulle bara sätta `zonvakt_t` i Permissive. Då blir felsökningen mer fokuserad och säkerheten behålls bättre än om hela SELinux sätts i Permissive.

Skillnaden är att om hela SELinux sätts i Permissive slutar den blockera. Om bara zonvakt ändras fortsätter SELinux att skydda andra delar av systemet. `zonvakt_t` ska inte vara kvar i Permissive när härdningen är klar.

**Förtydligande:** Det handlar om andra processer och tjänster, inte bara andra portar. Andra AVC-händelser kan fortfarande loggas.

## Ö9 – Granska `audit2allow`

**Mitt ursprungliga svar ?**

1. Jag skulle behålla:
   - `corecmd_mmap_bin_files(zonvakt_t)`
   - `corecmd_exec_bin(zonvakt_t)`
   - `auth_read_passwd_file(zonvakt_t)`
2. Jag skulle ta bort `files_manage_etc_files(zonvakt_t)` och `corenet_tcp_bind_generic_port(zonvakt_t)` eftersom de ger för breda rättigheter och kan vara en säkerhetsrisk.
3. `audit2allow` har ingen förståelse för säkerhetskontexten. Det översätter råa loggar till regler som kan tysta felen.

**Det jag rättade:** `corecmd_exec_bin(zonvakt_t)` behövdes inte heller enligt lösningsförslaget. De två regler jag skulle behålla är `corecmd_mmap_bin_files` och `auth_read_passwd_file`, om tjänsten behöver dem. I VG-uppgiften tog jag också bort den generella porträttigheten och använde en egen porttyp för zonvakt.

## Ö10 – Verifiering eller konfiguration?

Skriptet innehåller:

```bash
sudo setenforce 1 && echo "SELinux: GÄLLER"
```

**Mitt ursprungliga svar ?**

1. Det visar bara vad man sätter för stunden, men efter omstart kan läget ändras tillbaka.
2. Jag tänkte att det bara ändrar SELinux medan skriptet körs och sedan ändras tillbaka igen.
3. Jag skulle kontrollera SELinux-konfigurationsfilen och använda `semanage fcontext` och `restorecon`.

**Det jag rättade:** `setenforce 1` ändrar det aktuella läget och ändringen fortsätter gälla även när skriptet är klart. Problemet är att skriptet ändrar systemet i stället för att kontrollera hur det redan var. Efter omstart används inställningen i `/etc/selinux/config`.

Jag skulle i stället kontrollera utan att ändra något:

```bash
getenforce
sestatus
grep '^SELINUX=' /etc/selinux/config
```

Jag vill se att SELinux är **Enforcing nu** och att `SELINUX=enforcing` står i konfigurationsfilen. `semanage fcontext` och `restorecon` gäller filernas etiketter, inte kontroll av SELinux-läget.

---
