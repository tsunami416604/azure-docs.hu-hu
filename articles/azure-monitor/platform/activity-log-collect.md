---
title: Azure-tevékenységnapló gyűjtése a Log Analytics-munkaterületen
description: Gyűjtse össze az Azure-tevékenységnaplót az Azure Figyelőnaplókban, és használja a figyelési megoldást az Azure-tevékenységnapló elemzéséhez és kereséséhez az összes Azure-előfizetésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055309"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-tevékenységnaplók gyűjtése és elemzése az Azure-figyelő Log Analytics-munkaterületén

> [!WARNING]
> Most már összegyűjtheti a tevékenységnaplót egy Log Analytics-munkaterületen egy olyan diagnosztikai beállítás használatával, amely hasonló az erőforrásnaplók gyűjtéséhez. Lásd: [Azure-tevékenységnaplók gyűjtése és elemzése a Log Analytics-munkaterületen az Azure Monitorban.](diagnostic-settings-legacy.md)

Az [Azure-tevékenységnapló](platform-logs-overview.md) betekintést nyújt az Azure-előfizetésben bekövetkezett előfizetési szintű eseményekbe. Ez a cikk ismerteti, hogyan gyűjtheti a tevékenységnaplót egy Log Analytics-munkaterületre, és hogyan használhatja a Tevékenységnapló-elemzés [figyelési megoldását,](../insights/solutions.md)amely naplólekérdezéseket és nézeteket biztosít az adatok elemzéséhez. 

A tevékenységnapló és a Log Analytics-munkaterület összekapcsolása a következő előnyökkel jár:

- Konszolidálja a tevékenységnaplót több Azure-előfizetésből egyetlen helyre elemzésre.
- 90 napnál hosszabb ideig tárolja a tevékenységnapló-bejegyzéseket.
- A tevékenységnapló adatainak összefüggése az Azure Monitor által gyűjtött egyéb figyelési adatokkal.
- A [naplólekérdezések](../log-query/log-query-overview.md) segítségével összetett elemzéseket végezhet, és mélyreható elemzéseket nyerhet a tevékenységnapló-bejegyzésekről.

## <a name="connect-to-log-analytics-workspace"></a>Csatlakozás a Log Analytics-munkaterülethez
Egyetlen munkaterület is csatlakoztatható a tevékenységnapló több előfizetések ugyanabban az Azure-bérlőben. Több bérlő közötti gyűjtésért olvassa el [az Azure-tevékenységnaplók gyűjtése egy Log Analytics-munkaterületre a különböző Azure Active Directory-bérlők előfizetései között.](activity-log-collect-tenants.md)

> [!IMPORTANT]
> A következő eljárással hibaüzenet jelenhet meg, ha a Microsoft.OperationalInsights és a Microsoft.OperationsManagement erőforrás-szolgáltatók nincsenek regisztrálva az előfizetéshez. Tekintse meg [az Azure-erőforrás-szolgáltatók és -típusok](../../azure-resource-manager/management/resource-providers-and-types.md) ezek a szolgáltatók regisztrálásához.

A tevékenységnaplót az alábbi eljárással kapcsolhatja össze a Log Analytics-munkaterülettel:

1. Az Azure Portal **Log Analytics-munkaterületeinek** menüjében válassza ki a munkaterületet a tevékenységnapló gyűjtéséhez.
1. A munkaterület menüjének **Munkaterület-adatforrások** szakaszában válassza az **Azure-tevékenységnapló t.**
1. Kattintson a csatlakoztatni kívánt előfizetésre.

    ![Munkaterületek](media/activity-log-export/workspaces.png)

1. Kattintson a **Csatlakozás** gombra, ha az előfizetésben lévő tevékenységnaplót a kijelölt munkaterülethez szeretné csatlakoztatni. Ha az előfizetés már csatlakoztatva van egy másik munkaterülethez, először a **Kapcsolat bontásához** kattintson a Kapcsolat bontása gombra.

    ![Munkaterületek összekapcsolása](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Elemzés a Log Analytics-munkaterületen
Amikor egy tevékenységnaplót egy Log Analytics-munkaterülethez csatlakoztat, a program bejegyzéseket ír a munkaterületre egy **AzureActivity** nevű táblába, amelyet [naplólekérdezéssel](../log-query/log-query-overview.md)lehet beolvasni. A tábla szerkezete a [naplóbejegyzés kategóriájától](activity-log-view.md#categories-in-the-activity-log)függően változik . Az [azure-tevékenységnapló eseménysémája](activity-log-schema.md) az egyes kategóriák leírását tartalmazza.

## <a name="activity-logs-analytics-monitoring-solution"></a>Tevékenységnaplók elemzésének figyelési megoldása
Az Azure Log Analytics figyelési megoldás több naplólekérdezést és nézetet tartalmaz a Naplóelemzési munkaterületen lévő tevékenységnapló-rekordok elemzéséhez.

### <a name="install-the-solution"></a>A megoldás telepítése
A **Tevékenységnapló-elemzési** megoldás telepítéséhez a [Figyelési megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution) című témaköreljárásának használatával. Nincs szükség további konfigurációra.

### <a name="use-the-solution"></a>Használja a megoldást
A figyelési megoldások az Azure Portal **Monitor** menüjéből érhetők el. Válassza a **Tovább** lehetőséget az **Elemzések** szakaszban az **Áttekintés** lap megoldáscsempékkel való megnyitásához. **Az Azure-tevékenységnaplók** csempe megjeleníti az **AzureActivity-rekordok** számát a munkaterületen.

![Az Azure-tevékenységnaplók csempéje](media/collect-activity-logs/azure-activity-logs-tile.png)


Kattintson az **Azure-tevékenységnaplók** csempére az **Azure-tevékenységnaplók** nézet megnyitásához. A nézet az alábbi táblázatban szereplő képi megjelenítési részeket tartalmazza. Minden alkatrész legfeljebb 10 elemet sorol fel, amelyek megfelelnek az adott alkatrész adott időtartományra vonatkozó feltételeinek. Futtathat egy naplólekérdezést, amely az összes egyező rekordot visszaadja, ha a Rész alján az **Összes** megtekintése gombra kattint.

![Az Azure-tevékenységnaplók irányítópultja](media/collect-activity-logs/activity-log-dash.png)

| Képi megjelenítési rész | Leírás |
| --- | --- |
| Az Azure-tevékenységnapló bejegyzései | A kiválasztott dátumtartomány első Azure-tevékenységnapló-bejegyzésrekordjának sávdiagramját jeleníti meg, és megjeleníti a 10 legfontosabb tevékenységhívó listáját. Kattintson a sávdiagramra a `AzureActivity`naplókeresés futtatásához. Kattintson egy hívó elemre az adott elem összes tevékenységnapló-bejegyzését visszaadó naplókeresés futtatásához. |
| Tevékenységnaplók állapot szerint | A kiválasztott dátumtartomány perecdiagramját és az első tíz állapotrekord listáját jeleníti meg. Kattintson a diagramra a `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`naplólekérdezés futtatásához. Kattintson egy állapotelemre az állapotrekord összes tevékenységnapló-bejegyzését visszaadó naplókeresés futtatásához. |
| Tevékenységnaplók erőforrás szerint | A tevékenységnaplókkal rendelkező erőforrások teljes számát jeleníti meg, és felsorolja az első tíz erőforrást az egyes erőforrások rekordszámával. Kattintson a teljes területre a `AzureActivity | summarize AggregatedValue = count() by Resource`naplókeresés futtatásához, amely a megoldás számára elérhető összes Azure-erőforrást megjeleníti. Kattintson egy erőforrásra az erőforrás összes tevékenységrekordját visszaadó naplólekérdezés futtatásához. |
| Tevékenységnaplók erőforrásszolgáltató szerint | A tevékenységnaplókat előállító és az első tíz helyet tartalmazó erőforrás-szolgáltatók teljes számát jeleníti meg. Kattintson a teljes területre egy `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`naplólekérdezés futtatásához, amely az összes Azure-erőforrás-szolgáltatót megjeleníti. Kattintson egy erőforrás-szolgáltatóra a szolgáltató összes tevékenységrekordját visszaadó naplólekérdezés futtatásához. |

## <a name="next-steps"></a>További lépések

- További információ a [tevékenységnaplóról.](platform-logs-overview.md)
- További információ az [Azure Monitor adatplatformjáról.](data-platform.md)
- A [naplólekérdezések](../log-query/log-query-overview.md) segítségével megtekintheti a tevékenységnapló részletes adatait.
