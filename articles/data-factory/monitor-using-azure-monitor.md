---
title: "Azure-figyelővel adat-előállítók figyelése |} Microsoft Docs"
description: "Útmutató: Azure-figyelő a figyelheti az adat-előállító adatcsatornák Azure Data Factory adataival diagnosztikai naplók engedélyezése."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: f30042ad8d687db59e1aaa092c46cee371e8c7fb
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Azure-figyelővel képernyő adat-előállítók  
Sok áthelyezése alkotórészek összetettek a felhőalapú alkalmazásokhoz. Győződjön meg arról, hogy az alkalmazás marad be adatokat, és megfelelő állapotban fut figyelés nyújt. Emellett segít, hogy ki a lehetséges problémák stave és a múltbeli kiépítettektől eltérő hibakeresést. Figyelési adatok segítségével emellett az alkalmazással kapcsolatos átfogó megismerésében. Ennek az információnak a segítséget nyújtanak az alkalmazások teljesítményének vagy karbantartási követelmények, vagy a, amelyek egyébként kézi beavatkozás műveletek automatizálására.

Az Azure biztosít alapszintű infrastruktúra metrikák és a naplókat a legtöbb Microsoft Azure-szolgáltatások. További információkért lásd: [figyelési áttekintés](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Az Azure diagnosztikai naplók erőforrás a művelet részletes, gyakori adatait adja meg az erőforrás által kibocsátott naplók. Adat-előállító kiírja a diagnosztikai naplók az Azure-figyelő. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd: [figyelése és kezelése a Data Factory version1 adatcsatornák](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

* Mentse azokat egy **Tárfiók** naplózási vagy manuális ellenőrzést. A diagnosztikai beállítások használatával megőrzési időtartama (napokban) is megadhat.
* Adatfolyam-őket **Event Hubs** egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a Power bi szempontjából.
* Elemezheti őket a **Operations Management Suite (OMS) szolgáltatáshoz**

A tárolási fiók vagy esemény hub névtér, amely nem ugyanahhoz az előfizetéshez van kibocsátó naplók erőforrásként is használhatja. Konfigurálja a beállítást a felhasználónak kell rendelkeznie mindkét előfizetéshez megfelelő szerepköralapú hozzáférés-vezérlést (RBAC) elérésére.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások
Diagnosztikai naplók nem számítási erőforrások diagnosztikai beállítások használatával vannak konfigurálva. Egy erőforrás-vezérlő diagnosztikai beállításai:

* Ha diagnosztikai naplók küldése (Storage-fiók, az Event Hubs, és/vagy az OMS szolgáltatáshoz).
* Napló kategóriák küldése.
* Mennyi ideig napló kategóriákhoz rendszer meddig őrizze meg a storage-fiók
* Egy nulla napos megőrzési azt jelenti, hogy a naplók végtelen tartanak. Ellenkező esetben a értéke lehet bármely 1 és 2147483647 között eltelt napok számát.
* Ha a megőrzési házirend-beállításokat, de a naplók tárolása a storage-fiók le van tiltva (például csak az Event Hubs vagy OMS vannak jelölve), az adatmegőrzési hatástalan.
* Adatmegőrzési alkalmazott napi,, így napi (UTC) szerint naplókat, amelyik most már a megőrzési túl napjától végén házirend törlődnek. Például ha egy nap adatmegőrzési, mai nap kezdetén a napló, a nap előtt tegnap törlése akkor történik meg.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Diagnosztikai naplók keresztül REST API-k engedélyezése

Hozzon létre vagy Azure figyelő REST API diagnosztikai beállítások frissítése

**Kérés**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Fejlécek**
* Cserélje le `{api-version}` rendelkező `2016-09-01`.
* Cserélje le `{resource-id}` az erőforrás diagnosztikai beállításainak módosítása milyen, amelynek az erőforrás-azonosító. További információ [erőforráscsoportok használata az Azure erőforrások kezeléséhez](../azure-resource-manager/resource-group-portal.md).
* Állítsa be a `Content-Type` fejlécének `application/json`.
* Állítsa be az engedélyezési fejléc Azure Active Directory származó JSON webes jogkivonat. További információkért lásd: [intézett hitelesítési kérésekhez](../active-directory/develop/active-directory-authentication-scenarios.md).

**Törzs**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
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
| StorageAccountId |Karakterlánc | Az erőforrás-azonosítója, amelyet szeretne elküldeni a diagnosztikai naplókat a tárfiók |
| serviceBusRuleId |Karakterlánc | A service bus szabály azonosítója a service bus-névtér, amelyben a folyamatos átviteli diagnosztikai naplók létrehozott Event Hubs rendelkezik szeretné. A szabály azonosítója formátumban van: "{service bus erőforrás-azonosító} /authorizationrules/ {kulcsnév}".|
| WorkspaceId | Összetett típus | A tömb metrika idő szemek és az adatmegőrzési házirendek. Ezt a tulajdonságot jelenleg üres. |
|metrics| A meghívott csővezeték átadandó futtassa paraméterértékeket a feldolgozási folyamat| A JSON-objektum argumentum értékek leképezése a paraméterek nevei | 
| naplók| Összetett típus| A diagnosztikai naplófájl kategória neve erőforrástípus. Szerezze be az erőforrás diagnosztikai naplófájl kategóriák listáját, először hajtson végre egy GET diagnosztikai beállítások műveletet. |
| category| Karakterlánc| Napló kategóriák és az adatmegőrzési tömbje |
| Időkeretben vannak | Karakterlánc | A lépésköz legyen metrikák rögzített időtartam ISO 8601 formátumban. PT1M (egy percig) kell lennie.|
| engedélyezve| Logikai | Meghatározza, hogy a metrika- vagy naplófájl kategória gyűjteménye engedélyezett ehhez az erőforráshoz|
| retentionPolicy| Összetett típus| Az adatmegőrzési metrika- vagy naplófájl kategória ismerteti. Csak a tárolási fiók lehetőséget használja.|
| nap| Int| A metrikák és a naplók a megőrizni kívánt napok száma. A 0 érték határozatlan ideig őrzi meg a naplókat. Csak a tárolási fiók lehetőséget használja. |

**Válasz**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
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

Azure figyelő REST API-t a diagnosztika beállítás adatainak beolvasása

**Kérés**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Fejlécek**
* Cserélje le `{api-version}` rendelkező `2016-09-01`.
* Cserélje le `{resource-id}` az erőforrás diagnosztikai beállításainak módosítása milyen, amelynek az erőforrás-azonosító. További információk segítségével erőforráscsoportok az Azure erőforrások kezeléséhez.
* Állítsa be a `Content-Type` fejlécének `application/json`.
* A JSON Web Token származó Azure Active Directory hitelesítési fejlécéhez beállítása. További információkért lásd: Authenticating kérelmeket.

**Válasz**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
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
További információ az itt] (https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>A naplók és események séma

### <a name="activity-run-logs-attributes"></a>Tevékenység Futtatás naplók attribútumai

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
   "properties:" 
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
| Szint |Karakterlánc | A diagnosztikai naplók szintje. 4. szint mindig a helyzet tevékenységfuttatási naplókat. | `4`  |
| correlationId |Karakterlánc | Nyomon követésére egy adott kérelem-végpont egyedi azonosítója | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Karakterlánc | A timespan, UTC formátumban esemény időpontja | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Karakterlánc| A tevékenység futtatásához Azonosítóját | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Karakterlánc| Futtassa a folyamat azonosítója | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Karakterlánc | A data factory erőforráshoz kapcsolódó erőforrás-azonosítója | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Karakterlánc | Diagnosztikai naplók kategóriáját. Állítsa be ezt a tulajdonságot "ActivityRuns" | `ActivityRuns` |
|szint| Karakterlánc | A diagnosztikai naplók szintje. Állítsa be ezt a tulajdonságot "Tájékoztató" | `Informational` |
|operationName| Karakterlánc |A tevékenység állapota neve. A start szívverés állapota, ha a rendszer `MyActivity -`. A záró szívverés állapota, ha a rendszer `MyActivity - Succeeded` végső állapot | `MyActivity - Succeeded` |
|pipelineName| Karakterlánc | A folyamat neve | `MyPipeline` |
|activityName| Karakterlánc | A tevékenység neve. | `MyActivity` |
|start| Karakterlánc | A timespan, UTC formátumban tevékenységfuttatási elindítása | `2017-06-26T20:55:29.5007959Z`|
|vége| Karakterlánc | A tevékenység végén futtatása timespan, UTC formátumban. Ha a tevékenység nem ér véget, mégis (diagnosztikai naplófájl egy tevékenység indítása), alapértelmezett értéke `1601-01-01T00:00:00Z` van beállítva.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>A következő feldolgozási sorban futtatási naplók attribútumai

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
| Szint |Karakterlánc | A diagnosztikai naplók szintje. 4. szint a helyzet tevékenységfuttatási naplókat. | `4`  |
| correlationId |Karakterlánc | Nyomon követésére egy adott kérelem-végpont egyedi azonosítója | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Karakterlánc | A timespan, UTC formátumban esemény időpontja | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|futtatásazonosító| Karakterlánc| Futtassa a folyamat azonosítója | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Karakterlánc | A data factory erőforráshoz kapcsolódó erőforrás-azonosítója | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Karakterlánc | Diagnosztikai naplók kategóriáját. Állítsa be ezt a tulajdonságot "PipelineRuns" | `PipelineRuns` |
|szint| Karakterlánc | A diagnosztikai naplók szintje. Állítsa be ezt a tulajdonságot "Tájékoztató" | `Informational` |
|operationName| Karakterlánc |A folyamat állapotát a neve. "Futószalag - sikeresen befejeződött" futtatása folyamat befejezésekor végső állapot| `MyPipeline - Succeeded` |
|pipelineName| Karakterlánc | A folyamat neve | `MyPipeline` |
|start| Karakterlánc | A timespan, UTC formátumban tevékenységfuttatási elindítása | `2017-06-26T20:55:29.5007959Z`|
|vége| Karakterlánc | A tevékenység befejezési futtatható timespan, UTC formátumban. Ha a tevékenység nem ér véget, mégis (diagnosztikai naplófájl egy tevékenység indítása), alapértelmezett értéke `1601-01-01T00:00:00Z` van beállítva.  | `2017-06-26T20:55:29.5007959Z` |
|status| Karakterlánc | Végső állapot, a folyamatának futtatása (sikeres vagy sikertelen) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Indítás, futtatási naplók attribútumai

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
| Szint |Karakterlánc | A diagnosztikai naplók szintje. 4. Futtassa a naplók tevékenység szint beállítása | `4`  |
| correlationId |Karakterlánc | Nyomon követésére egy adott kérelem-végpont egyedi azonosítója | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Karakterlánc | A timespan, UTC formátumban esemény időpontja | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|eseményindító azonosítója| Karakterlánc| Az eseményindító futtatásához Azonosítóját | `08587023010602533858661257311` |
|resourceId| Karakterlánc | A data factory erőforráshoz kapcsolódó erőforrás-azonosítója | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Karakterlánc | Diagnosztikai naplók kategóriáját. Állítsa be ezt a tulajdonságot "PipelineRuns" | `PipelineRuns` |
|szint| Karakterlánc | A diagnosztikai naplók szintje. Állítsa be ezt a tulajdonságot "Tájékoztató" | `Informational` |
|operationName| Karakterlánc |A végső állapot eseményindító nevét e azt sikeresen következik be. "MyTrigger - sikeres volt." Ha sikeres volt-e a szívverés| `MyTrigger - Succeeded` |
|Eseményindító_neve| Karakterlánc | Az eseményindító neve | `MyTrigger` |
|triggerType| Karakterlánc | Az eseményindító (kézi indítási vagy ütemezés eseményindító) típusú | `ScheduleTrigger` |
|triggerEvent| Karakterlánc | Az eseményindító esemény | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Karakterlánc | TimeSpan érték, UTC formátumban eseményindító tűz elindítása | `2017-06-26T20:55:29.5007959Z`|
|status| Karakterlánc | Végső állapotát e trigger sikeresen következik (sikeres vagy sikertelen) | `Succeeded`|

### <a name="metrics"></a>Mérőszámok

Azure figyelő címinfrastruktúra megjelenítési lehetőségeinek a teljesítmény- és a munkaterhelések Azure állapot telemetriai adatok felhasználását teszi lehetővé. A legfontosabb Azure telemetriai adatok típus (más néven teljesítményszámlálók) vagy az Azure erőforrások által kibocsátott metrikákat. Az Azure figyelő többféleképpen is konfigurálhatja, és felhasználhatják a figyeléshez és hibaelhárításhoz metrikákat.

ADFV2 bocsát ki a következő metrikák

| **Metrika**           | **Metrika megjelenített neve**         | **Egység** | **Aggregáció típusa** | **Leírás**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Sikerült a folyamat fut metrikák | Darabszám    | Összes                | Teljes folyamatok futtatása sikeres egy perc időszakban |
| PipelineFailedRuns   | Nem sikerült a folyamat fut metrikák    | Darabszám    | Összes                | Teljes folyamatok futtatása sikertelen egy perc időszakban    |
| ActiviySucceededRuns | Sikeres volt a tevékenység futtatása metrikák | Darabszám    | Összes                | Egy perc időszakban a teljes tevékenység sikeres fut  |
| ActivityFailedRuns   | Nem sikerült a tevékenységi futtatása metrikák    | Darabszám    | Összes                | Teljes tevékenység futtatása sikertelen, egy perc időszakban     |
| TriggerSucceededRuns | Sikeres volt a trigger futtatása metrikák  | Darabszám    | Összes                | Teljes eseményindító sikeres futtat egy perc időszakban   |
| TriggerFailedRuns    | Nem sikerült az eseményindító futtatása metrikák     | Darabszám    | Összes                | Teljes eseményindító sikertelen futtat egy perc időszakban      |

A metrikák hozzáférni, kövesse a cikkben - https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="next-steps"></a>Következő lépések
Lásd: [figyelő programozott folyamatok kezelését és](monitor-programmatically.md) cikkben tájékozódhat az figyelése és kezelése a folyamatok futtatásával. 