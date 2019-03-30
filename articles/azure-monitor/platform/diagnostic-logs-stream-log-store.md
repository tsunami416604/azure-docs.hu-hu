---
title: Stream Azure diagnosztikai naplók a Log Analytics-munkaterületet az Azure monitorban
description: Útmutató az Azure monitorban Log Analytics-munkaterületet az Azure diagnosztikai naplók streamelése.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 33d8f2e7c65a786d1ecb389574fe186efb6fb705
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630789"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Stream Azure diagnosztikai naplók a Log Analytics-munkaterületet az Azure monitorban

**[Az Azure diagnosztikai naplók](diagnostic-logs-overview.md)**  továbbítható egy Log Analytics-munkaterületen a portal, PowerShell-parancsmagok vagy az Azure CLI használatával az Azure Monitor közel valós időben.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>A Log Analytics-munkaterületen mire képes a diagnosztikai naplók

Az Azure Monitor egy rugalmas napló lekérdezési és elemzési eszköz, amely lehetővé teszi, hogy betekintést nyerhet az Azure-erőforrások által létrehozott nyers naplóadat adatokat biztosít. Bizonyos funkciók a következők:

* **Naplólekérdezés** -írási összetett lekérdezéseket, a naplózási adatokat, a különböző összevetését naplók adatforrásokat, és hozzon létre több mint diagram, amely az Azure-irányítópulton is rögzítheti.
* **Riasztási** -észlelés, ha egy vagy több esemény egy adott lekérdezésnek megfelelő, és a egy e-mailben vagy webhook-hívással használatával az Azure Monitor riasztások értesüljön róla.
* **Speciális elemzési** – machine learning a alkalmazni, és a naplók által feltárt lehetséges problémák azonosítása érdekében algoritmusokkal mintát.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>A Log Analytics-munkaterületet a diagnosztikai naplók streamelésének engedélyezéséhez

Streamelési diagnosztikai naplók a portálon keresztül programozás útján vagy a használatával engedélyezheti a [Azure Monitor REST API-k](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Mindkét esetben hoz létre egy diagnosztikai beállítás található, amely megadott Log Analytics-munkaterület és naplókategóriák és metrikák is szeretne küldeni munkaterületre. A diagnosztika **naplókategória** napló, amely egy erőforrás rendelkezhetnek olyan típusú.

A Log Analytics-munkaterület nem rendelkezik és a naplókat kibocsátó mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő hozzáférése RBAC mindkét előfizetéshez erőforrásnak ugyanabban az előfizetésben lennie.

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Stream diagnosztikai naplók a portál használatával
1. A portálon lépjen az Azure Monitor, és kattintson a **diagnosztikai beállítások** a a **beállítások** menü.


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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Keresztül streamelésének engedélyezéséhez a [Azure PowerShell-parancsmagok](../../azure-monitor/platform/powershell-quickstart-samples.md), használhatja a `Set-AzDiagnosticSetting` parancsmag ezekkel a paraméterekkel:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
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

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Hogyan kérdezhet le a az adatokat a Log Analytics-munkaterület?

A naplók panel az Azure Monitor-portálon, a diagnosztikai naplók lekérdezheti a AzureDiagnostics táblában a naplókezelési megoldás részeként. Vannak még [több figyelési megoldások az Azure-erőforrások](../../azure-monitor/insights/solutions.md) telepítheti a azonnali információkhoz juthat a naplózási adatokat küld az Azure Monitor szolgáltatásba.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Ismert korlátozás: AzureDiagnostics oszlop-korlát
Mert sok erőforrás küldése adattípusok összes küldött ugyanabban a táblában (_AzureDiagnostics_), ez a tábla sémája az összes különböző adattípusok összegyűjtött sémái felettes készletét. Például ha már létrehozta a gyűjteményhez a következő adattípusú diagnosztikai beállítások, az összes küld ugyanazon a munkaterületen:
- Auditnaplók (oszlopok A, B és C álló sémával rendelkező) erőforráshoz 1  
- (D, E és az F oszlopot álló sémával rendelkező) erőforráshoz 2 hibanaplókat  
- Folyamatnaplók adatainak (oszlopok G, H, és szeretnék sémával rendelkező) erőforráshoz 3  
 
A AzureDiagnostics tábla néhány adatot az alábbi módon fog kinézni:  
 
| ResourceProvider | Kategória | A | B | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | Naplók | x1 | y1 | z1 |
| Microsoft.Resource2 | Hibanaplókat | | | | q1 | w1 | E1 csomag |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | Hibanaplókat | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | Naplók | x5 | y5 | z5 |
| ... |
 
Csak egy explicit bármely adott Azure Log tábla nem rendelkezik 500-nál több oszlopot. Elérése után már az első 500 kívül bármely másik oszlopot az adatokat tartalmazó sorokat dobja el a betöltési idő. A AzureDiagnostics táblában, különösen ki vannak téve lehet hatással a ezt a korlátot. Ez általában történik, vagy mert az adatforrások széles küldött ugyanazon a munkaterületen, vagy több nagyon részletes adatforrások küld a ugyanazon a munkaterületen. 
 
#### <a name="azure-data-factory"></a>Azure Data Factory  
Az Azure Data Factory, naplók, nagyon részletes készlete miatt egy különösen hatással lehet ez a korlátozás számára ismert erőforrást. Ebben az esetben:  
- *Semmilyen tevékenységet a folyamat képest van meghatározva. a felhasználó paraméterek*: minden felhasználó egyedi nevű paraméter tevékenység elleni létrehozott egy olyan új oszlop lesz. 
- *Tevékenység bemeneti és kimeneti*: ezek eltérőek lehetnek a tevékenység-tevékenység, és hozzon létre egy nagy mennyiségű oszlopok részletes jellege miatt. 
 
Mint a szélesebb körű megkerülő megoldás, az alábbi javaslatokat javasoljuk, hogy az ADF-naplók elkülönítése az esélye, hogy ezek a naplók a munkaterületeken összegyűjtött napló másféle érintő minimalizálása érdekében a saját munkaterületre. Várhatóan rendelkezik összeválogatott naplók elérhető az Azure Data Factory által mid április 2019.
 
#### <a name="workarounds"></a>Megkerülő megoldások
Rövid távú, amíg az 500-as oszlopos korlátot újra meg kell határozni, javasoljuk, hogy részletes adattípusok szét az esélye, szerezze meg a korlátot, különálló munkaterületnek.
 
Hosszabb távú, az Azure Diagnostics fog kell megéri forrásadatok egy egységes, ritka séma egyes adatok típusonként; egyedi táblák dinamikus típusok támogatása párosítva ez jelentős mértékben javítja az adatok Azure-naplók az Azure Diagnostics mechanizmussal érkező használhatósága. Már láthatja ezt válassza ki az Azure-erőforrástípus, például [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) vagy [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) naplókat. Keresse meg az Azure-t támogató ezeket a válogatott naplókat az új erőforrástípusok kapcsolatos híreket az [Azure frissítések](https://azure.microsoft.com/updates/) blogon!


## <a name="next-steps"></a>További lépések

* [További információk az Azure Diagnostics-naplókról](diagnostic-logs-overview.md)

