---
title: Raspberry Pi webszimulátor csatlakoztatása az Azure IoT Hubhoz (Node.js)
description: A Raspberry Pi webszimulátort a Raspberry Pi-hez való Azure IoT Hubhoz csatlakoztatva adatokat küldhet az Azure-felhőbe.
author: wesmc7777
manager: philmea
keywords: raspberry pi szimulátor, azúrkék iot raspberry pi, raspberry pi iot hub, raspberry pi adatokat küld felhőbe, raspberry pi felhőbe
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: efbe41be6c923f3547df86fd6faeb56bff5e0802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954533"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Raspberry Pi online szimulátor csatlakoztatása az Azure IoT Hubhoz (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban a Raspberry Pi online szimulátorral való munka alapjainak megismerésével kezdheti. Ezután megtudhatja, hogyan csatlakoztathatja zökkenőmentesen a Pi-szimulátort a felhőhöz az [Azure IoT Hub](about-iot-hub.md)használatával.

Ha fizikai eszközökkel rendelkezik, az első lépésekhez keresse fel a [Raspberry Pi csatlakoztatását az Azure IoT Hubhoz.](iot-hub-raspberry-pi-kit-node-get-started.md)

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

## <a name="what-you-do"></a>Mit csinálsz

* Ismerje meg az alapokat a Raspberry Pi online szimulátor.

* Hozzon létre egy IoT hubot.

* Regisztráljon egy eszközt a Pi számára az IoT hubban.

* Futtasson egy mintaalkalmazást a Pi-n szimulált érzékelőadatok ioT hubra küldéséhez.

A szimulált Raspberry Pi csatlakoztatása egy létrehozott IoT-központhoz. Ezután futtasson egy mintaalkalmazást a szimulátorral az érzékelőadatok létrehozásához. Végül elküldi az érzékelő adatait az IoT hubra.

## <a name="what-you-learn"></a>Ismertetett témák

* Azure IoT-központ létrehozása és az új eszközkapcsolati karakterlánc beszerezni. Ha nem rendelkezik Azure-fiókkal, néhány perc alatt [hozzon létre egy ingyenes Azure-próbafiókot.](https://azure.microsoft.com/free/)

* Hogyan működik a Raspberry Pi online szimulátor.

* Az érzékelőadatok küldése az IoT hubra.

## <a name="overview-of-raspberry-pi-web-simulator"></a>A Raspberry Pi webes szimulátor áttekintése

Kattintson a gombra, hogy indítson Raspberry Pi online szimulátor.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Raspberry Pi szimulátor indítása</a>

A webes szimulátorban három terület található.

1. Szerelési terület – Az alapértelmezett áramkör, amelyen a Pi egy BME280 érzékelővel és egy LED-del van összekötve. Ez a terület az előzetes verzióban zárolva van, így jelenleg nem módosítható.

2. Kódolási terület – Online kódszerkesztő, amelyben a Raspberry Pi-hoz írhat kódot. Az alapértelmezett mintaalkalmazás segítségével egy BME280 érzékelő adatait gyűjtheti, amelyeket az Azure IoT Hubba küld tovább. Az alkalmazás teljesen kompatibilis a valódi Pi eszközökkel. 

3. Integrált konzolablak – A kód kimenetét jeleníti meg. Ennek az ablaknak a felső részén három gomb található.

   * **Futtatás** – Futtatja a kódterületen lévő alkalmazást.

   * **Alaphelyzetbe állítás** – Visszaállítja az alapértelmezett mintaalkalmazást a kódolási területre.

   * **Összecsukás/Kibontás** – A jobb szélső gombbal a konzolablak kicsinyíthető le és bontható ki.

> [!NOTE]
> A Raspberry Pi webes szimulátor már elérhető előzetes verzióban. Szeretnénk hallani a hangját a [Gitter Chatroomban.](https://gitter.im/Microsoft/raspberry-pi-web-simulator) A forráskód nyilvános a [GitHubon.](https://github.com/Azure-Samples/raspberry-pi-web-simulator)

![Pi online szimulátor áttekintése](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Mintaalkalmazás futtatása a Pi webes szimulátorán

1. Ügyeljen rá, hogy a kódolási területen az alapértelmezett mintaalkalmazással dolgozik. A 15. sor helyőrzőjét írja át az Azure IoT Hub eszközkapcsolati sztringjére.
1. 
   ![Az eszköz kapcsolati sztringjének cseréje](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Válassza **Run** a `npm start` Futtatás vagy a típus gombot az alkalmazás futtatásához.

A következő kimenetnek kell látnia, amely az érzékelő adatait és ![az IoT hub kimenetére küldött üzeneteket jeleníti meg - a Raspberry Pi-ből az IoT hubra küldött érzékelőadatokat](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>A központ által fogadott üzenetek elolvasása

Az IoT hub által a szimulált eszközről fogadott üzenetek figyelésének egyik módja az Azure IoT-eszközök a Visual Studio-kódhoz használata. További információ: [Az Azure IoT-eszközök használata a Visual Studio-kódhoz az eszköz és az IoT Hub közötti üzenetek küldéséhez és fogadásához.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Az eszköz által küldött adatok feldolgozásának további módjaiért folytassa a következő szakaszsal.

## <a name="next-steps"></a>További lépések

Egy mintaalkalmazást futtatott az érzékelőadatok gyűjtésére és az IoT-központba való elküldésre.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
