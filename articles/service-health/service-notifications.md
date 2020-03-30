---
title: Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon
description: A szolgáltatásállapot-értesítések lehetővé teszik a Microsoft Azure által közzétett szolgáltatásállapot-üzenetek megtekintését.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748649"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

A szolgáltatásállapot-értesítéseket az Azure-infrastruktúra teszi közzé az [Azure tevékenységnaplójában.](../azure-monitor/platform/platform-logs-overview.md)  Az értesítések az előfizetés alá eső erőforrásokkal kapcsolatos információkat tartalmaznak. Tekintettel a tevékenységnaplóban tárolt adatok nagy mennyiségére, külön felhasználói felület áll rendelkezésre, amely megkönnyíti a szolgáltatásállapot-értesítésekre vonatkozó riasztások megtekintését és beállítását. 

A szolgáltatásállapot-értesítések az osztálytól függően lehetnek tájékoztató vagy perelhető kérhetőek.

A szolgáltatásállapot-értesítések különböző osztályairól a [Szolgáltatásállapot-értesítések tulajdonságai című](service-health-notifications-properties.md)témakörben talál további információt.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatásállapot-értesítések megtekintése az Azure Portalon

1. Az [Azure Portalon](https://portal.azure.com)válassza a **Figyelő**lehetőséget.

    ![Képernyőkép az Azure Portal menüjéről, kijelölve a Monitor](./media/service-notifications/home-monitor.png)

    Az Azure Monitor egyetlen összevont nézetben egyesíti az összes figyelési beállítást és adatot. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

1. Válassza **a Riasztások lehetőséget.**

    ![Képernyőkép a Figyelő tevékenységnaplóról, kijelölve a Riasztások](./media/service-notifications/service-health-summary.png)

1. Válassza a **+Tevékenységnapló-riasztás hozzáadása**lehetőséget, és állítson be egy riasztást, amely biztosítja, hogy értesítést kapjon a jövőbeli szolgáltatási értesítésekről. További információt a [Tevékenységnapló-értesítések létrehozása a szolgáltatásértesítésekhez](../azure-monitor/platform/alerts-activity-log-service-notifications.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

* További információ a [tevékenységnapló-riasztásokról.](../azure-monitor/platform/activity-log-alerts.md)
