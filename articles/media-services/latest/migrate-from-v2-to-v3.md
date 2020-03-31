---
title: Áttelepítés az Azure Media Services v2-ről a v3-ra | Microsoft dokumentumok
description: Ez a cikk az Azure Media Services v3-ban bevezetett módosításait ismerteti, és két verzió közötti különbségeket mutatja be. A cikk áttelepítési útmutatást is tartalmaz a Media Services 2-es v3-as ról a 3-as programra való áttéréshez.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472067"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Áttelepítési útmutató a Media Services 2-es v3-as ról a 3-as vagy a 3-as

>Kap értesítést, hogy mikor kell újra ezt az oldalt a `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` frissítések másolásával és beillesztése ezt az URL-t: az RSS feed olvasó.

Ez a cikk a Media Services 2-es és 3-as v3-as részéből származó áttelepítési útmutatást tartalmazza.

Ha a mai napon kifejlesztett videoszolgáltatással rendelkezik a [media services v2 API-kon](../previous/media-services-overview.md)felül, tekintse át az alábbi irányelveket és szempontokat, mielőtt áttérne a v3 API-kra. A v3 API számos előnnyel és új funkcióval rendelkezik, amelyek javítják a Media Services fejlesztői élményét és képességeit. Azonban, ahogy azt a cikk [ismert problémák](#known-issues) című részében, vannak is bizonyos korlátozások miatt az API-verziók közötti változások. Ez a lap megmarad, mivel a Media Services csapata folyamatosan fejleszti a v3 API-kat, és kezeli a verziók közötti hézagokat. 

## <a name="prerequisites"></a>Előfeltételek

* Médiaszolgáltatások [2. és 3.-es v.](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>A Media Services 3-as számú alkalmazásának előnyei
  
### <a name="api-is-more-approachable"></a>Az API jobban megközelíthető

*  A v3 egy egységes API felületen alapul, amely az Azure Resource Manager szolgáltatásra épülő felügyeleti és műveleti funkciókat is biztosít. Az Azure Resource Manager-sablonok segítségével hozhat létre és helyezhet üzembe átalakításokat, végpontok streamelését, élő eseményeket és egyebeket.
* [OpenAPI Specification (korábbi nevén Swagger)](https://aka.ms/ams-v3-rest-sdk) dokumentum.
    Az összes szolgáltatásösszetevő sémáját elérhetővé teszi, beleértve a fájlalapú kódolást is.
* A [.NET,](https://aka.ms/ams-v3-dotnet-ref).NET Core, [Node.js,](/javascript/api/overview/azure/mediaservices/management) [Python,](https://aka.ms/ams-v3-python-ref) [Java,](https://aka.ms/ams-v3-java-ref) [Go](https://aka.ms/ams-v3-go-ref)és Ruby.
* [Azure CLI-integráció](https://aka.ms/ams-v3-cli-ref) az egyszerű parancsfájlok támogatásához.

### <a name="new-features"></a>Új funkciók

* A fájlalapú feladat feldolgozásához http(S) URL-címet használhat bemenetként.<br/>Nem kell, hogy a tartalom már tárolt Az Azure-ban, és nem kell létrehozni eszközök.
* Bemutatja az [átalakítások](transforms-jobs-concept.md) fogalmát a fájlalapú feladatfeldolgozáshoz. Az átalakítás segítségével újrafelhasználható konfigurációkat hozhat létre, Azure Resource Manager-sablonokat hozhat létre, és elkülönítheti a feldolgozási beállításokat több ügyfél vagy bérlő között.
* Egy eszköz rendelkezhet több [streamelési lokátorral,](streaming-locators-concept.md) amelyek mindegyike különböző [dinamikus csomagolási](dynamic-packaging-overview.md) és dinamikus titkosítási beállításokkal rendelkezik.
* [A tartalomvédelem](content-key-policy-concept.md) támogatja a többkulcsos funkciókat.
* Akár 24 órás élő eseményeket is streamelhet, ha a Media Services használatával egyetlen bitráta-hozzájárulási hírcsatornát több bitrátával rendelkező kimeneti adatfolyamba átkódol.
* Új alacsony késleltetésű élő közvetítés támogatása az élő eseményeken. További információ: [Késés](live-event-latency.md).
* A Live Event Preview támogatja a [dinamikus csomagolást](dynamic-packaging-overview.md) és a dinamikus titkosítást. Ez lehetővé teszi a tartalomvédelmet az előzetes verzióban, valamint a DASH és a HLS csomagoláson.
* Az élő kimenet használata egyszerűbb, mint a program entitás a v2 API-kban. 
* Továbbfejlesztett RTMP támogatás (nagyobb stabilitás és több forráskódoló támogatás).
* RTMPS biztonságos betöltés.<br/>Élő esemény létrehozásakor 4 betöltési URL-t kap. A 4 betöltési URL-címek szinte azonosak, ugyanazt a streamelési jogkivonatot (AppId), csak a port szám része más. Két URL elsődleges és az RTMPS biztonsági mentése.   
* Szerepköralapú hozzáférés-vezérlés (RBAC) az entitások felett. 

## <a name="known-issues"></a>Ismert problémák

*  Jelenleg használhatja az [Azure Portalon:](https://portal.azure.com/)

    * Media Services v3 [élő események](live-events-outputs-concept.md)kezelése, 
    * nézet (nem kezeli) v3 [Eszközök,](assets-concept.md) 
    * [az API-k elérésével kapcsolatos információk.](access-api-portal.md) 

    Minden más felügyeleti feladathoz (például [átalakítások és feladatok](transforms-jobs-concept.md) és [tartalomvédelem)](content-protection-overview.md)használja a [REST API-t,](https://docs.microsoft.com/rest/api/media/)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)
* A media szolgáltatás számára fenntartott egységeket (MRUs) kell kiépítenie a fiókjában, hogy szabályozhassa a feladatok egyidejűségét és teljesítményét, különösen a video- vagy hangelemzéssel kapcsolatosakat. További információért lásd a [médiafeldolgozás skálázását](../previous/media-services-scale-media-processing-overview.md) ismertető cikket. Az MRUs-okat a [CLI 2.0-s v3-as](media-reserved-units-cli-how-to.md)használatával kezelheti az [Azure Portalon](../previous/media-services-portal-scale-media-processing.md), vagy a [v2 API-k](../previous/media-services-dotnet-encoding-units.md)használatával. Ki kell építenie az MRUs-t, függetlenül attól, hogy a Media Services v2-es vagy 3-as API-kat használja.
* A v3 API-val létrehozott Media Services-entitásokat nem tudja a v2 API-val kezelni.  
* A V2 API-ban nem minden entitás jelenik meg automatikusan a V3 API-ban.  Az alábbiakban példákat mutatunk be a két verzióban lévő, nem kompatibilis entitásokra:  
    * A v2-ben létrehozott feladatok és feladatok nem jelennek meg a v3-ban, mivel nincsenek átalakításhoz társítva. A javaslat az, hogy váltson a v3 átalakítások és feladatok. Lesz egy viszonylag rövid ideig igénylő figyelemmel kíséri a fedélzeti v2 jobs az átállás során.
    * A v2-vel létrehozott csatornák és programok (amelyek a v3-ban élő eseményekre és élő kimenetekre vannak leképezve) nem kezelhetők a v3-mal. A javaslat az, hogy váltson a v3 Élő események és élő kimenetek egy kényelmes csatorna stop.<br/>Jelenleg nem lehet áttelepíteni a folyamatosan futó csatornákat.  

> [!NOTE]
> Ez a lap megmarad, mivel a Media Services csapata folyamatosan fejleszti a v3 API-kat, és kezeli a verziók közötti hézagokat.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Távoli fájl kódolása URL alapján és a videó streamelése - .NET](stream-files-dotnet-quickstart.md)
