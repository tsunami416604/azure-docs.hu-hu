---
title: Az Azure IoT Hub 403004-es deviceMaximumQueueDepthexceeded(A) eszközmaximális várólistájának megoldása
description: A 403004-es deviceMaximumQueueDepthExceeded(DeviceMaximumQueueDepthExceeded) hiba javítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497495"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Ez a cikk a **403004 DeviceMaximumQueueDepthExceedt** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Amikor felhőből eszközre irányuló üzenetet próbál küldeni, a kérelem **a 403004** vagy a **DeviceMaximumQueueDepthExceeded**hibával sikertelen.

## <a name="cause"></a>Ok

Ennek oka az, hogy az eszközhöz várólistára helyezett üzenetek száma meghaladja a [várólista-korlátot (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

A legvalószínűbb oka annak, hogy fut be ezt a korlátot, mert https-t `ReceiveAsync`használ az üzenet fogadásához, ami folyamatos lekérdezéshez vezet a használatával, ami azt eredményezi, hogy az IoT Hub szabályozza a kérelmet.

## <a name="solution"></a>Megoldás

A HTTPS protokollt használó, a felhőből az eszközre irányuló üzenetek támogatott mintája időnként csatlakoztatott eszközök, amelyek ritkán (kevesebb mint 25 percenként) ellenőrzik az üzeneteket. A várólistakorlátba való befutás valószínűségének csökkentése érdekében váltson AMQP vagy MQTT-re a felhőből az eszközre irányuló üzenetekhez.

Másik lehetőségként javíthatja az eszközoldali logikát a várólistán lévő üzenetek gyors befejezéséhez, elutasításához vagy elhagyásához, lerövidítheti az életidőt, vagy fontolja meg kevesebb üzenet küldését. Lásd [a C2D-üzenetek élettartamával foglalkozó részt](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Végül fontolja meg a [Várólista kiürítése API-t](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) a függőben lévő üzenetek rendszeres kiürítéséhez a korlát elérése előtt.