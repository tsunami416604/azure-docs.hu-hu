---
title: Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (Python) |} A Microsoft Docs
description: Eszköz fejlesztőként Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz Python használatával.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9f39832b50ed983e7d8a0bfc0a06366870717fa3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051985"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők a Raspberry Pi csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz a Python programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben ismertetett lépések végrehajtásához szüksége van a következő összetevők:

* A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Raspberry Pi eszköz a Raspbian operációs rendszert. Egy figyelő, billentyűzetből és egérből a Raspberry Pi csatlakozik a grafikus felhasználói környezet elérésére van szüksége. A Raspberry Pi képesnek kell lennie [csatlakozni az internethez](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Szükség esetén egy [értelemben Hat](https://www.raspberrypi.org/products/sense-hat/) a Raspberry pi bővítmény tábla. Ez a tábla gyűjt telemetrikus adatokat küldeni az Azure IoT Central alkalmazáshoz különböző érzékelők. Ha nem rendelkezik egy **értelemben Hat** táblához, használhatja helyette az emulátor (elérhető Raspberry Pi-kép részeként).

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
    - Jelenlegi
    - Sebesség ventilátor
    - Integrációs modul váltógombot.
- Tulajdonságok
    - Eszköz-tulajdonságot die
    - Hely felhőalapú tulajdonság

A konfigurációs eszköz-sablon teljes részletekért tekintse meg a [Raspberry PI eszköz sablon részletei](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **Raspberry Pi** eszköz sablont és nyomon az eszköz kapcsolat részleteinek (**hatókör azonosítója, az eszköz azonosítója, az elsődleges kulcs**). További információkért lásd: [valós eszköz hozzáadása az Azure IoT Central alkalmazásnak](tutorial-add-device.md).


### <a name="configure-the-raspberry-pi"></a>A Raspberry Pi konfigurálása

Az alábbi lépéseket ismertetik letöltése és konfigurálása a Python-mintaalkalmazás a Githubról. Ez a mintaalkalmazás:

* Azure IoT Central telemetriai és tulajdonságértékeket küld.
* Beállítás Azure IoT Central-ben végrehajtott változtatások válaszol.

Az eszköz konfigurálása [részletes utasítások a Githubon.](https://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> A Raspberry Pi Python-mintához kapcsolatos további információkért lásd: a [információs](https://aka.ms/iotcentral-docs-Raspi-releases) fájlt a Githubon.


1. Miután konfigurálta az eszközt, az eszköz el kell adatokat küldeni az Azure IoT Central rövid ideig.
1. Az Azure IoT Central-alkalmazás láthatja, hogy a kód a Raspberry Pi-on futó hogyan működjön együtt az alkalmazás:

    * Az a **mérések** lap a valós eszközhöz, tekintse meg a Raspberry Pi által küldött telemetriát. Ha használja a **értelemben HAT emulátor**, módosíthatja a telemetriai értékeket a grafikus felhasználói felületen, a Raspberry Pi-on.
    * Az a **tulajdonságok** lapon láthatja a jelentett értékét **Die szám** tulajdonság.
    * Az a **beállítások** lapon módosíthatja a Raspberry Pi feszültség és ventilátor sebesség például a különböző beállításait. A Raspberry Pi nyugtázza a módosítást, ha a beállítás állapota **szinkronizált** az Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI eszköz sablon részletei

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| TEMP           | ° C     | tartsuk ott -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1000    | 0              |
| accelerometerX | felügyeleti csoport     | -2000   | 2000    | 0              |
| accelerometerY | felügyeleti csoport     | -2000   | 2000    | 0              |
| accelerometerZ | felügyeleti csoport     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Jelenlegi      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Sebesség ventilátor    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mező neve | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköztulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | –       |

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépések:

* [Egy általános Node.js ügyfél-alkalmazás csatlakoztatása az Azure IoT Central](howto-connect-nodejs.md)
