---
title: Málna-PI összekötése az Azure IoT Central-alkalmazással (Python) | Microsoft Docs
description: Eszköz-fejlesztőként, hogyan csatlakoztatható a málna PI az Azure IoT Central-alkalmazáshoz a Python használatával.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3f91e09e35eec0685cb4333802b860fb08412cb6
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019744"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Málna PI összekötése az Azure IoT Central-alkalmazással (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan lehet az eszköz fejlesztője a málna PI-t a Microsoft Azure IoT Central alkalmazáshoz a Python programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések végrehajtásához a következő összetevőkre van szükség:

* A mintául szolgáló **Devkits** létrehozott Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* A Raspbian operációs rendszert futtató málna PI-eszköz. A málna PI-nek képesnek kell lennie az internethez való kapcsolódásra. További információ: [a málna PI beállítása](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> A málna PI-eszközök beállításával és csatlakozásával kapcsolatos információkért látogasson el [a málna PI használatába](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="sample-devkits-application"></a>**Példa Devkits** alkalmazásra

A mintául szolgáló **Devkits** létrehozott alkalmazások egy **málna PI** -eszközt tartalmaznak a következő jellemzőkkel:

- Telemetria, amely a következő méréseket tartalmazza, amelyeket az eszköz gyűjt:
  - Nedvességtartalom
  - Hőmérséklet
  - Pressure
  - Magnetometer (X, Y, Z)
  - Gyorsulásmérő (X, Y, Z)
  - Giroszkóp (X, Y, Z)
- Beállítások
  - Feszültség
  - Aktuális
  - Ventilátor sebessége
  - IR-váltógomb
- properties
  - Die Number Device tulajdonság
  - Location Cloud tulajdonság

Az eszköz sablon konfigurációjának részletes ismertetését lásd: [málna PI-eszköz sablonjának részletei](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central alkalmazásban vegyen fel egy valódi eszközt a **málna PI** -eszköz sablonból. Jegyezze fel az eszköz kapcsolatának részleteit (a**hatókör azonosítóját**, az **eszköz azonosítóját**és az **elsődleges kulcsot**). További információ: Real- [eszköz hozzáadása az Azure IoT Central alkalmazáshoz](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>A málna PI konfigurálása

A következő lépések azt ismertetik, hogyan töltheti le és konfigurálhatja a Python-alkalmazás mintáját a GitHubról. Ez a minta alkalmazás:

* Telemetria és tulajdonságértékek küldése az Azure IoT Centralnak.
* Válaszol az Azure IoT Centralban végrehajtott módosítások beállítására.

1. Kapcsolódjon a málna PI-ből származó rendszerhéj-környezethez, akár a málna PI Desktopból, akár távolról SSH használatával.

1. Futtassa a következő parancsot a IoT Central Python-ügyfél telepítéséhez:

    ```sh
    pip install iotc
    ```

1. Töltse le a Python-kód mintáját:

    ```sh
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Szerkessze `app.py` a letöltött fájlt, és `DEVICE_ID`cserélje le `SCOPE_ID`a, `PRIMARY/SECONDARY device KEY` , és helyőrzőket a korábban feljegyzett kapcsolatok értékekre. Mentse a módosításokat.

    > [!TIP]
    > A málna PI-ben lévő rendszerhéjban a **Nano** vagy a **VI** szövegszerkesztőt is használhatja.

1. A minta futtatásához használja a következő parancsot:

    ```sh
    python app.py
    ```

    A málna PI elkezdi telemetria-mérések küldését az Azure IoT Centralba.

1. Az Azure IoT Central alkalmazásban láthatja, hogy a málna PI-on futó kód hogyan működik együtt az alkalmazással:

    * A valódi eszköz mérések lapján megtekintheti a málna PI-ból eljuttatott telemetria.
    * A **Tulajdonságok** lapon megtekintheti a **Die Number** Device tulajdonságot.
    * A **Beállítások** lapon módosíthatja a málna PI beállításait, például a feszültséget és a ventilátor sebességét. Ha a málna PI felismeri a változást, a beállítás szinkronizált értékkéntjelenik meg.

## <a name="raspberry-pi-device-template-details"></a>Málna PI-eszköz sablonjának részletei

A mintául szolgáló **Devkits** létrehozott alkalmazások egy **málna PI** -eszközt tartalmaznak a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mezőnév     | Mértékegységek  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| ideiglenes           | °C     | – 40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | – 2000   | 2000    | 0              |
| gyorsulásmérő | mg     | – 2000   | 2000    | 0              |
| accelerometerZ | mg     | – 2000   | 2000    | 0              |
| gyroscopeX     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeY     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | – 2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállítások

| Display name | Mezőnév | Mértékegységek | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültség      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Erősítők  | 0              | 0       | 100     | 0       |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Váltási beállítások

| Display name | Mezőnév | Szövegen | Kikapcsolt szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | BE      | KI      | Ki     |

### <a name="properties"></a>properties

| Type            | Display name | Mezőnév | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköz tulajdonsága | Die száma   | dieNumber  | szám    |
| Text            | Location     | location   | –       |

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztatható a málna PI az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés az, hogy megtudja, hogyan [állíthat be egyéni](howto-set-up-template.md) IoT-eszközt a saját eszközökhöz.
