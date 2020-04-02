---
title: Az Azure Cosmos DB-adatok figyelése az Azure Diagnosztikai beállítások használatával
description: Megtudhatja, hogy miként figyelheti az Azure Cosmos DB-ben tárolt adatok teljesítményét és rendelkezésre állását az Azure Diagnostic siai beállításokkal
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521043"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Az Azure Cosmos DB-adatok figyelése az Azure diagnosztikai beállításainak használatával

Az Azure diagnosztikai beállításai az erőforrásnaplók gyűjtésére szolgálnak. Az Azure-erőforrás naplók által kibocsátott egy erőforrás, és gazdag, gyakori adatokat az adott erőforrás működéséről. Ezeket a naplókat kérésenként rögzíti, és "adatsíknaplóknak" is nevezik őket. Néhány példa az adatsík-műveletekre: törlés, beszúrás és olvasási hírcsatorna. Ezeknek a naplóknak a tartalma erőforrástípustól függően változik.

A platformmetrikák és a tevékenységnaplók gyűjtése automatikusan történik, mivel létre kell hoznia egy diagnosztikai beállítást az erőforrásnaplók összegyűjtéséhez vagy az Azure Monitoron kívülre továbbításhoz. Az Azure Cosmos-fiókok diagnosztikai beállítását az alábbi lépések végrehajtásával kapcsolhatja be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg az Azure Cosmos-fiókot. Nyissa **meg** a Diagnosztikai beállítások ablaktáblát, és válassza **a Diagnosztikai beállítás hozzáadása** lehetőséget.

1. A **Diagnosztikai beállítások** ablaktáblán töltse ki az űrlapot a következő adatokkal: 

    * **Név**: Adja meg a létrehozandó naplók nevét.

    * A naplókat archiválhatja **egy tárfiókba,** **streamelheti egy eseményközpontba** vagy **elküldheti a Log Analytics szolgáltatásba**

1. Diagnosztikai beállítás létrehozásakor megadhatja, hogy a naplók melyik kategóriáját kell összegyűjteni. Az Azure Cosmos DB által támogatott naplók kategóriái az alábbiakban láthatók az általuk gyűjtött mintanaplóval együtt:

 * **DataPlaneRequests:** Válassza ezt a lehetőséget a háttér-kérelmek naplózásához az összes API-k, amelyek magukban foglalják az SQL, Graph, MongoDB, Cassandra és table API-fiókok az Azure Cosmos DB. A legfontosabb tulajdonságok `Requestcharge`a `statusCode` `clientIPaddress`következők: `partitionID`, , , és .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests:** Válassza ezt a lehetőséget a felhasználó által kezdeményezett kérelmek naplózásához az előtéri az Azure Cosmos DB MongoDB API-jának kiszolgálásához. Ez a naplótípus más API-fiókok esetében nem érhető el. A legfontosabb tulajdonságok `Requestcharge`a `opCode`következők: , . Ha engedélyezi a MongoRequests a diagnosztikai naplók, győződjön meg róla, hogy kapcsolja ki a DataPlaneRequests. Az API-n minden kérelemhez egy naplót láthat.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests:** Válassza ezt a lehetőséget a felhasználó által kezdeményezett kérelmek naplózásához az előtéri az Azure Cosmos DB Cassandra API-jának kiszolgálásához. Ez a naplótípus más API-fiókok esetében nem érhető el. A legfontosabb tulajdonságai `requestCharge` `piiCommandText`a `operationName`, , . Ha engedélyezi a CassandraRequests-t a diagnosztikai naplókban, győződjön meg arról, hogy kikapcsolja a DataPlaneRequests.When you enable CassandraRequests in diagnostics logs, make sure to turn off the DataPlaneRequests. Az API-n minden kérelemhez egy naplót láthat.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: Ezzel a beállítással naplózva a végrehajtott lekérdezésszöveget. Ez a naplótípus csak az SQL API-fiókok hoz érhető el.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Válassza ezt a lehetőséget a partíciókulcsok statisztikáinak naplózásához. Ez jelenleg a partíciókulcsok tárolási méretével (KB) jelenik meg. Tekintse meg a [hibaelhárítási problémák segítségével azure diagnosztikai lekérdezések](#diagnostic-queries) szakasza ebben a cikkben. Például a "PartitionKeyStatistics" (PartitionKeyStatistics) kifejezést használó lekérdezések. A napló az első három partíciókulcson kerül kibocsátásra, amelyek a legtöbb adattárolót foglalják el. Ez a napló olyan adatokat tartalmaz, mint például az előfizetés azonosítója, a régió neve, az adatbázis neve, a gyűjtemény neve, a partíciókulcs és a tárterület mérete kb-ban.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Ez a napló a partíciókulcsok másodpercenkénti összesített RU/s-felhasználását jelenti. Jelenleg az Azure Cosmos DB csak az SQL API-fiókokhoz, valamint a pontolvasási/-olvasási és tárolt eljárási műveletekhez jelent partíciókulcsokat. más API-k és művelettípusok nem támogatottak. Más API-k esetében a diagnosztikai naplótábla partíciókulcs-oszlopa üres lesz. Ez a napló olyan adatokat tartalmaz, mint az előfizetés azonosítója, a régió neve, az adatbázis neve, a gyűjtemény neve, a partíciókulcs, a művelet típusa és a kérelemdíj. Tekintse meg a [hibaelhárítási problémák segítségével azure diagnosztikai lekérdezések](#diagnostic-queries) szakasza ebben a cikkben. Például a "PartitionKeyRUConsumption" (PartitionKeyRUConsumption) használó lekérdezések. 

* **ControlPlaneRequests**: Ez a napló részleteket tartalmaz a vezérlősík-műveletekről, például egy fiók létrehozásáról, egy régió hozzáadásáról vagy eltávolításáról, a fiókreplikációs beállítások frissítésével stb. Ez a naplótípus minden API-típushoz elérhető, amelyek tartalmazzák az SQL (Core), a MongoDB, a Gremlin, a Cassandra, a Table API.

* **Kérések:** Válassza ezt a lehetőséget az Azure Cosmos DB metrikaadatok gyűjtéséhez a diagnosztikai beállításban lévő célhelyekre. Ez ugyanazokat az adatokat gyűjtött automatikusan az Azure Metrics. Metrikaadatok gyűjtése erőforrásnaplókkal mindkét típusú adat együtt elemzéséhez és metrikaadatok küldéséhez az Azure Monitoron kívül.

Az Azure Portal, a CLI vagy a PowerShell használatával diagnosztikai beállítások létrehozásáról az [Azure-naplónaplók és metrikák gyűjtéséhez diagnosztikai beállítás létrehozása az Azure-cikkben című témakörben](../azure-monitor/platform/diagnostic-settings.md) olvashat részletesen.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Diagnosztikai lekérdezésekkel kapcsolatos problémák elhárítása

1. Hogyan juthat el a kérelem díjak drága lekérdezések?

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

1. Hogyan lehet megtalálni, hogy mely műveletek szedik a legtöbb RU/s-t?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Hogyan juthat be a disztribúció a különböző műveletek?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Mi a partíció által felhasznált maximális átviteli teljesítmény?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Hogyan juthat el az információt a partíciókulcsok RU / s fogyasztás másodpercenként?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Egy adott partíciókulcs kérelemdíjának bekérése

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Hogyan juthat el a felső partíció kulcsok a legtöbb RU /s egy adott időszakban fogyasztott? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Hogyan juthat el a naplókat a partíciókulcsok, amelyek tárolási mérete nagyobb, mint 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Hogyan juthat partíció kulcs statisztikák kiértékelése ferde között az első három partíciót adatbázis-fiók?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>További lépések

* [Azure Monitor az Azure Cosmos DB-hoz](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Az Azure Cosmos DB metrikák figyelése és hibakeresése](use-metrics.md)
