---
title: Stream Azure diagnosztikai naplók egy eseményközpontba
description: Ismerje meg, hogyan streamelése az Azure diagnosztikai naplók egy eseményközpontba.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: d178041a420c49480c6043869f139eb3a09c91fd
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959273"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Stream Azure diagnosztikai naplók egy eseményközpontba
**[Az Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)**  továbbítható bármely alkalmazás a beépített "Exportálás az Event Hubs" lehetőség használatával, a portálon, vagy az Event Hub engedélyezési szabály azonosítója a diagnosztikai beállítást az Azure-on keresztül engedélyezésével közel valós időben PowerShell-parancsmagok vagy az Azure parancssori felület.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Mire képes a diagnosztikai naplók és az Event Hubs
Az alábbiakban néhány olyan módon, a streamelési funkciót használhat a diagnosztikai naplókhoz:

* **Stream naplók 3. fél naplózás és a telemetriai rendszer** – összes a diagnosztikai naplók a függőleges vonal tranzakciónaplójának adatai egy külső SIEM- vagy a log analytics eszközzel ugyanabba az eseményközpontba streamelheti.
* **A service health megtekintéséhez streamelési "gyakori elérésű útvonal" adatok a Power bi-bA** – Event Hubs használatával, a Stream Analytics és a Power BI, egyszerűen alakíthatja át a diagnosztikai adatok közel valós idejű elemzés az Azure-szolgáltatásokhoz való. [A dokumentációs cikk állítsa be az Event Hubs, a Stream Analytics használatával adatokat feldolgozni és kimenetként használja a Power BI remek áttekintést nyújt](../stream-analytics/stream-analytics-power-bi-dashboard.md). Íme néhány tipp az első állítsa be a diagnosztikai naplók segítségével:

  * Egy eseményközpontba kategóriájára vonatkozó diagnosztikai naplók automatikusan jön létre, ha a beállítás a portálon vagy engedélyezése a PowerShell, ezért az event hubs kiválasztása a névtér nevét kezdetű **insights –**.
  * A következő SQL-kódot egy Stream Analytics-mintalekérdezés elemezni a naplóban lévő összes adat egy Power BI-táblához, amelyekkel:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Hozhat létre egy egyéni telemetriát és a naplózás platform** – Ha már rendelkezik egy személyre szabott telemetriai platform vagy csak mértékegységeként létrehozására, rugalmasan méretezhető közzétételi és előfizetési jellegű az Event hubs lehetővé teszi, hogy rugalmasan diagnosztikai naplókat. [Az Event Hubs használatával itt globális méretezhetőség telemetriai platform Dan Rosanova útmutatójában](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Diagnosztikai naplók streamelésének engedélyezéséhez

Streamelési diagnosztikai naplók a portálon keresztül programozás útján vagy a használatával engedélyezheti a [Azure Monitor REST API-k](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Mindkét esetben hoz létre egy diagnosztikai beállítás található, amely megadott Event Hubs-névtér és naplókategóriák és metrikák is szeretne küldeni névtérhez. Egy eseményközpont engedélyezi a napló kategóriákhoz tartozó névtér jön létre. A diagnosztika **naplókategória** napló, amely egy erőforrás gyűjthet olyan típusú.

> [!WARNING]
> Engedélyezése és számítási erőforrások (például a virtuális gépek vagy a Service Fabric) diagnosztikai naplóinak streamelési [szükséges lépések külön készletét](azure-diagnostics-streaming-event-hubs.md).

Az Event Hubs névterének nem muszáj ugyanabban az előfizetésben a naplókat kibocsátó mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő RBAC hozzáféréssel rendelkezik mind az előfizetések, és mindkét előfizetés erőforrásként kell az azonos AAD-bérlő részét képezik.

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Stream diagnosztikai naplók a portál használatával

1. A portálon lépjen az Azure Monitor, és kattintson a **diagnosztikai beállítások**

    ![Az Azure Monitor figyelési szakasza](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Igény szerint szűrje a listát, erőforráscsoport vagy erőforrás típusa, majd kattintson az a erőforrás, amelynek szeretné egy diagnosztikai beállítás.

3. Ha a beállítások nem létezik az erőforráson kiválasztott, kéri létre beállítást. Kattintson a "Engedélyezze a diagnosztikát."

   ![Diagnosztikai beállítás - beállítások nélkül hozzáadása](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Ha meglévő beállításokat az erőforráson, látni fogja a beállítások már konfigurálva van a ehhez az erőforráshoz. Kattintson a "Diagnosztikai beállítás hozzáadása."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Adja meg a beállítás a neve, és jelölje be a **egy eseményközpontba Stream**, majd válassza ki az Event Hubs-névtér.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

   A kiválasztott névtér lesz, ahol az eseményközpont létrehozásakor a (Ha ez az első alkalommal streamelési diagnosztikai naplók) vagy adatfolyamként történő (ha vannak még erőforrások, amelyek ehhez a névtérhez, hogy naplókategória vannak streaming), és a szabályzat határozza meg az engedélyeket, amelyek a streamelési mechanizmussal rendelkezik. Jelenleg egy eseményközpontba streamelési kezelése, küldési és figyelési engedélyek szükségesek. Hozzon létre, vagy módosítsa az Event Hubs névtér megosztott hozzáférési házirendek a konfigurálás lapot a portál névteréhez. Ezek a diagnosztikai beállítások frissítéséhez az ügyfél a ListKey engedéllyel kell rendelkeznie az Event Hubs engedélyezési szabályt. Opcionálisan megadhatja egy eseményközpont neve. Ha megad egy eseményközpont neve, naplókat a rendszer ennek az eseményközpontnak helyett egy újonnan létrehozott eseményközpont log kategória szerinti irányítja.

4. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat múlva az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és diagnosztikai naplókat a rendszer streamként továbbítja ennek az eseményközpontnak, amint új esemény adat keletkezik.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával

Keresztül streamelésének engedélyezéséhez a [Azure PowerShell-parancsmagok](insights-powershell-samples.md), használhatja a `Set-AzureRmDiagnosticSetting` parancsmag ezekkel a paraméterekkel:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

Az Event Hub engedélyezési szabály azonosítója egy karakterláncérték, ebben a formátumban: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, például `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Jelenleg nem választhat egy adott eseményközpont neve a PowerShell használatával.

### <a name="via-azure-cli"></a>Az Azure CLI-n keresztül

Keresztül streamelésének engedélyezéséhez a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), használhatja a [az monitor diagnostic-settings létrehozása](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) parancsot.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Adhat hozzá további kategóriát a diagnosztikai napló szótárak ad hozzá a JSON-tömböt adhatók be a `--logs` paraméter.

A `--event-hub-rule` argumentum az Event Hub engedélyezési szabály ID ugyanazt a formátumot használja a mértékét a PowerShell-parancsmag.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hogyan felhasználása a a naplóadatokat, az Event Hubs?

A következő minta kimeneti adatokat az Event Hubsról:

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

| Elem neve | Leírás |
| --- | --- |
| rekordok |Az adattartalomban szereplő összes alkalmazásnapló-események tömbje. |
| time |Idő az esemény történt. |
| category |Naplókategória ezt az eseményt. |
| resourceId |Erőforrás-azonosító az erőforrás által generált ezt az eseményt. |
| operationName |A művelet neve. |
| szint |Választható. Azt jelzi, hogy a naplózási események szint. |
| properties |Az esemény tulajdonságai. |

Megtekintheti az összes erőforrás-szolgáltató, amely támogatja a streamelés az Event Hubs listáját [Itt](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>A számítási erőforrások Stream adatait

A Windows Azure Diagnostics-ügynök használatával, a számítási erőforrások diagnosztikai naplóinak streamelheti is. [Ebben a cikkben](azure-diagnostics-streaming-event-hubs.md) , hogy hogyan állította be.

## <a name="next-steps"></a>További lépések

* [Az Azure Monitor szolgáltatással Stream Azure Active Directory-naplók](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [További információk az Azure Diagnostics-naplókról](monitoring-overview-of-diagnostic-logs.md)
* [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
