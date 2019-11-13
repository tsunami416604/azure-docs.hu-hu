---
title: A málna PI webszimulátor összekötése az Azure IoT Hub (node. js)
description: A málna PI webszimulátor összekapcsolásával Azure-IoT Hub a málna PI-hoz az Azure-felhőbe való adatküldéshez.
author: wesmc7777
manager: philmea
keywords: raspberry pi simulator, azure iot raspberry pi, raspberry pi iot hub, raspberry pi send data to cloud, raspberry pi to cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954533"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>A málna PI online szimulátor összekötése az Azure IoT Hub (node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban elkezdi megtanulni a málna PI online szimulátor használatának alapjait. Ezután megtudhatja, hogyan csatlakoztatható zökkenőmentesen a PI-szimulátor a felhőhöz az [Azure IoT hub](about-iot-hub.md)használatával.

Ha fizikai eszközökkel rendelkezik, a kezdéshez látogasson el a [málna PI csatlakoztatása az Azure IoT hubhoz](iot-hub-raspberry-pi-kit-node-get-started.md) című oldalon.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Teendők

* Ismerkedjen meg a málna PI online szimulátor alapjaival.

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a PI-hez az IoT hub-ban.

* Futtasson egy minta alkalmazást a PI-ben, hogy szimulált érzékelői adatait küldjön az IoT hub-nak.

Szimulált málna PI összekötése egy Ön által létrehozott IoT hubhoz. Ezután futtasson egy minta alkalmazást a szimulátor használatával az érzékelők létrehozásához. Végül elküldi az érzékelő adatait az IoT hubhoz.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT hub létrehozása és az új eszköz-kapcsolódási karakterlánc beszerzése. Ha nem rendelkezik Azure-fiókkal, mindössze néhány perc alatt [hozzon létre egy ingyenes Azure próbaverziós fiókot](https://azure.microsoft.com/free/) .

* A málna PI online szimulátor használata.

* Szenzorok adatainak küldése az IoT hubhoz.

## <a name="overview-of-raspberry-pi-web-simulator"></a>A málna PI web Simulator áttekintése

A gombra kattintva elindíthatja a málna PI online szimulátort.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">A málna PI szimulátor elindítása</a>

A webes szimulátorban három terület található.

1. Összeállítási térség – az alapértelmezett áramkör, hogy a PI egy BME280-érzékelővel és egy LED-sel csatlakozik. A terület az előzetes verzióban zárolva van, ezért jelenleg nem végezhető el a Testreszabás.

2. Kódolási területek – egy online Kódszerkesztő, amely a málna PI-vel való kódolást teszi elérhetővé. Az alapértelmezett minta alkalmazás segít összegyűjteni az érzékelők adatait a BME280-érzékelőből, és elküldi az Azure-IoT Hub. Az alkalmazás teljes mértékben kompatibilis a valódi PI-eszközökkel. 

3. Integrált konzolablak-ablak – a kód kimenetét jeleníti meg. Az ablak tetején három gomb található.

   * **Futtassa** az alkalmazást a kódolási csoportban.

   * **Alaphelyzetbe** állítás – állítsa vissza a kódolási területeket az alapértelmezett minta alkalmazásba.

   * **Kidobás/Kibontás** – a jobb oldalon van egy gomb a konzol ablakának kidobásához/kibontásához.

> [!NOTE]
> A málna PI webes szimulátor már előzetes verzióban érhető el. Szeretnénk hallani a hangját a [Gitter chaten](https://gitter.im/Microsoft/raspberry-pi-web-simulator). A forráskód nyilvános a [githubon](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![A PI online Simulator áttekintése](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Minta alkalmazás futtatása a PI web Simulator-on

1. A kódolási területen ellenőrizze, hogy az alapértelmezett minta alkalmazáson dolgozik-e. Cserélje le a helyőrzőt a 15. sorban az Azure IoT hub-eszköz kapcsolatok karakterláncára.
1. 
   ![Az eszköz-kapcsolatok karakterláncának cseréje](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Az alkalmazás futtatásához válassza a **Futtatás** lehetőséget, vagy írja be a `npm start`.

A következő kimenetnek kell megjelennie, amely megjeleníti az érzékelő adatait és az IoT hub számára küldött üzeneteket, ![a málna PI-ról az IoT hub-ra küldött kimeneti érzékelő adatait](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>A hub által fogadott üzenetek olvasása

Az IoT hub által a szimulált eszközről fogadott üzenetek figyelésének egyik módja a Visual Studio Code-hoz készült Azure IoT Tools használata. További információ: az [Azure IoT Tools for Visual Studio Code használata üzenetek küldéséhez és fogadásához az eszköz és a IoT hub között](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Az eszköz által elküldhető adatfeldolgozás további módjairól folytassa a következő szakasszal.

## <a name="next-steps"></a>Következő lépések

Egy minta alkalmazást futtatott az érzékelő adatainak összegyűjtéséhez és az IoT hubhoz való elküldéséhez.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
