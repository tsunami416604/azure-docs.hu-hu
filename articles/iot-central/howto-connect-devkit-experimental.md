---
title: Egy fejlesztői készlet eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Eszköz a fejlesztők megtudhatja, hogyan az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1097d14c824bcdebb9062fcf63b04e9837b0d170
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811613"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Az MXChip IoT DevKit eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők az MXChip IoT fejlesztői készlet (DevKit) eszköz csatlakoztatása a Microsoft Azure IoT Central alkalmazáshoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

1. A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
1. Egy fejlesztői készlet eszköz. DevKit eszköz vásárol, a Microsoft [MXChip IoT DevKit](http://mxchip.com/az3166).

## <a name="sample-devkits-application"></a>Devkits mintaalkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **MXChip** eszköz sablon a következő jellemzőkkel:

- Telemetriai adatokat, amely tartalmazza az eszköz a mérések **páratartalom**, **hőmérséklet**, **nyomás**, **magnetométer** (mérése X, Y, mentén Z tengely), **érzékelőből** (X, Y, mentén mért Z tengely) és **Giroszkóp** (X, Y, mentén mért Z tengely).
- Állapot, amely tartalmaz egy példa meghatározásáért **Eszközállapot**.
- Az esemény mérési egy **B bekapcsolva** esemény. 
- Beállítások megjelenítése **feszültség**, **aktuális**, **ventilátor sebesség**, és a egy **integrációs modul** be-vagy kikapcsolása.
- Eszköztulajdonság tartalmazó tulajdonságok **die száma** és **eszköz helye** vagyis a location tulajdonság, valamint az egy **gyártott a** felhőbeli tulajdonság. 

A konfiguráció teljes részletekért tekintse meg [MXChip eszköz sablon részletei](#mxchip-device-template-details)


## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **MXChip** eszköz sablont, és jegyezze fel az eszköz kapcsolat részleteinek (**hatókör azonosítója, az eszköz Azonosítóját és az elsődleges kulcs**).

1. Adjon hozzá egy **valós eszköz** Device Explorer, kattintson a **+ új > valós** valós eszköz hozzáadásához.

    * Adja meg az eszköz azonosítója **<span style="color:Red">(kell lennie a kisbetűs)</span>** vagy a javasolt eszközazonosítót használ.
    * Adja meg az eszköz nevét, vagy használja a javasolt név

    ![Eszköz hozzáadása](media/howto-connect-devkit-experimental/add-device.png)

1. Például beolvasni a kapcsolat adatait **hatókör azonosítója, az eszköz Azonosítóját és az elsődleges kulcs** kattintva az új eszköz **Connect** az eszköz oldalon.

    ![Kapcsolat adatai](media/howto-connect-devkit-experimental/device-connect.png)

1. Ügyeljen arra, hogy ezek az adatok mentése, akkor lesz ideiglenesen első kapcsolódik az internethez az DevKit eszköz előkészítése.

### <a name="prepare-the-devkit-device"></a>A fejlesztői készlet eszköz előkészítése

> [!NOTE]
> Ha az eszköz korábban használt, és rendelkezik a Wi-Fi hitelesítő adatokat tárolja, és konfigurálja újra az eszközt egy másik Wi-Fi hálózatot, a kapcsolati karakterlánc vagy a telemetriai mérési szeretne, nyomja le az mind a **A** és **B** egyidejűleg gombokat a táblán. Ha nem működik, nyomja le az **alaphelyzetbe** gombra, és próbálkozzon újra.

#### <a name="to-prepare-the-devkit-device"></a>Az DevKit eszköz előkészítése

1. Az MXChip a a legújabb előregyártott Azure IoT Central belső vezérlőprogram letöltése a [kiadások](https://aka.ms/iotcentral-docs-MXChip-releases) lapját a githubon.
1. A fejlesztői készlet eszköz csatlakoztatása a fejlesztői gépén, USB-kábel segítségével. Windows, a fájl explorer megnyílik egy ablak a tárolót a fejlesztői készlet eszközön leképezve meghajtón. Ha például a meghajtó neve lehet **AZ3166 (D:)**.
1. Húzza a **iotCentral.bin** fájlt arra a meghajtóra ablakot. A másolás befejeződése után az eszköz újraindul, új belső vezérlőprogramját.

1. A fejlesztői készlet eszköz újraindításakor a következő képernyő jelenik meg:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > A képernyőn megjelenik az bármi más, ha alaphelyzetbe az eszközt, majd kattintson a **A** és **B** gombok használatát az eszközön, az eszköz újraindítását egy időben.

1. Az eszköz most már hozzáférési pont (AP) módban van. A Wi-Fi hozzáférési pont a számítógép vagy mobileszköz csatlakozhat.

1. A számítógépen telefonon vagy táblagépen kapcsolódni a Wi-Fi hálózat nevét, az eszköz képernyőjén látható. Ha ehhez a hálózathoz csatlakozik, nem rendelkezik internet-hozzáféréssel. Ebben az állapotban várható és csak csatlakozik ehhez a hálózathoz, rövid időre az eszköz konfigurálása közben.

1. Nyissa meg a webböngészőjét, és navigáljon a [ http://192.168.0.1/start ](http://192.168.0.1/start). A következő weblapon megjelenik:

    ![Eszköz konfigurációs lapján](media/howto-connect-devkit-experimental/configpage.png)

    A weblap: 
    - Adja hozzá a Wi-Fi hálózat nevét 
    - a Wi-Fi-hálózat jelszavát
    - PIN-kódot az eszközön LCD látható 
    - a kapcsolat adatai **hatókör azonosítója, az eszköz azonosítóját és az elsődleges kulcs** eszköze (kell már mentette-e a következő lépéseket követve)      
    - Válassza ki az összes elérhető telemetriai adat-mérést. 

1. Miután kiválasztotta **konfigurálása eszköz**, ezt oldal jelenik meg:

    ![Eszköz konfigurálva](media/howto-connect-devkit-experimental/deviceconfigured.png)

1. Nyomja le az **alaphelyzetbe** gomb az eszközön.


## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

A fejlesztői készlet eszköz újraindításakor az eszközön a képernyőn látható:

* A telemetriai adatokat küldött üzenetek száma.
* Hibák száma.
* A kapott kívánt tulajdonságok száma és a jelentett tulajdonságok számát.

> [!NOTE]
> Ha az eszköz megjelenik a connect-ellenőrzés során hurkolás lehet, ha az eszköz *letiltott* az IoT-központ és *feloldása* az eszköz így képes csatlakozni az alkalmazáshoz.

Rázza meg az eszköz a jelentett tulajdonságok száma növekmény. Az eszköz küld egy véletlenszerű számot, a **Die szám** eszköztulajdonság.

A telemetriai adatok mérések és a jelentett tulajdonságértékeket megtekintése, és a beállítások konfigurálása az Azure IoT Central:

1. Használat **Device Explorer** navigálhat a **mérések** lapját valós MXChip hozzáadott:

    ![Navigáljon a valódi eszköz](media/howto-connect-devkit-experimental/realdevicenew.png)

1. Az a **mérések** lapon megtekintheti az MXChip eszközről érkező telemetriai adatok:

    ![Valódi eszköz telemetriájának megtekintése](media/howto-connect-devkit-experimental/devicetelemetrynew.png)

1. Az a **tulajdonságok** lapon megtekintheti a legutóbbi száma és az eszköz helye, az eszköz által jelentett:

    ![Az eszköz tulajdonságai](media/howto-connect-devkit-experimental/devicepropertynew.png)

1. Az a **beállítások** lapon módosíthatja a beállításokat az MXChip eszközön:

    ![Eszköz-beállítások megjelenítése](media/howto-connect-devkit-experimental/devicesettingsnew.png)

1. Az a **irányítópult** lapon láthatja a hely hozzárendelése

    ![Eszköz-irányítópult megtekintése](media/howto-connect-devkit-experimental/devicedashboardnew.png)


## <a name="download-the-source-code"></a>Letöltheti a forráskódot

Ha azt szeretné, és módosítsa az eszköz kódot, letöltheti a Githubról. Ha azt tervezi, a kód módosítása, kövesse ezeket az utasításokat [a fejlesztési környezet előkészítését](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) az asztali operációs rendszerének.

Letöltheti a forráskódot, az asztali gépen futtassa a következő parancsot:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Az előző parancs letölti a forráskód nevű mappába `iot-central-firmware`. 

> [!NOTE]
> Ha **git** nincs telepítve a fejlesztési környezet töltheti le a [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>A kód áttekintése

Használja a Visual Studio Code-ban, nyissa meg a fejlesztőkörnyezet előkészítése során lett telepítve, amely a `AZ3166` mappájában a `iot-central-firmware` mappa: 

![Visual Studio Code](media/howto-connect-devkit-experimental/vscodeview.png)

Hogyan a telemetriája el lesz küldve az Azure IoT Central alkalmazásnak megtekintéséhez nyissa meg a **main_telemetry.cpp** a forrásmappában található fájl.

A függvény `buildTelemetryPayload` hoz létre a telemetria hasznos JSON-az érzékelőkről származó adatok az eszközön.

A függvény `sendTelemetryPayload` hívások `sendTelemetry` a a **iotHubClient.cpp** a JSON-adattartalom küldése az IoT hubra az Azure IoT központi alkalmazás használja.

Hogyan jelentett tulajdonságértékeket az Azure IoT Central alkalmazásnak megtekintéséhez nyissa meg a **main_telemetry.cpp** a forrásmappában található fájl.

A függvény `telemetryLoop` küld a **doubleTap** jelentett tulajdonságot, ha a érzékelőből észlel olyan dupla koppintással. Használja a `sendReportedProperty` működni a **iotHubClient.cpp** forrásfájl.

A kód a **iotHubClient.cpp** forrásfájl funkciókat használ a [ a Microsoft Azure IoT SDK-k és tárak a c nyelvhez készült](https://github.com/Azure/azure-iot-sdk-c) kommunikáljon az IoT Hub.

Módosítására, hozhat létre, és töltse fel a mintakódot az eszköz kapcsolatos információkért tekintse meg a **readme.md** fájlt a `AZ3166` mappát.

## <a name="mxchip-device-template-details"></a>Az MXChip eszköz sablon részletei

A minta Devkits alkalmazást sablon alapján létrehozott alkalmazás tartalmaz egy MXChip eszköz sablont a következő jellemzőkkel:

### <a name="measurements"></a>Mérések

#### <a name="telemetry"></a>Telemetria 

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


#### <a name="states"></a>Állapotok 
| Name (Név)          | Megjelenített név   | NORMÁL | FIGYELMEZTETÉS | DANGER | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Eszköz állapota   | Zöld  | Narancssárga  | Piros    | 

#### <a name="events"></a>Események 
| Name (Név)             | Megjelenített név      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | B gomb megnyomásakor  | 

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
| Eszköztulajdonság | Eszköz helye   | location  | location    |
| Szöveg            | Az előállított     | manufacturedIn   | –       |



## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan DevKit eszköz csatlakozhat az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépések:

* [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
