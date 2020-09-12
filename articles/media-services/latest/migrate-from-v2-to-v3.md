---
title: Áttelepítés Azure Media Services v2-ről v3-re | Microsoft Docs
description: Ez a cikk a Azure Media Services v3 verzióban bevezetett változásokat ismerteti, és két verzió közötti különbségeket mutatja. A cikk áttelepítési útmutatót is biztosít a Media Services v2-ről a v3-re való áthelyezéshez.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b4e79a2aab5ca72ff8263bfc5734757bbff41005
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297739"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

Ez a cikk a Media Services v2 és v3 közötti áttelepítési útmutatót ismerteti.

Ha az [örökölt Media Services v2 API](../previous/media-services-overview.md)-kra épülő videó-szolgáltatást már ma fejlesztette ki, tekintse át a következő irányelveket és szempontokat a V3 API-kra való Migrálás előtt. A V3 API számos előnnyel és új funkcióval rendelkezik, amelyek javítják a Media Services fejlesztői élményét és képességeit. Azonban a jelen cikk [ismert problémák](#known-issues) című szakaszában leírtak szerint az API-verziók változásai miatt bizonyos korlátozások is fennállnak. Ez az oldal továbbra is érvényben marad, mivel a Media Services csapat tovább fejleszti a V3 API-kat, és megjavítja a verziók közötti hézagokat. 

## <a name="prerequisites"></a>Előfeltételek

* Felülvizsgálat [Media Services v2 és v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>A Media Services v3 előnyei
  
### <a name="api-is-more-approachable"></a>Az API jobban megközelíthető

*  A v3 egy egységes API felületen alapul, amely az Azure Resource Manager szolgáltatásra épülő felügyeleti és műveleti funkciókat is biztosít. Azure Resource Manager sablonok használatával átalakításokat, folyamatos átviteli végpontokat, élő eseményeket és egyéb műveleteket hozhat létre és helyezhet üzembe.
* [OpenAPI-specifikáció (korábbi nevén henceg)](https://aka.ms/ams-v3-rest-sdk) dokumentum.
    Minden szolgáltatás-összetevő számára elérhetővé teszi a sémát, beleértve a fájl alapú kódolást is.
* A [.net](https://aka.ms/ams-v3-dotnet-ref), a .net Core, a [Node.js](/javascript/api/overview/azure/mediaservices/management), a [Python](https://aka.ms/ams-v3-python-ref), a [Java](https://aka.ms/ams-v3-java-ref), a [Go](https://aka.ms/ams-v3-go-ref)és a Ruby esetében elérhető SDK-k.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) -integráció az egyszerű parancsfájlok támogatásához.

### <a name="new-features"></a>Új funkciók

* A fájl alapú feladatok feldolgozásához a HTTP (S) URL-címet használhatja bemenetként.<br/>Nem kell az Azure-ban tárolt tartalmakat használnia, és nem kell eszközöket létrehoznia.
* Bevezeti a fájl alapú feladatok feldolgozásának [átalakítási](transforms-jobs-concept.md) koncepcióját. Az átalakító használatával újrafelhasználható konfigurációk hozhatók létre, Azure Resource Manager sablonok hozhatók létre, és a feldolgozási beállítások elkülöníthetők több ügyfél vagy bérlő között.
* Egy eszközhöz több [folyamatos átviteli lokátor](streaming-locators-concept.md) is tartozhat, amelyek különböző [dinamikus csomagolási](dynamic-packaging-overview.md) és dinamikus titkosítási beállításokkal rendelkeznek.
* A [Content Protection](content-key-policy-concept.md) támogatja a többkulcsos funkciókat.
* Akár 24 órán át tartó élő eseményeket is továbbíthat, ha Media Services használ egy sávszélesség-összefoglalást egy olyan kimeneti adatfolyamba, amely több bitrátát tartalmaz.
* Új, kis késleltetésű élő adatfolyam-támogatás élő eseményeken. További információ: [késés](live-event-latency.md).
* Az élő esemény előzetes verziója támogatja a [dinamikus csomagolást](dynamic-packaging-overview.md) és a dinamikus titkosítást. Ez az előzetes verzióban, valamint a DASH és a HLS csomagoláson is lehetővé teszi a tartalomvédelem használatát.
* Az élő kimenet használata egyszerűbb, mint a program entitás a v2 API-kon. 
* Továbbfejlesztett RTMP-támogatás (nagyobb stabilitás és több forrás-kódoló támogatása).
* A RTMP biztonságos betöltést biztosít.<br/>Élő esemény létrehozásakor 4 betöltési URL-címet kap. A 4 betöltési URL-cím majdnem azonos, ugyanazzal a folyamatos átviteli jogkivonattal (AppId) rendelkezik, csak a portszám rész különbözik. A két URL-cím elsődleges és biztonsági másolat az RTMP-hez.   
* Szerepköralapú hozzáférés-vezérléssel (RBAC) rendelkezik az entitások felett. 

## <a name="known-issues"></a>Ismert problémák

*  Jelenleg a [Azure Portal](https://portal.azure.com/) a következőket használhatja:

    * Media Services v3 [élő események](live-events-outputs-concept.md)kezelése 
    * v3- [eszközök](assets-concept.md)megtekintése (nem felügyelt) 
    * [az API-k elérésére vonatkozó információk beolvasása](./access-api-howto.md). 

    Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](/rest/api/media/), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.
* A Media szolgáltatás számára fenntartott egységeket (MRUs-ket) a fiókjában kell kiépíteni a feladatok párhuzamosságának és teljesítményének szabályozása érdekében, különösen a videó-vagy hangelemzést is beleértve. További információért lásd a [médiafeldolgozás skálázását](../previous/media-services-scale-media-processing-overview.md) ismertető cikket. A MRUs a parancssori felület 2,0-es verziójával kezelheti [Media Services v3](media-reserved-units-cli-how-to.md)esetén, a [Azure Portal](../previous/media-services-portal-scale-media-processing.md)vagy a [v2 API](../previous/media-services-dotnet-encoding-units.md)-k használatával. MRUs kell kiépíteni, függetlenül attól, hogy Media Services v2 vagy V3 API-t használ.
* Media Services a V3 API-val létrehozott entitásokat nem lehet a v2 API-val felügyelni.  
* A v2 API-ban lévő összes entitás automatikusan megjelenik a V3 API-ban.  A következő példa a két, nem kompatibilis verzióban található entitásokra mutat példát:  
    * A v2-ben létrehozott feladatok és feladatok nem jelennek meg a v3-as verzióban, mivel nincsenek átalakítók társítva. A javaslat a v3 átalakításokra és feladatokra vált. Viszonylag rövid időszakra van szükség, amely a fedélzeti v2-feladatok figyelését teszi szükségessé az átállás során.
    * A v2-vel létrehozott csatornák és programok (amelyek az élő eseményekhez és a v3-as élő kimenetekhez vannak leképezve) nem folytathatják a v3-vel való felügyeletet. Javasoljuk, hogy térjen át a v3 élő eseményekre, és az élő kimeneteket egy kényelmes csatornán állítsa le.<br/>Jelenleg nem telepíthet át folyamatosan futó csatornákat.  

> [!NOTE]
> Ez az oldal továbbra is érvényben marad, mivel a Media Services csapat tovább fejleszti a V3 API-kat, és megjavítja a verziók közötti hézagokat.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a video-.NET](stream-files-dotnet-quickstart.md)
