---
title: Az Azure IoT Central alkalmazáshoz (Python) Raspberry Pi Connnect |} A Microsoft Docs
description: Eszköz fejlesztőként Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz Python használatával.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a3d6ad9f2f442481908bc02252fcc8ab1a74419e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205588"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők a Raspberry Pi csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz a Python programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információkért lásd: [az Azure IoT központi alkalmazás létrehozása](howto-create-application.md).
* Raspberry Pi eszköz a Raspbian operációs rendszert. Egy figyelő, billentyűzetből és egérből a Raspberry Pi csatlakozik a grafikus felhasználói környezet elérésére van szüksége. A Raspberry Pi képesnek kell lennie [csatlakozni az internethez](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Szükség esetén egy [értelemben Hat](https://www.raspberrypi.org/products/sense-hat/) a Raspberry pi bővítmény tábla. Ez a tábla gyűjt telemetrikus adatokat küldeni az Azure IoT Central alkalmazáshoz különböző érzékelők. Ha nem rendelkezik egy **értelemben Hat** táblához, használhatja helyette egy-emulátoron.

## <a name="sample-devkits-application"></a>**Minta Devkits** alkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel: 

- Telemetriai adatokat, amely tartalmazza az eszköz a mérések **páratartalom**, **hőmérséklet**, **nyomás**, **Magnometer** (mért mentén X Y, tengely Z), **Accelorometer** (X, Y, mentén mért Z tengely) és **Giroszkóp** (X, Y, mentén mért Z tengely).
- Beállítások megjelenítése **feszültség**, **aktuális**,**ventilátor sebesség** és a egy **integrációs modul** be-vagy kikapcsolása.
- Eszköztulajdonság tartalmazó tulajdonságainak **die szám** és **hely** felhőbeli tulajdonság.


Tekintse meg a konfigurációs eszköz sablon kapcsolatos részletes [Raspberry PI eszköz sablon részletei](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **Raspberry Pi** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterláncát. További információkért lásd: [valós eszköz hozzáadása az Azure IoT Central alkalmazásnak](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>A Raspberry Pi konfigurálása

Az alábbi lépéseket ismertetik letöltése és konfigurálása a Python-mintaalkalmazás a Githubról. Ez a mintaalkalmazás:

* Azure IoT Central telemetriai és tulajdonságértékeket küld.
* Beállítás Azure IoT Central-ben végrehajtott változtatások válaszol.

> [!NOTE]
> A Raspberry Pi Python-mintához kapcsolatos további információkért lásd: a [információs](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) fájlt a Githubon.

1. A webböngésző segítségével a Raspberry Pi desktopban lépjen a [Azure IoT Central belső vezérlőprogram-kiadások](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) lapot.

1. Töltse le a zip-fájlt, amely tartalmazza a legújabb belső vezérlőprogramot, a kezdőmappa a Raspberry Pi-on. A fájlnév a következőhöz hasonló `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Bontsa ki a belső vezérlőprogram-fájlt, használja a **Fájlkezelőben** a Raspberry Pi desktopban. Kattintson a jobb gombbal a zip-fájlt, és válassza a **itt kinyerése**. Ez a művelet létrehoz egy nevű mappába `RaspberryPi-IoTCentral-X.X.X` az otthoni mappában.

1. Ha nem rendelkezik egy **értelemben Hat** táblához csatlakozik a Raspberry Pi-októl az emulátorban engedélyeznie kell:
    1. A **Fájlkezelőben**, a a `RaspberryPi-IoTCentral-X.X.X` mappát, kattintson a jobb gombbal a **config.iot** fájlt, és válassza a **szövegszerkesztőben**.
    1. Módosítsa a sort `"simulateSenseHat": false,` való `"simulateSenseHat": true,`.
    1. A módosítások mentéséhez és bezárásához **szövegszerkesztőben**.

1. Indítsa el a **terminálon** munkamenetet, és használja a `cd` parancs használatával keresse meg azt a mappát az előző lépésben létrehozott.

1. A mintaalkalmazás futtatása indításához írja be a `./start.sh` a a **terminálon** ablak. Ha használja a **értelemben HAT emulátor**, a grafikus felhasználói felületen jeleníti meg. A grafikus felhasználói Felülettel használhatja az Azure IoT Central alkalmazásnak küldött telemetriai adatok értékek módosításához.

1. A **terminálon** ablak, amely a következőhöz hasonló üzenetet jelenít meg `Device information being served at http://192.168.0.60:8080`. Az URL-címet a környezetében eltérő lehet. Másolja az URL-címet, és a konfigurációs lapon keresse meg a webböngésző használatával:

    ![Eszközök konfigurálása](media/howto-connect-raspberry-pi-python/configure.png)

1. Adja meg az eszköz kapcsolati karakterláncának rögzített egy hozzáadásakor valós eszközöknek az Azure IoT Central alkalmazáshoz. Válassza a **konfigurálása eszköz**. Megjelenik egy üzenet **eszköz van konfigurálva, az eszköz el kell indulnia, adatokat küldeni az Azure IoT Central rövid ideig**.

1. Az Azure IoT Central-alkalmazás láthatja, hogy a kód a Raspberry Pi-on futó hogyan működjön együtt az alkalmazás:

    * Az a **mérések** lap a valós eszközhöz, tekintse meg a Raspberry Pi által küldött telemetriát. Ha használja a **értelemben HAT emulátor**, módosíthatja a telemetriai értékeket a grafikus felhasználói felületen, a Raspberry Pi-on.
    * Az a **tulajdonságok** lapon láthatja a jelentett értékét **Die szám** tulajdonság.
    * Az a **beállítások** lapon módosíthatja a Raspberry Pi feszültség és ventilátor sebesség például a különböző beállításait. A Raspberry Pi nyugtázza a módosítást, ha a beállítás állapota **szinkronizált** az Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI eszköz sablon részletei

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mezőnév     | Mértékegységek  | Minimum | Maximum | Tizedeshelyek |
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

| Megjelenített név | Mezőnév | Mértékegységek | Tizedeshelyek | Minimum | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Sebesség ventilátor    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mezőnév | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| INTEGRÁCIÓS MODUL           | activateIR | BE      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mezőnév | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköztulajdonság | Die száma   | dieNumber  | szám    |
| SMS            | Tartózkodási hely     | hely   | -       |

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépések:

* [Egy általános Node.js ügyfél-alkalmazás csatlakoztatása az Azure IoT Central](howto-connect-nodejs.md)
