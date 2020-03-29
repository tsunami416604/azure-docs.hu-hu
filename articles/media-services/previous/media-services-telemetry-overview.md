---
title: Azure Media Services telemetria | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a Microsoft Azure Media Services telemetriai adatairól.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: e2cbb36158722a47518f575b391340b5e25bd908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895778"
---
# <a name="azure-media-services-telemetry"></a>Az Azure Media Services telemetriai adatai  


> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services (AMS) lehetővé teszi a szolgáltatások telemetriai/metrika-adatok elérését. Az AMS jelenlegi verziója lehetővé teszi az élő **csatorna, a** **StreamingEndpoint**és az élő **archív** entitások telemetriai adatainak gyűjtését. 

Telemetriai egy tárolási tábla egy Azure Storage-fiókban megadott megadott (általában az AMS-fiókhoz társított tárfiókot használja). 

A telemetriai rendszer nem kezeli az adatmegőrzést. A régi telemetriai adatokat a tárolótáblák törlésével távolíthatja el.

Ez a témakör ismerteti, hogyan konfigurálhatja és használja fel az AMS telemetriai adatokat.

## <a name="configuring-telemetry"></a>Telemetria konfigurálása

Telemetriai adatokat konfigurálhat egy összetevő szintű részletesség. Két részletszint van "Normál" és "Részletes". Jelenleg mindkét szint ugyanazt az információt adja vissza. A "Normál" használata ajánlott. 

Az alábbi témakörök bemutatják, hogyan engedélyezheti a telemetriai adatokat:

[Telemetria engedélyezése a .NET segítségével](media-services-dotnet-telemetry.md) 

[Telemetria engedélyezése REST-el](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Telemetriai adatok fogyasztása

Telemetriai adatok egy Azure storage-ba a storage-fiókban, amely a Media Services-fiók konfigurálásakor megadott értékadatok at. Ez a szakasz a metrikák tárolási táblázatait ismerteti.

A telemetriai adatokat az alábbi módokon használhatja fel:

- Adatok olvasása közvetlenül az Azure Table Storage -ból (pl. a storage SDK használatával). A telemetriai tárolási táblák leírását lásd **a fogyasztása telemetriai adatok** [ebben](https://msdn.microsoft.com/library/mt742089.aspx) a témakörben.

Vagy

- Használja a Media Services .NET SDK támogatását a tárolási adatok olvasásához, [a](media-services-dotnet-telemetry.md) jelen témakörben leírtak szerint. 


Az alábbiakban ismertetett telemetriai séma úgy van kialakítva, hogy az Azure Table Storage keretein belül jó teljesítményt nyújtson:

- Az adatokat fiókazonosító és szolgáltatásazonosító particionálja, hogy az egyes szolgáltatások telemetriái egymástól függetlenül lekérdezhetők legyenek.
- A partíciók tartalmazzák azt a dátumot, amely ésszerű felső határt ad a partíció méretének.
- A sorkulcsok fordított idősorrendben vannak ahhoz, hogy lehetővé tegyék a legutóbbi telemetriai elemek lekérdezését egy adott szolgáltatáshoz.

Ez lehetővé teszi, hogy a gyakori lekérdezések közül sok hatékony legyen:

- Párhuzamos, független adatok letöltése külön szolgáltatásokhoz.
- Egy adott szolgáltatás összes adatának beolvasása egy dátumtartományban.
- Szolgáltatás legfrissebb adatainak beolvasása.

### <a name="telemetry-table-storage-output-schema"></a>Telemetriai tábla tárolási kimeneti sémája

Telemetriai adatok at ad egy táblázatban, "TelemetryMetrics20160321" ahol a "20160321" a létrehozott tábla dátuma. Telemetriai rendszer létrehoz egy külön táblát minden új nap alapján 00:00 UTC. A tábla ismétlődő értékek tárolására szolgál, például a bitráta egy adott időablakon belül, az elküldött bájtok stb. 

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|{fiókazonosító}_{entitásazonosítója}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>A fiókazonosító szerepel a partíciós kulcsban, hogy egyszerűsítse a munkafolyamatokat, ahol több Media Services-fiók ír ugyanarra a tárfiókra.
RowKey|{másodperc éjfélig}_{véletlenszerű érték}|01688_00199<br/><br/>A sorkulcs a másodpercek számával kezdődik éjfélig, hogy a legfelső n stílusú lekérdezések engedélyezve lehessen a partíción belül. További információt [ebben a cikkben](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) talál. 
Időbélyeg|Dátum és idő|Automatikus időbélyeg az Azure-táblából 2016-09-09T22:43:42.241Z
Típus|A telemetriai adatokat biztosító entitás típusa|Csatorna/StreamingEndpoint/Archiválás<br/><br/>Az eseménytípusa csak karakterlánc-érték.
Név|A telemetriai esemény neve|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime (Megfigyeltidő)|A telemetriai esemény bekövetkezésének időpontja (UTC)|2016-09-09T22:42:36.924Z<br/><br/>A megfigyelt időt a telemetriai adatokat küldő entitás (például egy csatorna) biztosítja. Az összetevők között időszinkronizálási problémák lehetnek, így ez az érték hozzávetőleges
Szervizazonosító|{szolgáltatás azonosítója}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitásspecifikus tulajdonságok|Az esemény meghatározása szerint|StreamName: stream1, Bitráta 10123, ...<br/><br/>A fennmaradó tulajdonságok a megadott eseménytípushoz vannak definiálva. Az Azure Table tartalma kulcsérték-párok.  (azaz a tábla különböző sorai különböző tulajdonságkészleteket tartalmaznak).

### <a name="entity-specific-schema"></a>Entitásspecifikus séma

Az entitásspecifikus telemetrikus adatbejegyzéseknek három típusa van, amelyek mindegyike a következő gyakorisággal van leküldéses:

- Végpontok streamelése: 30 másodpercenként
- Élő csatornák: Percenként
- Élő archívum: Percenként

**Streamvégpont**

Tulajdonság|Érték|Példák
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg az Azure Table 2016-09-09T22:43:42.241Z táblából
Típus|Típus|Streamelésvégpont
Név|Név|StreamingEndpointRequestLog
ObservedTime (Megfigyeltidő)|ObservedTime (Megfigyeltidő)|2016-09-09T22:42:36.924Z
Szervizazonosító|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|A végpont állomásneve|builddemoserver.origin.mediaservices.windows.net
StatusCode (Állapotkód)|Rekordok HTTP-állapota|200
ResultCode (Eredménykód)|Eredménykód részletei|S_OK
Kérésszáma|Teljes kérelem az összesítésben|3
Küldött bájt|Elküldött összesített bájtok|2987358
Kiszolgálókkajának átkése|A kiszolgáló átlagos késése (a tárolással együtt)|129
E2ELatency (E2ELatency)|Átlagos végpontok késése|250

**Élő csatorna**

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg az Azure-táblából 2016-09-09T22:43:42.241Z
Típus|Típus|Csatorna
Név|Név|CsatornaSzívverés
ObservedTime (Megfigyeltidő)|ObservedTime (Megfigyeltidő)|2016-09-09T22:42:36.924Z
Szervizazonosító|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType típus|A műsorszám típusa videó/hang/szöveg|videó/hang
Sávneve|A pálya neve|videó/audio_1
Bitráta|Sebesség követése|785000
Egyéni attribútumok||   
Bejövő bitráta|Tényleges bejövő bitráta|784548
Átfedések száma|Átfedés a lenyelésben|0
Megszakítási szám|A vágány folytonosságának hiánya|0
LastTimestamp (LastTimestamp)|Utoljára betöltött adatok időbélyege|1800488800
Nem növelt szám|A nem növekvő időbélyeg miatt eldobott töredékek száma|2
Nem igazított kulcskeretek|Azt jelzi, hogy kapunk-e olyan töredéke(ket) (minőségi szintek között), amelyekkulcs-keretek nincsenek összehangolva |True (Igaz)
Nem igazított PresentationTime|Azt jelzi, hogy kapunk-e olyan töredéke(ket) (minőségi szinteken/sávokon keresztül), ahol a megjelenítési idő nincs összehangolva|True (Igaz)
Váratlan bitráta|Igaz, ha számított / tényleges bitráta audio / video sáv > 40.000 bps és IncomingBitrate == 0 VAGY IncomingBitrate és actualBitrate különböznek 50% |True (Igaz)
Kifogástalan|Igaz, ha <br/>átfedésszám, <br/>Megszakítások száma, <br/>Nem növekvő szám, <br/>Nem igazított kulcskeretek, <br/>Nincs igazított PresentationTime, <br/>Váratlan bitráta<br/> mind 0|True (Igaz)<br/><br/>Kifogástalan egy összetett függvény, amely hamis értéket ad vissza, ha az alábbi feltételek bármelyike fennáll:<br/><br/>- Átfedésszáma > 0<br/>- Megszakításiszám > 0<br/>- NonincreasingCount > 0<br/>- Nem igazított kulcsképek == Igaz<br/>- UnalignedPresentationTime == Igaz<br/>- UnexpectedBitrate == Igaz

**Élő archívum**

Tulajdonság|Érték|Példák/megjegyzések
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Időbélyeg|Időbélyeg|Automatikus időbélyeg az Azure-táblából 2016-09-09T22:43:42.241Z
Típus|Típus|Archívum
Név|Név|ArchívumSzívverés
ObservedTime (Megfigyeltidő)|ObservedTime (Megfigyeltidő)|2016-09-09T22:42:36.924Z
Szervizazonosító|Szolgáltatás azonosítója|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Jegyzékfájl neve|A program url-címe|eszköz-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism.ism
Sávneve|A pálya neve|audio_1
TrackType típus|A pálya típusa|Hang/videó
CustomAttribute (Egyéni attribútum)|Hexasztúr, amely különbséget tesz a különböző sáv azonos nevű és bitráta (több kameraszög)|
Bitráta|Sebesség követése|785000
Kifogástalan|Igaz, ha fragmentDiscardedCount == 0 && ArchiveAcquisitionError == Hamis|Igaz (ez a két érték nincs jelen a metrikában, de jelen vannak a forráseseményben)<br/><br/>Kifogástalan egy összetett függvény, amely hamis értéket ad vissza, ha az alábbi feltételek bármelyike fennáll:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == Igaz

## <a name="general-qa"></a>Q&A.

### <a name="how-to-consume-metrics-data"></a>Hogyan kell felhasználni a metrikák adatait?

Metrikák adatok az ügyfél tárfiókban az Azure-táblák sorozataként tárolódik. Ezek az adatok a következő eszközökkel használhatók fel:

- AMS SDK
- Microsoft Azure Storage Explorer (támogatja az exportálást vesszővel tagolt értékformátumba és dolgozza fel az Excelben)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hogyan lehet megtalálni az átlagos sávszélesség-felhasználás?

Az átlagos sávszélesség-felhasználás a küldött bájt átlaga egy adott időtartam alatt.

### <a name="how-to-define-streaming-unit-count"></a>Hogyan definiálható a streamelési egységek száma?

A streamelési egység száma definiálható a szolgáltatás streamelési végpontjaiból származó csúcsátviteli teljesítményként, osztva egy streamelési végpont csúcsátviteli értékével. Egy streamelési végpont maximális használható átviteli sebesség160 Mb/s.
Tegyük fel például, hogy egy ügyfél szolgáltatásának csúcsátviteli sebessége 40 Mb/s (a Bájt küldése egy ideig küldött maximális értéke). Ezután a streamelési egységek száma egyenlő (40 Mb/s)*(8 bit/bájt)/(160 Mbps) = 2 streamelési egység.

### <a name="how-to-find-average-requestssecond"></a>Hogyan lehet megtalálni az átlagos kérelmek / másodperc?

A kérelmek/másodperc átlagos számának megkereséséhez számítsa ki a kérelmek átlagos számát (RequestCount) egy adott időtartamalatt.

### <a name="how-to-define-channel-health"></a>Hogyan definiálható a csatorna állapota?

A csatorna állapota összetett logikai függvényként definiálható, így hamis, ha az alábbi feltételek bármelyike fennáll:

- Átfedésszáma > 0
- Megszakításokszáma > 0
- NonincreasingCount > 0
- Nem igazított kulcsképek == Igaz 
- Nem igazított presentationtime == Igaz 
- Váratlan bitráta == Igaz


### <a name="how-to-detect-discontinuities"></a>Hogyan lehet észlelni a diszkontinuitásokat?

A megszakítások észleléséhez keresse meg az összes olyan csatornaadat-bejegyzést, ahol a Megszakítási0 >. A megfelelő ObservedTime időbélyeg azt jelzi, hogy a megszakítások mikor következtek be.

### <a name="how-to-detect-timestamp-overlaps"></a>Hogyan lehet észlelni az időbélyeg átfedéseit?

Az időbélyeg-átfedések észleléséhez keresse meg az összes olyan csatornaadat-bejegyzést, ahol az OverlapCount > 0. A megfelelő ObservedTime időbélyeg azt jelzi, hogy az időbélyeg átfedések mikor történtek.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hogyan lehet megtalálni a streamelési kérelmek hibáit és okait?

A streamelési kérelmek hibáinak és okainak megkereséséhez keresse meg az összes streamelési végpont-adatbejegyzést, ahol a ResultCode nem egyenlő S_OK. A megfelelő StatusCode mező a kérelem sikertelenssének okát jelzi.

### <a name="how-to-consume-data-with-external-tools"></a>Hogyan kell felhasználni az adatokat külső eszközökkel?

A telemetrikus adatok a következő eszközökkel dolgozhatók fel és jeleníthetők meg:

- PowerBI
- Application Insights
- Azure Monitor (korábban Shoebox)
- AMS Élő irányítópult
- Azure Portal (függőben lévő kiadás)

### <a name="how-to-manage-data-retention"></a>Hogyan lehet kezelni az adatmegőrzést?

A telemetriai rendszer nem biztosít adatmegőrzési kezelést vagy a régi rekordok automatikus törlését. Így a régi rekordokat manuálisan kell kezelnie és törölnie a tárolótáblából. A tárolási SDK-t a teendőkért.

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
