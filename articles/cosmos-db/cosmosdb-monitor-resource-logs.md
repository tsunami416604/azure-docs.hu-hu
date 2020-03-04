---
title: Azure Cosmos DB-adatellenőrzés az Azure diagnosztikai beállításainak használatával
description: Ismerje meg, hogyan használható az Azure diagnosztikai beállításai a Azure Cosmos DB tárolt adatmennyiség és rendelkezésre állás figyelésére
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 184fc65dae57292243be9abdca71a129512b3d0b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252049"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure Cosmos DB adatai figyelése az Azure diagnosztikai beállításainak használatával

Az Azure diagnosztikai beállításai az erőforrás-naplók összegyűjtésére szolgálnak. Az Azure-erőforrás-naplókat egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. Ezeket a naplókat a rendszer kérelem szerint rögzíti, és a továbbiakban "adatsíkok naplóiként" is emlegetik. Néhány példa az adatsík-műveletekre: delete, INSERT és readFeed. Ezek a naplók a tartalom erőforrás típusa szerint változó.

A platform metrikáit és a tevékenységek naplóit automatikusan gyűjti a rendszer, míg az erőforrás-naplók összegyűjtéséhez és a Azure Monitoron kívüli továbbításához létre kell hoznia egy diagnosztikai beállítást. Az Azure Cosmos-fiókok diagnosztikai beállításainak bekapcsolásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. Navigáljon az Azure Cosmos-fiókjához. Nyissa meg a **diagnosztikai beállítások** ablaktáblát, majd kattintson a **diagnosztikai beállítás hozzáadása** lehetőségre.

1. A **diagnosztikai beállítások** panelen töltse ki az űrlapot a következő részletekkel: 

    * **Név**: adjon meg egy nevet a létrehozandó naplók számára.

    * A naplókat tárolhatja **archiválásra egy Storage-fiókba**, **streamet egy Event hub** -ba, vagy **elküldheti log Analytics**

1. Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön. Az Azure Cosmos DB által támogatott naplók kategóriái a következőkkel együtt vannak felsorolva:

 * **DataPlaneRequests**: válassza ezt a lehetőséget, ha a háttérbeli kérelmeket naplózni szeretné az összes API-ra, beleértve az SQL-, Graph-, MongoDB-, Cassandra-és Table API-fiókokat Azure Cosmos db. A fontos tulajdonságok a következők: `Requestcharge`, `statusCode`, `clientIPaddress`és `partitionID`.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: ezzel a beállítással naplózhatja a felhasználó által kezdeményezett kérelmeket az előtérből a MongoDB API-ra irányuló Azure Cosmos db kérések kiszolgálására, ez a napló típusa más API-fiókok esetében nem érhető el. A MongoDB-kérelmek megjelennek a MongoRequests és a DataPlaneRequests is. A fontos tulajdonságok a következők: `Requestcharge`, `opCode`.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: válassza ezt a lehetőséget a végrehajtott lekérdezési szöveg naplózásához. Ez a naplózási típus csak az SQL API-fiókok esetében érhető el.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: válassza ezt a lehetőséget a partíciós kulcsok statisztikáinak naplózásához. Ez jelenleg a partíciós kulcsok tárolási méretével (KB) van ábrázolva. Tekintse meg a jelen cikk [Azure diagnosztikai lekérdezések használatával kapcsolatos hibaelhárítási problémáit](#diagnostic-queries) . Például a "PartitionKeyStatistics" kifejezést használó lekérdezések. A rendszer kibocsátja a naplót a legtöbb adattárolást elfoglaló első három partíciós kulcson. Ez a napló olyan adatmennyiséget tartalmaz, mint például az előfizetés azonosítója, a régió neve, az adatbázis neve, a gyűjtemény neve, a partíció kulcsa, valamint a tárterület mérete KB-ban.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Ez a napló a partíciós kulcsok aggregált másodpercenkénti számát jelenti. A Azure Cosmos DB jelenleg csak az SQL API-fiókok, valamint a pont írási/olvasási és tárolt eljárási műveleteinek partíciós kulcsait jelenti. más API-k és műveleti típusok nem támogatottak. Más API-k esetén a diagnosztikai napló tábla partíciós kulcs oszlopa üres lesz. Ez a napló olyan adattípusokat tartalmaz, mint például az előfizetés azonosítója, a régió neve, az adatbázis neve, a gyűjtemény neve, a partíció kulcsa, a művelet típusa és a kérelem díja. Tekintse meg a jelen cikk [Azure diagnosztikai lekérdezések használatával kapcsolatos hibaelhárítási problémáit](#diagnostic-queries) . Például a "PartitionKeyRUConsumption" kifejezést használó lekérdezések. 

* **ControlPlaneRequests**: Ez a napló a vezérlési sík műveleteivel, például a fiókok létrehozásával, a régiók hozzáadásával vagy eltávolításával, a fiók replikációs beállításainak frissítésével kapcsolatos részleteket tartalmazza. Ez a napló típusa minden olyan API-típushoz elérhető, amely tartalmazza az SQL (mag), a MongoDB, a Gremlin, a Cassandra és a Table API.

* **Kérelmek**: akkor válassza ezt a lehetőséget, ha a Azure Cosmos DBről a diagnosztikai beállításokban lévő célhelyekre szeretne metrikus adatokat gyűjteni. Ez ugyanazokat az adatokat gyűjti össze automatikusan az Azure-Mérőszámokban. A metrikai adatok összegyűjtése az erőforrás-naplókkal mindkét típusú adat elemzéséhez, valamint a Azure Monitoron kívüli metrikai adatok küldéséhez.

A diagnosztikai beállítások a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes információkért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák gyűjtéséhez az Azure-ban](../azure-monitor/platform/diagnostic-settings.md) .


## <a id="diagnostic-queries"></a>Diagnosztikai lekérdezésekkel kapcsolatos hibák elhárítása

1. Hogyan kérheti le a költséges lekérdezések díjait?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Hogyan lehet megkeresni, hogy mely műveletek zajlanak a legtöbb RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Hogyan kérheti le a különböző műveletek terjesztését?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Mi a partíció által felhasznált maximális átviteli sebesség?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Hogyan kérheti le a másodpercenkénti RU/s-fogyasztási kulcsok adatait?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Egy adott partíciós kulcs igénylési díjainak beszerzése

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Hogyan lehet a legfelső szintű partíciós kulcsokat lekérni a legtöbb RU/s értékkel egy adott időszakban? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. A naplófájlok beszerzése azokhoz a partíciós kulcsokhoz, amelyek tárolási mérete meghaladja a 8 GB-ot?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. A partíciós kulcs statisztikáinak beszerzése, hogy kiértékelje az adatbázis-fiók három partíciójának kiértékelését?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Következő lépések

* [Azure Cosmos DB Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [A Azure Cosmos DB metrikáinak monitorozása és hibakeresése](use-metrics.md)
