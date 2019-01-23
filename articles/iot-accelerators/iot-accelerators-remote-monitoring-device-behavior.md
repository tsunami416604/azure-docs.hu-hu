---
title: A szimulált eszköz viselkedésének a távoli figyelési megoldás – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a távoli figyelési megoldásban a szimulált eszköz viselkedésének megadása a JavaScript használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c2151a4b1eb2a853ed343f6720b4f53af5e5e449
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449520"
---
# <a name="implement-the-device-model-behavior"></a>Az eszközmodell viselkedésének megvalósítása

A cikk [az eszközmodell sémájának megismerése](iot-accelerators-remote-monitoring-device-schema.md) , amely meghatározza egy szimulált eszköz modellje sémáját. A cikkben kétféle típusú JavaScript-fájlt, amely egy szimulált eszköz viselkedésének megvalósítása lehet hivatkozni:

- **Állapot** JavaScript-fájlok, amelyek a belső állapotot, az eszköz frissítéséhez rögzített időközönként futnak.
- **Módszer** JavaScript-fájlok, amelyek futtatását, amikor a megoldás meghív egy metódust az eszközön.

> [!NOTE]
> Modell eszközműködés csak az eszköz szimulálása szolgáltatásban üzemeltetett szimulált eszközök vonatkoznak. Ha szeretne létrehozni egy igazi eszközön, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító](iot-accelerators-connecting-devices.md).

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszköz állapota
> * Szimulált eszköz reakciója egy metódust hívhat meg, a távoli figyelési megoldás meghatározása
> * A parancsfájlok hibakeresése

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a saját egyéni szimulált eszközmodell viselkedésének definiálása. Ez a cikk bemutatta, hogyan való:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszköz állapota
> * Szimulált eszköz reakciója egy metódust hívhat meg, a távoli figyelési megoldás meghatározása
> * A parancsfájlok hibakeresése

Most, hogy megismerte a szimulált eszköz viselkedésének megadása, a javasolt következő lépésre megtudhatja, hogyan [egy szimulált eszköz létrehozása](iot-accelerators-remote-monitoring-create-simulated-device.md).

A távoli figyelési megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
