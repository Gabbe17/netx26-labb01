### Ö1 – Två lager ✅

Hetzners molnbrandvägg skyddar servern mot trafik via internet, medan nftables styr vilken trafik som får komma in och ut från själva servern, även inom det privata nätverket.
___
### Ö2 – Var hör raden hemma? ✅

a) Båda – Trafiken från internet kan filtreras av både Hetzners molnbrandvägg och nftables.

b) nftables – Trafiken mellan appservern och databasen går i det privata nätverket.

c) nftables – SSH-trafiken från jump-servern till labbservern filtreras av nftables.

d) nftables – Utgående DNS-trafik från servern filtreras av nftables.
___
### Ö3 – Utgående riktning ❌

Om molnbrandväggen bara har inkommande regler så är  utgående trafik tillåtet.

Om vi lägger till en utgående regel för TCP/443 tillåts bara den trafiken. Annan utgående trafik, t.ex DNS på UDP/53 kommer blockeras.
___
### Ö4 – Ordningen ✅

Regeln "ct state established,related accept" ska ligga först för att tillåta redan etablerade anslutningar, exempelvis SSH.

Om vi aktiverar "policy drop" utan att tillåta SSH-trafiken kan anslutningen brytas och vi riskerar att bli utelåsta från servern.
___
### Ö5 – Policy eller regel? ✅

"policy drop" blockerar automatiskt trafik som inte har någon tillåtande regel.

En vanlig "drop" regel blockerar trafiken direkt när paketet matchar regeln.

Loggregeln måste ligga före en vanlig "drop" regel, annars hinner trafiken inte loggas. Med "policy drop" kan loggregeln ligga sist i kedjan.
___
### Ö6 – Reserverade adresser ❌

I subnätet 10.40.7.0/26 är 10.40.7.0 nätadressen och 10.40.7.63 broadcastadressen.

Adresserna är reserverade och kan inte användas. De användbara adresserna är 10.40.7.1–10.40.7.62.
___
### Ö7 – Tre utfall ✅

a) Tyst drop – Paketen blockeras utan något svar tillbaka. I Wireshark kan man se upprepade SYN-paket utan svar.

b) Reject – Brandväggen skickar ett svar tillbaka att man blivit nekad, t.ex TCP RST eller ICMP.

c) Tjänsten avvisar – Trafiken når fram och TCP-anslutningen skapas, men tjänsten nekar eller stänger anslutningen.
___
### Ö8 – Läs en loggrad ✅

Trafiken kommer från IP-adressen 172.20.1.203 till 172.20.1.107 via eth1.

Man försöker ansluta med TCP från port 51922 till port 46472. SYN betyder att en anslutning försöker starta.

Loggraden skapades av regeln med prefixet ZON-IN-DROP och trafiken blockeras sedan av policy drop.
___
### Ö9 – Beständigt ✅

Om vi bara laddar reglerna med "nft -f" så kan dem försvinna sen när servern startas om.

Därför sparade vi reglerna i "/etc/nftables/zon.nft", och såg till att "/etc/sysconfig/nftables.conf" läser filen och använde "sudo systemctl enable nftables" så att reglerna laddas automatiskt vid omstart.
___
### Ö10 – Ett nekat test som inte bevisar något ✅

Att anslutningen till port 9000 nekas behöver inte betyda att vår brandväggsregel fungerar.

Det kan bero på att ingen tjänst lyssnar på porten eller att en annan brandvägg blockerar trafiken.

Vi kan använda "ss -tlnp" för att kontrollera om en tjänst lyssnar och "sudo nft list ruleset" för att kontrollera våra brandväggsregler. Vi kan även kontrollera loggarna för att se om trafiken blockerades.
___



