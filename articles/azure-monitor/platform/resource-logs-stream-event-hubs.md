---
title: Az Azure platformnaplóinak streamelése egy eseményközpontba
description: Megtudhatja, hogyan streamelheti az Azure-erőforrásnaplókat egy eseményközpontba, hogy adatokat küldjön külső rendszereknek, például külső gyártóknak és más naplóelemzési megoldásoknak.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658914"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Az Azure platformnaplók streamelése az Azure Event Hubs-ba
Az Azure-beli [platformnaplók,](platform-logs-overview.md) beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat nyújtanak az Azure-erőforrásokhoz és az Azure platformhoz, amelytől függenek.  Ez a cikk ismerteti a streamelési platform naplók eseményközpontok adatok küldése külső rendszerek, például külső siems és más log elemzési megoldások.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Az eseményközpontba küldött platformnaplókkal kapcsolatban használható?
Az Azure-ban a streamplatform-naplók at az eseményközpontokba továbbítják a következő funkciók biztosítása érdekében:

* **Naplót streamelhet külső gyártótól származó naplózási és telemetriai rendszerekbe** – az összes platformnaplót egyetlen eseményközpontba streamelheti, hogy egy külső SIEM-be vagy log-elemző eszközre naplóadatokat továbbítson.
  
* **Egyéni telemetriai és naplózási platform létrehozása** – Az eseményközpontok nagymértékben skálázható közzétételi-előfizetési jellege lehetővé teszi a platformnaplók rugalmas betöltését egy egyéni teletry platformra. A részletekért tekintse meg [a globális méretezési telemetriai platform tervezése és méretezése az Azure Event Hubs-on.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

* **A szolgáltatás állapotának megtekintése streamelési adatok használatával a Power BI-ba** – Az Event Hubs, a Stream Analytics és a Power BI segítségével a diagnosztikai adatokat közel valós idejű elemzési adatokká alakíthatja az Azure-szolgáltatásokban. Lásd: Stream Analytics és Power BI: A megoldás részleteiért tekintse meg a Stream Analytics és a Power BI: A megoldással kapcsolatos részletekért tekintse meg a [valós idejű elemzési irányítópultot.](../../stream-analytics/stream-analytics-power-bi-dashboard.md)

    A következő SQL-kód egy minta Stream Analytics-lekérdezés, amely segítségével elemezheti az összes naplóadatot egy Power BI-táblában:
    
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
Létre kell [hoznia egy eseményközpontot,](../../event-hubs/event-hubs-create.md) ha még nem rendelkezik ilyen. Ha már rendelkezik egy diagnosztikai beállítással ezzel az Event Hubs névtérrel, majd az eseményközpontot újra felhasználja a rendszer.

A névtér megosztott hozzáférési házirendje határozza meg a streamelési mechanizmus engedélyeit. Az Event Hubs-ba való streameléshez kezelési, küldési és figyelési engedélyek szükségesek. Az Azure Portalon létrehozhat vagy módosíthat megosztott hozzáférési szabályzatokat az Event Hubs névtér konfigurálása lapján.

A diagnosztikai beállítás streamelési szintre való frissítéséhez listkey engedéllyel kell rendelkeznie az adott Eseményközpont engedélyezési szabályához. Az Event Hubs névtér nem kell ugyanabban az előfizetésben, mint az előfizetés, amely a naplókat, mindaddig, amíg a felhasználó, aki konfigurálja a beállítást, megfelelő RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez, és mindkét előfizetés ugyanabban az AAD-bérlőben van.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
Platformnaplók küldése egy eseményközpontba és más célhelyekre egy Azure-erőforrás diagnosztikai beállításának létrehozásával. A naplók [és metrikák azure-beli gyűjtéséhez](diagnostic-settings.md) olvassa el a Diagnosztikai beállítás létrehozása című témakört.

## <a name="collect-data-from-compute-resources"></a>Adatok gyűjtése számítási erőforrásokból
A diagnosztikai beállítások erőforrásnaplókat gyűjtenek az Azure számítási erőforrásaihoz, mint bármely más erőforráshoz, de a vendég operációs rendszerüket vagy a számítási feladataikat nem. Az adatok gyűjtéséhez telepítse a [Log Analytics-ügynököt.](log-analytics-agent.md) 


## <a name="consuming-log-data-from-event-hubs"></a>Naplóadatok fogyasztása eseményközpontokból
Az eseményközpontokból származó platformnaplók JSON formátumban kerülnek felhasználásra az alábbi táblázat elemeivel.

| Elem neve | Leírás |
| --- | --- |
| Rekordok |A hasznos adatban lévő összes naplóesemény tömbje. |
| time |Az az időpont, amikor az esemény bekövetkezett. |
| category |Az esemény naplózási kategóriája. |
| resourceId |Az eseményt létrehozó erőforrás erőforrásazonosítója. |
| operationName |A művelet neve. |
| szint |Választható. A napló eseményszintjét jelzi. |
| properties |Az esemény tulajdonságai. Ezek az egyes Azure-szolgáltatások [ ]()esetében a ( a) című részben leírtak szerint változnak. |


A következőkben az Event Hubs-ból származó kimeneti adatok egy erőforrásnapló:

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



## <a name="next-steps"></a>További lépések

* [További információ az erőforrásnaplókról.](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozásával naplót és metrikákat gyűjthet az Azure-ban.](diagnostic-settings.md)
* [Streamelje az Azure Active Directory-naplókat az Azure Monitor segítségével.](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Ismerkedés az Event Hubs-szal.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

