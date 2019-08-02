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
ms.date: 06/07/2019
ms.author: juliako
ms.openlocfilehash: c730f41d1dbc48c6622d0a2ba43c32dd1a96c24c
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311808"
---
# <a name="azure-media-services-v3-release-notes"></a>A Azure Media Services v3 kibocsátási megjegyzései

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="known-issues"></a>Ismert problémák

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [Rest APIt](https://aka.ms/ams-v3-rest-sdk), a CLI-t vagy az egyik támogatott SDK-t.

További információ: áttelepítési [útmutató Media Services v2-ről v3-re](migrate-from-v2-to-v3.md#known-issues)való áttéréshez.

## <a name="july-2019"></a>Július 2019

### <a name="content-protection"></a>Tartalomvédelem

A jogkivonatok korlátozásával védett tartalom továbbításakor a végfelhasználóknak meg kell szerezniük egy jogkivonatot, amelyet a rendszer a kézbesítési kérelem részeként küld. A *jogkivonat* -Visszajátszások megelőzési funkciója lehetővé teszi, hogy Media Services ügyfelek megszabják, hogy egy adott jogkivonat hányszor használható kulcs vagy licenc igénylésére. További információ: [jogkivonat](content-protection-overview.md#token-replay-prevention)-újrajátszás megakadályozása.

Ez a szolgáltatás jelenleg az USA középső és nyugati középső régiójában érhető el.

## <a name="june-2019"></a>Június 2019

### <a name="video-subclipping"></a>Videó kivágása

Most már elvégezheti a videó kivágását vagy alvágását [](https://docs.microsoft.com/rest/api/media/jobs), amikor feladatokkal kódolja. 

Ez a funkció a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával létrehozott bármely [átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik. 

Példák:

* [Videó kivágása a .NET-tel](subclip-video-dotnet-howto.md)
* [Videó kivágása a REST-tel](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019. május

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Media Services diagnosztikai naplók és metrikák Azure Monitor támogatása

Mostantól a Azure Monitor használatával megtekintheti a telemetria-emmited Media Services alapján.

* A Media Services kulcs kézbesítési végpontja által küldött kérelmek figyeléséhez használja a Azure Monitor diagnosztikai naplókat. 
* Media Services [streaming](streaming-endpoint-concept.md)-végpontok által kibocsátott mérőszámok figyelése.   

Részletekért lásd: [Media Services metrikák és diagnosztikai naplók figyelése](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Többszörös hangsávok támogatják a dinamikus csomagolást 

Ha több, több kodeket és nyelvet tartalmazó hangsávokkal rendelkező adatfolyam-továbbítási eszközt is tartalmaz, a [dinamikus csomagolás](dynamic-packaging-overview.md) mostantól támogatja a HLS kimenetének több hangsávot (4-es vagy újabb verzió).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionális pár nyitva van a Media Services 

A Media Services mostantól Korea középső és déli régiójában érhető el. 

További információkért tekintse [meg a felhőket és a régiókat, amelyekben Media Services v3 létezik](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Teljesítménybeli változások

A Media Services teljesítmény-fejlesztési funkciókat tartalmazó frissítések lettek hozzáadva.

* A feldolgozáshoz támogatott maximális fájlméret frissült. Lásd: [kvóták és korlátozások](limits-quotas-constraints.md).
* A [kódolási sebesség fejlesztése](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Április 2019

### <a name="new-presets"></a>Új beállításkészletek

* A rendszer hozzáadta a [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) a beépített analizátor-előkészletekhez.
* A rendszer hozzáadta a [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) a beépített kódoló-előbeállításokhoz. További információ: [Content-Aware kódolás](cae-experimental.md). 

## <a name="march-2019"></a>Március 2019

A dinamikus csomagolás mostantól támogatja a Dolby Atmos-t. További információ: [dinamikus csomagolás által támogatott](dynamic-packaging-overview.md#audio-codecs)hangkodekek.

Mostantól megadhatja az eszköz vagy a fiók szűrőinek listáját, amely a folyamatos átviteli Lokátorra vonatkozik. További információ: [szűrők hozzárendelése a streaming Locator](filters-concept.md#associating-filters-with-streaming-locator)szolgáltatáshoz.

## <a name="february-2019"></a>Február 2019

A Media Services v3 mostantól támogatott az Azure National felhőkben. Az összes funkció még nem érhető el az összes felhőben. Részletekért lásd: [felhők és régiók, amelyekben Azure Media Services v3 létezik](azure-clouds-regions.md).

A [Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) esemény hozzá lett adva a Media Services Azure Event Grid sémához.

## <a name="january-2019"></a>Január 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard-és MPI-fájlok 

Ha az MP4-fájl (ok) Media Encoder Standard kódolást hoz létre, a rendszer létrehoz egy új. MPI-fájlt, és hozzáadja a kimeneti eszközhöz. Ez az MPI-fájl a [dinamikus csomagolás](dynamic-packaging-overview.md) és a folyamatos átviteli forgatókönyvek teljesítményének növelésére szolgál.

Ne módosítsa vagy távolítsa el az MPI-fájlt, vagy az ilyen fájl létezésére (vagy nem) való függőséget a szolgáltatásban.

## <a name="december-2018"></a>2018. december

A V3 API GA-kiadásának frissítései a következők:
       
* A **PresentationTimeRange** tulajdonságai többé nem szükségesek az **eszközök** és a **fiókok szűrőinek**kiszűréséhez. 
* A **feladatok** és **átalakítások** $Top és $skip lekérdezési beállításai el lettek távolítva, és $OrderBy lettek hozzáadva. Az új rendezési funkció hozzáadásának részeként a rendszer azt észlelte, hogy a $top és $skip lehetőség korábban még nem volt elérhető, noha nincsenek implementálva.
* A számbavétel bővíthetőségét újra engedélyezték. Ez a funkció engedélyezve volt az SDK előzetes verzióiban, és véletlenül le lett tiltva a GA verzióban.
* Két előre megadott folyamatos átviteli házirend lett átnevezve. A **SecureStreaming** mostantól **MultiDrmCencStreaming**. A **SecureStreamingWithFairPlay** mostantól **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

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
- A parancs hozzáadta a lejárati idő (most + 23H) és az ```az ams asset get-sas-url``` engedélyek (olvasás) alapértelmezett értékeit.

#### <a name="job-commands"></a>Feladatok parancsai

- ```--correlation-data```és ```--label``` argumentumok hozzáadva
- ```--output-asset-names```átnevezve ```--output-assets```:. Mostantól fogadja a "assetName = label" formátumú adategységek szóközzel tagolt listáját. Címkét nem tartalmazó adategység küldése a következőhöz hasonló: "assetName =".

#### <a name="streaming-locator-commands"></a>Adatfolyam-kereső parancsai

- ```az ams streaming locator```az ```az ams streaming-locator```alapparancs le lett cserélve.
- ```--streaming-locator-id```és ```--alternative-media-id support``` argumentumok hozzáadva.
- ```--content-keys argument```az argumentum frissítve.
- ```--content-policy-name```átnevezve ```--content-key-policy-name```:.

#### <a name="streaming-policy-commands"></a>Streaming Policy parancsok

- ```az ams streaming policy```az ```az ams streaming-policy```alapparancs le lett cserélve.
- A titkosítási paraméterek támogatják ```az ams streaming-policy create``` a hozzáadását.

#### <a name="transform-commands"></a>Átalakítási parancsok

- ```--preset-names```argumentum lecserélve ```--preset```. Mostantól egyszerre csak 1 kimenetet vagy beállításkészletet állíthat be (további futtatáshoz ```az ams transform output add```). Emellett egyéni StandardEncoderPreset is beállíthat az egyéni JSON elérési útjának átadásával.
- ```az ams transform output remove```a kimeneti index eltávolításának átadásával végezhető el.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumentumok hozzáadva ```az ams transform output add``` a ```az ams transform create``` és parancsokhoz.

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

A Media Services v3 által támogatott nyelvek: .NET Core, Java, Node. js, Ruby, írógéppel, Python, go.

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

### <a name="plans-for-changes"></a>Módosítások tervek

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Hamarosan elérhető az Azure CLI 2,0-modul, amely az összes funkcióra vonatkozik (beleértve az élő, a tartalmi kulcsra vonatkozó szabályzatokat, a fiók-és az adategységeket, a folyamatos átviteli szabályzatokat). 

### <a name="known-issues"></a>Ismert problémák

Az alábbi probléma csak azokat az ügyfeleket érinti, akik az előnézeti API-t használták az eszköz-vagy AccountFilters.

Ha 09/28 és 10/12 közötti eszközöket vagy fiók-szűrőket hozott létre Media Services v3 parancssori felülettel vagy API-kkal, el kell távolítania az összes eszközt és AccountFilters, majd újra létre kell hoznia őket a verziók ütközése miatt. 

## <a name="may-2018---preview"></a>Május 2018 – előzetes verzió

### <a name="net-sdk"></a>.NET SDK

A .NET SDK-ban a következő funkciók találhatók:

* **Átalakítja** és feladatait a médiatartalom kódolásához vagy elemzéséhez. **** Példák: adatfolyam- [fájlok](stream-files-tutorial-with-api.md) és- [elemzés](analyze-videos-tutorial-with-api.md).
* **Streaming** -lokátorok tartalmak közzétételéhez és a végfelhasználói eszközökre való továbbításához
* **Folyamatos átviteli szabályzatok** és **tartalmi kulcsokra vonatkozó házirendek** a kulcsfontosságú kézbesítés és a tartalomvédelem (DRM) konfigurálásához a tartalmak kézbesítése során.
* Élő **események** és **élő kimenetek** élő adatfolyam-tartalmak betöltésének és archiválásának konfigurálásához.
* A médiatartalom tárolására és közzétételére szolgáló **eszközök** az Azure Storage-ban. 
* **Adatfolyam** -végpontok a dinamikus csomagolás, a titkosítás és a folyamatos átviteli sebesség konfigurálásához és méretezéséhez mind az élő, mind az igény szerinti médiatartalmak esetében.

### <a name="known-issues"></a>Ismert problémák

* A feladatok elküldésekor megadhatja, hogy a forrás videóját HTTPS URL-címek, SAS URL-címek vagy az Azure Blob Storage-ban található fájlok elérési útjai használatával beolvassa. Az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
