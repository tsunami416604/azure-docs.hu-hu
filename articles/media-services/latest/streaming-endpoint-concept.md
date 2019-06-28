---
title: Streamvégpontok (forrás) az Azure Media Servicesben |} A Microsoft Docs
description: Az Azure Media Services egy folyamatos átviteli végponton (forrás) jelöli, a dinamikus becsomagolást és a streamelési szolgáltatás, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy az egy Content Delivery Network (CDN) további terjesztés céljából.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: ab74b778757aefc22f66e8b52d1f1d922526f14a
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296135"
---
# <a name="streaming-endpoints"></a>Streamvégpontok 

A Microsoft Azure Media Services szolgáltatásban egy [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints) jelöli egy dinamikus (just-in-time) csomagolás és a forrás szolgáltatás, amely az élő és igény szerinti tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, az egyik a gyakori streamelési protokollok (HLS vagy DASH). Emellett a **folyamatos átviteli végponton** iparágvezető DRMs dinamikus (just-in-time) titkosítást nyújt.

Egy Media Services-fiók létrehozásakor egy **alapértelmezett** folyamatos átviteli végponton, létrehozott egy leállított állapotban. Nem lehet törölni a **alapértelmezett** folyamatos átviteli végponton. További Streamelési végpontok a fiók alatt hozható létre (lásd: [kvóták és korlátozások](limits-quotas-constraints.md)). 

> [!NOTE]
> Videók streamelése el kell indítania a **folyamatos átviteli végponton** , amelyre vonatkozóan szeretné a videó. 
>  
> Ha a futó állapotban van a folyamatos átviteli végponton csak számolunk fel.

## <a name="naming-convention"></a>Az elnevezési konvenciót

Az alapértelmezett végpont: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Minden további végpontok: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Típusok  

Kétféle **streamvégponttípus** létezik: **Standard szintű** (előzetes verzió) és **prémium**. A skálázási egységek száma határozza meg a típus (`scaleUnits`) lefoglalni a streamvégpontra. 

A táblázat a típusokat írja le:  

|Típus|Skálázási egységek|Leírás|
|--------|--------|--------|  
|**Standard**|0|Az alapértelmezett streamvégpont egy **Standard** írja be, a prémium szintű típus beállításával módosítható `scaleUnits`.|
|**Prémium**|>0|**Prémium szintű** adatfolyam-továbbítási végpontok speciális munkaterhelésekhez megfelelőek, és dedikált, skálázható sávszélesség-kapacitást biztosítanak. Helyez át egy **prémium** típusának a módosításával `scaleUnits` (streamelési egységek). `scaleUnits` Adja meg, amely vásárolható meg, 200 MB/s-os léptékben dedikált kilépési kapacitáson. A **Prémium** típus használatakor mindegyik engedélyezett egység további sávszélesség-kapacitást nyújt az alkalmazásnak. |

> [!NOTE]
> Internetes tartalomterjesztés tartalomkézbesítés vásárlóknak azt javasoljuk, hogy engedélyezze a folyamatos átviteli végponton CDN.

SLA-információkért lásd: [díjszabás és SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Streamelési típusok összehasonlítása

Funkció|Standard|Prémium
---|---|---
Ingyenes az első 15 nap során <sup>1</sup>| Igen |Nem
Teljesítmény |Legfeljebb 600 MB/s, és a CDN használata esetén megadhat egy magasabb hatékony átviteli sebességre.|200 MB / s folyamatos átviteli egységek (SU). Egy újabb hatékony átviteli sebességre is adja meg, a CDN használata esetén.
Tartalomkézbesítési hálózat (CDN)|Az Azure CDN, harmadik féltől származó CDN, vagy nincs CDN.|Az Azure CDN, harmadik féltől származó CDN, vagy nincs CDN.
Az elszámolás| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Automatikus felskálázással a célként megadott átviteli sebességet.|További SUS-t
IP-szűrés/G20/egyéni állomás <sup>2</sup>|Igen|Igen
Progresszív letöltés|Igen|Igen
Javasolt felhasználás |Javasolt a folyamatos átviteli forgatókönyvek túlnyomó többsége.|Professzionális használata.

<sup>1</sup> az ingyenes próbaverzió csak érvényes újonnan létrehozott media services-fiókok és az alapértelmezett Streamelési végpontot.<br/>
<sup>2</sup> csak közvetlenül a folyamatos átviteli végponton használni, ha a CDN nincs engedélyezve a végponton.<br/>

## <a name="properties"></a>Tulajdonságok 

Ez a szakasz tájékoztatást nyújt a egyes a folyamatos átviteli végponton tulajdonságait. Hogyan hozhat létre egy új streamvégpont és leírások az összes tulajdonság példákért lásd [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` – Használja a következő biztonsági beállításokat adhatja meg ezen a streamvégponton: Akamai aláírás fejléc hitelesítési kulcsokat és IP-címek, amelyek jogosultak a végponthoz csatlakozik.<br />Ez a tulajdonság csak akkor állítható mikor `cdnEnabled` hamis értékre van állítva.
- `cdnEnabled` -Azt jelzi-e az Azure CDN-integrációnak, a folyamatos átviteli végponton engedélyezett (alapesetben le van tiltva). Ha `cdnEnabled` TRUE értéket kap, a következő konfigurációk letiltásának: `customHostNames` és `accessControl`.
  
    Nem minden adatközpontban az Azure CDN-integrációt támogatja. Ellenőrizze-e az adatközpontot tartalmaz-e elérhető az Azure CDN-integrációnak, tegye a következőket:
 
  - Próbáljon meg a `cdnEnabled` igaz értékre.
  - Ellenőrizze a visszaadott eredmény számára egy `HTTP Error Code 412` (PreconditionFailed) "Streaming endpoint CdnEnabled tulajdonság nem állítható igaz, a CDN-funkciók nem érhető el a jelenlegi régióban." üzenettel 

    Ha ez a hiba, az Adatközpont nem támogatja. Próbálkozzon egy másik adatközpontba.
- `cdnProfile` – Amikor `cdnEnabled` értéke igaz, akkor is átadhat `cdnProfile` értékeket. `cdnProfile` van a CDN-profil nevét, ahol a CDN-végponti pont létrejön. Adjon meg egy meglévő cdnProfile, vagy egy új használja. Ha az érték NULL, és `cdnEnabled` , true, az alapértelmezett érték "AzureMediaStreamingPlatformCdnProfile" szolgál. Ha a megadott `cdnProfile` már létezik, a végpont annak alapján jön létre. Ha a profil nem létezik, új profil automatikusan jön létre.
- `cdnProvider` -Ha a CDN engedélyezve van, akkor is átadhat `cdnProvider` értékeket. `cdnProvider` azt szabályozza, melyik-szolgáltatót fogja használni. Jelenleg három értékek támogatottak: "StandardVerizon", "PremiumVerizon" and "StandardAkamai". Ha a nem érték van megadva, és `cdnEnabled` értéke true, "StandardVerizon" használatos (Ez az alapértelmezett érték).
- `crossSiteAccessPolicies` -Itt adhatja meg a webhelyek közötti hozzáférési házirendek a különböző ügyfelek részére. További információkért lásd: [tartományok közötti házirend fájl meghatározásának](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) és [így a szolgáltatás elérhető Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>A beállítások csak a Smooth Streaming vonatkoznak.
- `customHostNames` – Egy folyamatos átviteli végponton, amely egyéni gazdagépnévvel irányított forgalom fogadására konfigurálásához használt.  Ez a tulajdonság érvényes, a Standard és prémium szintű Streamelési végpontok és a segítségével állítható be, amikor `cdnEnabled`: False (hamis).
    
    A tartománynév tulajdonjogát a Media Services jóvá kell hagynia. A Media Services ellenőrzi a tartománynak a nevét tulajdonjogát azzal, hogy egy `CName` rekordot, amely a Media Services-fiók azonosítója a tartományban használt hozzáadandó összetevőjeként. Tegyük fel, a "sports.contoso.com", amely egyéni gazdagépnévvel a folyamatos átviteli végponton, egy rekord használható `<accountId>.contoso.com` konfigurálni kell, hogy a Media Services-ellenőrzési állomásnevek egyikére mutatnak. Az ellenőrzési állomásnév verifydns tevődik össze. \<mediaservices – dns-zóna >. 

    Az alábbiakban a várt DNS-zónák az ellenőrzés rekordban használható különböző Azure-régiókban.
  
  - Észak-Amerika, Európa, Szingapúr, Hongkong (KKT), japán:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Kína:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Ha például egy `CName` rekordot, amely leképezi a "945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com", "verifydns.media.azure.net" igazolja, hogy a Media Services Azonosítót 945a4c4e-28ea-45cd-8ccb-a519f6b700ad így rendelkezik-e a contoso.com tartomány tulajdonjogát annak lehetővé tétele bármely név alatt, amely egyéni gazdagépnévvel egy streamvégponton, az adott fiók használható a contoso.com. A Media szolgáltatás azonosítóérték megkereséséhez nyissa meg a [az Azure portal](https://portal.azure.com/) , és válassza ki a Media Services-fiókját. A **Fiókazonosító** tetején jelenik meg, az oldal jobb.
        
    Ha egy egyéni állomásnevet anélkül, hogy a megfelelő ellenőrzése beállítására irányuló kísérlet a `CName` rekord, a DNS-válasz sikertelen lesz, majd a gyorsítótárba egy ideig. Után egy megfelelő bejegyzés van beállítva, ez eltarthat egy darabig, mindaddig, amíg a gyorsítótárazott válasz újra van-e érvényesítve. Az egyéni tartomány DNS-szolgáltatónál, attól függően is igénybe vehet bárhol pár percet vagy egy órát, kísérelje meg újra a rekord érvényesítését.
        
    Mellett a `CName` , amely leképezi `<accountId>.<parent domain>` való `verifydns.<mediaservices-dns-zone>`, létre kell hoznia egy másik `CName` , amely hozzárendeli az egyéni állomásnevet (például `sports.contoso.com`), a Media Services Streaming Endpoint a gazdagép nevét (például `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Streamvégpontok találhatók ugyanabban az adatközpontban, nem oszthat meg ugyanazt az egyéni állomásnevet.

    Jelenleg az a Media Services nem támogatja az SSL egyéni tartománnyal rendelkező. 
    
- `maxCacheAge` -Felülbírálások az alapértelmezett max-age HTTP-gyorsítótár szabályozhatja a tartalomstreameléshez használt streamvégpont media töredék és igény szerinti jegyzékek által beállított fejléc. Az érték másodperc van beállítva.
- `resourceState` -

    - Leállított - létrehozása után egy folyamatos átviteli végponton kezdeti állapota
    - Kezdő - van való futás közbeni állapota
    - Futó - viszont segítségével tartalmat kíván közvetíteni ügyfelek
    - Skálázás – a skálázási egység éppen növelése vagy csökkenése
    - A leállított állapotot leállításával - van való
    - Törlés - Törlés alatt van
    
- `scaleUnits` -Adja meg, amely vásárolható meg, 200 MB/s-os léptékben dedikált kilépési kapacitáson. Ha szeretne áthelyezni egy **prémium szintű** írja be, módosítsa a `scaleUnits`.

## <a name="working-with-cdn"></a>A CDN használata

A legtöbb esetben engedélyezve kell lennie a CDN-nek. Ha azonban egyidőben 500 nézőnél kevesebb várható, akkor ajánlott letiltani a CDN-t, mert a CDN a legjobban az egyidejűséghez skálázható.

### <a name="considerations"></a>Megfontolandó szempontok

* A folyamatos átviteli végponton `hostname` és a streamelési URL-cím ugyanaz marad, engedélyezi a CDN-e.
* Ha a tartalom, vagy a CDN nélkül tesztelhetik, létrehozhat egy másik folyamatos átviteli végponton, amely nem a CDN engedélyezve van.

### <a name="detailed-explanation-of-how-caching-works"></a>Részletes ismertetése a gyorsítótárazás működése

Nem érkezik a nem meghatározott sávszélesség értékét, ha a változó hozzáadása a CDN-t, mert a sávszélességet, a CDN engedélyezve van a tartalomstreameléshez használt streamvégpont. Rengeteg múlik azon, hogy a tartalmat, mennyire népszerű van, bitsebességre való átkódolása és a protokollok típusát. A CDN-t csak van gyorsítótárazás, mi kéri. Ez azt jelenti, hogy közvetlenül a CDN-t – a népszerű tartalmat szolgáltató mindaddig, amíg a rendszer gyorsítótárazza a videó töredék. Élő tartalmak valószínű, hogy a gyorsítótárazható, mert a figyelt pontosan ugyanaz sokan általában rendelkezik. Az igény szerinti tartalomterjesztésről egy kicsit bonyolultabb lehet, mivel bizonyos tartalmakat, amelyek a népszerű és néhány, amely nem lehet. Ha több millió eszköz videó, ahol egyikük sem népszerű (csak 1 vagy 2 megtekintők egy hét), de több ezer különböző videók megtekintése személyek, a CDN sokkal kevésbé hatékony lesz. Ez a gyorsítótár-tévesztések száma, a tartalomstreameléshez használt streamvégpont terhelését növeli.
 
Is kell figyelembe venni, hogyan adaptív adatfolyam-továbbítási működik. Minden egyes videó töredék saját entitás, mert van gyorsítótárazva. Például ha egy bizonyos videót a címzett figyeli, először a személy kihagyja körül nézi csupán néhány másodpercet Itt csak a videó szilánkok társított személy nézte lekérése a CDN gyorsítótárazza. Az adaptív streameléshez 5 – 7 különböző bitsebességre való átkódolása videó általában rendelkezik. Ha egy személy egy sávszélességű állapotfigyelőszolgáltatás, és a egy másik személy egy másik sávszélességű állapotfigyelőszolgáltatás, majd azokat minden egyes külön gyorsítótárazásának a CDN-ben. Akkor is, ha két ember által figyelt az azonos átviteli sebesség, sikerült kell streamelési különböző protokollokon keresztül. Minden egyes protokoll (HLS, MPEG-DASH, Smooth Streaming) külön-külön gyorsítótárazza. Így minden protokoll és átviteli külön gyorsítótárazásának, és csak ezen kért videó töredék lettek gyorsítótárazva.

### <a name="enable-azure-cdn-integration"></a>Az Azure CDN-integráció engedélyezése

Miután egy folyamatos átviteli végponton ki van építve a CDN engedélyezve van egy meghatározott várakozási idő a Media Services megkezdése előtt el DNS frissítése történik a folyamatos átviteli végponton leképezése a CDN-végponthoz.

Ha később szeretné a CDN engedélyezése vagy tiltása, a streamvégpontnak kell lennie a **leállt** állapota. Azt az Azure CDN engedélyezve az integráció és a módosítások között minden a CDN jelenléti helyein lesz aktív akár két óráig is eltarthat. Azonban Ön telepítheti a streamelési végpontot és a stream nélkül megszakítások indítsa el a streamvégpontot, és az integráció befejezése után a CDN-ből továbbítsa a streamet. Az üzembe helyezési ideje alatt a tartalomstreameléshez használt streamvégpont szerepelni fog a **indítása** állapotát, és előfordulhat, hogy figyelje a teljesítmény csökkenését.

Ha a standard szintű streamvégpont jön létre, a Standard – Verizon alapértelmezés szerint van konfigurálva. Premium Verizon vagy Standard – Akamai szolgáltatók REST API-k használatával konfigurálhatja. 

Az összes az Azure adatközpontjaiban kivéve Kínát és a Szövetségi kormányzati régió engedélyezve van a CDN-integrációt.

> [!IMPORTANT]
> Az Azure Media Services integrációja az Azure CDN van alkalmazva **verizon Azure CDN** a standard streamvégpontok. Prémium szintű streamelési végpontok használatával az összes konfigurálható **tarifacsomagok és szolgáltatók az Azure CDN**. Az Azure CDN-funkciókkal kapcsolatos további információkért lásd: a [CDN áttekintése](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Határozza meg, ha a DNS-módosítás történt.

Megadhatja, hogy ha a DNS-módosítás történt-e egy folyamatos átviteli végponton, (a folyamatban átirányítja a forgalmat az Azure CDN) használatával https://www.digwebinterface.com. Ha az eredmények azureedge.net tartománynevek az eredmények között, a forgalom van most folyamatban mutatott a CDN-t.

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="next-steps"></a>További lépések

A minta [a tárházban](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) bemutatja, hogyan indítsa el az alapértelmezett streamvégpontot a .NET használatával.

