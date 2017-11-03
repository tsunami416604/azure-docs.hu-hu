---
title: "Diagnosztikai beállítások áttelepítése az Azure IoT Hub |} Microsoft Docs"
description: "Megtudhatja, hogyan kell használni az Azure diagnosztikai beállítások helyett az IoT hub valós idejű műveletek állapotának figyelésére figyelési műveletek Azure IoT-központ frissítése."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 2716f397ad0e7abfdcd397340da8fa8116a172db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Az IoT Hub át műveletek figyelés felvétele a diagnosztikai beállítások

A felhasználók [figyelési műveletek] [ lnk-opsmon] az IoT-központ műveletek állapotának követésére telepíthet át, hogy a munkafolyamatot [Azure diagnosztikai beállítások] [ lnk-diagnostics-settings], egy Azure-figyelő szolgáltatás. Diagnosztikai beállítások adja meg az erőforrás-szintű diagnosztikai adatainak számos Azure-szolgáltatásokhoz.

A műveletek, az IoT-központ figyelési funkcióit elavult, és a jövőben eltávolításra kerül. Ez a cikk nyújt tájékoztatást helyezhetik át a munkaterheléseket a diagnosztikai beállításokat a figyelés művelettől. Az elavultként ütemterv kapcsolatos további információkért lásd: [figyelése az Azure IoT-megoldások Azure monitorral és az Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Az IoT-központ frissítése

Az Azure-portálon az IoT Hub frissítéséhez először kapcsolja be a diagnosztikai beállításokat, majd kapcsolja ki a figyelési műveletek.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Kapcsolja ki a figyelési műveletek

Az új diagnosztikai beállítások tesztelése a munkafolyamatban, után kikapcsolhatja a hálózatfigyelési szolgáltatást műveletek. 

1. Az IoT-központ menüben válasszon ki **figyelési műveletek**.
1. Válassza ki a minden felügyeleti kategória **nincs**.
1. Mentse a műveletek figyeli a változásokat.

## <a name="update-applications-that-use-operations-monitoring"></a>Figyelési műveletek használó alkalmazások

A figyelési műveletekhez, és a diagnosztikai beállítások sémák némileg eltérőek lehetnek. Fontos, hogy a ma figyelési műveletek hozzárendelése a diagnosztikai beállításokat használják a sémát használó alkalmazások frissíteni. 

Is diagnosztikai beállítások ajánlatok öt új kategóriákban nyomon követését. Alkalmazások a már meglévő séma frissítése után adja hozzá az új kategóriák:

- Felhő eszközre iker műveletek
- Eszköz-felhő iker műveletek
- A két lekérdezések
- Feladatműveletek
- Közvetlen módszer

Tekintse meg a megadott séma struktúrák [megérteni a sémát a diagnosztikai beállítások][lnk-diagnostics-schema].

## <a name="next-steps"></a>Következő lépések

- [Azure IoT Hub állapotának figyelésére, és a problémák diagnosztizálásához gyorsan][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
