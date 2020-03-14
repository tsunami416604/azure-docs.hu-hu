---
title: Folyamatos átviteli végpontok (forrás)
titleSuffix: Azure Media Services
description: Ismerje meg az adatfolyam-végpontokat (forrás), a dinamikus csomagolást és a streaming szolgáltatást, amely közvetlenül az ügyfél vagy egy Content Delivery Network (CDN) számára biztosít tartalmat.
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
ms.openlocfilehash: 849d1187d6b854d48ad75ab1e55f600407420346
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251257"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming-végpontok (forrás) Azure Media Services

Microsoft Azure Media Services a [folyamatos átviteli végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) egy dinamikus (igény szerinti) csomagolást és forrást jelentenek, amely az élő és az igény szerinti tartalmat közvetlenül egy ügyfél-alkalmazáshoz irányítja, amely a közös Streaming Media protokollok (HLS vagy Dash) egyikét használja. Emellett az adatfolyam- **végpont** dinamikus (igény szerinti) titkosítást biztosít az iparági vezető DRMs számára.

Media Services fiók létrehozásakor a rendszer egy **alapértelmezett** folyamatos átviteli végpontot hoz létre egy leállított állapotban. Az **alapértelmezett** folyamatos átviteli végpont nem törölhető. A fiók alatt több folyamatos átviteli végpont is létrehozható (lásd a [kvótákat és a korlátozásokat](limits-quotas-constraints.md)).

> [!NOTE]
> A streaming-videók elindításához el kell indítania azt a **folyamatos átviteli végpontot** , amelyről továbbítani szeretné a videót.
>
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont futó állapotban van.

## <a name="naming-convention"></a>Elnevezési konvenció

A folyamatos átviteli URL-cím állomásneve a következő: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, ahol `servicename` = a folyamatos átviteli végpont neve vagy az élő esemény neve.

Az alapértelmezett folyamatos átviteli végpont használatakor `servicename` kimarad, így az URL-cím: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Korlátozások

* Az adatfolyam-végpont neve legfeljebb 24 karakterből állhat.
* A névnek ezt a [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) -mintát kell követnie: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Típusok

Kétféle **adatfolyam-végpont** létezik: **standard** (előzetes verzió) és **prémium**. A típust a streaming-végpont számára lefoglalt méretezési egységek (`scaleUnits`) száma határozza meg.

A táblázat a típusokat írja le:

|Típus|Skálázási egységek|Leírás|
|--------|--------|--------|  
|**Standard**|0|Az alapértelmezett folyamatos átviteli végpont egy **standard** típus – a `scaleUnits`módosításával módosíthatja a prémium típust.|
|**Prémium**|>0|**Prémium** szintű A streaming-végpontok speciális számítási feladatokhoz és dedikált és skálázható sávszélesség-kapacitás biztosításához alkalmasak. A **prémium** típusra a `scaleUnits` (folyamatos átviteli egységek) beállításával léphet be. `scaleUnits` dedikált kimenő kapacitást biztosít, amely 200 Mbps-os növekményekben vásárolható meg. A **prémium** típus használata esetén minden engedélyezett egység további sávszélesség-kapacitást biztosít az alkalmazás számára. |

> [!NOTE]
> Azon ügyfelek számára, akik nagy internetes célközönségeknek szánt tartalmat szeretnének kézbesíteni, javasoljuk, hogy engedélyezze a CDN használatát a streaming végponton.

SLA-információ: [díjszabás és SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Adatfolyam-típusok összehasonlítása

Funkció|Standard|Premium
---|---|---
Átviteli sebesség |Akár 600 Mbps, és a CDN használata esetén sokkal nagyobb hatékonyságot biztosít.|200 MB/s átviteli egység (SU). Sokkal nagyobb hatékonyságot biztosíthat a CDN használatakor.
Tartalomkézbesítési hálózat (CDN)|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.
A számlázás arányosan történik| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Automatikus méretezés a célként megadott átviteli sebességgel.|További SUs
IP-szűrés/G20/egyéni gazdagép <sup>1</sup>|Igen|Igen
Progresszív letöltés|Igen|Igen
Ajánlott használat |A folyamatos átviteli forgatókönyvek túlnyomó többségét ajánljuk.|Professzionális használat.

<sup>1</sup> csak akkor használható közvetlenül a folyamatos átviteli végponton, ha a CDN nincs engedélyezve a végponton.<br/>

## <a name="streaming-endpoint-properties"></a>Adatfolyam-végpont tulajdonságai

Ez a szakasz részletesen ismerteti a folyamatos átviteli végpontok tulajdonságait. Az új adatfolyam-végpontok és az összes tulajdonság leírásának ismertetését példákat a [streaming Endpoint (adatfolyam-végpont](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)) című témakörben talál.

- `accessControl`: a következő biztonsági beállítások konfigurálására használható a folyamatos átviteli végponthoz: Akamai aláírási fejléc-hitelesítési kulcsok és IP-címek, amelyek számára engedélyezett a végponthoz való kapcsolódás. Ez a tulajdonság csak akkor állítható be, ha a `cdnEnabled` hamis értékre van állítva.

- `cdnEnabled`: azt jelzi, hogy engedélyezve van-e a streaming-végpont Azure CDN integrációja (alapértelmezés szerint le van tiltva). Ha a `cdnEnabled` igaz értékre állítja, a következő konfigurációk le vannak tiltva: `customHostNames` és `accessControl`.

    Nem minden adatközpont támogatja a Azure CDN integrációt. Az alábbi lépéseket követve ellenőrizheti, hogy az adatközpont rendelkezik-e a Azure CDN integrációs szolgáltatással:

  - Próbálja meg a `cdnEnabled` igaz értékre állítani.
  - Annak ellenőrzéséhez, hogy egy `HTTP Error Code 412` (PreconditionFailed) visszaadott eredménye "a streaming Endpoint CdnEnabled tulajdonság nem állítható be igaz értékre, mert a CDN-képesség nem érhető el az aktuális régióban."

    Ha ezt a hibaüzenetet kapja, az adatközpont nem támogatja azt. Próbálkozzon egy másik adatközpontgal.

- `cdnProfile`: Ha a `cdnEnabled` értéke TRUE (igaz), `cdnProfile` értékeket is megadhat. `cdnProfile` annak a CDN-profilnak a neve, amelyben a CDN-végpontot létrehozza a rendszer. Megadhat egy meglévő cdnProfile, vagy használhat egy újat. Ha az érték NULL, és a `cdnEnabled` értéke igaz, a rendszer az alapértelmezett "AzureMediaStreamingPlatformCdnProfile" értéket használja. Ha a megadott `cdnProfile` már létezik, a rendszer létrehoz egy végpontot. Ha a profil nem létezik, a rendszer automatikusan létrehoz egy új profilt.
- `cdnProvider`: Ha a CDN engedélyezve van, `cdnProvider` értékeket is megadhat. `cdnProvider` szabályozza, hogy melyik szolgáltatót fogja használni. Jelenleg három érték támogatott: "StandardVerizon", "PremiumVerizon" és "StandardAkamai". Ha nincs megadva érték, és a `cdnEnabled` értéke igaz, a rendszer a "StandardVerizon" értéket használja (ez az alapértelmezett érték).
- `crossSiteAccessPolicies`: a különböző ügyfelekhez tartozó helyek közötti hozzáférési házirendek megadására használatos. További információkért lásd: tartományok [közötti házirend fájljának specifikációja](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) és [egy szolgáltatás elérhetővé tétele a tartomány határain belül](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). A beállítások csak Smooth Streamingra vonatkoznak.
- `customHostNames`: egy adatfolyam-végpont konfigurálására szolgál az egyéni állomásnévre irányuló forgalom fogadásához. Ez a tulajdonság a standard és a prémium szintű streaming végpontok esetében érvényes, és akkor állítható be, ha `cdnEnabled`: false.

    Media Servicesnak meg kell erősítenie a tartománynév tulajdonjogát. Media Services ellenőrzi a tartománynevek tulajdonjogát úgy, hogy `CName` rekordot tartalmaz, amely tartalmazza a Media Services fiók AZONOSÍTÓját a használatban lévő tartományhoz hozzáadandó összetevőként. Ha például az "sports.contoso.com" nevet szeretné használni az adatfolyam-végpont egyéni állomásneveként, úgy kell beállítani `<accountId>.contoso.com` egy rekordját, hogy az a Media Services ellenőrző állomásnév egyikére mutasson. Az ellenőrző állomásnév verifydns áll.\<Mediaservices-DNS-Zone >.

    A következő, a különböző Azure-régiókra vonatkozó ellenőrzési rekordban használandó DNS-zónák a következők:
  
  - Észak-Amerika, Európa, Szingapúr, Hongkong KKT, Japán:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Kína

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Például egy `CName` rekord, amely a "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" kifejezést a "verifydns.media.azure.net" értékre képezi, igazolja, hogy a Media Services azonosító 945a4c4e-28ea-45cd-8ccb-a519f6b700ad rendelkezik a contoso.com tartomány tulajdonjogával, így a contoso.com alatti bármely név az adott fiókhoz tartozó streaming-végpont egyéni állomásneveként használható. A Media Service ID értékének megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) , és válassza ki a Media Service-fiókját. A **fiók azonosítója** megjelenik az oldal jobb felső sarkában.

    Ha a `CName` rekord megfelelő ellenőrzése nélkül próbál meg egyéni állomásnevet beállítani, a DNS-válasz meghiúsul, majd egy ideig gyorsítótárazva lesz. Ha megfelelő rekord van érvényben, eltarthat egy ideig, amíg újra nem érvényesítette a gyorsítótárazott választ. Az egyéni tartomány DNS-szolgáltatójától függően néhány perctől akár egy óráig is eltarthat, hogy újraérvényesítse a rekordot.

    Amellett, hogy a `CName`, amely a `verifydns.<mediaservices-dns-zone>``<accountId>.<parent domain>`ra mutat, létre kell hoznia egy másik `CName`, amely az egyéni állomásnevet (például `sports.contoso.com`) leképezi az Media Services streaming-végpont állomásneve (például `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Az ugyanabban az adatközpontban található adatfolyam-végpontok nem oszthatják meg ugyanazt az egyéni állomásnevet.

    A Media Services jelenleg nem támogatja az SSL-t az egyéni tartományokkal.

- `maxCacheAge` – felülbírálja a streaming végpont által beállított alapértelmezett Max-Age HTTP-gyorsítótár-vezérlő fejlécet az adathordozó-töredékeken és az igény szerinti jegyzékeken. Az érték másodpercben van megadva.
- `resourceState` -

    - Leállítva: egy adatfolyam-végpont kezdeti állapota a létrehozás után
    - Indítás: váltás a futó állapotra
    - Futtatás: képes a tartalom továbbítására az ügyfeleknek
    - Skálázás: a méretezési egységek növelése vagy csökkentése folyamatban van
    - Leállítás: a leállított állapotba való váltás folyamatban van
    - Törlés: törlése folyamatban van

- `scaleUnits`: olyan dedikált kimenő kapacitást biztosít, amelyet 200 Mbps-os növekményekben lehet megvásárolni. Ha **prémium** típusúra szeretne lépni, módosítsa `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Miért érdemes több folyamatos átviteli végpontot használni?

Egyetlen Streaming végpont is képes az élő és igény szerinti videók továbbítására, és a legtöbb ügyfél csak egy folyamatos átviteli végpontot használ. Ez a szakasz példákat tartalmaz arra, hogy miért van szükség több folyamatos átviteli végpont használatára.

* Minden fenntartott egység 200 Mbps sávszélességet tesz lehetővé. Ha több mint 2 000 Mbps (2 GB/s) sávszélességre van szüksége, a második adatfolyam-végpontot és a terheléselosztást használva további sávszélességet biztosíthat.

    A CDN azonban a legjobb módszer a tartalmak továbbítására, de ha olyan tartalmat hoz létre, amelyet a CDN több mint 2 GB-nál nagyobb mértékben húz, akkor további folyamatos átviteli végpontokat (Origins) adhat hozzá. Ebben az esetben olyan tartalmi URL-címeket kell kiadnia, amelyek kiegyensúlyozottak a két folyamatos átviteli végpont között. Ez a megközelítés nagyobb gyorsítótárazást tesz lehetővé, mint az egyes forrásokra irányuló kérések véletlenszerűen történő küldése (például egy Traffic Manageren keresztül). 
    
    > [!TIP]
    > Általában, ha a CDN több mint 2 GB-ot húz, akkor előfordulhat, hogy valami helytelenül van konfigurálva (például nem származik a forrás elleni védelem).
    
* Különböző CDN-szolgáltatók terheléselosztása. Például beállíthatja az alapértelmezett folyamatos átviteli végpontot a Verizon CDN használatára, és létrehoz egy másodikat a Akamai használatához. Ezután vegyen fel némi terheléselosztást a kettő között a többszörös CDN-egyensúly eléréséhez. 

    Az ügyfél azonban gyakran végez terheléselosztást több CDN-szolgáltató között egyetlen forrás használatával.
* Vegyes tartalom folyamatos átvitele: igény szerint élő és videó. 

    Az élő és igény szerinti tartalmakhoz való hozzáférési minták nagyon különbözőek. Az élő tartalom általában sok igényt kap ugyanarra a tartalomra. Az igény szerinti videó (például hosszú farok archiválási tartalma) esetében a tartalom használata alacsony. Így a gyorsítótárazás nagyon jól működik az élő tartalomon, de nem a hosszú farok tartalmán is.

    Vegyünk egy olyan forgatókönyvet, amelyben az ügyfelek elsősorban élő tartalmakat nézik, de csak alkalmanként figyelik az igény szerinti tartalmakat, és ugyanabból a streaming-végpontból lesznek kézbesítve. Az igény szerinti tartalom alacsony használata olyan gyorsítótár-területet foglal magában, amely jobb lesz az élő tartalomhoz. Ebben a forgatókönyvben azt javasoljuk, hogy az élő tartalmat egy streaming végpontból és egy másik adatfolyam-végpontból származó hosszú farok tartalma alapján szolgálja ki. Ez növeli az élő esemény tartalmának teljesítményét.
    
## <a name="scaling-streaming-with-cdn"></a>Streamelés méretezése CDN-nel

Lásd az alábbi cikkeket:

- [A CDN áttekintése](../../cdn/cdn-overview.md)
- [Streaming skálázás a CDN-vel](scale-streaming-cdn.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

Az [ebben a tárházban található](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) minta bemutatja, hogyan indíthatja el az alapértelmezett adatfolyam-végpontot a .net-tel.
