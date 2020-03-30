---
title: Az Azure IoT Hub áttéra diagnosztikai beállításokra | Microsoft dokumentumok
description: Az Azure IoT Hub frissítése az Azure diagnosztikai beállításainak használatához a műveletek figyelése helyett az IoT hubon lévő műveletek állapotának valós idejű figyeléséhez.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750691"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Az IoT Hub áttelepítése a műveletek figyeléséből diagnosztikai beállításokba

A [műveletek figyelésével](iot-hub-operations-monitoring.md) az IoT Hub műveleteinek állapotát nyomon követhető ügyfelek áttelepíthetik a munkafolyamatot az [Azure diagnosztikai beállításaiba](../azure-monitor/platform/platform-logs-overview.md), amely az Azure Monitor egyik szolgáltatása. A diagnosztikai beállítások számos Azure-szolgáltatás erőforrásszintű diagnosztikai adatait biztosítják.

**Az IoT Hub műveletek figyelési funkciói elavultak,** és el lettek távolítva a portálról. Ez a cikk a számítási feladatok áthelyezése a műveletek figyelése a diagnosztikai beállításokat. Az eprecációs ütemtervről az [Azure IoT-megoldások figyelése az Azure Monitor és](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)az Azure Resource Health segítségével című témakörben talál további információt.

## <a name="update-iot-hub"></a>IoT-központ frissítése

Az IoT Hub frissítéséhez az Azure Portalon először kapcsolja be a diagnosztikai beállításokat, majd kapcsolja ki a műveletek figyelését.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>A műveletek figyelésének kikapcsolása

> [!NOTE]
> 2019. március 11-től a műveletek figyelési funkciója törlődik az IoT Hub Azure Portal felületéről. Az alábbi lépések már nem érvényesek. Az áttelepítéshez győződjön meg arról, hogy a megfelelő kategóriák be vannak kapcsolva az Azure Monitor fenti diagnosztikai beállításait.

Miután tesztelte az új diagnosztikai beállításokat a munkafolyamatban, kikapcsolhatja a műveletek figyelési funkcióját. 

1. Az IoT Hub menüjében válassza **az Operations monitoring**lehetőséget.

2. Az egyes figyelési kategóriák alatt válassza a **Nincs**lehetőséget.

3. Mentse a műveletek figyelése változásokat.

## <a name="update-applications-that-use-operations-monitoring"></a>A műveletek figyelését használó alkalmazások frissítése

A műveletek figyelési és diagnosztikai beállításainak sémája kissé eltér. Fontos, hogy frissítse az alkalmazásokat, amelyek a műveletek figyelése ma leképezése a séma által használt diagnosztikai beállításokat. 

Emellett a diagnosztikai beállítások öt új kategóriát kínálnak a nyomon követéshez. A meglévő séma alkalmazásának frissítése után adja hozzá az új kategóriákat is:

* Felhőből az eszközre irányuló ikerműveletek
* Az eszközök közötti ikerműveletek
* Ikerlekérdezések
* Feladatműveletek
* Közvetlen módszerek

Az adott sémastruktúrákról [a Diagnosztikai beállítások sémájának megismerése című](iot-hub-monitor-resource-health.md#understand-the-logs)témakörben olvashat.

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>A figyelőeszköz alacsony késleltetéssel csatlakozik és bontja az eseményeket

Az eszközcsatlakoztatás figyeléséhez és az éles környezetben lévő események leválasztásához javasoljuk, hogy az Eseményrácson az [ **eszköz leválasztott** eseményére](iot-hub-event-grid.md#event-types) való feliratkozással értesítéseket kapjon, és figyelje az eszköz kapcsolati állapotát. Ebből az [oktatóanyagból](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálhatja az IT Hubról az Eszközkapcsolt és az eszközleválasztott eseményeket az IoT-megoldásban.

## <a name="next-steps"></a>További lépések

[Az Azure IoT Hub állapotának monitorozása és a problémák gyorsan diagnosztizálása](iot-hub-monitor-resource-health.md)
