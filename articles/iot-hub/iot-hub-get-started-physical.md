---
title: "Ismerkedés az Azure IoT-központ fizikai eszközök csatlakozás |} Microsoft Docs"
description: "Útmutató: Azure IoT-központ fizikai eszközöket és a modulok csatlakozni. Az eszközök küldhet az IoT-központ és az IoT-központ telemetriai figyelheti és az eszközök kezeléséhez."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "az Azure iot hub oktatóanyag"
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: f4128b6b049aa876e170c56dcf2e40720644dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Az Azure IoT-központ fizikai eszközök oktatóanyagok az első lépései

Ezek az oktatóanyagok bemutatják a Azure IoT-központ és az eszköz SDK-k. Az oktatóanyagok általános IoT-forgatókönyvek esetén az IoT-központ funkcióinak bemutatása foglalkozik. Az oktatóanyagok bemutatják, hogyan kombinálhatók az IoT-központ más Azure-szolgáltatások és eszközök nagyobb teljesítményű az IoT-megoldások létrehozásához is. A következő táblázatban található az oktatóanyagok bemutatják a fizikai az IoT-eszközök létrehozásához.

| IoT-eszközök                       | Programozási nyelv |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Az IoT-DevKit                      | [A VSCode Arduino][DevKit]     |
| Intel Edison                    | [NODE.js][Ed_Nd], [C][Ed_C]           |
| Adafruit lágyított HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 dolog fejlesztői      | [Arduino][Th_Ard]              |
| Adafruit lágyított M0             | [Arduino][M0_Ard]              |

Emellett az egy IoT peremhálózati átjáró segítségével engedélyezheti az eszközök csatlakoztatása az IoT hub.

| Átjáróeszköz               | Programozási nyelv | Platform         |
|------------------------------|----------------------|------------------|
| Intel NUC (modell DE3815TYKE) | C#                    | [Szél folyó Linux][NUC_Lnx] |

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
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
