---
title: A Azure Media Services v3 kibocsátási megjegyzései | Microsoft Docs
description: Ha naprakészen szeretne maradni a legújabb fejleményekkel, ez a cikk az Azure Media Services v3 legújabb frissítéseit tartalmazza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 06/03/2020
ms.author: juliako
ms.openlocfilehash: 6abb44012035f31f970a347687964afb0c715d29
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983383"
---
# <a name="azure-media-services-v3-release-notes"></a>A Azure Media Services v3 kibocsátási megjegyzései

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő információkat tartalmazza:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="known-issues"></a>Ismert problémák

> [!NOTE]
> A [Azure Portal](https://portal.azure.com/) a v3 [élő események](live-events-outputs-concept.md)kezelésére, a v3- [eszközök](assets-concept.md) és-feladatok megtekintésére, az API-k elérésére és a tartalmak titkosítására vonatkozó információk beszerzésére használható. Az összes többi felügyeleti feladathoz (például az átalakítások és a feladatok kezeléséhez) használja a [REST API](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.
>
> Részletekért lásd: [a Media Services v3 Azure Portal korlátozásai](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).

## <a name="june-2020"></a>2020. június

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Élő videó-elemzések IoT Edge előzetes kiadásban

A IoT Edge élő videós elemzésének előnézete nyilvános volt. További információ: [kibocsátási megjegyzések](../live-video-analytics-edge/release-notes.md).

A IoT Edge Live Video Analytics szolgáltatás a Media Service-család bővítése. Lehetővé teszi az élő videók elemzését a saját peremhálózati eszközein választott AI-modellekkel, és szükség esetén rögzítheti és rögzítheti a videót. Mostantól a valós idejű videó-elemzéseket tartalmazó alkalmazásokat is létrehozhat, és nem kell aggódnia az élő videós folyamat kiépítésének és működésének összetettsége nélkül.

## <a name="may-2020"></a>2020. május

A Azure Media Services mostantól általánosan elérhető a következő régiókban: "Észak-Németország", "Középnyugat-Németország", "Észak-Svájc" és "Nyugat-Svájc". Az ügyfelek a Azure Portal használatával telepíthetik Media Servicesokat ezeken a régiókban.

## <a name="april-2020"></a>2020. április

### <a name="improvements-in-documentation"></a>A dokumentáció tökéletesítései

Azure Media Player dokumentumok át lettek telepítve az [Azure-dokumentációba](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>2020. január

### <a name="improvements-in-media-processors"></a>A Media processors fejlesztése

- Továbbfejlesztett támogatás az összefűzött források számára a videó-elemzésben – az ilyen tartalmak mostantól megfelelően vannak összefűzött, mielőtt a rendszer elküldje őket a következtetések elvégzésére.
- Ha a miniatűröket a "legjobb" móddal hozza létre, a kódoló a 30 másodpercnél hosszabb időt keres a nem monokróm keret kiválasztásához.

### <a name="azure-government-cloud-updates"></a>Azure Government Felhőbeli frissítések

Media Services GA'ed a következő Azure Government régiókban: *USA Korm. Arizona* és *USA Korm. Texas*.

## <a name="december-2019"></a>2019. december

Az élő és a videó igény szerinti folyamatos átviteléhez a CDN támogatását is hozzáadta a *forrás-és a kisegítő* fejlécekhez. olyan ügyfelek számára érhető el, akik közvetlen szerződést kötöttek a Akamai CDN-vel. Forrás – Assist CDN – a Meghívási funkció a következő HTTP-fejléceket foglalja magában a Akamai CDN és a Azure Media Services-forrás között:

|HTTP-fejléc|Értékek|Küldő|Fogadó|Szerep|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-prefektus-enabled | 1 (alapértelmezett) vagy 0 |Tartalomkézbesítési hálózat (CDN)|Forrás|Annak jelzése, hogy a CDN engedélyezve van-e|
|CDN-Origin-Assist-prefektus-Path| Példa: <br/>Töredékek (videó = 1400000000, Format = mpd-Time-CMAF)|Forrás|Tartalomkézbesítési hálózat (CDN)|A CDN elérési útjának biztosítása|
|CDN – forrás – assziszt – prefektus – kérelem|1 (kirendelési kérelem) vagy 0 (normál kérelem)|Tartalomkézbesítési hálózat (CDN)|Forrás|A CDN kérelmének jelzése|

A következő lépésekkel tekintheti meg a fejléc-Exchange működés közbeni részét:

1. A Poster vagy a curl használatával adjon ki egy, a hang-vagy video-szegmensre vagy töredékre vonatkozó Media Services-forrásra vonatkozó kérést. Ügyeljen arra, hogy adja hozzá a CDN-Origin-Assist-prefektus-enabled: 1 fejlécet a kérelemben.
2. A válaszban látnia kell a CDN-Origin-Assist-prefektus-Path fejlécet a relatív elérési úttal, mint az értékét.

## <a name="november-2019"></a>2019. november

### <a name="live-transcription-preview"></a>Élő átirat előzetes verzió

Az élő átirat mostantól nyilvános előzetes verzióban érhető el, és az USA 2. nyugati régiójában használható.

Az élő átírást úgy terveztük, hogy az élő eseményekkel együtt használható kiegészítő képességként.  A szolgáltatás az átmenő és a standard vagy a prémium kódolású élő eseményeken is támogatott.  Ha ez a szolgáltatás engedélyezve van, a szolgáltatás a Cognitive Services [beszéd-szöveg](../../cognitive-services/speech-service/speech-to-text.md) funkciója segítségével átmásolja a beérkező szövegben szereplő szóbeli szavakat. Ezt a szöveget ezután elérhetővé kell tenni a videóval és hanggal együtt az MPEG-DASH és a HLS protokollok esetében. A számlázás egy új kiegészítő mérőszámon alapul, amely az élő eseményhez képest további költségeket eredményez, ha a "Running" állapotban van.  Az élő átírással és a számlázással kapcsolatos részletekért lásd: [élő átirat](live-transcription.md)

> [!NOTE]
> Jelenleg az élő átírás csak előzetes verzióként érhető el az USA 2. nyugati régiójában. Jelenleg csak az angol (en-US) nyelven beszélő szavak átírását támogatja.

### <a name="content-protection"></a>Tartalomvédelem

A korlátozott régiókban megjelent *jogkivonat-újrajátszás megelőzési* funkciója mostantól minden régióban elérhető.
Media Services ügyfél mostantól korlátozhatja, hogy a rendszer hányszor használhatja ugyanazt a jogkivonatot kulcs vagy licenc igénylésére. További információ: jogkivonat- [újrajátszás megakadályozása](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Új ajánlott élő kódoló partnerek

A következő új ajánlott partneri kódolók támogatása a RTMP Live streaminghez:

- [Cambria élő 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 és Max Action kamerák](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>A fájl kódolásának továbbfejlesztése

- Mostantól elérhető egy új tartalom-kompatibilis kódolási beállításkészlet. A szolgáltatás a GOP-ra igazított MP4-készletet a tartalommal kompatibilis kódolással hozza létre. A szolgáltatás bármilyen bemeneti tartalmat figyelembe vesz, és a bemeneti tartalom kezdeti egyszerű elemzését végzi. Ezeket az eredményeket használja a rétegek optimális számának, a megfelelő átviteli sebességnek és a megoldási beállításoknak az adaptív adatfolyamok általi továbbításához. Ez a készlet különösen hatékony az alacsony bonyolultságú és közepes bonyolultságú videókhoz, ahol a kimeneti fájlok alacsonyabb átviteli sebességű, de olyan minőségben, amely továbbra is jó élményt nyújt a nézők számára. A kimenet video-és hangalapú MP4-fájlokat fog tartalmazni. További információ: [Open API-specifikációk](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Továbbfejlesztett teljesítmény és többszálas működés a standard szintű kódolóban. Adott körülmények között az ügyfélnek a 5-40%-os VOD-kódolású teljesítmény-növelést kell látnia. Az alacsony komplexitású tartalom több átviteli sebességre való kódolása a legmagasabb teljesítménybeli növekedést fogja látni. 
- A standard szintű kódolás mostantól egy normál GOP-lépésszám-tartalmat tart fenn, amely az időalapú GOP-beállítás használatakor a többtényezős adatforgalom (VFR) kódolására vonatkozik.  Ez azt jelenti, hogy a kevert képarányos tartalmat, amely a 15-30 fps között változik, például a kimenetre kiszámított normál GOP-távolságokat kell látni az adaptív sávszélességű streaming MP4-fájlokkal. Ez javítja a sávok közötti zökkenőmentes váltás lehetőségét a HLS vagy kötőjel továbbításakor. 
-  Továbbfejlesztett AV-szinkronizálás a változó Képkockasebesség (VFR) forrás tartalmához

### <a name="video-indexer-video-analytics"></a>Video Indexer, video Analytics

- A VideoAnalyzer-készlettel kinyert kulcsképek mostantól a videó eredeti felbontásában jelennek meg az átméretezés helyett. A nagyfelbontású kulcsképek kinyerése eredeti minőségi rendszerképeket biztosít, és lehetővé teszi a Microsoft Computer Vision által biztosított lemezkép-alapú mesterséges intelligencia-modellek használatát, és Custom Vision szolgáltatásokat, amelyekkel még több információt nyerhet a videóból.

## <a name="september-2019"></a>2019. szeptember

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Élő események élő lineáris kódolása

Media Services v3 bejelenti az élő események élő lineáris kódolásának 24 órás x 365 napos előzetes verzióját.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Adathordozó-processzorok elavulása

Bejelentjük, hogy a *Azure Media Indexer* elavult, és *Azure Media Indexer 2 előzetes*verzió. A nyugdíjazási dátumokért tekintse meg az [örökölt összetevőkkel](../previous/legacy-components.md) foglalkozó témakört. A [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ezeket a régi adathordozó-processzorokat váltja fel.

További információ: [áttelepítés Azure Media Indexerról és Azure Media Indexer 2 – Azure Media Services video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>2019. augusztus

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>A Dél-afrikai regionális pár nyitva van a Media Services 

A Media Services mostantól elérhető Dél-Afrikában Észak-és Dél-Afrika nyugati régiójában.

További információkért tekintse [meg a felhőket és a régiókat, amelyekben Media Services v3 létezik](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Adathordozó-processzorok elavulása

Bejelentjük a *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzorok elavult kivonását. A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](../previous/legacy-components.md) ismertető témakört.

Részletekért lásd: [a Tamás Áttelepítésének Media Encoder standard](https://go.microsoft.com/fwlink/?LinkId=2101334) és az [ame áttelepítésének Media Encoder standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>2019. július

### <a name="content-protection"></a>Tartalomvédelem

A jogkivonatok korlátozásával védett tartalom továbbításakor a végfelhasználóknak meg kell szerezniük egy jogkivonatot, amelyet a rendszer a kézbesítési kérelem részeként küld. A *jogkivonat-Visszajátszások megelőzési* funkciója lehetővé teszi, hogy Media Services ügyfelek megszabják, hogy egy adott jogkivonat hányszor használható kulcs vagy licenc igénylésére. További információ: jogkivonat- [újrajátszás megakadályozása](content-protection-overview.md#token-replay-prevention).

Júliustól az előzetes verzió funkció csak az USA középső és az USA nyugati középső régiójában érhető el.

## <a name="june-2019"></a>2019. június

### <a name="video-subclipping"></a>Videó kivágása

Most már elvégezheti a videó kivágását vagy alvágását, amikor [feladatokkal](https://docs.microsoft.com/rest/api/media/jobs)kódolja. 

Ez a funkció a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával létrehozott bármely [átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik. 

Példák:

* [Videó kivágása a .NET-tel](subclip-video-dotnet-howto.md)
* [Videó kivágása a REST-tel](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019. május

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Media Services diagnosztikai naplók és metrikák Azure Monitor támogatása

Mostantól Azure Monitor használatával megtekintheti a Media Services által kibocsátott telemetria-adatmennyiséget.

* A Media Services kulcs kézbesítési végpontja által küldött kérelmek figyeléséhez használja a Azure Monitor diagnosztikai naplókat. 
* Media Services [streaming-végpontok](streaming-endpoint-concept.md)által kibocsátott mérőszámok figyelése.   

Részletekért lásd: [Media Services metrikák és diagnosztikai naplók figyelése](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Többszörös hangsávok támogatják a dinamikus csomagolást 

Ha több, több kodeket és nyelvet tartalmazó hangsávokkal rendelkező adatfolyam-továbbítási eszközt is tartalmaz, a [dinamikus csomagolás](dynamic-packaging-overview.md) mostantól támogatja a HLS kimenetének több hangsávot (4-es vagy újabb verzió).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionális pár nyitva van a Media Services 

A Media Services mostantól Korea középső és déli régiójában érhető el. 

További információkért tekintse [meg a felhőket és a régiókat, amelyekben Media Services v3 létezik](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Teljesítménnyel kapcsolatos fejlesztések

A Media Services teljesítmény-fejlesztési funkciókat tartalmazó frissítések lettek hozzáadva.

* A feldolgozáshoz támogatott maximális fájlméret frissült. Lásd: [kvóták és korlátozások](limits-quotas-constraints.md).
* A [kódolási sebesség fejlesztése](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>2019. április

### <a name="new-presets"></a>Új beállításkészletek

* A rendszer hozzáadta a [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) a beépített analizátor-előkészletekhez.
* A rendszer hozzáadta a [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) a beépített kódoló-előbeállításokhoz. További információ: [Content-Aware kódolás](content-aware-encoding.md). 

## <a name="march-2019"></a>2019. március

A dinamikus csomagolás mostantól támogatja a Dolby Atmos-t. További információ: [dinamikus csomagolás által támogatott hangkodekek](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Mostantól megadhatja az eszköz vagy a fiók szűrőinek listáját, amely a folyamatos átviteli Lokátorra vonatkozik. További információ: [szűrők hozzárendelése a streaming Locator](filters-concept.md#associating-filters-with-streaming-locator)szolgáltatáshoz.

## <a name="february-2019"></a>2019. február

A Media Services v3 mostantól támogatott az Azure National felhőkben. Az összes funkció még nem érhető el az összes felhőben. Részletekért lásd: [felhők és régiók, amelyekben Azure Media Services v3 létezik](azure-clouds-regions.md).

A [Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) esemény hozzá lett adva a Media Services Azure Event Grid sémához.

## <a name="january-2019"></a>2019. január

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard-és MPI-fájlok 

Ha az MP4-fájl (ok) Media Encoder Standard kódolást hoz létre, a rendszer létrehoz egy új. MPI-fájlt, és hozzáadja a kimeneti eszközhöz. Ez az MPI-fájl a [dinamikus csomagolás](dynamic-packaging-overview.md) és a folyamatos átviteli forgatókönyvek teljesítményének növelésére szolgál.

Ne módosítsa vagy távolítsa el az MPI-fájlt, vagy az ilyen fájl létezésére (vagy nem) való függőséget a szolgáltatásban.

## <a name="december-2018"></a>2018. december

A V3 API GA-kiadásának frissítései a következők:
       
* A **PresentationTimeRange** tulajdonságai többé nem szükségesek az **eszközök** és a **fiókok szűrőinek**kiszűréséhez. 
* A **feladatok** és **átalakítások** $Top és $skip lekérdezési beállításai el lettek távolítva, és $OrderBy lettek hozzáadva. Az új rendezési funkció hozzáadásának részeként a rendszer azt észlelte, hogy a $top és $skip lehetőség korábban még nem volt elérhető, noha nincsenek implementálva.
* A számbavétel bővíthetőségét újra engedélyezték. Ez a funkció engedélyezve volt az SDK előzetes verzióiban, és véletlenül le lett tiltva a GA verzióban.
* Két előre megadott folyamatos átviteli házirend lett átnevezve. A **SecureStreaming** mostantól **MultiDrmCencStreaming**. A **SecureStreamingWithFairPlay** most már **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>2018. november

A CLI 2,0 modul már elérhető a [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Új parancsok

- [az AMS-fiók](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az AMS Account-Filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az AMS Asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az AMS Asset-Filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az AMS Content-Key-Policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az AMS Job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az AMS Live-Event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az AMS Live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az AMS streaming-Endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az AMS streaming-Locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) -lehetővé teszi a Media szolgáltatás számára fenntartott egységek kezelését. További információ: a [Media szolgáltatás számára fenntartott egységek skálázása](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Új funkciók és a változások megszakítása

#### <a name="asset-commands"></a>Eszköz parancsai

- ```--storage-account```és ```--container``` argumentumok hozzáadva.
- A parancs hozzáadta a lejárati idő (most + 23H) és az engedélyek (olvasás) alapértelmezett értékeit ```az ams asset get-sas-url``` .

#### <a name="job-commands"></a>Feladatok parancsai

- ```--correlation-data```és ```--label``` argumentumok hozzáadva
- ```--output-asset-names```átnevezve: ```--output-assets``` . Mostantól fogadja a "assetName = label" formátumú adategységek szóközzel tagolt listáját. Címkét nem tartalmazó adategység küldése a következőhöz hasonló: "assetName =".

#### <a name="streaming-locator-commands"></a>Adatfolyam-kereső parancsai

- ```az ams streaming locator```az alapparancs le lett cserélve ```az ams streaming-locator``` .
- ```--streaming-locator-id```és ```--alternative-media-id support``` argumentumok hozzáadva.
- ```--content-keys argument```az argumentum frissítve.
- ```--content-policy-name```átnevezve: ```--content-key-policy-name``` .

#### <a name="streaming-policy-commands"></a>Streaming Policy parancsok

- ```az ams streaming policy```az alapparancs le lett cserélve ```az ams streaming-policy``` .
- A titkosítási paraméterek támogatják a ```az ams streaming-policy create``` hozzáadását.

#### <a name="transform-commands"></a>Átalakítási parancsok

- ```--preset-names```argumentum lecserélve ```--preset``` . Mostantól egyszerre csak 1 kimenetet vagy beállításkészletet állíthat be (további futtatáshoz ```az ams transform output add``` ). Emellett egyéni StandardEncoderPreset is beállíthat az egyéni JSON elérési útjának átadásával.
- ```az ams transform output remove```a kimeneti index eltávolításának átadásával végezhető el.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumentumok hozzáadva a ```az ams transform create``` és ```az ams transform output add``` parancsokhoz.

## <a name="october-2018---ga"></a>Október 2018 – GA

Ez a szakasz a Azure Media Services (AMS) októberi frissítéseit ismerteti.

### <a name="rest-v3-ga-release"></a>REST v3 GA kiadás

A [Rest v3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) -as kiadás több API-t tartalmaz az élő, a fiókok és az eszközök szintjének szűréséhez, valamint a DRM-támogatáshoz.

#### <a name="azure-resource-management"></a>Azure-erőforrások kezelése 

Az Azure Erőforrás-kezelés támogatása lehetővé teszi az egységes felügyeleti és üzemeltetési API-t (mostantól minden egy helyen).

Ettől a kiadástól kezdve a Resource Manager-sablonok használatával élő eseményeket hozhat létre.

#### <a name="improvement-of-asset-operations"></a>Az Asset Operations fejlesztése 

A következő fejlemények lettek bevezetve:

- HTTP (s) URL-címekről vagy Azure Blob Storage SAS URL-címekről való betöltés.
- Az eszközökhöz tartozó saját tárolók nevének megadása. 
- A Azure Functions használatával egyszerűbben hozhat létre egyéni munkafolyamatokat.

#### <a name="new-transform-object"></a>Új átalakítási objektum

Az új **átalakítási** objektum leegyszerűsíti a kódolási modellt. Az új objektum segítségével egyszerűen hozhat létre és oszthat meg Encoding Resource Manager-sablonokat és-készleteket. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory hitelesítés és RBAC

Az Azure AD-hitelesítés és a szerepköralapú Access Control (RBAC) lehetővé teszik a biztonságos átalakításokat, LiveEvents, tartalmi kulcsokra vonatkozó szabályzatokat vagy eszközöket az Azure AD-ban lévő szerepkörök vagy felhasználók számára.

#### <a name="client-sdks"></a>Ügyfél-SDK-k  

A Media Services v3 által támogatott nyelvek: .NET Core, Java, Node.js, Ruby, írógéppel, Python, go.

#### <a name="live-encoding-updates"></a>Élő kódolási frissítések

A következő élő kódolási frissítések vannak bevezetve:

- Új, alacsony késleltetésű mód az élő (10 másodperces végpont) számára.
- Továbbfejlesztett RTMP-támogatás (nagyobb stabilitás és több forrás-kódoló támogatása).
- A RTMP biztonságos betöltést biztosít.

    Ha élő eseményt hoz létre, most 4 betöltési URL-címet kap. A 4 betöltési URL-cím majdnem azonos, ugyanazzal a folyamatos átviteli jogkivonattal (AppId) rendelkezik, csak a portszám rész különbözik. A két URL-cím elsődleges és biztonsági másolat az RTMP-hez. 
- 24 órás átkódolási támogatás. 
- Továbbfejlesztett ad-jelzéses támogatás az RTMP-n keresztül a SCTE35-on keresztül.

#### <a name="improved-event-grid-support"></a>Továbbfejlesztett Event Grid támogatás

A következő Event Grid-támogatással kapcsolatos fejlesztési funkciók láthatók:

- Azure Event Grid integráció a Logic Apps és a Azure Functions egyszerűbb fejlesztéséhez. 
- Fizessen elő a kódolással, az élő csatornákkal és egyebekkel kapcsolatos eseményekre.

### <a name="cmaf-support"></a>CMAF-támogatás

A CMAF és a "CBCS" titkosítási támogatása az Apple HLS (iOS 11 +) és az MPEG-DASH-lejátszók számára, amelyek támogatják a CMAF.

### <a name="video-indexer"></a>Video Indexer

A Video Indexer GA kiadása augusztusban jelent meg. A jelenleg támogatott funkciókkal kapcsolatos új információk: [Mi az video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>A változtatások tervei

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Hamarosan elérhető az Azure CLI 2,0-modul, amely az összes funkcióra vonatkozik (beleértve az élő, a tartalmi kulcsra vonatkozó szabályzatokat, a fiók-és az adategységeket, a folyamatos átviteli szabályzatokat). 

### <a name="known-issues"></a>Ismert problémák

Az alábbi probléma csak azokat az ügyfeleket érinti, akik az előnézeti API-t használták az eszköz-vagy AccountFilters.

Ha 09/28 és 10/12 közötti eszközöket vagy fiók-szűrőket hozott létre Media Services v3 parancssori felülettel vagy API-kkal, el kell távolítania az összes eszközt és AccountFilters, majd újra létre kell hoznia őket a verziók ütközése miatt. 

## <a name="may-2018---preview"></a>Május 2018 – előzetes verzió

### <a name="net-sdk"></a>.NET SDK

A .NET SDK-ban a következő funkciók találhatók:

* **Átalakítja** és **feladatait** a médiatartalom kódolásához vagy elemzéséhez. Példák: adatfolyam- [fájlok](stream-files-tutorial-with-api.md) és- [elemzés](analyze-videos-tutorial-with-api.md).
* **Streaming-lokátorok** tartalmak közzétételéhez és a végfelhasználói eszközökre való továbbításához
* **Folyamatos átviteli szabályzatok** és **tartalmi kulcsokra vonatkozó házirendek** a kulcsfontosságú kézbesítés és a tartalomvédelem (DRM) konfigurálásához a tartalmak kézbesítése során.
* Élő **események** és **élő kimenetek** élő adatfolyam-tartalmak betöltésének és archiválásának konfigurálásához.
* A médiatartalom tárolására és közzétételére szolgáló **eszközök** az Azure Storage-ban. 
* **Adatfolyam-végpontok** a dinamikus csomagolás, a titkosítás és a folyamatos átviteli sebesség konfigurálásához és méretezéséhez mind az élő, mind az igény szerinti médiatartalmak esetében.

### <a name="known-issues"></a>Ismert problémák

* A feladatok elküldésekor megadhatja, hogy a forrás videóját HTTPS URL-címek, SAS URL-címek vagy az Azure Blob Storage-ban található fájlok elérési útjai használatával beolvassa. A Media Services v3 jelenleg nem támogatja a HTTPS URL-címeken keresztüli darabolásos átvitel kódolását.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

[Áttelepítési útmutató Media Services v2-ről v3-re való áttéréshez](migrate-from-v2-to-v3.md#known-issues).

## <a name="next-steps"></a>További lépések

- [Áttekintés](media-services-overview.md)
- [A Media Services v3 dokumentációjának frissítései](docs-release-notes.md)
- [A Media Services v2 kibocsátási megjegyzései](../previous/media-services-release-notes.md)
