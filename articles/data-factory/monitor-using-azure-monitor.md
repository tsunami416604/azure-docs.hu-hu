---
title: Az Azure Monitor használatával a data factoryk figyelése |} A Microsoft Docs
description: Útmutató a Data Factory-folyamatok figyelése a diagnosztikai naplók az adatok Azure Data Factory engedélyezésével az Azure Monitor használatával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 25bb455ea46fdc96e32e34d434dd844779b0b650
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495298"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Riasztás megjelenítése és figyelése az Azure Monitor használatával adat-előállítók
A felhőalapú alkalmazások összetettek a részek. Győződjön meg arról, hogy az alkalmazás mindig elérhető fel az adatokat és kifogástalan állapotban fut figyelést biztosít. Segít, hogy ki a lehetséges problémák stave vagy korábbi kiépítettektől hibaelhárítása. Monitorozási adatok segítségével emellett részletes elemzéseket kaphat az alkalmazásról. A Tudásbázis segítségével javíthatja az alkalmazás teljesítménye vagy Karbantarthatóság, vagy ellenkező esetben manuális beavatkozást igénylő műveletek automatizálása.

Az Azure Monitor a Microsoft Azure-ban a legtöbb szolgáltatás alapszintű infrastruktúra metrikákat és naplókat biztosít. További információkért lásd: [Figyelés áttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Az Azure diagnosztikai naplók az erőforrás által kibocsátott naplókat, a műveletet az erőforrás vonatkozó részletes, gyakori adatokkal szolgálnak. A Data Factory jelenít meg az Azure Monitor-diagnosztikai naplók.

## <a name="persist-data-factory-data"></a>Data Factory adatok megőrzése
A Data Factory csak 45 nap folyamatfuttatás adatokat tárolja. Ha azt szeretné megőrizni a folyamatfuttatás adatok legfeljebb 45 nap, az Azure Monitor használatával, nem csak irányíthatja a diagnosztikai naplók elemzéséhez, megőrizheti a azokat egy tárfiókba történő így gyári adatokat a chossing időtartamára.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

* Menti azokat egy **Tárfiók** naplózási vagy manuális ellenőrzést. A megőrzési időtartam (napban) használatával a diagnosztikai beállításokat is megadhat.
* Azokat a Stream **az Event Hubs** egy külső szolgáltatás vagy az egyéni elemzési megoldással, például a Power bi támogatunk.
* Elemezheti a **Log Analytics**

Egy tároló vagy egy eseményközpontba eseményközpont-névtér, amely nem ugyanabban az előfizetésben, amely a naplókat kibocsátó erőforrásként használhat. A beállítást konfiguráló felhasználónak rendelkeznie kell a megfelelő szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés mindkét előfizetéshez.

## <a name="set-up-diagnostic-logs"></a>Állítsa be a diagnosztikai naplók

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások
Diagnosztikai naplók nem számítási erőforrások diagnosztikai beállítások használatával vannak konfigurálva. Diagnosztikai beállítások egy erőforrás-vezérlő:

* Ha a diagnosztikai naplók (Storage-fiók, az Event Hubs, illetve a Log Analytics) érkeznek.
* Melyik naplókategóriák küldi el.
* Mennyi ideig minden naplókategória megőrződjön-e a storage-fiókban
* Egy nulla napnyi adatmegőrzéshez azt jelenti, hogy naplókat tartják örökre. Ellenkező esetben az érték lehet minden olyan 1 és 2147483647 között eltelt napok számát.
* Ha a megőrzési házirend-beállításokat, de a naplók tárolása a storage-fiók le van tiltva (például csak az Event Hubs vagy a Log Analytics-beállítás ki van jelölve), az adatmegőrzési szabályzatok nem befolyásolják.
* Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén törli a házirendet. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>REST API-kon keresztül a diagnosztikai naplók engedélyezése

Létrehozni vagy frissíteni a diagnosztikai beállítás az Azure Monitor REST API-ban

**Kérés**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Fejlécek**
* Cserélje le `{api-version}` a `2016-09-01`.
* Cserélje le `{resource-id}` az erőforrás-azonosító, az erőforrás, amelynek szeretné szerkeszteni a diagnosztikai beállítások. További információ [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-portal.md).
* Állítsa be a `Content-Type` fejlécet `application/json`.
* Az engedélyezési fejléc beállítása a JSON webes jogkivonat, hogy az Azure Active Directoryból. További információkért lásd: [irányuló kérelmek hitelesítéséhez](../active-directory/develop/authentication-scenarios.md).

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
| storageAccountId |Sztring | Az erőforrás-azonosítója, amelyre szeretné elküldeni a diagnosztikai naplókat a storage-fiók |
| serviceBusRuleId |Sztring | A service bus szabály azonosítója a service bus-névteret, amelyben meg szeretné, hogy a diagnosztikai naplók streameléshez létrehozott Event Hubs. A szabály csomagazonosítója a következő formátumban: "{service bus az erőforrás-azonosító} /authorizationrules/ {kulcsnév}".|
| workspaceId | Komplex típus | Metrika idő magok és az adatmegőrzési szabályzatok tömbje. Ez a tulajdonság jelenleg üres. |
|metrics| A folyamatfuttatás a meghívott folyamat átadandó paraméterértékek| JSON-objektum argumentumértékként paraméterneveket leképezése |
| naplók| Komplex típus| Az erőforrástípushoz diagnosztikai napló kategória neve. Diagnosztikai napló kategóriák egy erőforrás beszerzéséhez hajtsa végre a diagnosztikai beállítások művelet. |
| category| Sztring| Naplókategóriák és azok adatmegőrzési szabályzatok tömbje |
| timeGrain | Sztring | Metrikák időtartama ISO 8601 formátumban rögzített részletességét. PT1M (egy percig) kell lennie.|
| engedélyezve| Logikai | Megadja, hogy a metrika- vagy naplófájl kategória gyűjteményét engedélyezett ehhez az erőforráshoz|
| retentionPolicy| Komplex típus| A metrika- vagy naplófájl kategória esetén a megtartási házirendben ismerteti. Csak a tárolási fiók lehetőséget használja.|
| nap| Int| Ennyi napig megőrzi a metrikákat vagy naplókat. A 0 érték határozatlan ideig őrzi meg a naplókat. Csak a tárolási fiók lehetőséget használja. |

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

Diagnosztikai beállítás adatainak lekérése az Azure Monitor REST API-ban

**Kérés**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Fejlécek**
* Cserélje le `{api-version}` a `2016-09-01`.
* Cserélje le `{resource-id}` az erőforrás-azonosító, az erőforrás, amelynek szeretné szerkeszteni a diagnosztikai beállítások. További információk használatával erőforráscsoportok az Azure-erőforrások kezeléséhez.
* Állítsa be a `Content-Type` fejlécet `application/json`.
* Az engedélyezési fejléc beállítása a JSON Web Token, hogy az Azure Active Directoryból. További információkért lásd: kérelmek történő hitelesítést.

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
[További információ itt](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Naplók és események sémája

### <a name="activity-run-logs-attributes"></a>Attribútumok naplózza a tevékenység futtatása

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
| Szint |Sztring | A diagnosztikai naplók szintjét. 4. szint mindig a helyzet a tevékenység-végrehajtásonként naplókat. | `4`  |
| correlationId |Sztring | Egy adott kérés teljes körű nyomon egyedi azonosítója | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sztring | Az időtartomány, UTC-formátum az esemény időpontja | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Sztring| Futtassa a tevékenység azonosítója | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Sztring| A folyamat futásának Azonosítóját | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sztring | A data factory erőforrás társított erőforrás azonosítója | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sztring | Diagnosztikai naplók kategóriáját. A "ActivityRuns" tulajdonság értéke | `ActivityRuns` |
|szint| Sztring | A diagnosztikai naplók szintjét. Ezzel a tulajdonsággal, "Tájékoztatási szintű" | `Informational` |
|operationName| Sztring |A tevékenység nevét a állapotú. Ha az állapot kezdő szívverés, `MyActivity -`. Ha az állapot a végfelhasználók szívverés, `MyActivity - Succeeded` végleges állapotú | `MyActivity - Succeeded` |
|pipelineName| Sztring | A folyamat neve | `MyPipeline` |
|activityName| Sztring | A tevékenység neve | `MyActivity` |
|start| Sztring | A tevékenység futtatása a timespan UTC-formátum kezdete | `2017-06-26T20:55:29.5007959Z`|
|vége| Sztring | Vége a tevékenység futtatása timespan UTC-formátum. Ha a tevékenység nem ér véget, de (diagnosztikai napló egy tevékenység indítása), alapértelmezett értéke `1601-01-01T00:00:00Z` van beállítva.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Folyamat futásának naplók attribútumok

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
| Szint |Sztring | A diagnosztikai naplók szintjét. 4. szint a helyzet tevékenységfuttatás naplókat. | `4`  |
| correlationId |Sztring | Egy adott kérés teljes körű nyomon egyedi azonosítója | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sztring | Az időtartomány, UTC-formátum az esemény időpontja | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Sztring| A folyamat futásának Azonosítóját | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Sztring | A data factory erőforrás társított erőforrás azonosítója | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sztring | Diagnosztikai naplók kategóriáját. A "PipelineRuns" tulajdonság értéke | `PipelineRuns` |
|szint| Sztring | A diagnosztikai naplók szintjét. Ezzel a tulajdonsággal, "Tájékoztatási szintű" | `Informational` |
|operationName| Sztring |A folyamat nevét a állapotú. "Folyamat – sikeres" folyamat futtatásának befejezésekor végleges állapotú| `MyPipeline - Succeeded` |
|pipelineName| Sztring | A folyamat neve | `MyPipeline` |
|start| Sztring | A tevékenység futtatása a timespan UTC-formátum kezdete | `2017-06-26T20:55:29.5007959Z`|
|vége| Sztring | A tevékenység befejezési futtatja, az időtartam, UTC-formátum. Ha a tevékenység nem ér véget, de (diagnosztikai napló egy tevékenység indítása), alapértelmezett értéke `1601-01-01T00:00:00Z` van beállítva.  | `2017-06-26T20:55:29.5007959Z` |
|status| Sztring | Végső állapotát a folyamat futtatásához (sikeres vagy sikertelen) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Eseményindító-futtatáskor naplók attribútumok

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
| Szint |Sztring | A diagnosztikai naplók szintjét. Állítsa a tevékenység-végrehajtásonként naplók 4. szint. | `4`  |
| correlationId |Sztring | Egy adott kérés teljes körű nyomon egyedi azonosítója | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Sztring | Az időtartomány, UTC-formátum az esemény időpontja | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Sztring| Az eseményindító-futtatás azonosítója | `08587023010602533858661257311` |
|resourceId| Sztring | A data factory erőforrás társított erőforrás azonosítója | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Sztring | Diagnosztikai naplók kategóriáját. A "PipelineRuns" tulajdonság értéke | `PipelineRuns` |
|szint| Sztring | A diagnosztikai naplók szintjét. Ezzel a tulajdonsággal, "Tájékoztatási szintű" | `Informational` |
|operationName| Sztring |Nevét az eseményindítót a végső állapotát, hogy a sikeres aktiválás. "MyTrigger – sikeres" Ha sikeres volt-e a szívverés| `MyTrigger - Succeeded` |
|triggerName| Sztring | Az eseményindító neve | `MyTrigger` |
|triggerType| Sztring | A trigger (manuális eseményindító vagy ütemezési eseményindító) típusa | `ScheduleTrigger` |
|triggerEvent| Sztring | Az eseményindító esemény | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Sztring | Az időtartam, UTC-formátum eseményindító fire kezdete | `2017-06-26T20:55:29.5007959Z`|
|status| Sztring | Végső állapotát, hogy eseményindító sikeresen aktiválódott (sikeres vagy sikertelen) | `Succeeded`|

## <a name="metrics"></a>Mérőszámok

Az Azure Monitor lehetővé teszi, hogy értékes információkhoz juthat a teljesítmény és az Azure-ban a számítási feladatok állapotát a telemetriai adatok felhasználásához. Az Azure telemetriai adatokat a legfontosabb típus a legtöbb Azure-erőforrások által kibocsátott (más néven teljesítményszámlálók) metrikák. Az Azure Monitor konfigurálása és figyelése és hibaelhárítása a metrikák felhasználásához több módszert is biztosít.

ADFV2 bocsát ki a következő metrikák

| **Metrika**           | **Metrika megjelenített neve**         | **Egység** | **Aggregáció típusa** | **Leírás**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Sikeres futtatások metrikák folyamat | Darabszám    | Összes                | Teljes folyamatok futtatja sikerült perces időtartamon belül |
| PipelineFailedRuns   | Nem sikerült a folyamat futtatása metrikák    | Darabszám    | Összes                | Perc időtartamon belül a folyamatok teljes sikertelen futtatások    |
| ActiviySucceededRuns | Sikeres futtatások metrikák tevékenység | Darabszám    | Összes                | Összes tevékenység futtatása sikeres perces időtartamon belül  |
| ActivityFailedRuns   | Nem sikerült a tevékenységi futtatások metrikák    | Darabszám    | Összes                | Perc időtartamon belül az összes tevékenység sikertelen futtatások     |
| TriggerSucceededRuns | Eseményindító-futtatások metrikák sikerült  | Darabszám    | Összes                | Teljes eseményindító-futtatások sikeres perces időtartamon belül   |
| TriggerFailedRuns    | Nem sikerült az eseményindító-futtatások metrikák     | Darabszám    | Összes                | Perc időtartamon belül az összes eseményindító sikertelen futtatások      |

A mérőszámok elérése, kövesse a cikk- https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="alerts"></a>Riasztások

A támogatott mérőszámok az adat-előállító riasztásokat is növelheti. Kattintson a **riasztások** gombra az adat-előállító **figyelő** lapot.

![Figyelmeztetések választása](media/monitor-using-azure-monitor/alerts_image1.png)

Ezzel továbblép a **riasztások** lapot.

![Riasztások lap](media/monitor-using-azure-monitor/alerts_image2.png)

Is is jelentkezzen be az Azure Portalon, majd kattintson a **figyelője –&gt; riasztások** elérni a **riasztások** közvetlenül lapon.

![A portál menüjében riasztások](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Kattintson a **+ Új riasztási szabály** egy új riasztást létrehozni.

    ![Új riasztási szabály](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Adja meg a **riasztási feltétel**.

    > [!NOTE]
    > Ügyeljen arra, hogy válassza ki, hogy **összes** a a **szűrés erőforrástípus szerint**.

    ![Riasztási feltétel, a képernyőn 1/3.](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Riasztási feltétel, a képernyőn 2/3.](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Riasztási feltétel, a képernyőn 3/3.](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Adja meg a **riasztás részletei**.

    ![Riasztás részletei](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Adja meg a **műveletcsoport**.

    ![Műveletcsoport, 4, 1. képernyő](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Műveletcsoport, 4, 2 képernyő](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Műveletcsoport, 4, 3 képernyő](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Műveletcsoport, 4 képernyő a 4-ből](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>További lépések
Lásd: [figyelő programozással felügyelheti a folyamatokat és](monitor-programmatically.md) figyelése és kezelése a folyamatok futtatásával kapcsolatos cikket.
