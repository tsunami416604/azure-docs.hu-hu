---
title: "Csatlakozás egy málna Pi Azure IoT Suite C használatával szimulált telemetriai |} Microsoft Docs"
description: "Használja a Microsoft Azure IoT Starter Kit a Raspberry pi 3 és az Azure IoT Suite. A málna Pi kapcsolódni a távoli felügyeleti megoldás használatát C szimulált telemetriai adatokat küldhet a felhőhöz, és a megoldás irányítópultja metódusokra válaszol."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 19b16bff7874a03578b8766668c431553da0d875
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-c"></a>A málna Pi 3 kapcsolódni a távoli felügyeleti megoldás és a C használatával szimulált telemetriai adatokat küldhet

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Az oktatóanyag bemutatja, hogyan használható a málna Pi 3 szimulálása a hőmérséklet és a páratartalom a felhőbe küldött adatok. Az oktatóprogram:

- Raspbian operációs rendszer, a C programozási nyelv, és a Microsoft Azure IoT SDK C-hez egy minta eszköz végrehajtásához.
- Az IoT Suite távoli megfigyelési előre konfigurált megoldás, a felhő alapú háttér.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli felügyeleti megoldás látja el az Azure-előfizetéshez az Azure szolgáltatások. A központi telepítés által adott jelentéseket tükrözik a valós vállalati architektúra. Szükségtelen Azure felhasználási díjak elkerülése az előre konfigurált megoldást a következő azureiotsuite.com a példányának törlése után vele. Ha újra kell az előkonfigurált megoldás, egyszerűen létrehozhatja azt. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és konfigurálja a minta

Most töltse le és konfigurálja a távoli felügyeleti ügyfélalkalmazás a málna Pi.

### <a name="clone-the-repositories"></a>A tárolóhelyekkel klónozása

Ha még nem tette meg, klónozza a szükséges tárházak találhatók a következő parancsok futtatásával parancsot egy terminálban a Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Frissítés az eszköz kapcsolati karakterlánc

Nyissa meg a minta a fájlt a **nano** szerkesztő a következő parancsot:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/remote_monitoring/remote_monitoring.c
```

Keresse meg a következő sorokat:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Cserélje le a helyőrző értékeket az eszköz és az IoT-központ adatokat létrehozott és mentett Ez az oktatóanyag elején. Menti a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="build-the-sample"></a>A minta

A telepítés az előfeltétel-csomagokat a Microsoft Azure IoT eszköz SDK számára C a következő parancsok futtatásával parancsot egy terminálban a málna Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

A frissített megoldást a málna Pi a most már lefordíthatja:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
```

A minta program most futtathatja a málna Pi. Adja meg a parancsot:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

A következő minta kimenet látható egy példa a kimeneti látja a málna Pi a parancssorba:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja le az **Ctrl-C** kilép a programból bármikor.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Következő lépések

Látogasson el a [Azure IoT-fejlesztői központhoz](https://azure.microsoft.com/develop/iot/) további mintákat és Azure IoT-dokumentációja.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-simulator/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
