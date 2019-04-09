---
title: Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (Python) |} A Microsoft Docs
description: Eszköz a fejlesztők Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz Python használatával.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 6ac16651e2d49dd903ff994b18a8f571bd92fbf6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272359"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők a Raspberry Pi csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz a Python programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben ismertetett lépések végrehajtásához szüksége van a következő összetevők:

* A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Raspberry Pi eszköz a Raspbian operációs rendszert. A Raspberry Pi kell rendelkezniük az internethez való kapcsolódáshoz. További információkért lásd: [beállítása a Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Minta Devkits** alkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel:

- Telemetriai adatokat, amely tartalmazza az eszköz összegyűjti a következő mérési:
  - Páratartalom
  - Hőmérséklet
  - Pressure
  - Magnetométer (X, Y, a – Z)
  - Érzékelőből (X, Y, a – Z)
  - Giroszkóp (X, Y, a – Z)
- Beállítások
  - Feszültségérzékelő
  - Aktuális
  - Sebesség ventilátor
  - Integrációs modul váltógombot.
- Tulajdonságok
  - Eszköz-tulajdonságot die
  - Hely felhőalapú tulajdonság

A konfigurációs eszköz-sablon teljes részletekért lásd: a [Raspberry Pi eszköz sablon részleteinek](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **Raspberry Pi** eszköz sablont. Jegyezze fel az eszköz kapcsolati adatok (**hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**). További információkért lásd: [valós eszköz hozzáadása az Azure IoT Central alkalmazásnak](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>A Raspberry Pi konfigurálása

Az alábbi lépéseket ismertetik letöltése és konfigurálása a Python-mintaalkalmazás a Githubról. Ez a mintaalkalmazás:

* Azure IoT Central telemetriai és tulajdonságértékeket küld.
* Beállítás Azure IoT Central-ben végrehajtott változtatások válaszol.

Az eszköz konfigurálása [részletes utasítások a Githubon](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Ha az eszköz van konfigurálva, az eszköz elindítja a telemetriát mérések küldése az Azure IoT Central.
1. Az Azure IoT Central-alkalmazás láthatja, hogy a kód a Raspberry Pi-on futó hogyan működjön együtt az alkalmazás:

    * Az a **mérések** lap a valós eszközhöz, tekintse meg a Raspberry Pi által küldött telemetriát.
    * Az a **beállítások** lapon módosíthatja a Raspberry Pi például feszültség és ventilátor sebességű beállításai. A Raspberry Pi nyugtázza a módosítást, ha a beállítás állapota **szinkronizált**.

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi eszköz sablon részletei

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| TEMP           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Sebesség ventilátor    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mező neve | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Typo            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköztulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | –       |

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépések:

* [Egy általános Node.js ügyfél-alkalmazás csatlakoztatása az Azure IoT Central](howto-connect-nodejs.md)
