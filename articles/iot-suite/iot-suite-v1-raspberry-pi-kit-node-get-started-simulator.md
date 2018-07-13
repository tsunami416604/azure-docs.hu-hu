---
title: Raspberry Pi csatlakoztatása Azure IoT Suite szimulált telemetriát a Node.js használatával |} A Microsoft Docs
description: A Raspberry pi 3 – a Microsoft Azure IoT-Kezdőcsomag használja, és az Azure IoT Suite. A Node.js használatával a Raspberry Pi kapcsolódni a távoli figyelési megoldásban a szimulált telemetriát küld a felhőbe, és a megoldás irányítópultjáról indított metódusokra válaszol.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: node.js
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 53297049fd36eae3839c6a8146afc336b8f5cd02
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676487"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>A Raspberry Pi 3 csatlakozik a távoli figyelési megoldáshoz, és a Node.js használatával szimulált telemetria küldése

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Ez az oktatóanyag bemutatja, hogyan használhatja a Raspberry Pi 3 szimulálása hőmérséklettel és páratartalommal kapcsolatos adatokat továbbítja a felhőbe. Az oktatóanyag a következőket használja:

- Raspbian operációs rendszer, a Node.js programozási nyelv és a Microsoft Azure IoT SDK for Node.js megvalósítani egy mintául szolgáló eszköz.
- Az IoT Suite előre konfigurált távoli figyelési megoldás háttérrendszerként felhőalapú.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli figyelési megoldás látja el az Azure szolgáltatások az Azure-előfizetésében. Az üzembe helyezés egy valós vállalati architektúra tükrözi. Felesleges Azure-használati díjak elkerüléséhez a az előre konfigurált megoldást a következő azureiotsuite.com példányának törlése után azt. Ha újra kell az előre konfigurált megoldás, egyszerűen létrehozhatja azt. Felhasználás csökkentése a távoli figyelési megoldás futtatása közben kapcsolatos további információkért lásd: [konfigurálása az Azure IoT Suite előre konfigurált megoldások bemutató célokra][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és a minta konfigurálásához

Most töltse le és a távoli figyelési ügyfélalkalmazás konfigurálása a Raspberry Pi-on.

### <a name="install-nodejs"></a>A Node.js telepítése

Ha még nem tette meg, a Node.js telepítéséhez a Raspberry Pi-on. A node.js-hez készült IoT SDK 0.11.5 Node.js vagy újabb verziója szükséges. A következő lépések bemutatják, hogyan telepíthető a Node.js v6.10.2 a Raspberry Pi:

1. A következő paranccsal a Raspberry Pi frissítése:

    ```sh
    sudo apt-get update
    ```

1. A következő paranccsal töltse le a Node.js bináris fájlokat a Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. A következő paranccsal telepítse a bináris fájlok:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. A következő paranccsal ellenőrizze, hogy telepítette a Node.js v6.10.2 sikeresen:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>A tárház klónozása

Ha ezt még nem tette meg, klónozza a szükséges tárházakat a következő parancsok futtatásával parancsot egy terminálban a Pi-on:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Az eszköz kapcsolati karakterlánc frissítése

Nyissa meg a minta a fájlt a **nano** szerkesztő a következő paranccsal:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Keresse meg azt a sort:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Cserélje le a helyőrző értékeket az eszköz és az IoT Hub információk létrehozott és mentett, ez az oktatóanyag elején. Mentse a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="run-the-sample"></a>Minta futtatása

Futtassa a következő parancsokat a minta az előfeltételként szolgáló csomagok telepítéséhez:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

Most futtathatja a mintaprogram a Raspberry Pi-on. Adja meg a parancsot:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Az alábbi kimeneti példa egy példa a kimenetre parancsot a parancssorba a Raspberry Pi-on látja el:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja meg **Ctrl-C** bármikor a program kilép.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>További lépések

Látogasson el a [Azure IoT fejlesztői központ](https://azure.microsoft.com/develop/iot/) további minták és dokumentáció az Azure IOT-n.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
