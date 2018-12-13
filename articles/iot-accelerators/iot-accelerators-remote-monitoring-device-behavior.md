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
ms.openlocfilehash: 70f9ccbbe737bad4d6f88365e804d4421c418d28
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164007"
---
# <a name="implement-the-device-model-behavior"></a>Az eszközmodell viselkedésének megvalósítása

A cikk [az eszközmodell sémájának megismerése](iot-accelerators-remote-monitoring-device-schema.md) , amely meghatározza egy szimulált eszköz modellje sémáját. A cikkben kétféle típusú JavaScript-fájlt, amely egy szimulált eszköz viselkedésének megvalósítása lehet hivatkozni:

- **Állapot** JavaScript-fájlok, amelyek a belső állapotot, az eszköz frissítéséhez rögzített időközönként futnak.
- **Módszer** JavaScript-fájlok, amelyek futtatását, amikor a megoldás meghív egy metódust az eszközön.

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
