---
title: DNS-zónák és rekordok – Azure DNS –
description: Dns-zónák és rekordok Microsoft Azure DNS-ben való üzemeltetésének támogatásának áttekintése.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265154"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS-zónák és rekordok áttekintése

Ez a lap ismerteti a tartományok, DNS-zónák és DNS-rekordok és rekordkészletek legfontosabb fogalmait, valamint azt, hogy ezek hogyan támogatottak az Azure DNS-ben.

## <a name="domain-names"></a>Tartománynevek

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  Ezek alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”. A DNS-hierarchia tartományai globálisan vannak elosztva, és a DNS-névkiszolgálók világszerte üzemeltetik őket.

A tartománynév-regisztráló olyan szervezet, amely lehetővé teszi tartománynév vásárlását, például `contoso.com`.  A tartománynév vásárlása feljogosítja arra, hogy ezen a néven vezérelje `www.contoso.com` a DNS-hierarchiát, például lehetővé teszi, hogy a nevet a vállalat webhelyére irányítsa. A regisztrátor a tartományt a saját névkiszolgálóin üzemeltetheti az Ön nevében, vagy engedélyezheti alternatív névkiszolgálók megadását.

Az Azure DNS globálisan elosztott, magas rendelkezésre állású névkiszolgáló-infrastruktúrát biztosít, amely segítségével üzemeltetheti a tartományt. A tartományok Azure DNS-ben való üzemeltetésével kezelheti DNS-rekordjait ugyanazzal a hitelesítő adatokkal, API-kkal, eszközökkel, számlázással és támogatással, mint a többi Azure-szolgáltatás.

Az Azure DNS jelenleg nem támogatja a tartománynevek vásárlását. Ha tartománynevet szeretne vásárolni, külső tartománynév-regisztrálót kell használnia. Az anyakönyvvezető általában egy kis éves díjat számít fel. A tartományok ezután üzemeltethető az Azure DNS-ben a DNS-rekordok kezeléséhez. További információkért tekintse meg a [tartományok az Azure DNS-be való delegálását](dns-domain-delegation.md) ismertető cikket.

## <a name="dns-zones"></a>DNS-zónák

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Az élethez való idő

Az élettartam ideje (TTL) határozza meg, hogy az egyes rekordok at mennyi ideig gyorsítótárazzák az ügyfelek a visszabeszélés előtt. A fenti példában a TTL 3600 másodperc vagy 1 óra.

Az Azure DNS-ben a TTL van megadva a rekordkészlet, nem minden rekord, így ugyanazt az értéket használja az adott rekordkészleten belüli összes rekord.  Bármilyen TTL értéket megadhat 1 és 2 147 483 647 másodperc között.

### <a name="wildcard-records"></a>Helyettesítő karakteres rekordok

Az Azure DNS [helyettesítő rekordok](https://en.wikipedia.org/wiki/Wildcard_DNS_record) használatát is támogatja. A helyettesítő karakteres rekordokat a rendszer minden egyező nevű lekérdezésre válaszul adja vissza (kivéve, ha egy nem helyettesítő karakter rekordkészletből közelebbi egyezés van). Az Azure DNS támogatja a helyettesítő rekordkészletek et az NS és a SOA kivételével az összes rekordtípushoz.

Helyettesítő karakterrekord-készlet létrehozásához használja a\*rekordkészlet nevét ' ' . Másik lehetőségként használhat egy nevet\*is , amelynek bal szélső címkéje a ' , például a '\*.foo .

### <a name="caa-records"></a>CAA rekordok

A CAA-rekordok lehetővé teszik a tartománytulajdonosok számára, hogy meghatározzák, mely hitelesítésszolgáltatók jogosultak tanúsítványokat kiadni a tartományukhoz. Ez lehetővé teszi a hitelesítésszolgáltatók számára, hogy bizonyos körülmények között elkerüljék a tanúsítványok téves kiállítását. A CAA rekordok három tulajdonsággal rendelkeznek:
* **Jelzők**: Ez egy 0 és 255 közötti egész szám, amely az [RFC-n](https://tools.ietf.org/html/rfc6844#section-3) ként különleges jelentéssel bíró kritikus jelzőt jelöli.
* **Címke**: ASCII karakterlánc, amely a következők egyike lehet:
    * **probléma**: használja ezt, ha olyan hitelesítésleveleket szeretne megadni, amelyek jogosultak tanúsítványkiadásra (minden típus)
    * **issuewild**: használja ezt, ha olyan hitelesítésleveleket szeretne megadni, amelyek jogosultak tanúsítványkiállításra (csak helyettesítő karakterekkel)
    * **iodef**: adjon meg egy e-mail címet vagy állomásnevet, amelyre a hitelesítésszolgáltatói nem engedélyezett tanúsítványkibocsátási kérelmekesetén értesítést küldhetnek
* **Érték**: az adott kiválasztott címke értéke

### <a name="cname-records"></a>CNAME rekordok

CNAME-rekordhalmazok nem létezhetnek egyidejűleg más, velük egyező nevű rekordhalmazokkal. Nem hozhat létre például olyan CNAME rekordkészletet, amelynek relatív neve "www" és egy "Www" relatív névvel rendelkező A rekord egyszerre.

Mivel a zóna csúcsa\@(name = ' ') mindig tartalmazza a zóna létrehozásakor létrehozott NS- és SOA-rekordkészleteket, a zóna csúcsán nem hozhat létre CNAME rekordkészletet.

Ezek a korlátozások a DNS-szabványokból származnak, és nem korlátozzák az Azure DNS-t.

### <a name="ns-records"></a>NS-rekordok

A zóna csúcsán beállított NS rekord\@(név ' ' ' ) automatikusan létrejön az egyes DNS-zónákkal, és a zóna törlésekor automatikusan törlődik (nem törölhető külön).

Ez a rekordkészlet a zónához rendelt Azure DNS-névkiszolgálók nevét tartalmazza. A ns rekordkészlethez további névkiszolgálókat is hozzáadhat, így több DNS-szolgáltatóval is támogathatja a tartományok közös üzemeltetését. A rekordkészlet TTL- és metaadatait is módosíthatja. Az előre kitöltött Azure DNS-névkiszolgálók azonban nem távolíthatók el és nem módosíthatók. 

Ez csak a zóna csúcsán beállított NS rekordra vonatkozik. A zónában lévő egyéb ns rekordkészletek (a gyermekzónák delegálásához használt) megkötés nélkül hozhatók létre, módosíthatók és törölhetők.

### <a name="soa-records"></a>SOA rekordok

A SOA rekordkészlet automatikusan létrejön az egyes zónák\@csúcsán (név = ' '), és a zóna törlésekor automatikusan törlődik.  A SOA rekordok nem hozhatók létre és nem törölhetők külön-külön.

A SOA-rekord összes tulajdonságát módosíthatja, kivéve a "host" tulajdonságot, amely előre konfigurálva van az Azure DNS által biztosított elsődleges névkiszolgáló nevére.

A SOA rekordban lévő zóna sorozatszáma nem frissül automatikusan, amikor a zóna rekordjait módosítja. Szükség esetén manuálisan frissíthető a SOA rekord szerkesztésével.

### <a name="spf-records"></a>SPF-rekordok

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV rekordok

[Az SRV rekordokat](https://en.wikipedia.org/wiki/SRV_record) különböző szolgáltatások használják a kiszolgálóhelyek megadására. SRV rekord megadása az Azure DNS-ben:

* A *szolgáltatást* és *a protokollt* a rekordkészlet nevének részeként kell megadni, aláhúzásjelekkel előtaggal.  Például'\_korty. \_tcp.name".  A zóna csúcsán lévő rekordhoz nem szükséges megadni\@' ' a rekord nevében, egyszerűen használja\_a szolgáltatást és a protokollt, például ' sip. \_tcp".
* A *prioritás,* *súly,* *port*és *cél* a rekordkészlet minden egyes rekordjának paramétereiként van megadva.

### <a name="txt-records"></a>TXT rekordok

A TXT rekordok a tartománynevek tetszőleges szöveges karakterláncokhoz való hozzárendelésére szolgálnak. Ezeket több alkalmazásban is használják, különösen az e-mailek konfigurálásával kapcsolatban, például a [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) és a [DomainKeys Identified Mail (DKIM) rendszerben.](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)

A DNS-szabványok lehetővé teszik, hogy egyetlen TXT rekord több karakterláncot tartalmazzon, amelyek mindegyike legfeljebb 254 karakter hosszúságú lehet. Ha több karakterláncot használ, az okat az ügyfelek összefűzik, és egyetlen karakterláncként kezelik őket.

Az Azure DNS REST API hívásakor minden Egyes TXT-karakterláncot külön kell megadnia.  Az Azure Portal, PowerShell vagy CLI-felületek használatakor meg kell adnia egy karakterláncot rekordonként, amely szükség esetén automatikusan 254 karakteres szegmensekre oszlik.

A DNS-rekord ban lévő több karakterláncot nem szabad összetéveszteni a TXT rekordkészlet több TXT rekordjával.  A TXT rekordkészlet több rekordot is tartalmazhat, *amelyek mindegyike* több karakterláncot is tartalmazhat.  Az Azure DNS támogatja a teljes karakterlánc hossza legfeljebb 1024 karakter minden TXT rekordkészlet (az összes rekord együtt).

## <a name="tags-and-metadata"></a>Címkék és metaadatok

### <a name="tags"></a>Címkék

A címkék a név-érték párok listája, és az Azure Resource Manager az erőforrások címkézésére szolgál.  Az Azure Resource Manager címkék et használ az Azure-számlád szűrt nézeteinek engedélyezéséhez, és lehetővé teszi, hogy beállítson egy szabályzatot, amelyhez címkék szükségesek. Címkékkel kapcsolatos további információ: [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).

Az Azure DNS támogatja az Azure Resource Manager-címkék használatát a DNS-zóna erőforrásain.  Nem támogatja a címkéket a DNS-rekordhalmazokon, bár alternatív "metaadatként" a DNS-rekordhalmazok az alábbiakszerint támogatottak.

### <a name="metadata"></a>Metaadatok

A rekordhalmaz-címkék alternatívájaként az Azure DNS támogatja a "metaadatok" használatával a rekordkészletek jegyzetelését.  A címkékhez hasonlóan a metaadatok lehetővé teszik, hogy név-érték párokat társítson az egyes rekordhalmazokhoz.  Ez hasznos lehet például az egyes rekordhalmazok céljának rögzítéséhez.  A címkékkel ellentétben a metaadatok nem használhatók az Azure-számla szűrt nézetének biztosításához, és nem adhatók meg az Azure Resource Manager-szabályzatban.

## <a name="etags"></a>Címkék

Tegyük fel, hogy két vagy két folyamat egyszerre próbál meg módosítani egy DNS-rekordot. Melyik nyer? És tudja-e a győztes, hogy felülírta a valaki más által létrehozott módosításokat?

Az Azure DNS etag-ok segítségével kezeli az ugyanazon erőforrás egyidejű módosításait. Az e-címkék elkülönülnek az [Azure Resource Manager "Címkék" címkéitől.](#tags) Minden DNS-erőforráshoz (zónához vagy rekordkészlethez) tartozik egy Etag. Amikor egy erőforrást beolvas, az Etag is beolvassa. Egy erőforrás frissítésekor dönthet úgy, hogy adja vissza az Etag, így az Azure DNS ellenőrizheti, hogy az Etag a kiszolgálón egyezik. Mivel egy erőforrás minden egyes frissítése az Etag újragenerálását eredményezi, az Etag eltérés azt jelzi, hogy egyidejű változás történt. Etags is használható, amikor egy új erőforrást, hogy megbizonyosodjon arról, hogy az erőforrás még nem létezik.

Alapértelmezés szerint az Azure DNS PowerShell etagokat használ a zónák és rekordkészletek egyidejű módosításának blokkolására. Az opcionális *-Overwrite* kapcsoló az Etag-ellenőrzések letiltására használható, amely esetben a rendszer felülírja az egyidejű módosításokat.

Az Azure DNS REST API szintjén az Etag http-fejlécek használatával van megadva.  Viselkedésük a következő táblázatban található:

| Fejléc | Viselkedés |
| --- | --- |
| None |Put mindig sikeres (nem Etag ellenőrzések) |
| Ha egyező \<etag> |A PUT csak akkor sikerül, ha létezik erőforrás, és az Etag megegyezik |
| Ha-match * |A PUT csak akkor sikerül, ha létezik erőforrás |
| Ha nincs meccs * |A PUT csak akkor jár sikerrel, ha az erőforrás nem létezik |


## <a name="limits"></a>Korlátok

Az Azure DNS használatakor a következő alapértelmezett korlátozások érvényesek:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>További lépések

* Az Azure DNS használatának megkezdéséhez olvassa el, hogyan [hozhat létre DNS-zónát](dns-getstarted-create-dnszone-portal.md) és [hozhat létre DNS-rekordokat.](dns-getstarted-create-recordset-portal.md)
* Meglévő DNS-zóna áttelepítéséhez olvassa el a [DNS-zónafájl importálásának és exportálásának](dns-import-export.md)módját.
