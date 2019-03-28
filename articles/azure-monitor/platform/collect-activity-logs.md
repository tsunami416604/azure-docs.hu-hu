---
title: Összegyűjtheti és elemezheti az Azure-Tevékenységnaplók Log Analytics-munkaterületen |} A Microsoft Docs
description: Az Azure-tevékenységnaplóinak megoldás segítségével elemezheti és az összes Azure-előfizetések az Azure-tevékenységnapló kereshet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: magoedte
ms.openlocfilehash: 2fd74262d9c1b4a751df5d836f98bf89d31dbdc2
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540431"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Összegyűjtheti és elemezheti a Log Analytics-munkaterületet az Azure monitorban az Azure-Tevékenységnaplók

![Azure Tevékenységnaplók szimbólum](./media/collect-activity-logs/activity-log-analytics.png)

Az Activity Log Analytics megoldás segítségével elemezheti és keresse a [Azure tevékenységnapló](../../azure-monitor/platform/activity-logs-overview.md) az összes Azure-előfizetés. Az Azure-tevékenységnapló egy naplóban, amely az előfizetésekben erőforrásokon végrehajtott műveletekkel kapcsolatos információkat biztosít. A tevékenységnapló korábbi nevén *Auditnaplók* vagy *műveleti naplók* óta jelentést készít az előfizetés eseményeire.

A tevékenységnapló használatával megadhatja, hogy a *mi*, *akik*, és *amikor* írási műveletek (PUT, POST, DELETE) arról, hogy az erőforrást az előfizetésében. A műveletek és az egyéb releváns tulajdonságok állapotát is ismernie is. A tevékenységnapló nem tartalmaz olvasási (GET) műveleteket, illetve az erőforrások a klasszikus üzemi modellt használó műveleteket.

A csatlakozáskor az Azure-Tevékenységnaplók Log Analytics-munkaterületet a következőket teheti:

- Az előre meghatározott nézeteket a vizsgálati naplók elemzése
- Elemezheti és a Keresés és a tevékenység naplóinak több Azure-előfizetéssel
- A Tevékenységnaplók 90 napnál hosszabb ideig megőrizni<sup>1</sup>
- Tevékenységnaplók korrelációját, ha más Azure-platform-és alkalmazásadatok
- Tekintse meg az állapot szerint összesített üzemeltetési tevékenységek
- Tevékenységek történik az egyes Azure-szolgáltatások nézet trendek
- Jelentés az engedélyezési módosításait az Azure-erőforrások
- Azonosítsa, mely negatív hatással az erőforrások vagy szolgáltatáskimaradás esetén állapotbeli problémák
- Naplóbeli keresés használatával vesse össze a felhasználói tevékenységek, automatikus skálázási műveletek, az engedélyezési módosításainak és a környezet más naplók és metrikák szolgáltatásának állapota

<sup>1</sup>alapértelmezés szerint az Azure Monitor tartja az Azure-Tevékenységnaplók Log Analytics-munkaterület 90 nap, akkor is, ha azokat ingyenes szint. Vagy, ha 90 napnál kevesebb munkaterület adatmegőrzési beállítását. Ha a munkaterület 90 napnál hosszabb megőrzési rendelkezik, a tevékenységnaplókat őrzi meg a alapján a megőrzési idő a munkaterület a.

A Log Analytics-munkaterület ingyenesen Tevékenységnaplók gyűjt, és a naplókat tároló 90 napig ingyenesen. Ha 90 napnál hosszabb ideig naplókat tárolja, az adatok 90 napnál tovább tárolt adatok adatmegőrzési díjat számolunk.

Ha Ön az ingyenes tarifacsomag, tevékenységeket tartalmazó naplók nem vonatkoznak a napi adatok használata során.

## <a name="connected-sources"></a>Összekapcsolt források

Ellentétben a legtöbb Azure Monitor megoldások adatok nem lesznek gyűjtve tevékenységnaplóit ügynökök által. A megoldás által használt összes adat közvetlenül az Azure-ból származik.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Nem | Windows-ügynököktől a megoldás nem gyűjt adatokat. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás a Linux-ügynökök nem gyűjt adatokat. |
| [Az SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Nem | A megoldás az ügynökök a csatlakoztatott SCOM felügyeleti csoport nem gyűjt adatokat. |
| [Azure Storage-fiók](collect-azure-metrics-logs.md) | Nem | A megoldás nem gyűjt adatokat az Azure storage-ból. |

## <a name="prerequisites"></a>Előfeltételek

- Szeretné elérni az Azure-Tevékenységnaplók adatait, Azure-előfizetéssel kell rendelkeznie.

## <a name="configuration"></a>Konfiguráció

A következő lépésekkel konfigurálja az Activity Log Analytics megoldást, a munkaterületek.

1. Engedélyezze az Activity Log Analytics megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) felületéről vagy a [Log Analytics-megoldások hozzáadása a megoldástárból](../../azure-monitor/insights/solutions.md) című témakörben leírt eljárást követve.
2. Tevékenységnaplók az Ugrás a Log Analytics-munkaterület konfigurálása.
    1. Az Azure Portalon válassza ki a munkaterületet, és kattintson a **Azure tevékenységnapló**.
    2. Az egyes előfizetésekhez kattintson az előfizetés nevét.  
        ![Előfizetés hozzáadása](./media/collect-activity-logs/add-subscription.png)
    3. Az a *SubscriptionName* panelen kattintson a **Connect**.  
        ![Előfizetés csatlakoztatása](./media/collect-activity-logs/subscription-connect.png)

Az Azure Portalra való csatlakozáshoz jelentkezzen be egy Azure-előfizetést a munkaterületre.  

## <a name="using-the-solution"></a>A megoldás használata

A munkaterülethez az Activity Log Analytics megoldás hozzáadásakor az **Azure-tevékenységnaplóinak** az áttekintő irányítópult, csempe jelenik meg. Ez a csempe az Azure-előfizetést, amely hozzáfér a megoldás az Azure-tevékenységi rekordok száma számát jeleníti meg.

![Azure Tevékenységnaplók csempéje](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Azure Tevékenységnaplók megtekintése

Kattintson a **Azure-tevékenységnaplóinak** csempére kattintva nyissa meg a **Azure-tevékenységnaplóinak** irányítópultot. Az irányítópulton az alábbi táblázatban felsorolt panelek találhatók. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. A panel alján található **Az összes megtekintése** elemre vagy a panel fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

Tevékenységnapló adatainak csak akkor jelenik meg *után* konfigurálta a vizsgálati naplók nyissa meg a megoldást, így még a határidő előtt nem tekintheti meg adatokat.

| Panel | Leírás |
| --- | --- |
| Az Azure tevékenységnapló-bejegyzései | Bemutatja az Azure-tevékenységi naplóbejegyzés felső sávdiagram a kijelölt dátumtartományban rekord összegek és a felső 10 tevékenység hívók listáját jeleníti meg. Kattintson a sávdiagram egy Naplókeresés futtatásához a <code>AzureActivity</code>. Egy hívó elemre egy Naplókeresés futtatásához az összes tevékenységnapló-bejegyzései elem visszaadása. |
| Tevékenységnaplók állapot szerint | Az Azure-tevékenységi napló állapotát a kijelölt dátumtartományban perecdiagrammá mutatja. Az első tíz állapot rekordok listáját is megjeleníti a egy listát. Kattintson a diagramra a Naplókeresés futtatásához <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Kattintson egy állapot elemre egy Naplókeresés futtatásához az összes tevékenységnapló-bejegyzései állapot rekord visszaadása. |
| Tevékenységnaplók erőforrás szerint | Tevékenységnaplók az erőforrások teljes számát jeleníti meg, és az első tíz erőforrások rekord száma az egyes erőforrások listázza. A teljes terület a Naplókeresés futtatásához kattintson <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, amely jelzi, hogy az összes Azure-erőforrások rendelkezésre a megoldáshoz. Kattintson egy erőforrást egy Naplókeresés futtatásához az adott erőforrás az összes tevékenység rekordot ad vissza. |
| Erőforrás-szolgáltató által tevékenységeket tartalmazó naplók | Teljes száma, amelyek a tevékenység erőforrás-szolgáltatók naplózza, és felsorolja a tíz jeleníti meg. A teljes terület a Naplókeresés futtatásához kattintson <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, amely jelzi, hogy az összes Azure-erőforrás-szolgáltatók. Kattintson az erőforrás-szolgáltató, a szolgáltató minden tevékenységrekordok visszaadó Naplókeresés futtatásához. |

![Azure Tevékenységnaplók irányítópultja](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>További lépések

- Hozzon létre egy [riasztás](../../azure-monitor/platform/alerts-metric.md) mikor történik, egy adott tevékenységet.
- Használat [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md) a tevékenységnaplókból részletes információk megtekintéséhez.
