---
title: 'Oktatóanyag: figyelési adatfeldolgozás az Azure Adatkezelő kód nélkül'
description: Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a figyelési adatgyűjtést az Azure Adatkezelő egy sor kód nélkül és az adatlekérdezéssel.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198048"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Oktatóanyag: figyelési adatfeldolgozás és-lekérdezés az Azure Adatkezelő 

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a diagnosztikai és tevékenységi naplókból származó adatok betöltését egy Azure Adatkezelő-fürtre kód írása nélkül. Az egyszerű betöltési módszer használatával gyorsan megkezdheti az Azure-Adatkezelő lekérdezését az adatelemzéshez.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Táblázatok és betöltési leképezés létrehozása Azure Adatkezelő-adatbázisban.
> * Formázza a betöltött adatot egy frissítési házirend használatával.
> * Hozzon létre egy [Event hub](/azure/event-hubs/event-hubs-about) -t, és kapcsolódjon az Azure Adatkezelőhoz.
> * Adatok továbbítása egy Event hubhoz Azure Monitor [diagnosztikai metrikák és naplók](/azure/azure-monitor/platform/diagnostic-settings) és [tevékenységek naplóiból](/azure/azure-monitor/platform/activity-logs-overview).
> * A betöltött adatot az Azure Adatkezelő használatával kérdezheti le.

> [!NOTE]
> Hozza létre az összes erőforrást ugyanabban az Azure-helyen vagy régióban. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md). Ebben az oktatóanyagban az adatbázis neve *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Adatszolgáltató Azure Monitor: diagnosztikai metrikák és naplók és tevékenységek naplói

Tekintse meg és Ismerje meg a Azure Monitor diagnosztikai metrikák és naplók és a tevékenységek naplófájljai által biztosított adatokat alább. Ezen adatsémák alapján hozzon létre egy adatfeldolgozási folyamatot. Vegye figyelembe, hogy a napló minden eseménye tartalmaz rekordokat tartalmazó tömböt. A rekordok ezen tömbje az oktatóanyag későbbi részében lesz felosztva.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Példák a diagnosztikai metrikák és naplók és a tevékenységek naplóira

Az Azure diagnosztikai metrikáit és naplóit, valamint a tevékenységek naplóit egy Azure-szolgáltatás bocsátja ki, és adatokat biztosít a szolgáltatás működéséről. 

# <a name="diagnostic-metrics"></a>[Diagnosztikai metrikák](#tab/diagnostic-metrics)
#### <a name="example"></a>Példa

A diagnosztikai metrikák összesítése 1 perces időtartammal történik. A következő példa egy Azure Adatkezelő metrika – Event sémát mutat be a lekérdezési időtartamon:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
#### <a name="example"></a>Példa

Az alábbi példa egy Azure Adatkezelő diagnosztikai betöltési [naplót mutat be](using-diagnostic-logs.md#diagnostic-logs-schema):

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="example"></a>Példa

Az Azure-Tevékenységnaplók olyan előfizetési szintű naplók, amelyek betekintést nyújtanak az előfizetése erőforrásaiban végrehajtott műveletekre. A következő példa egy tevékenység-naplózási eseményt mutat be a hozzáférés ellenőrzéséhez:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Betöltési folyamat beállítása az Azure Adatkezelő

Az Azure Adatkezelő-folyamat beállítása több lépést is magában foglal, például a [táblázatok létrehozását és az adatfeldolgozást](/azure/data-explorer/ingest-sample-data#ingest-data). Emellett az adatkezelés, a leképezés és a frissítés is megvégezhető.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Kapcsolódás az Azure Adatkezelő webes felhasználói felületéhez

Az Azure Adatkezelő *TestDatabase* -adatbázisban válassza a **lekérdezés** lehetőséget az Azure adatkezelő webes felhasználói felületének megnyitásához.

![Lekérdezési oldal](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>A céltábla létrehozása

A Azure Monitor naplók szerkezete nem táblázatos. Az adatok kezelése és az egyes események kibontása egy vagy több rekordra. A nyers adatokat a rendszer betölti egy *ActivityLogsRawRecords* nevű köztes táblába a *DiagnosticRawRecords* és a diagnosztikai metrikák és naplók számára. Ekkor az adatkezelés és a kibontása is megtörténik. A frissítési szabályzatok segítségével a kibontott adatok bekerülnek a *ActivityLogs* táblába a tevékenységek naplóihoz, *DiagnosticMetrics* a diagnosztikai metrikák és a *DiagnosticLogs* a diagnosztikai naplókhoz. Ez azt jelenti, hogy két külön táblát kell létrehoznia a tevékenységek naplófájljainak betöltéséhez és három külön tábla létrehozásához a diagnosztikai metrikák és naplók betöltéséhez.

Az Azure Adatkezelő webes felhasználói felület használatával hozza létre a céltábla az Azure Adatkezelő-adatbázisban.

# <a name="diagnostic-metrics"></a>[Diagnosztikai metrikák](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Táblázatok létrehozása a diagnosztikai metrikák számára

1. A *TestDatabase* -adatbázisban hozzon létre egy *DiagnosticMetrics* nevű táblát a diagnosztikai metrikák rekordjainak tárolására. Használja a következő `.create table` Control parancsot:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Válassza a **Futtatás** lehetőséget a tábla létrehozásához.

    ![A lekérdezés futtatása](media/ingest-data-no-code/run-query.png)

1. Hozza létre a *DiagnosticRawRecords* nevű köztes adattáblát a *TestDatabase* adatbázisban az adatkezeléshez a következő lekérdezés használatával. Válassza a **Futtatás** lehetőséget a tábla létrehozásához.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. A köztes tábla nulla [adatmegőrzési szabályának](/azure/kusto/management/retention-policy) beállítása:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Táblák létrehozása a diagnosztikai naplókhoz 

1. A *TestDatabase* adatbázisban hozzon létre egy *DiagnosticLogs* nevű táblát a diagnosztikai naplók tárolására. Használja a következő `.create table` Control parancsot:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Válassza a **Futtatás** lehetőséget a tábla létrehozásához.

1. Hozza létre a *DiagnosticRawRecords* nevű köztes adattáblát a *TestDatabase* adatbázisban az adatkezeléshez a következő lekérdezés használatával. Válassza a **Futtatás** lehetőséget a tábla létrehozásához.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. A köztes tábla nulla [adatmegőrzési szabályának](/azure/kusto/management/retention-policy) beállítása:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Táblák létrehozása a tevékenység naplóihoz 

1. Hozzon létre egy *ActivityLogs* nevű táblázatot a *TestDatabase* adatbázisban a tevékenységi naplók rekordjainak fogadásához. A tábla létrehozásához futtassa a következő Azure Adatkezelő lekérdezést:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Az adatkezeléshez hozzon létre egy *ActivityLogsRawRecords* nevű köztes adattáblát a *TestDatabase* adatbázisban:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. A köztes tábla nulla [adatmegőrzési szabályának](/azure/kusto/management/retention-policy) beállítása:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Tábla-hozzárendelések létrehozása

 Mivel az adatformátum `json`, az adatleképezés szükséges. A `json` leképezése leképezi az egyes JSON-útvonalakat a tábla oszlopainak nevéhez.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosztikai metrikák/diagnosztikai naplók](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>A diagnosztikai metrikák és naplók leképezése a táblába

A diagnosztikai metrika és a naplózási adatok táblára való leképezéséhez használja a következő lekérdezést:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Tevékenység-naplók leképezése a táblára

A tevékenység naplójának az adattáblához való leképezéséhez használja a következő lekérdezést:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>A metrika és a naplózási adatok frissítési szabályzatának létrehozása

# <a name="diagnostic-metrics"></a>[Diagnosztikai metrikák](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Adatfrissítési szabályzat létrehozása diagnosztikai metrikák számára

1. Hozzon létre egy olyan [függvényt](/azure/kusto/management/functions) , amely kibővíti a diagnosztikai metrikai rekordok gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon. Használja a [`mv-expand`](/azure/kusto/query/mvexpandoperator) operátort:
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Adja hozzá a [frissítési szabályzatot](/azure/kusto/concepts/updatepolicy) a cél táblához. Ez a szabályzat automatikusan futtatja a lekérdezést a *DiagnosticRawRecords* köztes adattábla bármely újonnan betöltött adattáblájában, és betölti az eredményeket a *DiagnosticMetrics* táblába:

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Adatfrissítési szabályzat létrehozása diagnosztikai naplókhoz

1. Hozzon létre egy olyan [függvényt](/azure/kusto/management/functions) , amely kibővíti a diagnosztikai naplók rekordjainak gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon. Engedélyezheti a betöltési naplókat egy Azure Adatkezelő-fürtön, és a betöltési [naplók sémáját](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)is használhatja. Létre fog hozni egy táblát a sikeres és sikertelen betöltéshez, míg egyes mezők üresek lesznek a sikeres betöltéshez (például ErrorCode). Használja a [`mv-expand`](/azure/kusto/query/mvexpandoperator) operátort:

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Adja hozzá a [frissítési szabályzatot](/azure/kusto/concepts/updatepolicy) a cél táblához. Ez a szabályzat automatikusan futtatja a lekérdezést a *DiagnosticRawRecords* köztes adattábla bármely újonnan betöltött adattáblájában, és betölti az eredményeket a *DiagnosticLogs* táblába:

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Adatfrissítési szabályzat létrehozása a tevékenység naplóihoz

1. Hozzon létre egy olyan [függvényt](/azure/kusto/management/functions) , amely kibővíti a műveletnapló rekordjainak gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon. Használja a [`mv-expand`](/azure/kusto/query/mvexpandoperator) operátort:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Adja hozzá a [frissítési szabályzatot](/azure/kusto/concepts/updatepolicy) a cél táblához. Ez a szabályzat automatikusan futtatja a lekérdezést a *ActivityLogsRawRecords* köztes adattábla bármely újonnan betöltött adattáblájában, és betölti az eredményeket a *ActivityLogs* táblába:

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs-névtér létrehozása

Az Azure diagnosztikai beállításai lehetővé teszik a metrikák és naplók exportálását egy Storage-fiókba vagy egy Event hub-ba. Ebben az oktatóanyagban a metrikákat és a naplókat egy Event hub használatával irányítjuk. A következő lépések végrehajtásához létre kell hoznia egy Event Hubs névteret és egy Event hubot a diagnosztikai metrikák és naplók számára. A Azure Monitor létrehozza az Event hub *-elemzéseket – operatív* naplókat a tevékenység naplóihoz.

1. Hozzon létre egy Event hub-t egy Azure Resource Manager sablon használatával a Azure Portalban. A cikkben szereplő további lépések végrehajtásához kattintson a jobb gombbal az **üzembe helyezés az Azure** -ban gombra, majd válassza a **Megnyitás új ablakban**lehetőséget. Az **üzembe helyezés az Azure** -ban gombra kattintva megtekintheti a Azure Portal.

    [![üzembe helyezés az Azure-ban gomb](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Hozzon létre egy Event Hubs névteret és egy Event hubot a diagnosztikai naplókhoz.

    ![Event hub létrehozása](media/ingest-data-no-code/event-hub.png)

1. Adja meg az alábbi adatokat az űrlapon. Az alábbi táblázatban nem szereplő beállításokhoz használja az alapértelmezett értékeket.

    **Beállítás** | **Ajánlott érték** | **Leírás**
    |---|---|---|
    | **Előfizetés** | *Az Ön előfizetése* | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | **Erőforráscsoport** | *test-resource-group* | Hozzon létre egy új erőforráscsoportot. |
    | **Hely** | Válassza ki azt a régiót, amelyik a legjobban megfelel az igényeinek. | Hozza létre a Event Hubs névteret a többi erőforrással megegyező helyen.
    | **Névtér neve** | *AzureMonitoringData* | Válasszon egy egyedi nevet a névtér azonosításához.
    | **Event hub neve** | *DiagnosticData* | Az eseményközpont a névtéren belül helyezkedik el, ami egy egyedi hatókörkezelési tárolóként szolgál. |
    | **Fogyasztói csoport neve** | *adxpipeline* | Hozzon létre egy fogyasztói csoport nevét. A fogyasztói csoportokkal több fogyasztói alkalmazás is rendelkezhet az eseménystream külön nézetével. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Azure Monitor metrikák és naplók összekötése az Event hub-ban

Most csatlakoztatnia kell a diagnosztikai metrikákat és naplókat, valamint a tevékenység naplóit az Event hub-hoz.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosztikai metrikák/diagnosztikai naplók](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Diagnosztikai metrikák és naplók összekötése az Event hub-ban

Válasszon ki egy erőforrást, amelyből exportálni kívánja a metrikákat. Számos erőforrástípus támogatja a diagnosztikai adatexportálást, beleértve a Event Hubs névteret, Azure Key Vault, az Azure IoT Hub és az Azure Adatkezelő-fürtöket. Ebben az oktatóanyagban egy Azure Adatkezelő-fürtöt használunk erőforrásként, áttekintjük a lekérdezés teljesítményének mérőszámait és a betöltési eredmények naplóit.

1. Válassza ki a Kusto-fürtöt a Azure Portalban.
1. Válassza a **diagnosztikai beállítások**lehetőséget, majd kattintson a **diagnosztika bekapcsolása** hivatkozásra. 

    ![Diagnosztikai beállítások](media/ingest-data-no-code/diagnostic-settings.png)

1. Megnyílik a **diagnosztika beállításai** panel. Hajtsa végre a következő lépéseket:
   1. Adja meg a diagnosztikai napló adatait a *ADXExportedData*néven.
   1. A **napló**területen jelölje be a **SucceededIngestion** és a **FailedIngestion** jelölőnégyzetet is.
   1. A **metrika**területen jelölje be a **lekérdezési teljesítmény** jelölőnégyzetet.
   1. Jelölje ki az **adatfolyamot az Event hub számára** jelölőnégyzetet.
   1. Válassza a **Konfigurálás**lehetőséget.

      ![Diagnosztikai beállítások ablaktábla](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Az **Event hub kiválasztása** ablaktáblán állítsa be, hogyan exportálja az adatok a diagnosztikai naplókból a létrehozott Event hubhoz:
    1. Az **Event hub-névtér kiválasztása** listában válassza a *AzureMonitoringData*lehetőséget.
    1. Az **Event hub-név kiválasztása** listában válassza a *DiagnosticData*lehetőséget.
    1. Az **Event hub-házirend kiválasztása** listában válassza a **RootManagerSharedAccessKey**lehetőséget.
    1. Kattintson az **OK** gombra.

1. Kattintson a **Mentés** gombra.

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Tevékenységnaplók összekapcsolása az Event hub-ban

1. A Azure Portal bal oldali menüjében válassza a **műveletnapló**elemet.
1. Megnyílik a **tevékenység naplója** ablak. Válassza **az Exportálás az Event hubhoz**lehetőséget.

    ![Tevékenység naplójának ablaka](media/ingest-data-no-code/activity-log.png)

1. Megnyílik az **exportálási tevékenység naplója** ablak:
 
    ![Tevékenység naplójának exportálása ablak](media/ingest-data-no-code/export-activity-log.png)

1. Az **exportálási tevékenység naplója** ablakban végezze el a következő lépéseket:
      1. Válassza ki előfizetését.
      1. A **régiók** listában válassza az **összes kijelölése**lehetőséget.
      1. Jelölje be az **Exportálás az Event hubhoz** jelölőnégyzetet.
      1. A **Service Bus-névtér** kiválasztása elemre kattintva nyissa meg az **Event hub kiválasztása** ablaktáblát.
      1. Az **Event hub kiválasztása** ablaktáblán válassza ki az előfizetését.
      1. Az **Event hub-névtér kiválasztása** listában válassza a *AzureMonitoringData*lehetőséget.
      1. Az **Event hub-házirend kiválasztása** listából válassza ki az alapértelmezett Event hub-házirend nevét.
      1. Kattintson az **OK** gombra.
      1. Az ablak bal felső sarkában válassza a **Mentés**lehetőséget.
   Létrejön egy, az elemzések és az *operatív naplók* nevű Event hub.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Tekintse át az Event hubokba áramló adatforgalmat

1. Várjon néhány percet, amíg a kapcsolat meg nem történik, és az Event hub-ba való exportálás befejeződött. Nyissa meg a Event Hubs névteret, és tekintse meg a létrehozott Event hubokat.

    ![Létrehozott Event hubok](media/ingest-data-no-code/event-hubs-created.png)

1. Tekintse át az Event hub-ba irányuló adatforgalmat:

    ![Event hub-adatkészletek](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Event hub összekötése az Azure Adatkezelő

Most létre kell hoznia az adatkapcsolatokat a diagnosztikai metrikák és naplók és a tevékenységek naplóihoz.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Adatkapcsolatok létrehozása diagnosztikai metrikák és naplók és tevékenységek naplóihoz

1. Az Azure Adatkezelő *kustodocs*nevű fürtben válassza az **adatbázisok** lehetőséget a bal oldali menüben.
1. Az **adatbázisok** ablakban válassza ki a *TestDatabase* -adatbázist.
1. A bal oldali menüben válassza az **adatfeldolgozás**lehetőséget.
1. Az **adatfeldolgozási** ablakban kattintson az **+ adatkapcsolatok hozzáadása**lehetőségre.
1. Az **adatkapcsolatok** ablakban adja meg a következő adatokat:

    ![Event hub-adatkapcsolat](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosztikai metrikák/diagnosztikai naplók](#tab/diagnostic-metrics+diagnostic-logs) 

1. Használja az **adatkapcsolatok** ablakban az alábbi beállításokat:

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Adatkapcsolatok neve** | *DiagnosticsLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | **Event hub-névtér** | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | **Event hub** | *DiagnosticData* | A létrehozott eseményközpont. |
    | **Fogyasztói csoport** | *adxpipeline* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | | |

    Céltábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban statikus útválasztást (alapértelmezett) fog használni, ahol megadhatja a tábla nevét, az adatformátumot és a leképezést. Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Tábla** | *DiagnosticRawRecords* | A *TestDatabase* -adatbázisban létrehozott tábla. |
    | **Adatformátum** | *JSON* | A táblázatban használt formátum. |
    | **Oszlopleképezés** | *DiagnosticRawRecordsMapping* | A *TestDatabase* -adatbázisban létrehozott leképezés, amely leképezi a bejövő JSON-adattípusokat a *DiagnosticRawRecords* tábla oszlopnevek és adattípusai között.|
    | | |

1. Kattintson a **Létrehozás** gombra.  

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)

1. Használja az **adatkapcsolatok** ablakban az alábbi beállításokat:

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Adatkapcsolatok neve** | *ActivityLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | **Event hub-névtér** | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | **Event hub** | *elemzések – operatív naplók* | A létrehozott eseményközpont. |
    | **Fogyasztói csoport** | *$Default* | Az alapértelmezett fogyasztói csoport. Ha szükséges, létrehozhat egy másik fogyasztói csoportot is. |
    | | |

    Céltábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban statikus útválasztást (alapértelmezett) fog használni, ahol megadhatja a tábla nevét, az adatformátumot és a leképezést. Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Tábla** | *ActivityLogsRawRecords* | A *TestDatabase* -adatbázisban létrehozott tábla. |
    | **Adatformátum** | *JSON* | A táblázatban használt formátum. |
    | **Oszlopleképezés** | *ActivityLogsRawRecordsMapping* | A *TestDatabase* -adatbázisban létrehozott leképezés, amely leképezi a bejövő JSON-adattípusokat a *ActivityLogsRawRecords* tábla oszlopnevek és adattípusai között.|
    | | |

1. Kattintson a **Létrehozás** gombra.  
---

## <a name="query-the-new-tables"></a>Az új táblák lekérdezése

Most már rendelkezik egy adatforgalommal rendelkező folyamattal. A fürtön keresztüli betöltés alapértelmezés szerint 5 percet vesz igénybe, ezért a lekérdezés megkezdése előtt néhány percen belül engedélyezzük az adatfeldolgozást.

# <a name="diagnostic-metrics"></a>[Diagnosztikai metrikák](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>A diagnosztikai metrikák táblázatának lekérdezése

A következő lekérdezés elemzi a lekérdezési időtartam adatait az Azure Adatkezelő diagnosztikai metrikai rekordjaiból:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Lekérdezés eredményei:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>A diagnosztikai naplók táblázatának lekérdezése

Ez a folyamat egy Event hub használatával hoz létre betöltést. Tekintse át a betöltések eredményeit.
A következő lekérdezés azt elemzi, hogy hány betöltés van egy percen belül, beleértve a `Database`, `Table` és `IngestionSourcePath` az egyes intervallumokhoz tartozó mintákat:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Lekérdezés eredményei:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>A tevékenységi naplók táblázatának lekérdezése

Az alábbi lekérdezés az Azure Adatkezelő tevékenységi napló rekordjaiból származó adatokat elemzi:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Lekérdezés eredményei:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768,333 |
| | |

---

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan írhat számos további lekérdezést az Azure Adatkezelőból kinyert adatokhoz az [azure adatkezelő írási lekérdezésével](write-queries.md).
* [Azure Adatkezelő-betöltési műveletek figyelése diagnosztikai naplók használatával](using-diagnostic-logs.md)
* [Metrikák használata a fürt állapotának figyeléséhez](using-metrics.md)
