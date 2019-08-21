---
title: Az adatüzemek monitorozása Azure Monitor használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Monitort a Data Factory folyamatok figyeléséhez, ha engedélyezi a diagnosztikai naplókat a Azure Data Factory információi alapján.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: 6bad74d33f5d50bb7a35de69927bf97daad07798
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326851"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Az adatgyárak riasztása és monitorozása Azure Monitor használatával
A Felhőbeli alkalmazások számos mozgó részből összetettek. A figyelési szolgáltatással biztosítható, hogy az alkalmazás kifogástalan állapotban maradjon. Emellett segít elhárítani a lehetséges problémákat vagy a múltbeli hibák elhárítását. Emellett a figyelési adatok segítségével mélyebb elemzéseket nyerhet az alkalmazásról. Ez az információ segít az alkalmazások teljesítményének vagy karbantartásának javításában, vagy automatizálhatja azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

A Azure Monitor a Microsoft Azure legtöbb szolgáltatásához biztosít alapszintű infrastruktúra-metrikákat és-naplókat. Részletekért lásd: a [figyelés áttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Az Azure diagnosztikai naplói olyan erőforrások által kibocsátott naplók, amelyek gazdag és gyakori adatmennyiséget biztosítanak az adott erőforrás működéséről. Data Factory a Azure Monitorban a diagnosztikai naplók kimenete.

## <a name="persist-data-factory-data"></a>Data Factory-adatmegőrzés
Data Factory csak a 45 napig tárolja a folyamat futási értékeit. Ha több mint 45 napig kívánja megőrizni a feldolgozási folyamatot, a Azure Monitor használatával nem csak a diagnosztikai naplókat tudja átirányítani az elemzéshez, megtarthatja őket egy Storage-fiókba, így a választott időtartamra vonatkozóan a gyári adatok is megmaradnak.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

* A naplózáshoz vagy a manuális ellenőrzéshez mentse őket egy **Storage** -fiókba. Megadhatja a megőrzési időt (napokban) a diagnosztikai beállítások használatával.
* A harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás (például a Power BI) betöltéséhez továbbíthatja őket **Event Hubs** .
* Elemezze őket **log Analytics**

Használhat olyan Storage-fiókot vagy Event hub-névteret, amely nem ugyanabban az előfizetésben található, mint a naplókat kibocsátó erőforrás. A beállítást konfiguráló felhasználónak rendelkeznie kell a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) hozzáféréssel mindkét előfizetéshez.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások
A nem számítási erőforrások diagnosztikai naplói a diagnosztikai beállítások használatával konfigurálhatók. Erőforrás-vezérlő diagnosztikai beállításai:

* A diagnosztikai naplók küldése (Storage-fiók, Event Hubs vagy Azure Monitor napló).
* A rendszer mely naplózási kategóriákat küldje el.
* Mennyi ideig kell megőrizni az egyes naplók kategóriáit egy Storage-fiókban.
* Egy nulla napnyi adatmegőrzéshez azt jelenti, hogy naplókat tartják örökre. Ellenkező esetben az érték lehet minden olyan 1 és 2147483647 között eltelt napok számát.
* Ha adatmegőrzési házirend van beállítva, de a naplók tárolása egy Storage-fiókban le van tiltva (például csak Event Hubs vagy Azure Monitor naplók beállításai vannak kiválasztva), az adatmegőrzési szabályzatok nem lépnek érvénybe.
* Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén törli a házirendet. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Diagnosztikai naplók engedélyezése REST API-kon keresztül

Diagnosztikai beállítás létrehozása vagy frissítése Azure Monitor REST API

**Kérés**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Fejlécek**
* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* A `{resource-id}` helyére írja be annak az erőforrásnak az erőforrás-azonosítóját, amelynek a diagnosztikai beállításait módosítani szeretné. További információ az [erőforráscsoportok használatával az Azure-erőforrások kezeléséhez](../azure-resource-manager/manage-resource-groups-portal.md).
* Állítsa be `Content-Type` a `application/json`fejlécet a következőre:.
* Állítsa be az engedélyezési fejlécet egy olyan JSON webes tokenre, amelyet a Azure Active Directorytól kapott. További információ: [kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md).

**Törzs**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Tulajdonság | Típus | Leírás |
| --- | --- | --- |
| storageAccountId |String | Annak a Storage-fióknak az erőforrás-azonosítója, amelyre a diagnosztikai naplókat el szeretné küldeni |
| serviceBusRuleId |Sztring | A Service Bus-névtér Service Bus-szabályának azonosítója, amelyben Event Hubs létrehozni a folyamatos átviteli diagnosztikai naplókhoz. A szabály azonosítója a következő formátumú: "{Service Bus erőforrás-azonosító}/authorizationrules/{Key neve}".|
| workspaceId | Összetett típus | Metrikus idő típusú gabona és adatmegőrzési szabályzatok tömbje. Jelenleg ez a tulajdonság üres. |
|metrics| A folyamat által a meghívott folyamatnak átadni kívánt folyamat paramétereinek értéke| Egy JSON-objektum-hozzárendelési paraméter neve az argumentum értékéhez |
| logs| Összetett típus| Az erőforrástípus diagnosztikai naplójának kategóriája. Az erőforráshoz tartozó diagnosztikai naplók listájának beszerzéséhez először végezze el a diagnosztikai beállítások beolvasása műveletet. |
| category| String| A naplózási kategóriák és a megőrzési szabályzatok tömbje |
| timeGrain | Sztring | Az ISO 8601-es időtartamú formátumban rögzített mérőszámok részletessége. PT1M kell lennie (egy perc)|
| enabled| Logikai | Meghatározza, hogy engedélyezve van-e az adott metrika vagy naplózási kategória gyűjteménye ehhez az erőforráshoz|
| retentionPolicy| Összetett típus| A metrika vagy a napló kategóriájának adatmegőrzési szabályát ismerteti. Csak a Storage-fiók beállítására szolgál.|
| days| Int| A metrikák vagy naplók megőrzésére szolgáló napok száma. A 0 érték határozatlan ideig megőrzi a naplókat. Csak a Storage-fiók beállítására szolgál. |

**Válasz**

200 OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

A diagnosztikai beállításokkal kapcsolatos információk beolvasása Azure Monitor REST API

**Kérés**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Fejlécek**
* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* A `{resource-id}` helyére írja be annak az erőforrásnak az erőforrás-azonosítóját, amelynek a diagnosztikai beállításait módosítani szeretné. További információ az erőforráscsoportok használatával az Azure-erőforrások kezeléséhez.
* Állítsa be `Content-Type` a `application/json`fejlécet a következőre:.
* Állítsa be az engedélyezési fejlécet olyan JSON Web Tokenre, amelyet a Azure Active Directorytól kapott. További információ: kérelmek hitelesítése.

**Válasz**

200 OK

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[További információ itt](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Naplók sémája & eseményekhez

### <a name="azure-monitor-schema"></a>Azure Monitor séma

#### <a name="activity-run-logs-attributes"></a>Tevékenység-futtatási naplók attribútumai

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Tulajdonság | Típus | Leírás | Példa |
| --- | --- | --- | --- |
| Szint |Sztring | A diagnosztikai naplók szintje. A 4. szint mindig a tevékenység-futtatási naplók esetében használható. | `4`  |
| correlationId |Sztring | Egy adott kérelem végpontok közötti nyomon követésére szolgáló egyedi azonosító | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sztring | Az esemény időpontja TimeSpan, UTC formátumban`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| A tevékenység futtatásának azonosítója | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| A folyamat futtatásának azonosítója | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sztring | Társított erőforrás-azonosító a (z) adatfeldolgozó erőforráshoz | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | A diagnosztikai naplók kategóriája. A tulajdonság beállítása "ActivityRuns" értékre | `ActivityRuns` |
|level| Sztring | A diagnosztikai naplók szintje. A tulajdonság "tájékoztató" értékre állítása | `Informational` |
|operationName| Sztring |Az állapottal rendelkező tevékenység neve. Ha az állapot a kezdő szívverés, akkor `MyActivity -`a. Ha az állapot a záró szívverés, `MyActivity - Succeeded` a végső állapotú. | `MyActivity - Succeeded` |
|pipelineName| Sztring | A folyamat neve | `MyPipeline` |
|activityName| String | A tevékenység neve | `MyActivity` |
|start| Sztring | A TimeSpan, UTC formátumban futtatott tevékenység kezdete | `2017-06-26T20:55:29.5007959Z`|
|end| String | A tevékenység vége TimeSpan, UTC formátumban fut. Ha a tevékenység még nem fejeződött be (a tevékenységhez tartozó diagnosztikai naplóban), az alapértelmezett érték `1601-01-01T00:00:00Z` van beállítva.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Folyamat futtatási naplóinak attribútumai

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Tulajdonság | Típus | Leírás | Példa |
| --- | --- | --- | --- |
| Szint |Sztring | A diagnosztikai naplók szintje. A 4-es szint a tevékenység-futtatási naplók esetében használható. | `4`  |
| correlationId |Sztring | Egy adott kérelem végpontok közötti nyomon követésére szolgáló egyedi azonosító | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Az esemény időpontja TimeSpan, UTC formátumban`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Sztring| A folyamat futtatásának azonosítója | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sztring | Társított erőforrás-azonosító a (z) adatfeldolgozó erőforráshoz | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sztring | A diagnosztikai naplók kategóriája. A tulajdonság beállítása "PipelineRuns" értékre | `PipelineRuns` |
|level| Sztring | A diagnosztikai naplók szintje. A tulajdonság "tájékoztató" értékre állítása | `Informational` |
|operationName| Sztring |Az állapottal rendelkező folyamat neve. "Folyamat – sikeres", végső állapottal, amikor a folyamat futtatása befejeződött| `MyPipeline - Succeeded` |
|pipelineName| Sztring | A folyamat neve | `MyPipeline` |
|start| Sztring | A TimeSpan, UTC formátumban futtatott tevékenység kezdete | `2017-06-26T20:55:29.5007959Z`|
|end| Sztring | A tevékenység vége TimeSpan, UTC formátumban fut. Ha a tevékenység még nem fejeződött be (a tevékenységhez tartozó diagnosztikai naplóban), az alapértelmezett érték `1601-01-01T00:00:00Z` van beállítva.  | `2017-06-26T20:55:29.5007959Z` |
|status| String | A folyamat futásának végső állapota (sikeres vagy sikertelen) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Trigger futtatási naplók attribútumai

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Tulajdonság | Típus | Leírás | Példa |
| --- | --- | --- | --- |
| Szint |String | A diagnosztikai naplók szintje. Állítsa a 4-es szintre a tevékenység futtatási naplóihoz. | `4`  |
| correlationId |String | Egy adott kérelem végpontok közötti nyomon követésére szolgáló egyedi azonosító | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sztring | Az esemény időpontja TimeSpan, UTC formátumban`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Sztring| Az trigger futtatásának azonosítója | `08587023010602533858661257311` |
|resourceId| Sztring | Társított erőforrás-azonosító a (z) adatfeldolgozó erőforráshoz | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sztring | A diagnosztikai naplók kategóriája. A tulajdonság beállítása "PipelineRuns" értékre | `PipelineRuns` |
|level| Sztring | A diagnosztikai naplók szintje. A tulajdonság "tájékoztató" értékre állítása | `Informational` |
|operationName| String |A végleges állapotú trigger neve, függetlenül attól, hogy sikeresen kirúgták-e. "MyTrigger – sikeres", ha a szívverés sikeres volt| `MyTrigger - Succeeded` |
|triggerName| Sztring | Az trigger neve | `MyTrigger` |
|triggerType| Sztring | Az trigger típusa (manuális trigger vagy Schedule trigger) | `ScheduleTrigger` |
|triggerEvent| Sztring | Az eseményindító eseménye | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Sztring | Trigger-tűz elindítása TimeSpan, UTC formátumban | `2017-06-26T20:55:29.5007959Z`|
|status| Sztring | Az aktiválás sikeres indításának végső állapota (sikeres vagy sikertelen) | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics séma

Log Analytics örökli a sémát a Azure Monitortól a következő kivételekkel:

* Az egyes oszlopok nevének első betűje tőkésítve lesz, például a Azure Monitor *correlationId* *correlationId* lesznek a log Analyticsban.
* Az oszlop *szintje* el lesz dobva.
* A dinamikus oszlop *tulajdonságait* a rendszer az alábbi dinamikus JSON-blob-típusként fogja megőrizni:

    | Azure Monitor oszlop | Log Analytics oszlop | Type |
    | --- | --- | --- |
    | $. properties. UserProperties | UserProperties | Dinamikus |
    | $. properties. Széljegyzetek | Széljegyzetek | Dinamikus |
    | $. properties. Bemeneti | Input (Bemenet) | Dinamikus |
    | $. properties. Kimeneti | Output | Dinamikus |
    | $. properties. Hiba. errorCode | Hibakód | int |
    | $. properties. Hiba. üzenet | ErrorMessage | String |
    | $. properties. Hiba | Hiba | Dinamikus |
    | $. properties. Korábbi verzióknál | Korábbi verzióknál | Dinamikus |
    | $. properties. Paraméterek | Paraméterek | Dinamikus |
    | $. properties. SystemParameters | SystemParameters | Dinamikus |
    | $. properties. Címkék | Tags | Dinamikus |
    
## <a name="metrics"></a>Mérőszámok

A Azure Monitor lehetővé teszi a telemetria használatát az Azure-beli számítási feladatok teljesítményének és állapotának megismeréséhez. Az Azure telemetria-adatok legfontosabb típusa a legtöbb Azure-erőforrás által kibocsátott mérőszámok (más néven teljesítményszámlálók). A Azure Monitor számos módszert biztosít ezen mérőszámok konfigurálásához és felhasználásához a figyeléshez és a hibaelhárításhoz.

A ADFV2 a következő metrikákat bocsátja ki:

| **Metrika**           | **Metrika megjelenítendő neve**         | **Egység** | **Összesítés típusa** | **Leírás**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | A folyamat sikeresen futtatja a metrikákat | Count    | Összes                | A folyamatok teljes futtatása egy percen belül sikeres volt. |
| PipelineFailedRuns   | Sikertelen folyamat-futtatási metrikák    | Count    | Összes                | A folyamatok teljes futtatása egy percen belül meghiúsult    |
| ActivitySucceededRuns | A sikeres tevékenység metrikákat futtat | Count    | Összes                | A tevékenységek teljes futtatása egy percen belül sikeres volt.  |
| ActivityFailedRuns   | Sikertelen tevékenység-futtatási metrikák    | Count    | Összes                | Egy percen belül nem sikerült a tevékenységek teljes futtatása.     |
| TriggerSucceededRuns | A sikeres trigger metrikákat futtat  | Count    | Összes                | A triggerek teljes futtatása egy percen belül sikeres volt.   |
| TriggerFailedRuns    | Sikertelen trigger-futtatási metrikák     | Count    | Összes                | Egy percen belül sikertelen volt az összes trigger-Futtatás.      |

A metrikák eléréséhez hajtsa végre az [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)adatplatformon megjelenő utasításokat.

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Data Factory metrikák figyelése Azure Monitor

A Azure Monitor használatával Azure Data Factory-integrációt használhat az adatAzure Monitorba való továbbításhoz. Ez az integráció az alábbi helyzetekben hasznos:

1.  Összetett lekérdezéseket szeretne írni a Data Factory által közzétett mérőszámok gazdag készletén Azure Monitor. A lekérdezéseken Azure Monitor használatával is létrehozhat egyéni riasztásokat.

2.  Az adatüzemek között szeretne figyelni. Több adatgyárból is átirányíthat adatait egyetlen Azure Monitor munkaterületre.

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Diagnosztikai beállítások és munkaterület konfigurálása

Engedélyezze a diagnosztikai beállításokat az adatai-előállítója számára.

1. A portálon navigáljon Azure Monitor, majd kattintson a **Beállítások** menü **diagnosztikai beállítások** elemére.

2. Válassza ki azt az adatelőállítót, amelynek diagnosztikai beállításait be szeretné állítani.
    
3. Ha a kiválasztott adatgyárban nem találhatók beállítások, a rendszer felszólítja, hogy hozzon létre egy beállítást. Kattintson a "Engedélyezze a diagnosztikát."

   ![Diagnosztikai beállítás - beállítások nélkül hozzáadása](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Ha vannak meglévő beállítások az adatgyárban, megjelenik az ezen az adatgyáron már konfigurált beállítások listája. Kattintson a "Diagnosztikai beállítás hozzáadása."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Adja meg a beállítás nevét, és jelölje be a **küldés log Analytics**számára jelölőnégyzetet, majd válasszon ki egy log Analytics munkaterületet.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat elteltével megjelenik az új beállítás az adat-előállítóhoz tartozó beállítások listájában, és a diagnosztikai naplók a munkaterületre kerülnek, amint az új esemény adatai jönnek létre. Akár 15 percet is igénybe vehet egy esemény kibocsátása és Log Analytics megjelenésekor.

> [!NOTE]
> Mivel az adott Azure-tábla kifejezett korlátja nem több mint 500 oszlopot tartalmaz, **erősen ajánlott az erőforrás-specifikus mód használata**. További információ: [log Analytics ismert korlátozások](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Data Factory Analytics telepítése az Azure Marketplace-ről

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kattintson a **Létrehozás** elemre, és válassza ki a munkaterület és a munkaterület beállításait.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory metrikák figyelése

A **Azure Data Factory Analytics** telepítése egy alapértelmezett nézetet hoz létre, amely a következő metrikákat teszi lehetővé:

- ADF-futtatások – 1) a folyamat futása Data Factory

- ADF-futtatások – 2) a tevékenységek Data Factory

- ADF-futtatások – 3) a triggerek Data Factory

- ADF-hibák – 1) az első 10 folyamat hibája Data Factory

- ADF-hibák – 2) az első 10 tevékenység fut Data Factory

- ADF-hibák – 3) az első 10 kiváltó hibát Data Factory

- ADF-statisztika – 1) a tevékenység típus szerint fut

- ADF-statisztika – 2) trigger-futtatások típus szerint

- ADF-statisztika – 3) a folyamat maximális futási időtartama

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Megjelenítheti a fenti mérőszámokat, megtekintheti a mérőszámok mögötti lekérdezéseket, szerkesztheti a lekérdezéseket, riasztásokat hozhat létre, és így tovább.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Riasztások

Jelentkezzen be a Azure Portalba,  > és kattintson a**riasztások** figyelése lehetőségre a riasztások létrehozásához.

![Riasztások a portál menüjében](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Új riasztás létrehozásához kattintson az **+ új riasztási szabály** elemre.

    ![Új riasztási szabály](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Adja meg a riasztási feltételt.

    > [!NOTE]
    > Ügyeljen rá, hogy a **szűrés erőforrás típusa szerint**válassza az **összes** lehetőséget.

    ![Riasztási feltétel, 1. képernyő 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Riasztási feltétel, 2. képernyő, 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Riasztási feltétel, 3. képernyő](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Adja meg a **riasztás részleteit**.

    ![Riasztás részletei](media/monitor-using-azure-monitor/alerts_image8.png)

4.  A **műveleti csoport**definiálása.

    ![Műveleti csoport, 1. képernyő, 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Műveleti csoport, 2. képernyő, 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Műveleti csoport, 3. képernyő, 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Műveleti csoport, 4. képernyő](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>További lépések

A folyamatok figyelésével és kezelésével kapcsolatos további információkért tekintse meg a [folyamatok programozott figyelése és kezelése](monitor-programmatically.md) című cikket.
