---
title: Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon
description: Szolgáltatás állapotára vonatkozó értesítések lehetővé teszik a Microsoft Azure által közzétett service health üzenetek megtekintéséhez.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538353"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

Szolgáltatás állapotára vonatkozó értesítések be az Azure infrastruktúráján teszi közzé a [Azure tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md).  Az értesítések az előfizetéshez tartozó erőforrásokra vonatkozó információkat tartalmaznak. Adja meg a tevékenységnaplóban tárolt adatok mérete nagy mennyiségű, van egy külön felhasználói felületet, hogy könnyebben áttekinthetők és riasztásokat állíthat be a szolgáltatás állapotával kapcsolatos értesítésekre. 

Szolgáltatás állapotára vonatkozó értesítések tájékoztató vagy az osztály függően hasznos lehet.

A szolgáltatás állapotával kapcsolatos értesítésekre különböző osztályú további információkért lásd: [egészségügyi értesítések szolgáltatástulajdonságok](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

1. Az a [az Azure portal](https://portal.azure.com)válassza **figyelő**.

    ![Képernyőkép az Azure portál menüjében a kijelölt figyelő](./media/service-notifications/home-monitor.png)

    Az Azure Monitor összegyűjti az összes figyelési beállítást és adatok egyetlen, összevont nézetben. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

1. Válassza ki **riasztások**.

    ![Képernyőkép a Monitor tevékenységnapló-, a riasztások a kiválasztott](./media/service-notifications/service-health-summary.png)

1. Válassza ki **+ tevékenységnapló-riasztás hozzáadása**, és állítsa be a riasztást annak biztosítása érdekében a jövőbeli szolgáltatási értesítésekhez értesítést kap. További információkért lásd: [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [tevékenységnapló-riasztások](../azure-monitor/platform/activity-log-alerts.md).
