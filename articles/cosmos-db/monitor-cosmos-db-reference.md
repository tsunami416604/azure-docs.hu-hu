---
title: Azure Cosmos DB figyelési adatok hivatkozása | Microsoft dokumentumok
description: Napló és metrikák hivatkozás az Azure Cosmos DB-ből származó adatok figyeléséhez.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588722"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB-adatmonitorozási referencia
Ez a cikk referenciaként szolgál az Azure Cosmos DB teljesítményének és rendelkezésre állásának elemzéséhez gyűjtött naplózási és metrikaadatokra vonatkozóan. Tekintse meg [a Cosmos DB figyelése](monitor-cosmos-db.md) az Azure Cosmos DB figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért.


## <a name="resource-logs"></a>Erőforrásnaplók
Az alábbi táblázat felsorolja az Azure Cosmos DB erőforrásnaplók tulajdonságait, amikor az Okat az Azure Monitor naplók vagy az Azure Storage gyűjti. Az Azure Monitor naplók, azok az **AzureDiagnostics** táblában a **MICROSOFT ResourceProvider** értékkel gyűjtik *őket. DOCUMENTDB*. 

| Azure Storage mező vagy tulajdonság | Az Azure Monitor naplók tulajdonsága | Leírás |
| --- | --- | --- |
| **Idő** | **TimeGenerated** | A művelet bekövetkezésének dátuma és időpontja (UTC). |
| **resourceId** | **Erőforrás** | Az Azure Cosmos DB-fiók, amelyhez a naplók engedélyezve vannak.|
| **Kategória** | **Kategória** | Az Azure Cosmos DB-naplók, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** a rendelkezésre álló naplótípusok. |
| **operationName** | **Műveletneve** | A művelet neve. Ez az érték a következő műveletek bármelyike lehet: Létrehozás, Frissítés, Olvasás, Olvasás, Törlés, Csere, Végrehajtás, SqlQuery, Query, JSQuery, Head, HeadFeed vagy Upsert.   |
| **Tulajdonságok** | n/a | Ennek a mezőnek a tartalmát a következő sorok írják le. |
| **activityId** | **activityId_g** | A naplózott művelet egyedi GUID azonosítója. |
| **Useragent** | **userAgent_s** | A kérést végző ügyfél-felhasználói ügynököt megadva karakterlánc. A formátum : {user agent name}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Az elért erőforrás típusa. Ez az érték a következő erőforrástípusok bármelyike lehet: Adatbázis, Tároló, Dokumentum, Melléklet, Felhasználó, Engedély, StoredProcedure, Trigger, UserDefinedFunction vagy Offer. |
| **statusCode** | **statusCode_s** | A művelet válaszállapota. |
| **requestResourceId azonosító** | **Erőforrásazonosító** | A kérelemhez tartozó erőforrásazonosító. Az érték a végrehajtott művelettől függően a databaseRid, collectionRid vagy documentRid elemre mutathat.|
| **clientIpAddress cím** | **clientIpAddress_s** | Az ügyfél IP-címe. |
| **requestCharge (töltés)** | **requestCharge_s** | A művelet által használt RT-k száma |
| **gyűjteményRid** | **collectionId_s** | A gyűjtemény egyedi azonosítója.|
| **Időtartam** | **duration_s** | A művelet időtartama ezredmásodpercben. |
| **requestLength (kéréshossza)** | **requestLength_s** | A kérelem hossza bájtban. |
| **responseLength (válaszhossza)** | **responseLength_s** | A válasz hossza bájtban.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ez az érték nem üres, ha [erőforrás-tokeneket](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) használ a hitelesítéshez. Az érték a felhasználó erőforrás-azonosítójára mutat. |

Az Azure Monitor naplójának összes kategóriáját és a társított sémákra mutató hivatkozásokat az [Azure Monitor naplók kategóriáinak és sémáinak](../azure-monitor/platform/diagnostic-logs-schema.md)listája tartalmazza. 

## <a name="metrics"></a>Mérőszámok
Az alábbi táblázatok az Azure CosmOS DB-hez gyűjtött platformmutatókat sorolják fel. Az összes metrika a **Cosmos DB standard metrikák**névtérben tárolódik.

Az Azure Monitor támogatási metrikáinak listáját (beleértve a CosmosDB-t is) az [Azure Monitor által támogatott metrikák](../azure-monitor/platform/metrics-supported.md)című témakörben található. 

#### <a name="request-metrics"></a>Mérőszámok kérése
            
|Metrikus (metrikus megjelenítendő név)|Egység (összesítés típusa) |Leírás|Dimenziók| Idő részletessége| Örökölt metrikaleképezés | Használat |
|---|---|---|---| ---| ---| ---|
| Összes kérelem (összes kérelem) | Darabszám (darabszám) | A benyújtott kérelmek száma| Adatbázisnév, Gyűjteménynév, Régió, Állapotkód| Összes | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Belső kiszolgáló hiba, Szolgáltatás nem érhető el, szabályozott kérelmek, átlagos kérelmek másodpercenként | A kérelmek figyelésére állapotkód, tároló egy perc granularitás. Átlagos kérelmek másodpercenkénti lekéréséhez használja a Count aggregáció percben, és 60-mal elosztása. |
| Metaadatkérések (metaadat-kérelmek) |Darabszám (darabszám) | Metaadat-kérelmek száma. Az Azure Cosmos DB rendszermetaadat-tárolót tart fenn minden egyes fiókhoz, amely lehetővé teszi a gyűjtemények, adatbázisok stb. | Adatbázisnév, Gyűjteménynév, Régió, Állapotkód| Összes| |A metaadat-kérelmek miatti szabályozások figyelésére szolgál.|
| MongoRequests (Mongo kérések) | Darabszám (darabszám) | Mongo kérelmek száma | Adatbázisnév, Gyűjteménynév, Terület, Parancsnév, Hibakód| Összes |Mongo lekérdezéskérelem aránya, Mongo frissítési kérelmek aránya, Mongo törlési kérelem aránya, Mongo insert request rate, Mongo count request rate| A Mongo kérési hibáinak, a parancstípusonkénti használatnak a figyelésére szolgál. |

#### <a name="request-unit-metrics"></a>Kérelemegység-mérőszámok

|Metrikus (metrikus megjelenítendő név)|Egység (összesítés típusa)|Leírás|Dimenziók| Idő részletessége| Örökölt metrikaleképezés | Használat |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo kérelem díj) | Darabszám (összesen) |Mongo kérelem egységek elfogyasztott| Adatbázisnév, Gyűjteménynév, Terület, Parancsnév, Hibakód| Összes |Mongo lekérdezés kérelem díj, Mongo update request charge, Mongo delete request charge, Mongo Insert Request Charge, Mongo count request charge| Mongo erőforrás-rú-k figyelésére egy perc alatt használható.|
| TotalRequestUnits (összes kérelemegység)| Darabszám (összesen) | Felhasznált egységek igénylése| Adatbázisnév, Gyűjteménynév, Régió, Állapotkód |Összes| TotalRequestUnits| A teljes RU-használat figyelésére szolgál egy perces részletességgel. A másodpercenként felhasznált átlagos RU-k lehívásához használja a Teljes összesítés talgon percben, és ossza el 60-tal.|
| Kiépítettátviteli (kiépített átviteli érték)| Darabszám (maximum) |Kiosztott átviteli hang a tároló részletességén| Adatbázisneve, tárolóneve| 5 M| | A kiépített átviteli és átviteli rendszer figyelésére szolgál tárolónként.|

#### <a name="storage-metrics"></a>Tárolási mutatók

|Metrikus (metrikus megjelenítendő név)|Egység (összesítés típusa)|Leírás|Dimenziók| Idő részletessége| Örökölt metrikaleképezés | Használat |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (elérhető tárhely) |Bájt (összesen) | A rendelkezésre álló összes tárterület régiónként 5 perces részletességgel| Adatbázisneve, Gyűjteményneve, Régió| 5 M| Rendelkezésre álló tár| A rendelkezésre álló tárolókapacitás figyelésére szolgál (csak a helyhez kötött tárológyűjtemények esetében alkalmazható) A minimális részletesség 5 perc.| 
| Adathasználat (adathasználat) |Bájt (összesen) |Az összes adathasználat régiónként 5 perces részletességgel| Adatbázisneve, Gyűjteményneve, Régió| 5 M |Adatméret | A tárolóban és régióban a teljes adathasználat figyelésére használt minimális részletesség 5 percnek kell lennie.|
| Indexhasználat (indexhasználat) | Bájt (összesen) |Régiónként 5 perces részletességgel jelentett teljes indexhasználat| Adatbázisneve, Gyűjteményneve, Régió| 5 M| Index mérete| A tárolóban és régióban a teljes adathasználat figyelésére használt minimális részletesség 5 percnek kell lennie. |
| DocumentQuota (dokumentumkvóta) | Bájt (összesen) | A teljes tárolási kvóta régiónként 5 perces részletességgel jelentve.| Adatbázisneve, Gyűjteményneve, Régió| 5 M |Tárkapacitás| A tárolóban és a régióban a teljes kvóta figyelésére szolgál, a minimális részletesség nek 5 percnek kell lennie.|
| DocumentCount (bizonylatszám) | Darabszám (összesen) |Az 5 perces részletességgel jelentett dokumentumok száma régiónként| Adatbázisneve, Gyűjteményneve, Régió| 5 M |Dokumentumok száma|A dokumentumok számának figyelésére szolgál a tárolóban és a régióban, a minimális részletesség nek 5 percnek kell lennie.|

#### <a name="latency-metrics"></a>Késési metrikák

|Metrikus (metrikus megjelenítendő név)|Egység (összesítés típusa)|Leírás|Dimenziók| Idő részletessége| Használat |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikációkés)| Ezredmásodperc (minimum, Maximum, Átlagos) | P99 replikációs késés a földrajzi alapú fiók forrás- és célrégiói között| Forrásrégió, célrégió| Összes | A P99 replikációs késésének figyelésére szolgál bármely két régió között egy georeplikált fiók esetében. |
| Kiszolgálóoldali késés| Ezredmásodperc (átlag) | A kiszolgáló által a kérelem feldolgozásához szükséges idő. | Gyűjteménynév, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Régió | Összes | A kérelem késésének figyelésére az Azure Cosmos DB-kiszolgálón. |



#### <a name="availability-metrics"></a>Rendelkezésre állási mutatók

|Metrikus (metrikus megjelenítendő név) |Egység (összesítés típusa)|Leírás| Idő részletessége| Örökölt metrikaleképezés | Használat |
|---|---|---|---| ---| ---|
| Szolgáltatáselérhetősége (a szolgáltatás elérhetősége)| Százalék (minimum, Maximum) | A fiók kérelmek elérhetősége egy órás részletességgel| 1H | A szolgáltatás elérhetősége | Az összes átadott kérelem százalékos arányát jelöli. A rendszerhiba miatt sikertelennek minősülő kérelem, ha az állapotkód 410, 500 vagy 503, amely a fiók elérhetőségének figyelésére szolgál az óra részletessége. |


#### <a name="cassandra-api-metrics"></a>Cassandra API-metrikák

|Metrikus (metrikus megjelenítendő név)|Egység (összesítés típusa)|Leírás|Dimenziók| Idő részletessége| Használat |
|---|---|---|---| ---| ---|
| Cassandrarequests (Cassandra kérések) | Darabszám (darabszám) | Cassandra API-kérelmek száma| Adatbázisnév, Gyűjteménynév, Hibakód, Terület, OperationType, Erőforrástípus| Összes| Cassandra kéréseit egy szemcsésebben figyelték. Átlagos kérelmek másodpercenkénti lekéréséhez használja a Count aggregáció percben, és 60-mal elosztása.|
| CassandraRequestcharges (Cassandra terhelések kérése) | Számlálás (összeg, min, max, átlag) | Cassandra API-kérések által felhasznált kérelemegységek| Adatbázisnév, Gyűjteménynév, Terület, Művelettípus, Erőforrástípus| Összes| Cassandra API-fiók által percenként használt ritkok figyelésére szolgál.|
| CassandraConnectionClosures (Cassandra kapcsolatlezárások) |Darabszám (darabszám) |Lezárt Cassandra kapcsolatok száma| ClosureReason, Régió| Összes | Az ügyfelek és az Azure Cosmos DB Cassandra API közötti kapcsolat figyelésére szolgál.|

## <a name="see-also"></a>Lásd még:

- [Az Azure Cosmos DB figyelése](monitor-cosmos-db.md) az Azure Cosmos DB figyelése.
- Az [Azure-erőforrások figyelése az Azure Monitorsegítségével](../azure-monitor/insights/monitor-azure-resource.md) az Azure-erőforrások figyelésével kapcsolatos részletekért tekintse meg.
