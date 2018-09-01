---
title: Eszköz-sémát a távoli figyelési megoldás – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, amely meghatározza a távoli figyelési megoldásban a szimulált eszköz JSON-sémájában.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: f312f29e14c371e7b500f3eee6471151e3544513
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338855"
---
# <a name="understand-the-device-model-schema"></a>Az eszközmodell sémájának megismerése

A távoli figyelési megoldásban a szimulált eszközök segítségével tesztelheti működését. A távoli figyelési megoldás üzembe helyezésekor, a rendszer automatikusan biztosít a szimulált eszközök gyűjteménye. A meglévő szimulált eszközök testreszabása, vagy létrehozhatja a sajátját.

Ez a cikk ismerteti az eszközmodell sémájának, amely meghatározza az a funkciók és a egy szimulált eszköz. Az eszköz modellje egy JSON-fájlt tárolja.

A jelen cikkben kapcsolódó a következő cikkeket:

* [Az eszközmodell viselkedésének megvalósítása](iot-accelerators-remote-monitoring-device-behavior.md) ismerteti a JavaScript-fájlok használatával szimulált eszköz viselkedésének megvalósítása.
* [Új szimulált eszköz létrehozása](iot-accelerators-remote-monitoring-create-simulated-device.md) minden egy helyen helyezi, és bemutatja, hogyan helyezhet üzembe egy új szimulált eszköz típusa a megoldáshoz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Használjon egy JSON-fájlt egy szimulált eszköz modell
> * Adja meg a tulajdonságokat a szimulált eszköz
> * Adja meg a telemetriát küld a szimulált eszköz
> * Adja meg a felhőből az eszközre módszerek az eszköz válaszol

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan hozhat létre saját egyéni szimulált eszköz modellje. Ez a cikk bemutatta, hogyan való:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Használjon egy JSON-fájlt egy szimulált eszköz modell
> * Adja meg a tulajdonságokat a szimulált eszköz
> * Adja meg a telemetriát küld a szimulált eszköz
> * Adja meg a felhőből az eszközre módszerek az eszköz válaszol

Most, hogy megismerkedett a JSON-séma, a javasolt következő lépésre megtudhatja, hogyan [a szimulált eszköz viselkedésének megvalósítása](iot-accelerators-remote-monitoring-device-behavior.md).

A távoli figyelési megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
