---
title: Az Azure Media Services Streaming Endpoint áttekintése |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Services streamvégpontok.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c5979fa7ff67c5acda9ab653bc4ee52d8b5129a5
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293804"
---
# <a name="streaming-endpoints-overview"></a>Adatfolyam-Szolgáltatásvégpontok áttekintése  

## <a name="overview"></a>Áttekintés

A Microsoft Azure Media Services (AMS), egy **folyamatos átviteli végponton** streamelési szolgáltatás, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy az egy Content Delivery Network (CDN) további terjesztés céljából. A Media Services emellett zökkenőmentes Azure CDN-integrációt. A kimenő adatfolyam a Streamvégpontok szolgáltatás az élő stream, igény szerint, vagy az eszköz a Media Services-fiók a progresszív letöltés videó is lehet. Minden egyes Azure Media Services-fiók egy alapértelmezett Streamvégpontok tartalmazza. További Streamvégpontok a fiók alatt hozható létre. Nincsenek két Streamvégpontok, 1.0-s és 2.0-s verziója. 2017 január 10 kezdve minden újonnan létrehozott AMS-fiókok tartalmazza 2.0-s verziójának **alapértelmezett** Streamvégpontok. Adja hozzá az ehhez a fiókhoz további streamvégpontok is 2.0-s verzióját. Ez a változás nem érinti a meglévő fiókokat; meglévő Streamvégpontok 1.0-s verziója és 2.0-s verziójának frissíthetők. Ezzel lesz viselkedés, a számlázásra és a funkció a módosítások (további információkért lásd: a **típusai és verziói Streamelési** szakasz leírása az alábbiakban található).

Az Azure Media Services a következő tulajdonságokat a folyamatos átviteli végponton entitás hozzáadva: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Ezeket a tulajdonságokat részletes ismertetőt talál [ez](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Amikor hoz létre az Azure Media Services-fiók egy standard szintű streamvégpont jön létre, az alapértelmezett a **leállítva** állapota. Az alapértelmezett streamvégpontot nem törölhető. Rendelkezésre állás a célzott régió az Azure CDN, az újonnan létrehozott alapértelmezett alapértelmezés szerint streamvégpontot is tartalmaz "StandardVerizon" CDN szolgáltató integrációja. 
                
> [!NOTE]
> Az Azure CDN-integrációnak letiltható a streamvégpont elindítása előtt. A `hostname` és a streamelési URL-cím ugyanaz marad, engedélyezi a CDN-e.

Ez a témakör a fő funkciókat streamvégpontok által nyújtott áttekintést.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az alapértelmezett végpont: `{AccountName}.streaming.mediaservices.windows.net`

Minden további végpontok: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Streamelési típusai és verziói

### <a name="standardpremium-types-version-20"></a>Standard/prémium típusok (2.0-s verzió)

A Media Services 2017 január kiadásával kezdődően, két folyamatos átviteli típussal rendelkezik: **Standard szintű** és **prémium**. Ezek a típusok "2.0-s" Streaming endpoint verziójának részét képezik.

Typo|Leírás
---|---
**Standard** |Ez az alapértelmezett beállítást, akkor működik a legtöbb forgatókönyv esetében.<br/>Ezzel a beállítással meghatározott/korlátozott SLA-t kap, első 15 nap elindítása után a tartalomstreameléshez használt streamvégpont ingyenes.<br/>Ha egynél több streamvégpontok, csak az elsőt díjmentes az első 15 nap során, a többi számítjuk fel, amint elindítani ezeket. <br/>Vegye figyelembe, hogy ingyenes próbaverziója csak érvényes újonnan létrehozott media services-fiókok és az alapértelmezett streamvégpontot. Meglévő streamvégpontok, és emellett létrehozott streamelési végpontok nem tartalmazza az ingyenes próbaidőszak még frissíti őket 2.0-s verzió vagy 2.0-s verzióját, a létrehozásuk.
**Prémium** |Ez a beállítás akkor megfelelő szakmai újabb skálázási vagy vezérlőelem igénylő forgatókönyvek esetén.<br/>Változó SLA-t, amely prémium szintű streamelési egységek (SU) kapacitást alapul, a dedikált streamvégpontok élő izolált környezetben található, és ne kelljen az erőforrásokhoz.

Részletesebb információkért lásd: a **típusok összehasonlítása Streamelési** következő szakaszban.

### <a name="classic-type-version-10"></a>Hagyományos típus (1.0-s verzió)

A felhasználók számára, aki létrehozta az AMS-fiókok a 2017 január 10-kiadás előtt, hogy egy **klasszikus** egy streamvégponton típusát. Ez a típus a streaming endpoint verziója "1.0" részét.

Ha a **verzió "1.0"** streamvégpont rendelkezik > = 1 prémium szintű streamelési egységek (SU), a prémium szintű streamvégpont pedig minden AMS-funkciókat biztosít (hasonlóan a **Standard/prémium** típusa) anélkül, hogy bármilyen további konfigurációs lépéseket.

>[!NOTE]
>**Klasszikus** streamvégpontok ("1.0" és a 0 SU), csak korlátozott funkciókat biztosít, és nem tartozik szolgáltatói szerződés. Javasoljuk, hogy át **Standard** jobb felhasználói élményt és szolgáltatások, mint például a dinamikus csomagolás és titkosítás és más szolgáltatások használatához írja be a **Standard** típusa. Áttelepítése a **Standard** típusa, nyissa meg a [az Azure portal](https://portal.azure.com/) , és válassza ki **Opt a Standard**. Az áttelepítéssel kapcsolatos további információkért lásd: a [áttelepítési](#migration-between-types) szakaszban.
>
>Ügyeljen arra, hogy ez a művelet nem állítható vissza, és költségvonzattal jár.
>
 
## <a name="comparing-streaming-types"></a>Streamelési típusok összehasonlítása

### <a name="versions"></a>Verziók

|Typo|StreamingEndpointVersion|ScaleUnits|Tartalomkézbesítési hálózat (CDN)|Számlázás|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Klasszikus|1.0|0|NA|Ingyenes|NA|
|Standard szintű streamvégpont|2.0|0|Igen|Díjköteles|Igen|
|Prémium szintű streamelési egységek|1.0|>0|Igen|Díjköteles|Igen|
|Prémium szintű streamelési egységek|2.0|>0|Igen|Díjköteles|Igen|

### <a name="features"></a>Szolgáltatások

Szolgáltatás|Standard|Prémium
---|---|---
Ingyenes az első 15 nap során| Igen |Nem
Teljesítmény |Legfeljebb 600 MB/s, amikor az Azure CDN nincs használatban. CDN bevonásával.|200 MB / s folyamatos átviteli egységek (SU). CDN bevonásával.
SLA | 99.9|99,9 (200 Mbps per SU).
Tartalomkézbesítési hálózat (CDN)|Az Azure CDN, harmadik féltől származó CDN, vagy nincs CDN.|Az Azure CDN, harmadik féltől származó CDN, vagy nincs CDN.
Az elszámolás| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Automatikus felskálázással a célként megadott átviteli sebességet.|A további streamelési egységek
IP-szűrés/G20/egyéni állomás|Igen|Igen
Progresszív letöltés|Igen|Igen
Javasolt felhasználás |Javasolt a folyamatos átviteli forgatókönyvek túlnyomó többsége.|Professzionális használata.<br/>Ha úgy gondolja, előfordulhat, hogy Standard túli igényeinek. Lépjen kapcsolatba velünk (amsstreaming@microsoft.com) Ha egy egyidejű célközönség mérete nagyobb, mint 50 000 megtekintők.


## <a name="migration-between-types"></a>Áttelepítési típusok között

Ettől: | Művelet | Műveletek
---|---|---
Klasszikus|Standard|Jóváhagyás szükséges
Klasszikus|Prémium| Méretezési csoport (további streamelési egységek)
Standard/Premium|Klasszikus|Nem érhető el (ha streaming endpoint verziója 1.0-t. Módosítsa a "0" scaleunits beállítása klasszikus engedélyezett)
Standard (a/a CDN nélkül)|Ugyanezzel a konfigurációval a prémium szintű|Engedélyezett a **lépések** állapota. (az Azure Portalon)
Prémium szintű (a/a CDN nélkül)|Ugyanezzel a konfigurációval rendelkező standard|Engedélyezett a **lépések** state (Azure-portálon)
Standard (a/a CDN nélkül)|Prémium szintű, a másik config|Engedélyezett a **leállt** state (Azure-portálon). Nem engedélyezett a futó állapotot.
Prémium szintű (a/a CDN nélkül)|Standard szintű, a másik config|Engedélyezett a **leállt** state (Azure-portálon). Nem engedélyezett a futó állapotot.
Az SU 1.0-s verzió > = 1, a CDN|Standard/prémium nincs CDN|Engedélyezett a **leállt** állapota. Nem engedélyezett a **lépések** állapota.
Az SU 1.0-s verzió > = 1, a CDN|Standard/a CDN nem|Engedélyezett a **leállt** állapota. Nem engedélyezett a **lépések** állapota. CDN 1.0-s verziója lesz törölve, és új egy létrehozott és az első lépéseket.
Az SU 1.0-s verzió > = 1, a CDN|Prémium szintű/a CDN nem|Engedélyezett a **leállt** állapota. Nem engedélyezett a **lépések** állapota. Klasszikus CDN lesz törölve, és új egy létrehozott és az első lépéseket.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

