---
title: Az adatüzemek figyelése Azure Monitor használatával
description: Megtudhatja, hogyan használhatja a Azure Monitort a/Azure Data Factory folyamatok figyeléséhez, ha engedélyezi a diagnosztikai naplókat a Data Factory információi alapján.
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
ms.openlocfilehash: 67709ef96ffb8190812d625c04cd9749c0ebb900
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684622"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Az adatüzemek riasztása és monitorozása Azure Monitor használatával

A Felhőbeli alkalmazások összetettek és számos mozgó részből állnak. A figyelők segítségével biztosítható, hogy az alkalmazások kifogástalan állapotban maradjanak és fussanak. A figyelők segítenek elkerülni a lehetséges problémákat és a múltbeli hibák elhárítását is.

A monitorozási adataival részletes elemzéseket nyerhet az alkalmazásairól. Ez az információ segít az alkalmazások teljesítményének és karbantartásának javításában. Emellett segít automatizálni azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

A Azure Monitor a legtöbb Azure-szolgáltatás alapszintű infrastruktúrájának mérőszámait és naplóit biztosítja. Az Azure diagnosztikai naplókat egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. És Azure Data Factory diagnosztikai naplókat ír a figyelőben.

Részletekért lásd: [Azure monitor áttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Azure Data Factory-adatvédelem megőrzése

A Data Factory csak 45 napig tárolja az adatfeldolgozási folyamatokat. Ha továbbra is meg szeretné őrizni az adott időt, használja a monitort. A monitor segítségével a diagnosztikai naplókat átirányíthatja az elemzéshez. Megtarthatja azokat egy Storage-fiókban is, hogy a kiválasztott időtartamra vonatkozóan rendelkezzen gyári információval.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

* A diagnosztikai naplókat a naplózáshoz vagy a manuális ellenőrzéshez mentse egy Storage-fiókba. A diagnosztikai beállítások segítségével megadhatja a megőrzési időt napokban.
* Továbbítsa a naplókat az Azure Event Hubsba. A naplók bekerülnek egy partneri szolgáltatásba vagy egy egyéni analitikai megoldásba, például a Power BIba.
* A naplók elemzése Log Analyticsokkal.

Használhat olyan Storage-fiókot vagy Event-hub névteret, amely nem szerepel a naplókat kibocsátó erőforrás előfizetésében. A beállítást konfiguráló felhasználónak megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) hozzáféréssel kell rendelkeznie mindkét előfizetéshez.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Diagnosztikai beállítások használatával konfigurálhatja a nem számítási erőforrások diagnosztikai naplóit. Az erőforrás-vezérlő beállításai az alábbi funkciókkal rendelkeznek:

* Megadják a diagnosztikai naplók elküldéseinak helyét. Ilyenek például az Azure Storage-fiók, az Azure Event hub vagy a monitoring-naplók.
* Ezek határozzák meg, hogy mely naplózási kategóriákat küldik el a rendszer.
* Azt határozzák meg, hogy mennyi ideig kell megőrizni az egyes naplók kategóriáit egy Storage-fiókban.
* A nulla nap megőrzése azt jelenti, hogy a naplók örökre megmaradnak. Ellenkező esetben az érték tetszőleges számú nap lehet, 1-től 2 147 483 647-ig.
* Ha adatmegőrzési házirend van beállítva, de a naplók tárolása egy Storage-fiókban le van tiltva, az adatmegőrzési szabályzatok nem lépnek érvénybe. Ez a feltétel például akkor fordulhat elő, ha csak a Event Hubs vagy a figyelési naplók lehetőség van kiválasztva.
* Az adatmegőrzési szabályzatok naponta lesznek alkalmazva. A tartomány napjai közötti határ éjfélkor, az egyezményes világidő (UTC) szerint történik. A nap végén a rendszer törli az adatmegőrzési házirenden túli napokat. Ha például egy nap adatmegőrzési szabályzattal rendelkezik, a rendszer a tegnapi naptól kezdve törli a naplókat.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnosztikai naplók engedélyezése a Azure Monitor használatával REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Diagnosztikai beállítás létrehozása vagy frissítése a figyelő REST API

##### <a name="request"></a>Kérés

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Fejlécek

* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* Cserélje le a `{resource-id}`t annak az erőforrásnak az azonosítójával, amelynek a diagnosztikai beállításait szerkeszteni kívánja. További információk: [Using Resource groups to manage your Azure resources](../azure-resource-manager/manage-resource-groups-portal.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez).
* Állítsa a `Content-Type` fejlécet `application/json`re.
* Állítsa be az engedélyezési fejlécet a Azure Active Directory (Azure AD) által kapott JSON webes tokenre. További információ: [kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md).

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
| **storageAccountId** |Sztring | Annak a Storage-fióknak az erőforrás-azonosítója, amelyhez diagnosztikai naplókat szeretne küldeni. |
| **serviceBusRuleId** |Sztring | Annak a Service-Bus-névtérnek az azonosítója, amelyben a Event Hubs létre kívánja hozni a stream diagnosztikai naplóihoz. A szabály AZONOSÍTÓjának formátuma `{service bus resource ID}/authorizationrules/{key name}`.|
| **Munkaterület azonosítója** | Összetett típus | Metrikus idő típusú gabona és adatmegőrzési szabályzatok tömbje. A tulajdonság értéke üres. |
|**metrics**| A folyamat által a meghívott folyamatnak átadni kívánt folyamat paramétereinek értéke| Egy JSON-objektum, amely a paraméterek nevét az argumentum értékére képezi. |
| **naplók**| Összetett típus| Az erőforrástípus diagnosztikai-naplózási kategóriájának neve. Az erőforráshoz tartozó diagnosztikai naplók listájának beszerzéséhez hajtsa végre a diagnosztikai beállítások beolvasása műveletet. |
| **Kategória**| Sztring| Naplózási kategóriák és adatmegőrzési szabályzatok tömbje. |
| **timeGrain** | Sztring | A metrikák részletessége ISO 8601 időtartam formátumban rögzítve. A tulajdonság értékének `PT1M`kell lennie, amely egy percet ad meg. |
| **engedélyezve**| Logikai | Meghatározza, hogy engedélyezve van-e a metrika vagy a napló kategóriájának gyűjteménye ehhez az erőforráshoz. |
| **retentionPolicy**| Összetett típus| A metrika vagy a napló kategóriájának adatmegőrzési szabályát ismerteti. Ez a tulajdonság csak a Storage-fiókok esetében használatos. |
|**nap**| int| A metrikák vagy naplók megtartására szolgáló napok száma. Ha a tulajdonság értéke 0, a naplók örökre megmaradnak. Ez a tulajdonság csak a Storage-fiókok esetében használatos. |

##### <a name="response"></a>Válasz

200 OK.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>A figyelő diagnosztikai beállításaival kapcsolatos információk beolvasása REST API

##### <a name="request"></a>Kérés

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Fejlécek

* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* Cserélje le a `{resource-id}`t annak az erőforrásnak az azonosítójával, amelynek a diagnosztikai beállításait szerkeszteni kívánja. További információk: [Using Resource groups to manage your Azure resources](../azure-resource-manager/manage-resource-groups-portal.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez).
* Állítsa a `Content-Type` fejlécet `application/json`re.
* Állítsa be az engedélyezési fejlécet egy olyan JSON webes tokenre, amelyet az Azure AD-ből kapott. További információ: [kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Válasz

200 OK.

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
További információ: [diagnosztikai beállítások](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Naplók és események sémája

### <a name="monitor-schema"></a>Séma figyelése

#### <a name="activity-run-log-attributes"></a>Tevékenység – napló attribútumainak futtatása

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
| **Szint** |Sztring | A diagnosztikai naplók szintje. A tevékenységek futtatására szolgáló naplók esetében állítsa a tulajdonság értékét 4 értékre. | `4` |
| **correlationId** |Sztring | Egy adott kérelem nyomon követésére szolgáló egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **idő** | Sztring | Az esemény időpontja a TimeSpan UTC formátumban `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Sztring| A tevékenység futtatásának azonosítója. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Sztring| A folyamat futtatásának azonosítója. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sztring | Az adatfeldolgozó erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa `ActivityRuns`értékre a tulajdonság értékét. | `ActivityRuns` |
|**szintű**| Sztring | A diagnosztikai naplók szintje. Állítsa `Informational`értékre a tulajdonság értékét. | `Informational` |
|**operationName**| Sztring | Az állapottal rendelkező tevékenység neve. Ha a tevékenység a kezdő szívverés, a tulajdonság értéke `MyActivity -`. Ha a tevékenység a záró szívverés, a tulajdonság értéke `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| Sztring | A folyamat neve. | `MyPipeline` |
|**activityName**| Sztring | A tevékenység neve. | `MyActivity` |
|**start**| Sztring | A tevékenység kezdési időpontja TimeSpan UTC formátumban fut. | `2017-06-26T20:55:29.5007959Z`|
|**végén**| Sztring | A tevékenység befejezési időpontja TimeSpan UTC formátumban fut. Ha a diagnosztikai napló azt mutatja, hogy egy tevékenység megkezdődött, de még nem fejeződött be, a tulajdonság értéke `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Folyamat – naplózási attribútumok futtatása

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
| **Szint** |Sztring | A diagnosztikai naplók szintje. A tevékenységek futtatására szolgáló naplók esetében állítsa a tulajdonság értékét 4 értékre. | `4` |
| **correlationId** |Sztring | Egy adott kérelem nyomon követésére szolgáló egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **idő** | Sztring | Az esemény időpontja a TimeSpan UTC formátumban `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Sztring| A folyamat futtatásának azonosítója. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sztring | Az adatfeldolgozó erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa `PipelineRuns`értékre a tulajdonság értékét. | `PipelineRuns` |
|**szintű**| Sztring | A diagnosztikai naplók szintje. Állítsa `Informational`értékre a tulajdonság értékét. | `Informational` |
|**operationName**| Sztring | A folyamat neve az állapotával együtt. A folyamat futásának befejeződése után a tulajdonság értéke `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| Sztring | A folyamat neve. | `MyPipeline` |
|**start**| Sztring | A tevékenység kezdési időpontja TimeSpan UTC formátumban fut. | `2017-06-26T20:55:29.5007959Z`. |
|**végén**| Sztring | A tevékenység befejezési időpontja TimeSpan UTC formátumban fut. Ha a diagnosztikai naplóban egy tevékenység megkezdődött, de még nem fejeződött be, a tulajdonság értéke `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**állapota**| Sztring | A folyamat futásának végső állapota. A lehetséges tulajdonságértékek `Succeeded` és `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Trigger – napló attribútumainak futtatása

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
| **Szint** |Sztring | A diagnosztikai naplók szintje. A tevékenységek futtatására szolgáló naplók esetében állítsa a tulajdonság értékét 4 értékre. | `4` |
| **correlationId** |Sztring | Egy adott kérelem nyomon követésére szolgáló egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **idő** | Sztring | Az esemény időpontja a TimeSpan UTC formátumban `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Sztring| Az trigger futtatásának azonosítója. | `08587023010602533858661257311` |
|**resourceId**| Sztring | Az adatfeldolgozó erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa `PipelineRuns`értékre a tulajdonság értékét. | `PipelineRuns` |
|**szintű**| Sztring | A diagnosztikai naplók szintje. Állítsa `Informational`értékre a tulajdonság értékét. | `Informational` |
|**operationName**| Sztring | Az trigger neve a végső állapottal, amely azt jelzi, hogy az trigger sikeresen kilőtte-e. Ha a szívverés sikeres volt, a tulajdonság értéke `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| Sztring | Az trigger neve. | `MyTrigger` |
|**triggerType**| Sztring | Az trigger típusa. A lehetséges tulajdonságértékek `Manual Trigger` és `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| Sztring | Az eseményindító eseménye. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| Sztring | A trigger égetésének kezdési időpontja TimeSpan UTC formátumban. | `2017-06-26T20:55:29.5007959Z`|
|**állapota**| Sztring | Az a végső állapot, amely azt jelzi, hogy az trigger sikeresen kirúgták-e. A lehetséges tulajdonságértékek `Succeeded` és `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics séma

A Log Analytics a következő kivételekkel örökli a sémát a Figyelőtől:

* Az egyes oszlopok nevének első betűje tőkésített. A "correlationId" oszlop neve például "CorrelationId" a következőben: Log Analytics.
* Nincs "level" oszlop.
* A dinamikus "tulajdonságok" oszlop megmarad a következő dinamikus JSON-blob típusként.

    | Azure Monitor oszlop | Log Analytics oszlop | Típus |
    | --- | --- | --- |
    | $. properties. UserProperties | UserProperties | Dinamikus |
    | $. properties. Széljegyzetek | Széljegyzetek | Dinamikus |
    | $. properties. Bemeneti | Input (Bemenet) | Dinamikus |
    | $. properties. Kimeneti | Kimenet | Dinamikus |
    | $. properties. Hiba. errorCode | ErrorCode | int |
    | $. properties. Hiba. üzenet | errorMessage | sztring |
    | $. properties. Hiba | Hiba | Dinamikus |
    | $. properties. Korábbi verzióknál | Korábbi verzióknál | Dinamikus |
    | $. properties. Paraméterek | Paraméterek | Dinamikus |
    | $. properties. SystemParameters | SystemParameters | Dinamikus |
    | $. properties. Címkék | Címkék | Dinamikus |
    
## <a name="metrics"></a>Mérőszámok

A monitor segítségével megtekintheti az Azure-beli számítási feladatok teljesítményét és állapotát. A monitorozási adatok legfontosabb típusa a metrika, amelyet a teljesítményszámláló is nevez. A metrikákat a legtöbb Azure-erőforrás kibocsátja. A figyelő számos módszert biztosít ezen mérőszámok konfigurálásához és felhasználásához a figyeléshez és a hibaelhárításhoz.

A Azure Data Factory 2. verziója a következő metrikákat bocsátja ki.

| **Metrika**           | **Metrika megjelenítendő neve**         | **Egység** | **Összesítés típusa** | **Leírás**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | A folyamat sikeresen futtatja a metrikákat | Mennyiség    | Összesen                | A perceken belül sikeres folyamat-futtatások teljes száma. |
| PipelineFailedRuns   | Sikertelen folyamat-futtatási metrikák    | Mennyiség    | Összesen                | A meghiúsult folyamat-futtatások teljes száma egy percen belül.    |
| ActivitySucceededRuns | A sikeres tevékenység metrikákat futtat | Mennyiség    | Összesen                | Az egy percen belül sikeres tevékenység-futtatások teljes száma.  |
| ActivityFailedRuns   | Sikertelen tevékenység-futtatási metrikák    | Mennyiség    | Összesen                | A meghiúsult tevékenység-futtatások teljes száma egy percen belül.     |
| TriggerSucceededRuns | A sikeres trigger metrikákat futtat  | Mennyiség    | Összesen                | Egy percen belül sikeresen elindított trigger-futtatások teljes száma.   |
| TriggerFailedRuns    | Sikertelen trigger-futtatási metrikák     | Mennyiség    | Összesen                | A sikertelen trigger-futtatások teljes száma egy percen belül.      |

A metrikák eléréséhez hajtsa végre az [Azure monitor adatplatformon](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)megjelenő utasításokat.

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Data Factory metrikák figyelése Azure Monitor

Data Factory-integrációt és figyelőt is használhat a figyeléshez. Ez az integráció az alábbi helyzetekben hasznos:

* Összetett lekérdezéseket szeretne írni egy Data Factory által a figyelésre közzétett mérőszámok gazdag készletén. A lekérdezésekhez egyéni riasztásokat is létrehozhat.

* Az adatüzemek között szeretne figyelni. Több adatgyárból is átirányíthat adatait egyetlen figyelő munkaterületre.

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Diagnosztikai beállítások és munkaterület konfigurálása

Hozzon létre vagy adjon hozzá diagnosztikai beállításokat az adatai-előállítóhoz.

1. A portálon lépjen a figyelés elemre. Válassza a **beállítások** > **diagnosztikai beállítások**lehetőséget.

1. Válassza ki azt az adatelőállítót, amelyhez diagnosztikai beállítást kíván beállítani.

1. Ha a kiválasztott adatgyárban nem találhatók beállítások, a rendszer kéri, hogy hozzon létre egy beállítást. Kattintson **a diagnosztika bekapcsolása**elemre.

   ![Diagnosztikai beállítás létrehozása, ha nem léteznek beállítások](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Ha vannak meglévő beállítások az adatgyárban, megjelenik az adatgyárban már konfigurált beállítások listája. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.

   ![Diagnosztikai beállítás hozzáadása, ha a beállítások léteznek](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Adja meg a beállítás nevét, válassza a **küldés log Analytics**lehetőséget, majd válasszon ki egy munkaterületet **log Analytics munkaterületről**.

    ![Adja meg a beállításokat, és válasszon egy log-Analytics-munkaterületet](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Kattintson a **Mentés** gombra.

Néhány pillanat elteltével az új beállítás megjelenik az adat-előállító beállításainak listájában. A rendszer a diagnosztikai naplókat az adott munkaterületre továbbítja, amint új esemény-adatforrások jönnek létre. Akár 15 percig is eltarthat egy esemény kibocsátása, és amikor megjelenik a Log Analyticsban.

* _Erőforrás-specifikus_ módban a Azure Data Factory flow diagnosztikai naplói a _ADFPipelineRun_, a _ADFTriggerRun_és a _ADFActivityRun_ táblákba kerülnek
* _Azure-Diagnostics_ módban a diagnosztikai naplók az _AzureDiagnostics_ táblába kerülnek

> [!NOTE]
> Mivel az Azure-tábla nem rendelkezhet több mint 500 oszloppal, javasoljuk, hogy az erőforrás-specifikus módot válassza. További információ: [log Analytics ismert korlátozások](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Data Factory Analytics telepítése az Azure Marketplace-ről

![Nyissa meg az "Azure Marketplace" kifejezést, írja be az "elemzési szűrő" kifejezést, és válassza a "Azure Data Factory Analytics (előzetes verzió)" lehetőséget.](media/data-factory-monitor-oms/monitor-oms-image3.png)

![A "Azure Data Factory Analytics (előzetes verzió)" részletei](media/data-factory-monitor-oms/monitor-oms-image4.png)

Válassza a **Létrehozás** lehetőséget, majd válassza ki a **OMS-munkaterület** és a OMS- **munkaterület beállításait**.

![Új megoldás létrehozása](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory metrikák figyelése

A Azure Data Factory Analytics telepítése a nézetek alapértelmezett készletét hozza létre, így a következő metrikák válnak elérhetővé:

- ADF-futtatások – 1) a folyamat futása Data Factory
 
- ADF-futtatások – 2) a tevékenységek Data Factory

- ADF-futtatások – 3) a triggerek Data Factory

- ADF-hibák – 1) az első 10 folyamat hibája Data Factory

- ADF-hibák – 2) az első 10 tevékenység fut Data Factory

- ADF-hibák – 3) az első 10 kiváltó hibát Data Factory

- ADF-statisztika – 1) a tevékenység típus szerint fut

- ADF-statisztika – 2) trigger-futtatások típus szerint

- ADF-statisztika – 3) a folyamat maximális futási időtartama

![A "munkafüzetek (előzetes verzió)" és a "AzureDataFactoryAnalytics" nevű ablak kiemelve](media/data-factory-monitor-oms/monitor-oms-image6.png)

Megjelenítheti az előző mérőszámokat, megtekintheti a mérőszámok mögötti lekérdezéseket, szerkesztheti a lekérdezéseket, riasztásokat hozhat létre, és egyéb műveleteket is végrehajthat.

![A folyamat futtatásának grafikus ábrázolása a adat-előállítóban "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> A Azure Data Factory Analytics (előzetes verzió) diagnosztikai naplókat küld az _erőforrás-specifikus_ célhelyekre. A következő táblázatokból is írhat lekérdezéseket: _ADFPipelineRun_, _ADFTriggerRun_és _ADFActivityRun_.

## <a name="alerts"></a>Riasztások

Jelentkezzen be a Azure Portalba, és válassza a **figyelő** > **riasztások** lehetőséget a riasztások létrehozásához.

![Riasztások a portál menüjében](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Riasztások létrehozása

1. Új riasztás létrehozásához válassza az **+ új riasztási szabály** lehetőséget.

    ![Új riasztási szabály](media/monitor-using-azure-monitor/alerts_image4.png)

1. Adja meg a riasztási feltételt.

    > [!NOTE]
    > Ügyeljen rá, hogy a **szűrés erőforrás típusa** legördülő listában válassza az **összes** lehetőséget.

    !["A riasztási feltétel meghatározása" > "válassza a cél" lehetőséget, amely megnyitja az "erőforrás kiválasztása" panelt. ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["A riasztási feltétel meghatározása" > "feltételek hozzáadása", amely megnyitja a "jel logikai beállítása" panelt.](media/monitor-using-azure-monitor/alerts_image6.png)

    !["A jel típusának konfigurálása" panel](media/monitor-using-azure-monitor/alerts_image7.png)

1. Adja meg a riasztás részleteit.

    ![Riasztás részletei](media/monitor-using-azure-monitor/alerts_image8.png)

1. A műveleti csoport definiálása.

    ![Szabály létrehozása a Kiemelt "új műveleti csoporttal"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Új műveleti csoport létrehozása](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-mailek, SMS-cím, leküldéses és hangvezéreltség konfigurálása](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Műveleti csoport definiálása](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>További lépések
[Folyamatok programozott figyelése és kezelése](monitor-programmatically.md)
