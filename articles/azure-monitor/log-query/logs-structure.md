---
title: Az Azure figyelőnaplók szerkezete | Microsoft dokumentumok
description: Naplólekérdezésre van szükség a naplóadatok Azure Monitorból való lekéréséhez.  Ez a cikk ismerteti, hogyan használják az új naplólekérdezéseket az Azure Monitorban, és olyan fogalmakat tartalmaz, amelyeket meg kell értenie, mielőtt létrehozna egyet.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662076"
---
# <a name="structure-of-azure-monitor-logs"></a>Az Azure Figyelő naplók struktúrája
Az Azure Monitor hatékony funkciója az adatok gyors betekintésének lehetősége [naplólekérdezéssel.](log-query-overview.md) Hatékony és hasznos lekérdezések létrehozásához érdemes megérteni néhány alapvető fogalmat, például azt, hogy hol találhatók a kívánt adatok, és hogyan épülnek fel. Ez a cikk az első lépésekhez szükséges alapfogalmakat tartalmazza.

## <a name="overview"></a>Áttekintés
Az Azure Monitor naplókban lévő adatok at egy Log Analytics-munkaterület vagy egy Application Insights-alkalmazás tárolja. Mindkettő az [Azure Data Explorer](/azure/data-explorer/) által működik, ami azt jelenti, hogy kihasználják a hatékony adatmotort és a lekérdezési nyelvet.

A munkaterületek és az alkalmazások adatai táblákba vannak rendezve, amelyek mindegyike különböző típusú adatokat tárol, és saját egyedi tulajdonságokkal rendelkezik. A legtöbb [adatforrás](../platform/data-sources.md) a saját tábláiba fog írni egy Log Analytics-munkaterületen, míg az Application Insights egy Application Insights-alkalmazás előre meghatározott táblákészletére ír. A naplólekérdezések nagyon rugalmasak, így egyszerűen kombinálhatja a több táblából származó adatokat, és akár erőforrásközi lekérdezést is használhat több munkaterületen lévő táblák adatainak kombinálásához, illetve munkaterület- és alkalmazásadatokat egyesítő lekérdezések írásához.

Az alábbi képen példák at adatforrások, amelyek írásban a különböző táblák, amelyek a minta lekérdezések.

![Táblák](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Az Azure Monitor Naplók által gyűjtött összes adatot, kivéve az Application Insights-ot, egy [Log Analytics-munkaterület tárolja.](../platform/manage-access.md) Egy vagy több munkaterületet az adott követelményektől függően hozhat létre. [Adatforrások,](../platform/data-sources.md) például a tevékenységnaplók és az erőforrás-naplók az Azure-erőforrások, ügynökök a virtuális gépeken, és az adatok elemzési és figyelési megoldások adatokat írni egy vagy több munkaterületet, amely a bevezetés részeként konfigurált. Más szolgáltatások, például [az Azure Security Center](/azure/security-center/) és az Azure [Sentinel](/azure/sentinel/) is használja a Log Analytics munkaterületet az adataik tárolására, így naplólekérdezések használatával elemezhetők, valamint más forrásokból származó adatok figyelése.

A munkaterület különböző tábláikülönböző táblákban tárolnak különböző típusú adatokat, és minden tábla egyedi tulajdonságokkal rendelkezik. A szabványos táblák létrehozásakor a munkaterületre kerülnek, és új táblákat adnak hozzá a különböző adatforrásokhoz, megoldásokhoz és szolgáltatásokhoz, ahogy bevannak rakva. Egyéni táblákat is létrehozhat az [Adatgyűjtő API használatával.](../platform/data-collector-api.md)

A munkaterület tábláiban és sémájában a **Schema** munkaterület Naplóanalytics lapján tallózhat.

![Munkaterületi séma](media/scope/workspace-schema.png)

A következő lekérdezéssel sorolja fel a munkaterület tábláit és az előző nap során az egyes rekordokba gyűjtött rekordok számát. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Az egyes adatforrások dokumentációjában az általuk létrehozott táblák részleteiről olvashat. Ilyenek például az [ügynökadatforrásokhoz](../platform/agent-data-sources.md), [az erőforrásnaplókhoz](../platform/diagnostic-logs-schema.md)és a [figyelési megoldásokhoz](../insights/solutions-inventory.md)készült cikkek .

### <a name="workspace-permissions"></a>Munkaterületi engedélyek
[Az Azure Monitor-naplók központi telepítésének tervezése](../platform/design-logs-deployment.md) című témakörben a hozzáférés-vezérlési stratégia és a munkaterületen lévő adatokhoz való hozzáférésre vonatkozó javaslatok megismerése című témakörben található. A mellett, hogy hozzáférést biztosít a munkaterülethez, korlátozhatja a hozzáférést az egyes táblákhoz a [Table Level RBAC](../platform/manage-access.md#table-level-rbac)használatával.

## <a name="application-insights-application"></a>Application Insights-alkalmazás
Amikor létrehoz egy alkalmazást az Application Insightsban, a megfelelő alkalmazás automatikusan létrejön az Azure Monitor naplók. Nincs szükség konfigurációra az adatok gyűjtéséhez, és az alkalmazás automatikusan megírja a figyelési adatokat, például az oldalnézeteket, a kéréseket és a kivételeket.

A Log Analytics-munkaterülettől eltérően az Application Insights-alkalmazások rögzített táblákkal rendelkeznek. Más adatforrások nem konfigurálhatók úgy, hogy az alkalmazásba írjanak, így nem hozhatók létre további táblák. 

| Tábla | Leírás | 
|:---|:---|
| availabilityResults | A rendelkezésre állási tesztek ből származó összefoglaló adatok. |
| browserIdőzítések      | Az ügyfél teljesítményére vonatkozó adatok, például a bejövő adatok feldolgozásához szükséges idő. |
| Customevents        | Az alkalmazás által létrehozott egyéni események. |
| customMetrics       | Az alkalmazás által létrehozott egyéni metrikák. |
| Függőségek        | Az alkalmazás ból külső összetevőkre irányuló hívások. |
| Kivételek          | Az alkalmazás futásidejű által okozott kivételek. |
| Oldalmegtekintések           | Az egyes webhelynézetek adatai a böngésző adataival. |
| performanceCounters | Az alkalmazást támogató számítási erőforrások teljesítménymérései. |
| Kérelmek            | Az egyes jelentkezési kérelmek részletei.  |
| Nyomok              | Elosztott nyomkövetés eredménye. |

Megtekintheti a séma az egyes tábla a **Séma** lapon az alkalmazás Naplóanalytics.

![Alkalmazásséma](media/scope/application-schema.png)

## <a name="standard-properties"></a>Szokásos tulajdonságok
Bár az Azure Monitor naplók minden egyes táblája saját sémával rendelkezik, az összes tábla által megosztott szabványos tulajdonságok vannak. Tekintse [meg a Standard tulajdonságok az Azure Monitor naplók](../platform/log-standard-properties.md) az egyes részletekért.

| Log Analytics-munkaterület | Application Insights-alkalmazás | Leírás |
|:---|:---|:---|
| TimeGenerated | időbélyeg  | A rekord létrehozásának dátuma és időpontja. |
| Típus          | Itemtype   | Annak a táblának a neve, amelyből a rekordot beolvasták. |
| _ResourceId   |            | Annak az erőforrásnak az egyedi azonosítója, amelyhez a rekord társítva van. |
| _IsBillable   |            | Itt adható meg, hogy a bevitt adatok számlázhatók-e. |
| _BilledSize   |            | A számlázandó adatok bájtjainak méretét adja meg. |

## <a name="next-steps"></a>További lépések
- További információ a loganalytics használatáról [a naplókeresések létrehozásához és szerkesztéséhez.](../log-query/portals.md)
- Tekintse meg az új lekérdezési nyelvet használó [lekérdezések írásával kapcsolatos oktatóanyagot.](../log-query/get-started-queries.md)
