---
title: Az Azure Media Services segítségével élő Streamelés áttekintése |} A Microsoft Docs
description: Ez a cikk nyújt áttekintést élő adatfolyam továbbítása az Azure Media Services v3 használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a31cd950ae241eb55c840c716f4679c5a67b1379
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350012"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő Stream a az Azure Media Services v3

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. A Media Services élő események streamelése, a következőkre lesz szüksége:  

- Egy kamera, amely az élő esemény rögzítéséhez.<br/>A telepítő ötleteket, tekintse meg [egyszerű és hordozható esemény videó fogaskerék beállítása]( https://link.medium.com/KNTtiN6IeT).
- Egy élő videókódoló, amely a fényképezőgép (vagy egy másik eszközt, például egy laptop) jelek alakítja át, amelyet elküld a Media Services-hírcsatorna hozzájárulás. A közreműködői hírcsatorna hirdetési, például a SCTE – 35 jelölők kapcsolatos jelek is tartalmazhatnak.<br/>Ajánlott élő streamelési kódolók listáját lásd: [valós idejű kódolók](recommended-on-premises-live-encoders.md). Emellett tekintse meg ezt a blogot: [Élő streamelés az éles üzemre OBS](https://link.medium.com/ttuwHpaJeT).
- A Media Services szolgáltatásban, amelyek lehetővé teszik, hogy, összetevők előzetes verzió, csomag, jegyezze fel, titkosítása és az élő esemény szórási, az ügyfelek számára, vagy egy CDN későbbi terjesztés.

Ez a cikk áttekintést és útmutatást az élő adások online közvetítése a Media Services és más ide tartozó cikkekre mutató hivatkozásokat biztosít.

> [!NOTE]
> Az Azure portal jelenleg nem használható v3-erőforrások kezeléséhez. Használja a [REST API-val](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), vagy a támogatott valamelyik [SDK-k](developers-guide.md).

## <a name="dynamic-packaging"></a>A dinamikus csomagolás

A Media Services igénybe veheti a dinamikus Packaging](dynamic-packaging-overview.md), amely lehetővé teszi az előzetes verzió és az élő adatstreameket [MPEG DASH, HLS és Smooth Streaming formátumban](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) származó a hozzájárulást hírcsatorna küldése a szolgáltatáshoz. A nézők vissza az élő stream továbbítása bármely HLS, DASH vagy Smooth Streaming kompatibilis lejátszók játszhatja le. Használhat [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) a web- vagy mobilalkalmazásokat, hogy a stream bármelyik ezeket a protokollokat.

## <a name="dynamic-encryption"></a>A dinamikus titkosítás

A dinamikus titkosítás lehetővé teszi a dinamikus titkosítást az AES-128, vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek élő vagy igény szerinti tartalom: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. További információkért lásd: [a dinamikus titkosítás](content-protection-overview.md).

## <a name="dynamic-manifest"></a>A dinamikus Manifest

Dinamikus szűrés segítségével nyomon követi, formátum, bitsebességre való átkódolása és bemutató idő windows a játékosok megismerése által küldött számát. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Élő események típusai

Egy élő eseményt két típus egyike lehet: csatlakoztatott mind az élő kódolás. A Media Services v3 élő streameléssel kapcsolatos részletekért lásd: [élő események és élő kimenetek](live-events-outputs-concept.md).

### <a name="pass-through"></a>Továbbítás

![az átmenő](./media/live-streaming/pass-through.svg)

Az átmenő használatakor **élő esemény**, amelyeket összeköthet a helyszíni élő kódoló készítése több sávszélességű video-adatfolyamot, és elküldeni, mint a hozzájárulás hírcsatorna az élő esemény (protokollal RTMP vagy töredékes MP4). Az élő esemény majd sorozatéhoz keresztül a bejövő video-adatfolyamok további feldolgozás nélkül. Az ilyen egy csatlakoztatott élő esemény hosszú ideig futó élő események van optimalizálva, vagy 24 x 365 lineáris élő adatfolyam. 

### <a name="live-encoding"></a>Live Encoding  

![Live encoding](./media/live-streaming/live-encoding.svg)

Valós idejű kódolás a Media Services használatakor állíthatók be a helyszíni élő kódoló egy egyféle sávszélességű videó elküldeni a hozzájárulás hírcsatorna az élő esemény (RTMP vagy Fragmented-Mp4 protokoll használatával). Az élő esemény kódol, hogy a bejövő egyféle sávszélességű adatfolyamot a egy [több sávszélességű video-adatfolyamot](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), elérhetővé tesz a kézbesítési lejátszására protokollok, mint például az MPEG-DASH, HLS és Smooth Streaming-eszközöket. 

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Szeretné megtudni, a Media Services v3 élő adatfolyam-továbbítási munkafolyamat, először tekintse át kell, és a következő fogalmak megismerése: 

- [Streamvégpontok](streaming-endpoint-concept.md)
- [Élő események és élő kimenetek](live-events-outputs-concept.md)
- [A streamelési Lokátorok](streaming-locators-concept.md)

Az egy élő adatfolyam-továbbítási munkafolyamat lépései a következők:

1. A Media Services-fiók, ellenőrizze, hogy a **folyamatos átviteli végponton** fut-e. 
2. Hozzon létre egy [élő esemény](live-events-outputs-concept.md). <br/>Az esemény létrehozásakor megadhat autostart azt. Másik lehetőségként megkezdése az eseményt, amikor készen áll a streamelés elindításához.<br/> Autostart Ha értéke igaz, az élő esemény fogja elindítani a megfelelő létrehozása után. A számlázás elindul, amint az élő esemény elindult. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson. További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).
3. A betöltés URL-címe és a konfigurálása a helyszíni kódolót, a hírcsatorna-hozzájárulás küldése az URL-cím használatával.<br/>Lásd: [élő kódolók ajánlott](recommended-on-premises-live-encoders.md).
4. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti ténylegesen fogadja.
5. Hozzon létre egy új **eszköz** objektum.
6. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.<br/>A **élő kimeneti** archiválja a streamet, a **eszköz**.
7. Hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.<br/>Ha azt tervezi, a tartalmak, tekintse át a [Content protection áttekintése](content-protection-overview.md).
8. Az útvonalak listájában a **Streamelési lokátor** visszatéréshez használandó URL-címek (ezek a determinisztikus).
9. A gazdanevének beszerzése a **folyamatos átviteli végponton** érkező adatfolyam szeretné.
10. Az URL-címet a 8. lépés kombinálva a teljes URL-Címének lekéréséhez 9. lépésben az állomásnevet.
11. Ha szeretné állítani, így a **élő esemény** megtekinthető, le kell állítania az esemény-és törlése a **Streamelési lokátor**.

## <a name="other-important-articles"></a>Egyéb fontos cikkek

- [Ajánlott élő kódolók](recommended-on-premises-live-encoders.md)
- [Használatával a felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md)
- [Élő esemény típusú szolgáltatások összehasonlítása](live-event-types-comparison.md)
- [Állapotok és számlázás](live-event-states-billing.md)
- [Késés](live-event-latency.md)

## <a name="next-steps"></a>További lépések

* [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)
* [Migrálási útmutató segítséget nyújt a Media Services v2 áthelyezését v3](migrate-from-v2-to-v3.md)
