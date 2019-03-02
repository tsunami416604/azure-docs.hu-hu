---
title: Az Azure Media Services alapfogalmaiért – Azure |} A Microsoft Docs
description: Ez a témakör rövid áttekintést nyújt az Azure Media Services – alapelvek, és hivatkozásokat tartalmaz részleteket.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f9d431fe0ee76edf5d41c1ce7831f335128402a8
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244740"
---
# <a name="media-services-concepts"></a>A Media Services – alapelvek

Ez a témakör rövid áttekintést nyújt az Azure Media Services – alapelvek és cikkekre mutató hivatkozások magyarázatra van szüksége a Media Services v3 kapcsolatos fogalmakat és funkciókat biztosít. Az alábbi témakörök ismertetik az alapvető fogalmakkal fejlesztés megkezdése előtt át kell tekinteni.

## <a name="cloud-upload-and-storage"></a>Felhőbe történő feltöltés és tárolás

A Media Services-fiók létrehozása és a digitális fájlok feltöltése kell kezelése, titkosítása, kódolás, elemzése és az Azure-ban médiafolyam indításához **eszközök**.

- [Felhőbeli feltöltés és tárolás](storage-account-concept.md)
- [Eszközök fogalma](assets-concept.md)

## <a name="encoding"></a>Encoding

Miután az eszközök a kiváló minőségű digitális médiafájlok feltöltése, kódolnia azokat, hogy a böngészők és eszközök széles lejátszhatók. 

Kódolás a Media Services v3-as, létre kell **alakítja át az** és **feladatok**.

![Átalakítások](./media/encoding/transforms-jobs.png)

- [Átalakítások és feladatok](transforms-jobs-concept.md)
- [Kódolás a Media Services használatával](encoding-concept.md)

## <a name="media-analytics"></a>Media analytics (Médiaelemzés)

A video- és fájlok elemzéséhez, is szeretne létrehozni **alakítja át az** és **feladatok**.

- [Video- és hangfájlok elemzése](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Csomagolás, teljesítés, védelem

Miután a tartalom van kódolva, igénybe veheti a **dinamikus csomagolási**. **Streamvégpont** a médiatartalmak eredményjelző ügyfél lejátszók Media Services dinamikus csomagolási szolgáltatás. A videók a kimeneti adategység elérhetővé tenni az ügyfelek számára a lejátszás, létre kell hoznia egy **Streamelési lokátor** majd kialakítható a streamelési URL-címek. 

Létrehozásakor a **Streamelési lokátor**, mellett az eszköz nevét, meg kell adnia **Streamelési házirend**. **Adatfolyam-házirendek** adatfolyam-továbbítási protokollok meghatározásához és a titkosítási beállítások (ha vannak) – a **Streamelési Lokátorok**.

A dinamikus csomagolás, a tartalom, élő vagy igény szerinti adatfolyam-e használható. Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat igényalapú streameléshez.

![A dinamikus csomagolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

A Media Services biztosítható az élő és igény szerinti tartalom, dinamikusan titkosítja az Advanced Encryption Standard (AES-128) vagy /, és a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.

Ha a stream a titkosítási beállítások megadása, hozzon létre a **tartalom kulcs házirend** társítsa a **Streamelési lokátor**. A **tartalom kulcs házirend** konfigurálhatja a tartalomkulcsot a rendszer hogyan továbbítja az ügyfelek közötti.

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.svg)

&#42;* a dinamikus titkosítás AES-128 "tiszta kulcsot" CBCS és CENC támogatja. 

Használhatja a Media Services **dinamikus jegyzékfájlok** csak egy adott megjelenítés vagy subclips a videó továbbításához. A következő példában egy kódoló egy mezzanine eszköz kódolandó hét ISO MP4-fájlnak videó beállításkészletben (a 180p a 1080p) lett megadva. A kódolt objektumhoz dinamikusan csomagolható be a következő adatfolyam-továbbítási protokollok bármelyikét: HLS, MPEG DASH és Smooth.  A diagram tetején jelenik meg a HLS-jegyzékfájl a szűrők az eszköz (tartalmaz minden hét beállításkészletben).  A bal alsó "ott" nevű szűrőt alkalmaztak, amelyhez a HLS-jegyzékfájl jelenik meg. A "ott" szűrő meghatározza, hogy távolítsa el az összes bitsebességre való átkódolása alább 1 MB/s, amely eredményezett az alsó két minőségi szint alatt levágja, a válaszban. Az alul a jobb oldalon a HLS-jegyzékfájlt, amelyhez "mobileszköz" nevű szűrőt alkalmaztak jelenik meg. A "mobileszköz" szűrő Megadja, hogy távolítsa el a beállításkészletben, ahol a megoldás nem haladja meg a két eredményezett 720p 1080p beállításkészletben levágja, folyamatban van.

![Megjelenítés szűrése](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [dinamikus becsomagolás](dynamic-packaging-overview.md)
- [Streamvégpontok](streaming-endpoint-concept.md)
- [A streamelési Lokátorok](streaming-locators-concept.md)
- [Streamelési házirendek](streaming-policy-concept.md)
- [Tartalmi kulcs házirendjei](content-key-policy-concept.md)
- [A Content protection](content-protection-overview.md)
- [A dinamikus jegyzékek](filters-dynamic-manifest-overview.md)
- [Szűrők](filters-concept.md)

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam)

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. **Élő események** feldolgozására és feldolgozása az élő videóközvetítési felelősek. Amikor létrehoz egy **élő esemény**, egy bemeneti végpont jön létre, használhatja élő jelet küld a távoli kódoló. Miután a tárfiókba kerülnek stream a **élő esemény**, megkezdheti a streamelési eseményt hoz létre egy **eszköz**, **élő kimeneti**, és **Streamelési lokátor** . **Kimeneti élő** archiválja a streamet, a **eszköz** és keresztül megtekintők számára elérhető legyen a **Streamelési Endpoid**. A **élő esemény** két típus egyike lehet: **átmenő** és **valós idejű kódolás**.

Az alábbi ábrán az átmenő írja be a munkafolyamat:

![az átmenő](./media/live-streaming/pass-through.svg)

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő események és élő kimenetek](live-events-outputs-concept.md)

## <a name="monitoring"></a>Figyelés

### <a name="event-grid"></a>Event Grid

A feladat előrehaladásának megtekintéséhez használjon **Event Grid**. A Media Services is az élő esemény típusok bocsát ki. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. 

- [Event Grid-események kezelése](reacting-to-media-services-events.md)
- [Sémák](media-services-event-schemas.md)

## <a name="player-clients"></a>Lejátszóügyfelek

Az Azure Media Player segítségével streameli a Media Services számos különböző böngészők és eszközök a médiatartalmak lejátszására. Az Azure Media Player iparági szabványok – például HTML5, Media forrás Extensions (MSE) és az Encrypted Media Extensions (EME) adjon meg egy streamelési megoldást használja. 

- [Az Azure Media Player áttekintése](use-azure-media-player.md)

## <a name="next-steps"></a>További lépések

[Feltöltése, kódolása és streamelése a Media Services használatával](stream-files-tutorial-with-api.md)
