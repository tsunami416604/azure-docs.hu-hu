---
title: Az Azure IoT Hub 409001-es devicealreadyexists hibájának hibaelhárítása
description: A 409001-es deviceAlreadyExists hiba javításának ismertetése
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960788"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Ez a cikk a **409001 DeviceAlreadyExists** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Amikor megpróbál regisztrálni egy eszközt az IoT Hubban, a kérelem **a 409001 DeviceAlreadyExists**hibával sikertelen.

## <a name="cause"></a>Ok

Már van egy eszköz ugyanazzal az eszközazonosítóval az IoT hubban. 

## <a name="solution"></a>Megoldás

Használjon másik eszközazonosítót, majd próbálkozzon újra.