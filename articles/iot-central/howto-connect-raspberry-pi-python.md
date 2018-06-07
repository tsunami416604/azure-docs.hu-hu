---
title: Kapcsolat az Azure IoT központi alkalmazáshoz (Python) egy málna Pi |} Microsoft Docs
description: Egy eszköz fejlesztőjeként egy málna Pi csatlakoztatása az Azure IoT központi alkalmazás Python használatával.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: e9c2d18a518bd5c98fcc35efdb0dff36970a49b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629065"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Egy málna Pi csatlakozni az Azure IoT központi alkalmazás (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Ez a cikk ismerteti, hogyan eszköz fejlesztőként egy málna Pi kapcsolódni a Microsoft Azure IoT központi alkalmazáshoz, a Python programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* A létrehozott Azure IoT központi alkalmazást a **minta Devkits** alkalmazássablon. További információkért lásd: [létrehozása az Azure IoT központi alkalmazás](howto-create-application.md).
* A Raspbian operációs rendszert futtató málna Pi eszköz. Egy monitor, billentyűzet és egér csatlakozik a málna Pi férhet hozzá a grafikus felhasználói Felülettel környezethez szükséges. A málna Pi képesnek kell lennie [csatlakozni az internethez](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Szükség esetén egy [logika Hat](https://www.raspberrypi.org/products/sense-hat/) a málna pi bővítmény tábla. A tábla gyűjt telemetrikus adatokat küldeni az Azure IoT központi alkalmazás különböző érzékelők. Ha nem rendelkezik egy **logika Hat** üzenőfalon, használhatja az emulátor helyette.

A létrehozott alkalmazást, a **minta Devkits** alkalmazás sablon tartalmaz egy **málna Pi** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| nedvességtartalma       | %      | 0       | 100     | 0              |
| TEMP           | ° C     | tartsuk ott -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | az mg     | -2000   | 2000    | 0              |
| accelerometerY | az mg     | -2000   | 2000    | 0              |
| accelerometerZ | az mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mező neve | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| INFRAVÖRÖS           | activateIR | ON      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköz tulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | –       |

### <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT központi-alkalmazás hozzáadása a valódi eszközről a **málna Pi** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterlánc. További információkért lásd: [valós eszköz hozzáadása az Azure IoT központi alkalmazás](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>A Raspberry Pi konfigurálása

A következő lépések ismertetik töltenie és konfigurálnia kell a Githubról Python mintaalkalmazást. A mintaalkalmazást:

* Telemetriai adatok és a tulajdonság értékek küld Azure IoT központi.
* Az Azure IoT-központ módosított válaszol.

> [!NOTE]
> A Pi Python málna minta kapcsolatos további információkért tekintse meg a [információs](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) fájlt a Githubon.

1. A Pi málna asztali a webböngésző segítségével keresse meg a [Azure IoT központi belső vezérlőprogram feloldja a](https://github.com/Microsoft/microsoft-iot-central-firmware/releases) lap.

1. Töltse le a zip-fájl, amely tartalmazza a legújabb belső vezérlőprogramját a málna Pi otthoni mappájába. A fájl neve a következőképpen néz `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Bontsa ki a belső vezérlőprogram fájlt, használja a **fájlkezelő** az málna Pi íróasztal. Kattintson a jobb gombbal a zip-fájl, és válassza a **kibontása Itt**. Ez a művelet létrehoz egy nevű mappát `RaspberryPi-IoTCentral-X.X.X` az otthoni mappában.

1. Ha nem rendelkezik egy **logika Hat** a málna Pi csatolt tábla engedélyeznie kell az emulátor:
    1. A **fájlkezelő**, a a `RaspberryPi-IoTCentral-X.X.X` mappát, kattintson a jobb gombbal a **config.iot** fájlt, és válassza a **szövegszerkesztőben**.
    1. Módosítsa a sort `"simulateSenseHat": false,` való `"simulateSenseHat": true,`.
    1. A módosítások mentéséhez és bezárásához **szövegszerkesztőben**.

1. Indítsa el a **Terminálszolgáltatások** munkamenet és használja a `cd` parancs használatával keresse meg azt a mappát az előző lépésben létrehozott.

1. Indítsa el a futó mintaalkalmazást, írja be a következőt `./start.sh` a a **Terminálszolgáltatások** ablak. Ha használja a **logika HAT emulátor**, a grafikus felhasználói felületen jeleníti meg. A grafikus felhasználói felület segítségével módosíthatja a telemetriai adatok értékeit az Azure IoT központi alkalmazás számára.

1. A **Terminálszolgáltatások** ablak, amely a következőhöz hasonló üzenetet jelenít meg `Device information being served at http://192.168.0.60:8080`. Az URL-cím eltérhet a környezetben. Másolja az URL-címet, és keresse meg a lap a webböngésző segítségével:

    ![Eszközök konfigurálása](media/howto-connect-raspberry-pi-python/configure.png)

1. Adja meg az eszköz kapcsolati karakterláncot, amikor az az Azure IoT központi alkalmazáshoz fel egy valódi eszközt feljegyezte. Válassza a **eszköz konfigurálása**. Megjelenik egy üzenet **eszköz be van állítva, az eszköz webalkalmazásokba történő adatküldés Azure IoT központi rövid ideig**.

1. Az Azure IoT központi alkalmazás láthatja, hogy a kód a málna Pi futó hogyan működjön együtt az alkalmazás:

    * Az a **mérések** lap az valós eszközhöz, tekintse meg a málna Pi küldött telemetriai adatok. Ha használja a **logika HAT emulátor**, módosíthatja a telemetriai adatok értékek a málna Pi a grafikus felhasználói felületen.
    * A a **tulajdonságok** lapon megtekintheti a jelentett értékének **Die szám** tulajdonság.
    * Az a **beállítások** lapon módosíthatja a málna Pi feszültség és ventilátor sebesség például különböző beállításait. Amikor a málna Pi elfogadja a módosítást, a beállítás meg **szinkronizált** az Azure IoT-központ.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte a málna Pi csatlakoztatása az Azure IoT központi alkalmazás, az alábbiakban a javasolt lépéseket:

* [Általános Node.js ügyfélalkalmazást Azure IoT központi csatlakozás](howto-connect-nodejs.md)