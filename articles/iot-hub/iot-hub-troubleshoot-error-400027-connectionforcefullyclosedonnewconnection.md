---
title: Az Azure IoT Hub hibáinak elhárítása 400027 ConnectionForcefullyClosedOnNewConnection
description: Ismerje meg, hogyan javíthatja a 400027-es hibát a ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960528"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Ez a cikk a **400027 ConnectionForcefullyClosedOnNewConnection** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az eszközről a felhőbe irányuló kettős művelet (például a Read vagy a patch jelentett tulajdonságai) vagy a közvetlen metódus meghívása meghiúsul a **400027**hibakód esetén.

## <a name="cause"></a>Ok

Egy másik ügyfél létrehozott egy új kapcsolódást IoT Hub ugyanazzal a hitelesítő adatokkal, így IoT Hub lezárta az előző kapcsolódást. IoT Hub nem engedélyezi, hogy egynél több ügyfél csatlakozhasson ugyanazzal a hitelesítő adatokkal.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy minden ügyfél a saját identitásával csatlakozik a IoT Hubhoz.