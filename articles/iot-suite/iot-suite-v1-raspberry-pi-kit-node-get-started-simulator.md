---
title: Csatlakozás egy málna Pi Azure IoT Suite Node.js használatával szimulált telemetriai |} Microsoft Docs
description: Használja a Microsoft Azure IoT Starter Kit a Raspberry pi 3 és az Azure IoT Suite. A málna Pi kapcsolódni a távoli felügyeleti megoldás használata Node.js szimulált telemetriai adatokat küldhet a felhőhöz, és a megoldás irányítópultja metódusokra válaszol.
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
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
ms.locfileid: "24011958"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>A málna Pi 3 kapcsolódni a távoli felügyeleti megoldás és a Node.js segítségével szimulált telemetriai adatokat küldhet

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Az oktatóanyag bemutatja, hogyan használható a málna Pi 3 szimulálása a hőmérséklet és a páratartalom a felhőbe küldött adatok. Az oktatóprogram:

- Raspbian operációs rendszer, a Node.js programozási nyelv, és a Microsoft Azure IoT SDK for Node.js egy minta eszköz végrehajtásához.
- Az IoT Suite távoli megfigyelési előre konfigurált megoldás, a felhő alapú háttér.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli felügyeleti megoldás látja el az Azure-előfizetéshez az Azure szolgáltatások. A központi telepítés által adott jelentéseket tükrözik a valós vállalati architektúra. Szükségtelen Azure felhasználási díjak elkerülése az előre konfigurált megoldást a következő azureiotsuite.com a példányának törlése után vele. Ha újra kell az előkonfigurált megoldás, egyszerűen létrehozhatja azt. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és konfigurálja a minta

Most töltse le és konfigurálja a távoli felügyeleti ügyfélalkalmazás a málna Pi.

### <a name="install-nodejs"></a>A Node.js telepítése

Ha még nem tette meg, telepítse a málna Pi Node.js. Az IoT-SDK for Node.js 0.11.5 Node.js vagy újabb verzió szükséges. A következő lépések bemutatják a Node.js v6.10.2 telepíthető a málna Pi:

1. A málna Pi frissítéséhez használja a következő parancsot:

    ```sh
    sudo apt-get update
    ```

1. Az alábbi parancs segítségével töltse le a Node.js bináris fájlok a málna Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. A következő paranccsal telepítse a bináris fájlok:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. A következő paranccsal ellenőrizheti a Node.js v6.10.2 sikeresen telepítette:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>A tárolóhelyekkel klónozása

Ha még nem tette meg, klónozza a szükséges tárházak találhatók a következő parancsok futtatásával parancsot egy terminálban a Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Frissítés az eszköz kapcsolati karakterlánc

Nyissa meg a minta a fájlt a **nano** szerkesztő a következő parancsot:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Keresse meg a sor:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Cserélje le a helyőrző értékeket az eszköz és az IoT-központ adatokat létrehozott és mentett Ez az oktatóanyag elején. Menti a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="run-the-sample"></a>A minta futtatásához

A következő parancsokat a csomagokat a minta telepítéséhez:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

A minta program most futtathatja a málna Pi. Adja meg a parancsot:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

A következő minta kimenet látható egy példa a kimeneti látja a málna Pi a parancssorba:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja le az **Ctrl-C** kilép a programból bármikor.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Következő lépések

Látogasson el a [Azure IoT-fejlesztői központhoz](https://azure.microsoft.com/develop/iot/) további mintákat és Azure IoT-dokumentációja.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
