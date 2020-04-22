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
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5cc8bae0f0245f5c4b45ca0cd446582b04788c21
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758753"
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