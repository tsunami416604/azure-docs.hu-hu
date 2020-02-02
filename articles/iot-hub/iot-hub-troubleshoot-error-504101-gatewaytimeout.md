---
title: Az Azure IoT Hub hibáinak elhárítása 504101 GatewayTimeout
description: Ismerje meg, hogyan javíthatja a 504101-es hibát a GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960671"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Ez a cikk a **504101 GatewayTimeout** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Amikor egy közvetlen metódust próbál meg IoT Hubról egy eszközre meghívni, a kérelem a **504101 GatewayTimeout**hibával meghiúsul.

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1\. ok

IoT Hub hibát észlelt, és nem tudta megerősíteni, hogy a közvetlen metódus befejeződött-e az időtúllépés előtt.

### <a name="cause-2"></a>2\. ok

Az Azure IoT C# SDK (< 1.19.0) korábbi verziójának használatakor az eszköz és a IOT hub közötti AMQP-kapcsolat egy hiba miatt csendesan elhagyható.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1\. megoldás

Próbálkozzon újra.

### <a name="solution-2"></a>2\. megoldás

Frissítsen az Azure IOT C# SDK legújabb verziójára.