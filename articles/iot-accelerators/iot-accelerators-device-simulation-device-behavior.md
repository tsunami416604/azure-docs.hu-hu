---
title: A szimulált eszköz viselkedésének megoldásban eszköz szimulálása – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti az eszköz szimulálása megoldásban a szimulált eszköz viselkedésének megadása a JavaScript használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 43edbc653ddbd55aab5e722071de1f2cf4bcd1c4
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344516"
---
# <a name="implement-the-device-model-behavior"></a>Az eszközmodell viselkedésének megvalósítása

A cikk [az eszközmodell sémájának megismerése](iot-accelerators-device-simulation-device-schema.md) , amely meghatározza egy szimulált eszköz modellje sémáját. A cikkben kétféle típusú JavaScript-fájlt, amely egy szimulált eszköz viselkedésének megvalósítása lehet hivatkozni:

- **Állapot**: JavaScript-fájlok, amelyek a belső állapotot, az eszköz frissítéséhez rögzített időközönként futnak.
- **Módszer**: a JavaScript-fájlok, amelyek futtatását, amikor a megoldás meghív egy metódust az eszközön.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszköz állapota
> * Adja meg, hogyan szimulált eszköz válaszol egy metódust hívhat meg csatlakozik az IoT hubról
> * A parancsfájlok hibakeresése

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a saját egyéni szimulált eszközmodell viselkedésének definiálása. Ez a cikk bemutatta, hogyan való:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszköz állapota
> * Adja meg, hogyan szimulált eszköz válaszol egy metódust hívhat meg csatlakozik az IoT hubról
> * A parancsfájlok hibakeresése

Most, hogy megismerte a szimulált eszköz viselkedésének megadása, a javasolt következő lépésre megtudhatja, hogyan [egy szimulált eszköz létrehozása](iot-accelerators-device-simulation-create-simulated-device.md).

Az Eszközszimuláció megoldás fejlesztői ismertetését lásd: a [fejlesztői referencia-útmutató](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
