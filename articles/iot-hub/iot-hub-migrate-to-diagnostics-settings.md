---
title: Diagnosztikai beállítások áttelepítése az Azure IoT Hub |} A Microsoft Docs
description: Hogyan lehet frissíteni az Azure IoT hubra az Azure diagnosztikai beállítások használata helyett a műveletek, az IoT hub valós idejű műveleti állapotának figyelése.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 85bdb4b4802283c591e4d7a9e8b14ae74fa44e8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666722"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Az IoT Hub át műveletek figyelése a diagnosztikai beállítások

Használó ügyfelek [műveletek figyelése] [ lnk-opsmon] műveletek az IoT Hub állapotának nyomon követését is áttelepíthet az adott munkafolyamat [Azure diagnosztikai beállítások] [ lnk-diagnostics-settings], az Azure Monitor szolgáltatás. Diagnosztikai beállítások adja meg az erőforráscsoport-szintű diagnosztikai adatok számos Azure-szolgáltatásokhoz.

A műveletek az IoT Hub monitorozási funkciókat elavult, és a jövőben törlődik. Ez a cikk lépéseit a műveletek figyelése a diagnosztikai beállításokba mozgatását. Az elavulással kapcsolatos ütemterv kapcsolatos további információkért lásd: [monitorozása az Azure IoT-megoldások az Azure monitorral és az Azure Resource Health][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Az IoT Hub frissítése

Az Azure Portalon az IoT Hub frissíteni, először kapcsolja be a diagnosztikai beállítások, majd kapcsolja ki a műveletek figyelése.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Kapcsolja ki a műveletek figyelése

A munkafolyamat tesztelése az új diagnosztikai beállítások, után kikapcsolhatja a hálózatfigyelési szolgáltatást műveletek. 

1. Válassza ki az IoT Hub menüben **műveletek figyelése**.
1. Minden felügyeleti kategória területen válassza ki a **None**.
1. A műveletek figyelése a módosítások mentéséhez.

## <a name="update-applications-that-use-operations-monitoring"></a>Műveletek figyelése használó alkalmazások frissítése

Műveletek figyelése és a diagnosztikai beállításokat a sémák némileg eltérőek lehetnek. Fontos, hogy a műveletek figyelése ma leképezése a diagnosztikai beállítások által használt sémát használó alkalmazások frissítéséhez. 

Ezenkívül diagnosztikai beállítások ajánlatok nyomon követése az öt új kategóriák is elérhetők. Miután frissítette az alkalmazást a meglévő séma, adja hozzá az új kategóriák:

- Felhőalapú ikereszköz műveletek
- Eszközről a felhőbe – az ikereszköz-műveletek
- Ikereszköz-lekérdezések
- Feladatműveletek
- Közvetlen metódusok

Lásd: a megadott séma struktúrák [megismerni a sémát a diagnosztikai beállítások][lnk-diagnostics-schema].

## <a name="next-steps"></a>További lépések

- [Azure IoT Hub állapotának monitorozásához és a problémák gyorsan diagnosztizálása][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/
[lnk-monitor]: iot-hub-monitor-resource-health.md
