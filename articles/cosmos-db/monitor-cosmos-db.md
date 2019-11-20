---
title: Figyelési Azure Cosmos DB | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Cosmos DB teljesítményét és rendelkezésre állását.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.openlocfilehash: acba365ac5cd33b6fbba5910e5e8b469bf111aac
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186850"
---
# <a name="monitoring-azure-cosmos-db"></a>Figyelés Azure Cosmos DB
Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk az Azure Cosmos-adatbázisok által létrehozott figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és riasztáshoz.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
A Azure Cosmos DB az Azure-ban teljes körű figyelési szolgáltatásként hozza létre a figyelési adatok [Azure monitor](../azure-monitor/overview.md) , amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrások mellett. 

Ha még nem ismeri az Azure-szolgáltatások figyelését, kezdje az Azure- [erőforrások monitorozása Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) a következő témakörrel:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Cosmos DB gyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálására és az Azure-eszközökkel történő elemzésére szolgáló példákat tartalmaz.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Cosmos DB Azure Monitor (előzetes verzió)
A [Azure Cosmos DB Azure monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) a [Azure monitor munkafüzetek szolgáltatásán](../azure-monitor/app/usage-workbooks.md) alapul, és ugyanazokat a figyelési adatokat használja, amelyeket az alábbi szakaszokban ismertetett Cosmos DBhoz gyűjtöttek be. Ezzel az eszközzel megtekintheti az összes Azure Cosmos DB-erőforrás teljes teljesítményét, hibáit, kapacitását és működési állapotát egy egységes interaktív felhasználói élményben, és kihasználhatja Azure Monitor egyéb funkcióit részletes elemzés és riasztás céljából. 

![Cosmos DB Azure Monitor](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>A Azure Cosmos DB gyűjtött adatok figyelése
Azure Cosmos DB ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások. A Azure Cosmos DB által létrehozott naplók és metrikák részletes ismertetését lásd: [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) .

Az egyes Azure Cosmos-adatbázisokhoz tartozó Azure Portal **áttekintő** lapja az adatbázis-használat rövid áttekintését tartalmazza, beleértve a kérését és az óránkénti számlázási használatot. Ez hasznos információ, de csak kis mennyiségű figyelési adat érhető el. Ezeket az adatokat a rendszer automatikusan gyűjti, és az elemzéshez azonnal elérhetővé válik, miközben a további adatgyűjtést is engedélyezheti néhány konfigurációval.

![Áttekintő lap](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Diagnosztikai beállítások
A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítást kell létrehoznia az erőforrás-naplók összegyűjtéséhez vagy a Azure Monitoron kívüli továbbításához. A diagnosztikai beállításoknak a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes folyamatért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](../azure-monitor/platform/diagnostic-settings.md) .

Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön. A Azure Cosmos DB kategóriái az alábbiakban láthatók a mintaadatok mellett.

 * **DataPlaneRequests**: válassza ezt a lehetőséget, ha a háttérbeli kérelmeket minden olyan API-hoz naplózni szeretné, amely tartalmazza az SQL, a Graph, a MongoDB, a Cassandra és a Table API fiókokat Azure Cosmos db. A fontos tulajdonságok a következők: Requestcharge, statusCode, clientIPaddress és partitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: ezzel a beállítással a felhasználó által kezdeményezett kérelmeket naplózhatja az előtérből a MongoDB API-ra irányuló Azure Cosmos db kérések kiszolgálásához. A MongoDB-kérelmek megjelennek a MongoRequests és a DataPlaneRequests is. A fontos tulajdonságok a következők: Requestcharge, műveleti kód.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: válassza ezt a lehetőséget a végrehajtott lekérdezési szöveg naplózásához. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: válassza ezt a lehetőséget a partíciós kulcsok statisztikáinak naplózásához. Ez jelenleg a partíciós kulcsok tárolási méretével (KB) van ábrázolva. A rendszer kibocsátja a naplót a legtöbb adattárolást elfoglaló első három partíciós kulcson.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Metrikai kérelmek**: Ha ezt a beállítást választja, a rendszer a diagnosztikai beállításban található célokból származó metrikai adatokat gyűjt a Azure Cosmos DBról. Ez ugyanazokat az adatokat gyűjti össze automatikusan az Azure-Mérőszámokban. A metrikai adatok összegyűjtése az erőforrás-naplókkal mindkét típusú adat elemzéséhez, valamint a Azure Monitoron kívüli metrikai adatok küldéséhez.

## <a name="analyzing-metric-data"></a>Metrikus adatok elemzése
A Azure Cosmos DB a metrikák használatának egyéni élményét biztosítja. A jelen felület használatáról és a különböző Azure Cosmos DB forgatókönyvek elemzéséről lásd: [Azure Cosmos db metrikáinak monitorozása és hibakeresése Azure monitor](cosmos-db-azure-monitor-metrics.md) .

A metrikákat a **Azure monitor** menüből **megnyitva a metrikák** segítségével elemezheti az Azure Cosmos db metrikáit más Azure-szolgáltatásoktól származó metrikákkal. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/platform/metrics-getting-started.md) . Az Azure Cosmos DB összes mérőszáma a névtérben **Cosmos dB standard mérőszámokban**szerepel. A következő dimenziókat használhatja ezekkel a metrikákkal, amikor szűrőt ad hozzá egy diagramhoz:

- collectionName
- DatabaseName
- OperationType
- Régió
- statusCode


## <a name="analyzing-log-data"></a>Naplózási adatok elemzése
Azure Monitor naplókban található adatkészletek olyan táblákban tárolódnak, amelyekben az egyes táblák egyedi tulajdonságokkal rendelkeznek. A Azure Cosmos DB az alábbi táblázatokban tárolja az adattárakat.

| Tábla | Leírás |
|:---|:---|
| AzureDiagnostics | Az erőforrás-naplók tárolására több szolgáltatás által használt közös táblázat. Azure Cosmos DBból származó erőforrás-naplók azonosíthatók a `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | A tevékenység naplójában lévő összes rekordot tároló általános tábla. 


> [!IMPORTANT]
> Amikor kijelöli a **naplók** elemet a Azure Cosmos db menüben, a rendszer megnyit egy log Analytics a jelenlegi Azure Cosmos-adatbázisra beállított lekérdezési hatókörrel. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="sample-queries"></a>Mintalekérdezések
A következő lekérdezések segítségével figyelheti az Azure Cosmos-adatbázisokat.

* Az összes Azure Cosmos DB-ből a diagnosztikai naplók lekérdezése egy megadott időszak:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Lekérdezni a 10 legtöbb nemrég naplózott események:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* A lekérdezés minden műveletre, művelet típusa szerint csoportosítva:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* A lekérdezés az összes művelet szerint csoportosítva **erőforrás**:

    ```Kusto
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* A lekérdezés az összes felhasználói tevékenység, erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
* A 100 RUs-nál nagyobb összes lekérdezés **DataPlaneRequests** és **QueryRunTimeStatistics**származó adatokkal való lekéréséhez.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
        AzureDiagnostics
        | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
        | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* A lekérdezéshez, amelynek műveletek tovább tart, mint 3 idő ezredmásodpercben:

    ```Kusto
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Lekérdezése, mely ügynök fut a műveletek:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Lekérdezni a, ha a hosszú ideig futó műveletek lettek végrehajtva:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```
    
* A partíciós kulcs statisztikáinak beszerzéséhez az adatbázis-fiók felső 3 partíciójának kiértékeléséhez:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB programozott figyelése
A fiók metrikáit, például a fiók tárolási használat és a teljes kérelmek, a portálon elérhető nem érhetők el az SQL API-kon keresztül. Használati adatok, a gyűjtemény szintjén azonban lekérheti az SQL API-k használatával. Adatgyűjtési szint adatok lekéréséhez tegye a következőket:

* A REST API [hajtsa végre a gyűjtemény egy GET](https://msdn.microsoft.com/library/mt489073.aspx). A kvóta- és használati adatok gyűjtésére az x-ms-resource-kvóta és az x-ms-erőforrás-használat fejlécek, a válaszban adja vissza.
* A .NET SDK használatához használja a [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metódussal, amely adja vissza egy [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) , amely tartalmaz egy használati tulajdonságok száma például  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**, stb.

További metrikákhoz való hozzáféréshez, használja a [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Elérhető metrikai meghatározások meghívásával lekérhető:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Lekérdezések egyéni metrikákat lekéréséhez használja a következő formátumot:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Következő lépések

- A Azure Cosmos DB által létrehozott naplók és metrikák hivatkozását a [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) tartalmazza.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/insights/monitor-azure-resource.md) .
