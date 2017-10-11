---
title: "Csatlakozás egy málna Pi Azure IoT Suite Node.js használatával valós érzékelők |} Microsoft Docs"
description: "Használja a Microsoft Azure IoT Starter Kit a Raspberry pi 3 és az Azure IoT Suite. A málna Pi kapcsolódni a távoli felügyeleti megoldás használata Node.js telemetriai adatokat küldhet az érzékelők a felhőhöz, és a megoldás irányítópultja metódusokra válaszol."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 91546157cc8eabf68706391ce706038d8dc5f82d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>A málna Pi 3 kapcsolódni a távoli felügyeleti megoldás és telemetriai adatokat küldhet egy Node.js segítségével valós érzékelő

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

Az oktatóanyag bemutatja, hogyan használhatja a Microsoft Azure IoT Starter Kit málna Pi 3 fejlesztéséhez a hőmérséklet és a páratartalom olvasó, amely képes kommunikálni a felhőben. Az oktatóprogram:

- Raspbian operációs rendszer, a Node.js programozási nyelv, és a Microsoft Azure IoT SDK for Node.js egy minta eszköz végrehajtásához.
- Az IoT Suite távoli megfigyelési előre konfigurált megoldás, a felhő alapú háttér.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban végezze el a következő lépéseket:

- Telepítse a távoli felügyeleti előkonfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatásokhoz.
- Állítsa be az eszköz és a érzékelők kommunikáljanak a számítógép és a távoli felügyeleti megoldás.
- Frissítse a mintakódot eszköz csatlakozni a távoli felügyeleti megoldás, és, amely megtalálható a megoldás irányítópultja telemetriai adatokat küldhet.

[!INCLUDE [iot-suite-raspberry-pi-kit-prerequisites](../../includes/iot-suite-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli felügyeleti megoldás látja el az Azure-előfizetéshez az Azure szolgáltatások. A központi telepítés által adott jelentéseket tükrözik a valós vállalati architektúra. Szükségtelen Azure felhasználási díjak elkerülése az előre konfigurált megoldást a következő azureiotsuite.com a példányának törlése után vele. Ha újra kell az előkonfigurált megoldás, egyszerűen létrehozhatja azt. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config].

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és konfigurálja a minta

Most töltse le és konfigurálja a távoli felügyeleti ügyfélalkalmazás a málna Pi.

### <a name="install-nodejs"></a>A Node.js telepítése

Node.js telepíthető a Raspberry Pi. Az IoT-SDK for Node.js 0.11.5 Node.js vagy újabb verzió szükséges. A következő lépések bemutatják a Node.js v6.10.2 telepíthető a málna Pi:

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

Ha még nem tette meg, klónozza a szükséges adattárak a Pi a következő parancsok futtatásával:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`
```

### <a name="update-the-device-connection-string"></a>Frissítés az eszköz kapcsolati karakterlánc

Nyissa meg a minta a fájlt a **nano** szerkesztő a következő parancsot:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Keresse meg a sor:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Cserélje le a helyőrző értékeket az eszköz és az IoT-központ adatokat létrehozott és mentett Ez az oktatóanyag elején. Menti a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="run-the-sample"></a>A minta futtatásához

A következő parancsokat a csomagokat a minta telepítéséhez:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic
npm install
```

A minta program most futtathatja a málna Pi. Adja meg a parancsot:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

A következő minta kimenet látható egy példa a kimeneti látja a málna Pi a parancssorba:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja le az **Ctrl-C** kilép a programból bármikor.

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Következő lépések

Látogasson el a [Azure IoT-fejlesztői központhoz](https://azure.microsoft.com/develop/iot/) további mintákat és Azure IoT-dokumentációja.

[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
