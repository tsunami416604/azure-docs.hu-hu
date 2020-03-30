---
title: Az Azure IoT Hub 404103-as deviceNotOnline-hibája
description: Ismerje meg, hogyan lehet kijavítani a 404103-as DeviceNotOnline hibát
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960814"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Ez a cikk a **404103 DeviceNotOnline-hibák** okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az eszközközvetlen metódusa a **404103 DeviceNotOnline** hibával akkor is sikertelen, ha az eszköz online állapotban van. 

## <a name="cause"></a>Ok

Ha tudja, hogy az eszköz online állapotban van, és továbbra is megkapja a hibát, valószínűleg azért, mert a közvetlen metódus visszahívása nincs regisztrálva az eszközön.

## <a name="solution"></a>Megoldás

Az eszköz közvetlen metódus-visszahívások megfelelő konfigurálásához olvassa el [a Közvetlen metódus kezelése eszközön](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)című témakört.