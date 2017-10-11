---
title: "Csatlakozás Azure IoT Suite használatával az Intel NUC átjáró |} Microsoft Docs"
description: "A Microsoft IoT kereskedelmi átjáró Kit és a távoli figyelési előkonfigurált megoldást használni. Az Azure IoT peremhálózati átjáró használatával kapcsolódni a távoli felügyeleti megoldás, szimulált telemetriai adatokat küldeni a felhőben és a megoldás irányítópultja metódusokra válaszolni."
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Csatlakozzon a távoli figyelési előkonfigurált megoldást az Azure IoT peremhálózati átjárót, és szimulált telemetriai adatokat küldhet

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

Az oktatóanyag bemutatja, ezzel szimulálva a hőmérséklet és a páratartalom adatokat küldeni a távoli felügyeleti előkonfigurált megoldás Azure IoT Edge használata. Az oktatóprogram:

- Az Azure IoT peremhálózati minta átjáró végrehajtásához.
- Az IoT Suite távoli megfigyelési előre konfigurált megoldás, a felhő alapú háttér.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban végezze el a következő lépéseket:

- Telepítse a távoli felügyeleti előkonfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatásokhoz.
- Az Intel NUC átjáróeszköz beállítása a számítógép és a távoli felügyeleti megoldás folytatott kommunikációhoz.
- Konfigurálja az IoT peremhálózati átjáró, amely megtalálható a megoldás irányítópultja szimulált telemetriai adatokat küldhet.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> A távoli felügyeleti megoldás látja el az Azure-előfizetéshez az Azure szolgáltatások. A központi telepítés által adott jelentéseket tükrözik a valós vállalati architektúra. Szükségtelen Azure felhasználási díjak elkerülése az előre konfigurált megoldást a következő azureiotsuite.com a példányának törlése után vele. Ha újra kell az előkonfigurált megoldás, egyszerűen létrehozhatja azt. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config].

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Ismételje meg az előző lépéseket egy második eszköz, például egy eszköz azonosítójával **device02**. A minta elküldi az adatokat a távoli felügyeleti megoldás az átjáró két szimulált eszközökről.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Az egyéni IoT peremhálózati modul létrehozása

Most már lefordíthatja az egyéni IoT peremhálózati modul, amely lehetővé teszi, hogy az átjáró üzeneteket küldhet a távoli figyelési megoldást igényelnek. Átjáró és az IoT-Edge modulok konfigurálásával kapcsolatos további információkért lásd: [Azure IoT peremhálózati fogalmak][lnk-gateway-concepts].

Töltse le az egyéni IoT peremhálózati modulok forráskódja a Githubról a következő parancsokkal:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Hozni az egyéni IoT biztonsági modult, a következő parancsokkal:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

A build script a build mappában helyezi el a libsimulator.so egyéni IoT peremhálózati modul.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Konfigurálja és futtassa az IoT-átjáró

Most már konfigurálhatja az IoT peremhálózati átjáró szimulált telemetriai adatokat küldeni a távoli figyelési irányítópulton. Átjáró és az IoT-Edge modulok konfigurálásával kapcsolatos további információkért lásd: [Azure IoT peremhálózati fogalmak][lnk-gateway-concepts].

> [!TIP]
> Ebben az oktatóanyagban a használja, a standard `vi` az Intel NUC a szövegszerkesztőben. Ha még nem használta `vi` , el kell végeznie egy bevezető oktatóanyag, például a [Unix - a vi szerkesztő oktatóanyag] [ lnk-vi-tutorial] való ismerkedésre a szerkesztő. Másik lehetőségként telepítheti a könnyebben [nano](https://www.nano-editor.org/) a paranccsal szerkesztő `smart install nano -y`.

Nyissa meg a minta konfigurációs fájlt a **vi** szerkesztő a következő parancsot:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Cserélje le a **deviceID** és **deviceKey** helyőrzőt azonosítóit és a két eszköz a távoli felügyeleti megoldás korábban létrehozott kulcsokat.

Mentse a módosításokat.

Most már futtathatja az IoT peremhálózati átjáró a következő parancsokkal:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

Az átjáró elindítja az Intel NUC és szimulált telemetriai adatokat küld a távoli figyelési megoldást:

![Az IoT-átjárónak állít elő, szimulált telemetriai adat][img-simulated telemetry]

Nyomja le az **Ctrl-C** kilép a programból bármikor.

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

Az IoT-átjárónak szimulált telemetriai most küld a távoli figyelési megoldást igényelnek. A telemetriai adatokat a megoldás irányítópultján tekintheti meg.

- Nyissa meg a megoldás irányítópultja.
- Válassza ki a két eszköz, az átjárót konfigurált a **nézet eszköz** legördülő menüből.
- Az átjáró eszközökről telemetriai adatok az irányítópulton jeleníti meg.

![A szimulált átjáró eszközök telemetriai adatok megjelenítéséhez][img-telemetry-display]

> [!WARNING]
> Ha nem adja meg a távoli figyelési megoldást igényelnek fut az Azure-fiókjával, a futtatásakor a kell fizetni. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config]. Ha befejezte, használja az előkonfigurált megoldás törlése az Azure-fiókjával.

## <a name="next-steps"></a>Következő lépések

Látogasson el a [Azure IoT-fejlesztői központhoz](https://azure.microsoft.com/develop/iot/) további mintákat és Azure IoT-dokumentációja.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started