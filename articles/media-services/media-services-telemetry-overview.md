---
title: Az Azure Media Services-Telemetria |} Microsoft Docs
description: "Ez a cikk áttekintést nyújt az Azure Media Services telemetriai adatokat."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 1b26d7925fe5bd39905d9f51d22433b1eea43af6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-telemetry"></a>Az Azure Media Services-Telemetria

Az Azure Media Services (AMS) lehetővé teszi, hogy a hozzá tartozó szolgáltatások telemetriai/metrikák adatokhoz való hozzáférést. AMS jelenlegi verziója lehetővé teszi, hogy a működés közbeni telemetriai adatokat gyűjthessen **csatorna**, **StreamingEndpoint**, és működés közbeni **archív** entitásokat. 

Telemetria ír egy Azure Storage-fiók megadott tárolási táblához (általában, használja a tárfiók az AMS-fiók társított). 

A telemetria-rendszer nem tudja kezelni az adatok megőrzése. A régi telemetriai adatok eltávolításához törölni kell a storage-táblákat.

Ez a témakör ismerteti a konfigurálásával és AMS telemetriai adatok felhasználását.

## <a name="configuring-telemetry"></a>Telemetriai adatok konfigurálása

Telemetria összetevő szintű részletesség konfigurálhatók. Nincsenek két részletességi szintje "Normál" és "Részletes". Mindkét szintek jelenleg, ugyanazokat az információkat adja vissza. Javasoljuk, hogy használja a "normál. 

A következő témakörök bemutatják, hogyan telemetriai engedélyezése:

[A telemetriai adatok .NET engedélyezése](media-services-dotnet-telemetry.md) 

[A telemetriai adatok REST engedélyezése](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Telemetria információk felhasználása

Telemetriai adatok írása az Azure Storage táblájához a tárfiókot, a Media Services-fiókhoz tartozó telemetriai konfigurálásakor megadott. Ez a szakasz ismerteti a metrikák a storage-táblákat.

Használatba vehetné a telemetriai adatokat a következő módszerek valamelyikével:

- Olvassa el az adatokat közvetlenül az Azure Table Storage (pl. tárhely SDK használatával). A telemetriai adatokat tároló tábla ismertetését lásd: a **telemetriai adatokat fel** a [ez](https://msdn.microsoft.com/library/mt742089.aspx) témakör.

Vagy

- Használjon a támogatás a Media Services .NET SDK tárolási adatok olvasása a [ez](media-services-dotnet-telemetry.md) témakör. 


Az alábbiakban ismertetett telemetriai séma ahhoz, hogy megkapja az Azure Table Storage keretein belül a megfelelő teljesítmény célja:

- Adatok particionálása fiókkal azonosítója és felügyeletiszolgáltatás-azonosító telemetriai minden szolgáltatás engedélyezése a egymástól függetlenül lehet lekérdezni.
- Partíció mindegyike a dátumot adjon egy ésszerű felső határa a partíció méretét.
- Sor kulcsai fordított idő érdekében, hogy lehetővé teszi az utolsó telemetriai elemek kérdezhető le egy adott szolgáltatáshoz.

Ez lehetővé teszi a közös lekérdezések hatékony számos:

- Párhuzamos, független különböző szolgáltatások adatainak letöltése.
- Egy adott szolgáltatáshoz dátumtartomány az összes adat beolvasása.
- A szolgáltatáshoz a legújabb adatok beolvasása.

### <a name="telemetry-table-storage-output-schema"></a>Kimeneti tárolási telemetriai táblaséma

Egy tábla, "TelemetryMetrics20160321" hol "20160321" az dátum, a létrehozott tábla összesítés telemetriai adatokat tárolja. Telemetria rendszer táblázatot hoz létre külön: 00:00 UTC alapú új naponként. A tábla Ismétlődő értékek tárolására szolgál például betöltési sávszélességű egy adott időszakban idő, a küldött bájtok mennyiségét, stb. 

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|{szolgáltatásfiók azonosítója} _ {Entitásazonosító}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66 < br /<br/>A Fiókazonosító egyszerűbbé teheti a munkafolyamatok, ahol több Media Services-fiók ugyanabban a tárfiókban ír a partíciós kulcs szerepel.
RowKey|{másodperc éjféli} _ {véletlenszerű értéket}|01688_00199<br/><br/>A sorkulcs kezdődik-e a felső n stílus lekérdezések a partíción belüli engedélyezéséhez éjféli másodpercek számát. További információkért lásd: [ez](../cosmos-db/table-storage-design-guide.md#log-tail-pattern) cikk. 
időbélyeg|Dátum és idő|Az Azure tábla 2016 időbélyeg Auto-09-09T22:43:42.241Z
Típus|A telemetriai adatokat szolgáltató entitás típusa|Csatorna/StreamingEndpoint/archív<br/><br/>Esemény típusa csak karakterlánc-értéke.
Név|A telemetriai adatok esemény neve|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Az idő a telemetriai adatok esemény történt (idő szerint UTC)|2016-09-09T22:42:36.924Z<br/><br/>A megfigyelt idő (például egy csatorna) telemetriai adatok küldése az entitás valósul meg. Nem határozható meg, ez az érték összetevői közötti szinkronizálás problémák megközelítőleges időpontja
ServiceID|{felügyeletiszolgáltatás-azonosító}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitás jellemző tulajdonságok|Az esemény által meghatározott módon|StreamName: stream1, sávszélességű 10123...<br/><br/>A többi tulajdonság az adott esemény típushoz vannak definiálva. Az Azure tábla tartalma kulcs-érték párokat.  (Ez azt jelenti, hogy a tábla különböző soraira különböző tulajdonságkészletekkel rendelkező tulajdonságai).

### <a name="entity-specific-schema"></a>Entitás-specifikus séma

A következő gyakorisággal leküldött entitásra vonatkozó távmérést adatokat bejegyzések három típusa van:

- Adatfolyam-végpontok: minden 30 másodperc
- Az élő csatornák: percenként
- Archív Live: percenként

**A Streamvégpont**

Tulajdonság|Érték|Példák
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
időbélyeg|időbélyeg|Automatikus Azure tábla 2016 időbélyeg-09-09T22:43:42.241Z
Típus|Típus|StreamingEndpoint
Név|Név|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Felügyeletiszolgáltatás-azonosító|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Állomásnév|A végpont állomásneve|builddemoserver.origin.mediaservices.Windows.NET
statusCode|Rögzíti a HTTP-állapot|200
ResultCode|Eredmény kódja részletei|ÉRTÉKKEL
RequestCount|Összes kérést az összesítés|3
BytesSent|Küldött bájtok összesített száma|2987358
ServerLatency|Átlagos kiszolgáló késleltetése (beleértve a tároló)|129
E2ELatency|Átlagos végpontok közötti késés|250

**Élő csatorna**

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
időbélyeg|időbélyeg|Az Azure tábla 2016 időbélyeg Auto-09-09T22:43:42.241Z
Típus|Típus|Csatorna
Név|Név|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Felügyeletiszolgáltatás-azonosító|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Írja be a videó/hang/szöveg nyomon követése|videó/hang
TrackName|A track neve|videó/audio_1
Átviteli sebesség|Track sávszélességű|785000
CustomAttributes||   
IncomingBitrate|Tényleges bejövő sávszélességű|784548
OverlapCount|A bemeneti átfedésben vannak|0
DiscontinuityCount|A track folytonosságában|0
LastTimestamp|Utolsó feldolgozott adatokat időbélyeg|1800488800
NonincreasingCount|Töredék száma nem növekvő miatt elvetett időbélyeg|2
UnalignedKeyFrames|E fragment(s) érkezett (különböző szolgáltatásminőségi szinteket) ahol kulcs nincsenek egyeztetve keretek |True (Igaz)
UnalignedPresentationTime|E megkaptuk fragment(s) (egyes szolgáltatásminőségi szinteket /), ahol nem illeszkednek a bemutató idő|True (Igaz)
UnexpectedBitrate|Igaz Ha a hang-és videófolyamot számított/tényleges sávszélességű > 40 000 bps és IncomingBitrate nyomon == vagy IncomingBitrate és actualBitrate eltérő 50 % 0 |True (Igaz)
Kifogástalan|IGAZ, ha <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> a rendszer minden 0|True (Igaz)<br/><br/>Kifogástalan állapot van olyan összetett függvényt ad vissza, ha az alábbi feltételek bármelyike tartsa hamis:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames == True<br/>-UnalignedPresentationTime == True<br/>-UnexpectedBitrate == True

**Élő archív**

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
időbélyeg|időbélyeg|Az Azure tábla 2016 időbélyeg Auto-09-09T22:43:42.241Z
Típus|Típus|Archívum
Név|Név|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Felügyeletiszolgáltatás-azonosító|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Program URL-címe|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba.ISM
TrackName|A track neve|audio_1
TrackType|A track típusa|Hang-és videófolyamot
CustomAttribute|Hexadecimális karakterlánc, amely állapottípust határoz meg különböző nyomon követése, azonos nevű és átviteli sebesség (multi kamera szög)|
Átviteli sebesség|Track sávszélességű|785000
Kifogástalan|IGAZ, ha FragmentDiscardedCount == 0 & & ArchiveAcquisitionError == False|Igaz (e két érték nem szerepelnek a mérték, de jelen a forrás esemény)<br/><br/>Kifogástalan állapot van olyan összetett függvényt ad vissza, ha az alábbi feltételek bármelyike tartsa hamis:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError == True

## <a name="general-qa"></a>Általános kérdések és válaszok

### <a name="how-to-consume-metrics-data"></a>Hogyan metrikák adatokat?

Metrikák tárolja az ügyfél tárfiókja Azure táblák sorozataként. Ezek az adatok használatával a következő eszközöket képes használni:

- AMS SDK
- A Microsoft Azure Tártallózó (vesszővel tagolt adatfájlba és a feldolgozott Excel exportálási támogatja)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hol találhatók a átlagos sávszélesség-használat?

Az átlagos sávszélesség-használat BytesSent átlagát az időtartam alatt.

### <a name="how-to-define-streaming-unit-count"></a>Hogyan streamelési egységek számának meghatározásához?

A streamelési egységek száma a szolgáltatás streamvégpontok a maximális átviteli sebesség egy adatfolyam-továbbítási végpontjának osztva az a maximális átviteli sebesség adható meg. Egy streamvégpont használható maximális átviteli sebesség 160 MB/s.
Tegyük fel, hogy az ügyfél szolgáltatás a maximális átviteli sebesség 40 MB/s (a maximális értékének BytesSent az időtartam alatt). Ezt követően a streamelési egységek száma megegyezik (40 MB/s) * (8 Bit/bájt) /(160 Mbps) = 2 adatfolyam-továbbítási egység.

### <a name="how-to-find-average-requestssecond"></a>Hol találhatók átlagos kérelmek/másodperc?

Átlagos száma található kérelmek/másodperc, számítási kérelmek (RequestCount) átlagos száma az időtartam alatt.

### <a name="how-to-define-channel-health"></a>Hogyan csatorna állapotának meghatározásához?

Csatorna állapotfigyelő logikai függvény összetett lehet meghatározni, hogy legyen hamis tartsa a következő feltételek:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Hogyan folytonosság megszakítását észlelését?

Minden csatorna adatok bejegyzések kerültek folytonosság megszakítását észleléséhez, ahol DiscontinuityCount > 0. A megfelelő ObservedTime időbélyeg az időpontokat a folytonosság megszakítását létrejöttének időpontját jelzi.

### <a name="how-to-detect-timestamp-overlaps"></a>Hogyan észleli a rendszer átfedéseket időbélyeg?

Minden csatorna adatok bejegyzések kerültek észleli a rendszer átfedéseket timestamp, ahol OverlapCount > 0. A megfelelő ObservedTime időbélyeg azt jelenti, hogy az időbélyegző átfedésben van az időpontokat történt.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hol találhatók az adatfolyam-továbbítási kérelem hibákat és azokat az oka?

Adatfolyam-továbbítási kérelem hibák és okok megkereséséhez található összes Streamvégponton adatok bejegyzés, ahol ResultCode nincs értékkel egyenlőnek. A megfelelő StatusCode mező azt jelzi, hogy a kérelem sikertelenségének okát.

### <a name="how-to-consume-data-with-external-tools"></a>Hogyan lehet külső eszközökkel adatokat?

Távmérést adatok feldolgozása, illetve a következő eszközök formájában jelenik meg:

- PowerBI
- Application Insights
- Az Azure Monitor (korábbi nevén Shoebox)
- AMS élő irányítópult
- Azure-portálon (függőben lévő kiadás)

### <a name="how-to-manage-data-retention"></a>Hogyan kezelhetők az adatmegőrzés?

A telemetria-rendszer nem biztosít megőrzési adatkezelés vagy automatikus törlési régi rögzíti. Így kell kezelni, és törölje manuálisan a régi rögzíti a tárolási táblából. Tárolási menete SDK hivatkozhat.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
