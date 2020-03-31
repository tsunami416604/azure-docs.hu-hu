---
title: Az Azure IoT Hub 504101 GatewayTimeout-hibájának hibaelhárítása
description: Ismerje meg, hogyan javíthato az 504101 GatewayTimeout hibát
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960671"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Ez a cikk az **504101 GatewayTimeout** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Amikor megpróbál meghívni egy közvetlen metódust az IoT Hubról egy eszközre, a kérelem sikertelen lesz az **504101 GatewayTimeout**hibával.

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1. ok

Az IoT Hub hibát észlelt, és nem tudta megerősíteni, hogy a közvetlen metódus befejeződött-e az időtúllépés előtt.

### <a name="cause-2"></a>2. ok

Az Azure IoT C# SDK (<1.19.0) korábbi verziójának használatakor az eszköz és az IoT Hub közötti AMQP-kapcsolat hiba miatt némán eldobható.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1. megoldás

Próbálja ki újra.

### <a name="solution-2"></a>2. megoldás

Frissítsen az Azure IOT C# SDK legújabb verziójára.