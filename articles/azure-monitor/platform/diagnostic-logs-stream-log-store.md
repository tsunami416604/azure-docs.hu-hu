---
title: A Log Analytics az Azure diagnosztikai naplók Stream
description: Útmutató az Azure Log Analytics-munkaterület diagnosztikai naplók streamelése.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 8f4b39141a9509990525769833e2cd193419752c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469372"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>A Log Analytics az Azure diagnosztikai naplók Stream

**[Az Azure diagnosztikai naplók](diagnostic-logs-overview.md)**  továbbítható közel valós időben az Azure Log Analyticshez a portal, PowerShell-parancsmagok vagy az Azure CLI használatával.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>A Log Analytics mire képes a diagnosztikai naplók

Az Azure Log Analytics egy olyan rugalmas log keresést és elemzést eszköz, amely lehetővé teszi, hogy betekintést nyerhet az Azure-erőforrások által létrehozott nyers naplóadatokat. Bizonyos funkciók a következők:

* **Naplóbeli keresés** -írási speciális lekérdezések naplója adatait, és még akkor is, a különböző forrásokból származó összevetését naplók létrehozása diagramok, amelyek az Azure-irányítópulton is rögzítheti.
* **Riasztási** -észlelés, ha egy vagy több esemény egy adott lekérdezésnek megfelelő, és a egy e-mailben vagy webhook-hívással értesüljön róla.
* **Megoldások** – használja az előre elkészített nézeteket és irányítópultokat, amelyek azonnali betekintést nyerhet a naplózási adatokat.
* **Speciális elemzési** – machine learning a alkalmazni, és a naplók által feltárt lehetséges problémák azonosítása érdekében algoritmusokkal mintát.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>A Log Analytics diagnosztikai naplóinak streamelésének engedélyezéséhez

Streamelési diagnosztikai naplók a portálon keresztül programozás útján vagy a használatával engedélyezheti a [Azure Monitor REST API-k](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Mindkét esetben hoz létre egy diagnosztikai beállítás található, amely megadott Log Analytics-munkaterület és naplókategóriák és metrikák is szeretne küldeni munkaterületre. A diagnosztika **naplókategória** napló, amely egy erőforrás rendelkezhetnek olyan típusú.

A Log Analytics-munkaterület nem rendelkezik és a naplókat kibocsátó mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő hozzáférése RBAC mindkét előfizetéshez erőforrásnak ugyanabban az előfizetésben lennie.

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Stream diagnosztikai naplók a portál használatával
1. A portálon lépjen az Azure Monitor, és kattintson a **diagnosztikai beállítások**

    ![Az Azure Monitor figyelési szakasza](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Igény szerint szűrje a listát, erőforráscsoport vagy erőforrás típusa, majd kattintson az a erőforrás, amelynek szeretné egy diagnosztikai beállítás.

3. Ha a beállítások nem létezik az erőforráson kiválasztott, kéri létre beállítást. Kattintson a "Engedélyezze a diagnosztikát."

   ![Diagnosztikai beállítás - beállítások nélkül hozzáadása](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Ha meglévő beállításokat az erőforráson, látni fogja a beállítások már konfigurálva van a ehhez az erőforráshoz. Kattintson a "Diagnosztikai beállítás hozzáadása."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Adja meg a beállítás a neve, és jelölje be a **Küldés a Log Analyticsnek**, majd válassza ki a Log Analytics-munkaterületet.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Kattintson a **Save** (Mentés) gombra.

Néhány pillanat múlva az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és diagnosztikai naplókat a rendszer streamként továbbítja a munkaterület, amint új esemény adat keletkezik. Vegye figyelembe, hogy mikor bocsásson ki egy eseményt, és amikor megjelenik a Log Analytics között legfeljebb tizenöt perc is lehet.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Keresztül streamelésének engedélyezéséhez a [Azure PowerShell-parancsmagok](../../azure-monitor/platform/powershell-quickstart-samples.md), használhatja a `Set-AzureRmDiagnosticSetting` parancsmag ezekkel a paraméterekkel:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Vegye figyelembe, hogy a munkaterület azonosítója tulajdonság szükséges a teljes körű Azure erőforrás-azonosító, a munkaterület, nem a munkaterület azonosítója és kulcsa jelenik meg a Log Analytics-portálon.

### <a name="via-azure-cli"></a>Via Azure CLI

Keresztül streamelésének engedélyezéséhez a [Azure CLI-vel](../../azure-monitor/platform/cli-samples.md), használhatja a [az monitor diagnostic-settings létrehozása](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) parancsot.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Adhat hozzá további kategóriát a diagnosztikai napló szótárak ad hozzá a JSON-tömböt adhatók be a `--logs` paraméter.

A `--resource-group` argumentum csak akkor kötelező, ha `--workspace` nem egy objektumot.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Hogyan kérdezhet le a az adatokat a Log Analytics?

A naplók keresése panel a portálon vagy a Log Analytics részét képező Advanced Analytics élmény, a diagnosztikai naplók lekérdezheti a AzureDiagnostics táblában a naplókezelési megoldás részeként. Vannak még [számos olyan megoldást az Azure-erőforrások](../../azure-monitor/insights/solutions.md) azonnali betekintést a naplóadatok a Log analyticsbe küld be is telepítheti.

## <a name="next-steps"></a>További lépések

* [További információk az Azure Diagnostics-naplókról](diagnostic-logs-overview.md)

