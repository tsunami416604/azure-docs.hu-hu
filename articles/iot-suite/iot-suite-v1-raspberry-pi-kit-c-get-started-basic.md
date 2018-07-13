---
title: Valódi érzékelők használata a C Azure IoT Suite Raspberry Pi csatlakoztatása |} A Microsoft Docs
description: A Raspberry pi 3 – a Microsoft Azure IoT-Kezdőcsomag használja, és az Azure IoT Suite. A Raspberry Pi kapcsolódni a távoli figyelési megoldás használatát C telemetriát küldjön az érzékelők a felhőbe, és a megoldás irányítópultjáról indított metódusokra válaszol.
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
ms.openlocfilehash: 635eb9d4e85eaf43dc83f2bd5a0d0f7c32620d98
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611307"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>A Raspberry Pi 3 csatlakozik a távoli figyelési megoldáshoz, és küldhet telemetriát egy valódi érzékelő C használatával

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Ez az oktatóanyag bemutatja, hogyan használhatja a Microsoft Azure IoT-Kezdőcsomag a Raspberry Pi 3 hőmérséklettel és páratartalommal kapcsolatos olvasó, amely képes kommunikálni a felhőben fejleszthet. Az oktatóanyag a következőket használja:

- Raspbian operációs rendszer, a C programozási nyelvet és a c nyelvhez készült Microsoft Azure IoT SDK egy mintául szolgáló eszköz megvalósításához.
- Az IoT Suite előre konfigurált távoli figyelési megoldás háttérrendszerként felhőalapú.

## <a name="overview"></a>Áttekintés

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

- Helyezze üzembe a távoli figyelési előre konfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatást.
- Állítsa be az eszközök és érzékelők kommunikáljanak a számítógép és a távoli figyelési megoldáshoz.
- Az eszköz mintakód frissítése a távoli figyelési megoldáshoz csatlakozhat, és telemetriát is megtekintheti a megoldás irányítópultján.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli figyelési megoldás látja el az Azure szolgáltatások az Azure-előfizetésében. Az üzembe helyezés egy valós vállalati architektúra tükrözi. Felesleges Azure-használati díjak elkerüléséhez a az előre konfigurált megoldást a következő azureiotsuite.com példányának törlése után azt. Ha újra kell az előre konfigurált megoldás, egyszerűen létrehozhatja azt. Felhasználás csökkentése a távoli figyelési megoldás futtatása közben kapcsolatos további információkért lásd: [konfigurálása az Azure IoT Suite előre konfigurált megoldások bemutató célokra][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és a minta konfigurálásához

Most töltse le és a távoli figyelési ügyfélalkalmazás konfigurálása a Raspberry Pi-on.

### <a name="clone-the-repositories"></a>A tárház klónozása

Ha ezt még nem tette meg, klónozza a szükséges tárházakat a következő parancsok futtatásával parancsot egy terminálban a Pi-on:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>Az eszköz kapcsolati karakterlánc frissítése

Nyissa meg a minta a fájlt a **nano** szerkesztő a következő paranccsal:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
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
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

Most futtathatja a mintaprogram a Raspberry Pi-on. Adja meg a parancsot:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Az alábbi kimeneti példa egy példa a kimenetre parancsot a parancssorba a Raspberry Pi-on látja el:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja meg **Ctrl-C** bármikor a program kilép.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>További lépések

Látogasson el a [Azure IoT fejlesztői központ](https://azure.microsoft.com/develop/iot/) további minták és dokumentáció az Azure IOT-n.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
