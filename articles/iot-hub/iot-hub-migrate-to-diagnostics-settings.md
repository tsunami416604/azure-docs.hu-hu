---
title: Azure-IoT Hub áttérni a diagnosztikai beállításokra | Microsoft Docs
description: Az Azure IoT Hub frissítése az Azure Diagnostics beállításainak az Operations monitoring helyett történő használatára az IoT hub műveleteinek valós idejű figyeléséhez.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 0feca8b477a1d76eae9e3f41de0026a33cc3249d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259333"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>IoT Hub migrálása az Operations monitoringból a diagnosztikai beállításokba

Azok az ügyfelek, akik az [Operations monitoring](iot-hub-operations-monitoring.md) használatával követik a IoT hub műveleteit, áttelepíthetik ezt a munkafolyamatot az [Azure Diagnostics-beállításokba](../azure-monitor/platform/resource-logs-overview.md), a Azure monitor egy szolgáltatására. A diagnosztikai beállítások számos Azure-szolgáltatás erőforrás-szintű diagnosztikai információit biztosítják.

**A IoT hub Operations monitoring funkciója elavult**, és el lett távolítva a portálról. Ez a cikk azokat a lépéseket ismerteti, amelyekkel áthelyezheti a számítási feladatokat az operatív figyelésből a diagnosztikai beállításokba. Az elavult idővonalról további információt az [Azure IoT-megoldások monitorozása Azure monitor és Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)című témakörben talál.

## <a name="update-iot-hub"></a>Update IoT Hub

A Azure Portal IoT Hub frissítéséhez először kapcsolja be a diagnosztikai beállításokat, majd kapcsolja ki a műveletek figyelését.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Műveletek figyelésének kikapcsolása

> [!NOTE]
> 2019. március 11-én az Operations monitoring szolgáltatás el lett távolítva a IoT Hub Azure Portal felületéről. Az alábbi lépések már nem érvényesek. A Migrálás előtt győződjön meg arról, hogy a megfelelő kategóriák be vannak kapcsolva Azure Monitor a fenti diagnosztikai beállításokban.

Ha teszteli az új diagnosztikai beállításokat a munkafolyamatban, kikapcsolhatja az Operations monitoring funkciót. 

1. A IoT Hub menüben válassza az **Operations monitoring**elemet.

2. Az egyes figyelési kategóriák területen válassza a **nincs**lehetőséget.

3. Mentse az Operations monitoring módosításait.

## <a name="update-applications-that-use-operations-monitoring"></a>Operations monitoringot használó alkalmazások frissítése

Az operatív monitorozási és diagnosztikai beállítások sémái némileg eltérőek. Fontos, hogy frissítse azokat az alkalmazásokat, amelyek az Operations monitoring szolgáltatást használják ma a diagnosztikai beállítások által használt sémához való leképezéshez. 

Emellett a diagnosztikai beállítások öt új kategóriát biztosítanak a nyomon követéshez. Miután frissítette az alkalmazásokat a meglévő sémához, adja hozzá az új kategóriákat is:

* A felhőből az eszközre irányuló kettős műveletek
* Az eszközről a felhőbe irányuló kettős művelet
* Dupla lekérdezés
* Feladatműveletek
* Közvetlen metódusok

Az adott séma-struktúrákkal kapcsolatban lásd: [a diagnosztikai beállításokhoz tartozó séma megismerése](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Az eszközök csatlakoztatásának figyelése és az események leválasztása alacsony késéssel

Ha figyelni szeretné az eszköz csatlakoztatását, és leválasztja az eseményeket az éles üzemben, javasoljuk, hogy a riasztások beszerzéséhez és az eszköz kapcsolati állapotának figyeléséhez az eszközön leválasztott Event Grid [ eseményt](iot-hub-event-grid.md#event-types) . Ebből az [oktatóanyagból](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálhatja a csatlakoztatott eszközök és az eszközök kapcsolatait a IoT-megoldás IoT hub.

## <a name="next-steps"></a>További lépések

[Az Azure IoT Hub állapotának monitorozása és a problémák gyorsan diagnosztizálása](iot-hub-monitor-resource-health.md)
