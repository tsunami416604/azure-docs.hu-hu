---
title: Fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: A MXChip IoT fejlesztői készlet-eszközök Azure IoT Central-alkalmazáshoz való csatlakoztatását bemutató útmutató.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4e04ae7d9594ac064c9f3707c797fb2709a79cb6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582957"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>MXChip-IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk a MXChip IoT fejlesztői készlet (fejlesztői készlet) eszköznek a Microsoft Azure IoT Central alkalmazáshoz való csatlakoztatását ismerteti.

## <a name="before-you-begin"></a>Előzetes teendők

A cikkben szereplő lépések végrehajtásához a következő erőforrásokra van szükség:

1. A **mintául szolgáló Devkits** létrehozott Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
1. Egy fejlesztői készlet-eszköz. Fejlesztői készlet-eszköz vásárlásához látogasson el a [MXChip IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Példa Devkits alkalmazásra

A **mintául szolgáló Devkits** létrehozott alkalmazás tartalmaz egy **MXChip** , amely meghatározza a következő eszköz jellemzőit:

- Telemetria mérések a **páratartalom**, a **hőmérséklet**, a **nyomás**, a **Magnetometer** (x, y, z tengely mentén mérve), **gyorsulásmérő** (x, y, z tengely mentén mérve) és **giroszkóp** (x, y, z tengely mentén mérve).
- Állapot mérése az **eszköz állapotához**.
- A B gomb esemény-mérése **megnyomva**.
- A **feszültség**, a **jelenlegi**, a **ventilátor sebessége**és az **IR** -váltás beállításai.
- Az eszköz tulajdonságainak **száma** és az **eszköz helye**, amely egy Location tulajdonság.
- **A-ben előállított**Cloud Property.
- Parancsok **visszhangja** és **visszaszámlálása**. Ha egy valós eszköz egy **echo** -parancsot kap, az elküldött érték jelenik meg az eszköz képernyőjén. Ha egy valós eszköz **visszaszámlálási** parancsot kap, a rendszer egy minta alapján a LED-ciklusokat, az eszköz pedig visszaszámlálási értékeket küld vissza IoT Central.

A konfiguráció részletes ismertetését lásd: [MXChip-eszköz sablonjának részletei](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

### <a name="get-your-device-connection-details"></a>Az eszköz kapcsolati adatainak beolvasása

Az Azure IoT Central alkalmazásban vegyen fel egy valódi eszközt a **MXChip** -eszköz sablonból, és jegyezze fel az eszköz kapcsolatának részleteit: a **hatókör-azonosítót, az eszköz azonosítóját és az elsődleges kulcsot**:

1. Valódi eszköz hozzáadásához vegyen fel egy **valós eszközt** a Device Explorerból, és válassza az **+ új > valós** lehetőséget.

    * Adja meg a kisbetűs **eszköz azonosítóját**, vagy használja a javasolt **eszköz azonosítóját**.
    * Adja meg az **eszköz nevét**, vagy használja a javasolt nevet

    ![Eszköz hozzáadása](media/howto-connect-devkit/add-device.png)

1. Az eszköz kapcsolati adatainak, a **hatókör azonosítójának**, az **eszköz azonosítójának**és az **elsődleges kulcsnak**a beszerzéséhez válassza a **Kapcsolódás** lehetőséget az eszköz oldalon.

    ![Kapcsolat adatai](media/howto-connect-devkit/device-connect.png)

1. Jegyezze fel a kapcsolat részleteit. A következő lépésben a fejlesztői készlet-eszköz előkészítésekor átmenetileg le van választva az internetről.

### <a name="prepare-the-devkit-device"></a>A fejlesztői készlet eszköz előkészítése

Ha korábban már használta az eszközt, és újra szeretné konfigurálni egy másik WiFi-hálózat, a kapcsolódási karakterlánc vagy a telemetria-mérés használatára, nyomja meg egyszerre az **a és a** **B** gombokat is. Ha nem működik, nyomja le az **Alaphelyzetbe állítás** gombot, és próbálkozzon újra.

#### <a name="to-prepare-the-devkit-device"></a>A fejlesztői készlet-eszköz előkészítése

1. Töltse le a legújabb, előre elkészített Azure IoT Central belső vezérlőprogramot a MXChip a GitHubon a [kiadások](https://aka.ms/iotcentral-docs-MXChip-releases) oldaláról.
1. Csatlakoztassa a fejlesztői készlet eszközt a fejlesztői számítógéphez egy USB-kábellel. A Windowsban egy fájlkezelő ablak nyílik meg a fejlesztői készlet eszközön lévő tárolóhoz rendelt meghajtón. Előfordulhat például, hogy a meghajtó neve **AZ3166 (D:)** .
1. Húzza a **iotCentral. bin** fájlt a meghajtó ablakára. A másolás befejezésekor az eszköz újraindul az új belső vezérlőprogram.

1. A fejlesztői készlet-eszköz újraindításakor a következő képernyő jelenik meg:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Ha a képernyőn bármilyen más látható, állítsa alaphelyzetbe az eszközt, és nyomja le az eszközön az **a** és **B** gombokat az eszköz újraindításához.

1. Az eszköz most már hozzáférési pont (AP) módban van. Ehhez a Wi-Fi-elérési ponthoz csatlakozhat a számítógépről vagy mobileszközön.

1. A számítógépén, telefonján vagy táblaszámítógépén az eszköz képernyőjén megjelenő WiFi-hálózat nevére kell csatlakoznia. Ha csatlakozik ehhez a hálózathoz, nem rendelkezik internet-hozzáféréssel. Ez az állapot várható, és az eszköz konfigurálásakor csak rövid idő alatt csatlakozik ehhez a hálózathoz.

1. Nyissa meg a webböngészőt, és navigáljon [http://192.168.0.1/start](http://192.168.0.1/start). A következő weblap jelenik meg:

    ![Eszköz konfigurációja lap](media/howto-connect-devkit/configpage.png)

    A weblapon írja be a következőt:
    - A WiFi-hálózat neve
    - A WiFi hálózati jelszava
    - Az eszköz képernyőjén megjelenő PIN-kód
    - A kapcsolat részleteinek **hatókör-azonosítója**, az **eszköz azonosítója**és az eszköz **elsődleges kulcsa** (a lépéseket a következő lépésekkel kell elmentenie)
    - Az összes rendelkezésre álló telemetria-mérés kiválasztása

1. Az **eszköz konfigurálása**lehetőség kiválasztása után a következő oldal jelenik meg:

    ![Eszköz konfigurálva](media/howto-connect-devkit/deviceconfigured.png)

1. Nyomja meg az eszköz **alaphelyzetbe állítása** gombot.

## <a name="view-the-telemetry"></a>A telemetria megtekintése

A fejlesztői készlet-eszköz újraindításakor az eszköz képernyője a következőt jeleníti meg:

* Az elküldött telemetria-üzenetek száma.
* A hibák száma.
* A kapott kívánt tulajdonságok száma és az elküldött jelentett tulajdonságok száma.

> [!NOTE]
> Ha az eszköz úgy tűnik, hogy a kapcsolódáshoz próbál csatlakozni, ellenőrizze, hogy az eszköz le van-e **tiltva** a IoT Centralban, és oldja fel az eszköz **zárolását** , hogy az alkalmazáshoz kapcsolódjon.

A jelentett tulajdonság elküldéséhez rázza meg az eszközt. Az eszköz véletlenszerűen kiválasztott számot küld a **Die Number** eszköz tulajdonságának.

Megtekintheti a telemetria-méréseket és a jelentett tulajdonságértékeket, valamint konfigurálhatja a beállításokat az Azure IoT Centralban:

1. Az **eszközök** segítségével navigáljon a hozzáadott valós MXChip-eszköz **mérések** lapjára:

    ![Navigáljon a valódi eszközre](media/howto-connect-devkit/realdevicenew.png)

1. A **mérések** lapon megtekintheti az MXChip-eszközről érkező telemetria:

    ![Valós eszközről származó telemetria megtekintése](media/howto-connect-devkit/devicetelemetrynew.png)

1. A **Tulajdonságok** lapon megtekintheti a legutóbbi meghaló számot és az eszköz által jelentett helyet:

    ![Eszköz tulajdonságainak megtekintése](media/howto-connect-devkit/devicepropertynew.png)

1. A **Beállítások** lapon frissítheti a MXChip eszköz beállításait:

    ![Eszközbeállítások megtekintése](media/howto-connect-devkit/devicesettingsnew.png)

1. A **parancsok** lapon meghívhatja az **echo** és a **Countdown** parancsokat:

    ![Hívási parancsok](media/howto-connect-devkit/devicecommands.png)

1. Az **irányítópult** lapon a hely leképezése látható

    ![Eszköz irányítópultjának megtekintése](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Forráskód letöltése

Ha szeretné felderíteni és módosítani az eszköz kódját, letöltheti azt a GitHubról. Ha azt tervezi, hogy módosítja a kódot, kövesse az alábbi utasításokat, és [készítse elő a fejlesztési környezetet](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) az asztali operációs rendszer számára.

A forráskód letöltéséhez futtassa a következő parancsot az asztali gépen:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Az előző parancs letölti a forráskódot egy `iot-central-firmware`nevű mappába.

> [!NOTE]
> Ha a **git** nincs telepítve a fejlesztői környezetben, letöltheti [https://git-scm.com/downloadról ](https://git-scm.com/download).

## <a name="review-the-code"></a>A kód áttekintése

A Visual Studio Code segítségével nyissa meg a `MXCHIP/mxchip_advanced` mappát a `iot-central-firmware` mappában:

![Visual Studio-kód](media/howto-connect-devkit/vscodeview.png)

Ha szeretné megtudni, hogyan küldik el az telemetria az Azure IoT Central alkalmazásnak, nyissa meg a **telemetria. cpp** fájlt a `src` mappában:

- A függvény `TelemetryController::buildTelemetryPayload` létrehozza a JSON-telemetria adattartalmat az eszköz érzékelőkből származó adatok használatával.

- A függvény `TelemetryController::sendTelemetryPayload` hív `sendTelemetry` a **AzureIOTClient. cpp** fájlban, hogy elküldje a JSON-adattartalmat az Azure IoT Central-alkalmazás által használt IoT hub.

Ha szeretné megtudni, hogyan történik a tulajdonságértékek jelentése az Azure IoT Central alkalmazásnak, nyissa meg a **telemetria. cpp** fájlt a `src` mappában:

- Az `TelemetryController::loop` függvény körülbelül 30 másodpercenként küldi el a **hely** jelentett tulajdonságát. A **AzureIOTClient. cpp** forrásfájl `sendReportedProperty` függvényét használja.

- A függvény `TelemetryController::loop` elküldi a **dieNumber** jelentett tulajdonságot, amikor az eszköz gyorsulásmérője dupla koppintást észlel. A **AzureIOTClient. cpp** forrásfájl `sendReportedProperty` függvényét használja.

Ha szeretné megtekinteni, hogy az eszköz hogyan válaszol a IoT Central alkalmazásban hívott parancsokra, nyissa meg a **registeredMethodHandlers. cpp** fájlt a `src` mappában:

- A **dmEcho** függvény az **echo** parancs kezelője. Megjeleníti az eszköz képernyőjén található adattartalomban megadott **displayedValue** .

- A **dmCountdown** függvény a **visszaszámlálási** parancs kezelője. Megváltoztatja az eszköz LED-ének színét, és egy jelentett tulajdonságot használ a visszaszámlálási érték visszaküldéséhez a IoT Central alkalmazásnak. A jelentett tulajdonság neve megegyezik a paranccsal. A függvény a **AzureIOTClient. cpp** forrásfájl `sendReportedProperty` függvényét használja.

A **AzureIOTClient. cpp** forrásfájl kódja a [Microsoft Azure IoT SDK-k és a C-könyvtárak](https://github.com/Azure/azure-iot-sdk-c) függvényeit használja a IoT hub való interakcióhoz.

A mintakód eszközre történő módosításával, létrehozásával és feltöltésével kapcsolatos további információkért tekintse meg a **readme.MD** fájlt a `MXCHIP/mxchip_advanced` mappában.

## <a name="mxchip-device-template-details"></a>MXChip-eszköz sablonjának részletei

A mintául szolgáló Devkits létrehozott alkalmazás tartalmaz egy MXChip-eszközt, amely a következő tulajdonságokkal rendelkezik:

### <a name="measurements"></a>Mérések

#### <a name="telemetry"></a>Telemetria

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| ideiglenes           | °C     | – 40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1000    | 0              |
| accelerometerX | mg     | – 2000   | 2000    | 0              |
| gyorsulásmérő | mg     | – 2000   | 2000    | 0              |
| accelerometerZ | mg     | – 2000   | 2000    | 0              |
| gyroscopeX     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeY     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | – 2000   | 2000    | 0              |

#### <a name="states"></a>Állapotok 
| Name (Név)          | Megjelenített név   | NORMÁL | Vigyázat | VESZÉLY | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Eszközállapot   | Zöld  | Narancssárga  | Piros    | 

#### <a name="events"></a>Események 
| Name (Név)             | Megjelenített név      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | B gomb lenyomva  | 

### <a name="settings"></a>Beállítások

Numerikus beállítások

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültség      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Erősítők  | 0              | 0       | 100     | 0       |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Váltási beállítások

| Megjelenített név | Mező neve | Szövegen | Kikapcsolt szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | KIKAPCSOLÁSA      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Data type |
| --------------- | ------------ | ---------- | --------- |
| Eszköz tulajdonsága | Die száma   | dieNumber  | szám    |
| Eszköz tulajdonsága | Eszköz helye   | location  | location    |
| Szöveg            | Gyártás folyamatban     | manufacturedIn   | N/A       |

### <a name="commands"></a>Parancsok

| Megjelenített név | Mező neve | Visszatérési típus | Beviteli mező megjelenítendő neve | Beviteli mező neve | Beviteli mező típusa |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo       | szöveg        | megjelenítendő érték         | displayedValue   | szöveg             |
| Visszaszámlálás    | Visszaszámlálás  | szám      | Darabszám               | countFrom        | szám           |

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakozhat egy MXChip IoT-fejlesztői készlet az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, hogyan [állíthat be egyéni eszközöket](howto-set-up-template.md) a saját IoT-eszközéhez.
