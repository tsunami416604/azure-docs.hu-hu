---
title: Az Azure IoT Hub - első lépések az IoT-eszközök felhőhöz történő csatlakoztatásának |} Microsoft Docs
description: 'Útmutató: Azure IoT-központ az IoT-modulok és a starter Kit csatlakozni. Az eszközök küldhet az IoT-központ és az IoT-központ telemetriai figyelheti és az eszközök kezeléséhez.'
author: dominicbetts
manager: timlt
keywords: az Azure iot hub oktatóanyag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634702"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Az Azure IoT Hub valós eszközök az első lépései

Azure IoT-központ és az Azure IoT-eszközök SDK-k segítségével az eszközök internetes hálózatát (IoT) megoldások:

* Az Azure IoT-központot egy olyan teljes körűen felügyelt szolgáltatás a felhőben, amelyek biztonságosan csatlakozik, figyeli, és kezeli az IoT-eszközök. Az Azure IoT eszközoldali SDK-k segítségével valósítja meg az IoT-eszközök.
* Az IoT-átjáró összetettebb IoT-forgatókönyvek esetén használható. Például, ha szeretné például az örökölt eszközök, a sávszélességgel kapcsolatos költségek, a biztonsági és adatvédelmi szabályzatok vagy a biztonsági adatok feldolgozása tényezőket kell figyelembe venni. Ezekben az esetekben használható [Azure IoT peremhálózati](https://docs.microsoft.com/azure/iot-edge/) egy átjáró, amely az eszköz csatlakozik az IoT hub végrehajtásához.

## <a name="what-the-how-to-articles-cover"></a>Az útmutatók vonatkozik

Ezek a cikkek bemutatják a Azure IoT-központ és az eszköz SDK-k. A cikk foglalkozik általános IoT-forgatókönyvek esetén az IoT-központ funkcióinak bemutatása. A cikkek is bemutatják, hogyan kombinálhatók az IoT-központ más Azure-szolgáltatások és eszközök nagyobb teljesítményű az IoT-megoldások létrehozásához. A cikkekben használja valódi az IoT-eszközök.

## <a name="set-up-your-device"></a>Az eszköz beállítása

Csatlakozás az IoT-eszközön vagy Azure IoT Hub-átjáró:

| IoT-eszközök                       | Programozási nyelv |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Az IoT-DevKit                       | [A VSCode Arduino][DevKit]     |
| Intel Edison                     | [NODE.js][Ed_Nd], [C][Ed_C]    |
| Adafruit lágyított HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 dolog fejlesztői       | [Arduino][Th_Ard]              |
| Adafruit lágyított M0              | [Arduino][M0_Ard]              |
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
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
