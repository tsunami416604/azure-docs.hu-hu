---
title: Összegyűjtheti és elemezheti az Azure-Tevékenységnaplók Log Analytics-munkaterületen |} A Microsoft Docs
description: Az Azure-tevékenységnapló az Azure Monitor naplóira összegyűjtheti, és a figyelési megoldás segítségével elemezheti és az összes Azure-előfizetések az Azure-tevékenységnapló kereshet.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248129"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Összegyűjtheti és elemezheti a Log Analytics-munkaterületet az Azure monitorban az Azure-Tevékenységnaplók
A [Azure-tevékenységnapló](activity-logs-overview.md) előfizetés-szintű eseményeit, amelyek az Azure-előfizetésében történt betekintést nyújt. Ez a cikk bemutatja, hogyan lehet a tevékenységnapló gyűjtése a Log Analytics-munkaterületet és az Activity Log Analytics használata [figyelési megoldás](../insights/solutions.md), amely biztosít log lekérdezések és nézetek adatok elemzésére szolgáló. 

A tevékenységnapló csatlakoztatása a Log Analytics-munkaterület az alábbi előnyöket nyújtja:

- A tevékenységnaplóban a több Azure-előfizetéssel való elemzéshez egyetlen helyen egyesíthetők.
- Tevékenység-bejegyzései 90 napnál hosszabb ideig Store.
- Vesse össze a tevékenységnapló adatait az Azure Monitor által gyűjtött egyéb figyelési adatok.
- Használat [lekérdezések naplózását](../log-query/log-query-overview.md) összetett elemzéseket végezhet, és részletes információkhoz juthat a tevékenységnapló-bejegyzései.

## <a name="connect-to-log-analytics-workspace"></a>Csatlakozhat a Log Analytics-munkaterület
Egy tevékenységnapló csak egy munkaterülethez csatlakoztathatók, de a tevékenységnapló ugyanahhoz az Azure-bérlőhöz több előfizetéshez tartozó központi munkaterülete lehet csatlakoztatni. A gyűjtemény több bérlőre kiterjedő, lásd: [Azure Tevékenységnaplók gyűjteni azokat a Log Analytics-munkaterületet az Azure Active Directory különböző előfizetésekben bérlők](activity-log-collect-tenants.md).

Az alábbi eljárás segítségével a tevékenységnapló csatlakoztatása a Log Analytics-munkaterület:

1. Az a **Log Analytics-munkaterületek** menü az Azure Portalon, válassza ki a munkaterületet, a tevékenységnapló gyűjtéséhez.
1. Az a **munkaterület adatforrásai** szakasz a munkaterület menüjében válassza az **Azure tevékenységnapló**.
1. Kattintson az előfizetésre, amelyhez csatlakozni.

    ![Munkaterületek](media/activity-log-export/workspaces.png)

1. Kattintson a **Connect** az előfizetésben a tevékenységnapló kapcsolódni a kiválasztott munkaterületen. Ha az előfizetés már össze van kapcsolva egy másik munkaterülettel, kattintson a **Disconnect** , először válassza le azt.

    ![Csatlakozás a munkaterületek](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>A Log Analytics-munkaterület elemzése
A Log Analytics-munkaterülethez való csatlakozáskor egy tevékenységnapló bejegyzések lesz írva a munkaterület a következő táblába **AzureActivity** lekérhető a egy [naplólekérdezés](../log-query/log-query-overview.md). Ez a táblázat szerkezetét attól függően változik, a [naplóbejegyzés kategóriáját](activity-logs-overview.md#categories-in-the-activity-log). Lásd: [Azure-tevékenységnapló eseménysémája](activity-log-schema.md) az egyes kategóriák leírását.

## <a name="activity-logs-analytics-monitoring-solution"></a>Tevékenység naplók elemzési figyelési megoldás
Az Azure Log Analytics figyelési megoldás magában foglalja a több log lekérdezések és nézetek elemzése a Log Analytics-munkaterületet a tevékenységnapló-rekordokat.

### <a name="install-the-solution"></a>A megoldás telepítése
Ismertetett eljárással [figyelési megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution) telepítéséhez a **Activity Log Analytics** megoldás. Nincs szükség további konfigurálásra.

### <a name="use-the-solution"></a>A megoldás használatához
Figyelési megoldások érhető el a **figyelő** menü az Azure Portalon. Válassza ki **további** a a **Insights** szakaszban megnyitásához a **áttekintése** a megoldás csempéiben tartalmazó oldalt. A **Azure-tevékenységnaplóinak** száma számát jeleníti meg a csempe **AzureActivity** rögzíti a munkaterületén.

![Azure Tevékenységnaplók csempéje](media/collect-activity-logs/azure-activity-logs-tile.png)


Kattintson a **Azure-tevékenységnaplóinak** csempére kattintva nyissa meg a **Azure-tevékenységnaplóinak** megtekintése. A nézet az alábbi táblázatban a Vizualizáció részeket tartalmazza. Minden egyes. része felsorolja a megadott időtartományban a részek feltételeknek megfelelő legfeljebb 10 elemet. A napló lekérdezéssel, amely visszaadja az összes ilyen rekordját kattintva **összes** alján, a rész.

![Azure Tevékenységnaplók irányítópultja](media/collect-activity-logs/activity-log-dash.png)

| Vizualizációs rész | Leírás |
| --- | --- |
| Az Azure tevékenységnapló-bejegyzései | Megjeleníti a sávdiagramot oszlopdiagramra cseréli a legfontosabb Azure-tevékenységnapló bejegyzés a kijelölt dátumtartományban rekord összegek, és a felső 10 tevékenység hívók listáját jeleníti meg. Kattintson a sávdiagram egy Naplókeresés futtatásához a `AzureActivity`. Egy hívó elemre egy Naplókeresés futtatásához, a cikk az összes tevékenység naplóbejegyzések visszaadása. |
| Tevékenységnaplók állapot szerint | A perecdiagram az Azure-tevékenységnapló állapota a kijelölt dátumtartományban és az első tíz állapot rekordok listáját jeleníti meg. Kattintson a diagramra a napló lekérdezések futtatása `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Kattintson egy állapotelem futtatni a keresést a állapot rekord összes tevékenység naplóbejegyzés indíthat a naplóban. |
| Tevékenységnaplók erőforrás szerint | Tevékenységnaplók az erőforrások teljes számát jeleníti meg, és az első tíz erőforrások rekord száma az egyes erőforrások listázza. A teljes terület a Naplókeresés futtatásához kattintson `AzureActivity | summarize AggregatedValue = count() by Resource`, amely jelzi, hogy az összes Azure-erőforrások rendelkezésre a megoldáshoz. Kattintson egy erőforrásra egy adott erőforráshoz tartozó összes tevékenység rekordot ad vissza log-lekérdezés futtatásához. |
| Erőforrás-szolgáltató által tevékenységeket tartalmazó naplók | Erőforrás-szolgáltatókat tartalmazó tevékenységeket tartalmazó naplók előállítására teljes számát jeleníti meg, és a tíz sorolja fel. Kattintson a napló lekérdezések futtatása a teljes terület `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, amely jelzi, hogy az összes Azure-erőforrás-szolgáltatók. Kattintson az erőforrás-szolgáltató egy összes tevékenység rekord visszaadása a szolgáltató log-lekérdezés futtatásához. |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [tevékenységnapló](activity-logs-overview.md).
- Tudjon meg többet a [Azure Monitor adatplatform](data-platform.md).
- Használat [lekérdezések naplózását](../log-query/log-query-overview.md) a tevékenységnaplóról részletes információk megtekintéséhez.
