---
title: 'Oktatóanyag: A figyelési adatok betöltése az Azure Data Explorerben kód nélkül'
description: Ebben az oktatóanyagban megtudhatja, hogyan kell beadni a figyelési adatokat az Azure Data Explorer betöltése egy sor kód és az adatok lekérdezése nélkül.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198048"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Oktatóanyag: Adatok betöltése és lekérdezésfigyelési adatok betöltése az Azure Data Explorerben 

Ez az oktatóanyag bemutatja, hogyan kell adatokat beadni a diagnosztikai és tevékenységnaplókból egy Azure Data Explorer-fürtbe kód írása nélkül. Ezzel az egyszerű betöltési módszerrel gyorsan megkezdheti az Azure Data Explorer lekérdezését az adatok elemzéséhez.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Táblák és betöltési leképezés létrehozása egy Azure Data Explorer-adatbázisban.
> * Formázza a bevitt adatokat egy frissítési szabályzat használatával.
> * Hozzon létre egy [eseményközpontot,](/azure/event-hubs/event-hubs-about) és csatlakoztassa az Azure Data Explorerhez.
> * Adatok at továbbítegy eseményközpont az Azure Monitor [diagnosztikai metrikák és naplók](/azure/azure-monitor/platform/diagnostic-settings) és [tevékenységnaplók.](/azure/azure-monitor/platform/activity-logs-overview)
> * A bevitt adatok lekérdezése az Azure Data Explorer használatával.

> [!NOTE]
> Hozza létre az összes erőforrást ugyanabban az Azure-ban vagy régióban. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Egy Azure Data Explorer-fürt és -adatbázis.](create-cluster-database-portal.md) Ebben az oktatóanyagban az adatbázis neve *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor adatszolgáltató: diagnosztikai metrikák és naplók és tevékenységnaplók

Tekintse meg és ismerje meg az Azure Monitor diagnosztikai metrikái és az alábbi naplók és tevékenységnaplók által biztosított adatokat. Ezek az adatsémák alapján egy betöltési folyamatot hoz létre. Ne feledje, hogy a napló minden eseménye rekordok tömbjével rendelkezik. Ez a rekordtömb az oktatóanyag későbbi részében lesz felosztva.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Példák diagnosztikai metrikákra, naplókra és tevékenységnaplókra

Az Azure diagnosztikai metrikáit, valamint a naplókat és a tevékenységnaplókat egy Azure-szolgáltatás bocsátja ki, és adatokat szolgáltat a szolgáltatás működéséről. 

# <a name="diagnostic-metrics"></a>[Diagnosztikai mutatók](#tab/diagnostic-metrics)
#### <a name="example"></a>Példa

A diagnosztikai metrikák összesítése 1 perces idősébával van összesítve. Az alábbiakban egy példa látható az Azure Data Explorer metrika-eseménysémájára a lekérdezés időtartamán:

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

Az alábbiakban egy példa látható az Azure Data Explorer [diagnosztikai betöltési naplójára:](using-diagnostic-logs.md#diagnostic-logs-schema)

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

Az Azure-tevékenységnaplók előfizetési szintű naplók, amelyek betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. Az alábbiakban egy tevékenységnapló-eseményt mutatunk be a hozzáférés ellenőrzéséhez:

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Betöltési folyamat beállítása az Azure Data Explorerben

Az Azure Data Explorer folyamatának beállítása több lépésből áll, például [a tábla létrehozásából és az adatok betöltéséből.](/azure/data-explorer/ingest-sample-data#ingest-data) Az adatokat is módosíthatja, leképezheti és frissítheti.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Csatlakozás az Azure Data Explorer webfelhasználói felületéhez

Az Azure Data Explorer *TestDatabase-adatbázisában* válassza a **Lekérdezés** lehetőséget az Azure Data Explorer webfelhasználói felületének megnyitásához.

![Lekérdezés lap](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Céltáblák létrehozása

Az Azure Monitor-naplók struktúrája nem táblázatos. Az adatokat manipulálni fogja, és az egyes eseményeket egy vagy több rekordra bontja ki. A nyers adatok at egy *közreműködésű, ActivityLogsRawRecords* nevű köztes táblába kerül a tevékenységnaplókhoz és *a DiagnosticRawRecords* diagnosztikai metrikákhoz és naplókhoz. Ekkor az adatok at manipulálni és bővíteni. Egy frissítési szabályzat használatával a kibontott adatok ezután a tevékenységnaplók, *diagnosticmetrics* diagnosztikai metrikák és *diagnosztikai naplók diagnosztikai naplók* a *ActivityLogs* táblába kerül. Ez azt jelenti, hogy két külön táblát kell létrehoznia a tevékenységnaplók betöltéséhez, és három külön táblát a diagnosztikai metrikák és naplók betöltéséhez.

Az Azure Data Explorer webfelhasználói felületén hozza létre a céltáblákat az Azure Data Explorer adatbázisban.

# <a name="diagnostic-metrics"></a>[Diagnosztikai mutatók](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Táblázatok létrehozása a diagnosztikai metrikákhoz

1. A *TestDatabase-adatbázisban* hozzon létre egy *DiagnosticMetrics* nevű táblát a diagnosztikai metrikák rekordjainak tárolásához. Használja a `.create table` következő vezérlőparancsot:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. A tábla létrehozásához válassza a **Futtatás** lehetőséget.

    ![A lekérdezés futtatása](media/ingest-data-no-code/run-query.png)

1. Hozza létre a *DiagnosticRawRecords* nevű köztes adattáblát a *TestDatabase* adatbázisban az alábbi lekérdezés sel történő adatkezeléshez. A tábla létrehozásához válassza a **Futtatás** lehetőséget.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Nulla [adatmegőrzési szabály](/azure/kusto/management/retention-policy) beállítása a köztes táblához:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Táblák létrehozása a diagnosztikai naplókhoz 

1. A *TestDatabase-adatbázisban* hozzon létre egy *DiagnosticLogs* nevű táblát a diagnosztikai naplórekordok tárolásához. Használja a `.create table` következő vezérlőparancsot:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. A tábla létrehozásához válassza a **Futtatás** lehetőséget.

1. Hozza létre a *DiagnosticRawRecords* nevű köztes adattáblát a *TestDatabase* adatbázisban az alábbi lekérdezés sel történő adatkezeléshez. A tábla létrehozásához válassza a **Futtatás** lehetőséget.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Nulla [adatmegőrzési szabály](/azure/kusto/management/retention-policy) beállítása a köztes táblához:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Táblák létrehozása a tevékenységnaplókhoz 

1. Hozzon létre egy *ActivityLogs* nevű táblát a *TestDatabase* adatbázisban a tevékenységnapló-rekordok fogadásához. A tábla létrehozásához futtassa a következő Azure Data Explorer-lekérdezést:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Hozza létre az *ActivityLogsRawRecords* nevű köztes adattáblát a *TestDatabase* adatbázisban adatkezelés céljából:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Nulla [adatmegőrzési szabály](/azure/kusto/management/retention-policy) beállítása a köztes táblához:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Táblaleképezések létrehozása

 Mivel az adatformátum a `json`, adatleképezés szükséges. A `json` leképezés minden json elérési utat egy táblaoszlop nevéhez rendel.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosztikai metrikák / Diagnosztikai naplók](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Diagnosztikai mutatók és naplók leképezése a táblázathoz

A diagnosztikai metrika és a naplóadatok táblához való hozzárendeléséhez használja a következő lekérdezést:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Tevékenységnaplók hozzárendelése a táblához

A tevékenységnapló adatainak a táblához való hozzárendeléséhez használja a következő lekérdezést:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>A metrika- és naplóadatok frissítési szabályzatának létrehozása

# <a name="diagnostic-metrics"></a>[Diagnosztikai mutatók](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Adatfrissítési szabályzat létrehozása diagnosztikai mérőszámokhoz

1. Hozzon létre egy [függvényt,](/azure/kusto/management/functions) amely kibővíti a diagnosztikai metrikarekordok gyűjteményét, hogy a gyűjtemény minden egyes értéke külön sort kapjon. Használja [`mv-expand`](/azure/kusto/query/mvexpandoperator) az operátort:
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

2. Adja hozzá a [frissítési házirendet](/azure/kusto/concepts/updatepolicy) a céltáblához. Ez a házirend automatikusan futtatja a lekérdezést a *DiagnosticRawRecords* köztes adattáblában lévő újonnan bevitt adatokon, és az eredményeket a *DiagnosticMetrics* táblába adja be:

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Adatfrissítési házirend létrehozása diagnosztikai naplókhoz

1. Hozzon létre egy [függvényt,](/azure/kusto/management/functions) amely kibővíti a diagnosztikai naplók rekordjainak gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon. Engedélyezni fogja a betöltési naplókat egy Azure Data Explorer-fürtön, és [használhatja a betöltési naplók sémáját.](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema) Egy táblát hoz létre a sikeres és a sikertelen betöltéshez, míg néhány mező üres lesz a sikeres betöltéshez (például ErrorCode). Használja [`mv-expand`](/azure/kusto/query/mvexpandoperator) az operátort:

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

2. Adja hozzá a [frissítési házirendet](/azure/kusto/concepts/updatepolicy) a céltáblához. Ez a házirend automatikusan futtatja a lekérdezést a *DiagnosticRawRecords* köztes adattáblában lévő újonnan bevitt adatokon, és az eredményeket a *DiagnosticLogs* táblába bevezeti:

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Adatfrissítési házirend létrehozása tevékenységnaplókhoz

1. Hozzon létre egy [függvényt,](/azure/kusto/management/functions) amely kibővíti a tevékenységnapló-rekordok gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon. Használja [`mv-expand`](/azure/kusto/query/mvexpandoperator) az operátort:

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

2. Adja hozzá a [frissítési házirendet](/azure/kusto/concepts/updatepolicy) a céltáblához. Ez a házirend automatikusan futtatja a lekérdezést az *ActivityLogsRawRecords* köztes adattáblában lévő újonnan bevitt adatokon, és az eredményeket az *ActivityLogs* táblába bevezeti:

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs névtér létrehozása

Az Azure diagnosztikai beállításai lehetővé teszik a metrikák és naplók exportálását egy tárfiókba vagy egy eseményközpontba. Ebben az oktatóanyagban a metrikákat és a naplókat egy eseményközponton keresztül irányítjuk. A következő lépésekben hozzon létre egy Event Hubs névteret és egy eseményközpontot a diagnosztikai metrikákhoz és naplókhoz. Az Azure Monitor létrehozza az eseményközpont *insights-operational-logs* a tevékenységnaplók.

1. Hozzon létre egy eseményközpontot egy Azure Resource Manager-sablon használatával az Azure Portalon. A cikkben ismertetett további lépések végrehajtásához kattintson a jobb gombbal az Üzembe helyezés az **Azure-ba** gombra, majd válassza **a Megnyitás új ablakban parancsot.** Az **Azure üzembe helyezése** gomb az Azure Portalra viszi.

    [![Üzembe helyezés az Azure-ban gomb](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Hozzon létre egy Event Hubs névteret és egy eseményközpontot a diagnosztikai naplókhoz.

    ![Eseményközpont létrehozása](media/ingest-data-no-code/event-hub.png)

1. Adja meg az alábbi adatokat az űrlapon. Az alábbi táblázatban nem szereplő beállításoknál használja az alapértelmezett értékeket.

    **Beállítás** | **Ajánlott érték** | **Leírás**
    |---|---|---|
    | **Előfizetés** | *Az Ön előfizetése* | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | **Erőforráscsoport** | *teszt-erőforrás-csoport* | Hozzon létre egy új erőforráscsoportot. |
    | **Helyen** | Válassza ki azt a régiót, amely a legjobban megfelel az igényeinek. | Hozza létre az Event Hubs névteret ugyanazon a helyen, mint más erőforrásokat.
    | **Névtér neve** | *AzureMonitoringData* | Válasszon egy egyedi nevet a névtér azonosításához.
    | **Event Hubs neve** | *Diagnosztikai adatok* | Az eseményközpont a névtéren belül helyezkedik el, ami egy egyedi hatókörkezelési tárolóként szolgál. |
    | **Fogyasztói csoport neve** | *adxpipeline* | Hozzon létre egy fogyasztói csoport nevét. A fogyasztói csoportokkal több fogyasztói alkalmazás is rendelkezhet az eseménystream külön nézetével. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Az Azure Monitor metrikáinak és naplóinak csatlakoztatása az eseményközponthoz

Most kell csatlakoztatnia a diagnosztikai metrikák és naplók és a tevékenységnaplók az eseményközponthoz.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosztikai metrikák / Diagnosztikai naplók](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Diagnosztikai mérőszámok és naplók csatlakoztatása az eseményközponthoz

Válassza ki azt az erőforrást, amelyből mutatókat szeretne exportálni. Számos erőforrástípus támogatja a diagnosztikai adatok exportálását, beleértve az Event Hubs névteret, az Azure Key Vaultot, az Azure IoT Hubot és az Azure Data Explorer-fürtöket. Ebben az oktatóanyagban egy Azure Data Explorer-fürtöt fogunk használni erőforrásként, áttekintjük a lekérdezésteljesítmény-metrikákat és a betöltési eredmények naplóit.

1. Válassza ki a Kusto-fürtöt az Azure Portalon.
1. Válassza **a Diagnosztikai beállítások**lehetőséget, majd a Diagnosztikai beállítások **bekapcsolása hivatkozást.** 

    ![Diagnosztikai beállítások](media/ingest-data-no-code/diagnostic-settings.png)

1. Megnyílik **a Diagnosztikai beállítások** ablaktábla. Hajtsa végre a következő lépéseket:
   1. Adja meg a diagnosztikai napló adatainak az *ADXExportedData*nevet.
   1. A **LOG csoportban**jelölje be a **SucceededIngestion** és **a FailedIngestion** jelölőnégyzetet.
   1. A **Metrikus csoportban**jelölje be a **Lekérdezés teljesítménye** jelölőnégyzetet.
   1. Jelölje be az **Eseményközpontba adatfolyam** jelölőnégyzetet.
   1. Válassza a **Konfigurálás** lehetőséget.

      ![Diagnosztikai beállítások ablaktábla](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Az **Eseményközpont kiválasztása** ablaktáblában konfigurálja, hogyan exportálhatja az adatokat a diagnosztikai naplókból a létrehozott eseményközpontba:
    1. Az **Eseményközpont névtérének kiválasztása** listában válassza az *AzureMonitoringData lehetőséget.*
    1. Az **Eseményközpont nevének kiválasztása** listában válassza a *DiagnosticData*lehetőséget.
    1. Az **Eseményközpont házirendjének nevének kiválasztása** listában válassza a **RootManagerSharedAccessKey elemet.**
    1. Válassza **az OK gombot.**

1. Kattintson a **Mentés** gombra.

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Tevékenységnaplók csatlakoztatása az eseményközponthoz

1. Az Azure Portal bal oldali menüjében válassza a **Tevékenységnapló lehetőséget.**
1. Megnyílik **a Tevékenységnapló** ablak. Válassza **az Exportálás az eseményközpontba**lehetőséget.

    ![Tevékenységnapló ablaka](media/ingest-data-no-code/activity-log.png)

1. Megnyílik **az Exportálási tevékenységnapló** ablak:
 
    ![Tevékenységnapló exportálása ablak](media/ingest-data-no-code/export-activity-log.png)

1. Az **Exportálási tevékenységnapló ablakban** kövesse az alábbi lépéseket:
      1. Válassza ki előfizetését.
      1. A **Régiók** listában válassza az **Összes kijelölése lehetőséget.**
      1. Jelölje be az **Exportálás eseményközpontba** jelölőnégyzetet.
      1. Válassza **a Szolgáltatásbusz névterének kiválasztása** lehetőséget az **Eseményközpont kiválasztása** ablaktábla megnyitásához.
      1. Az **Eseményközpont kiválasztása** ablaktáblában válassza ki az előfizetést.
      1. Az **Eseményközpont névtérének kiválasztása** listában válassza az *AzureMonitoringData lehetőséget.*
      1. Az **Eseményközpont házirendjének nevének kiválasztása** listában válassza ki az alapértelmezett eseményközpont-házirend nevét.
      1. Válassza **az OK gombot.**
      1. Az ablak bal felső sarkában válassza a **Mentés gombot.**
   Egy eseményközpont a neve *insights-operational-logs* jön létre.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Az eseményközpontokba áramló adatok megtekintése

1. Várjon néhány percet, amíg a kapcsolat definiálva van, és a tevékenységnapló exportálása az eseményközpontba befejeződik. Nyissa meg az Event Hubs névterét a létrehozott eseményközpontok megtekintéséhez.

    ![Létrehozott eseményközpontok](media/ingest-data-no-code/event-hubs-created.png)

1. Tekintse meg az eseményközpontba áramló adatokat:

    ![Az eseményközpont adatai](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Eseményközpont csatlakoztatása az Azure Data Explorerrel

Most létre kell hoznia az adatkapcsolatokat a diagnosztikai metrikákhoz, naplókhoz és tevékenységnaplókhoz.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Az adatkapcsolat létrehozása diagnosztikai metrikákhoz, naplókhoz és tevékenységnaplókhoz

1. Az Azure Data Explorer *kustodocs*nevű fürtjében válassza a bal oldali **menüadatbázisok elemét.**
1. Az **Adatbázisok** ablakban jelölje ki a *TestDatabase* adatbázist.
1. A bal oldali menüben válassza az **Adatbetöltés**lehetőséget.
1. Az **Adatbetöltés ablakban** kattintson a **+ Adatkapcsolat hozzáadása gombra.**
1. Az **Adatkapcsolat** ablakban adja meg a következő adatokat:

    ![Eseményközpont-adatkapcsolat](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosztikai metrikák / Diagnosztikai naplók](#tab/diagnostic-metrics+diagnostic-logs) 

1. Használja az alábbi beállításokat az **Adatkapcsolat** ablakban:

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Adatkapcsolat neve** | *DiagnosticsLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | **Az eseményközpont névtere** | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | **Eseményközpont** | *Diagnosztikai adatok* | A létrehozott eseményközpont. |
    | **Fogyasztói csoport** | *adxpipeline* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | | |

    Céltábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban statikus útválasztást (alapértelmezettet) fog használni, ahol megadja a tábla nevét, az adatformátumot és a leképezést. Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Tábla** | *DiagnosticRawRecords (DiagNOsztyák:* | A *TestDatabase* adatbázisban létrehozott tábla. |
    | **Adatformátum** | *JSON* | A táblázatban használt formátum. |
    | **Oszlopleképezés** | *DiagnosticRawRecordsMapping* | A *TestDatabase* adatbázisban létrehozott leképezés, amely a bejövő JSON-adatokat a *DiagnosticRawRecords* tábla oszlopnevéhez és adattípusához rendeli hozzá.|
    | | |

1. Kattintson a **Létrehozás** gombra.  

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)

1. Használja az alábbi beállításokat az **Adatkapcsolat** ablakban:

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Adatkapcsolat neve** | *ActivityLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | **Az eseményközpont névtere** | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | **Eseményközpont** | *insights-működési-naplók* | A létrehozott eseményközpont. |
    | **Fogyasztói csoport** | *$Default* | Az alapértelmezett fogyasztói csoport. Szükség esetén létrehozhat egy másik fogyasztói csoportot. |
    | | |

    Céltábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban statikus útválasztást (alapértelmezettet) fog használni, ahol megadja a tábla nevét, adatformátumát és leképezését. Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Tábla** | *ActivityLogsRawRecords* | A *TestDatabase* adatbázisban létrehozott tábla. |
    | **Adatformátum** | *JSON* | A táblázatban használt formátum. |
    | **Oszlopleképezés** | *ActivityLogsRawRecordsMapping* | A *TestDatabase* adatbázisban létrehozott leképezés, amely leképezi a bejövő JSON-adatokat az *ActivityLogsRawRecords* tábla oszlopnevéhez és adattípusához.|
    | | |

1. Kattintson a **Létrehozás** gombra.  
---

## <a name="query-the-new-tables"></a>Az új táblák lekérdezése

Most már van egy folyamat az adatok folynak. A fürtön keresztüli betöltés alapértelmezés szerint 5 percet vesz igénybe, ezért hagyja, hogy az adatok néhány percig folyjanak a lekérdezés megkezdése előtt.

# <a name="diagnostic-metrics"></a>[Diagnosztikai mutatók](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>A diagnosztikai metrikák táblájának lekérdezése

A következő lekérdezés elemzi a lekérdezés időtartamának adatait az Azure Data Explorer diagnosztikai metrikarekordjaiból:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Lekérdezés immár a következő eredményei:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[Diagnosztikai naplók](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>A diagnosztikai naplók tábla lekérdezése

Ez a folyamat egy eseményközponton keresztül hoz létre betöltéseket. Áttekintheti a lenyelések eredményeit.
A következő lekérdezés azt elemzi, hogy egy perc alatt hány `Database`betöltés `IngestionSourcePath` keletkezett, beleértve a példát és `Table` az egyes intervallumokat:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Lekérdezés immár a következő eredményei:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords (DiagNOsztyák: | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[Tevékenységnaplók](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>A tevékenységnaplók tábla lekérdezése

A következő lekérdezés elemzi az Azure Data Explorer tevékenységnapló-rekordjaiból származó adatokat:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Lekérdezés immár a következő eredményei:

|   |   |
| --- | --- |
|   |  átlagos(Időtartam-m) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan írhat még sok további lekérdezést az Azure Data Explorerből kinyert adatokra [az Azure Data Explorer írási lekérdezéseinek](write-queries.md)használatával.
* [Az Azure Data Explorer betöltési műveleteinek figyelése diagnosztikai naplók használatával](using-diagnostic-logs.md)
* [Metrikák használata a fürtállapot monitorozásához](using-metrics.md)
