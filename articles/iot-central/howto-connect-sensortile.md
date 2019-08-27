---
title: SensorTile. Box eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: A SensorTile. Box eszköznek az Azure IoT Central-alkalmazáshoz való csatlakoztatásáról itt tájékozódhat.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 3d804b1e14d1b79266a74340e8682f1bf03d8f30
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050559"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>A SensorTile. Box eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan lehet egy SensorTile. Box eszközt a Microsoft Azure IoT Central alkalmazáshoz csatlakozni az eszköz fejlesztőinek.

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések végrehajtásához a következő erőforrásokra van szükség:

* Egy SensorTile. Box eszköz. További információ: [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Az Android-eszközön telepített ST-érzékelő alkalmazás a [következő címről tölthető le](https://play.google.com/store/apps/details?id=com.st.bluems):. További információért látogasson el ide: [ST. érzékelő](https://www.st.com/stblesensor)
* Az **DevKits** alkalmazás sablonjában létrehozott Azure IoT Central alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Adja hozzá a **SensorTile. Box** eszközt a IoT Central alkalmazáshoz az **eszközbeállítások** lapon, majd az **+ új**elemre kattintva és a **SensorTile. Box** sablon kiválasztásával.

### <a name="get-your-device-connection-details"></a>Az eszköz kapcsolati adatainak beolvasása

Az Azure IoT Central alkalmazásban adjon hozzá egy valós eszközt a **SensorTile. Box** eszköz sablonjában, és jegyezze fel az eszköz kapcsolatának részleteit: **Hatókör-azonosító**, **eszköz azonosítója**és **elsődleges kulcs**:

1. Eszköz hozzáadása az eszközökről. Valódi eszköz hozzáadásához válassza az **+ új > valós** lehetőséget.

    * Adja meg a kisbetűs **eszköz azonosítóját**, vagy használja a javasolt **eszköz azonosítóját**.
    * Adja meg az **eszköz nevét**, vagy használja a javasolt nevet

    ![Eszköz hozzáadása](media/howto-connect-sensortile/real-device.png)

1. Az eszköz kapcsolati adatainak, a **hatókör azonosítójának**, az **eszköz azonosítójának**és az **elsődleges kulcsnak**a beszerzéséhez válassza a **Kapcsolódás** lehetőséget az eszköz oldalon.

    ![Kapcsolat részletei](media/howto-connect-sensortile/connect-device.png)

1. Jegyezze fel a kapcsolat részleteit. A következő lépésben a fejlesztői készlet-eszköz előkészítésekor átmenetileg le van választva az internetről.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>A SensorTile. Box beállítása a mobileszköz-alkalmazással

Ez a szakasz azt ismerteti, hogyan lehet leküldeni az alkalmazás belső vezérlőprogramot az eszközre. Ezután hogyan küldheti el az eszközre vonatkozó adatIoT Centralt a Bluetooth alacsony energiafogyasztású (egypontos) kapcsolattal rendelkező, a ST-t érzékelő Mobile App használatával.

1. Nyissa meg a ST-Sensor-érzékelő alkalmazást, és kattintson az **új alkalmazás létrehozása** gombra.

    ![App létrehozása](media/howto-connect-sensortile/create-app.png)

1. Válassza ki a **barométer** alkalmazást.
1. Nyomja meg a feltöltés gombot.

    ![Barométer feltöltése](media/howto-connect-sensortile/barometer-upload.png)

1. Nyomja meg a SensorTile. Box gombhoz társított lejátszás gombot.
1. Ha a folyamat befejeződött, a SensorTile. Box a hőmérsékletet, a nyomást és a páratartalmat a kapcsolaton keresztül továbbítja.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>A SensorTile. Box összekötése a felhővel

Ebből a szakaszból megtudhatja, hogyan csatlakoztatható a SensorTile. Box a Mobile-alkalmazáshoz, és hogyan csatlakoztatható a felhőhöz.

1. A bal oldali menüben kattintson a **Felhőbeli naplózás** gombra.

    ![Felhőbeli naplózás](media/howto-connect-sensortile/cloud-logging.png)

1. Válassza az **Azure IoT Central** lehetőséget a felhőalapú szolgáltatóként.
1. Szúrja be a korábban feljegyzett eszköz AZONOSÍTÓját és hatókör-AZONOSÍTÓját.

    ![Hitelesítő adatok](media/howto-connect-sensortile/credentials.png)

1. Válassza ki az **alkalmazás kulcsának** választógombját.
1. Kattintson a **Kapcsolódás** elemre, és válassza ki a feltölteni kívánt telemetria-adatok közül.
1. Néhány másodperc elteltével az adat megjelenik a IoT Central alkalmazás irányítópultján.

## <a name="sensortilebox-device-template-details"></a>SensorTile. Box – eszköz sablonjának részletei

A SensorTile. Box eszköz sablonjában létrehozott alkalmazás a következő jellemzőkkel rendelkezik:

### <a name="telemetry"></a>Telemetria

| Mezőnév     | Mértékegységek  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 30       | 90     | 1              |
| ideiglenes           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | – 2000   | 2000    | 0              |
| gyorsulásmérő | mg     | – 2000   | 2000    | 0              |
| accelerometerZ | mg     | – 2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztatható egy SensorTile. Box az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, [Hogyan állítható be egy egyéni eszköz-sablon](howto-set-up-template.md) a saját IoT-eszközhöz.
