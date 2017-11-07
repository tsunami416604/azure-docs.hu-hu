---
title: "Csatlakozás Azure IoT Suite használatával az Intel NUC átjáró |} Microsoft Docs"
description: "A Microsoft IoT kereskedelmi átjáró Kit és a távoli figyelési előkonfigurált megoldást használni. Az Azure IoT peremhálózati átjáró használatával kapcsolódni a távoli felügyeleti megoldás, telemetriai adatokat küldhet a felhőhöz, és a megoldás irányítópultja metódusokra megválaszolásához SensorTag eszköz engedélyezése."
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
ms.openlocfilehash: 85b9ed0000c8c5ff2c7d6cc8fa4a051e0b27d6c2
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Csatlakozzon a távoli figyelési előkonfigurált megoldást az Azure IoT peremhálózati átjárót, és telemetriai adatokat küldhet egy SensorTag a

[!INCLUDE [iot-suite-v1-gateway-kit-selector](../../includes/iot-suite-v1-gateway-kit-selector.md)]

Az oktatóanyag bemutatja, hogyan használható Azure IoT biztonsági adatokat küldeni a hőmérséklet és a páratartalom SensorTag eszközről a távoli felügyeleti előkonfigurált megoldás. A SensorTag kapcsolódik az Intel NUC Bluetooth átjárót. Az oktatóprogram:

- Az Azure IoT peremhálózati minta átjáró végrehajtásához.
- Az IoT Suite távoli megfigyelési előre konfigurált megoldás, a felhő alapú háttér.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban végezze el a következő lépéseket:

- Telepítse a távoli felügyeleti előkonfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatásokhoz.
- Az Intel NUC átjáróeszköz beállítása a számítógép és a távoli felügyeleti megoldás folytatott kommunikációhoz.
- Az Intel NUC átjáró telemetriai adatok fogadhatók az SensorTag eszközökről, és küldje el a távoli figyelési irányítópult beállítását.

[!INCLUDE [iot-suite-v1-gateway-kit-prerequisites](../../includes/iot-suite-v1-gateway-kit-prerequisites.md)]

[Texas eszközök BLA SensorTag][lnk-sensortag]. Ez az oktatóanyag telemetriai adatokat lekérdezi a SensorTag eszköz Bluetooth-on keresztül.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli felügyeleti megoldás látja el az Azure-előfizetéshez az Azure szolgáltatások. A központi telepítés által adott jelentéseket tükrözik a valós vállalati architektúra. Szükségtelen Azure felhasználási díjak elkerülése az előre konfigurált megoldást a következő azureiotsuite.com a példányának törlése után vele. Ha újra kell az előkonfigurált megoldás, egyszerűen létrehozhatja azt. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config].

[!INCLUDE [iot-suite-v1-gateway-kit-view-solution](../../includes/iot-suite-v1-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Bluetooth-kapcsolat konfigurálása

Bluetooth konfigurálja az Intel NUC, melyek lehetővé teszik a SensorTag eszköz csatlakoztatása és telemetriai adatokat küldhet.

### <a name="find-the-mac-address-of-the-sensortag"></a>A MAC-címét a SensorTag keresése

1. Az Intel NUC a rendszerhéj futtassa a következő parancsot a Bluetooth szolgáltatás feloldása:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. A következő parancsokat a Bluetooth-szolgáltatás elindítása az Intel NUC, és írja be a Bluetooth rendszerhéj:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. A Bluetooth-vezérlő power futtathatnak a következő parancsot:

    ```bash
    power on
    ```

    Ha a tartományvezérlő, megjelenik egy üzenet **power módosítása a sikeres**.

1. Futtassa a szolgáltatást a közeli Bluetooth-eszközök a következő parancsot:

    ```bash
    scan on
    ```

1. Nyomja meg a bekapcsológombot az abba, hogy a felderíthető SensorTag. A zöld LED tokenkódot.

1. Amikor megjelenik egy üzenet, hogy a vezérlő észlelte a SensorTag a rendszerhéj, jegyezze fel az eszköz MAC-címét. A MAC-cím a következőképpen néz **A0:E6:F8:B5:F6:00**. A MAC-címet az oktatóanyag későbbi részében szüksége, az átjáró konfigurálásakor.

1. A következő paranccsal kapcsolja ki a Bluetooth vizsgálata:

    ```bash
    scan off
    ```

1. A következő parancs futtatásával ellenőrizze, hogy a SensorTag eszköz csatlakozhat:

    ```bash
    connect <SensorTag MAC address>
    ```

    Ha a csatlakozás sikeres, a rendszerhéj jeleníti meg az üzenet **sikeres kapcsolat** és kiírja a SensorTag eszközére vonatkozó információt. Ha nem sikerül, ellenőrizze a SensorTag továbbra is be van kapcsolva.

1. Ezután válassza le a SensorTag és kilépés a Bluetooth-rendszerhéjból a következő parancsok futtatásával:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-software](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Az egyéni IoT peremhálózati modul létrehozása

Most már lefordíthatja az egyéni IoT peremhálózati modul, amely lehetővé teszi, hogy az átjáró üzeneteket küldhet a távoli figyelési megoldást igényelnek. Átjáró és az IoT-Edge modulok konfigurálásával kapcsolatos további információkért lásd: [Azure IoT peremhálózati fogalmak][lnk-gateway-concepts].

Töltse le az egyéni IoT peremhálózati modulok forráskódja a Githubról a következő parancsokkal:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Hozni az egyéni IoT biztonsági modult, a következő parancsokkal:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

A build script a build mappában helyezi el a libsensor2remotemonitoring.so egyéni IoT peremhálózati modul.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Konfigurálja és futtassa az IoT-átjáró

Most már konfigurálhatja az IoT peremhálózati átjáró SensorTag eszközét telemetriai adatokat küldeni a távoli figyelési irányítópult. Átjáró és az IoT-Edge modulok konfigurálásával kapcsolatos további információkért lásd: [Azure IoT peremhálózati fogalmak][lnk-gateway-concepts].

> [!TIP]
> Ebben az oktatóanyagban a használja, a standard `vi` az Intel NUC a szövegszerkesztőben. Ha még nem használta `vi` , el kell végeznie egy bevezető oktatóanyag, például a [Unix - a vi szerkesztő oktatóanyag] [ lnk-vi-tutorial] való ismerkedésre a szerkesztő. Másik lehetőségként telepítheti a könnyebben [nano](https://www.nano-editor.org/) a paranccsal szerkesztő `smart install nano -y`.

Nyissa meg a minta konfigurációs fájlt a **vi** szerkesztő a következő parancsot:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Az IOT hubbal modul konfigurációjában keresse meg a következő sorokat:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Cserélje le a helyőrző értékeket az IoT-központ adatokat létrehozott és mentett Ez az oktatóanyag elején. IoTHubName értékét a következőképpen néz **yourrmsolution37e08**, és IoTSuffix értéke általában **azure-devices.net**.

A hozzárendelési modul konfigurációjában keresse meg a következő sorokat:

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Cserélje le a **macAddress** helyőrzőt a korábban feljegyzett SensorTag MAC-címét. Cserélje le a **deviceID** és **deviceKey** helyőrzőt azonosítóit és a két eszköz a távoli felügyeleti megoldás korábban létrehozott kulcsokat.

Keresse meg a SensorTag modulnak a konfiguráció a következő sorokat:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Cserélje le a **eszköz\_mac\_cím** helyőrzőt a korábban feljegyzett SensorTag MAC-címét.

Mentse a módosításokat.

Most már futtathatja az átjáró a következő parancsokkal:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

Az IoT-átjárónak elindítja az Intel NUC, és a SensorTag telemetriai adatokat küld a távoli figyelési megoldást:

![Az IoT-átjárónak telemetriai küldi a SensorTag][img-telemetry]

Nyomja le az **Ctrl-C** kilép a programból bármikor.

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

Az átjáró éppen most küldi telemetriai az SensorTag eszközről a távoli felügyeleti megoldás. A telemetriai adatokat a megoldás irányítópultján tekintheti meg. A megoldás irányítópultról is elküldheti parancsok SensorTag eszközét az átjárón keresztül.

- Nyissa meg a megoldás irányítópultja.
- Válassza ki az eszközt, az átjáró a SensorTag jelölő konfigurált a **nézet eszköz** legördülő menüből.
- A telemetriai adatokat a SensorTag eszközről az irányítópulton jeleníti meg.

![A SensorTag eszközökről telemetriai adatok megjelenítéséhez][img-telemetry-display]

> [!WARNING]
> Ha nem adja meg a távoli figyelési megoldást igényelnek fut az Azure-fiókjával, a futtatásakor a kell fizetni. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config]. Ha befejezte, használja az előkonfigurált megoldás törlése az Azure-fiókjával.


## <a name="next-steps"></a>Következő lépések

Látogasson el a [Azure IoT-fejlesztői központhoz](https://azure.microsoft.com/develop/iot/) további mintákat és Azure IoT-dokumentációja.

[img-telemetry]: ./media/iot-suite-v1-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-v1-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started