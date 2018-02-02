---
title: "Az Azure IoT Hub - első lépések az IoT-eszközök felhőhöz történő csatlakoztatásának |} Microsoft Docs"
description: "Útmutató: Azure IoT-központ az IoT-modulok és a starter Kit csatlakozni. Az eszközök küldhet az IoT-központ és az IoT-központ telemetriai figyelheti és az eszközök kezeléséhez."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "az Azure iot hub oktatóanyag"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 34742208e9189eb31310b58770ee4a22e33f56d5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Az Azure IoT Hub get oktatóanyagok indítása

Azure IoT-központ és az Azure IoT-eszközök SDK-k segítségével az eszközök internetes hálózatát (IoT) megoldások:

* Az Azure IoT-központot egy olyan teljes körűen felügyelt szolgáltatás a felhőben, amelyek biztonságosan csatlakozik, figyeli, és kezeli az IoT-eszközök. Az Azure IoT eszközoldali SDK-k segítségével valósítja meg az IoT-eszközök.
* Az IoT-átjáró összetettebb IoT-forgatókönyvek esetén használható. Például, ha szeretné például az örökölt eszközök, a sávszélességgel kapcsolatos költségek, a biztonsági és adatvédelmi szabályzatok vagy a biztonsági adatok feldolgozása tényezőket kell figyelembe venni. Ezekben az esetekben használható [Azure IoT peremhálózati](https://docs.microsoft.com/azure/iot-edge/) egy átjáró, amely az eszköz csatlakozik az IoT hub végrehajtásához.

## <a name="what-the-tutorials-cover"></a>Az oktatóanyagok vonatkozik

Ezek az oktatóanyagok bemutatják a Azure IoT-központ és az eszköz SDK-k. Az oktatóanyagok általános IoT-forgatókönyvek esetén az IoT-központ funkcióinak bemutatása foglalkozik. Az oktatóanyagok bemutatják, hogyan kombinálhatók az IoT-központ más Azure-szolgáltatások és eszközök nagyobb teljesítményű az IoT-megoldások létrehozásához is. Az oktatóanyagok választhatja ki, vagy a szimulált, vagy a valós az IoT-eszközök használatához. Továbbá megismerheti az átjáró használatával engedélyezhető az eszközök csatlakoztatása az IoT hub.

## <a name="set-up-your-device"></a>Az eszköz beállítása

Csatlakoztassa az IoT-eszközön vagy Azure IoT Hub-átjárót. Első lépésként szimulált vagy fizikai eszköz közül választhat:

| IoT-eszközök                       | Programozási nyelv |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Az IoT-DevKit                       | [A VSCode Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit lágyított HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 dolog fejlesztői       | [Arduino][Th_Ard]              |
| Adafruit lágyított M0              | [Arduino][M0_Ard]              |
| Szimulált eszköz számítógépen           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
| Online eszköz szimulátor         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
