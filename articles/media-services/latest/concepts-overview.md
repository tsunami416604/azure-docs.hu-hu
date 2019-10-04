---
title: Azure Media Services terminológia és fogalmak – Azure | Microsoft Docs
description: Ez a témakör rövid áttekintést nyújt Azure Media Services terminológiáról és fogalmakról, és további részletekre mutató hivatkozásokat tartalmaz.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910320"
---
# <a name="media-services-concepts"></a>Media Services fogalmak

Ez a témakör rövid áttekintést nyújt Azure Media Services terminológiáról és fogalmakról. A cikk a cikkekre mutató hivatkozásokat is tartalmaz, amelyek részletesen ismertetik a Media Services v3 fogalmakat és funkciókat. 

A fejlesztés megkezdése előtt tekintse át az alábbi témakörökben ismertetett alapvető fogalmakat.

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="terminology"></a>Terminológia

Ez a szakasz azt mutatja be, hogy egyes általános iparági feltételek hogyan képezik le az Media Services V3 API-t.

### <a name="live-event"></a>Élő esemény

Egy **élő esemény** a betöltéshez, az átkódoláshoz (opcionálisan) és a videó-, hang-és valós idejű metaadatokból álló élő streamek csomagolásához szükséges folyamatokat jelöli.

Media Services v2 API-kból áttelepítést igénylő ügyfelek esetében az **élő esemény** a **Channel** entitást a v2-ben váltja fel. További információ: [Migrálás v2-ről v3](migrate-from-v2-to-v3.md)-ra.

### <a name="streaming-endpoint-packaging-and-origin"></a>Folyamatos átviteli végpont (csomagolás és forrás)

A **folyamatos átviteli végpontok** egy dinamikus (igény szerinti) csomagolási és forrás-szolgáltatást jelentenek, amely az élő és az igény szerinti tartalmat közvetlenül egy ügyfél-lejátszó alkalmazás számára teszi elérhetővé az egyik Common Streaming Media Protocol (HLS vagy Dash) használatával. Emellett az adatfolyam- **végpont** dinamikus (igény szerinti) titkosítást biztosít az iparág vezető DRMs.

A Media Streaming Industry szolgáltatásban ezt a szolgáltatást általában **csomagolónak** vagy **forrásnak**nevezzük.  Az iparág egyéb általános használati feltételei közé tartozik például a JITP (Just-in-time-becsomagolás) vagy a JITE (Just-in-time-Encryption). 
 
## <a name="cloud-upload-and-storage"></a>Felhőbe történő feltöltés és tárolás

Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot, és felkell töltenie a digitális fájlokat az eszközökbe.

- [Felhőbe történő feltöltés és tárolás](storage-account-concept.md)
- [Eszközök koncepciója](assets-concept.md)

## <a name="encoding"></a>Encoding

Miután feltölti a kiváló minőségű digitális médiafájlokat az eszközökbe, kódolhatja azokat formátumokba, amelyek számos böngészőben és eszközön játszhatók le. 

Az Media Services v3 kódoláshoz **átalakításokat** és **feladatokat**kell létrehoznia.

![Átalakítások](./media/encoding/transforms-jobs.png)

- [Átalakítások és feladatok](transforms-jobs-concept.md)
- [Kódolás Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Media Analytics

A videó-és hangfájlok elemzéséhez **átalakításokat** és **feladatokat**is létre kell hoznia.

- [Videó-és hangfájlok elemzése](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Csomagolás, teljesítés, védelem

A tartalom kódolása után igénybe veheti a **dinamikus csomagolás**előnyeit. Media Services a **streaming Endpoint**/Origin az a dinamikus csomagolási szolgáltatás, amely a médiatartalom továbbítására szolgál az ügyfélszámítógépek számára. Ahhoz, hogy a kimeneti eszközön a videók elérhetők legyenek az ügyfelek számára a lejátszáshoz, létre kell hoznia egy **adatfolyam-keresőt** , majd streaming URL-címeket kell létrehoznia. 

A **folyamatos átviteli lokátor**létrehozásakor az eszköz neve mellett meg kell adnia a **folyamatos átviteli házirendet**. A **folyamatos átviteli szabályzatok** lehetővé teszik a folyamatos átviteli protokollok és a titkosítási beállítások (ha vannak) definiálását a **streaming-lokátorok**számára.

A dinamikus csomagolás a tartalom élő vagy igény szerinti továbbítására szolgál. A következő ábra az igény szerinti folyamatos átvitelt mutatja be dinamikus csomagolási munkafolyamattal.

![Dinamikus csomagolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy/és a három jelentős digitális jogkezelési (DRM) rendszerből: Microsoft PlayReady, Google Widevine és Apple FairPlay. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.

Ha az adatfolyamban titkosítási beállításokat ad meg, hozza létre a **tartalmi kulcs házirendjét** , és társítsa azt a **folyamatos átviteli lokátorhoz**. A **tartalmi kulcs házirendje** lehetővé teszi annak konfigurálását, hogy a rendszer hogyan továbbítsa a tartalmi kulcsot a végfelhasználók számára.

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.svg)

&#42;a dinamikus titkosítás támogatja az AES-128 "Clear Key", a CBCS és a CENC használatát. 

Media Services **dinamikus jegyzékfájlok** használatával csak a videó egy adott kiadatását vagy alklipeit továbbíthatja. A következő példában egy kódolót használtak egy köztes eszköz kódolásához hét ISO MP4 (180p és 1080p között). A kódolt eszköz a következő folyamatos átviteli protokollok bármelyikében dinamikusan becsomagolható: HLS, MPEG DASH és Smooth.  A diagram tetején megjelenik az objektum HLS-jegyzéke, amely nem tartalmaz szűrőket (ez mind a hét összes leképezését tartalmazza).  A bal alsó sarokban megjelenik a HLS jegyzékfájl, amelybe az "ott" nevű szűrő lett alkalmazva. Az "ott" szűrő azt adja meg, hogy az 1 Mbps alatti összes bitrátát eltávolítja, ami az alsó két minőségi szintet eredményezte a válaszban. A jobb alsó sarokban megjelenik a HLS jegyzékfájl, amelybe a "Mobile" nevű szűrő lett alkalmazva. A "mobil" szűrő meghatározza, hogy el lehessen távolítani azokat a leképezéseket, amelyekben a felbontás nagyobb, mint 720p, ami azt eredményezte, hogy a két 1080p-kiadatás kikerül.

![Kiadatás szűrése](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [dinamikus becsomagolás](dynamic-packaging-overview.md)
- [Streamvégpontok](streaming-endpoint-concept.md)
- [Streamelési lokátorok](streaming-locators-concept.md)
- [Streamelési szabályzatok](streaming-policy-concept.md)
- [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)
- [Tartalomvédelem](content-protection-overview.md)
- [Dinamikus jegyzékek](filters-dynamic-manifest-overview.md)
- [Szűrők](filters-concept.md)

## <a name="live-streaming"></a>Élő közvetítés

A Azure Media Services lehetővé teszi, hogy élő eseményeket nyújtson az ügyfeleknek az Azure-felhőben. Az **élő események** az élő videóadatok betöltését és feldolgozását végzik. Ha **élő eseményt**hoz létre, a rendszer létrehoz egy bemeneti végpontot, amelynek használatával élő jeleket küldhet egy távoli kódolóból. Ha a stream az **élő eseménybe**áramlik, megkezdheti a folyamatos átviteli eseményt egy **eszköz**, egy **élő kimenet**és a **folyamatos átviteli lokátor**létrehozásával. Az **élő kimenet** archiválja a streamet az objektumba, és elérhetővé teszi a nézők számára a **folyamatos átviteli végponton**keresztül. Az **élő esemény** a következő két típus egyike lehet: **átmenő** és **élő kódolás**.

Az alábbi ábrán az áteresztő típusú munkafolyamat látható:

![átmenő típusú](./media/live-streaming/pass-through.svg)

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő események és élő kimenetek](live-events-outputs-concept.md)

## <a name="monitoring"></a>Figyelés

### <a name="event-grid"></a>Event Grid

A feladatok előrehaladásának megtekintéséhez Event Gridt kell használnia. A Media Services az élő események típusát is kibocsátja. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. 

- [Event Grid események feldolgozása](reacting-to-media-services-events.md)
- [Sémák](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Figyelje a metrikákat és a diagnosztikai naplókat, amelyek segítségével megismerheti, hogy az alkalmazások hogyan működnek együtt a Azure Monitorokkal.

- [Metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md)
- [Diagnosztikai naplók sémái](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Lejátszóügyfelek

A Azure Media Player használatával a különböző böngészők és eszközök Media Services által továbbított médiatartalom lejátszását is elvégezheti. A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához. 

- [Az Azure Media Player áttekintése](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

* [Távoli fájl és stream-videó kódolása – REST](stream-files-tutorial-with-rest.md)
* [A feltöltött fájl és a Stream video-.NET kódolása](stream-files-tutorial-with-api.md)
* [Stream Live – .NET](stream-live-tutorial-with-api.md)
* [A videó elemzése – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 dinamikus titkosítás – .NET](protect-with-aes128.md)
* [Dinamikus titkosítás a multi-DRM-.NET használatával](protect-with-drm.md) 
