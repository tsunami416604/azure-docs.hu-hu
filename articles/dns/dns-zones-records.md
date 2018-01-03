---
title: "DNS-zónák és rekordok áttekintés – Azure DNS |} Microsoft Docs"
description: "DNS-zónák és a Microsoft Azure DNS-rekordok támogatása áttekintése."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: 0a0808d3963cc037aaf113c67fd01679ee8c1d40
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="overview-of-dns-zones-and-records"></a>A DNS-zónák és áttekintése

Ezen a lapon a tartományok, a DNS-zónák, és a DNS-rekordok és a rekordhalmazok és a hogyan támogatja őket az Azure DNS-ben kapcsolatos főbb fogalmakat ismerteti.

## <a name="domain-names"></a>Tartománynevek

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  Ezek alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”. A tartomány DNS-hierarchiában globálisan fel vannak osztva, DNS-névkiszolgálók világszerte üzemelteti.

A tartományregisztráló egy szervezet, amely lehetővé teszi egy, a tartomány nevét, például "contoso.com" beszerzési.  Tartománynév megvásárlásáról jogot biztosít a DNS-hierarchia a névvel, például hogy lehetővé teszi a neve "www.contoso.com" a vállalati webhely közvetlen szabályozására. A regisztráló a gazdagép a tartományhoz, a saját névkiszolgálók az Ön nevében, vagy adható meg alternatív névkiszolgálók.

Az Azure DNS biztosít globálisan elosztott, magas rendelkezésre állású neve kiszolgálói infrastruktúrát, amely a tartomány-segítségével. Az Azure DNS-tartományt üzemeltet, kezelheti az egyéb Azure-szolgáltatások, a DNS-rekordokat a ugyanazokat a hitelesítő adatokat, API-k, eszközök, számlázási és támogatási.

Az Azure DNS jelenleg nem támogatja tartománynevek megvásárlását. Ha szeretne vásárolni a tartomány nevét, egy külső regisztrációs használni szeretné. A regisztráló általában egy kis éves díj költségek. A tartomány DNS-rekordok Management majd az Azure DNS-lehet üzemeltetni. Lásd: [az Azure DNS-tartomány delegálása az](dns-domain-delegation.md) részleteiről.

## <a name="dns-zones"></a>DNS-zónák

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-rekordok

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Élő idő

Az élettartam (TTL) megadja, hogy az ügyfelek mennyi ideig gyorsítótárazzák az egyes rekordokat az újbóli lekérdezés előtt. A fenti példában az élettartam 3600 másodperc, vagyis 1 óra.

Az Azure DNS szolgáltatásban a TTL van megadva a rekordhalmaz, nem az egyes rekordokhoz, így a halmaz összes rekordján ugyanaz az érték szolgál.  Megadhatja, hogy bármely élettartam értéke 1 és 2 147 483 647 másodperc között.

### <a name="wildcard-records"></a>Helyettesítő rekordok

Az Azure DNS [helyettesítő rekordok](https://en.wikipedia.org/wiki/Wildcard_DNS_record) használatát is támogatja. Helyettesítő karakteres rekordot ad vissza semmilyen egyező nevű lekérdezésre válaszul (kivéve, ha egy nem helyettesítő rekordhalmaz származó közelebbi találat). Az Azure DNS támogatja a helyettesítő rekordhalmazok minden rekordtípus, kivéve az NS és SOA.

Helyettesítő rekordhalmaz létrehozásához használja a következő rekordhalmaznevet "\*". Másik megoldásként használhatja nevet "\*", a bal szélső címkéjével, például"\*.foo".

### <a name="caa-records"></a>CAA rekordok

CAA rekordok lehetővé teszi a adhatja meg, melyik tanúsítványszolgáltatók (CA) jogosultak a tanúsítványokat a tartományhoz tartozó tartományi tulajdonosai. Ez lehetővé teszi a hitelesítésszolgáltatók nem tud kiállítani tanúsítványokat bizonyos körülmények között elkerülése érdekében. CAA rekordok három jellemzőkkel rendelkezik:
* **Jelzők**: Ez az 0 és 255, a kritikus jelzőt, amely a kulcsszó különleges jelentéssel bír száma ábrázolásához közötti egész számot a [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Címke**: egy ASCII karakterlánc, amely a következők egyike lehet:
    * **a probléma**: akkor használja, ha meg szeretné határozni a (minden esetében) tanúsítványainak kiállításához engedélyezett hitelesítésszolgáltatók
    * **issuewild**: akkor használja, ha meg szeretné határozni (csak helyettesítő tanúsítványok) tanúsítványainak kiállításához engedélyezett hitelesítésszolgáltatók
    * **iodef**: Adjon meg egy e-mail címet vagy állomásnevet, amelyre a hitelesítésszolgáltatók értesítheti jogosulatlan cert probléma kérelmek
* **Érték**: a kiválasztott adott címke értéke

### <a name="cname-records"></a>A CNAME-rekordok

CNAME-rekordhalmazok nem létezhetnek egyidejűleg más, velük egyező nevű rekordhalmazokkal. Például akkor nem hozható létre egy CNAME-rekordhalmazt a "www" relatív név és egy A rekordot "www" relatív névvel egy időben.

Mivel a zóna felső pontja (név = "@") mindig tartalmazza az NS és SOA típusú rekordkészletek a zóna létrehozásakor létrehozott, nem hozható létre egy CNAME rekordot a zóna felső pontja állítják.

Ezek a megkötések a DNS-szabványokból erednek, és nem az Azure DNS korlátozásai.

### <a name="ns-records"></a>A Névkiszolgálói rekordokat

Az NS rekord a zóna felső pontja beállított (neve "@") automatikusan létrejön minden DNS-zónát, és automatikusan törlődik, ha a zóna törlődik (ezt nem lehet törölni külön-külön).

A rekordhalmaz az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza. Hozzáadhat további névhez a kiszolgálók e NS-rekord, támogatja a párhuzamos üzemeltetési tartományok egynél több DNS-szolgáltatónál. A TTL-t és a metaadatok a rekordhalmaz is módosíthatók. Azonban nem lehet eltávolítani, vagy módosítsa az előre megadott Azure DNS névkiszolgálóit. 

Vegye figyelembe, hogy csak vonatkozik az NS típusú rekordhalmaz zóna tetején. A zónában (a használt gyermekzónákhoz delegálása) más NS-rekordhalmazok létrehozott, módosíthatók és korlátozás nélkül törli.

### <a name="soa-records"></a>SOA-rekord

SOA típusú rekordhalmaz automatikusan jön létre minden zóna tetején (név = "@"), és a zóna törlésekor automatikusan törlődik.  SOA rekordokat nem hozható létre vagy törölt külön-külön.

Módosíthatja a SOA-rekord kivételével a "fogadó" tulajdonsággal, amely lehet hivatkozni az Azure DNS által megadott elsődleges név kiszolgálónév előre konfigurálva van az összes tulajdonságait.

### <a name="spf-records"></a>SPF-rekordokat

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-rekordok

[SRV-rekordok](https://en.wikipedia.org/wiki/SRV_record) különböző szolgáltatások által használt kiszolgáló helyének megadásához. Ha egy SRV rekordot az Azure DNS:

* A *szolgáltatás* és *protokoll* a rekordhalmaz nevének és aláhúzás karaktereket tartalmazhatnak előtagként részeként meg kell adni.  Például "\_sip.\_ TCP.Name ".  Egy rekord a zóna tetején, nincs szükség az adja meg a "@" a rekord nevében egyszerűen a szolgáltatást és protokollt használ, például "\_sip.\_ TCP ".
* A *prioritás*, *súly*, *port*, és *cél* paraméterei a rekordhalmaz a rekordokban vannak megadva.

### <a name="txt-records"></a>TXT-rekord

TXT rekord használt tartománynevek hozzárendelése tetszőleges szövegeit tartalmazó karakterláncok. Több alkalmazás, különösen e-mail konfigurációjával kapcsolatos, például a használatuk a [küldő házirend keretrendszer (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) és [DomainKeys azonosított Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

A DNS-szabványokból lehetővé teszik több karakterláncok, akár 254 karakter hosszúságú lehet, amelyek mindegyike tartalmazhat egy TXT rekord. Ha több karakterláncot használnak, ezeket ügyfelek összefűzendő és egyetlen karakterláncként kezelni.

Az Azure DNS REST API hívásakor kell külön-külön megadása minden TXT-karakterlánc.  Az Azure portál használata esetén PowerShell vagy a CLI-felületekkel adjon meg egy rekord, amely automatikusan oszlik 254 karakter szegmensek szükség esetén egy karakterlánc.

A DNS-rekord több karakterláncok nem keverendő kell egy TXT rekordkészlete több TXT rekordjait.  TXT rekord is tartalmazhatnak több rekord *, amelyek mindegyike* több karakterláncokat tartalmazhat.  Az Azure DNS minden TXT rekord (történő bejegyzésekkel együtt) támogatja a teljes karakterlánc hossza legfeljebb 1024 karakter hosszú lehet.

## <a name="tags-and-metadata"></a>Címkék és metaadatok

### <a name="tags"></a>Címkék

Címkék név-érték párok listáját, és Azure Resource Manager által használt címke erőforrásokhoz.  Az Azure Resource Manager címkék teszi lehetővé az Azure számlázásának szűrt nézeteinek, és lehetővé teszi új házirend címkék szükség. A címkékkel kapcsolatos további információért tekintse meg [Az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md) című cikket.

Az Azure DNS támogatja az Azure Resource Manager címkék használatával a DNS-zóna erőforrás.  Nem támogatja címkék a DNS-rekordhalmazok, bár a "metadata" alternatív támogatja a DNS-rekord beállítja, ahogy az alábbi leírásban.

### <a name="metadata"></a>Metaadatok

Rekordhalmaz címkék alternatívájaként Azure DNS-ben támogatja a ellátása megjegyzésekkel rekordhalmazok "metadata" használatával.  Címkék hasonló, metaadatok lehetővé teszi minden rekordhalmaz név-érték párok társítani.  Ez akkor lehet hasznos, például a rekord minden rekordhalmaz célját.  Címkék, ellentétben a metaadatok nem használható az Azure számlázásának szűrt megjelenítésére szolgáló, és nem adható meg az Azure Resource Manager-házirend.

## <a name="etags"></a>ETag-EK

Tegyük fel, hogy a két személynek vagy két folyamatok próbálja módosítani a DNS-rekord egy időben. Melyik wins? És a győztes nem tudja, hogy azok már felül más által létrehozott módosításokat?

Az Azure DNS ETag-EK ugyanahhoz a erőforráshoz egyidejű változtatások biztonságosan kezelésére használja. ETag-EK nem azonosak a [Azure Resource Manager "Címke"](#tags). Minden DNS-erőforrásrekordok (zóna vagy rekordhalmaz) rendelkezik egy ETag-gel társítva. Erőforrás lekérése, amikor a rendszer is lekéri az Etag. Egy erőforrás frissítésekor kiválaszthatja továbbítására vissza az Etag, az Azure DNS-ben is ellenőrizze, hogy az Etag a kiszolgáló megfelel. Minden egyes frissítés egy erőforráshoz eredményez a helyreállítás alatt Etag, mert az egy Etag nem egyezik azt jelzi, egyidejű módosítás történt. ETag-EK is használhatja új erőforrás létrehozása annak biztosítására, hogy az erőforrás nem létezik.

Alapértelmezés szerint az Azure DNS PowerShell zónák egyidejű módosításai és rekordkészletek ETag-EK használja. A választható *-felülírási* kapcsoló Etag ellenőrzések letiltásához használható, amely esetben minden egyidejű végrehajtott módosításokat a rendszer felülírja.

Az Azure DNS REST API szintjén ETag-EK megadott HTTP-fejlécek használatával.  A következő táblázatban azok viselkedését adja:

| Fejléc | Viselkedés |
| --- | --- |
| Nincs |A PUT mindig sikeres (nincs Etag ellenőrzése) |
| IF-match<etag> |PUT csak akkor sikeres, ha erőforrás létezik, és az Etag megfelel |
| IF-match * |A PUT csak akkor sikeres, ha erőforrás létezik-e |
| IF-none-match * |PUT csak akkor sikeres, ha az erőforrás nem létezik. |


## <a name="limits"></a>Korlátok

A következő alapértelmezett korlátokat alkalmazza, ha Azure DNS-sel:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>További lépések

* Azure DNS használatának megkezdéséhez megtudhatja, hogyan [hozzon létre egy DNS-zóna](dns-getstarted-create-dnszone-portal.md) és [DNS-rekordok létrehozása](dns-getstarted-create-recordset-portal.md).
* Egy meglévő DNS-zóna áttelepítéséhez megtudhatja, hogyan [importálni és exportálni egy DNS-zónafájlját](dns-import-export.md).
