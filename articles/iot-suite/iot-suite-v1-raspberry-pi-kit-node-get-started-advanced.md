---
title: Raspberry Pi csatlakoztatása Azure IoT Suite támogatásához a lemezfirmware-frissítések a Node.js használatával |} A Microsoft Docs
description: A Raspberry pi 3 – a Microsoft Azure IoT-Kezdőcsomag használja, és az Azure IoT Suite. A Node.js használatával a Raspberry Pi csatlakoztatása a távoli figyelési megoldáshoz telemetriát küldjön az érzékelők a felhőbe, és a egy távoli belső vezérlőprogramjának frissítéséhez.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309591"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>A Raspberry Pi 3 csatlakozik a távoli figyelési megoldáshoz, és a Node.js használatával távoli belső vezérlőprogram frissítésének engedélyezése

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Ez az oktatóanyag bemutatja, hogyan használható a Microsoft Azure IoT-Kezdőcsomag Raspberry Pi 3:

* Egy hőmérsékleti és páratartalom-olvasó, amely képes kommunikálni a felhőben fejleszthet.
* Engedélyezze, és a egy távoli belső vezérlőprogram frissítése a frissítést végez az ügyfélalkalmazás a Raspberry Pi-on.

Az oktatóanyag a következőket használja:

- Raspbian operációs rendszer, a Node.js programozási nyelv és a Microsoft Azure IoT SDK for Node.js megvalósítani egy mintául szolgáló eszköz.
- Az IoT Suite előre konfigurált távoli figyelési megoldás háttérrendszerként felhőalapú.

## <a name="overview"></a>Áttekintés

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

- Helyezze üzembe a távoli figyelési előre konfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatást.
- Állítsa be az eszközök és érzékelők kommunikáljanak a számítógép és a távoli figyelési megoldáshoz.
- Az eszköz mintakód frissítése a távoli figyelési megoldáshoz csatlakozhat, és telemetriát is megtekintheti a megoldás irányítópultján.
- A mintakód eszköz használatával az ügyfélalkalmazás frissítése.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli figyelési megoldás látja el az Azure szolgáltatások az Azure-előfizetésében. Az üzembe helyezés egy valós vállalati architektúra tükrözi. Felesleges Azure-használati díjak elkerüléséhez a az előre konfigurált megoldást a következő azureiotsuite.com példányának törlése után azt. Ha újra kell az előre konfigurált megoldás, egyszerűen létrehozhatja azt. Felhasználás csökkentése a távoli figyelési megoldás futtatása közben kapcsolatos további információkért lásd: [konfigurálása az Azure IoT Suite előre konfigurált megoldások bemutató célokra][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

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

Ha még nem tette meg, a Pi-on a következő parancsok futtatásával klónozza a a szükséges tárházakat:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Az eszköz kapcsolati karakterlánc frissítése

Nyissa meg a minta konfigurációs fájlt a **nano** szerkesztő a következő paranccsal:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Cserélje le a helyőrző értékeket az eszközazonosítót és a létrehozott és mentett elején. Ez az oktatóanyag az IoT Hub információk.

Amikor elkészült, a deviceinfo fájl tartalmát a következő példához hasonlóan kell kinéznie:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Mentse a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="run-the-sample"></a>Minta futtatása

Futtassa a következő parancsokat a minta az előfeltételként szolgáló csomagok telepítéséhez:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Most futtathatja a mintaprogram a Raspberry Pi-on. Adja meg a parancsot:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

Az alábbi kimeneti példa egy példa a kimenetre parancsot a parancssorba a Raspberry Pi-on látja el:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja meg **Ctrl-C** bármikor a program kilép.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. A megoldás irányítópultján kattintson **eszközök** használatával keresse fel a **eszközök** lapot. Válassza ki a Raspberry Pi a a **eszközlista**. Válassza a **módszerek**:

    ![Az irányítópult eszközök][img-list-devices]

1. Az a **metódus meghívása** lapon a **InitiateFirmwareUpdate** a a **metódus** legördülő listából.

1. Az a **FWPackageURI** írja be a következőt **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Ez a fájl tartalmazza a belső vezérlőprogram 2.0-s verziójához megvalósítását.

1. Válasszon **InvokeMethod**. Az alkalmazás a Raspberry Pi-on visszajelzést küld a megoldás irányítópultján. Ezután elindítja a belső vezérlőprogram frissítési folyamata töltse le a belső vezérlőprogram új verziója:

    ![Metódus előzményeinek megjelenítése][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Figyelje meg a belső vezérlőprogram frissítése folyamatban

Figyelheti, hogy a belső vezérlőprogram frissítése folyamat futtatása az eszközön, és a jelentett tulajdonságok megtekintésével a megoldás irányítópultján:

1. A Raspberry Pi-on a frissítési folyamat állapotát a tekintheti meg:

    ![Frissítés előrehaladásának megjelenítése][img-update-progress]

    > [!NOTE]
    > A távoli figyelési alkalmazás csendes módban újraindul a frissítés befejezése után. A parancs használata `ps -ef` ellenőrizze, hogy fut-e. Ha azt szeretné, a folyamat leáll, használja a `kill` folyamatazonosítója parancsot.

1. Megtekintheti a belső vezérlőprogram frissítésének állapotát, a megoldás portálján, az eszköz által jelentett módon. A következő képernyőképen az látható, az állapot és a frissítési folyamat, és az új belső vezérlőprogram verziója minden egyes fázisa időtartama:

    ![Feladat állapotának megjelenítése][img-job-status]

    Ha, lépjen vissza az irányítópultra, ellenőrizheti az eszköz továbbra is a belső vezérlőprogram frissítése a következő telemetriai adatokat küldenek.

> [!WARNING]
> Ha a távoli figyelési megoldás az Azure-fiókban futó hagyja, a futtatásakor számlázzuk ki. Felhasználás csökkentése a távoli figyelési megoldás futtatása közben kapcsolatos további információkért lásd: [konfigurálása az Azure IoT Suite előre konfigurált megoldások bemutató célokra][lnk-demo-config]. Ha befejezte, használja azt törölje az előre konfigurált megoldás az Azure-fiókjával.

## <a name="next-steps"></a>További lépések

Látogasson el a [Azure IoT fejlesztői központ](https://azure.microsoft.com/develop/iot/) további minták és dokumentáció az Azure IOT-n.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
