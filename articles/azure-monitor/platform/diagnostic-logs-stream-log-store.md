---
title: Stream Azure diagnosztikai naplók a Log Analytics-munkaterületet az Azure monitorban
description: Útmutató az Azure monitorban Log Analytics-munkaterületet az Azure diagnosztikai naplók streamelése.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 13eb1a8fcea2f74cda5921a51b8c2e8816be975f
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303710"
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

Néhány pillanat múlva az új beállítás jelenik meg az ehhez az erőforráshoz beállítások listáját, és diagnosztikai naplókat a rendszer streamként továbbítja a munkaterület, amint új esemény adat keletkezik. Előfordulhat, hogy akár 15 percet mikor bocsásson ki egy eseményt, és amikor megjelenik a Log Analytics között.

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

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure Diagnostics és az erőforrás-specifikus  
Az Azure Diagnostics konfigurálása a Log Analytics cél engedélyezését követően vannak, amely adatokat fog megjelenni a munkaterület két különböző módon:  
- **Az Azure Diagnostics** – Ez a ma az Azure-szolgáltatások többsége által használt örökölt mód. Ebben a módban az adatokat a diagnosztikai beállítások mutatott egy adott munkaterület összes lesz végül a a _AzureDiagnostics_ tábla. 
<br><br>Mert sok erőforrás adatokat küldeni a ugyanabban a táblában (_AzureDiagnostics_), ez a tábla sémája az összes különböző adattípusok összegyűjtött sémái felettes készletét. Például ha a gyűjteményhez a következő adattípusú létrehozott diagnosztikai beállítások, az összes küld ugyanazon a munkaterületen:
    - Auditnaplók (oszlopok A, B és C álló sémával rendelkező) erőforráshoz 1  
    - (D, E és az F oszlopot álló sémával rendelkező) erőforráshoz 2 hibanaplókat  
    - Folyamatnaplók adatainak (oszlopok G, H, és szeretnék sémával rendelkező) erőforráshoz 3  

    A AzureDiagnostics tábla néhány adatot az alábbi módon fog kinézni:  

    | ResourceProvider | Category | A | B | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | Adatmodelltáblához | x1 | y1 | z1 |
    | Microsoft.Resource2 | Hibanaplókat | | | | q1 | w1 | E1 csomag |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | Hibanaplókat | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | Adatmodelltáblához | x5 | y5 | z5 |
    | ... |

- **Erőforrás-specifikus** – ebben a módban az egyes táblák a kiválasztott munkaterületen hozhatók létre minden egyes a diagnosztikai beállítások konfigurációjának a kiválasztott kategória szerinti. Ez újabb a módszer sokkal könnyebben megtalálhatja a pontosan meg szeretne keresni a kockázatok egyértelmű elkülönítése keresztül teszi: egy tábla minden kategóriához. Ezenkívül dinamikus típusok biztosítja a támogatási előnye van. Ebben a módban, válassza ki az Azure-erőforrástípus, már látható példa [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) vagy [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) naplókat. Végső soron várhatóan át az erőforrás-specifikus mód minden adattípus. 

    A fenti példában létrehozott három táblát Ez azt eredményezi: 
    - Tábla _Adatmodelltáblához_ módon:

        | ResourceProvider | Category | A | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | Adatmodelltáblához | x1 | y1 | z1 |
        | Microsoft.Resource1 | Adatmodelltáblához | x5 | y5 | z5 |
        | ... |

    - Tábla _hibanaplókat_ módon:  

        | ResourceProvider | Category | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | Hibanaplókat | q1 | w1 | E1 csomag |
        | Microsoft.Resource2 | Hibanaplókat | q2 | w2 | e2 |
        | ... |

    - Tábla _DataFlowLogs_ módon:  

        | ResourceProvider | Category | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Egyéb előnyei az erőforrás-specifikus mód a jobb teljesítmény érdekében Adatbetöltési késés és a gyorsaság, könnyebben sémák és azok szerkezetét, valamint a RBAC jogosultságokat, egy adott táblához, és több, különböző közé tartozik.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Azure diagnosztikai erőforrás-specifikus vs mód kiválasztása
A legtöbb Azure-erőforrások nem fog tudni a választott-e az Azure diagnosztikai vagy erőforrás-specifikus módban; az adatok automatikusan átkerülnek a módszerrel, amely az erőforrás használatára van kiválasztva. Tekintse meg az adatokat küldeni a Log Analytics részletes, amelyen mód alkalmazott engedélyezését az erőforrás által biztosított dokumentációt. 

Az előző szakaszban leírtaknak végső soron a cél az Azure Monitor, az erőforrás-specifikus móddal rendelkeznek az összes szolgáltatás az Azure használatát. Az átállás elősegítése, és győződjön meg arról, hogy az adatok nem részeként elveszett, azt, bizonyos Azure-szolgáltatások, ha a Log Analytics előkészítési biztosít Önnek a kijelölési mód:  
   ![Diagnosztikai beállítások mód-választó](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Hogy **erősen** javasoljuk, hogy le potenciálisan nehéz áttelepítések elkerülése érdekében minden újonnan létrehozott diagnosztikai beállítások használata az erőforrás-központú mód.  

A meglévő diagnosztikai beállítások, egy adott Azure-erőforrás, amelyet engedélyezés után lesz visszamenőlegesen átállás az Azure diagnosztikai erőforrás-specifikus módra. A korábban betöltött adatok továbbra is elérhető legyen a _AzureDiagnostics_ mindaddig, amíg azt ki, a megőrzési beállítást a munkaterületen konfigurált életkorok, de az új adatokat küld a dedikált tábla tábla. Ez azt jelenti, hogy bármely-lekérdezéseket is a régi adatok span kell és az új (mindaddig, amíg a régi adatok teljesen életkorok), amely egy [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) operátor szerepel a lekérdezések egyesítése a két adatkészletet kell adni.

Kérjük, olvassa el új Azure-ral kapcsolatos híreket az erőforrás-specifikus módban támogató naplók szolgáltatási a a [Azure frissítések](https://azure.microsoft.com/updates/) blogon!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Ismert korlátozás: AzureDiagnostics oszlop-korlát
Csak egy explicit bármely adott Azure Log tábla nem rendelkezik 500-nál több oszlopot. Elérése után már az első 500 kívül bármely másik oszlopot az adatokat tartalmazó sorokat dobja el a betöltési idő. A AzureDiagnostics táblában, különösen ki vannak téve lehet hatással a ezt a korlátot. Ez általában történik, vagy mert az adatforrások széles küldött ugyanazon a munkaterületen, vagy több részletes adatforrások küld a ugyanazon a munkaterületen. 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Az Azure Data Factory, naplók, nagyon részletes készlete miatt egy különösen hatással lehet ez a korlátozás számára ismert erőforrást. Különösen bármely diagnosztikai beállítások konfigurálása előtt az erőforrás-specifikus mód nem engedélyezett, vagy explicit módon választhatja fordított-kompatibilitási okokból ennek az erőforrás-specifikus mód használatára:  
- *Semmilyen tevékenységet a folyamat képest van meghatározva. a felhasználó paraméterek*: minden felhasználó egyedi nevű paraméter tevékenység elleni létrehozott egy olyan új oszlop lesz. 
- *Tevékenység bemeneti és kimeneti*: ezek eltérőek lehetnek a tevékenység-tevékenység, és hozzon létre egy nagy számú oszlopot részletes jellege miatt. 
 
Mint a szélesebb körű megoldás az alábbi, javaslatokat az javasoljuk, hogy át a naplókat, minél hamarabb az erőforrás-specifikus mód használatára. Ha nem azonnal ehhez, közbenső alternatív az ADF-naplók elkülönítése az esélye, hogy ezek a naplók a munkaterületeken összegyűjtött napló másféle érintő minimalizálása érdekében a saját munkaterületre. 
 
#### <a name="workarounds"></a>Megkerülő megoldások
Rövid távon, amíg az összes Azure-szolgáltatások engedélyezve vannak az erőforrás-specifikus mód, a függő szolgáltatások nem, de az erőforrás-specifikus mód támogatása esetén javasoljuk, hogy külön részletes adattípusok külön munkaterületre, ezek a szolgáltatások által közzétett csökkentése érdekében a Szerezze meg a korlát lehetőségét.  
 
Hosszabb távon, az Azure Diagnostics lesz az erőforrás-specifikus módot támogató összes Azure-szolgáltatás felé történő áthelyezésének. Javasoljuk, hogy az áthelyezés erre az üzemmódra amint lehetséges, hogy az esetleges, az befolyásolja-e 500 oszlop korlátozások.  


## <a name="next-steps"></a>További lépések

* [További információk az Azure Diagnostics-naplókról](diagnostic-logs-overview.md)

