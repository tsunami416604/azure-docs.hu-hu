---
title: Adatgyárak figyelése az Azure Monitor használatával
description: Megtudhatja, hogyan használhatja az Azure Monitort az /Azure Data Factory-folyamatok figyelésére a Data Factory-ból származó adatokat tartalmazó diagnosztikai naplók engedélyezésével.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419459"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Adatgyárak riasztása és figyelése az Azure Monitor használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A felhőalapú alkalmazások összetettek és sok mozgó elhatással rendelkeznek. A figyelők adatokat szolgáltatnak annak érdekében, hogy az alkalmazások kifogástalan állapotban maradjanak. A monitorok segítenek elkerülni a lehetséges problémákat, és elháríthatják a korábbiproblémákat.

A figyelési adatok segítségével mély betekintést nyerhet az alkalmazásokról. Ez a tudás segít az alkalmazások teljesítményének és karbantarthatóságának javításában. Emellett segít a manuális beavatkozást igénylő műveletek automatizálásában is.

Az Azure Monitor alapszintű infrastruktúra-metrikákat és naplókat biztosít a legtöbb Azure-szolgáltatáshoz. Az Azure diagnosztikai naplók által kibocsátott egy erőforrás, és gazdag, gyakori adatokat az adott erőforrás működéséről. És az Azure Data Factory diagnosztikai naplókat ír a Figyelőben.

További információt az [Azure Monitor – áttekintés című témakörben talál.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)

## <a name="keeping-azure-data-factory-data"></a>Az Azure Data Factory adatainak megőrzése

A Data Factory csak 45 napig tárolja a folyamatfuttatási adatokat. Használja a Monitort, ha hosszabb ideig szeretné megőrizni ezeket az adatokat. A Figyelő segítségével diagnosztikai naplókat irányíthat elemzésre. A tárfiókban is tárolhatja őket, hogy a kiválasztott időtartamhoz gyári adatokkal rendelkezhessen.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

* Mentse a diagnosztikai naplókat egy tárfiókba naplózásvagy manuális ellenőrzés céljából. A diagnosztikai beállítások segítségével megadhatja a megőrzési időt napokban.
* A naplók streamelése az Azure Event Hubs-ba. A naplók egy partnerszolgáltatás vagy egy egyéni elemzési megoldás, például a Power BI bemenetévé válnak.
* Elemezze a naplókat a Log Analytics segítségével.

Használhat olyan tárfiókot vagy eseményközpont-névteret, amely nem szerepel a naplókat kibocsátó erőforrás előfizetésében. A beállítást konfiguráló felhasználónak mindkét előfizetéshez megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) hozzáféréssel kell rendelkeznie.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások

A diagnosztikai beállítások segítségével konfigurálhatja a nem számított erőforrások diagnosztikai naplóit. Az erőforrás-vezérlő beállításai a következő szolgáltatásokkal rendelkeznek:

* Meghatározzák a diagnosztikai naplók elküldésének helyét. Ilyen például egy Azure storage-fiók, egy Azure-eseményközpont vagy a Naplók figyelése.
* Meghatározzák, hogy mely naplókategóriákat küldi el a rendszer.
* Azt határozzák meg, hogy az egyes naplókategóriákmennyi ideig legyenek tárolva egy tárfiókban.
* A nulla napos megőrzés azt jelenti, hogy a naplók örökre meg lesznek őrizve. Ellenkező esetben az érték 1 és 2 147 483 647 között tetszőleges számú nap lehet.
* Ha az adatmegőrzési házirendek be vannak állítva, de a naplók tárolása egy tárfiókban le van tiltva, az adatmegőrzési házirendek nincs hatása. Ez a feltétel például akkor fordulhat elő, ha csak az Eseményközpontok vagy a Naplófigyelő beállítások vannak kiválasztva.
* Az adatmegőrzési házirendek naponta kerülnek alkalmazásra. A napok közötti határ éjfélkor történik koordinált világidő (UTC). A nap végén az adatmegőrzési házirenden túli napok naplói törlődnek. Például ha egy napos adatmegőrzési házirenddel rendelkezik, a mai nap elején a tegnapi naplók törlődnek.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnosztikai naplók engedélyezése az Azure Monitor REST API-n keresztül

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Diagnosztikai beállítás létrehozása vagy frissítése a REST-figyelő API-ban

##### <a name="request"></a>Kérés

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Fejlécek

* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* Cserélje `{resource-id}` le annak az erőforrásnak az azonosítójára, amelynek diagnosztikai beállításait szeretné megújozni. További információ: [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Állítsa `Content-Type` a `application/json`fejlécet a beállításra.
* Állítsa be az engedélyezési fejlécet az Azure Active Directoryból (Azure AD) kapott JSON-webtokenre. További információt a [Kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md)című témakörben talál.

##### <a name="body"></a>Törzs

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
| **storageAccountId** |Sztring | Annak a tárfióknak az erőforrás-azonosítója, amelyre diagnosztikai naplókat szeretne küldeni. |
| **szolgáltatásBusRuleId** |Sztring | A szolgáltatásbusz-névtér szolgáltatásbusz-névtér szolgáltatás-busz szabályazonosítója, amelyben szeretné, hogy az Eseményközpontok létre legyenek a streamelési diagnosztikai naplókhoz. A szabályazonosító formátuma `{service bus resource ID}/authorizationrules/{key name}`a .|
| **workspaceId** | Összetett típus | Metrikus időszemcsék és azok megőrzési házirendjei tömbje. A tulajdonság értéke üres. |
|**Mutatókat**| A megmeghívott folyamatnak átadandó folyamat futtatásparaméter-értékei| JSON-objektum, amely a paraméterneveket argumentumértékekhez rendeli. |
| **Naplók**| Összetett típus| Egy erőforrástípus diagnosztikai naplókategóriájának neve. Egy erőforrás diagnosztikai naplókategóriáinak listájának lekérnie, hajtsa végre a GET diagnosztikai beállítások műveletet. |
| **Kategória**| Sztring| Naplókategóriák és azok adatmegőrzési házirendjei tömbje. |
| **timeGrain** | Sztring | A metrikák részletessége, amelyek ISO 8601 időtartam formátumban vannak rögzítve. A tulajdonság értékének `PT1M`, amely egy percet határoz meg. |
| **Engedélyezve**| Logikai | Itt adható meg, hogy a metrika vagy a naplókategória gyűjtése engedélyezve van-e ehhez az erőforráshoz. |
| **megőrzési irányelv**| Összetett típus| Egy metrika vagy naplókategória adatmegőrzési szabályának ismertetése. Ez a tulajdonság csak tárfiókokhoz használatos. |
|**Nap**| Int| A metrikák vagy naplók megőrzéséhez eltelt napok száma. Ha a tulajdonság értéke 0, a naplók örökre megmaradnak. Ez a tulajdonság csak tárfiókokhoz használatos. |

##### <a name="response"></a>Válasz

200 ok.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>A diagnosztikai beállításokkal kapcsolatos információk a REST-figyelő API-ban

##### <a name="request"></a>Kérés

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Fejlécek

* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* Cserélje `{resource-id}` le annak az erőforrásnak az azonosítójára, amelynek diagnosztikai beállításait szeretné megújozni. További információ: [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Állítsa `Content-Type` a `application/json`fejlécet a beállításra.
* Állítsa be az engedélyezési fejlécet egy JSON-webtokenre, amelyet az Azure AD-től kapott. További információt a [Kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md)című témakörben talál.

##### <a name="response"></a>Válasz

200 ok.

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
További információt a [Diagnosztikai beállítások című témakörben talál.](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs-and-events"></a>Naplók és események sémája

### <a name="monitor-schema"></a>Séma figyelése

#### <a name="activity-run-log-attributes"></a>Tevékenység-futtatási napló attribútumai

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
| **Szint** |Sztring | A diagnosztikai naplók szintje. Tevékenységfuttatási naplók esetén állítsa a tulajdonság értékét 4-re. | `4` |
| **korrelációazonosító** |Sztring | Egy adott kérelem nyomon követéséhez szükséges egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Idő** | Sztring | Az esemény időpontja az UTC időtartományban `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Sztring| A tevékenységfutás azonosítója. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Sztring| A folyamatfuttatás azonosítója. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sztring | Az adat-gyári erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa a tulajdonság `ActivityRuns`értékét . | `ActivityRuns` |
|**Szinten**| Sztring | A diagnosztikai naplók szintje. Állítsa a tulajdonság `Informational`értékét . | `Informational` |
|**operationName**| Sztring | A tevékenység neve az állapotával együtt. Ha a tevékenység a kezdő szívverés, `MyActivity -`akkor a tulajdonság értéke . Ha a tevékenység a záró szívverés, `MyActivity - Succeeded`akkor a tulajdonság értéke . | `MyActivity - Succeeded` |
|**pipelineName**| Sztring | A csővezeték neve. | `MyPipeline` |
|**activityName (tevékenységneve)**| Sztring | A tevékenység neve. | `MyActivity` |
|**Elkezd**| Sztring | A tevékenység kezdési időpontja timespan UTC formátumban fut. | `2017-06-26T20:55:29.5007959Z`|
|**Végén**| Sztring | A tevékenység befejezési ideje timespan UTC formátumban fut. Ha a diagnosztikai napló azt mutatja, hogy egy tevékenység `1601-01-01T00:00:00Z`elkezdődött, de még nem ért véget, a tulajdonság értéke . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Folyamatfuttatási napló attribútumai

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
| **Szint** |Sztring | A diagnosztikai naplók szintje. Tevékenységfuttatási naplók esetén állítsa a tulajdonság értékét 4-re. | `4` |
| **korrelációazonosító** |Sztring | Egy adott kérelem nyomon követéséhez szükséges egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Idő** | Sztring | Az esemény időpontja az UTC időtartományban `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**futtatási azonosító**| Sztring| A folyamatfuttatás azonosítója. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sztring | Az adat-gyári erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa a tulajdonság `PipelineRuns`értékét . | `PipelineRuns` |
|**Szinten**| Sztring | A diagnosztikai naplók szintje. Állítsa a tulajdonság `Informational`értékét . | `Informational` |
|**operationName**| Sztring | A folyamat neve és állapota. A folyamat futtatása után a `Pipeline - Succeeded`tulajdonság értéke . | `MyPipeline - Succeeded`. |
|**pipelineName**| Sztring | A csővezeték neve. | `MyPipeline` |
|**Elkezd**| Sztring | A tevékenység kezdési időpontja timespan UTC formátumban fut. | `2017-06-26T20:55:29.5007959Z`. |
|**Végén**| Sztring | A tevékenység befejezési ideje timespan UTC formátumban fut. Ha a diagnosztikai napló azt mutatja, hogy egy tevékenység `1601-01-01T00:00:00Z`elkezdődött, de még nem ért véget, a tulajdonság értéke .  | `2017-06-26T20:55:29.5007959Z` |
|**Állapot**| Sztring | A folyamatfutás végleges állapota. A lehetséges `Succeeded` tulajdonságértékek a és `Failed`a. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Trigger-run napló attribútumok

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
| **Szint** |Sztring | A diagnosztikai naplók szintje. Tevékenységfuttatási naplók esetén állítsa a tulajdonság értékét 4-re. | `4` |
| **korrelációazonosító** |Sztring | Egy adott kérelem nyomon követéséhez szükséges egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Idő** | Sztring | Az esemény időpontja az UTC időtartományban `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Sztring| Az eseményindító futásának azonosítója. | `08587023010602533858661257311` |
|**resourceId**| Sztring | Az adat-gyári erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa a tulajdonság `PipelineRuns`értékét . | `PipelineRuns` |
|**Szinten**| Sztring | A diagnosztikai naplók szintje. Állítsa a tulajdonság `Informational`értékét . | `Informational` |
|**operationName**| Sztring | Az eseményindító neve a végső állapottal, amely azt jelzi, hogy az eseményindító sikeresen aktiválódott.The name of the trigger with its final status, which indicates whether the trigger successfully fired. Ha a szívverés sikeres volt, `MyTrigger - Succeeded`a tulajdonság értéke . | `MyTrigger - Succeeded` |
|**triggerName**| Sztring | Az eseményindító neve. | `MyTrigger` |
|**triggerType típus**| Sztring | A ravasz típusa. A lehetséges `Manual Trigger` tulajdonságértékek a és `Schedule Trigger`a. | `ScheduleTrigger` |
|**triggerEvent**| Sztring | Az esemény indítóeseménye. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Elkezd**| Sztring | Az eseményindító indítási időpontja timespan UTC formátumban. | `2017-06-26T20:55:29.5007959Z`|
|**Állapot**| Sztring | A végső állapot, amely megmutatja, hogy az eseményindító sikeresen aktiválódott-e. A lehetséges `Succeeded` tulajdonságértékek a és `Failed`a. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics-séma

A Log Analytics a következő kivételekkel örökli a sémát a Figyelőtől:

* Az egyes oszlopnevek első betűje nagybetűs. Például a "correlationId" oszlopnév a Figyelőben "CorrelationId" a Log Analytics.For example, the column name "correlationId" in Monitor is "CorrelationId" in Log Analytics.
* Nincs "Szint" oszlop.
* A dinamikus "properties" oszlop a következő dinamikus JSON blobtípusként marad meg.

    | Az Azure Monitor oszlop | Log Analytics oszlop | Típus |
    | --- | --- | --- |
    | $.properties. Felhasználói tulajdonságok | Felhasználói tulajdonságok | Dinamikus |
    | $.properties. Széljegyzetek | Széljegyzetek | Dinamikus |
    | $.properties. Bemenet | Input (Bemenet) | Dinamikus |
    | $.properties. Kimeneti | Kimenet | Dinamikus |
    | $.properties. Error.errorKód | ErrorCode | int |
    | $.properties. Hiba.üzenet | ErrorMessage üzenet | sztring |
    | $.properties. Hiba | Hiba | Dinamikus |
    | $.properties. Elődök | Elődök | Dinamikus |
    | $.properties. Paraméterek | Paraméterek | Dinamikus |
    | $.properties. SystemParameters (Rendszerparaméterek) | SystemParameters (Rendszerparaméterek) | Dinamikus |
    | $.properties. Címkék | Címkék | Dinamikus |
    
## <a name="metrics"></a>Mérőszámok

A Monitor segítségével betekintést nyerhet az Azure-munkaterhelések teljesítményébe és állapotába. A monitoradatok legfontosabb típusa a metrika, amelyet teljesítményszámlálónak is neveznek. Metrikák által kibocsátott legtöbb Azure-erőforrások. A Figyelő számos módszert kínál ezeknek a metrikáknak a figyeléséhez és a hibaelhárításhoz történő konfigurálására és felhasználására.

Az Azure Data Factory 2-es verziója a következő mutatókat bocsátja ki.

| **Metrika**           | **Metrikus megjelenítés neve**         | **Egység** | **Összesítés típusa** | **Leírás**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns között | Sikeres folyamatfuttatási metrikák | Darabszám    | Összesen                | Az egy percen belül sikeres folyamatfuttatások teljes száma. |
| PipelineFailedRuns   | Sikertelen folyamat futtatási metrikák    | Darabszám    | Összesen                | Az egy percen belül meghibásodott folyamatfuttatások teljes száma.    |
| ActivitySucceededRuns között | A sikeres tevékenység mérőszámait futtatja | Darabszám    | Összesen                | Az egy percen belül sikeres tevékenységfuttatások teljes száma.  |
| ActivityFailedRuns   | A sikertelen tevékenység mérőszámokat futtat    | Darabszám    | Összesen                | Az egy percen belül meghibásodott tevékenységfuttatások teljes száma.     |
| Aktiválássucceededruns | Sikeres eseményindító-futtatási mérőszámok  | Darabszám    | Összesen                | Az egy percen belül sikeres eseményindítók teljes száma.   |
| TriggerFailedRuns    | Sikertelen eseményindító futtatási mérőszámai     | Darabszám    | Összesen                | Az egy percen belül meghibásodott eseményindítók teljes száma.      |

A metrikák eléréséhez töltse ki az utasításokat az [Azure Monitor adatplatformjában.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

> [!NOTE]
> Csak befejezett, aktivált tevékenység és folyamatfuttatási események kerülnek kibocsátásra. Folyamatban és a sandbox/debug fut **nem** bocsát ki. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Adatgyári mérőszámok figyelése az Azure Monitorsegítségével

A Data Factory-integráció a Monitornal az adatok monitorra való továbbítására használható. Ez az integráció a következő esetekben hasznos:

* Összetett lekérdezéseket szeretne írni a Data Factory által figyelt metrikák gazdag készletére. Ezeken a lekérdezéseken egyéni riasztásokat hozhat létre a Figyelőn keresztül.

* Szeretné figyelni az adatgyárak között. Több adatgyárból is továbbíthat adatokat egyetlen Monitor-munkaterületre.

A funkció hétperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Diagnosztikai beállítások és munkaterület konfigurálása

Diagnosztikai beállítások at hozhat létre vagy adhat hozzá az adat-előállítóhoz.

1. A portálon nyissa meg a Figyelés. Válassza a **Beállítások** > **diagnosztikai beállításai lehetőséget**.

1. Válassza ki azt az adat-előállítóüzemet, amelyhez diagnosztikai beállítást szeretne beállítani.

1. Ha a kijelölt adateltelepen nincsenek beállítások, a rendszer kéri, hogy hozzon létre egy beállítást. Válassza **a Diagnosztika bekapcsolása**lehetőséget.

   ![Diagnosztikai beállítás létrehozása, ha nincsenek beállítások](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Ha az adat-előállítóban vannak beállítások, akkor megjelenik az adat-előállítón már konfigurált beállítások listája. Válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**

   ![Diagnosztikai beállítás hozzáadása, ha vannak beállítások](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Adjon nevet a beállításnak, válassza a **Küldés a Log Analytics szolgáltatásba**lehetőséget, majd válasszon ki egy munkaterületet a **Log Analytics-munkaterületről.**

    ![A beállítások elnevezése és a naplóelemzési munkaterület kiválasztása](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Kattintson a **Mentés** gombra.

Néhány pillanat múlva az új beállítás megjelenik az adat-előállító beállításainak listájában. A diagnosztikai naplók streamelése a munkaterületre, amint új eseményadatok jönnek létre. Akár 15 perc is eltelhet egy esemény kibocsátása és a Log Analytics-ben megjelenő idő között.

* _Erőforrás-specifikus_ módban az Azure Data Factory diagnosztikai naplói az _ADFPipelineRun_, _ADFTriggerRun_és _ADFActivityRun_ táblákba kerülnek.
* _Azure-Diagnostics_ módban a diagnosztikai naplók az _AzureDiagnostics_ táblába kerülnek

> [!NOTE]
> Mivel egy Azure-naplótábla nem rendelkezhet 500-nál több oszlopot, javasoljuk, hogy válassza ki az erőforrás-specifikus módot. További információt a Log Analytics ismert korlátai című témakörben [talál.](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Az Azure Data Factory Analytics telepítése az Azure Piactérről

![Lépjen az "Azure Piactérre", írja be az "Analytics-szűrő" kifejezést, és válassza az "Azure Data Factory Analytics (előzetes verzió" lehetőséget)](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Az "Azure Data Factory Analytics (előzetes verzió) részletei"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Válassza **a Létrehozás,** majd **az OMS-munkaterület** és **az OMS-munkaterület beállításai lehetőséget.**

![Új megoldás létrehozása](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Adatgyári mérőszámok figyelése

Az Azure Data Factory Analytics telepítése egy alapértelmezett nézetkészletet hoz létre, hogy a következő metrikák legyenek engedélyezve:

- ADF-futtatások – 1) A folyamat adatgyár szerint fut
 
- ADF fut- 2) Tevékenység fut a Data Factory

- ADF fut - 3) Trigger fut adatgyár

- ADF hibák - 1) Top 10 csővezeték hibák adatgyár

- ADF hibák - 2) Top 10 tevékenység fut a Data Factory

- ADF hibák - 3) Top 10 trigger hibák adatfactory

- ADF statisztika - 1) Tevékenység típus szerint fut

- ADF statisztika - 2) Az eseményindító típus szerint fut

- ADF statisztika - 3) Maximális csővezeték futtatási időtartam

![Ablak a "Munkafüzetek (előzetes verzió)" és az "AzureDataFactoryAnalytics" kiemelve](media/data-factory-monitor-oms/monitor-oms-image6.png)

Vizualizálhatja az előző mutatókat, megnézheti a metrikák mögötti lekérdezéseket, szerkesztheti a lekérdezéseket, riasztásokat hozhat létre, és egyéb műveleteket is végrehajthat.

![Az adatgyár által futtatott csővezeték grafikus ábrázolása"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Az Azure Data Factory Analytics (előzetes verzió) diagnosztikai naplókat küld _az erőforrás-specifikus_ céltáblákba. Lekérdezéseket a következő táblákba írhat: _ADFPipelineRun_, _ADFTriggerRun_és _ADFActivityRun_.

## <a name="alerts"></a>Riasztások

Jelentkezzen be az Azure Portalon, és válassza a Riasztások **figyelése** > **Alerts** riasztások létrehozásához.

![Riasztások a portál menüben](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Riasztások létrehozása

1. Új riasztás létrehozásához válassza a **+ Új riasztási szabályt.**

    ![Új riasztási szabály](media/monitor-using-azure-monitor/alerts_image4.png)

1. Adja meg a riasztási feltételt.

    > [!NOTE]
    > Győződjön meg arról, hogy a Szűrő erőforrástípus szerint legördülő listában az **Összes** lehetőséget **választja.**

    !["Riasztási feltétel megadása" > "Cél kiválasztása", amely megnyitja az "Erőforrás kiválasztása" ablaktáblát ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Riasztási feltétel definiálása" >" Feltételek hozzáadása", amely megnyitja a "Jellogika konfigurálása" ablaktáblát](media/monitor-using-azure-monitor/alerts_image6.png)

    !["Jeltípus konfigurálása" ablaktábla](media/monitor-using-azure-monitor/alerts_image7.png)

1. Adja meg a riasztás részleteit.

    ![Riasztás részletei](media/monitor-using-azure-monitor/alerts_image8.png)

1. Adja meg a műveletcsoportot.

    ![Szabály létrehozása kiemelt "Új műveletcsoport" lehetőséggel](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Új műveletcsoport létrehozása](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-mail, SMS, push és hang konfigurálása](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Műveletcsoport definiálása](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>További lépések
[Folyamatok programozási figyelése és kezelése](monitor-programmatically.md)
