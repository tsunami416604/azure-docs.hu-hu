---
title: Az Azure Media Services Telemetriai |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Media Services-telemetria.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5f33249496c9a4fc8a094e64f15c8d95da69194b
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294875"
---
# <a name="azure-media-services-telemetry"></a>Az Azure Media Services-telemetria  

Az Azure Media Services (AMS) lehetővé teszi a telemetriai adatok és metrikák adatokhoz a szolgáltatásokhoz való hozzáférést. Az AMS jelenlegi verziója lehetővé teszi a telemetriai adatokat gyűjthessen élő **csatorna**, **Streamvégpontok**, és élő **archív** entitásokat. 

Telemetriai adatokat írt egy tárolótáblában a megadott Azure Storage-fiók (általában akkor használja az AMS-fiók társított storage-fiók). 

A telemetriai rendszer nem felügyeli az adatmegőrzés. A storage-táblák törlésével eltávolíthatja a a korábbi telemetriai adatokat.

Ez a témakör ismerteti az AMS telemetriai adatok felhasználását és konfigurálása.

## <a name="configuring-telemetry"></a>Telemetria konfigurálása

Telemetria egy összetevő szintű részletességig is beállíthatja. Nincsenek két részletességgel "Normál" vagy "Részletes". Mindkét szintek jelenleg ugyanazokat az információkat adja vissza. Javasoljuk, hogy használja a "normál. 

A következő témakörök bemutatják, hogyan telemetria engedélyezése:

[.NET-tel telemetria engedélyezése](media-services-dotnet-telemetry.md) 

[A REST segítségével telemetria engedélyezése](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Fogyasztó telemetrikus adatokat

Telemetriai adatokat egy Azure Storage-táblához, amely a Media Services-fiókot telemetria konfigurálásakor megadott tárfiókban van megírva. Ez a szakasz ismerteti a storage-táblák a metrikákkal.

Telemetriai adatokat felhasználhatja az alábbi módszerek egyikével:

- Olvassa el az adatokat közvetlenül az Azure Table Storage (például a Storage SDK-val). A telemetriai adatok tárolótáblákat ismertetését lásd: a **telemetrikus adatokat használó** a [ez](https://msdn.microsoft.com/library/mt742089.aspx) témakör.

Vagy

- A támogatást a Media Services .NET SDK a tár-adatok beolvasására szolgáló ismertetett módon kihasználhassák [ez](media-services-dotnet-telemetry.md) témakör. 


Az alábbiakban ismertetett telemetriai séma Azure Table Storage határain belül jó teljesítményt biztosíthat célja:

- A fiók- és szolgáltatás-Azonosítóját, hogy az egyes szolgáltatásoktól érkező telemetriai adatok egymástól függetlenül lehet lekérdezni az adatok particionálása.
- Partíciók tartalmazza a dátumot adjon egy ésszerű felső határa a partíció méretét.
- Sor kulcsai fordított idő ahhoz, hogy az adott szolgáltatás kérdezhető le a legújabb telemetriai elemek.

Ez lehetővé teszi számos, a gyakori lekérdezések számára hatékony:

- Párhuzamos, független szolgáltatások külön adatainak letöltése.
- Egy adott szolgáltatáshoz, a dátumtartomány összes adat beolvasása.
- A szolgáltatás a legfrissebb adatok beolvasása során.

### <a name="telemetry-table-storage-output-schema"></a>Telemetriai table storage kimeneti sémája

Egy tábla, "TelemetryMetrics20160321", "20160321" az dátum, a létrejött táblában szereplő telemetriai adatokat tárolja. Telemetriai rendszer új naponta: 00:00 (UTC) alapján hoz létre egy különálló táblában. A táblázat az ismétlődő értékek tárolására szolgál például betöltési sávszélességű egy adott időszakban idő, a küldött bájtok, stb. 

Tulajdonság|Érték|Példák és megjegyzések
---|---|---
PartitionKey|{fiókazonosító} _ {entity Azonosítójú}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>A Fiókazonosító tartalmazza a partíciókulcsot, ahol több Media Services-fiókok írása ugyanazt a tárfiókot a munkafolyamatok egyszerűsítése érdekében.
RowKey|{seconds éjféli} _ {véletlenszerű értéket}|01688_00199<br/><br/>A sorkulcs indul ki, hogy a felső n stílus lekérdezések egy partíción belül éjféli másodpercek számát. További információkért tekintse meg [ezt](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) a cikket. 
Időbélyeg|Dátum/idő|Automatikus időbélyege az Azure-tábla 2016-09-09T22:43:42.241Z
Typo|A telemetria-adatokat biztosítva entitás típusa|Channel/StreamingEndpoint/Archive<br/><br/>Esemény típus egy karakterláncértéket.
Name (Név)|A telemetriai esemény neve|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Az idő, a telemetriai esemény történt (UTC)|2016-09-09T22:42:36.924Z<br/><br/>A megfigyelt idő (például egy csatorna) telemetriát küld az entitás által biztosított. Szinkronizálási hibák, ez az érték-összetevők közötti hozzávetőleges időt lehet
ServiceID|{service} Azonosítójú|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitás jellemző tulajdonságok|Az esemény által meghatározott módon|StreamName: stream1, sávszélességű 10123...<br/><br/>A többi tulajdonság az adott esemény típus vannak meghatározva. Az Azure tábla tartalma kulcs-érték párral.  (a tábla az egyes sorok eltérő kell, hogy a különböző tulajdonságokat is).

### <a name="entity-specific-schema"></a>Entitás-specifikus séma

A következő gyakorisággal leküldött entitásspecifikus telemetria-adatok bejegyzések három típusa van:

- Streamvégpontok: Ez lehet 30 másodperc
- Az élő csatornák: Minden perc
- Élő archiválás: Minden perc

**Streamvégpont**

Tulajdonság|Érték|Példák
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg Azure Table 2016-09-09T22:43:42.241Z
Typo|Typo|StreamingEndpoint
Name (Név)|Name (Név)|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Állomásnév|A végpont állomásneve|builddemoserver.origin.mediaservices.windows.net
StatusCode|Rögzíti a HTTP-állapot|200
Eredménykód|Eredmény kódja részletei|S_OK
RequestCount|Ilyenkor az összesítést a kérelmek száma összesen|3
BytesSent|Küldött bájtok összesített száma|2987358
Kiszolgáló késése|Kiszolgáló átlagos késése (tárolóval együtt)|129
E2ELatency|Átlagos végpontok közötti késés|250

**Élő csatorna**

Tulajdonság|Érték|Példák és megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyege az Azure-tábla 2016-09-09T22:43:42.241Z
Typo|Typo|Csatorna
Name (Név)|Name (Név)|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Írja be a videó vagy hang/szöveg nyomon követése|a videó vagy hang
TrackName|A track neve|a videó/audio_1
Átviteli sebesség|Sávszélességű nyomon követése|785000
CustomAttributes||   
IncomingBitrate|Tényleges bejövő sávszélességű|784548
OverlapCount|A betöltés átfedésben vannak|0
DiscontinuityCount|Nyomon követheti a kihagyást|0
LastTimestamp|Legutóbbi feldolgozott adatok időbélyege|1800488800
NonincreasingCount|Töredékek száma miatt nem növekvő elvetett időbélyeg|2
UnalignedKeyFrames|E fragment(s) (biztosít, minőségi) érkezett ahol kulcs nincsenek egyeztetve keretek |True (Igaz)
UnalignedPresentationTime|E érkezett fragment(s) (között a minőségi szintet és nyomon követi) bemutató idő nem igazodik ahol|True (Igaz)
UnexpectedBitrate|Igaz Ha számított/tényleges átviteli sebesség a hang/kép nyomon követheti az > 40 000/s és IncomingBitrate == vagy IncomingBitrate és actualBitrate különböznek egymástól, 50 %-kal 0 |True (Igaz)
Kifogástalan|IGAZ, ha <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> a rendszer minden 0|True (Igaz)<br/><br/>Kifogástalan állapotú összetett függvény, amely a hamis értéket ad vissza, ha az alábbi feltételek bármelyike tartsa van:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames igaz ==<br/>- UnalignedPresentationTime == True<br/>-UnexpectedBitrate igaz ==

**Élő archiválás**

Tulajdonság|Érték|Példák és megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyege az Azure-tábla 2016-09-09T22:43:42.241Z
Typo|Typo|Archívum
Name (Név)|Name (Név)|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
manifestName|Program URL-címe|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|A track neve|audio_1
TrackType|A szám típus|Hang/kép
Atribut CustomAttribute s|Hexadecimális karakterlánc, amely állapottípust azonos nevű másik nyomon követése és sávszélességű (többszörös kamera szög)|
Átviteli sebesség|Sávszélességű nyomon követése|785000
Kifogástalan|True, if FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False|Igaz (ezt a két értéket nem szerepelnek a mérőszám, de ezek meg adva a forrás esemény)<br/><br/>Kifogástalan állapotú összetett függvény, amely a hamis értéket ad vissza, ha az alábbi feltételek bármelyike tartsa van:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError igaz ==

## <a name="general-qa"></a>Általános a Q & A

### <a name="how-to-consume-metrics-data"></a>Hogyan metrikai adatok felhasználását?

Metrikák adatait a storage-fiókban az ügyfél Azure-beli táblák sorozataként tárolódik. Ezeket az adatokat a következő eszközökkel használhatók fel:

- AMS SDK
- A Microsoft Azure Storage Explorer (támogatja az exportálást, és vesszővel tagolt formátumú, és a feldolgozott Excel)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hogyan találhatja meg a átlagos sávszélesség-használatot?

Átlagos sávszélesség-használatot az az időtartam, BytesSent átlaga.

### <a name="how-to-define-streaming-unit-count"></a>Hogyan adhat meg a streamelési egységek számát?

A folyamatos átviteli egységek száma a szolgáltatás streamvégpontok elosztva a maximális átviteli sebesség egy streamvégponton, az a maximális átviteli sebesség adható meg. Egy streamvégponton használható maximális átviteli sebesség 160 MB/s.
Tegyük fel például, a maximális átviteli sebesség ügyfél szolgáltatásból 40 MB/s (maximális értéke BytesSent az időtartam alatt). Ezt követően a folyamatos átviteli egységek száma egyenlő (40 MB/s) * (8 Bit/bájt) /(160 Mbps) 2 folyamatos átviteli egység =.

### <a name="how-to-find-average-requestssecond"></a>Hogyan találhatja meg a átlagos kérelmek/másodperc?

Átlagos száma kérelmek/másodperc megkereséséhez számítási kérelmek (RequestCount) átlagos száma az időtartam alatt.

### <a name="how-to-define-channel-health"></a>Hogyan adhat meg a csatorna állapotát?

Csatorna állapota logikai függvény összetett lehet meghatározni, hogy legyen False (hamis) a következő feltételek bármelyikének tárolásához:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames igaz == 
- UnalignedPresentationTime igaz == 
- UnexpectedBitrate igaz ==


### <a name="how-to-detect-discontinuities"></a>Hogyan észleli a folytonosság megszakítását?

Folytonosság megszakítását észleléséhez, keresse meg az összes csatorna adatbevitelek ahol DiscontinuityCount > 0. A megfelelő ObservedTime időbélyege azt jelzi, hogy az időpontokat, amikor a folytonosság megszakítását történt.

### <a name="how-to-detect-timestamp-overlaps"></a>Időbélyeg észlelése átfedésben van?

Időbélyeg átfedések észleléséhez található összes csatorna adatbevitelek ahol OverlapCount > 0. A megfelelő ObservedTime időbélyege azt jelenti, hogy az időpontokat, amikor az időbélyeg átfedésben van történt.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hogyan találhatja meg a streamelési kérelemhibák és oka?

Streamelési kérelemhibák és okokból megkereséséhez található összes folyamatos átviteli végponton adatbevitelek, nem egyenlő S_OK ahol ResultCode. A megfelelő StatusCode a kérelem hiba okát jelzi.

### <a name="how-to-consume-data-with-external-tools"></a>Hogyan lehet az adatokat a külső eszközöket?

Telemetria adatok feldolgozása, illetve a következő eszközök formájában jelenik meg:

- PowerBI
- Application Insights
- Az Azure Monitor (korábbi nevén Shoebox)
- AMS Live Dashboard
- Az Azure Portal (függőben lévő kiadás)

### <a name="how-to-manage-data-retention"></a>Hogyan kezelheti az adatmegőrzés?

A telemetriai rendszer nem biztosít megőrzési adatkezelés vagy automatikus törlés régi rögzíti. Ebből kifolyólag kell kezelése és a storage-tábla manuális módszerrel törölje a régi rögzíti. A storage SDK for menete hivatkozhat.

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
