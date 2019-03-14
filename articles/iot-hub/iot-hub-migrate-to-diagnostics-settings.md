---
title: Diagnosztikai beállítások áttelepítése az Azure IoT Hub |} A Microsoft Docs
description: Hogyan lehet frissíteni az Azure IoT hubra az Azure diagnosztikai beállítások használata helyett a műveletek, az IoT hub valós idejű műveleti állapotának figyelése.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: b6cde8402c699a7477cd0efc79a44b3f5e150ad0
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792650"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Az IoT Hub át műveletek figyelése a diagnosztikai beállítások

Használó ügyfelek [műveletek figyelése](iot-hub-operations-monitoring.md) műveletek az IoT Hub állapotának nyomon követését is áttelepíthet az adott munkafolyamat [Azure diagnosztikai beállítások](../azure-monitor/platform/diagnostic-logs-overview.md), az Azure Monitor szolgáltatás. Diagnosztikai beállítások adja meg az erőforráscsoport-szintű diagnosztikai adatok számos Azure-szolgáltatásokhoz.

**A műveletek az IoT Hub figyelési funkció elavult**, és a portál el lett távolítva. Ez a cikk lépéseit a műveletek figyelése a diagnosztikai beállításokba mozgatását. Az elavulással kapcsolatos ütemterv kapcsolatos további információkért lásd: [monitorozása az Azure IoT-megoldások az Azure monitorral és az Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Update IoT Hub

Az Azure Portalon az IoT Hub frissíteni, először kapcsolja be a diagnosztikai beállítások, majd kapcsolja ki a műveletek figyelése.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Kapcsolja ki a műveletek figyelése

> [!NOTE]
> Mint 2019. március 11, a műveletek a figyelési funkció eltávolítják az IoT Hub az Azure portal felületén. Az alábbi lépések már nem érvényesek. Át, győződjön meg róla, hogy a helyes-e kategóriák közül az Azure Monitor diagnosztikai beállítások fenti vannak kapcsolva.

Után az új diagnosztikai beállítások tesztelni a munkafolyamat, kikapcsolhatja a hálózatfigyelési szolgáltatást műveleteket. 

1. Válassza ki az IoT Hub menüben **műveletek figyelése**.

2. Minden felügyeleti kategória területen válassza ki a **None**.

3. A műveletek figyelése a módosítások mentéséhez.

## <a name="update-applications-that-use-operations-monitoring"></a>Műveletek figyelése használó alkalmazások frissítése

Műveletek figyelése és a diagnosztikai beállításokat a sémák némileg eltérőek lehetnek. Fontos, hogy a műveletek figyelése ma leképezése a diagnosztikai beállítások által használt sémát használó alkalmazások frissítéséhez. 

Diagnosztikai beállítások nyújt is, öt új kategóriák is elérhetők a nyomkövetésre. Miután frissítette az alkalmazást a meglévő séma, adja hozzá az új kategóriák:

* Felhőalapú ikereszköz műveletek
* Eszközről a felhőbe – az ikereszköz-műveletek
* Ikereszköz-lekérdezések
* Feladatműveletek
* Közvetlen metódusok

Lásd: a megadott séma struktúrák [megismerni a sémát a diagnosztikai beállítások](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Eszköz figyelésének csatlakozhat, és közel valós idejű adateléréssel események leválasztása

Figyelése eszköz csatlakoztatása, és éles környezetben események leválasztása, javasoljuk, hogy feliratkozik a [ **eszköz leválasztva** esemény](iot-hub-event-grid.md#event-types) az Event Grid értesítéseket kaphat, és figyelheti az eszköz kapcsolati állapotát. Ezzel [oktatóanyag](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálható a csatlakoztatott eszköz és az eszköz leválasztott események az IoT Hub az IoT-megoldás.

## <a name="next-steps"></a>További lépések

[Az Azure IoT Hub állapotának monitorozása és a problémák gyorsan diagnosztizálása](iot-hub-monitor-resource-health.md)
