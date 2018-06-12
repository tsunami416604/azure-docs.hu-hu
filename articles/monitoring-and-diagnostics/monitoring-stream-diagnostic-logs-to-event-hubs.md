---
title: Az adatfolyam Azure diagnosztikai naplók az eseményközpontba
description: Útmutató az Azure diagnosztikai naplók az eseményközpontba adatfolyamként történő küldéséhez.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 18b385edc7efed20d940be96670b8c40ce66ddfd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264323"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Az adatfolyam Azure diagnosztikai naplók az eseményközpontba
**[Az Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)**  továbbítható közel valós idejű bármely alkalmazás a beépített "Exportálás az Event Hubs" beállítás használatával, a portálon, vagy a központi engedélyezési szabály eseményazonosító keresztül az Azure diagnosztikai beállítás engedélyezésével PowerShell-parancsmagok vagy Azure CLI 2.0.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Mi mindent diagnosztikai naplók és az Event Hubs
Az alábbiakban néhány módszereket, akkor előfordulhat, hogy az adatfolyam-továbbítási képesség a diagnosztikai naplók:

* **Adatfolyam-bejegyzéseit, amelyek a 3. fél naplózása és telemetriai rendszerek** – is adatfolyam formájában a diagnosztikai naplók cső napló az adatokat egy külső SIEM- vagy naplófájl elemzőeszköz egy eseményközpontba.
* **Szolgáltatás állapotának megtekintéséhez a Power bi szolgáltatásba "forró path" adatok folyamatos** – az Event Hubs használatával, a Stream Analytics és a Power BI, könnyen átalakíthatja a diagnosztikai adatokat a valós idejű elemzése az Azure-szolgáltatások a közelében. [A dokumentációs cikket tartalmaz az Event Hubs beállítása, a Stream Analytics adatok feldolgozása és kimenetként használata a Power bi kiváló áttekintést nyújt](../stream-analytics/stream-analytics-power-bi-dashboard.md). Az alábbiakban néhány tippek a diagnosztikai naplók első beállítása:

  * Az eseményközpontok a diagnosztikai naplók kategóriájú automatikusan jön létre, jelölje be a beállítást, a portálon, vagy engedélyezze a PowerShell segítségével, így szeretné kiválasztani az event hubs kezdetű névvel névtér **insights -**.
  * A következő SQL-kódot a Stream Analytics mintalekérdezést használó összes a napló az adatok a Power bi táblára:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Létre egy egyéni telemetriai adatokat és a naplózás platform** – Ha már rendelkezik egy egyedi telemetriai platform vagy csak egy kiválóan méretezhető kiépítésével foglalkozó végezni azok jellegű közzétételi-feliratkozási az Event hubs lehetővé teszi a rugalmas tölti be a diagnosztikai naplókat. [Az Event Hubs használatával egy globális méretű telemetriai platform itt Dan Rosanova útmutatójában](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Adatfolyamként való küldése a diagnosztikai naplók engedélyezése

Adatfolyamként való küldése a diagnosztikai naplók programozott módon, a portálon, vagy használatával engedélyezheti a [Azure figyelő REST API-k](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Mindkét módszer esetén létrehozhat egy diagnosztikai beállítás található, amely akkor adja meg az Event Hubs-névteret és a napló kategóriák és a metrikák szeretne küldeni névtérhez. Az eseményközpontok engedélyezi napló kategóriákhoz tartozó névtér jön létre. A diagnosztika **napló kategória** a naplóban, amely egy erőforrás gyűjthet típusa.

> [!WARNING]
> Engedélyezése, valamint a folyamatos átviteli a számítási erőforrások (például a virtuális gépek vagy a Service Fabric) diagnosztikai naplók [meg kell adni egy másik lépések](../event-hubs/event-hubs-streaming-azure-diags-data.md).

Az Event Hubs névtér nem kell lennie a naplók kibocsátó mindaddig, amíg a beállítás konfigurálása felhasználó hozzáfér megfelelő RBAC mindkét előfizetéshez erőforrás ugyanahhoz az előfizetéshez.

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Az adatfolyam diagnosztikai naplókat a portálon

1. A portál Azure figyelő keresse meg és kattintson a **diagnosztikai beállítások**

    ![Figyelés szakaszban Azure-figyelő](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Opcionálisan erőforráscsoport és erőforrások típus szerint a lista szűréséhez, majd kattintson az erőforráson, amelynek szeretné beállítani a diagnosztikai.

3. Ha a beállítások nem található az erőforrás a választott, kéri beállítás létrehozása. Kattintson a "Diagnosztika bekapcsolásához."

   ![Diagnosztikai beállítás - nincsenek meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Ha az erőforrás-meglévő beállítások, látni fogja már ehhez az erőforráshoz konfigurált beállítások listája. A "Hozzáadás diagnosztikai beállításának."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Adjon a beállítás a neve, és jelölje be a **adatfolyam egy eseményközpontba**, majd válassza ki az Event Hubs névteret.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

   A kijelölt névtér lesz az eseményközpont létre (Ha ez az első alkalommal adatfolyam diagnosztikai naplók) vagy a folyamatos átviteli rendelkező (ha van már erőforrásokat, amelyek ehhez a névtérhez napló kategória vannak streaming), és a házirend határozza meg az adatfolyam-továbbítási mechanizmus jogosultságai. Napjainkban az eseményközpontba streaming engedélyekkel kell rendelkeznie kezelése, a Küldés és a figyelés. Hozzon létre, vagy az Event Hubs névtér megosztott hozzáférési házirendek a portálon az konfigurálása lapon módosítsa a névtér. Ezek a diagnosztikai beállítások frissítéséhez az ügyfél a ListKey engedéllyel kell rendelkeznie az Event Hubs engedélyezési szabályt. Opcionálisan megadhat egy eseményközpont neveként. Ha megad egy eseményközpont neveként, naplók adott eseményközpont helyett egy újonnan létrehozott eseményközpont napló kategóriánként legyenek átirányítva.

4. Kattintson a **Save** (Mentés) gombra.

Néhány másodpercen belül az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és diagnosztikai naplók részére, hogy az event hubs, amint létrejön az új esemény-adatokat.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával

Adatfolyamként keresztül a [Azure PowerShell-parancsmagok](insights-powershell-samples.md), használhatja a `Set-AzureRmDiagnosticSetting` parancsmag ezekkel a paraméterekkel:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

A központi engedélyezési szabály Eseményazonosító: a formátumú karakterlánc: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, például `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Jelenleg nem választhat egy adott eseményközpont neveként a PowerShell használatával.

### <a name="via-azure-cli-20"></a>Via Azure CLI 2.0

Adatfolyamként keresztül a [Azure CLI 2.0](insights-cli-samples.md), használhatja a [az figyelő diagnosztikai-beállítások létrehozása](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) parancsot.

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

Hozzáadhat további kategóriákra a diagnosztikai naplófájl szótárak ad hozzá a JSON-tömb, mint az átadott a `--logs` paraméter.

A `--event-hub-rule` argumentum, a központi engedélyezési szabály eseményazonosító ugyanazt a formátumot használja a csatornára. További információ a PowerShell-parancsmag.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hogyan Eseményközpontokból származó napló adatokat felhasználó?

Minta kimenet Eseményközpontokból származó adatokat a következő:

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
| rekordok |Ezek a hasznos adatok az összes naplóeseményeket tömbjét. |
| time |Az időt az esemény történt. |
| category |Ez az esemény napló kategóriát. |
| resourceId |Az erőforrás ezt az eseményt létrehozó erőforrás-azonosító. |
| operationName |A művelet neve. |
| szint |Választható. A naplózási esemény szintjét jelzi. |
| properties |Az esemény tulajdonságai. |

Megtekintheti az összes erőforrás-szolgáltató, amely támogatja az Event hubs streaming listáját [Itt](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Az adatfolyam adatok a számítási erőforrásokat

Adatfolyam formájában a diagnosztikai naplókat a számítási erőforrásokat, a Windows Azure diagnosztikai ügynök használatával is. [Ebben a cikkben találhat](../event-hubs/event-hubs-streaming-azure-diags-data.md) beállítása, amely a.

## <a name="next-steps"></a>További lépések

* [További tudnivalók az Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
* [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
