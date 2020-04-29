---
title: Azure Cosmos DB monitorozási adathivatkozás | Microsoft Docs
description: Naplózási és metrikai referenciák a Azure Cosmos DBból származó adatok figyeléséhez.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588722"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB-adatmonitorozási referencia
Ez a cikk referenciaként szolgál az Azure Cosmos DB teljesítményének és rendelkezésre állásának elemzéséhez gyűjtött naplózási és metrikaadatokra vonatkozóan. A Azure Cosmos DB figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg a [figyelés Cosmos db](monitor-cosmos-db.md) .


## <a name="resource-logs"></a>Erőforrásnaplók
A következő táblázat a Azure Monitor-naplókba vagy az Azure Storage-ba gyűjtött Azure Cosmos DB erőforrás-naplók tulajdonságait sorolja fel. Azure Monitor naplókban a rendszer a **AzureDiagnostics** táblában gyűjti össze a Microsoft **ResourceProvider** értékét *. DOCUMENTDB*. 

| Azure Storage-mező vagy-tulajdonság | Azure Monitor naplók tulajdonsága | Leírás |
| --- | --- | --- |
| **idő** | **TimeGenerated** | A művelet bekövetkeztekor megadott dátum és idő (UTC). |
| **resourceId** | **Erőforrás** | Az Azure Cosmos DB fiók, amely számára engedélyezve vannak a naplók.|
| **Kategória** | **Kategória** | Azure Cosmos DB naplók esetében a **DataPlaneRequests**, a **MongoRequests**, a **QueryRuntimeStatistics**, a **PartitionKeyStatistics**, a **PartitionKeyRUConsumption**, a **ControlPlaneRequests** a rendelkezésre álló naplózási típusok. |
| **operationName** | **OperationName** | A művelet neve. Ez az érték a következő műveletek bármelyike lehet: létrehozás, frissítés, olvasás, ReadFeed, törlés, csere, végrehajtás, SqlQuery, lekérdezés, JSQuery, Head, HeadFeed vagy Upsert.   |
| **Tulajdonságok** | n/a | A mező tartalmát az alábbi sorok írják le. |
| **Tevékenységazonosító** | **activityId_g** | A naplózott művelet egyedi GUID azonosítója. |
| **userAgent** | **userAgent_s** | Egy karakterlánc, amely megadja a kérést végző ügyfél-felhasználói ügynököt. A formátum {User Agent Name}/{Version}.|
| **requestResourceType** | **requestResourceType_s** | Az elérni kívánt erőforrás típusa. Az érték a következő erőforrástípusok bármelyike lehet: adatbázis, tároló, dokumentum, melléklet, felhasználó, engedély, StoredProcedure, trigger, UserDefinedFunction vagy ajánlat. |
| **statusCode** | **statusCode_s** | A művelet válaszának állapota. |
| **requestResourceId** | **ResourceId** | A kérelemre vonatkozó resourceId. Az érték databaseRid, collectionRid vagy documentRid is mutathat a végrehajtott művelettől függően.|
| **clientIpAddress** | **clientIpAddress_s** | Az ügyfél IP-címe. |
| **requestCharge** | **requestCharge_s** | A művelet által használt RUs száma |
| **collectionRid** | **collectionId_s** | A gyűjtemény egyedi azonosítója.|
| **időtartama** | **duration_s** | A művelet időtartama ezredmásodpercben. |
| **requestLength** | **requestLength_s** | A kérelem hossza (bájt). |
| **responseLength** | **responseLength_s** | A válasz hossza (bájt).|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Ez az érték nem üres, ha [erőforrás-tokeneket](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) használ a hitelesítéshez. Az érték a felhasználó erőforrás-AZONOSÍTÓJÁRA mutat. |

Az összes Azure Monitor naplózási kategória és a társított sémák hivatkozásainak listáját lásd: [Azure monitor naplók kategóriái és sémái](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Mérőszámok
Az alábbi táblázatok felsorolják az Azure CosmOS DB-hez összegyűjtött platform-metrikákat. A rendszer az összes mérőszámot a névtérben tárolja **Cosmos dB standard metrikákat**.

Az összes Azure Monitor támogatási metrikák (beleértve a CosmosDB) listáját lásd: [Azure monitor támogatott metrikák](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Kérelmek metrikái
            
|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa) |Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| TotalRequests (összes kérelem) | Darabszám (darabszám) | Benyújtott kérelmek száma| DatabaseName, CollectionName, régió, StatusCode| Összes | TotalRequests, http-2xx, http-3xx, http 400, HTTP 401, belső kiszolgálóhiba, szolgáltatás nem érhető el, szabályozott kérelmek, átlagos kérelmek másodpercenként | Az állapotkód, a tároló percenkénti részletességgel történő figyelésére használatos. Az átlagos kérelmek másodpercenkénti lekéréséhez használja a Count összesítést percek alatt, és ossza meg a 60-as számmal. |
| MetadataRequests (metaadat-kérelmek) |Darabszám (darabszám) | A metaadat-kérelmek száma. Azure Cosmos DB minden fiókhoz rendszermetaadat-tárolót tart fenn, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen. | DatabaseName, CollectionName, régió, StatusCode| Összes| |Metaadat-kérelmek miatti szabályozások figyelésére használatos.|
| MongoRequests (Mongo kérelmek) | Darabszám (darabszám) | A Mongo kérelmek száma | DatabaseName, CollectionName, régió, CommandName, ErrorCode| Összes |Mongo-lekérdezési kérelmek gyakorisága, a Mongo frissítési kérelmi aránya, a Mongo törlési kérelmi aránya, a Mongo beszúrási kérelmi aránya, a Mongo száma kérelmek aránya| Mongo-kérési hibák figyelésére használatos. |

#### <a name="request-unit-metrics"></a>Adategység metrikáinak kérése

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo-kérelem díja) | Darabszám (összesen) |Felhasznált Mongo-kérelmek egységei| DatabaseName, CollectionName, régió, CommandName, ErrorCode| Összes |Mongo-lekérdezési kérelem díja, Mongo frissítési kérelmének díja, Mongo törlési kérelmének díja, Mongo-kérelem díja, Mongo száma kérelmek díja| Egy percen belül figyeli a Mongo-erőforrást.|
| TotalRequestUnits (összes kérelem egysége)| Darabszám (összesen) | Felhasználható kérési egységek| DatabaseName, CollectionName, régió, StatusCode |Összes| TotalRequestUnits| A teljes RU-használat figyelésére használható percenkénti részletességgel. Ha másodpercenként átlagosan felhasznált RU-t szeretné lekérni, használja a teljes összesítést percenként, és ossza meg a 60-as számmal.|
| ProvisionedThroughput (kiépített átviteli sebesség)| Darabszám (maximum) |Kiépített átviteli sebesség a tároló részletessége szerint| DatabaseName, ContainerName| 5 M| | Tárolón kiépített átviteli sebesség figyelésére szolgál.|

#### <a name="storage-metrics"></a>Tárolási metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (rendelkezésre álló tár) |Bájtok (összesen) | Régiónként 5 perces részletességgel jelentett teljes rendelkezésre álló tárterület| DatabaseName, CollectionName, régió| 5 M| Rendelkezésre álló tár| A rendelkezésre álló tárolókapacitás (csak a rögzített tárolási gyűjtemények esetében érvényes) figyeléséhez használatos minimális részletességnek 5 percnek kell lennie.| 
| DataUsage (adathasználat) |Bájtok (összesen) |Régiónként 5 perces részletességgel jelentett teljes adatfelhasználás| DatabaseName, CollectionName, régió| 5 M |Adatméret | A tároló és régió teljes adatfelhasználásának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|
| IndexUsage (használat indexelése) | Bájtok (összesen) |Az összes indexelési használat összesen 5 perces részletességgel számolt be régiónként| DatabaseName, CollectionName, régió| 5 M| Index mérete| A tároló és régió teljes adatfelhasználásának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie. |
| DocumentQuota (dokumentum kvótája) | Bájtok (összesen) | Régiónként 5 perces részletességgel jelentett teljes tárolási kvóta.| DatabaseName, CollectionName, régió| 5 M |Tárkapacitás| A tároló és régió teljes kvótájának figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|
| DocumentCount (dokumentumok száma) | Darabszám (összesen) |Régiónként 5 perces részletességgel jelentett dokumentumok száma összesen| DatabaseName, CollectionName, régió| 5 M |Dokumentumok száma|A dokumentumok számának a tárolóban és régióban való figyelésére szolgál, a minimális részletességnek 5 percnek kell lennie.|

#### <a name="latency-metrics"></a>Késési metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Használat |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikációs késés)| Ezredmásodperc (minimum, maximum, átlag) | P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz| SourceRegion, TargetRegion| Összes | Egy földrajzilag replikált fiók két régiója közötti P99-replikációs késés figyelésére használatos. |
| Kiszolgálóoldali késés| Ezredmásodperc (átlagos) | A kiszolgáló által a kérelem feldolgozásához szükséges idő. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, régió | Összes | A kérés késésének figyelésére szolgál a Azure Cosmos DB kiszolgálón. |



#### <a name="availability-metrics"></a>Rendelkezésre állási metrikák

|Metrika (metrika megjelenítendő neve) |Egység (aggregáció típusa)|Leírás| Időbeli részletesség| Örökölt metrika leképezése | Használat |
|---|---|---|---| ---| ---|
| ServiceAvailability (szolgáltatás rendelkezésre állása)| Százalék (minimum, maximum) | A fiók egy órás részletességgel kéri a rendelkezésre állást| 1H | Szolgáltatás rendelkezésre állása | Az átadott kérelmek százalékos arányát jelöli. A rendszer hibát okozó kérést küld, ha az állapotkód 410, 500 vagy 503, amely a fiók rendelkezésre állásának figyelésére használatos az óránkénti részletességgel. |


#### <a name="cassandra-api-metrics"></a>Cassandra API metrikák

|Metrika (metrika megjelenítendő neve)|Egység (aggregáció típusa)|Leírás|Dimenziók| Időbeli részletesség| Használat |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-kérelmek) | Darabszám (darabszám) | Cassandra API kérelmek száma| DatabaseName, CollectionName, ErrorCode, régió, OperationType, ResourceType| Összes| A Cassandra-kérések figyelésére szolgál percenkénti részletességgel. Az átlagos kérelmek másodpercenkénti lekéréséhez használja a Count összesítést percek alatt, és ossza meg a 60-as számmal.|
| CassandraRequestCharges (Cassandra-kérelmek díjai) | Darabszám (Sum, min, Max, AVG) | Cassandra API kérelmek által felhasznált kérési egységek| DatabaseName, CollectionName, régió, OperationType, ResourceType| Összes| Egy Cassandra API fiók által percenként használt RUs figyelésére használatos.|
| CassandraConnectionClosures (Cassandra-kapcsolatok bezárása) |Darabszám (darabszám) |Lezárt Cassandra-kapcsolatok száma| ClosureReason, régió| Összes | Az ügyfelek és a Azure Cosmos DB Cassandra API közötti kapcsolat figyelésére használatos.|

## <a name="see-also"></a>Lásd még:

- A figyelési Azure Cosmos DB leírását a [figyelés Azure Cosmos db](monitor-cosmos-db.md) című részben tekintheti meg.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/insights/monitor-azure-resource.md) .
