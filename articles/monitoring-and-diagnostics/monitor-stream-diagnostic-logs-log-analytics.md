---
title: Adatfolyam-szolgáltatáshoz Azure diagnosztikai naplók |} Microsoft Docs
description: Útmutató az Azure diagnosztikai naplókat a Naplóelemzési munkaterület adatfolyamként történő küldéséhez.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 82011126375a3c5016e110aac9ce6bc1b2d59cdf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Adatfolyam-szolgáltatáshoz Azure diagnosztikai naplók

**[Az Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)**  továbbítható közel valós időben az Azure Naplóelemzés szolgáltatáshoz a portál, PowerShell-parancsmagok vagy Azure CLI 2.0 használatával.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>A Naplóelemzési mire képes a diagnosztikai naplók

Az Azure Naplóelemzés egy olyan rugalmas napló keresési és az elemzések eszköz, amely lehetővé teszi a nyers adatok Azure-erőforrások előállított betekintést. Bizonyos funkciók a következők:

* **Naplófájl-keresési** -írási speciális lekérdezéseket készít a napló adatairól, összefüggésbe naplók különböző forrásokból, és még akkor is létre diagramokat az irányítópulton való rögzítéséhez Azure rögzíthet.
* **Riasztási** -észlelés, ha egy vagy több esemény egy adott lekérdezésnek megfelelő, és válnak értesítést kap egy e-mailben vagy webhook hívással.
* **Megoldások** -előre elkészített nézeteket és irányítópultokat, amelyek azonnali betekintést naplót használja.
* **Sokoldalú analitikai** - gépi tanulás alkalmazni, és a naplók által feltárt lehetséges problémák azonosítása érdekében megfelelő algoritmusok mintát.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Diagnosztikai naplók szolgáltatáshoz adatfolyamként

Adatfolyamként való küldése a diagnosztikai naplók programozott módon, a portálon, vagy használatával engedélyezheti a [Azure figyelő REST API-k](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Mindkét módszer esetén létrehozhat egy diagnosztikai beállítás található, amely akkor adja meg a Naplóelemzési munkaterület és a napló kategóriák és a metrikák szeretne küldeni, hogy a munkaterületet. A diagnosztika **napló kategória** naplóban, amely egy erőforrás olyan a típusa.

A Naplóelemzési munkaterület nem kell lennie a naplók kibocsátó mindaddig, amíg a beállítás konfigurálása felhasználó hozzáfér megfelelő RBAC mindkét előfizetéshez erőforrás ugyanahhoz az előfizetéshez.

> [!NOTE]
> Diagnosztikai beállítások keresztül többdimenziós metrikák küldése jelenleg nem támogatott. Metrikák többdimenziósak, egybesimított egyetlen dimenzionális metrika, dimenzióértékek gyűjtődnek exportálja.
>
> *Például*: A "Bejövő üzenetek" metrika eseményközpontban felfedezte, és a forrásadatok egy várólista szintenként. Azonban a metrika fog megjelenni minden bejövő üzenet összes diagnosztikai beállítások keresztül exportálásakor várólisták a központ.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Az adatfolyam diagnosztikai naplókat a portálon
1. A portál Azure figyelő keresse meg és kattintson a **diagnosztikai beállítások**

    ![Figyelés szakaszban Azure-figyelő](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. Opcionálisan erőforráscsoport és erőforrások típus szerint a lista szűréséhez, majd kattintson az erőforráson, amelynek szeretné beállítani a diagnosztikai.

3. Ha a beállítások nem található az erőforrás a választott, kéri beállítás létrehozása. Kattintson a "Diagnosztika bekapcsolásához."

   ![Diagnosztikai beállítás - nincsenek meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   Ha az erőforrás-meglévő beállítások, látni fogja már ehhez az erőforráshoz konfigurált beállítások listája. A "Hozzáadás diagnosztikai beállításának."

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Adjon a beállítás a neve, és jelölje be a **küldeni a Naplóelemzési**, majd válassza ki a Naplóelemzési munkaterület.

   ![Diagnosztikai beállítás - meglévő beállítások hozzáadása](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Kattintson a **Save** (Mentés) gombra.

Néhány másodpercen belül az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és diagnosztikai naplók átvitt munkaterületet, amint létrejön az új esemény-adatokat. Vegye figyelembe, hogy mikor is ki lesz adva egy eseményt, és amikor megjelenik a Naplóelemzési között legfeljebb 15 perc is lehet.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Adatfolyamként keresztül a [Azure PowerShell-parancsmagok](insights-powershell-samples.md), használhatja a `Set-AzureRmDiagnosticSetting` parancsmag ezekkel a paraméterekkel:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Vegye figyelembe, hogy a workspaceID tulajdonság szükséges a munkaterület, nem a munkaterület azonosítója/kulcs jelenik meg a Log Analytics-portálon az Azure erőforrás-azonosítója.

### <a name="via-azure-cli-20"></a>Via Azure CLI 2.0

Adatfolyamként keresztül a [Azure CLI 2.0](insights-cli-samples.md), használhatja a [az figyelő diagnosztikai-beállítások létrehozása](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) parancsot.

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

Hozzáadhat további kategóriákra a diagnosztikai naplófájl szótárak ad hozzá a JSON-tömb, mint az átadott a `--logs` paraméter.

A `--resource-group` argumentum csak akkor kötelező, ha `--workspace` nincs egy objektumot.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Hogyan lekérdezni a Naplóelemzési lévő adatokat?

A naplófájl-keresési panelen a portálon vagy Advanced Analytics élmény Naplóelemzési részeként a napló-kezelési megoldás a AzureDiagnostics táblázat alatt részeként lekérdezheti a diagnosztikai naplókat. Is [több megoldásokat kínál az Azure-erőforrások](../log-analytics/log-analytics-add-solutions.md) webhelynaplókat azonnali a naplóadatok küldendő Log Analyticshez való telepítése.

## <a name="next-steps"></a>További lépések

* [További tudnivalók az Azure diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md)
