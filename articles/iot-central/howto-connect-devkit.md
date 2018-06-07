---
title: Egy DevKit eszköz csatlakoztatása az Azure IoT központi alkalmazás |} Microsoft Docs
description: Eszköz fejlesztőként útmutató egy MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT központi alkalmazás.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af5cfc2f598893328bc8d4acc979f6d777114f99
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628793"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Csatlakoztasson egy MXChip IoT DevKit eszközt az Azure IoT központi alkalmazás

Ez a cikk ismerteti, hogyan eszköz fejlesztőként MXChip IoT DevKit (DevKit) eszköz kapcsolódni a Microsoft Azure IoT központi alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. A létrehozott Azure IoT központi alkalmazást a **minta Devkits** alkalmazássablon. További információkért lásd: [létrehozása az Azure IoT központi alkalmazás](howto-create-application.md).
1. Egy DevKit eszközt. Vásároljon egy DevKit eszköz, látogasson el a [MXChip IoT DevKit](http://mxchip.com/az3166).

A létrehozott alkalmazást, a **minta Devkits** alkalmazás sablon tartalmaz egy **MXChip** eszköz sablon a következő jellemzőkkel:

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

### <a name="states"></a>állapotok 

| Name (Név)          | Megjelenített név   | NORMÁL | FIGYELMEZTETÉS | VESZÉLYE | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Az eszköz állapotát   | Zöld  | Narancssárga  | Piros    | 

### <a name="events"></a>Események 

| Name (Név)             | Megjelenített név      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | B gomb megnyomott állapota  | 

### <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT központi-alkalmazás hozzáadása a valódi eszközről a **MXChip** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterlánc. További információkért lásd: [valós eszköz hozzáadása az Azure IoT központi alkalmazás](tutorial-add-device.md).

## <a name="prepare-the-devkit-device"></a>A DevKit eszköz előkészítése

> [!TIP]
> DevKit eszköz hibaelhárítási útmutatót, lásd: [IoT DevKit Ismerkedés](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/).

Az DevKit eszköz előkészítése:

1. Töltse le a legújabb előre elkészített Azure IoT központi belső vezérlőprogramját a MXChip a [kiadott](https://github.com/Azure/iot-central-firmware/releases) lap a Githubon. A letöltési fájlnév, a kiadások lapra a következőhöz hasonló `AZ3166-IoT-Central-X.X.X.bin`.

1. Csatlakoztassa a fejlesztői számítógépén, USB-kábelen keresztül a DevKit eszközt. A Windows a fájl explorer ablak leképezve a tárolási az DevKit eszközön a meghajtón. Például a meghajtó neve lehet **AZ3166 (D:)**.

1. Húzza a **iotCentral.bin** a meghajtó ablak fájlt. A másolási befejeződése után az eszköz újraindul, új belső vezérlőprogramot.

1. A DevKit eszköz újraindításakor a következő képernyőt jeleníti meg:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Ha a képernyőn látható bármi más, nyomja meg a **alaphelyzetbe** gomb az eszközön. 

1. Az eszköz most már hozzáférési pont (AP) módban van. A WiFi-hozzáférési pont csatlakozhat a számítógépről vagy a mobileszközön.

1. A számítógép telefonját vagy táblagépét csatlakozzon a jelenik meg, az eszköz Wi-Fi hálózat nevét. Ehhez a hálózathoz való csatlakozáskor nincs internet-hozzáféréssel. Ebben az állapotban egy várható üzenet, és csak csatlakozik ehhez a hálózathoz, rövid időre, amíg az eszköz konfigurálásához.

1. Nyissa meg a webböngészőt, és keresse meg [ http://192.168.0.1/start ](http://192.168.0.1/start). A következő weblap jelenik meg:

    ![Eszköz konfigurációs lapján](media/howto-connect-devkit/configpage.png)

    A weblap: 
    - Adja hozzá a Wi-Fi hálózat nevét 
    - a Wi-Fi hálózati jelszót 
    - PIN-kód jelenik meg az eszköz LCD 
    - az eszköz kapcsolati karakterlánca. 
      A kapcsolati karakterláncot a @ található `https://apps.iotcentral.com`  ->  `Device Explorer`  ->  `Device`  ->  `Select or Create a new Real Device`  ->  `Connect this device` (a jobb felső sarokban) 
    - Válassza ki az összes elérhető telemetriai adat mérési! 

1. A választott **eszköz konfigurálása**, ez az oldal akkor jelenik meg:

    ![Eszköz konfigurálva](media/howto-connect-devkit/deviceconfigured.png)

1. Nyomja meg a **alaphelyzetbe** gomb az eszközön.

> [!NOTE]
> Konfigurálja újra az eszközt egy másik Wi-Fi hálózatot, a kapcsolati karakterlánc vagy a telemetriai mérési, nyomja meg mind a **A** és **B** gombok a táblán egy időben. Ha nem működik, nyomja meg az **alaphelyzetbe** gombra, majd próbálja meg újból. 

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

Ha a DevKit eszköz újraindul, az eszközön a képernyőn látható:

* Küldött telemetriai üzenetek száma.
* A hibák száma.
* A fogadott kívánt tulajdonságok száma és a küldött jelentésben szereplő tulajdonságok száma.

Rázza meg az eszköz növekmény küldött jelentésben szereplő tulajdonságok száma. Az eszköz küld egy véletlenszerű számot, a **Die számú** eszköz tulajdonság.

A telemetriai adatok mérések és a jelentett tulajdonságértékek megtekintése, és beállítások konfigurálása az Azure IoT-központ:

1. Használjon **eszköz Explorer** lehetőségre, és navigáljon a **mérések** a valódi MXChip eszköz hozzáadott oldalon:

    ![Navigáljon a valódi eszköz](media/howto-connect-devkit/realdevice.png)

1. Az a **mérések** lapon megtekintheti az MXChip eszközről érkező telemetriai adatok:

    ![Nézet telemetriai valós eszközről](media/howto-connect-devkit/realtelemetry.png)

1. Az a **tulajdonságok** lapon megtekintheti az utolsó száma az eszköz által jelzett:

    ![Az eszköz tulajdonságai](media/howto-connect-devkit/deviceproperties.png)

1. Az a **beállítások** lapján frissítheti a MXChip eszközön található beállítások:

    ![Eszköz beállításainak megjelenítése](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>A forráskód letöltése

Ha azt szeretné, vizsgálatát, és a kód módosítására, letöltheti a Githubról. Ha azt tervezi, a kód módosítására, kövesse ezeket az utasításokat [készítse elő a fejlesztési környezetet](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) az asztali operációs rendszerhez.

A forráskód letöltéséhez a asztali számítógépen futtassa a következő parancsot:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Az előző parancs letölti a forráskód mappába `iot-central-firmware`. 

> [!NOTE]
> Ha **git** nincs telepítve a fejlesztői környezetben, letöltheti a [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>A kód áttekintése

Visual Studio Code, amelyek telepítették a fejlesztési környezetet, nyissa meg a felkészülés során használja a `AZ3166` mappájában a `iot-central-firmware` mappába: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Hogyan van küldött telemetriai adatok az Azure IoT központi alkalmazás megtekintéséhez nyissa meg a **main_telemetry.cpp** forrásmappában lévő fájlt.

A függvény `buildTelemetryPayload` hoz létre a JSON telemetriai forgalma az érzékelők adatait használó az eszközön.

A függvény `sendTelemetryPayload` hívások `sendTelemetry` a a **iotHubClient.cpp** a JSON-adattartalmat küldeni az IoT-központ a Azure IoT központi alkalmazás használ.

Hogyan tulajdonságértékek jelentik az Azure IoT központi alkalmazás megtekintéséhez nyissa meg a **main_telemetry.cpp** forrásmappában lévő fájlt.

A függvény `telemetryLoop` elküldi a **doubleTap** jelentett tulajdonság, amikor a gyorsulásmérő észleli a dupla koppintson. Használja a `sendReportedProperty` működni a **iotHubClient.cpp** forrásfájl.

A kód a **iotHubClient.cpp** forrásfájl funkciókat használ a [ Microsoft Azure IoT SDK-k és C-könyvtárakban](https://github.com/Azure/azure-iot-sdk-c) IoT-központ kommunikál.

Módosíthatja, elkészítéséhez és töltse fel a mintakódot az eszköz kapcsolatos információkért lásd: a **readme.md** fájlt a `AZ3166` mappát.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a DevKit eszköz csatlakoztatása az Azure IoT központi alkalmazás rendelkezik, az alábbiakban a javasolt lépéseket:

* [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md)