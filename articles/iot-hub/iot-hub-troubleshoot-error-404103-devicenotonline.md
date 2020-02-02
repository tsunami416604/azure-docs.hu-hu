---
title: Az Azure IoT Hub hibáinak elhárítása 404103 DeviceNotOnline
description: Ismerje meg, hogyan javíthatja a 404103-es hibát a DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960814"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Ez a cikk a **404103 DeviceNotOnline** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az eszköz közvetlen metódusa meghiúsul, ha az eszköz online állapotban van, és a 404103-es **DeviceNotOnline** hiba történik. 

## <a name="cause"></a>Ok

Ha tudja, hogy az eszköz online állapotban van, és továbbra is hibaüzenetet kap, valószínű, hogy a közvetlen metódus visszahívása nincs regisztrálva az eszközön.

## <a name="solution"></a>Megoldás

Az eszköz megfelelő konfigurálásához a közvetlen metódus-visszahívások esetében lásd: [közvetlen metódus kezelése eszközön](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).