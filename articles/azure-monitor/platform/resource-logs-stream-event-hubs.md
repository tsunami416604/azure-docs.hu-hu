---
title: Azure platform-naplók továbbítása egy Event hubhoz
description: Ismerje meg, hogyan továbbíthatja az Azure-beli erőforrás-naplókat egy Event hub-ba, ha külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra küldi az információkat
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658914"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Azure platform-naplók továbbítása az Azure Event Hubsba
Az Azure [platform-naplói](platform-logs-overview.md) , beleértve az Azure-tevékenységeket és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokról és az azoktól függő Azure-platformról.  Ez a cikk a streaming platform naplófájljait ismerteti az Event hubok számára az adatküldés külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Az Event hub-nak eljuttatott platform-naplókból elvégezhető műveletek
Stream platform-naplók az Azure-ban az Event hubokba a következő funkciók biztosításához:

* **Stream-naplók harmadik féltől származó naplózási és telemetria rendszerekhez** – az összes platform naplóját egyetlen esemény központba továbbíthatja, hogy az adatcsatorna adatai egy harmadik féltől származó Siem-vagy log Analytics-eszközbe lépjenek.
  
* **Hozzon létre egy egyéni telemetria-és naplózási platformot** – az Event hubok rugalmasan méretezhető közzétételi és előfizetési felépítése lehetővé teszi, hogy a platform-naplókat rugalmasan betöltse egy egyéni teletry platformra. A részletekért lásd: [globális méretű telemetria platform tervezése és méretezése az Azure Event Hubsban](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* A **szolgáltatás állapotának megtekintése az adattovábbítással Power BIba** – Event Hubs, Stream Analytics és Power bi használatával alakítsa át a diagnosztikai adatait az Azure-szolgáltatások közel valós idejű elemzéséhez. A megoldás részleteiért tekintse meg a [stream Analytics és Power bi: valós idejű elemzési irányítópultot a folyamatos adattovábbításhoz](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

    A következő SQL-kód egy minta Stream Analytics-lekérdezés, amellyel az összes naplózási adattal elemezhető egy Power BI táblázatba:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Előfeltételek
Ha még nem rendelkezik ilyennel, [létre kell hoznia egy Event hubot](../../event-hubs/event-hubs-create.md) . Ha már rendelkezik diagnosztikai beállítással ezzel a Event Hubs névtérrel, az Event hub újra felhasználható lesz.

A névtérhez tartozó megosztott hozzáférési házirend határozza meg az adatfolyam-mechanizmushoz tartozó engedélyeket. A Event Hubs való folyamatos átvitelhez a kezelés, a Küldés és a figyelés engedélyek szükségesek. A megosztott hozzáférési szabályzatokat a Event Hubs névtér configure (Konfigurálás) lapján lehet létrehozni vagy módosítani a Azure Portal.

Ha szeretné frissíteni a diagnosztikai beállítást, hogy a folyamatos átvitelt is tartalmazza, rendelkeznie kell a ListKey engedéllyel az adott Event Hubs engedélyezési szabályhoz. A Event Hubs névtérnek nem kell ugyanabban az előfizetésben lennie, mint a naplókat kibocsátó előfizetésnek, feltéve, hogy a beállítást konfiguráló felhasználó RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez, és mindkét előfizetés ugyanahhoz a HRE-bérlőhöz tartozik.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
Az Azure-erőforrások diagnosztikai beállításának létrehozásával elküldheti a platform naplóit az Event hub és más célhelyek számára. A részletekért lásd: [diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md) .

## <a name="collect-data-from-compute-resources"></a>Adatok gyűjtése a számítási erőforrásokból
A diagnosztikai beállítások olyan erőforrás-naplókat gyűjtenek az Azure számítási erőforrásaihoz, mint bármely más erőforrás, nem pedig a vendég operációs rendszerük vagy a munkaterhelések. Az adatok gyűjtéséhez telepítse a [log Analytics-ügynököt](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Az Event hubokból származó naplózási adatok felhasználása
Az Event hubokból származó platform-naplók JSON formátumban lesznek felhasználva az alábbi táblázat elemeivel.

| Elem neve | Leírás |
| --- | --- |
| rekordok |A hasznos adatok összes naplózási eseményének tömbje. |
| time |Az esemény időpontja. |
| category |Az eseményhez tartozó napló kategóriája. |
| resourceId |Az eseményt létrehozó erőforrás erőforrás-azonosítója. |
| operationName |A művelet neve. |
| szint |Választható. A naplózási esemény szintjét jelzi. |
| properties |Az esemény tulajdonságai. Ezek a következő témakörben leírtak szerint változnak [ ]()minden Azure-szolgáltatás esetében. |


Az alábbi példa kimeneti adatokat Event Hubs egy erőforrás-naplóhoz:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](platform-logs-overview.md).
* [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md).
* [A Stream Azure Active Directory a naplókat a Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [A Event Hubs első lépései](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

