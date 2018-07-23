---
title: DNS-zónák és rekordok áttekintése – Azure DNS-ben |} A Microsoft Docs
description: Támogatási DNS-zónák és a Microsoft Azure DNS-rekordok üzemeltetésére áttekintése.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 7f69d77ac7a6c2a17ef2568f0c7edaef2e1ee3f5
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174415"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS-zónák és rekordok áttekintése

Ezen a lapon a tartományok, a DNS-zónák és a DNS-rekordok és a rekordhalmazok és a hogyan támogatja őket az Azure DNS-ben kapcsolatos főbb fogalmakat ismerteti.

## <a name="domain-names"></a>Tartománynevek

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  Ezek alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”. A tartomány DNS-hierarchiában globálisan elosztott, a világ különböző pontjain található DNS-névkiszolgálókra üzemelteti.

A tartománynév regisztrálójánál az olyan szervezet, amely lehetővé teszi, hogy vásároljon egy tartománynevet, például "contoso.com".  Tartománynév vásárlása lehetővé teszi a DNS-hierarchia a névvel, például lehetővé teszi, hogy a neve "www.contoso.com" a vállalati webhely közvetlen ellenőrzésére vonatkozó jogot. A regisztráló a gazdagép a tartományhoz, a saját névkiszolgálókat az Ön nevében, vagy adhatók meg alternatív névkiszolgálókat.

Az Azure DNS a globálisan elosztott, magas rendelkezésre állású neve kiszolgálói infrastruktúrát, amely segítségével üzemeltessen saját tartományt biztosít. A tartományok Azure DNS-ben üzemelteti, mint az egyéb Azure-szolgáltatások is kezelheti a DNS-rekordokat a hitelesítő adatokkal, API-k, eszközök, számlázás és támogatási.

Az Azure DNS jelenleg nem támogatja a tartománynév-vásárlást. Vásároljon egy tartománynevet szeretne, ha szeretné használni egy külső tartománynév regisztrálójánál. A regisztráló általában csekély éves díjért költséget. A tartomány DNS-rekordok kezelését majd az Azure DNS-ben lehet üzemeltetni. Lásd: [tartomány delegálása az Azure DNS](dns-domain-delegation.md) részleteiről.

## <a name="dns-zones"></a>DNS-zónák

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Élő idő

Az élettartam, vagy a TTL, határozza meg, mennyi ideig minden rekord gyorsítótárazza az ügyfelek előtt újra lekérdezzük folyamatban van. A fenti példában az élettartam 3600 másodperc, vagyis 1 óra.

Az Azure DNS-ben az Élettartama van megadva a rekordhalmaz, nem az egyes rekordokhoz, így a halmaz összes rekordján ugyanaz az érték szolgál.  Megadhatja, hogy bármilyen TTL-érték 1 és 2 147 483 647 másodperc között.

### <a name="wildcard-records"></a>Helyettesítő karaktert tartalmazó rekord

Az Azure DNS [helyettesítő rekordok](https://en.wikipedia.org/wiki/Wildcard_DNS_record) használatát is támogatja. Helyettesítő karaktert tartalmazó rekordot ad vissza minden egyező nevű lekérdezésre válaszul (kivéve, ha van egy nem helyettesítő rekordhalmazból származó közelebbi találat). Az Azure DNS támogatja a helyettesítő rekordhalmazok minden rekord esetében, kivéve az NS és SOA típusú.

Helyettesítő rekordhalmazok létrehozásához használja a rekordhalmaz nevének "\*". Másik lehetőségként használhatja egy nevet a "\*"címkeként bal szélső, például"\*.foo".

### <a name="caa-records"></a>A CAA-rekordok

CAA-rekordok a tartomány tulajdonosai számára mely tanúsítványszolgáltatók (CA) jogosultak a tartományi tanúsítványok engedélyezése. Ez lehetővé teszi a hitelesítésszolgáltatók bizonyos körülmények között esetleg tanúsítványkibocsátás elkerülése érdekében. A CAA-rekordok három jellemzőkkel rendelkeznek:
* **Jelölők**: Ez az egy egész számot 0 és 255, képviseli, amely kiszolgálónként kulcsszó különleges jelentéssel bír a kritikus zászló között a [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Címke**: egy ASCII-karakterlánc, amely a következők egyike lehet:
    * **a probléma**: akkor használja, ha meg szeretné határozni a hitelesítésszolgáltatók, amelyek számára engedélyezett a (minden esetében) tanúsítványok kiállítása
    * **issuewild**: akkor használja, ha meg szeretné határozni engedélyezett (csak helyettesítő karaktert tartalmazó tanúsítványok esetén) tanúsítványainak kiállításához hitelesítésszolgáltatók
    * **iodef**: Adjon meg egy e-mail cím vagy állomásnév hitelesítésszolgáltatók képes értesíteni a jogosulatlan cert probléma kéréseket
* **Érték**: az érték az adott címkével ellátott kiválasztása

### <a name="cname-records"></a>CNAME-rekordok

CNAME-rekordhalmazok nem létezhetnek egyidejűleg más, velük egyező nevű rekordhalmazokkal. Például nem létrehozott "www" relatív nevű CNAME típusú rekordhalmaz és a egy A rekordot "www" relatív nevű egyszerre.

Mivel a zóna felső pontja (név = "\@") mindig tartalmazza az NS és SOA típusú rekordhalmazok a zóna létrehozásakor létrehozott, nem hozható létre egy CNAME rekordot a zóna legfelső pontján állítsa be.

Ezek a korlátozások a DNS-szabványok keletkeznek, és nem az Azure DNS korlátozásai.

### <a name="ns-records"></a>A Névkiszolgálói rekordokat

Az NS rekord a zóna legfelső pontján set (neve "\@") automatikusan jön létre minden egyes DNS-zónát, és a zóna törlésekor a rendszer automatikusan törli (ez nem törölhetők külön-külön).

Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza, a rekordhalmaz. Hozzáadhat további névhez e NS-rekord-kiszolgálókat, támogatja a közös üzemeltetési tartomány több DNS-szolgáltatónál. Az élettartam és a rekordhalmaz metaadatait is módosíthatja. Azonban nem távolítsa el vagy módosítsa az ki van töltve az Azure DNS névkiszolgálóit. 

Ez csak a zóna legfelső pontján NS típusú rekordhalmaz vonatkozik. Más Névkiszolgálói rekordhalmazt a zónában (a használt gyermek zónák delegálása) létrehozott, módosított és korlátozás nélkül.

### <a name="soa-records"></a>SOA-rekord

SOA típusú rekordhalmazok automatikusan jön létre minden zóna tetején található rekordokra (név = "\@"), és a zóna törlésekor a rendszer automatikusan törli.  SOA-rekord nem hozható létre vagy külön-külön törölve.

A SOA típusú rekordját a "host" tulajdonságot, amely előre konfigurálva tekintse meg az Azure DNS által nyújtott elsődleges név kiszolgálónév kivételével az összes tulajdonságait módosíthatja.

### <a name="spf-records"></a>Az SPF-rekordok

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-rekordok

[SRV-rekordok](https://en.wikipedia.org/wiki/SRV_record) különböző szolgáltatások által használt kiszolgáló helyet adhat meg. Amikor ad meg egy SRV-rekordot az Azure DNS-ben:

* A *szolgáltatás* és *protokoll* aláhúzásjeleket előtaggal van ellátva, a rekordhalmaz nevének részeként meg kell adni.  Például "\_sip.\_ TCP.Name ".  Egy rekord a zóna legfelső pontján, nem adja meg, nincs szükség a(z)\@"a rekord neve egyszerűen használja a szolgáltatás és a protokoll, például"\_sip.\_ TCP ".
* A *prioritású*, *súly*, *port*, és *cél* paraméterei a rekordhalmaz egyes rekordjainak vannak megadva.

### <a name="txt-records"></a>Txt típusú rekordok

Txt típusú rekordok tartománynevek leképezése a tetszőleges szöveges karakterláncok használhatók. Több alkalmazás, különösen e-mail-konfigurációval kapcsolatos, például használják őket a [küldő házirend keretrendszer (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) és [DomainKeys azonosított Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

A DNS-szabványok engedélyeznek egy egyetlen txt típusú rekordot tartalmazhat több karakterláncok, amelyek mindegyike legfeljebb 254 karakter hosszúságú lehet. Ha több karakterlánc használnak, az ügyfelek által összefűzött, és egy egyetlen karakterláncként.

Az Azure DNS – REST API hívásakor kell külön-külön mindegyik TXT karakterláncot kell megadni.  Ha az Azure portal használatával, PowerShell vagy parancssori felület adjon meg egy rekord, amely automatikusan oszlik 254 karakterből álló szegmensek szükség esetén egyetlen karakterlánc.

A több karakterláncokat, a DNS-rekord nem tévesztendő a több txt típusú rekordok txt típusú rekordhalmaz.  Egy txt típusú rekordhalmaz több rekordot is tartalmazhat *, amelyek mindegyike* több karakterláncokat is tartalmazhat.  Az Azure DNS támogatja (között az összes rekordot kombinált) minden egyes txt típusú rekordhalmaz karakterlánc teljes hossza legfeljebb 1024 karakter hosszú lehet.

## <a name="tags-and-metadata"></a>A címkék és a metaadatok

### <a name="tags"></a>Címkék

Címkék név-érték párok listáját, és vannak az Azure Resource Manager által használt erőforrások.  Az Azure Resource Manager engedélyezése az Azure-elszámolások szűrt nézeteinek címkéket használ, és lehetővé teszi, hogy a címkék olyan szükséges szabályzat beállítása. A címkékkel kapcsolatos további információért tekintse meg [Az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md) című cikket.

Az Azure DNS támogatja az Azure Resource Manager-címkék használatát a DNS-zóna erőforrás.  Nem támogatja a címkék a DNS-rekordhalmazok, bár, támogatja a "metaadatok" alternatív DNS-rekord beállítja, kövesse az alábbi utasításokat.

### <a name="metadata"></a>Metaadatok

Rekordhalmaz címkék helyett az Azure DNS támogatja a jegyzetkészítés rekordhalmazok "metaadatok" segítségével.  Címkék hasonlóan, metaadatok lehetővé teszi minden rekordhalmaz név-érték párok társítani.  Ez lehet hasznos, például a rekord minden rekordhalmaz célját.  Ellentétben a címkék és a metaadatok nem használható az Azure-elszámolások szűrt nézetét biztosít, és a egy Azure Resource Manager-szabályzatban nem adható meg.

## <a name="etags"></a>ETag

Tegyük fel, hogy a két személynek vagy két folyamatok próbálja meg módosítani a DNS-rekord egy időben. Melyik wins? És a győztes nem tudja, hogy azok már módosításait mások által létrehozott felülírja?

Az Azure DNS a megváltozott forgalommennyiség egyidejű ugyanarra az erőforrásra biztonságosan ETag használ. ETag elkülönülnek [Azure Resource Manager "Címkék"](#tags). Minden DNS-erőforrás (zóna vagy rekordhalmaz) egy hozzá társított ETag címkével rendelkezik. Minden alkalommal, amikor egy erőforrást a rendszer lekéri, az ETag címkéje is lekéri. Egy erőforrás frissítésekor kiválaszthatja adja át vissza az ETag címke, az Azure DNS-ben is ellenőrizze, hogy az Etag a kiszolgáló megfelel. Minden frissítés egy erőforráshoz újragenerálása folyamatban van az Etag eredményez, mivel az Etag nem egyezik azt jelzi, egyidejű változás történt. ETag is segítségével új erőforrás létrehozásakor győződjön meg arról, hogy az erőforrás még nem létezik.

Alapértelmezés szerint az Azure DNS PowerShell használatával ETag egyidejű letiltása a zónák és -rekordhalmazok. A választható *-felülírása* kapcsoló használható Etag ellenőrzések letiltásához, ebben az esetben minden egyidejű bekövetkezett változásokat a rendszer felülírja.

Az Azure DNS – REST API a szintjén ETag megadott HTTP-fejlécek használata.  Viselkedésük van megadva a következő táblázatban:

| Fejléc | Viselkedés |
| --- | --- |
| None |PUT mindig sikeres (nincs Etag-ellenőrzések) |
| IF-match <etag> |PUT csak akkor sikeres, ha az erőforrás létezik, és Etagje megegyezik |
| IF-match * |PUT csak akkor sikeres, ha az erőforrás létezik |
| IF-none-match * |PUT csak akkor sikeres, ha az erőforrás nem létezik. |


## <a name="limits"></a>Korlátok

Az alábbi alapértelmezett korlátok vonatkoznak az Azure DNS használata esetén:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>További lépések

* Az Azure DNS használatának megkezdéséhez, megtudhatja, hogyan [DNS-zóna létrehozása](dns-getstarted-create-dnszone-portal.md) és [DNS-rekordok létrehozása](dns-getstarted-create-recordset-portal.md).
* Át egy meglévő DNS-zónát, megtudhatja, hogyan [importálása és exportálása a DNS-zónafájl](dns-import-export.md).
