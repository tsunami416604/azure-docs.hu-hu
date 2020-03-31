---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558753"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

A háttéralkalmazások használhatják az Azure IoT Hub primitívjeit, például [az ikereszközt][lnk-devtwin] és [a közvetlen metódusokat][lnk-c2dmethod]az eszközökön végrehajtott eszközkezelési műveletek távoli indításához és figyeléséhez. Ez az oktatóanyag bemutatja, hogyan működhet együtt egy háttéralkalmazás és egy eszközalkalmazás egy távoli eszköz újraindításának kezdeményezése és figyelése az IoT Hub használatával.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Közvetlen módszerrel kezdeményezhet eszközkezelési műveleteket (például újraindítás, gyári alaphelyzetbe állítás és belső vezérlőprogram frissítése) egy háttéralkalmazásból a felhőben. A készülék a következőkért felelős:

* Az IoT Hubról küldött metóduskérés kezelése.

* A megfelelő eszközspecifikus művelet megművelete az eszközön.

* Állapotfrissítések biztosítása *jelentett tulajdonságokon* keresztül az IoT Hub.

A felhőben lévő háttéralkalmazás segítségével ikereszköz-lekérdezéseket futtathat az eszközkezelési műveletek előrehaladásának jelentéséhez.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
