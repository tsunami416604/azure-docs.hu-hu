---
title: Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon
description: Tekintse meg a szolgáltatás állapotával kapcsolatos értesítéseket a Azure Portal. A szolgáltatás állapotával kapcsolatos értesítéseket az Azure-infrastruktúra teszi közzé az Azure-tevékenység naplójába.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 615d08b6a04aef9e8ef2033154da8ff8caeebe04
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967770"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

A szolgáltatás állapotával kapcsolatos értesítéseket az Azure-infrastruktúra teszi közzé az [Azure-tevékenység naplójába](../azure-monitor/platform/platform-logs-overview.md).  Az értesítések az előfizetéséhez tartozó erőforrásokra vonatkozó információkat tartalmaznak. A tevékenység naplójában tárolt, valószínűleg nagy mennyiségű információ miatt külön felhasználói felület van, amely megkönnyíti a riasztások megtekintését és beállítását a szolgáltatás állapotára vonatkozó értesítésekben. 

A szolgáltatás állapotával kapcsolatos értesítések az osztálytól függően tájékoztató vagy végrehajthatóak lehetnek.

A szolgáltatási állapot értesítéseinek különböző osztályaival kapcsolatos további információkért lásd: a [Service Health Notifications tulajdonságai](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatás állapotára vonatkozó értesítések megtekintése a Azure Portal

1. A [Azure Portal](https://portal.azure.com)válassza a **figyelő**elemet.

    ![A Azure Portal menü képernyőképe a kijelölt figyelővel](./media/service-notifications/home-monitor.png)

    A Azure Monitor az összes megfigyelési beállítást és az adatait egyetlen összevont nézetbe foglalja össze. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

1. Válassza a **riasztások**lehetőséget.

    ![Képernyőfelvétel a figyelési tevékenység naplóról, a riasztások kiválasztásával](./media/service-notifications/service-health-summary.png)

1. Válassza a **+ tevékenység naplójának hozzáadása riasztás**lehetőséget, és állítson be egy riasztást, amely biztosítja, hogy értesítést kapjon a jövőbeli szolgáltatási értesítésekről. További információkért lásd: [műveletnapló riasztások létrehozása a szolgáltatási értesítéseken](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [tevékenység naplójának riasztásokról](../azure-monitor/platform/activity-log-alerts.md).
