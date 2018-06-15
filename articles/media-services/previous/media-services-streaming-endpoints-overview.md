---
title: Az Azure Media Services Streamvégponton áttekintése |} Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Services adatfolyam-végpontok.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 7b4077747d4c9ec581496786063fc8fbe0c6b247
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790432"
---
# <a name="streaming-endpoints-overview"></a>Adatfolyam-továbbítási végpontok áttekintése 

## <a name="overview"></a>Áttekintés

A Microsoft Azure Media Services (AMS), egy **Streamvégponton** egy adatfolyam-szolgáltatás által biztosított tartalom közvetlenül player ügyfélalkalmazást, vagy az a Content Delivery Network (CDN) későbbi terjesztés jelöli. A Media Services is biztosít az Azure CDN való zökkenőmentes integrációt. A kimenő adatfolyam egy StreamingEndpoint szolgáltatásból lehet élő stream, igény szerint, vagy az eszköz a Media Services-fiók a progresszív letöltés videó. Minden Azure Media Services-fiók egy alapértelmezett StreamingEndpoint tartalmazza. A fiók alatt további Streamvégpontok hozhatók létre. Streamvégpontok, 1.0-s és 2.0 két verziója van. 10 2017. január verziótól kezdődően az újonnan létrehozott AMS számlákat tartalmazza 2.0-s verziójának **alapértelmezett** StreamingEndpoint. Ehhez a fiókhoz hozzáadni kívánt további streamvégpontok is 2.0-s verziójában. Ez a változás nem befolyásolja a meglévő fiókokat; meglévő Streamvégpontok 1.0-s verziója és a 2.0-s verziójának frissítése. A módosítás nem lesznek viselkedés, a számlázásra és a szolgáltatás módosításokat (további információkért lásd: a **típusai és verziói** szakasz leírása az alábbiakban).

Ezenkívül az Azure Media Services (2017. január jelent) 2.15 verziójával kezdődően a Streamvégponton entitás a következő tulajdonságok hozzáadott: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Részletes megtudhatja, ezeket a tulajdonságokat, [ez](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Amikor hoz létre egy Azure Media Services-fiók egy szabványos streamvégpont létrejön az alapértelmezett a **leállítva** állapotát. Az alapértelmezett streamvégpontból nem törölhető. Attól függően, hogy az Azure CDN rendelkezésre állás érdekében a célként megadott terület, az újonnan létrehozott alapértelmezett alapértelmezés szerint adatfolyam-továbbítási végpontra is "StandardVerizon" CDN szolgáltató integráció. 

>[!NOTE]
>Azure CDN-integráció le kell tiltani a streamvégpontján megkezdése előtt.

Ez a témakör áttekintést nyújt a főbb funkciók adatfolyam-végpontok által biztosított.

## <a name="streaming-types-and-versions"></a>Adatfolyam-típusai és verziói

### <a name="standardpremium-types-version-20"></a>Standard vagy prémium típusok (verzió: 2.0-s)

A Media Services a 2017. január kiadástól kezdve, még a két adatfolyam típusa: **szabványos** és **prémium**. Ezek a típusok a Streaming endpoint 2.0-s verziójában "" részét képezik.

Típus|Leírás
---|---
**Standard**|Ez a forgatókönyv a legtöbb volna használható alapértelmezett beállítás.<br/>Ezzel a lehetőséggel rögzített/korlátozott SLA-t kap, első 15 nap elindítása után a streamvégpontján szabad.<br/>Ha egynél több adatfolyam-végpontok, csak az első címtárra szabad az első 15 nap, a többi számlázzuk, amint elindítani ezeket. <br/>Vegye figyelembe, hogy ingyenes próbaverzió csak az újonnan létrehozott media services-fiókok és az alapértelmezett streamvégpontból alkalmazza. Meglévő streamvégpontok és továbbá létrehozott streamvégpontok nem tartalmazza az ingyenes próbaidőszak is frissíti őket 2.0-s verziójában vagy 2.0-s verzióját, hozza létre őket.
**Prémium**|Ez a beállítás akkor megfelelő szakmai nagyobb skálázási vagy vezérlő igénylő forgatókönyvek esetén.<br/>Változó SLA-t, amely prémium vásárolt streamelési egységet (SU) kapacitás alapul, a dedikált streamvégpontok élő elkülönített környezetben, és nem "versenyeznek" az erőforrásokat.

Részletes információkat, tekintse meg a **összehasonlítása adatfolyam-típusok** a következő szakaszban.

### <a name="classic-type-version-10"></a>Klasszikus típusú (1.0-s verzió)

AMS fiókok előtti 10 2017. január kiadásaiban létrehozó felhasználók, meg kell adni egy **klasszikus** egy streamvégpont típusú. Ez az adatfolyam-továbbítási endpoint verziója "1.0" részét képezi.

Ha a **verzió "1.0"** adatfolyam-továbbítási végpontra rendelkezik > = 1 premium adatfolyam-egységek (SU), prémium streamvégpont lesz, és minden AMS-szolgáltatásokat biztosít (ugyanúgy, mint a **Standard vagy prémium** típusa) nélkül további konfigurációs lépéseket.

>[!NOTE]
>**Klasszikus** végpontok streaming ("1.0" és a 0 SU), korlátozott szolgáltatásokat biztosít, és nem tartalmaz egy SLA-t. Javasoljuk, hogy át **szabványos** típus jobb élményt és szolgáltatások, mint a dinamikus csomagolás és a titkosítás és egyéb szolgáltatások használata a **szabványos** típusa. Át a **szabványos** típusa, keresse fel a [Azure-portálon](https://portal.azure.com/) válassza **Opt-in a Standard**. Az áttelepítéssel kapcsolatos további információkért lásd: a [áttelepítési](#migration-between-types) szakasz.
>
>Ügyeljen arra, hogy ez a művelet nem állítható vissza, és egy árképzési hatással van.
>
 
## <a name="comparing-streaming-types"></a>Adatfolyam-továbbítási típusok összehasonlítása

### <a name="versions"></a>Verziók

|Típus|StreamingEndpointVersion|ScaleUnits|Tartalomkézbesítési hálózat (CDN)|Számlázás|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Klasszikus|1.0|0|NA|Ingyenes|NA|
|Standard szintű streamvégpont|2.0|0|Igen|Díjköteles|Igen|
|Prémium szintű streamelési egységek|1.0|>0|Igen|Díjköteles|Igen|
|Prémium szintű streamelési egységek|2.0|>0|Igen|Díjköteles|Igen|

### <a name="features"></a>Szolgáltatások

Szolgáltatás|Standard|Prémium
---|---|---
Szabad első 15 nap| Igen |Nem
Teljesítmény |Legfeljebb 600 MB/s Azure CDN nem használata esetén. CDN bevonásával méretezhető.|200 MB / adatfolyam-egységek (SU). CDN bevonásával méretezhető.
SLA | 99.9|99,9 (200 MB/s / SU).
Tartalomkézbesítési hálózat (CDN)|Az Azure CDN, harmadik féltől származó CDN vagy nem CDN.|Az Azure CDN, harmadik féltől származó CDN vagy nem CDN.
Számlázási arányosítva van| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Automatikus méretezik akár a célként megadott átviteli sebességet.|További streamelési egység
IP-szűrés/G20/egyéni állomás|Igen|Igen
Progresszív letöltés|Igen|Igen
Ajánlott kihasználtsága |Az adatfolyam-forgatókönyvek többsége ajánlott.|Professional használat.<br/>Ha úgy gondolja, hogy előfordulhat, hogy túl Standard igényeinek. Kapcsolatfelvétel (amsstreaming@microsoft.com) Ha a párhuzamos célközönség mérete nagyobb, mint 50 000 megjelenítők várja.


## <a name="migration-between-types"></a>Áttelepítési típus között

Forrás | Művelet | Műveletek
---|---|---
Klasszikus|Standard|Részt kell
Klasszikus|Prémium| A skála (További adatfolyam-egységek)
Standard vagy Premium|Klasszikus|Nem érhető el (Ha a folyamatos átviteli végpont verziószáma 1.0. "0" scaleunits beállítása a klasszikus átállítása engedélyezett)
Standard (a/CDN nélkül)|Prémium szintű ugyanazokat a konfigurációkat a|Engedélyezett a **lépések** állapotát. (az Azure portálon)
Prémium (a/CDN nélkül)|Ugyanazokat a konfigurációkat standard|Engedélyezett a **lépések** állapot (Azure-portálon)
Standard (a/CDN nélkül)|A különböző config Premium|Engedélyezett a **leállt** (Azure-portálon) állapot. Nem engedélyezett a futó állapotot.
Prémium (a/CDN nélkül)|A különböző config standard|Engedélyezett a **leállt** (Azure-portálon) állapot. Nem engedélyezett a futó állapotot.
SU az 1.0-s verzió > = 1 CDN|Standard vagy prémium nem CDN|Engedélyezett a **leállt** állapotát. Nem engedélyezett a **lépések** állapotát.
SU az 1.0-s verzió > = 1 CDN|A/nélkül CDN standard|Engedélyezett a **leállt** állapotát. Nem engedélyezett a **lépések** állapotát. CDN 1.0-s verziója lesz törölt és új egyik létrehozott, és elindult.
SU az 1.0-s verzió > = 1 CDN|A/nélkül CDN Premium|Engedélyezett a **leállt** állapotát. Nem engedélyezett a **lépések** állapotát. Klasszikus CDN lesz törölve, és új egyik létrehozott, és elindult.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

