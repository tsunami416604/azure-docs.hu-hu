---
title: Az Azure Media Services streamvégpontok |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési végpontokkal is, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 9d3aad92f5b0130f83e3023a2fdca5710d544311
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256051"
---
# <a name="streaming-endpoints"></a>Streamvégpontok

A Microsoft Azure Media Services (AMS), a [adatfolyam-továbbítási végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) entitás streamelési szolgáltatás, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy további, a Content Delivery Network (CDN) a jelöli terjesztési. A folyamatos átviteli végponton szolgáltatásból származó kimenő adatfolyamot élő stream, vagy igény szerinti Videoobjektum a Media Services-fiók lehet. Egy Media Services-fiók létrehozásakor egy **alapértelmezett** folyamatos átviteli végponton, létrehozott egy leállított állapotban. Az alapértelmezett Streaming Endpoint nem törölhető. További Streamelési végpontokkal is létrehozhatók a fiókban. Videók streamelése indításához indítsa el a folyamatos átviteli végponton kell. 

## <a name="streamingendpoint-types"></a>Streamvégpontok típusok  

Kettő **Streamvégpontok** típusok: **Standard** és **prémium**. A skálázási egységek száma határozza meg a típus (`scaleUnits`) lefoglalni a streamvégpontra. 

A táblázat ismerteti:  

|Típus|Skálázási egységek|Leírás|
|--------|--------|--------|  
|**Standard szintű Streamvégpont** (ajánlott)|0|A **Standard** írja be a lehetőség ajánlott gyakorlatilag az összes streamelési forgatókönyvekhez és bármilyen méretű közönségre. A **Standard** típus automatikusan méretezi a kimenő sávszélesség. <br/>Rendkívül nagy követelmények rendelkező ügyfelek számára a Media Services által **prémium** streamvégpontot választanak, amely használható a horizontális felskálázási kapacitása a legnagyobb internet célközönségek számára. Ha várhatóan széles és egyidejű megtekintők, írjon nekünk az amsstreaming@microsoft.com útmutatást, hogy át kell helyeznie a **prémium** típusa. |
|**Prémium szintű Streamvégpont**|>0|A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. Helyez át egy **prémium** típusának a módosításával `scaleUnits`. `scaleUnits` Adja meg, amely vásárolható meg, 200 MB/s-os léptékben dedikált kilépési kapacitáson. Használatakor a **prémium** típusa, a minden engedélyezett egység további sávszélesség-kapacitást az alkalmazás biztosít. |

## <a name="working-with-cdn"></a>A CDN használata

A legtöbb esetben rendelkeznie kell a CDN engedélyezve van. Azonban ha meg vannak várhatóan kisebb, mint 500 megtekintők maximális párhuzamossági majd ajánlott CDN letiltása, mivel az egyidejűségi CDN méretezhető ajánlott.

### <a name="detailed-explanation-of-how-caching-works"></a>Részletes ismertetése a gyorsítótárazás működése

Nem érkezik a nem meghatározott sávszélesség értékét, ha a változó hozzáadása a CDN-t, mert a sávszélességet, a CDN engedélyezve van a tartalomstreameléshez használt streamvégpont. Rengeteg múlik azon, hogy a tartalmat, mennyire népszerű van, bitsebességre való átkódolása és a protokollok típusát. A CDN-t csak van gyorsítótárazás, mi kéri. Ez azt jelenti, hogy közvetlenül a CDN-t – a népszerű tartalmat szolgáltató mindaddig, amíg a rendszer gyorsítótárazza a videó töredék. Élő tartalmak valószínű, hogy a gyorsítótárazható, mert a figyelt pontosan ugyanaz sokan általában rendelkezik. Az igény szerinti tartalomterjesztésről egy kicsit bonyolultabb lehet, mivel bizonyos tartalmakat, amelyek a népszerű és néhány, amely nem lehet. Ha több millió eszköz videó, ahol egyikük sem népszerű (csak 1 vagy 2 megtekintők egy hét), de több ezer különböző videók megtekintése személyek, a CDN sokkal kevésbé hatékony lesz. Ez a gyorsítótár-tévesztések száma, a tartalomstreameléshez használt streamvégpont terhelését növeli.
 
Is kell figyelembe venni, hogyan adaptív adatfolyam-továbbítási működik. Minden egyes videó töredék saját entitás, mert van gyorsítótárazva. Például ha egy bizonyos videót a címzett figyeli, először a személy kihagyja körül nézi csupán néhány másodpercet Itt csak a videó szilánkok társított személy nézte lekérése a CDN gyorsítótárazza. Az adaptív streameléshez 5 – 7 különböző bitsebességre való átkódolása videó általában rendelkezik. Ha egy személy egy sávszélességű állapotfigyelőszolgáltatás, és a egy másik személy egy másik sávszélességű állapotfigyelőszolgáltatás, majd azokat minden egyes külön gyorsítótárazásának a CDN-ben. Akkor is, ha két ember által figyelt az azonos átviteli sebesség, sikerült kell streamelési különböző protokollokon keresztül. Minden egyes protokoll (HLS, MPEG-DASH, Smooth Streaming) külön-külön gyorsítótárazza. Így minden protokoll és átviteli külön gyorsítótárazásának, és csak ezen kért videó töredék lettek gyorsítótárazva.
 
## <a name="streamingendpoint-properties"></a>Streamvégpontok tulajdonságai 

Ez a szakasz tájékoztatást nyújt a egyes a Streamvégpontok tulajdonságait. Hogyan hozhat létre egy új streamvégpont és leírások az összes tulajdonság példákért lásd [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

|Tulajdonság|Leírás|  
|--------------|----------|
|`accessControl`|A következő biztonsági beállításokat adhatja meg ezen a streamvégponton használt: Akamai aláírás fejléc hitelesítési kulcsokat és IP-címek, amelyek jogosultak a végponthoz csatlakozik.<br />Ez a tulajdonság beállítható mikor `cdnEnabled`": hamis értékre van állítva.|  
|`cdnEnabled`|Azt jelzi-e az Azure CDN-integrációnak, a folyamatos átviteli végponton engedélyezett (alapesetben le van tiltva.)<br /><br /> Ha `cdnEnabled` TRUE értéket kap, a következő konfigurációk letiltásának: `customHostNames` és `accessControl`.<br /><br />Nem minden adatközpontban az Azure CDN-integrációt támogatja. Ellenőrizze-e az adatközpontban rendelkezik-e az Azure CDN elérhető integrációs tegye a következőket:<br /><br /> – Próbálja beállítani a `cdnEnabled` igaz értékre.<br /><br /> – Ellenőrizze a visszaadott eredmény számára egy `HTTP Error Code 412` (PreconditionFailed) "Streaming endpoint CdnEnabled tulajdonság nem állítható igaz, a CDN-funkciók nem érhető el a jelenlegi régióban." üzenettel<br /><br /> Ha ez a hiba, az Adatközpont nem támogatja. Próbálkozzon egy másik adatközpontba.|  
|`cdnProfile`|Amikor `cdnEnabled` értéke igaz, akkor is átadhat `cdnProfile` értékeket. `cdnProfile` van a CDN-profil nevét, ahol a CDN-végponti pont létrejön. Adjon meg egy meglévő cdnProfile, vagy egy új használja. Ha az érték NULL, és `cdnEnabled` , true, az alapértelmezett érték "AzureMediaStreamingPlatformCdnProfile" szolgál. Ha a megadott `cdnProfile` már létezik, a végpont annak alapján jön létre. Ha a profil nem létezik, új profil automatikusan jön létre.|
|`cdnProvider`|Ha a CDN engedélyezve van, akkor is átadhat `cdnProvider` értékeket. `cdnProvider` azt szabályozza, melyik-szolgáltatót fogja használni. Jelenleg három értékek támogatottak: "StandardVerizon", "PremiumVerizon" és "StandardAkamai". Ha a nem érték van megadva, és `cdnEnabled` értéke true, "StandardVerizon" használatos (Ez az alapértelmezett érték.)|
|`crossSiteAccessPolicies`|Itt adhatja meg a webhelyek közötti hozzáférési házirendek a különböző ügyfelek részére. További információkért lásd: [tartományok közötti házirend fájl meghatározásának](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) és [így a szolgáltatás elérhető Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).|  
|`customHostNames`|Segítségével a konfigurálása egy streamvégponton, amely egyéni gazdagépnévvel irányított forgalom fogadására. Ez lehetővé teszi, hogy könnyebben forgalom felügyeleti konfiguráció egy globális Traffic Manager (GTM) keresztül és a vállalati arculattal tartománynevek, a streamvégpont neve használható.<br /><br /> A tartománynév tulajdonjogát az Azure Media Services jóvá kell hagynia. Az Azure Media Services ellenőrzi a tartománynak a nevét tulajdonjogát azzal, hogy egy `CName` rekordra, amely tartalmazza az Azure Media Services-fiók azonosítója a tartományban használt hozzáadandó összetevőjeként. Tegyük fel, a "sports.contoso.com", amely egyéni gazdagépnévvel a folyamatos átviteli végponton, egy rekord használható `<accountId>.contoso.com` konfigurálni kell, hogy a Media Services-ellenőrzési állomásnevek egyikére mutatnak. Az ellenőrzési állomásnév verifydns tevődik össze. \<mediaservices – dns-zóna >. Az alábbi táblázat tartalmazza a várt DNS-zónák az ellenőrzés rekord a különböző Azure-régiókban használandó.<br /><br /> Észak-Amerika, Európa, Szingapúr, Hongkong (KKT), illetve japán:<br /><br /> -mediaservices.windows.net<br /><br /> -verifydns.mediaservices.windows.net<br /><br /> Kína:<br /><br /> -mediaservices.chinacloudapi.cn<br /><br /> -verifydns.mediaservices.chinacloudapi.cn<br /><br /> Ha például egy `CName` rekordot, amely leképezi a "945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com", "verifydns.mediaservices.windows.net" igazolja, hogy az Azure Media Services Azonosítót 945a4c4e-28ea-45cd-8ccb-a519f6b700ad rendelkezik-e a tulajdonjogát, a ezáltal lehetővé téve bármilyen nevét, amely egyéni gazdagépnévvel egy streamvégponton, az adott fiók használható a contoso.com a contoso.com tartományhoz.<br /><br /> A Media szolgáltatás azonosítóérték megkereséséhez nyissa meg a [az Azure portal](https://portal.azure.com/) , és válassza ki a Media Services-fiókját. Az IRÁNYÍTÓPULT lap közepén jobb oldalt megjelenik a MEDIA szolgáltatás azonosítója.<br /><br /> **Figyelmeztetés**: Ha egy egyéni állomásnevet anélkül, hogy a megfelelő ellenőrzése beállítására irányuló kísérlet a `CName` rekord, a DNS-válasz sikertelen lesz, majd a gyorsítótárba egy ideig. Után egy megfelelő bejegyzés van beállítva, ez eltarthat egy darabig, mindaddig, amíg a gyorsítótárazott válasz újra van-e érvényesítve. Az egyéni tartomány DNS-szolgáltatónál, attól függően is igénybe vehet bárhol pár percet vagy egy órát, kísérelje meg újra a rekord érvényesítését.<br /><br /> Mellett a `CName` , amely leképezi `<accountId>.<parent domain>` való `verifydns.<mediaservices-dns-zone>`, létre kell hoznia egy másik `CName` , amely hozzárendeli az egyéni állomásnevet (például `sports.contoso.com`), a Media Services StreamingEndpont állomás nevét (például `amstest.streaming.mediaservices.windows.net`).<br /><br /> **Megjegyzés:**: ugyanabban az adatközpontban található Streamvégpontok nem lehet megosztani az egyéni állomásnévvel.<br /> Ez a tulajdonság érvényes, a Standard és prémium szintű streamelési végpontok és állítható mikor "cdnEnabled": hamis<br/><br/> Jelenleg az AMS nem támogatja az SSL egyéni tartománnyal rendelkező.  |  
|`maxCacheAge`|Felülbírálja az alapértelmezett max-age HTTP gyorsítótár control fejléc a tartalomstreameléshez használt streamvégpont media töredék és igény szerinti jegyzékek állította. Az érték másodperc van beállítva.|
|`resourceState`|A tulajdonság értékei a következők:<br /><br /> -Leállt. A streamvégpont létrehozása után kezdeti állapotát.<br /><br /> -Től. A streamvégpont való van a futó állapotot.<br /><br /> -Fut. A streamvégpont is képes ügyfelek tartalomstreamelést.<br /><br /> -Méretezést. A skálázási egység éppen növelhető vagy csökkenthető.<br /><br /> -Leállítása folyamatban van. A streamvégpont való van a leállított állapotot.<br/><br/> -Törlése folyamatban van. A streamvégpont törlése folyamatban van.|
|`scaleUnits `|scaleUnits vásárolható meg, 200 MB/s-os léptékben kimenő dedikált kapacitást biztosít. Ha szeretne áthelyezni egy **prémium szintű** írja be, módosítsa a `scaleUnits`. |

## <a name="next-steps"></a>További lépések

A minta [a tárházban](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) bemutatja, hogyan indítsa el az alapértelmezett streamvégpontot a .NET használatával.

