---
title: Végpontok streamelése (origin)
titleSuffix: Azure Media Services
description: Ismerje meg a Streamelési végpontokat (Origin), egy dinamikus csomagolási és streamelési szolgáltatást, amely közvetlenül egy ügyféllejátszó alkalmazásnak vagy egy tartalomkézbesítési hálózatnak (CDN) szolgáltat tartalmat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: a8674714451f3cc686acb74d6c66ac6800b75936
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478080"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Végpontok (Origin) streamelése az Azure Media Servicesszolgáltatásban

A Microsoft Azure Media Services szolgáltatásban a [streamelési végpont](https://docs.microsoft.com/rest/api/media/streamingendpoints) egy dinamikus (just-in-time) csomagolási és származási szolgáltatást jelent, amely az élő és az igény szerinti tartalmat közvetlenül az ügyféllejátszó alkalmazásnak juttatja el a közös streamelési médiaprotokollok (HLS vagy DASH) egyikével. Emellett a **streamelési végpont** dinamikus (just-in-time) titkosítást biztosít az iparágvezető DRM-ek számára. 

Amikor létrehoz egy Media Services-fiókot, **egy alapértelmezett** streamelési végpont jön létre az Ön számára leállított állapotban. Az **alapértelmezett** streamelési végpont nem törölhető. További streamelési végpontok hozhatók létre a fiók alatt [(lásd: Kvóták és korlátozások).](limits-quotas-constraints.md)

> [!NOTE]
> A videók streamelésének elindításához el kell indítania azt a **streamelési végpontot,** amelyből a videót streamelni szeretné.
>
> Csak akkor kell fizetnie, ha a streamelési végpont futó állapotban van.

Győződjön meg róla, hogy a [dinamikus csomagolás](dynamic-packaging-overview.md) témakörét is áttekinti. 

## <a name="naming-convention"></a>Elnevezési konvenció

A streamelési URL-cím `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`állomásnév-formátuma: , ahol `servicename` = a streamelési végpont vagy az élő esemény neve.

Az alapértelmezett streamelési `servicename` végpont használatakor a `{accountname}-{regionname}.streaming.azure.net`program kihagyja az URL-címet: .

### <a name="limitations"></a>Korlátozások

* A streamelési végpont neve maximális értéke 24 karakter.
* A névnek ezt a `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`regex mintát kell [követnie:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

## <a name="types"></a>Típusok

Két **streamelési végponttípus** létezik: **Normál** (előzetes verzió) és **Prémium**. A típust a streamelési`scaleUnits`végponthoz lefoglalni kívánt méretezési egységek ( ) száma határozza meg.

A táblázat a típusokat írja le:

|Típus|Skálázási egységek|Leírás|
|--------|--------|--------|  
|**Standard**|0|Az alapértelmezett streamelési végpont **egy standard** típusú – a beállításával prémium típusúra `scaleUnits`módosítható.|
|**Prémium**|>0|**Prémium** A streamelési végpontok speciális számítási feladatokhoz és dedikált és méretezhető sávszélesség-kapacitást biztosítanak. **Prémium** típusúra lép (streamelési `scaleUnits` egységek) módosításával. `scaleUnits`200 Mbps-os lépésekben megvásárolható dedikált kimenő kapacitást biztosít. A **Prémium** típus használataesetén minden engedélyezett egység további sávszélesség-kapacitást biztosít az alkalmazás számára. |

> [!NOTE]
> Azoknak az ügyfeleknek, akik nagy internetes közönségnek szeretnének tartalmat biztosítani, javasoljuk, hogy engedélyezze a CDN-t a streamelési végponton.

Az SLA-val kapcsolatos információkat lásd: [Árképzés és SLA.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="comparing-streaming-types"></a>Adatfolyam-típusok összehasonlítása

Szolgáltatás|Standard|Prémium
---|---|---
Teljesítmény |Akár 600 Mbps, és sokkal nagyobb hatékony átviteli sebesség, ha a CDN-t használja.|200 Mbps streamelési egységenként (SU). CdN-használat esetén sokkal nagyobb hatékony átviteli hatállyal rendelkezhet.
Tartalomkézbesítési hálózat (CDN)|Azure CDN, külső CDN vagy nincs CDN.|Azure CDN, külső CDN vagy nincs CDN.
A számlázás arányosan történik| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Az automatikus skálázás a megcélzott átviteli ig.|További SUs
IP-szűrés/G20/Egyéni állomás <sup>1</sup>|Igen|Igen
Progresszív letöltés|Igen|Igen
Ajánlott használat |A streamelési forgatókönyvek túlnyomó többségéhez ajánlott.|Professzionális használat.

<sup>1</sup> Csak közvetlenül a streamelési végponton használható, ha a CDN nincs engedélyezve a végponton.<br/>

## <a name="streaming-endpoint-properties"></a>Végpont-tulajdonságok streamelése

Ez a szakasz a streamelési végpont tulajdonságainak részleteit tartalmazza. Példák egy új streamelési végpont és az összes tulajdonság leírásának létrehozásáról, lásd: [Végpont streamelése.](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)

- `accessControl`: A következő biztonsági beállítások konfigurálására szolgál ehhez a streamelési végponthoz: Akamai aláírásfejléc-hitelesítési kulcsok és IP-címek, amelyek csatlakozhatnak ehhez a végponthoz. Ez a tulajdonság csak `cdnEnabled` akkor állítható be, ha hamis.

- `cdnEnabled`: Azt jelzi, hogy az Azure CDN-integráció ehhez a streamelési végponthoz engedélyezve van(alapértelmezés szerint letiltva). Ha `cdnEnabled` értéke igaz, a következő konfigurációk `customHostNames` `accessControl`lesz tiltva: és .

    Nem minden adatközpont támogatja az Azure CDN-integrációt. Annak ellenőrzéséhez, hogy az adatközpont rendelkezik-e az Azure CDN-integrációval, tegye a következő lépéseket:

  - Próbálja meg `cdnEnabled` a beállítás az igaz.
  - Ellenőrizze a visszaadott `HTTP Error Code 412` eredményt egy "A végpont CdnEnabled tulajdonság streamelése cdnEnabled tulajdonsággal" üzenettel rendelkező visszaadott eredmény, mert a CDN-képesség nem érhető el az aktuális régióban."

    Ha ez a hibaüzenet jelenik meg, az adatközpont nem támogatja azt. Próbálkozzon egy másik adatközponttal.

- `cdnProfile`: `cdnEnabled` Ha igaz értékre van `cdnProfile` állítva, értékeket is átadhat. `cdnProfile`annak a CDN-profilnak a neve, amelyen a CDN végpont létrejön. Megadhat egy meglévő cdnProfile-t, vagy használhat egy újat. Ha az érték `cdnEnabled` NULL és igaz, a rendszer az alapértelmezett "AzureMediaStreamingPlatformCdnProfile" értéket használja. Ha a `cdnProfile` megadott már létezik, egy végpont jön létre alatta. Ha a profil nem létezik, automatikusan létrejön egy új profil.
- `cdnProvider`: Ha a CDN engedélyezve `cdnProvider` van, értékeket is átadhat. `cdnProvider`szabályozza, hogy melyik szolgáltatót fogja használni. Jelenleg három érték támogatott: "StandardVerizon", "PremiumVerizon" és "StandardAkamai". Ha nincs megadva `cdnEnabled` érték, és igaz, a "StandardVerizon" használatos (ez az alapértelmezett érték).
- `crossSiteAccessPolicies`: A különböző ügyfelek helyközi hozzáférési házirendjeinek megadására szolgál. További információt a [Tartományok közötti házirendfájl-specifikáció](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) és [A szolgáltatás tartományok közötti határok közötti elérhetővé tétele című témakörben](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)talál. A beállítások csak a sima streamelésre vonatkoznak.
- `customHostNames`: A streamelési végpont konfigurálására szolgál az egyéni állomásnévre irányított forgalom fogadására. Ez a tulajdonság a standard és a prémium `cdnEnabled`szintű streamelési végpontokra érvényes, és a következő ben állítható be: false.

    A tartománynév tulajdonjogát a Media Servicesnek meg kell erősítenie. A Media Services ellenőrzi a tartománynév `CName` tulajdonjogát azáltal, hogy a használt tartományhoz összetevőként a Media Services-fiók azonosítóját tartalmazó rekordot kell hozzáadni. Például ahhoz, hogy a "sports.contoso.com" a streamelési végpont egyéni `<accountId>.contoso.com` állomásneveként legyen használva, a rekordot úgy kell konfigurálni, hogy a Media Services-hitelesítési állomásnevek egyikére mutasson. Az ellenőrző állomásnév a verifydns-ből áll. \<mediaservices-dns-zone>.

    A következőkben a várt DNS-zónákat kell használni a különböző Azure-régiók ellenőrzési rekordjában.
  
  - Észak-Amerika, Európa, Szingapúr, Hongkong KKT, Japán:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Kína:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    A "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" és "verifydns.media.azure.net" típusú `CName` rekord például azt bizonyítja, hogy a Media Services 945a4c4e-28ea-45cd-8ccb-a519f6b700ad azonosítója a contoso.com tartomány tulajdonosa, így lehetővé teszi, hogy a contoso.com alatt lévő bármely név egyéni állomásnévként használható legyen a fiók alatt lévő streamelési végponthoz. A Media Service-azonosító értékének megkereséséhez nyissa meg az [Azure Portalon,](https://portal.azure.com/) és válassza ki a Media Service-fiókot. A **fiókazonosító** a lap jobb felső részén jelenik meg.

    Ha a `CName` rekord megfelelő ellenőrzése nélkül próbál egyéni állomásnevet beállítani, a DNS-válasz sikertelen lesz, majd egy ideig gyorsítótárba kerül. Ha a megfelelő rekord a helyén van, eltarthat egy ideig, amíg a gyorsítótárazott válasz újraérvényesítésre kerül. Az egyéni tartomány DNS-szolgáltatójától függően a rekord újraérvényesítése néhány perctől egy óráig tart.

    A hozzá `CName` rendelésre `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>`leképezett lehetőségen kívül létre kell hoznia egy másikat, `CName` amely az egyéni állomásnevet (például `sports.contoso.com`) a Media Services streaming végpontjának állomásnevére (például `amstest-usea.streaming.media.azure.net`) rendeli hozzá.

    > [!NOTE]
    > Az ugyanabban az adatközpontban található streamelési végpontok nem oszthatók meg ugyanazt az egyéni állomásnevet.

    Jelenleg a Media Services nem támogatja a TLS egyéni tartományok.

- `maxCacheAge`- Felülbírálja az alapértelmezett max-age HTTP cache vezérlő fejléc által beállított streamelési végpont a médiatöredékek és az igény szerinti jegyzékek. Az érték másodpercben van beállítva.
- `resourceState` -

    - Leállítva: a streamelési végpont kezdeti állapota a létrehozás után
    - Indítás: átvált a futó állapotba
    - Futás: képes tartalmat streamelni az ügyfeleknek
    - Méretezés: a méretezési egységek növekednek vagy csökkennek
    - Megállás: a leállított állapotba vált
    - Törlés: törlés alatt áll

- `scaleUnits`: 200 Mbps-os lépésekben megvásárolható dedikált kimenő kapacitásbiztosítása. Ha **prémium** típusúra szeretne áthelyezni, állítsa be a beállítást. `scaleUnits`

## <a name="why-use-multiple-streaming-endpoints"></a>Miért érdemes több streamelési végpontot használni?

Egyetlen streamelési végpont élő és igény szerinti videókat is streamelhet, és a legtöbb ügyfél csak egy streamelési végpontot használ. Ez a szakasz néhány példát ad arra, hogy miért lehet több streamelési végpontot használnia.

* Minden lefoglalt egység 200 Mbps sávszélességet tesz lehetővé. Ha több mint 2000 Mb/s (2 Gb/s) sávszélességre van szüksége, a második streamelési végpont és a terheléselosztás segítségével további sávszélességet adhat.

    Azonban a CDN a legjobb módja annak, hogy a streamelési tartalom horizontális felskálázása, de ha olyan sok tartalmat szállít, hogy a CDN több mint 2 Gbps-ot húz, akkor további streamelési végpontokat (eredeteket) adhat hozzá. Ebben az esetben a két streamelési végpontközött kiegyensúlyozott tartalomURL-eket kell kiosztania. Ez a megközelítés jobb gyorsítótárazást biztosít, mint véletlenszerűen küldeni a kérelmeket az egyes eredeti eredetnek (például egy forgalomkezelőn keresztül). 
    
    > [!TIP]
    > Általában, ha a CDN húzza több mint 2 Gbps, akkor valami lehet, hogy rosszul van konfigurálva (például nincs eredeti árnyékolás).
    
* Terheléselosztás különböző CDN-szolgáltatók. Beállíthatja például az alapértelmezett streamelési végpontot a Verizon CDN használatára, és létrehozhat egy másodikat az Akamai használatához. Ezután adjon hozzá némi terheléselosztást a kettő között a több CDN kiegyensúlyozás eléréséhez. 

    Az ügyfelek azonban gyakran egyetlen eredet használatával több CDN-szolgáltató között végeznek terheléselosztást.
* Vegyes tartalom streamelése: Élő és igény szerinti videó. 

    Az élő és az igény szerinti tartalom hozzáférési mintái nagyon eltérőek. Az élő tartalom általában kap egy csomó kereslet az azonos tartalom egyszerre. A videó on-demand tartalom (hosszú farok archív tartalom például) alacsony használat ugyanazon a tartalomon. Így a gyorsítótárazás nagyon jól működik az élő tartalom, de nem olyan jól a hosszú farok tartalmat.

    Fontolja meg egy olyan forgatókönyv, amelyben az ügyfelek elsősorban élő tartalmat néznek, de csak alkalmanként nézik az igény szerinti tartalmat, és ugyanabból a streamelési végpontból szolgálják ki. Az igény szerinti tartalom alacsony használata olyan gyorsítótárterületet foglalna el, amelyet jobban lehetne menteni az élő tartalom számára. Ebben a forgatókönyvben azt javasoljuk, hogy az élő tartalom egy streamelési végpont és a hosszú farok tartalom egy másik streamelési végpont. Ez javítja az élő esemény tartalmának teljesítményét.
    
## <a name="scaling-streaming-with-cdn"></a>Streamelés méretezése CDN-nel

Lásd az alábbi cikkeket:

- [CDN – áttekintés](../../cdn/cdn-overview.md)
- [Streamelés méretezése CDN-nel](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Kérdések et tehet fel, és frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="see-also"></a>Lásd még

[Dinamikus csomagolás](dynamic-packaging-overview.md)

## <a name="next-steps"></a>További lépések

[Streamvégpontok kezelése](manage-streaming-endpoints-howto.md)
