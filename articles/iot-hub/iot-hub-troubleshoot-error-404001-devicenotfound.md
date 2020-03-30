---
title: 'Az Azure IoT Hub 404001-es hibájának hibaelhárítása: DeviceNotFound'
description: Ismerje meg, hogyan lehet kijavítani a 404001-es deviceNotFound hibát
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960827"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Ez a cikk a **404001 DeviceNotFound** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

A felhőből az eszközre (C2D) irányuló kommunikáció során, például C2D üzenet, ikerfrissítés vagy közvetlen metódus során a művelet **a 404001-es DeviceNotFound**hibával sikertelen.

## <a name="cause"></a>Ok

A művelet nem sikerült, mert az eszközt az IoT Hub nem találja. Az eszköz nincs regisztrálva vagy le van tiltva.

## <a name="solution"></a>Megoldás

Regisztrálja a használt eszközazonosítót, majd próbálkozzon újra.