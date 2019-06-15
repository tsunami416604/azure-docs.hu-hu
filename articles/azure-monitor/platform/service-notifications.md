---
title: Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon
description: Szolgáltatás állapotára vonatkozó értesítések lehetővé teszik a Microsoft Azure által közzétett service health üzenetek megtekintéséhez.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069354"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

Szolgáltatás állapotára vonatkozó értesítések az Azure infrastruktúráján teszi közzé. Az előfizetéshez tartozó erőforrásokra vonatkozó adatokat tartalmaznak. Ezek az értesítések egy alárendelt osztály tevékenység alkalmazásnapló-események, és így is található a tevékenységnaplóban. Szolgáltatás állapotára vonatkozó értesítések tájékoztató vagy az osztály függően hasznos lehet.

A szolgáltatás állapotával kapcsolatos értesítésekre különböző osztályú további információkért lásd: [egészségügyi értesítések szolgáltatástulajdonságok](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>A szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon

1. Az a [az Azure portal](https://portal.azure.com)válassza **figyelő**.

    ![Képernyőkép az Azure portál menüjében a kijelölt figyelő](./media/service-notifications/home-monitor.png)

    Az Azure Monitor összegyűjti az összes figyelési beállítást és adatok egyetlen, összevont nézetben. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

1. Válassza ki **riasztások**.

    ![Képernyőkép a Monitor tevékenységnapló-, a riasztások a kiválasztott](./media/service-notifications/service-health-summary.png)

1. Válassza ki **+ tevékenységnapló-riasztás hozzáadása**, és állítsa be a riasztást annak biztosítása érdekében a jövőbeli szolgáltatási értesítésekhez értesítést kap. További információkért lásd: [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>További lépések

* Kap [riasztási értesítéseket, amikor a szolgáltatás állapotával kapcsolatos értesítés](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) tesznek közzé.  
* Tudjon meg többet [tevékenységnapló-riasztások](../../azure-monitor/platform/activity-log-alerts.md).
