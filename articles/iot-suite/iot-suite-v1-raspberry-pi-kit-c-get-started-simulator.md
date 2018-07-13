---
title: Raspberry Pi csatlakozhat a szimulált telemetria használata a C Azure IoT Suite |} A Microsoft Docs
description: A Raspberry pi 3 – a Microsoft Azure IoT-Kezdőcsomag használja, és az Azure IoT Suite. Használatát C a Raspberry Pi kapcsolódni a távoli figyelési megoldásban a szimulált telemetriát küld a felhőbe, és a megoldás irányítópultjáról indított metódusokra válaszol.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 19b16bff7874a03578b8766668c431553da0d875
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635608"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-c"></a>A Raspberry Pi 3 csatlakozik a távoli figyelési megoldáshoz, és a C használatával szimulált telemetria küldése

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Ez az oktatóanyag bemutatja, hogyan használhatja a Raspberry Pi 3 szimulálása hőmérséklettel és páratartalommal kapcsolatos adatokat továbbítja a felhőbe. Az oktatóanyag a következőket használja:

- Raspbian operációs rendszer, a C programozási nyelvet és a c nyelvhez készült Microsoft Azure IoT SDK egy mintául szolgáló eszköz megvalósításához.
- Az IoT Suite előre konfigurált távoli figyelési megoldás háttérrendszerként felhőalapú.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli figyelési megoldás látja el az Azure szolgáltatások az Azure-előfizetésében. Az üzembe helyezés egy valós vállalati architektúra tükrözi. Felesleges Azure-használati díjak elkerüléséhez a az előre konfigurált megoldást a következő azureiotsuite.com példányának törlése után azt. Ha újra kell az előre konfigurált megoldás, egyszerűen létrehozhatja azt. Felhasználás csökkentése a távoli figyelési megoldás futtatása közben kapcsolatos további információkért lásd: [konfigurálása az Azure IoT Suite előre konfigurált megoldások bemutató célokra][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és a minta konfigurálásához

Most töltse le és a távoli figyelési ügyfélalkalmazás konfigurálása a Raspberry Pi-on.

### <a name="clone-the-repositories"></a>A tárház klónozása

Ha ezt még nem tette meg, klónozza a szükséges tárházakat a következő parancsok futtatásával parancsot egy terminálban a Pi-on:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Az eszköz kapcsolati karakterlánc frissítése

Nyissa meg a minta a fájlt a **nano** szerkesztő a következő paranccsal:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/remote_monitoring/remote_monitoring.c
```

Keresse meg a következő sorokat:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Cserélje le a helyőrző értékeket az eszköz és az IoT Hub információk létrehozott és mentett, ez az oktatóanyag elején. Mentse a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="build-the-sample"></a>A minta létrehozása

Telepítse az előfeltételként szolgáló csomagok esetében a Microsoft Azure IoT eszközoldali SDK a c nyelvhez készült a következő parancsok futtatásával parancsot egy terminálban a Raspberry Pi-on:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

A frissített mintaalkalmazás megoldást most már lefordíthatja a Raspberry Pi-on:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
```

Most futtathatja a mintaprogram a Raspberry Pi-on. Adja meg a parancsot:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Az alábbi kimeneti példa egy példa a kimenetre parancsot a parancssorba a Raspberry Pi-on látja el:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja meg **Ctrl-C** bármikor a program kilép.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>További lépések

Látogasson el a [Azure IoT fejlesztői központ](https://azure.microsoft.com/develop/iot/) további minták és dokumentáció az Azure IOT-n.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-simulator/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
