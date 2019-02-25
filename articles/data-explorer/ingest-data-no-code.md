---
title: 'Oktatóanyag: A diagnosztikai és a tevékenység napló adatokat az Azure Data Explorer egyetlen sor kód nélkül'
description: Ebben az oktatóanyagban elsajátíthatja, hogyan lehet adatokat betölteni az adatokat az Adatkezelőbe az Azure egy sor kódot és a lekérdezés nélkül.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: a678722666146fdf22e88680ab414b09d2a7ffaa
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749936"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Oktatóanyag: Az Azure Data Explorer adatokat egyetlen sor kód nélkül

Ebben az oktatóanyagban fog szól, hogyan képes feldolgozni a diagnosztikai adatokat és az adatkezelőt az Azure-fürthöz Tevékenységnaplók kódírás nélkül. Egyszerű Adatbetöltési ezzel a módszerrel gyorsan megkezdheti a lekérdezése az Azure Data Explorer adatelemzés céljából.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre táblák és a feldolgozó leképezés az adatkezelőt az Azure-adatbázisban.
> * A feldolgozott adatok formázásához egy frissítési szabályzattal.
> * Hozzon létre egy [eseményközpont](/azure/event-hubs/event-hubs-about) , és csatlakoztassa az Azure Data Explorer.
> * Stream data egy eseményközpontba való [diagnosztikai naplók az Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) és [Azure Monitor-Tevékenységnaplók](/azure/azure-monitor/platform/activity-logs-overview).
> * A feldolgozott adatok lekérdezése Azure Data Explorer használatával.

> [!NOTE]
> Az azonos Azure-beli hely vagy régió összes erőforrás létrehozása. Ez a követelmény a Azure Monitor-diagnosztikai naplók számára.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Az Azure Data Explorer fürt és az adatbázis](create-cluster-database-portal.md). Ebben az oktatóanyagban az adatbázis neve a következő *AzureMonitoring*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Az Azure Monitor-adatszolgáltató: diagnosztikai és Tevékenységnaplók

Megtekintheti, és megismerheti az Azure Monitor diagnosztikai és a tevékenység naplók által megadott adatok. Az Adatbetöltési folyamat ezen adatsémák alapján hozunk létre.

### <a name="diagnostic-logs-example"></a>A példában a diagnosztikai naplók

Az Azure diagnosztikai naplók az Azure-szolgáltatások által kibocsátott metrikák, a művelet az adott szolgáltatás vonatkozó adatokkal szolgálnak. Adatok és a egy 1 perces időfelbontási összesítve. A diagnosztikai naplóban szereplő minden egyes esemény egy bejegyzést tartalmaz. Íme egy példa egy Azure Data Explorer metrika eseménysémája meg lekérdezések időtartama:

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Tevékenység naplók példa

Azure-Tevékenységnaplók olyan előfizetési szintű naplók, amelyek tartalmazzák a rekordok gyűjteményei. A naplók nyújtanak az előfizetéséhez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. Diagnosztikai naplókkal ellentétben egy tevékenységnapló szereplő minden esemény van egy rekordokból álló tömbbel. Meg kell osztani a rekordokból álló tömböt, az oktatóanyag későbbi részében. Íme egy példa egy tevékenységnapló eseményt hozzáférés ellenőrzése:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Az Adatkezelőben az Azure-feldolgozási folyamat beállítása

Például egy Azure Data Explorer-folyamat beállítása magában foglalja számos lépést [tábla létrehozása és az adatok betöltési](/azure/data-explorer/ingest-sample-data#ingest-data). Módosítására, térkép, és frissítheti az adatokat.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Csatlakozás az Azure Data Explorer webes felhasználói felületen

Az Azure Data Explorer *AzureMonitoring* adatbázisra, válassza **lekérdezés** az Azure Data Explorer webes felhasználói felületének megnyitásához.

![Lekérdezés lap](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>A céloldali tábla létrehozásához

Az Azure Data Explorer webes felhasználói felülete segítségével a cél táblák létrehozása az Azure Data Explorer adatbázisban.

#### <a name="the-diagnostic-logs-table"></a>A diagnosztikai naplók tábla

1. Az a *AzureMonitoring* adatbázisra, és hozzon létre egy táblát nevű *DiagnosticLogsRecords* tárolására a diagnosztikai napló rögzíti. Használja a következő `.create table` szabályozhatja a parancsot:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Válassza ki **futtatása** a tábla létrehozásához.

    ![A lekérdezés futtatása](media/ingest-data-no-code/run-query.png)

#### <a name="the-activity-logs-tables"></a>A Tevékenységnaplók táblák

Tevékenységeket tartalmazó naplók a struktúra nem táblázatos, mert szüksége lesz az adatok feldolgozására, és bontsa ki minden egyes esemény egy vagy több rekordot. A nyers adatokat fog kell egy köztes nevű táblát betöltött *ActivityLogsRawRecords*. Ugyanakkor az adatok kezelhetők és kibontva. A kibontott adatok be majd lesz töltve a *ActivityLogsRecords* tábla egy frissítési szabályzattal. Ez azt jelenti, hogy kell két külön táblák létrehozása a tevékenységnaplókat tölt.

1. Hozzon létre egy táblát nevű *ActivityLogsRecords* a a *AzureMonitoring* adatbázis tevékenység naplórekordok fogadásához. A tábla létrehozásához futtassa a következő Azure adatkezelő lekérdezés:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. A köztes adatok nevű táblát hozzon létre *ActivityLogsRawRecords* a a *AzureMonitoring* adatkezelés adatbázist:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Táblaleképezések létrehozása

 Mivel az adatok formátumát `json`, adatok leképezése kötelező. A `json` leképezés minden json-útvonal képez le egy tábla oszlop neve.

#### <a name="table-mapping-for-diagnostic-logs"></a>Tábla hozzárendelése a diagnosztikai naplókhoz

A diagnosztikai naplók adatok leképezése a táblában, használja a következő lekérdezést:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>A következő tevékenységeket tartalmazó naplók tábla hozzárendelése

A vizsgálati naplók adatok leképezése a táblában, használja a következő lekérdezést:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-activity-logs-data"></a>A vizsgálati naplók adatok frissítési szabályzat létrehozása

1. Hozzon létre egy [függvény](/azure/kusto/management/functions) , amely kibontja a rekordok gyűjteményei, hogy a gyűjteményben szereplő összes értékhez kap külön sorba. Használja a [ `mvexpand` ](/azure/kusto/query/mvexpandoperator) operátor:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Adja hozzá a [szabályzat frissítése](/azure/kusto/concepts/updatepolicy) céltáblába. Ez a szabályzat automatikusan az a lekérdezés futtatása az újonnan betöltött adatokat a *ActivityLogsRawRecords* köztes adatok táblát, és az eredmények betöltési a *ActivityLogsRecords* tábla:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Az Azure Event Hubs-névtér létrehozása

Az Azure diagnosztikai naplók engedélyezése egy tárfiókba vagy egy eseményközpontba való exportálása metrikákat. Ebben az oktatóanyagban azt fogja irányítani a metrikákat egy eseményközpont-n keresztül. Létre fog hozni a következő lépéseket az Event Hubs-névtér és eseményközpont a diagnosztikai naplókhoz. Az Azure Monitor fog létrehozni az event hubs *insights-operational-logs* a vizsgálati naplók számára.

1. Event hub létrehozása az Azure Portalon egy Azure Resource Manager-sablon használatával. Kövesse a cikkben ismertetett lépések a többi, kattintson a jobb gombbal a **üzembe helyezés az Azure** gombra, és válassza ki **Megnyitás új ablakban**. A **üzembe helyezés az Azure** gombra, akkor megnyílik az Azure Portalra.

    [![Deploy to Azure gombbal](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, a diagnosztikai naplókhoz.

    ![Eseményközpont létrehozása](media/ingest-data-no-code/event-hub.png)

1. Adja meg az alábbi adatokat az űrlapon. A következő táblázatban nem szereplő bármely beállításainak megadásához használja az alapértelmezett értékeket.

    **Beállítás** | **Ajánlott érték** | **Leírás**
    |---|---|---|
    | **Előfizetés** | *Az Ön előfizetése* | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | **Erőforráscsoport** | *test-resource-group* | Hozzon létre egy új erőforráscsoportot. |
    | **Hely** | Válassza ki a régiót, amely a legjobban az igényeinek. | Az Event Hubs-névtér létrehozása az egyéb erőforrások ugyanazon a helyen.
    | **Namespace neve** | *AzureMonitoringData* | Válasszon egy egyedi nevet a névtér azonosításához.
    | **Eseményközpont neve** | *DiagnosticLogsData* | Az eseményközpont a névtéren belül helyezkedik el, ami egy egyedi hatókörkezelési tárolóként szolgál. |
    | **Fogyasztói csoport neve** | *adxpipeline* | Hozzon létre egy fogyasztói csoport neve. A fogyasztói csoportokkal több fogyasztói alkalmazás is rendelkezhet az eseménystream külön nézetével. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Csatlakozás az Azure Monitor naplóira az eseményközpont

Most, csatlakoznia kell a diagnosztikai naplók és a vizsgálati naplók az eseményközpontba.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Diagnosztikai naplók csatlakozni az eseményközpont

Válasszon ki egy erőforrást, ahonnan a metrikák exportálása. Többféle erőforrás támogatja a diagnosztikai naplók, beleértve az Event Hubs-névtér, az Azure Key Vault, Azure IoT Hub és az Azure Data Explorer fürtök exportálása. Ebben az oktatóanyagban egy Azure Data Explorer fürtöt erőforrásként a fogjuk használni.

1. Válassza ki a Kusto-fürtöt az Azure Portalon.
1. Válassza ki **diagnosztikai beállítások**, majd válassza ki a **diagnosztika bekapcsolása** hivatkozásra. 

    ![Diagnosztikai beállítások](media/ingest-data-no-code/diagnostic-settings.png)

1. A **diagnosztikai beállítások** panel nyílik meg. Az alábbi lépéseket:
    1. Adjon a diagnosztikai naplóadatokat neve *ADXExportedData*.
    1. A **METRIKA**, jelölje be a **AllMetrics** (nem kötelező) jelölőnégyzetet.
    1. Válassza ki a **Stream egy eseményközpontba** jelölőnégyzetet.
    1. Válassza ki **konfigurálása**.

    ![Diagnosztikai beállítások panel](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Az a **válassza ki az eseményközpont** ablaktáblán adatainak exportálása a diagnosztikai naplók az event hubs létrehozott konfigurálása:
    1. Az a **eseményközpont névterének kijelölése** listájáról válassza ki a *AzureMonitoringData*.
    1. Az a **válassza eseményközpontnév** listájáról válassza ki a *diagnosticlogsdata*.
    1. Az a **válassza eseményközpont szabályzatának neve** listájáról válassza ki a **RootManagerSharedAccessKey**.
    1. Kattintson az **OK** gombra.

1. Kattintson a **Mentés** gombra. Az eseményközpont névtér nevét és a házirend neve a ablakban jelenik meg.

    ![Diagnosztikai beállítások mentése](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="connect-activity-logs-to-your-event-hub"></a>A Tevékenységnaplók csatlakozni az eseményközpont

1. Az Azure portal bal oldali menüben válassza ki a **tevékenységnapló**.
1. A **tevékenységnapló** ablak nyílik meg. Válassza ki **exportálás eseményközpontba**.

    ![Tevékenységnapló ablak](media/ingest-data-no-code/activity-log.png)

1. A **tevékenységnapló exportálása** ablak nyílik meg:
 
    ![Exportálás tevékenységnapló ablak](media/ingest-data-no-code/export-activity-log.png)

1. Az a **tevékenységnapló exportálása** ablakban tegye a következőket:
      1. Válassza ki előfizetését.
      1. Az a **régiók** menüben válassza ki **válassza ki az összes**.
      1. Válassza ki a **exportálása az eseményközpontba** jelölőnégyzetet.
      1. Válasszon **válassza ki a service bus-névtér** megnyitásához a **válassza ki az eseményközpont** ablaktáblán.
      1. Az a **válassza ki az eseményközpont** ablaktáblán válassza ki az előfizetését.
      1. Az a **eseményközpont névterének kijelölése** listájáról válassza ki a *AzureMonitoringData*.
      1. Az a **válassza eseményközpont szabályzatának neve** listájához, válassza ki az alapértelmezett eseményközpont szabályzatának nevét.
      1. Kattintson az **OK** gombra.
      1. Az ablak bal felső sarkában válassza **mentése**.
   Egy eseményközpont nevű *insights-operational-logs* jön létre.

### <a name="see-data-flowing-to-your-event-hubs"></a>Tekintse meg az az event hubs szolgáltatásba áramló adatok

1. Várjon néhány percet, amíg a kapcsolat definiálva van, a tevékenységnapló Exportálás az eseményközpontba befejeződött. Nyissa meg az Event Hubs-névtér az event hubs létrehozott megtekintéséhez.

    ![Az Event hubs létrehozva](media/ingest-data-no-code/event-hubs-created.png)

1. Tekintse meg az eseményközpont adatforgalmát:

    ![Event hubs-adatok](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Egy eseményközpont csatlakozhat az Azure Data Explorer

Most meg kell a adatkapcsolatokat, a diagnosztikai naplók és a vizsgálati naplók létrehozása.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>A diagnosztikai naplók számára az adatkapcsolat létrehozása

1. Az Azure Data Explorer fürt nevű *kustodocs*válassza **adatbázisok** a bal oldali menüben.
1. Az a **adatbázisok** ablakban válassza ki a *AzureMonitoring* adatbázis.
1. A bal oldali menüben válassza ki a **adatbetöltés**.
1. Az a **adatbetöltés** ablakban kattintson a **+ adatkapcsolat hozzáadása**.
1. Az a **adatkapcsolat** ablakban adja meg a következőket:

    ![Event hub-adatkapcsolat](media/ingest-data-no-code/event-hub-data-connection.png)

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Adatok kapcsolat neve** | *DiagnosticsLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | **Eseményközpont-névtér** | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | **Event hub** | *diagnosticlogsdata* | A létrehozott eseményközpont. |
    | **Fogyasztói csoport** | *adxpipeline* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | | |

    Céloldali tábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. A jelen oktatóanyag esetében statikus útválasztás (alapértelmezett), amelyben meghatározhatja azokat a táblázat neve, az adatok formátumát és a leképezést fogja használni. Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Tábla** | *DiagnosticLogsRecords* | A létrehozott tábla a *AzureMonitoring* adatbázis. |
    | **Adatformátum** | *JSON* | A formátumot használja a táblában. |
    | **Oszlopleképezés** | *DiagnosticLogsRecordsMapping* | A létrehozott hozzárendelést a *AzureMonitoring* adatbázis, amely bejövő JSON-adatokat az oszlop nevükkel és adattípusukkal a *DiagnosticLogsRecords* tábla.|
    | | |

1. Kattintson a **Létrehozás** gombra.  

### <a name="create-the-data-connection-for-activity-logs"></a>A Tevékenységnaplók az adatkapcsolat létrehozása

Ismételje meg a [a diagnosztikai naplók számára az adatkapcsolat létrehozása](#diagnostic-logs-data-connection) szakasz a tevékenységi naplóit az adatkapcsolat létrehozása.

1. Használja a következő beállításokat a **adatkapcsolat** ablakban:

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Adatok kapcsolat neve** | *ActivityLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | **Eseményközpont-névtér** | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | **Event hub** | *insights-operational-logs* | A létrehozott eseményközpont. |
    | **Fogyasztói csoport** | *$Default* | Az alapértelmezett felhasználói csoport. Ha szükséges, létrehozhat egy másik fogyasztói csoportot. |
    | | |

    Céloldali tábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban statikus útválasztás (alapértelmezett), amelyben meghatározhatja azokat a táblázat neve, az adatok formátuma és hozzárendelésének fog használni. Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | **Tábla** | *ActivityLogsRawRecords* | A létrehozott tábla a *AzureMonitoring* adatbázis. |
    | **Adatformátum** | *JSON* | A formátumot használja a táblában. |
    | **Oszlopleképezés** | *ActivityLogsRawRecordsMapping* | A létrehozott hozzárendelést a *AzureMonitoring* adatbázis, amely bejövő JSON-adatokat az oszlop nevükkel és adattípusukkal a *ActivityLogsRawRecords* tábla.|
    | | |

1. Kattintson a **Létrehozás** gombra.  

## <a name="query-the-new-tables"></a>Az új táblák lekérdezése

Most már egy folyamatot az adat vizsgálható. Keresztül a fürt feldolgozó alapértelmezés szerint 5 percet vesz igénybe, így lehetővé teszi az adatok áramlását lekérdezéséhez megkezdése előtt néhány percig.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Egy példa a diagnosztikai naplók tábla lekérdezése

A következő lekérdezés az Adatkezelőben az Azure diagnosztikai napló rögzíti az időtartam adatait elemzi:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Lekérdezés eredményei:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>Egy példa a tevékenység naplók tábla lekérdezése

A következő lekérdezés az Adatkezelőben az Azure log tevékenységrekordok származó adatokat elemzi:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Lekérdezés eredményei:
|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>További lépések

Ismerje meg, számos további lekérdezések írása a következő cikk az Azure Data Explorer kinyert adatok:

> [!div class="nextstepaction"]
> [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)
