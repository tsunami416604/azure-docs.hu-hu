---
title: Az Azure IoT Hub 403002-es hibaesetén az IoTHubQuotaExceeded
description: A 403002-es IoTHubQuotaExceeded hiba javítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961113"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Ez a cikk a **403002 IoTHubQuotaExceeded** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az IoT Hubra irányuló összes kérelem sikertelen a **403002 IoTHubQuotaExceeded**hibával. Az Azure Portalon az IoT hub-eszközök listája nem töltődik be.

## <a name="cause"></a>Ok

Az IoT hub napi üzenetkvótája túllépve. 

## <a name="solution"></a>Megoldás

[Frissítse vagy növelje az egységek számát az IoT hubon,](iot-hub-upgrade.md) vagy várja meg a következő UTC napot a napi kvóta frissítéséhez.

## <a name="next-steps"></a>További lépések

* Ha meg szeretné tudni, hogy a műveletek hogyan számítanak bele a kvótába, például a kettős lekérdezések és a közvetlen metódusok, [lásd: IoT Hub díjszabásának ismertetése](iot-hub-devguide-pricing.md#charges-per-operation)
* A napi kvótahasználat figyelésének beállításához állítson be egy riasztást a *használt üzenetek teljes számával.* A részletes útmutatásért olvassa el a [Metrikák és riasztások beállítása az IoT Hubbal](tutorial-use-metrics-and-diags.md#set-up-metrics)