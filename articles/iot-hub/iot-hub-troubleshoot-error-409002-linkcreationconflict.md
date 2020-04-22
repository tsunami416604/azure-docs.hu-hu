---
title: Az Azure IoT Hub 409002-es linkcreationconflict hibája
description: A 409002-es LinkCreationConflict hiba javítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758734"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Ez a cikk a **409002 LinkCreationConflict** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Megjelenik a **409002-es LinkCreationConflict** hiba a diagnosztikai naplókban, valamint az eszköz leválasztása vagy a felhőből az eszközbe irányuló üzenethiba. 

<!-- When using AMQP? -->

## <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha az IoT Hub azt észleli, hogy egy ügyfél egynél több kapcsolattal rendelkezik. Valójában, amikor egy új kapcsolatkérelem érkezik egy meglévő kapcsolattal rendelkező eszközhöz, az IoT Hub ezzel a hibával bezárja a meglévő kapcsolatot.

### <a name="cause-1"></a>1. ok

A leggyakoribb esetben egy külön probléma (például [404104 DeviceConnectionClosedRemotely)](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)okozza az eszköz leválasztását. Az eszköz megpróbálja azonnal újra létrehozni a kapcsolatot, de az IoT Hub továbbra is úgy véli, hogy az eszköz csatlakoztatva van. Az IoT Hub bezárja az előző kapcsolatot, és naplózza ezt a hibát.

### <a name="cause-2"></a>2. ok

A hibás eszközoldali logika hatására az eszköz létrehozza a kapcsolatot, ha az egyik már meg van nyitva.

## <a name="solution"></a>Megoldás

Ez a hiba általában egy másik, átmeneti probléma mellékhatásaként jelenik meg, ezért keressen más hibákat a naplókban a további hibaelhárításhoz. Ellenkező esetben csak akkor adjon ki új csatlakozási kérelmet, ha a kapcsolat megszakad.
