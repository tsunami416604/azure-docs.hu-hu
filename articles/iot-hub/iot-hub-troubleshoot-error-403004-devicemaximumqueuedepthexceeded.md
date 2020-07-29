---
title: Az Azure IoT Hub hibáinak elhárítása 403004 DeviceMaximumQueueDepthExceeded
description: Ismerje meg, hogyan javíthatja a 403004-es hibát a DeviceMaximumQueueDepthExceeded
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758753"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Ez a cikk a **403004 DeviceMaximumQueueDepthExceeded** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

A felhőből az eszközre irányuló üzenetek küldésére tett kísérlet során a kérelem meghiúsul a **403004** -as vagy a **DeviceMaximumQueueDepthExceeded**-es hibával.

## <a name="cause"></a>Ok

A mögöttes ok az, hogy az eszköz várólistán lévő száma meghaladja a [várólista korlátját (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Ennek a korlátnak a legvalószínűbb oka az, hogy a HTTPS használatával fogadja az üzenetet, ami folyamatos lekérdezéseket eredményez a használatával `ReceiveAsync` , ami IoT hub a kérés szabályozását.

## <a name="solution"></a>Megoldás

A HTTPS-alapú felhőből az eszközre irányuló üzenetek támogatott mintája időnként olyan eszközökhöz csatlakozik, amelyek ritkán keresik az üzeneteket (kevesebb, mint 25 percenként). Ha csökkenteni szeretné a várólista-korláton való futás valószínűségét, váltson át a AMQP vagy a MQTT elemre a felhőből az eszközre irányuló üzenetekhez.

Azt is megteheti, hogy kibővíti az eszköz oldalának logikáját a várólistára helyezett üzenetek gyors elvégzéséhez, elutasításához vagy kivonásához, lerövidíti az élettartamot vagy kevesebb üzenetet Lásd [a C2D-üzenetek élettartamával foglalkozó részt](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Végül érdemes lehet a [törlési várólista API](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) -t használni a függőben lévő üzenetek rendszeres tisztításához a korlát elérésekor.