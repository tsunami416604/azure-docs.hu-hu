---
title: "Csatlakozás egy málna Pi Azure IoT Suite C használatával támogatja a belső vezérlőprogram-frissítésekre |} Microsoft Docs"
description: "Használja a Microsoft Azure IoT Starter Kit a Raspberry pi 3 és az Azure IoT Suite. A málna Pi kapcsolódni a távoli felügyeleti megoldás használatát C telemetriai adatokat küldhet az érzékelők a felhőbe, és hajtsa végre a távoli vezérlőprogram-frissítés."
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
ms.openlocfilehash: 8160752b0116c3ef3e6b6ab7920bb35e471f180b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>A málna Pi 3 kapcsolódni a távoli felügyeleti megoldás, és engedélyezze a távoli belső vezérlőprogram-frissítésekre C használatával

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Az oktatóanyag bemutatja, hogyan használható a Microsoft Azure IoT Starter Kit málna Pi 3:

* Fejlesztés a hőmérséklet és a páratartalom olvasó, amely képes kommunikálni a felhőben.
* Engedélyezi, és végre a távoli belső vezérlőprogram frissítése a frissítés az ügyfélalkalmazás a málna Pi.

Az oktatóprogram:

* Raspbian operációs rendszer, a C programozási nyelv, és a Microsoft Azure IoT SDK C-hez egy minta eszköz végrehajtásához.
* Az IoT Suite távoli megfigyelési előre konfigurált megoldás, a felhő alapú háttér.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban végezze el a következő lépéseket:

* Telepítse a távoli felügyeleti előkonfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatásokhoz.
* Állítsa be az eszköz és a érzékelők kommunikáljanak a számítógép és a távoli felügyeleti megoldás.
* Frissítse a mintakódot eszköz csatlakozni a távoli felügyeleti megoldás, és, amely megtalálható a megoldás irányítópultja telemetriai adatokat küldhet.
* A mintakód eszköz használatával frissítheti az ügyfélalkalmazás.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli felügyeleti megoldás látja el az Azure-előfizetéshez az Azure szolgáltatások. A központi telepítés által adott jelentéseket tükrözik a valós vállalati architektúra. Szükségtelen Azure felhasználási díjak elkerülése az előre konfigurált megoldást a következő azureiotsuite.com a példányának törlése után vele. Ha újra kell az előkonfigurált megoldás, egyszerűen létrehozhatja azt. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Töltse le és konfigurálja a minta

Most töltse le és konfigurálja a távoli felügyeleti ügyfélalkalmazás a málna Pi.

### <a name="clone-the-repositories"></a>A tárolóhelyekkel klónozása

Ha még nem tette meg, klónozza a szükséges adattárak a Pi a következő parancsok futtatásával:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Frissítés az eszköz kapcsolati karakterlánc

Nyissa meg a minta konfigurációs fájlt a **nano** szerkesztő a következő parancsot:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Cserélje le a helyőrző értékeket az ID és az IoT-központ eszközinformáció létrehozott és mentett Ez az oktatóanyag elején.

Amikor elkészült, a deviceinfo információja fájl tartalma az alábbihoz hasonlóan kell kinéznie:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Menti a módosításokat (**Ctrl-O**, **Enter**), és zárja be a szerkesztőt (**Ctrl-X**).

## <a name="build-the-sample"></a>A minta

Ha még nem tette meg, telepítse a csomagokat a Microsoft Azure IoT eszköz SDK C-hez a következő parancsok futtatásával parancsot egy terminálban a málna Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

A megoldást a málna Pi a most már lefordíthatja:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

A minta program most futtathatja a málna Pi. Adja meg a parancsot:

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

A következő minta kimenet látható egy példa a kimeneti látja a málna Pi a parancssorba:

![Raspberry Pi-alkalmazás kimenete][img-raspberry-output]

Nyomja le az **Ctrl-C** kilép a programból bármikor.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. A megoldás irányítópulton kattintson **eszközök** és látogasson el a **eszközök** lap. Válassza ki a Raspberry Pi a a **eszközlista**. Válassza a **módszerek**:

    ![Az irányítópult eszközök][img-list-devices]

1. Az a **metódus meghívása** lapon, válassza ki **InitiateFirmwareUpdate** a a **metódus** legördülő menüből.

1. Az a **FWPackageURI** mezőbe írja be **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Az archívum 2.0-s verziójának a belső vezérlőprogram végrehajtásának tartalmaz.

1. Válasszon **InvokeMethod**. Az alkalmazás a málna Pi nyugtázás visszaküldi a megoldás irányítópultja. Ezután elindítja a belső vezérlőprogram frissítési folyamat által a belső vezérlőprogram az új verzió letöltése:

    ![Módszer előzmények megjelenítése][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Figyelje meg a belső vezérlőprogram frissítése folyamatban

Figyelheti a folyamatot nem lehet frissíteni, hogy az eszközön, és a megoldás irányítópultjának jelentett tulajdonságait megtekintve belső vezérlőprogram:

1. A frissítési folyamat előrehaladását a a málna Pi tekintheti meg:

    ![Megjeleníti a frissítési folyamatot][img-update-progress]

    > [!NOTE]
    > A távoli figyelési alkalmazás csendes újraindul, ha a frissítés befejezése. A paranccsal `ps -ef` ellenőrzése, hogy fut-e. Ha azt szeretné, a folyamat leáll, használja a `kill` parancsot a folyamat azonosítója.

1. Megtekintheti a belső vezérlőprogram-frissítés állapota az eszközt, a megoldás portál által jelentett módon. Az alábbi képernyőfelvételen látható állapotát és a frissítési folyamat, és az új belső vezérlőprogram-verziója minden szakaszhoz időtartama:

    ![Feladat állapotának megjelenítése][img-job-status]

    Ha, lépjen vissza az irányítópulthoz, ellenőrizheti az eszköz továbbra is küld a frissítést követő telemetriai.

> [!WARNING]
> Ha nem adja meg a távoli figyelési megoldást igényelnek fut az Azure-fiókjával, a futtatásakor a kell fizetni. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config]. Ha befejezte, használja az előkonfigurált megoldás törlése az Azure-fiókjával.

## <a name="next-steps"></a>Következő lépések

Látogasson el a [Azure IoT-fejlesztői központhoz](https://azure.microsoft.com/develop/iot/) további mintákat és Azure IoT-dokumentációja.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md