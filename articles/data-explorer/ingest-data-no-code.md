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
ms.openlocfilehash: 94ecfda127422e395cf10a4fac1bb07cd0614f5c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416615"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Oktatóanyag: Az Azure Data Explorer adatokat egyetlen sor kód nélkül

Ebben az oktatóanyagban hogyan, hogyan lehet, hogy diagnosztikai és a tevékenységnapló adatainak egyetlen sor kód nélkül az adatkezelőt az Azure-fürthöz. Ez a módszer egyszerű Adatbetöltési segítségével gyorsan megkezdheti az Azure Data Explorer adatlekérdezést, az adatelemzés.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Hozzon létre táblák és a feldolgozó leképezés az adatkezelőt az Azure-adatbázisban.
> * Egy frissítési szabályzattal betöltött adatok formázásához.
> * Hozzon létre egy [Eseményközpont](/azure/event-hubs/event-hubs-about) , és csatlakoztassa az Azure Data Explorer.
> * Stream data egy eseményközpontba való [diagnosztikai naplók az Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) és [Azure Monitor-Tevékenységnaplók](/azure/azure-monitor/platform/activity-logs-overview).
> * Az Azure az adatkezelő segítségével a feldolgozott adatokat kérdezhet le.

> [!NOTE]
> Minden erőforrás létrehozása ugyanabban a régióban az Azure helye /. Ez a követelmény a Azure Monitor-diagnosztikai naplók számára.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Az Azure Data Explorer fürt és az adatbázis](create-cluster-database-portal.md). Ebben az oktatóanyagban az adatbázis neve a következő *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Azure Monitorozási adatok szolgáltató - diagnosztikai és a tevékenységeket tartalmazó naplók

Megtekintheti, és megismerheti az Azure Monitoring diagnosztikai és a tevékenység naplók által megadott adatok. Az Adatbetöltési folyamat ezen adatsémák alapján hozunk létre.

### <a name="diagnostic-logs-example"></a>A példában a diagnosztikai naplók

Az Azure diagnosztikai naplók az Azure-szolgáltatások által kibocsátott metrikák, a művelet az adott szolgáltatás vonatkozó adatokkal szolgálnak. Adatok összesített értéket jelenít meg az aggregációs időköz 1 perc. Diagnosztikai naplók eseményekhez több rekordot tartalmaz. Következő példázza az Azure Data Explorer elemzési eseménye séma meg lekérdezések időtartama:

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

Azure-Tevékenységnaplók olyan előfizetési szintű naplók tartalmazó rekordok gyűjteménye. A naplók nyújtanak az előfizetéséhez tartozó erőforrásokon végrehajtott műveletekkel kapcsolatos információk. Diagnosztikai naplókkal ellentétben a tevékenység naplók esemény van egy rekordokból álló tömbbel. Fogja meg kell osztani a rekordokból álló tömböt, az oktatóanyag későbbi részében. Következő példája egy tevékenységnapló eseményéhez hozzáférés ellenőrzéséhez:

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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Az Adatkezelőben az Azure-feldolgozási folyamat beállítása 

Az Azure Data Explorer folyamat telepítő tartalmaz különféle tartalmazó lépésekhez [tábla létrehozása és az adatok betöltési](/azure/data-explorer/ingest-sample-data#ingest-data). Módosítására, térkép, és frissítheti az adatokat.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Csatlakozás az Azure Data Explorer webes felhasználói felületen

1. Az Azure Data Explorer *AzureMonitoring* adatbázisra, válassza **lekérdezés**, kattintva nyissa meg az Azure Data Explorer webes felhasználói felületen.

    ![Lekérdezés](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Céltáblák létrehozása

Az Azure Data Explorer webes felület használatával a célként megadott táblákhoz létrehozása az Azure Data Explorer adatbázisban.

#### <a name="diagnostic-logs-table"></a>Diagnosztikai naplók tábla

1. Hozzon létre egy táblát *DiagnosticLogsRecords* a a *AzureMonitoring* adatbázis, amely megkapja a diagnosztikai naplóban rögzíti a használatával a `.create table` szabályozhatja a parancsot:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Válassza ki **futtatása** a tábla létrehozásához.

    ![Lekérdezés futtatása](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Tevékenységnaplók táblák

A tevékenység naplók struktúra nem táblázatos, mert szüksége lesz az adatok feldolgozására, és bontsa ki minden egyes esemény egy vagy több rekordot. A nyers adatokat fog elemezhető, köztes táblához *ActivityLogsRawRecords*. Ugyanakkor az adatok kezelhetők és kibontva. A kibontott adatok be majd lesz töltve a *ActivityLogsRecords* táblázatba egy frissítési szabályzatot. Ezért szüksége, hozzon létre két külön táblát tevékenység naplók támogatunk.

1. Hozzon létre egy táblát *ActivityLogsRecords* a a *AzureMonitoring* adatbázis, amely megkapja a tevékenység a rekordok naplózása. Futtassa a következő Azure adatkezelő lekérdezést a tábla létrehozásához:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. A köztes adatok tábla létrehozásához *ActivityLogsRawRecords* a a *AzureMonitoring* adatkezelés adatbázist:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Táblaleképezések létrehozása

 Az adatok formátuma `json`, ezért az adatok leképezése kötelező. A `json` leképezés minden json-útvonal képez le egy tábla oszlop neve.

#### <a name="diagnostic-logs-table-mapping"></a>Diagnosztikai naplók tábla hozzárendelése

Az adatok leképezése a tábla használja a következő lekérdezést:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
{"column":"Timestamp","path":"$.time"},
{"column":"ResourceId","path":"$.resourceId"},
{"column":"MetricName","path":"$.metricName"},
{"column":"Count","path":"$.count"},
{"column":"Total","path":"$.total"},
{"column":"Minimum","path":"$.minimum"},
{"column":"Maximum","path":"$.maximum"},
{"column":"Average","path":"$.average"},
{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Tevékenységnaplók tábla hozzárendelése

Az adatok leképezése a tábla használja a következő lekérdezést:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Frissítési szabályzat létrehozása

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

2. Adjon hozzá egy [szabályzat frissítése](/azure/kusto/concepts/updatepolicy) céltáblába. Automatikusan elindul a lekérdezés az újonnan betöltött adatokat a *ActivityLogsRawRecords* köztes adatok táblát, és az eredmények betöltési *ActivityLogsRecords* tábla:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Hozzon létre egy Event Hub-Namespace

Az Azure diagnosztikai naplók tárfiókba vagy egy Eseményközpontba való exportálása metrikák engedélyezése. Ebben az oktatóanyagban azt irányíthatja a metrikákat egy Eseményközpont-n keresztül. Az Event Hubs-Namespace és az Eseményközpont fog létrehozni a diagnosztikai naplók a következő lépések számára. Az Azure figyelési fog létrehozni az Event Hubs *insights-operational-logs* tevékenységi naplóit.

1. Létrehoz egy eseményközpontot, az Azure Portalon egy Azure Resource Manager-sablon használatával. Az üzembe helyezés indításához kattintson a következő gombra. Kattintson a jobb gombbal, és válassza ki **Megnyitás új ablakban**, ezért kövesse a cikkben ismertetett lépések a többi. A **üzembe helyezés az Azure** gombra, akkor megnyílik az Azure Portalra.

    [![Üzembe helyezés az Azure-ban](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Hozzon létre egy Event Hub-Namespace és a egy Eseményközpontba, a diagnosztikai naplókhoz.

    ![Eseményközpont létrehozása](media/ingest-data-no-code/event-hub.png)

    Adja meg az alábbi adatokat az űrlapon. Az alábbi táblázatban fel nem sorolt beállítások esetében használja az alapértelmezett értéket.

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *test-resource-group* | Hozzon létre egy új erőforráscsoportot. |
    | Hely | Válassza ki a régiót, amely a legjobban az igényeinek. | Az eseményközpont-névtér létrehozása az egyéb erőforrások megegyező helyen.
    | Névtér neve | *AzureMonitoringData* | Válasszon egy egyedi nevet a névtér azonosításához.
    | Event Hubs neve | *DiagnosticLogsData* | Az eseményközpont a névtéren belül helyezkedik el, ami egy egyedi hatókörkezelési tárolóként szolgál. |
    | Fogyasztói csoport neve | *adxpipeline* | Hozzon létre egy fogyasztói csoport neve. Lehetővé teszi, hogy több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Csatlakozás Azure Monitoring naplók az Eseményközponthoz

### <a name="diagnostic-logs-connection-to-event-hub"></a>Diagnosztikai naplók kapcsolat eseményközpontba

Válasszon ki egy erőforrást, ahonnan a metrikák exportálása. Többféle erőforrás, beleértve az Event Hub-Namespace, KeyVault, az IoT Hub és az Azure Data Explorer fürt exportálása diagnosztikai naplók engedélyezése. Ebben az oktatóanyagban az Azure Data Explorer fürt használjuk a erőforrásként.

1. Válassza ki a Kusto-fürtöt az Azure Portalon.

    ![Diagnosztikai beállítások](media/ingest-data-no-code/diagnostic-settings.png)

1. Válassza ki **diagnosztikai beállítások** bal oldali menüből.
1. Kattintson a **diagnosztika bekapcsolása** hivatkozásra. A **diagnosztikai beállítások** ablak nyílik meg.

    ![Diagnosztikai beállítások ablak](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Az a **diagnosztikai beállítások** panelen:
    1. A diagnosztikai naplóadatokat neve: *ADXExportedData*
    1. Válassza ki **AllMetrics** (nem kötelező) jelölőnégyzetet.
    1. Válassza ki **Stream egy eseményközpontba** jelölőnégyzetet.
    1. Kattintson a **konfigurálása**

1. Az a **válassza ki az eseményközpont** panelen konfigurálja a létrehozott eseményközpont exportálása:
    1. **Eseményközpont névterének kijelölése** *AzureMonitoringData* legördülő listából.
    1. **Válassza ki eseményközpont neve** *diagnosticlogsdata* legördülő listából.
    1. **Válassza ki eseményközpont szabályzatának neve** legördülő listából.
    1. Kattintson az **OK** gombra.

1. Kattintson a **Save** (Mentés) gombra. Az esemény eseményközpont-névtér, a név és a szabályzat neve megjelenik az ablakban.

    ![Diagnosztikai beállítások mentése](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Tevékenységnaplók eseményközpontba kapcsolat

1. Az Azure portal bal oldali menüben válassza ki a **tevékenységnapló**
1. **Tevékenységnapló** ablak nyílik meg. **Kattintson az Exportálás eseményközpontba**

    ![Tevékenységnapló](media/ingest-data-no-code/activity-log.png)

1. Az a **tevékenységnapló exportálása** ablakban:
 
    ![Tevékenységnapló exportálása](media/ingest-data-no-code/export-activity-log.png)

    1. Válassza ki előfizetését.
    1. A **régiók** legördülő menüben válassza a **az összes kijelölése**
    1. Válassza ki **exportálása az eseményközpontba** jelölőnégyzetet.
    1. Kattintson a **válassza ki a service bus-névtér** megnyitásához a **válassza ki az eseményközpont** ablaktáblán.
    1. Az a **válassza ki az eseményközpont** ablaktáblán válassza ki a legördülő menük: az előfizetés, az esemény-névtér *AzureMonitoringData*, és az alapértelmezett eseményközpont szabályzatának nevét.
    1. Kattintson az **OK** gombra.
    1. Kattintson a **mentése** az ablak jobb felső oldalán. Egy eseményközpont nevű *insights-operational-logs* jön létre.

### <a name="see-data-flowing-to-your-event-hubs"></a>Tekintse meg az az Event hubs szolgáltatásba áramló adatok

1. Várjon néhány percet, amíg a kapcsolat definiálva van, és a Tevékenységnaplói exportálás eseményközpontba befejeződött. Nyissa meg a megtekintéséhez az event hubs létrehozott Eseményközpont-névteret.

    ![Az Event Hubs létrehozva](media/ingest-data-no-code/event-hubs-created.png)

1. Tekintse meg az Eseményközpont adatforgalmát:

    ![Event Hubs data](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Eseményközpont csatlakozhat az Azure Data Explorer

### <a name="diagnostic-logs-data-connection"></a>Diagnosztikai naplók adatkapcsolat

1. Az Azure Data Explorer fürt *kustodocs*válassza **adatbázisok** a bal oldali menüben.
1. Az a **adatbázisok** ablakban válassza ki az adatbázis nevét *AzureMonitoring*
1. A bal oldali menüben válassza ki a **adatbetöltés**
1. Az a **adatbetöltés** ablakban kattintson a **+ adatkapcsolat hozzáadása**
1. Az a **adatkapcsolat** ablakban adja meg a következőket:

    ![Eseményközpont-kapcsolat](media/ingest-data-no-code/event-hub-data-connection.png)

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *DiagnosticsLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | Eseményközpont-névtér | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | Eseményközpont | *diagnosticlogsdata* | A létrehozott eseményközpont. |
    | Fogyasztói csoport | *adxpipeline* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | | |

    Céloldali tábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban használhatja a statikus útválasztás (alapértelmezett), amelyben meghatározhatja azokat a táblázat neve, a formátumot, és a leképezés. Ezért hagyja **adataimat magában foglalja az útválasztási információ** nincs bejelölve.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *DiagnosticLogsRecords* | A létrehozott tábla *AzureMonitoring* adatbázis. |
    | Adatformátum | *JSON* | Táblázat formázása. |
    | Oszlopleképezés | *DiagnosticLogsRecordsMapping* | A létrehozott hozzárendelést *AzureMonitoring* adatbázis, amely bejövő JSON-adatokat az oszlop nevükkel és adattípusukkal *DiagnosticLogsRecords*.|
    | | |

1. Kattintson a **Create** (Létrehozás) gombra  

### <a name="activity-logs-data-connection"></a>Tevékenységnaplók adatkapcsolat

Ismételje meg a [diagnosztikai naplók adatkapcsolat](#diagnostic-logs-data-connection) hozhat létre a tevékenység szakaszban adatkapcsolat naplózza.

1. Helyezze be a következő beállításokat a **adatkapcsolat** ablakban:

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *ActivityLogsConnection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | Eseményközpont-névtér | *AzureMonitoringData* | A korábban a névtér azonosításához választott név. |
    | Eseményközpont | *insights-operational-logs* | A létrehozott eseményközpont. |
    | Fogyasztói csoport | *$Default* | Az alapértelmezett felhasználói csoport. Ha szükséges, létrehozhat egy másik fogyasztói csoportot. |
    | | |

    Céloldali tábla:

    Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben az oktatóanyagban használhatja a statikus útválasztás (alapértelmezett), amelyben meghatározhatja azokat a táblázat neve, a formátumot, és a leképezés. Ezért hagyja **adataimat magában foglalja az útválasztási információ** nincs bejelölve.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *ActivityLogsRawRecords* | A létrehozott tábla *AzureMonitoring* adatbázis. |
    | Adatformátum | *JSON* | Táblázat formázása. |
    | Oszlopleképezés | *ActivityLogsRawRecordsMapping* | A létrehozott hozzárendelést *AzureMonitoring* adatbázis, amely bejövő JSON-adatokat az oszlop nevükkel és adattípusukkal *ActivityLogsRawRecords*.|
    | | |

1. Kattintson a **Create** (Létrehozás) gombra  

## <a name="query-the-new-tables"></a>Az új táblák lekérdezése

Rendelkezik egy tárfiókba adatokkal. Keresztül a fürt feldolgozó alapértelmezés szerint 5 percet vesz igénybe, így lehetővé teszi az adatfolyam néhány percet, mielőtt elkezdené a lekérdezés.

### <a name="diagnostic-logs-table-query-example"></a>Diagnosztikai naplók tábla lekérdezési példa

A következő lekérdezés az Adatkezelőbe az Azure diagnosztikai napló rekordok időtartamának adatait elemzi:

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

### <a name="activity-logs-table-query-example"></a>Tevékenység naplók tábla lekérdezési példa

A következő lekérdezés az adatkezelőt az Azure log tevékenységrekordok származó adatokat elemzi:

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

Ismerje meg, számos további lekérdezések írására, a következő cikk segítségével Azure adatkezelő kinyert adatokkal:

> [!div class="nextstepaction"]
> [Az Azure Data Explorer lekérdezéseket írni](write-queries.md)
