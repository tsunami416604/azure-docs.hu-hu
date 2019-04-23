---
title: Eszközkezelés a csatlakoztatott távoli figyelési tömeges – Azure |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan kezelheti az egyszerre több távoli figyelési megoldáshoz csatlakoztatott eszközökre.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8a5c74c76662a089675fcbdcd8d5a7ea54b58fd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799038"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Oktatóanyag: A csatlakoztatott eszközök tömeges kezelése

Ebben az oktatóanyagban a távoli figyelési megoldásgyorsító kezelheti a csatlakoztatott eszközök tömeges konfigurációját használja.

Contoso kezelőként új verziójú belső vezérlőprogrammal működő eszközök egy csoportját konfigurálnia kell. Nem kívánja minden egyes eszköz belső vezérlőprogramjának frissítéséhez külön-külön kell. Egy csoport az eszközök belső vezérlőprogramjának frissítéséhez használhatja a távoli figyelési megoldásgyorsító eszközcsoportok és az automatikus kezelés. Bármilyen eszközön, az eszköz csoporthoz lekérdezi a a legújabb belső vezérlőprogrammal, amint az eszköz online állapotba kerül.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Eszközcsoport létrehozása
> * Készítse elő, és a belső vezérlőprogram üzemeltetése
> * Egy eszköz konfigurálásának létrehozása az Azure Portalon
> * Egy eszköz konfigurálásának importálja a távfelügyeleti megoldáshoz
> * A konfiguráció üzembe helyezése a az eszközök az eszközcsoport
> * A telepítés monitorozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell a távoli monitorozási megoldásgyorsító üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe a távoli monitorozási megoldásgyorsítót, végezze el [Távoli felhőalapú monitorozási megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid útmutatóban leírtakat.

A belső vezérlőprogram-fájlokat az Azure storage-fiókkal van szüksége. Használhat meglévő storage-fiókot, vagy [hozzon létre egy új tárfiókot](../storage/common/storage-quickstart-create-account.md) az előfizetésében.

Az oktatóanyagban egy [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) eszköz mintául szolgáló eszközt.

A következő szoftvereknek telepítve a helyi gépen lesz szüksége:

* [A Visual Studio Code (a VS Code)](https://code.visualstudio.com/).
* A [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code-bővítménnyel.

Előkészületek:

* Győződjön meg arról, a [IoT DevKit eszközén a rendszertöltő van verzió 1.4.0-s vagy újabb](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Ellenőrizze, hogy az IoT DevKit SDK jelenleg a rendszerbetöltőt verzióval azonos verziójúnak. Az IoT DevKit SDK a VS Code-ban az Azure IoT Workbench használatával frissítheti. A parancskatalógus megnyitásához, és adja meg **Arduino: Üzenőfal Manager**. További információkért lásd: [a fejlesztési környezet előkészítését](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Emellett szüksége IoT DevKit legalább egy eszköz csatlakozni a távoli figyelési megoldásgyorsító. Ha még nem kapcsolta egy IoT DevKit eszközt, tekintse meg [csatlakozás az MXChip IoT DevKit AZ3166, az IoT távoli figyelési megoldásgyorsító](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Lépjen az irányítópultra

A távoli monitorozási megoldás irányítópultjának megtekintéséhez először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#dashboard) területre. A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Ezután kattintson az **Indítás** elemre a [rövid útmutató](quickstart-remote-monitoring-deploy.md) során üzembe helyezett távoli monitorozási megoldásgyorsító csempéjén.

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Automatikusan az adott eszközcsoporton belső vezérlőprogramjának frissítéséhez, az eszközöknek a távoli figyelési megoldásban eszköz csoport tagjainak kell lenniük:

1. Az a **eszközök** lapon, válassza ki az összes a **IoT DevKit** a megoldásgyorsító csatlakozott eszközök. Ezután kattintson a **Jobs** (Feladatok) gombra.

1. Az a **feladatok** panelen kattintson **címkék**, állítsa a feladat nevet **AddDevKitTag**, majd adja hozzá a nevű szöveges címke **IsDevKitDevice** értékkel **Y**. Kattintson a **alkalmaz**.

1. Most már a címkeértékeket használatával hozzon létre egy eszközcsoportot. Az a **eszközök** kattintson **eszközcsoportok kezelése**.

1. A címke nevét használó szöveges szűrő létrehozása **IsDevKitDevice** és érték **Y** állapotban. Mentse az eszközcsoport, **IoT DevKit eszközök**.

Az oktatóanyag későbbi részében a következő eszközcsoporthoz a alkalmazni egy eszköz konfigurálásának, amely frissíti a belső vezérlőprogram az összes tag használhatja.

## <a name="prepare-and-host-the-firmware"></a>Készítse elő, és a belső vezérlőprogram üzemeltetése

A [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code-bővítmény tartalmaz az eszköz bemutató kódminta a belső vezérlőprogram frissítését.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Nyissa meg a belső vezérlőprogram OTA minta a VS Code-ban

1. Ellenőrizze, hogy az IoT DevKit nincs csatlakoztatva a számítógéphez. Indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Nyomja meg **F1** a parancskatalógus megnyitásához, írja be, és válassza ki **IoT Workbench: Példák**. Válassza ki **IoT DevKit** , a tábla.

1. Keresés **belső vezérlőprogram OTA** kattintson **nyílt minta**. Egy új VS Code-ablak nyílik meg, és megjeleníti a **firmware_ota** projektmappa:

    ![IoT Workbench, válassza ki a belső vezérlőprogram OTA példa](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Az új belső vezérlőprogram létrehozása

Az eszköz belső vezérlőprogramjának verziója kezdeti 1.0.0. Az új belső vezérlőprogram magasabb verziószámú kell rendelkeznie.

1. A VS Code-ban nyissa meg a **FirmwareOTA.ino** fájlt, és módosítsa a `currentFirmwareVersion` a `1.0.0` való `1.0.1`:

    ![Változás belső vezérlőprogram verziója](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Nyissa meg a parancskatalógust, majd írja be, és válassza ki **IoT Workbench: Eszköz**. Válassza ki **eszköz összeállításához** a kód fordítása:

    ![Eszköz fordítás](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    A VS Code menti a lefordított fájlt a `.build` mappát a projekt. A beállításoktól függően a VS Code elrejthetik a `.build` az Intézőben mappájában.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Hozza létre a CRC érték, és a belső vezérlőprogram méretének kiszámítása

1. Nyissa meg a parancskatalógust, majd írja be, és válassza ki **IoT Workbench: Eszköz**. Válassza ki **készítése CRC**:

    ![CRC készítése](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. A VS Code állít elő, és kinyomtatja a CRC érték, a belső vezérlőprogram fájlnév és elérési útja és a fájlméret, a kimeneti ablakban. Jegyezze fel ezeket az értékeket későbbi használatra:

    ![CRC adatai](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>A belső vezérlőprogram feltöltése a felhőbe

Az Azure storage-fiók használatával az új belső vezérlőprogram-fájl a felhőben futtatni.

1. Az Azure Portalon nyissa meg a tárfiókot. A szolgáltatások területen válassza ki a **Blobok**. Hozzon létre egy nyilvános tárolóban nevű **belső vezérlőprogram** a belső vezérlőprogram-fájlok tárolására:

    ![Mappa létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. A belső vezérlőprogram-fájl feltöltése a tárolóba, jelölje be a **belső vezérlőprogram** tárolóra, majd kattintson **feltöltése**.

1. Válassza ki a **FirmwareOTA.ino.bin**. Ez a fájl az előző szakaszban végrehajtott jegyezze fel a teljes elérési útja.

1. A belső vezérlőprogram fájl követően a feltöltés befejeződött, jegyezze meg a fájl URL-címe.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Hozhat létre és tölt fel az eredeti belső vezérlőprogram az IoT DevKit eszköz

1. A VS Code-ban nyissa meg a **FirmwareOTA.ino** fájlt, és módosítsa a `currentFirmwareVersion` vissza `1.0.0`:

    ![1.0.0-s verziójának](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Nyissa meg a parancskatalógust, majd írja be, és válassza ki **IoT Workbench: Eszköz**. Válassza ki **eszköz feltöltése**:

    ![Eszköz feltöltése](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. A VS Code ellenőrzi, és feltölti a kódot az IoT DevKit eszköz.

1. Ha a feltöltés befejezését követően az IoT DevKit eszköz újraindul. Az újraindítás befejeződése után az IoT DevKit képernyőjén látható **Keretrendszer verziója: 1.0.0-s**, és hogy az új belső vezérlőprogram ellenőrzi, hogy:

    ![OTA-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Hozzon létre egy eszköz konfigurálása

Egy eszköz konfigurációja meghatározza az eszközök állapotának. Általában egy fejlesztő [hoz létre a konfigurációs](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) a a **IoT-eszköz konfigurációs** oldal az Azure Portalon. Egy eszköz konfigurációs egy JSON-dokumentum, amely meghatározza az eszközök állapotának és a metrikák.

A következő konfiguráció mentése nevű fájlként **belső vezérlőprogram-update.json** a helyi gépen. Cserélje le a `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM`, és `YOURPACKAGESIZE` helyőrzőket a korábban végrehajtott jegyezze fel értékekkel:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Ezt a konfigurációs fájlt használja a következő szakaszban.

## <a name="import-a-configuration"></a>Konfiguráció importálása

Ebben a szakaszban Ön így importálja az eszköz konfigurációs csomag a távoli figyelési megoldásgyorsító. Az operátornak általában a Ez a feladat befejeződik.

1. A távoli figyelési webes felhasználói felületen, keresse meg a **csomagok** lapot, és kattintson **+ új csomag**:

    ![Új csomag](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Az a **új csomag** panel, válassza a **eszközkonfiguráció** csomag típusaként és **belső vezérlőprogram** konfigurációs típusaként. Kattintson a **Tallózás** keresése a **belső vezérlőprogram-update.json** fájlt a helyi gépen, és kattintson a **feltöltése**:

    ![Csomag feltöltése.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A csomagok listájában mostantól tartalmazza a **belső vezérlőprogram-frissítés** csomagot.

## <a name="deploy-the-configuration-to-your-devices"></a>A konfiguráció az eszközökre telepíteni kívánt

Ebben a szakaszban létre, és hajtsa végre a központi telepítés, amely az eszköz konfigurálása az IoT DevKit-eszközökre vonatkozik.

1. A távoli figyelési webes felhasználói felületen, keresse meg a **központi telepítések** lapot, és kattintson **+ új üzembe helyezési**:

    ![Új központi telepítés](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Az a **új üzembe helyezési** panelen, a központi telepítés létrehozásához a következő beállításokat:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Belső vezérlőprogram frissítésének telepítése|
    |Csomag típusa|Eszközkonfiguráció|
    |Konfiguráció típusa|Belső vezérlőprogram|
    |Csomag|firmware-update.json|
    |Eszközcsoport|IoT DevKit eszközök|
    |Prioritás|10|

    ![Üzemelő példány létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Kattintson az **Alkalmaz** gombra. Megjelenik az új központi telepítést a **központi telepítések** lap, amely megmutatja a következő metrikákat:

    * **Megcélzott** az eszköz csoportban található eszközök számát jeleníti meg.
    * **Alkalmazott** a konfigurációs tartalommal frissített eszközök számát jeleníti meg.
    * **Sikeres** a központi telepítési eszközök számát mutatja, hogy a jelentést sikerült.
    * **Nem sikerült** a központi telepítési eszközök számát mutatja, hogy a jelentést nem sikerült.

## <a name="monitor-the-deployment"></a>A telepítés monitorozása

Néhány perc elteltével az IoT DevKit az új belső vezérlőprogram adatait kérdezi le, és töltse le a az eszköz elindul:

![OTA-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

A hálózat sebességétől függően a letöltés előfordulhat, hogy eltarthat néhány percig. A belső vezérlőprogram letöltését követően az eszköz ellenőrzi a méretét és a CRC érték. Az MXChip képernyőn **átadott** Ha ellenőrzés sikeres.

![OTA – 3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Ha az ellenőrzés sikeres, az eszköz újraindul. Látja a visszaszámlálás **5** való **0** történik, az újraindítás előtt.

![OTA – 4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Az újraindítás után a IoT DevKit rendszertöltő az új verzióra frissíti a belső vezérlőprogram. A frissítés eltarthat néhány másodpercig. Ebben a szakaszban az eszközön a RGB LED piros pedig a képernyő üres.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Ha az újraindítás befejeződött, az IoT DevKit eszköz most már fut a belső vezérlőprogram 1.0.1-es verziója.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Az a **központi telepítések** lap, kattintson a központi telepítést, frissítheti azok az eszközök állapotának megtekintéséhez. Minden egyes eszköz állapota látható az eszköz csoport és a meghatározott egyéni metrikákat.

![Telepítés részletei](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan lehet a megoldáshoz csatlakoztatott eszközökre belső vezérlőprogramjának frissítéséhez. A megoldás frissítheti az eszközeit, használja az automatikus kezelés. A megoldás alapjául szolgáló IoT hub automatikus management szolgáltatásával kapcsolatos további tudnivalókért lásd: [konfigurálása és figyelése az Azure portal használatával nagy mennyiségű IoT-eszközök](../iot-hub/iot-hub-auto-device-config.md).