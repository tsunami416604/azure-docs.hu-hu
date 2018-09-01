---
title: Eszköz séma megoldásban eszköz szimulálása – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, amely meghatározza a szimulált eszköz az eszköz szimulálása megoldásban JSON-sémájában.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338155"
---
# <a name="understand-the-device-model-schema"></a>Az eszközmodell sémájának megismerése

Használhatja az Eszközszimuláció megoldásgyorsító teszt betölti az IoT Hub használó megoldások létrehozásához. Az Eszközszimuláció megoldás üzembe helyezésekor, a rendszer automatikusan biztosít a szimulált eszközök gyűjteménye. A meglévő szimulált eszközök testreszabása, vagy létrehozhatja a sajátját.

Ez a cikk ismerteti az eszközmodell sémájának, amely meghatározza az a funkciók és a egy szimulált eszköz. Az eszköz modellje egy JSON-fájlt tárolja.

A jelen cikkben kapcsolódó a következő cikkeket:

* [Az eszközmodell viselkedésének megvalósítása](iot-accelerators-device-simulation-device-behavior.md) ismerteti a JavaScript-fájlok használatával szimulált eszköz viselkedésének megvalósítása.
* [Új szimulált eszköz létrehozása](iot-accelerators-device-simulation-create-simulated-device.md) minden egy helyen helyezi, és bemutatja, hogyan helyezhet üzembe egy új szimulált eszköz típusa a megoldáshoz.

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

Most, hogy megismerkedett a JSON-séma, a javasolt következő lépésre megtudhatja, hogyan [a szimulált eszköz viselkedésének megvalósítása](iot-accelerators-device-simulation-device-behavior.md).

Az Eszközszimuláció megoldás fejlesztői ismertetését lásd: a [fejlesztői referencia-útmutató](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
