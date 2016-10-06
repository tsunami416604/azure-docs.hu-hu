## A rekordok

Minden DNS-rekord rendelkezik névvel és típussal. A rekordok különféle típusokba vannak rendezve attól függően, hogy milyen adatokat tartalmaznak. A leggyakoribb típus az „A” rekord, amely egy nevet képez le egy IPv4-címhez. Egy másik típus, az „MX” rekord, egy nevet képez le egy levelezési kiszolgálóhoz.

Az Azure DNS minden gyakori DNS-rekord típust támogat, beleértve a következőket: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV és TXT. Vegye figyelembe:
- A SOA típusú rekordhalmazok automatikusan jönnek létre az egyes zónákkal együtt, külön nem hozhatók létre.
- Az SPF-rekordokat TXT típusú rekordok használatával kell létrehozni. További információkat [ezen az oldalon](http://tools.ietf.org/html/rfc7208#section-3.1) talál.

Az Azure DNS-ben a rekordok relatív nevek használatával vannak meghatározva. A „teljes” tartománynév (FQDN) tartalmazza a zóna nevét, a „relatív” név azonban nem. Például: a „www” relatív rekordnév a „contoso.com” zónában a www.contoso.com teljes tartománynevet adja ki.

## A rekordhalmazok

Előfordulhat, hogy több, azonos nevű és típusú DNS-rekordot is létre kell hoznia. Tegyük fel például, hogy a „www.contoso.com” webhely két különböző IP-címről is üzemel. A webhelynek két különböző A-rekordra van szüksége a két IP-címhez. Példa egy rekordhalmazra:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Az Azure DNS rekordhalmazok használatával kezeli a DNS-rekordokat. A rekordhalmazok az egy zónába tartozó, ugyanazzal a névvel és típussal rendelkező DNS-rekordok gyűjteményei. A legtöbb rekordhalmaz egyetlen rekordot tartalmaz, de a fentihez hasonló példához hasonló esetek sem szokatlanok, ahol egy rekordhalmazban több rekord is van.

A SOA és CNAME típusú rekordhalmazok kivételt jelentenek ez alól. A DNS-szabványok nem engedélyezik, hogy ezen típusok esetén több rekord is ugyanazzal a névvel rendelkezzen.

Az élettartam (TTL) megadja, hogy az ügyfelek mennyi ideig gyorsítótárazzák az egyes rekordokat az újbóli lekérdezés előtt. Ebben a példában az élettartam 3600 másodperc, vagyis 1 óra. Az élettartam a rekordhalmazhoz van megadva, nem az egyes rekordokhoz, így a halmaz összes rekordján ugyanaz az érték érvényesül.

#### Helyettesítő rekordhalmazok

Az Azure DNS [helyettesítő rekordok](https://en.wikipedia.org/wiki/Wildcard_DNS_record) használatát is támogatja. A rendszer ezeket minden egyező nevű lekérdezésre visszaadja (hacsak nincs egy nem helyettesítő rekordhalmazból származó közelebbi találat). A helyettesítő rekordhalmazok minden rekordtípus esetén támogatottak, kivéve az NS és SOA típust.  

Helyettesítő rekordhalmazok létrehozásához használja a következő rekordhalmaznevet: \*. Vagy használjon \* címkével ellátott nevet, például: \*.foo.

#### CNAME-rekordhalmazok

CNAME-rekordhalmazok nem létezhetnek egyidejűleg más, velük egyező nevű rekordhalmazokkal. Nem hozhat létre például egyidejűleg egy CNAME-rekordhalmazt és egy A-rekordot is a „www” relatív névvel. Mivel a zóna felső pontja (név = @) mindig tartalmazza a zóna létrehozásakor létrejött NS és SOA típusú rekordhalmazokat, a zóna felső pontján nem hozhat létre CNAME-rekordhalmazokat. Ezek a korlátozások a DNS-szabványokból erednek, és nem az Azure DNS korlátozásai.


<!--HONumber=Sep16_HO4-->


