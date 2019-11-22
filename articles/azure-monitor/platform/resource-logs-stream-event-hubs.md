---
title: Azure-beli erőforrás-naplók továbbítása egy Event hubhoz
description: Ismerje meg, hogyan továbbíthatja az Azure-beli erőforrás-naplókat egy Event hub-ba, ha külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra küldi az információkat
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 680570c5102f656b2b2d2e05f9e08f51fe892f44
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304939"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Azure-beli erőforrás-naplók továbbítása az Azure Event Hubsba
Az Azure-beli [erőforrás-naplók](resource-logs-overview.md) részletes és gyakori információkat biztosítanak az Azure-erőforrások belső működéséről. Ez a cikk azt ismerteti, hogyan lehet adatfolyamokat továbbítani az esemény-hubok számára az olyan külső rendszerekre, mint a harmadik féltől származó SIEM-és egyéb log Analytics-megoldások.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Az Event hub-nak eljuttatott erőforrás-naplókból elvégezhető műveletek
Stream erőforrás-naplók az Azure-ban az Event hubokba a következő funkciók biztosításához:

* **Stream-naplók harmadik féltől származó naplózási és telemetria rendszerekhez** – az összes erőforrás-naplót egy adott esemény központba továbbíthatja, hogy az adatcsatornán át lehessen továbbítani az adatait egy külső Siem-vagy log Analytics-eszközre.
* **Hozzon létre egy egyéni telemetria-és naplózási platformot** – az Event hubok rugalmasan méretezhető közzétételi és előfizetési környezete lehetővé teszi az erőforrás-naplók rugalmas betöltését egy egyéni teletry-platformra. A részletekért lásd: [globális méretű telemetria platform tervezése és méretezése az Azure Event Hubsban](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

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
Ha még nem rendelkezik ilyennel, [létre kell hoznia egy Event hubot](../../event-hubs/event-hubs-create.md) . Ha korábban az erőforrás-naplókat a Event Hubs névtérbe továbbította, akkor az Event hub újra fel lesz használva.

A névtérhez tartozó megosztott hozzáférési házirend határozza meg az adatfolyam-mechanizmushoz tartozó engedélyeket. A Event Hubs való folyamatos átvitelhez a kezelés, a Küldés és a figyelés engedélyek szükségesek. A megosztott hozzáférési szabályzatokat a Event Hubs névtér configure (Konfigurálás) lapján lehet létrehozni vagy módosítani a Azure Portal.

Ha szeretné frissíteni a diagnosztikai beállítást, hogy a folyamatos átvitelt is tartalmazza, rendelkeznie kell a ListKey engedéllyel az adott Event Hubs engedélyezési szabályhoz. A Event Hubs névtérnek nem kell ugyanabban az előfizetésben lennie, mint a naplókat kibocsátó előfizetésnek, feltéve, hogy a beállítást konfiguráló felhasználó RBAC-hozzáféréssel rendelkezik mindkét előfizetéshez, és mindkét előfizetés ugyanahhoz a HRE-bérlőhöz tartozik.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
A rendszer alapértelmezés szerint nem gyűjti az erőforrás-naplókat. Az Azure-erőforrások diagnosztikai beállításának létrehozásával elküldheti őket az Event hub és más célhelyek számára. A részletekért lásd: [diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md) .

## <a name="stream-data-from-compute-resources"></a>Adatfolyam-adatok a számítási erőforrásokból
A jelen cikkben ismertetett folyamat az [Azure-erőforrás-naplók áttekintése](diagnostic-settings.md)című témakörben leírtak szerint nem számítási erőforrásokra mutat.
Stream erőforrás-naplók az Azure számítási erőforrásaiból a Windows Azure Diagnostics ügynök használatával. A részletekért tekintse meg a következő témakört: [Streaming Azure Diagnostics adatok a gyors elérési úton Event Hubs használatával](diagnostics-extension-stream-event-hubs.md) .


## <a name="consuming-log-data-from-event-hubs"></a>Az Event hubokból származó naplózási adatok felhasználása
Amikor erőforrás-naplókat használ az Event hubokból, az a következő táblázatban szereplő elemekkel JSON formátumú lesz.

| Elem neve | Leírás |
| --- | --- |
| rekordok |A hasznos adatok összes naplózási eseményének tömbje. |
| time |Az esemény időpontja. |
| category |Az eseményhez tartozó napló kategóriája. |
| resourceId |Az eseményt létrehozó erőforrás erőforrás-azonosítója. |
| operationName |A művelet neve. |
| szint |Választható. A naplózási esemény szintjét jelzi. |
| properties |Az esemény tulajdonságai. Ezek a következő témakörben leírtak szerint változnak [ ]()minden Azure-szolgáltatás esetében. |


A következő példa kimeneti adatokat Event Hubs:

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

* [A Stream Azure Active Directory a naplókat a Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [További információ az Azure erőforrás-naplókkal kapcsolatban](resource-logs-overview.md).
* [A Event Hubs első lépései](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

