---
title: Azure-beli tevékenység-naplók összegyűjtése és elemzése Log Analytics munkaterületen | Microsoft Docs
description: Gyűjtse össze az Azure-beli tevékenység naplóját Azure Monitor naplókban, és a figyelési megoldás használatával elemezze és keresse meg az Azure-tevékenység naplóját az összes Azure-előfizetésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 044f974d83eba098820639e67412110329d5ad7d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249112"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-beli tevékenység-naplók gyűjtése és elemzése Log Analytics munkaterületen Azure Monitor

> [!WARNING]
> Mostantól a tevékenység naplóját begyűjtheti egy Log Analytics munkaterületre egy, az erőforrás-naplók összegyűjtéséhez hasonló diagnosztikai beállítás használatával. Lásd: [Az Azure-Tevékenységnaplók összegyűjtése és elemzése log Analytics munkaterületen Azure monitor](diagnostic-settings-legacy.md).

Az [Azure-tevékenység naplója](platform-logs-overview.md) betekintést nyújt az Azure-előfizetésében bekövetkezett előfizetési szintű eseményekre. Ez a cikk bemutatja, hogyan gyűjtheti be a tevékenység naplóját egy Log Analytics munkaterületre, és hogyan használhatja a Activity Log Analytics [figyelési megoldást](../insights/solutions.md), amely naplózási lekérdezéseket és nézeteket biztosít az adatok elemzéséhez. 

A tevékenység naplójának Log Analytics munkaterülethez való csatlakoztatása a következő előnyöket biztosítja:

- A tevékenység naplójának konszolidálása több Azure-előfizetésből egy helyre az elemzéshez.
- 90 napnál hosszabb ideig tárolhatja a tevékenység naplójának bejegyzéseit.
- A tevékenység-naplózási adatok korrelációja Azure Monitor által gyűjtött egyéb megfigyelési adatokkal.
- A [naplók](../log-query/log-query-overview.md) használatával összetett elemzéseket végezhet, és részletes elemzéseket készíthet a tevékenységi naplók bejegyzéseiről.

## <a name="connect-to-log-analytics-workspace"></a>Kapcsolódás Log Analytics munkaterülethez
Egyetlen munkaterület csatlakoztatható ugyanahhoz az Azure-bérlőhöz tartozó több előfizetés tevékenységi naplójához. A több bérlőre kiterjedő gyűjteményekért lásd: az [Azure-tevékenységek naplóinak összegyűjtése egy log Analytics munkaterületre különböző Azure Active Directory-bérlők előfizetései között](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Ha a Microsoft. OperationalInsights és a Microsoft. OperationsManagement erőforrás-szolgáltató nincs regisztrálva az előfizetésében, hibaüzenetet kaphat a következő eljárással kapcsolatban. A szolgáltatók regisztrálásához tekintse meg az [Azure erőforrás-szolgáltatókat és-típusokat](../../azure-resource-manager/management/resource-providers-and-types.md) .

A következő eljárással összekapcsolhatja a tevékenység naplóját a Log Analytics munkaterülettel:

1. A Azure Portal **log Analytics munkaterületek** menüjében válassza ki a munkaterületet a tevékenység naplójának összegyűjtéséhez.
1. A munkaterület menü **munkaterület adatforrásai** területén válassza az **Azure-tevékenység napló**elemet.
1. Kattintson arra az előfizetésre, amelyhez csatlakozni szeretne.

    ![Munkaterületek](media/activity-log-export/workspaces.png)

1. Kattintson a **Kapcsolódás** lehetőségre a tevékenység naplójának a kiválasztott munkaterülethez való összekapcsolásához. Ha az előfizetés már egy másik munkaterülethez van csatlakoztatva, kattintson az első **Leválasztás** elemre a leválasztáshoz.

    ![Munkaterületek összekapcsolása](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Elemzés Log Analytics munkaterületen
Amikor egy Log Analytics munkaterülethez kapcsolódik a tevékenység naplójában, a bejegyzéseket a **AzureActivity** nevű táblába írja a rendszer, amelyet egy [napló lekérdezéssel](../log-query/log-query-overview.md)kérhet le. A tábla szerkezete a [naplóbejegyzés kategóriájára](activity-log-view.md#categories-in-the-activity-log)függően változik. Az egyes kategóriák leírását az [Azure Activity napló esemény sémája](activity-log-schema.md) tartalmazza.

## <a name="activity-logs-analytics-monitoring-solution"></a>Activity logs Analytics monitorozási megoldás
Az Azure Log Analytics-figyelési megoldás több naplózási lekérdezést és nézetet tartalmaz a Log Analytics munkaterületen található műveletnapló-rekordok elemzéséhez.

### <a name="install-the-solution"></a>A megoldás telepítése
A **Activity log Analytics** megoldás telepítéséhez használja a [figyelési megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution) című szakaszt. Nincs szükség további konfigurációra.

### <a name="use-the-solution"></a>A megoldás használatához
A figyelési megoldások a Azure Portal **figyelő** menüjében érhetők el. Válassza a **továbbiak** lehetőséget a **betekintési** szakaszban, hogy megnyissa az **Áttekintés** lapot a megoldás csempével. Az **Azure-tevékenység naplói** csempén a munkaterületen található **AzureActivity** -rekordok száma látható.

![Azure Tevékenységnaplók csempéje](media/collect-activity-logs/azure-activity-logs-tile.png)


Kattintson az **Azure-tevékenységek naplói** csempére az **Azure-tevékenység naplói** nézetének megnyitásához. A nézet a következő táblázatban szereplő vizualizációs részeket tartalmazza. Mindegyik rész legfeljebb 10 olyan elemet sorol fel, amelyek megfelelnek a megadott időtartományra vonatkozó feltételeknek. Futtathat egy olyan naplózási lekérdezést, amely az összes egyező rekordot visszaadja, ha az **összes megtekintése** elemre kattint a rész alján.

![Azure Tevékenységnaplók irányítópultja](media/collect-activity-logs/activity-log-dash.png)

| Vizualizációs rész | Leírás |
| --- | --- |
| Az Azure tevékenységnapló-bejegyzései | Megjeleníti az Azure-beli tevékenység naplójának felső rekordjának a kiválasztott dátumtartományt tartalmazó oszlopát, és megjeleníti az első 10 tevékenység-hívó listáját. Kattintson a sávdiagramra a `AzureActivity`naplójának kereséséhez. Kattintson egy hívó elemre, és futtassa az adott elemhez tartozó összes műveletnapló-bejegyzést. |
| Tevékenységnaplók állapot szerint | Megjeleníti az Azure-tevékenység naplójának állapotát a kiválasztott dátumtartomány esetében, valamint az első tíz állapotüzenetek listáját. Kattintson a diagramra a `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`naplózási lekérdezésének futtatásához. Kattintson egy állapot elemre az adott állapotjelző rekord összes tevékenységi naplójára vonatkozó naplóbeli keresés futtatásához. |
| Tevékenységnaplók erőforrás szerint | Megjeleníti a tevékenységi naplókkal rendelkező erőforrások teljes számát, és felsorolja az egyes erőforrásokhoz tartozó rekordok számát tartalmazó első tíz erőforrást. Kattintson a teljes területen a `AzureActivity | summarize AggregatedValue = count() by Resource`naplójának kereséséhez, amely megjeleníti a megoldás számára elérhető összes Azure-erőforrást. Kattintson egy erőforrásra az adott erőforráshoz tartozó összes tevékenységi rekord visszaadására szolgáló naplózási lekérdezés futtatásához. |
| Erőforrás-szolgáltató által tevékenységeket tartalmazó naplók | Megjeleníti a tevékenységek naplóit előállító erőforrás-szolgáltatók teljes számát, és felsorolja a tíz legfontosabbat. Az összes Azure-erőforrás-szolgáltatót megjelenítő `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`hoz tartozó napló lekérdezésének futtatásához kattintson a teljes területen. Kattintson egy erőforrás-szolgáltatóra egy olyan log-lekérdezés futtatásához, amely a szolgáltató összes tevékenységi rekordját visszaadja. |

## <a name="next-steps"></a>Következő lépések

- További információ a [tevékenység naplóról](platform-logs-overview.md).
- További információ a [Azure monitor adatplatformról](data-platform.md).
- A [napló lekérdezései](../log-query/log-query-overview.md) segítségével megtekintheti a tevékenység naplójának részletes adatait.
