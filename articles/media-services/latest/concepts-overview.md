---
title: A Media Services terminológiája és fogalmai
titleSuffix: Azure Media Services
description: Ismerje meg az Azure Media Services terminológiáját és fogalmait.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500084"
---
# <a name="media-services-terminology-and-concepts"></a>A Media Services terminológiája és fogalmai

Ez a témakör rövid áttekintést nyújt az Azure Media Services terminológiájáról és fogalmairól. A cikk a Media Services v3-as fogalmainak és funkcióinak részletes magyarázatával rendelkező cikkekre mutató hivatkozásokat is tartalmaz.

Az ezekben a témakörökben leírt alapvető fogalmakat a fejlesztés megkezdése előtt felül kell vizsgálni.

> [!NOTE]
> Jelenleg az Azure [Portal](https://portal.azure.com/) segítségével: kezelheti a Media Services v3 [Élő események](live-events-outputs-concept.md), megtekintése (nem kezeli) v3 [assets](assets-concept.md), és [az API-k elérésével kapcsolatos információk.](access-api-portal.md)
> Minden más felügyeleti feladathoz (például [átalakítások és feladatok](transforms-jobs-concept.md) és [tartalomvédelem)](content-protection-overview.md)használja a [REST API-t,](https://aka.ms/ams-v3-rest-ref)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 terminológia

|Időtartam|Leírás|
|---|---|
|Élő esemény|Az **élő esemény** a video-, hang- és valós idejű metaadatok betöltési, átkódolási (opcionálisan) és csomagolási élő adatfolyamainak folyamatát jelöli.<br/><br/>A Media Services v2 API-król átkelő ügyfelek számára az **élő esemény** felváltja a **csatorna** entitást a 2-es v2-ben. További információ: [Áttelepítés a v2-ről a v3-ra.](migrate-from-v2-to-v3.md)|
|Folyamatos átvitelvégpont/csomagolás/eredet|A **streamelési végpont** egy dinamikus (just-in-time) csomagolási és származási szolgáltatást jelent, amely az élő és igény szerinti tartalmat közvetlenül az ügyféllejátszó-alkalmazásnak juttatja el. Ez használ egy -ból mindennapi folyó média protokoll (HLS vagy KÖTŐJEL). Emellett a **streamelési végpont** dinamikus (just-in-time) titkosítást biztosít az iparágvezető digitális jogkezelő rendszerek (DRM-ek) számára.<br/><br/>A médiaadatfolyam-továbbítási iparágban ezt a szolgáltatást általában **Packager** vagy **Origin**néven emlegetik.  Más gyakori kifejezések az iparágban ez a képesség közé tartozik a JITP (just-in-time-packager) vagy JITE (just-in-time-encryption).

## <a name="media-services-v3-concepts"></a>A Media Services v3-as fogalmai

|Alapelvek|Leírás|Hivatkozások|
|---|---|---|
|Eszközök és tartalom feltöltése|A médiatartalmak Azure-beli kezelésének, titkosításának, elemzésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot, és fel kell töltenie digitális fájljait az **Assets**programba.|[Felhőbe történő feltöltés és tárolás](storage-account-concept.md)<br/><br/>[Eszközök koncepciója](assets-concept.md)|
|Tartalom kódolása|Miután feltöltötte kiváló minőségű digitális médiafájljait az Eszközök be, olyan formátumokba kódolhatja őket, amelyek sokféle böngészőben és eszközön lejátszhatók. <br/><br/>A Media Services v3-as számmal való kódoláshoz létre kell **hoznia az Átalakításokat** és **a Feladatokat.**|[Átalakítások és feladatok](transforms-jobs-concept.md)<br/><br/>[Kódolás a Media Services szolgáltatással](encoding-concept.md)|
|Tartalomelemzés (Video Indexer)|A Media Services v3 funkcióval betekintést nyerhet a video- és hangfájlokból a Media Services v3-as készletei vel. A Media Services v3-as készleteivel végzett tartalom elemzéséhez létre kell **hoznia az Átalakításokat** és **a Feladatok at.**<br/><br/>Ha részletesebb elemzéseket szeretne, használja közvetlenül [a Video Indexer t.](https://docs.microsoft.com/azure/media-services/video-indexer/)|[Video- és hangfájlok elemzése](analyzing-video-audio-files-concept.md)|
|Csomagolás és kézbesítés|A tartalom kódolása után kihasználhatja a **dinamikus csomagolás**előnyeit. A Media Services szolgáltatásban a **streamelési végpont** az a dinamikus csomagolási szolgáltatás, amely a médiatartalom ügyféllejátszóknak történő kézbesítésére szolgál. Ahhoz, hogy a kimeneti eszközben lévő videókat elérhetővé tegye az ügyfelek számára a lejátszáshoz, létre kell hoznia egy **streamelési lokátort,** majd létre kell hoznia a streamelési URL-címeket. <br/><br/>A **streamelési lokátor**létrehozásakor az eszköz neve mellett meg kell adnia a **streamelési szabályzatot.** **A streamelési házirendek** lehetővé teszik a streamelési protokollok és a titkosítási beállítások (ha vannak **ilyenek) meghatározását a streamelési lokátorokhoz.** A dinamikus csomagolást akár élőben, akár igény szerint közvetíti. <br/><br/>A Media Services **dinamikus jegyzékfájljai** segítségével csak a videó egy adott interpretációját vagy részklipjeit streamelheti.|[Dinamikus csomagolás](dynamic-packaging-overview.md)<br/><br/>[Végpontok streamelése](streaming-endpoint-concept.md)<br/><br/>[Streamelési lokátorok](streaming-locators-concept.md)<br/><br/>[Streamelési szabályzatok](streaming-policy-concept.md)<br/><br/>[Dinamikus jegyzékek](filters-dynamic-manifest-overview.md)<br/><br/>[Szűrők](filters-concept.md)|
|Tartalomvédelem|A Media Services segítségével az Advanced Encryption Standard (AES-128) vagy/és a három fő DRM-rendszer bármelyikével dinamikusan titkosított élő és igény szerinti tartalmakat is kézbesíthet: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek hivatalos ügyfelek nek történő kézbesítéséhez. <br/><br/>Ha titkosítási beállításokat ad meg az adatfolyamon, hozza létre a **tartalomkulcs-házirendet,** és társítsa azt a **streamelési lokátorral.** A **tartalomkulcs-házirend** lehetővé teszi a tartalomkulcs végfelhasználói kézbesítésének konfigurálását.<br/><br/> Próbálja meg újra felhasználni a házirendeket, ha ugyanazokra a beállításokra van szükség.| [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)<br/><br/>[Tartalomvédelem](content-protection-overview.md)|
|Live streaming (Élő adatfolyam)|A Media Services lehetővé teszi, hogy élő eseményeket biztosítson ügyfeleinek az Azure-felhőben. Az **élő események** az élő videóadatok betöltését és feldolgozását végzik. **Élő esemény**létrehozásakor létrejön egy bemeneti végpont, amely segítségével élő jelet küldhet egy távoli kódolótól. Miután az adatfolyam beáramlott az **élő eseménybe,** megkezdheti a streamelési eseményt **egy eszköz**, az **élő kimenet**és a **streamelési lokátor**létrehozásával. **Az Élő kimenet** archiválja az adatfolyamot az **eszközbe,** és elérhetővé teszi a megtekintők számára a **streamvégponton**keresztül. Az élő esemény beállítható *áthaladásra* (egy helyszíni élő kódoló több bitráta-adatfolyamot küld) vagy *élő kódolásra* (a helyszíni élő kódoló egyetlen átviteli adatfolyamot küld). |[Élő közvetítés – áttekintés](live-streaming-overview.md)<br/><br/>[Élő események és élő kimenetek](live-events-outputs-concept.md)|
|Figyelés eseményrácsgal|A feladat előrehaladásának megtekintéséhez használja az **Event Grid**et. A Media Services az élő eseménytípusokat is kibocsátja. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. |[Event Grid-események kezelése](reacting-to-media-services-events.md)<br/><br/>[Sémák](media-services-event-schemas.md)|
|Figyelés az Azure Monitorral|Az Azure Monitor segítségével figyelheti a metrikákat és a diagnosztikai naplókat, amelyek segítenek megérteni, hogyan teljesítenek az alkalmazások.|[Metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md)<br/><br/>[Diagnosztikai naplók sémái](media-services-diagnostic-logs-schema.md)|
|Lejátszóügyfelek|Az Azure Media Player segítségével lejátszhatja a Media Services által streamelt médiatartalmakat a legkülönbözőbb böngészőkben és eszközökön. Az Azure Media Player az iparági szabványokat, például a HTML5-öt, a Médiaforrás-bővítményeket (MSE) és a titkosított médiabővítményeket (EME) használja a bővített adaptív streamelési élmény biztosításához. |[Az Azure Media Player áttekintése](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

* [Távoli fájl kódolása és videó streamelése – REST](stream-files-tutorial-with-rest.md)
* [Feltöltött fájl kódolása és videó streamelése – .NET](stream-files-tutorial-with-api.md)
* [Élő stream – .NET](stream-live-tutorial-with-api.md)
* [Videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)
* [Dinamikus titkosítás több DRM-mel - .NET](protect-with-drm.md)
