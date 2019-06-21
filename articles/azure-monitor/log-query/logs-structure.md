---
title: Azure Monitor naplóira szerkezete |} A Microsoft Docs
description: Naplóadatok lekérése az Azure Monitor log lekérdezés van szüksége.  Ez a cikk azt ismerteti, hogyan új naplózási lekérdezést használ az Azure monitorban, és biztosítja a fogalmakat, amelyek egy létrehozása előtt ismernie kell.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297299"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor naplóira szerkezete
Gyors betekintést nyerhet az adatokat az lehetővé teszi egy [naplólekérdezés](log-query-overview.md) egy hatékony szolgáltatás az Azure monitor. Hatékony és hasznos lekérdezések létrehozása, tisztában kell lennie bizonyos alapvető fogalmakkal, például hol helyezkedik el a kívánt adatokat, és hogyan épül. Ez a cikk ismerteti az alapfogalmakat, szükséges a kezdéshez.

## <a name="overview"></a>Áttekintés
Log Analytics-munkaterület vagy egy Application Insights alkalmazásban tárolt adatok az Azure Monitor naplóira. Mindkét működteti [Azure adatkezelő](/azure/data-explorer/) ami azt jelenti, hogy kihasználja a hatékony motor és a lekérdezési nyelv.

Adatok munkaterületek és alkalmazások is vannak rendezve, táblákba, amelyek mindegyike különböző típusú adatokat tárolja, és a saját egyedi tulajdonságkészlettel rendelkezik. A legtöbb [adatforrások](../platform/data-sources.md) Log Analytics-munkaterületet, a saját táblájában fog írni, míg az Application Insights egy Application Insights alkalmazásban táblák előre meghatározott fog írni. Napló lekérdezések rendkívül rugalmas, így könnyedén egyesítheti az adatokat több táblából, és akár az erőforrások közötti lekérdezés, úgy, hogy több munkaterület a táblák adatait vagy a munkaterület és application adatok összevonása forrásonként lekérdezések írására.

Az alábbi képen látható példák az adatforrásokat, amelyek különböző minta lekérdezésekben használt táblák írni.

![Táblák](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Az Azure Monitor naplóira, kivéve az Application Insights által gyűjtött összes adat tárolva van egy [Log Analytics-munkaterület](../platform/manage-access.md). Adott követelményektől függően egy vagy több munkaterülethez is létrehozhat. [Adatforrások](../platform/data-sources.md) tevékenységeket tartalmazó naplók és a diagnosztikai naplók az Azure-erőforrások, például ügynökök, a virtual machines és az elemzések és figyelési megoldások adatait fog adatokat írni egy vagy több munkaterületnek küldjenek a bevezetés részeként konfigurált. Más szolgáltatásokkal, mint például [az Azure Security Center](/azure/security-center/) és [Azure Sentinel-](/azure/sentinel/) is a Log Analytics-munkaterület használatával tárolja az adatokat, így azok elemezhetők együtt a monitorozási adatok, a többi napló lekérdezésekkel források.

Különböző típusú adatok vannak tárolva a munkaterületen található táblákat, és minden táblához tartozik egy egyedi tulajdonságokat. Táblák szabványos készletét kerülnek egy munkaterület létrehozása, és új táblát adnak hozzá a különböző adatforrások, megoldásokat és szolgáltatásokat, ezek előkészítve. Egyéni táblák használatával is létrehozhat a [adatgyűjtő API](../platform/data-collector-api.md).

Megnyithatja a táblák egy munkaterületet, és a sémát a **séma** a munkaterületet a Log Analytics lapján.

![Munkaterület-séma](media/scope/workspace-schema.png)

Használja a következő lekérdezést a minden egyes az előző nap során gyűjtött a táblák a munkaterületet, és a rekordok száma a listán. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Minden adatforrás az általuk létrehozott táblák részleteit dokumentációjában talál. Ilyenek például a cikkek [ügynök adatforrások](../platform/agent-data-sources.md), [diagnosztikai naplók](../platform/diagnostic-logs-schema.md), és [figyelési megoldások](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>A munkaterület-engedélyek
Lásd: [a munkaterület-engedélyek és hatókör](../platform/manage-access.md#workspace-permissions-and-scope) kapcsolatos részleteket az adatok a munkaterületen való hozzáférés biztosítása. Mellett magát a munkaterületet, a hozzáférés biztosításával az egyes táblák használatával hozzáférést korlátozhatja [tábla szint RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights-alkalmazás
Az Application Insights alkalmazást hoz létre, amikor egy alkalmazást az Azure Monitor naplóira automatikusan létrejön. Nincs szükség konfigurálásra, adatok gyűjtésére, és az alkalmazás automatikusan fog kiírni, adatok, például az oldalmegtekintéseket, a kérések és kivételek figyelése.

Ellentétben a Log Analytics-munkaterülettel egy Application Insights-alkalmazás rendelkezik egy rögzített táblák. Más adatforrások írni az alkalmazáshoz, így további táblákon nem hozható létre nem lehet konfigurálni. 

| Tábla | Leírás | 
|:---|:---|
| availabilityResults | Rendelkezésre állási tesztek összefoglaló adatait. |
| browserTimings      | Ügyfél teljesítményét, például a bejövő adatok feldolgozásához szükséges idő adatait. |
| customEvents        | Az alkalmazás által létrehozott egyéni eseményeket. |
| customMetrics       | Az alkalmazás által létrehozott egyéni metrikákat. |
| Függőségek        | Az alkalmazás külső összetevők hívásait. |
| Kivételek          | Az alkalmazás futtatókörnyezete által okozott kivételeket. |
| pageViews           | Minden webhely adatainak megtekintése a böngészőben adatokkal. |
| PerformanceCounters | A számítási erőforrásokat, az alkalmazás támogatja a TELJESÍTMÉNYMÉRÉSEK. |
| kérelmek            | Minden egyes alkalmazáskérelem adatai.  |
| nyomok              | Elosztott nyomkövetést eredményeit. |

A séma minden egyes is megtekintheti a **séma** az alkalmazás a Log Analytics lapot.

![Alkalmazás-séma](media/scope/application-schema.png)

## <a name="standard-properties"></a>Szokásos tulajdonságok
Az Azure Monitor naplóira minden tábla saját sémával rendelkezik, amíg nincsenek összes tábla által megosztott alapvető tulajdonságok. Lásd: [alapvető tulajdonságainak az Azure Monitor naplóira](../platform/log-standard-properties.md) kapcsolatos.

| Log Analytics-munkaterület | Application Insights-alkalmazás | Leírás |
|:---|:---|:---|
| TimeGenerated | timestamp  | Dátum és idő a rekord létrejött. |
| Típus          | itemType   | A rekord való lekérdezés tábla nevét. |
| _ResourceId   |            | Az erőforrás a rekord egyedi azonosítója társítva van. |
| _IsBillable   |            | Megadja, hogy a feldolgozott adatok számlázható. |
| _BilledSize   |            | Megadja a méretet, számlázunk adatok (bájt). |

## <a name="next-steps"></a>További lépések
- A megismerése [létrehozásához és szerkesztéséhez a Log Analytics naplóbeli kereséseivel](../log-query/portals.md).
- Tekintse meg a [oktatóanyag lekérdezések írásáról](../log-query/get-started-queries.md) az új lekérdezési nyelv segítségével.
