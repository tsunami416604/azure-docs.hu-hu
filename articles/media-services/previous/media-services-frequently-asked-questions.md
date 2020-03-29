---
title: Az Azure Media Services gyakori kérdéseket tesz fel
description: Ez a cikk választ ad az Azure Media Services szolgáltatással kapcsolatos gyakori kérdésekre.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: bdb5a43ad6669bfcd6e93ef4e3bf1646314e4606
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705870"
---
# <a name="media-services-v2-frequently-asked-questions"></a>A Media Services v2–2-es kérdés

Ez a cikk az Azure Media Services (AMS) felhasználói közössége által feltett gyakori kérdéseket orvosolja.

## <a name="general-ams-faqs"></a>Általános AMS gyakori kérdések

K: Hogyan streamelhető apple iOS-eszközökre?

V: add hozzá a "(format=m3u8-aapl)" elérési utat az URL "/Manifest" részéhez, hogy a streaming forráskiszolgáló visszaadja a HLS-tartalmat az Apple iOS natív eszközeinek felhasználásához (a részleteket lásd a [tartalom kézbesítése),](media-services-deliver-content-overview.md)

K: Hogyan méretezhető az indexelés?

A: A fenntartott egységek megegyeznek a kódolási és indexelési feladatokhoz. A [kódolásra fenntartott egységek méretezése](media-services-scale-media-processing-overview.md)szolgáltatásra vonatkozó utasításokat kövesse. **Vegye figyelembe,** hogy az Indexelő teljesítményét nem befolyásolja a fenntartott egység típusa.

K: Feltöltöttem, kódoltam és közzétettem egy videót. Mi lenne az oka a videó nem játszik le, amikor megpróbálom stream el?

A: Az egyik leggyakoribb oka az, hogy nem rendelkezik a streamelési végpont, amelyből próbál lejátszani a **futó** állapotban.  

K: Csinálhatok kompozitálást élő közvetítésen?

A: Az élő közvetítések összeállítása jelenleg nem érhető el az Azure Media Servicesszolgáltatásban, ezért előzetesen kell komponálnia a számítógépen.

K: Használhatom az Azure CDN-t az élő közvetítéssel?

A: A Media Services támogatja az Azure CDN-nel való integrációt (további információt a [Streamelési végpontok kezelése egy Media Services-fiókban című témakörben talál).](media-services-portal-manage-streaming-endpoints.md)  Az élő közvetítés t cdn-nel is használhatja. Az Azure Media Services zökkenőmentes streamelést, HLS- és MPEG-DASH kimeneteket biztosít. Mindezek a formátumok http-t használnak az adatok átviteléhez, és a HTTP-gyorsítótárazás előnyeit élvezik. Az élő streamelés tényleges video / audio adatok vannak osztva töredékek és ez az egyes töredékek kap cache-el CDN. Csak az adatokat kell frissíteni a jegyzékadatokat. A CDN rendszeres időközönként frissíti a jegyzékadatokat.

K: Támogatja az Azure Media-szolgáltatások a képek tárolását?

A: Ha csak jpeg- vagy PNG-lemezképeket szeretne tárolni, akkor azokat az Azure Blob Storage-ban kell tárolnia. Nincs előnye annak, ha a Media Services-fiókba helyezi őket, kivéve, ha azt szeretné, hogy a video- vagy audioeszközökhöz társítva legyenek. Vagy ha szükség van a képek etye-letként való használatára a videokódolóban. A Media Encoder Standard támogatja a képek videók tetejére való átfedését, és ez az, amit a JPEG és a PNG támogatott bemeneti formátumként sorol fel. További információt az [Átfedések létrehozása című témakörben talál.](media-services-advanced-encoding-with-mes.md#overlay)

K: Hogyan másolhatok eszközöket az egyik Media Services-fiókból a másikba?

A: Az eszközök egyik Media Services-fiókból a másikba történő másolásához használja az [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) bővítmény módszert, amely elérhető az [Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) tárházban. További információkért lásd [ezt a](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórumot téma.

K: Melyek a fájlok elnevezésének támogatott karakterei az AMS-szal végzett munka során?

V: A Media Services a IAssetFile.Name tulajdonság értékét használja a streamelési tartalom URL-címeinek létrehozásakor (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Ezért a százalékos kódolás nem engedélyezett. A **Name** tulajdonság értéke nem tartalmazhatja a következő [százalékkódolást fenntartott karaktereket](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$/?%#[]". Is, ott csak egy "." a fájlnév kiterjesztéséhez.

K: Hogyan lehet csatlakozni a REST használatával?

A: Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című](media-services-use-aad-auth-to-access-ams-api.md)témakörben talál további információt. 

K: Hogyan forgathatok el egy videót a kódolási folyamat során?

A: A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) 90/180/270 szögben támogatja a forgatást. Az alapértelmezett viselkedés az "Automatikus", ahol megpróbálja észlelni a bejövő MP4/MOV fájl ban lévő elforgatási metaadatokat, és kompenzálni azt. Az [itt](media-services-mes-presets-overview.md)meghatározott json-készletek egyikének a következő **Források** elemet kell tartalmaznia:

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
