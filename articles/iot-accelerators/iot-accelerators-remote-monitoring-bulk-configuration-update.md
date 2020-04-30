---
title: Távoli figyeléshez csatlakoztatott eszközök tömeges kezelése – Azure | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan kezelheti a távoli figyelési megoldáshoz kapcsolódó eszközöket tömegesen.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77565578"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Oktatóanyag: a csatlakoztatott eszközök tömeges kezelése

Ebben az oktatóanyagban a távoli figyelési megoldás-gyorsító használatával kezelheti a csatlakoztatott eszközök tömeges konfigurációját.

A contoso operátorként egy új belső vezérlőprogram-verzióval rendelkező eszköz-csoportot kell konfigurálnia. Nem szeretné, hogy mindegyik eszközön külön-külön frissítse a belső vezérlőprogramot. Az eszközök egy csoportjára vonatkozó belső vezérlőprogram frissítéséhez használhatja az eszközöket és az automatikus eszközkezelés szolgáltatást a távoli figyelési megoldás gyorsító eszközében. Minden eszköz, amelyet hozzáad az erőforráscsoporthoz, beolvassa a legújabb belső vezérlőprogramot, amint az eszköz online állapotba kerül.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Eszközcsoport létrehozása
> * A belső vezérlőprogram előkészítése és üzemeltetése
> * Eszköz konfigurációjának létrehozása a Azure Portalban
> * Eszköz konfigurációjának importálása a távoli figyelési megoldásba
> * A konfiguráció üzembe helyezése az eszközön az eszközök csoportban
> * A telepítés monitorozása

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell a távoli monitorozási megoldásgyorsító üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe a távoli monitorozási megoldásgyorsítót, végezze el [Távoli felhőalapú monitorozási megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid útmutatóban leírtakat.

Szüksége lesz egy Azure Storage-fiókra a belső vezérlőprogram fájljainak tárolásához. Használhat meglévő Storage-fiókot, vagy [létrehozhat egy új Storage-fiókot](../storage/common/storage-account-create.md) az előfizetésében.

Az oktatóanyag egy [IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/) eszközt használ minta eszközként.

A következő szoftverekre van szükség a helyi gépen:

* [Visual Studio Code (vs Code)](https://code.visualstudio.com/).
* Az [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) vs Code bővítmény.

Előkészületek:

* Győződjön meg arról [, hogy a IoT fejlesztői készlet-eszközön található rendszerbetöltő a 1.4.0 vagy újabb verzióban van](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Győződjön meg arról, hogy a IoT fejlesztői készlet SDK a rendszerbetöltővel megegyező verzióban található. A IoT fejlesztői készlet SDK-t a VS Code-ban az Azure IoT Workbench használatával frissítheti. Nyissa meg a parancssort, és írja be az **Arduino: Board Manager**parancsot. További információ: [a fejlesztési környezet előkészítése](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Emellett legalább egy IoT fejlesztői készlet-eszközt is csatlakoznia kell a távoli figyelési megoldáshoz. Ha még nem csatlakoztatott egy IoT fejlesztői készlet-eszközt, tekintse [meg a MXChip IoT fejlesztői készlet AZ3166 csatlakoztatása a IoT távoli figyelési megoldásának gyorsítása](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)című témakört.

## <a name="navigate-to-the-dashboard"></a>Lépjen az irányítópultra

A távoli monitorozási megoldás irányítópultjának megtekintéséhez először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#dashboard) területre. A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Ezután kattintson az **Indítás** elemre a [rövid útmutató](quickstart-remote-monitoring-deploy.md) során üzembe helyezett távoli monitorozási megoldásgyorsító csempéjén.

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Az eszközök egy csoportján lévő belső vezérlőprogram automatikus frissítéséhez az eszközöknek a távoli figyelési megoldásban lévő eszközcsoport tagjainak kell lenniük:

1. Az **eszközök** lapon válassza ki az összes olyan **IoT-fejlesztői készlet** -eszközt, amelyhez a megoldás-gyorsító csatlakozik. Ezután kattintson a **Jobs** (Feladatok) gombra.

1. A **feladatok** panelen válassza a **címkék**lehetőséget, állítsa a feladat nevét **AddDevKitTag**értékre, majd adjon hozzá egy **IsDevKitDevice** nevű szöveges címkét **Y**értékkel. Ezután kattintson az **alkalmaz**gombra.

1. Most már használhatja a címke értékeit az eszközcsoport létrehozásához. Az **eszközök** lapon kattintson az **erőforráscsoportok kezelése**elemre.

1. Hozzon létre egy olyan szöveges szűrőt, amely a feltételben a **IsDevKitDevice** és az **Y** értéket használja. Mentse az **IoT fejlesztői készlet-eszközökként**.

Az oktatóanyag későbbi részében ezzel az eszközzel olyan konfigurációt alkalmazhat, amely frissíti az összes tag belső vezérlőprogram-beállításait.

## <a name="prepare-and-host-the-firmware"></a>A belső vezérlőprogram előkészítése és üzemeltetése

Az [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) vs Code bővítmény tartalmazza a belső vezérlőprogram frissítésének minta-eszköz kódját.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Az OTA belső vezérlőprogram-minta megnyitása a VS Code-ban

1. Győződjön meg arról, hogy a IoT fejlesztői készlet nincs csatlakoztatva a számítógéphez. Indítsa el a VS Code-ot, majd kapcsolódjon a fejlesztői készlet a számítógéphez.

1. Nyomja meg az **F1** billentyűt a parancs paletta megnyitásához, írja be a parancsot, majd válassza a **IoT Workbench: példák**elemet. Ezután válassza a **IoT fejlesztői készlet** lehetőséget a táblaként.

1. Keresse meg az **OTA belső vezérlőprogramot** , és kattintson a **minta megnyitása**gombra. Megnyílik az új VS Code ablak, és megjeleníti a **firmware_ota** Project mappát:

    ![IoT Workbench, válassza a belső vezérlőprogram OTA-példa](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Az új belső vezérlőprogram létrehozása

Az eszköz belső vezérlőprogram kezdeti verziója a 1.0.0. Az új belső vezérlőprogram verziószámának magasabbnak kell lennie.

1. A VS Code-ban nyissa meg a **FirmwareOTA. Ino** fájlt `currentFirmwareVersion` , `1.0.0` és `1.0.1`változtassa meg a következőt:

    ![Belső vezérlőprogram verziójának módosítása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Nyissa meg a parancssort, majd írja be és válassza a **IoT Workbench: eszköz**elemet. Ezután válassza az **eszköz fordítása** lehetőséget a kód fordításához:

    ![Eszköz fordítása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    A VS Code a projektben lévő `.build` mappában menti a lefordított fájlt. A beállításoktól függően a VS Code elrejtheti a `.build` mappát a Explorer nézetben.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>A CRC-érték előállítása és a belső vezérlőprogram fájlméretének kiszámítása

1. Nyissa meg a parancssort, majd írja be és válassza a **IoT Workbench: eszköz**elemet. Ezután válassza a **CRC-létrehozó**:

    ![CRC előállítása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. A VS Code létrehozza és kinyomtatja a CRC-értéket, a belső vezérlőprogram fájlnevét és elérési útját, valamint a fájl méretét a kimeneti ablakban. Jegyezze fel ezeket az értékeket később:

    ![CRC-információ](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>A belső vezérlőprogram feltöltése a felhőbe

Használja Azure Storage-fiókját az új belső vezérlőprogram-fájl Felhőbeli üzemeltetéséhez.

1. Az Azure Portalon nyissa meg a tárfiókot. A szolgáltatások szakaszban válassza a **Blobok**elemet. Hozzon létre egy **belső vezérlőprogram** nevű nyilvános tárolót a belső vezérlőprogram fájljainak tárolásához:

    ![Mappa létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. A belső vezérlőprogram-fájl tárolóba való feltöltéséhez válassza ki a **belső vezérlőprogram** tárolóját, és kattintson a **feltöltés**elemre.

1. Válassza ki a **FirmwareOTA. Ino. bin**elemet. Az előző szakaszban megjegyezte a fájl teljes elérési útját.

1. A belső vezérlőprogram-fájl feltöltésének befejezése után jegyezze fel a fájl URL-címét.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Az eredeti belső vezérlőprogram felépítése és feltöltése a IoT fejlesztői készlet-eszközre

1. A VS Code-ban nyissa meg a **FirmwareOTA. Ino** fájlt `currentFirmwareVersion` , és `1.0.0`váltson vissza a következőre:

    ![A 1.0.0 verziója](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Nyissa meg a parancssort, majd írja be és válassza a **IoT Workbench: eszköz**elemet. Ezután válassza az **eszköz feltöltése**elemet:

    ![Eszköz feltöltése](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. A VS Code ellenőrzi és feltölti a kódot a IoT fejlesztői készlet-eszközére.

1. A feltöltés befejeződése után a IoT fejlesztői készlet-eszköz újraindul. Ha az újraindítás befejeződött, a IoT fejlesztői készlet képernyője a következőt jeleníti meg **: az FW verziója: 1.0.0**, és az új belső vezérlőprogram ellenőrzése:

    ![OTA-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Eszköz konfigurációjának létrehozása

Az eszköz konfigurációja meghatározza az eszközök kívánt állapotát. A fejlesztő általában a Azure Portal **IoT-eszköz konfigurációja** lapján [hozza létre a konfigurációt](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) . Az eszköz konfigurációja egy JSON-dokumentum, amely meghatározza az eszközök kívánt állapotát és a mérőszámok egy készletét.

Mentse a következő konfigurációt a **Firmware-Update. JSON** nevű fájlként a helyi számítógépen. Cserélje le `YOURSTRORAGEACCOUNTNAME`a `YOURCHECKSUM`,, `YOURPACKAGESIZE` és helyőrzőket a korábban megjegyzett értékekre:

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

Ezt a konfigurációs fájlt a következő szakaszban használhatja.

## <a name="import-a-configuration"></a>Konfiguráció importálása

Ebben a szakaszban az eszköz konfigurációját csomagként importálja a távoli figyelési megoldás gyorsító részébe. Az operátor általában befejezi ezt a feladatot.

1. A távoli figyelés webes KEZELŐFELÜLETén navigáljon a **csomagok** lapra, és kattintson az **+ új csomag**lehetőségre:

    ![Új csomag](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Az **új csomag** panelen válassza az **eszköz konfigurációja** lehetőséget a csomag típusaként és a **belső vezérlőprogramként** a konfiguráció típusaként. A **Tallózás** gombra kattintva keresse meg a **belső vezérlőprogram-Update. JSON** fájlt a helyi gépen, majd kattintson a **feltöltés**gombra:

    ![Csomag feltöltése](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A csomagok listája mostantól tartalmazza a **belső vezérlőprogram-frissítési** csomagot.

## <a name="deploy-the-configuration-to-your-devices"></a>A konfiguráció üzembe helyezése az eszközökön

Ebben a szakaszban olyan központi telepítést hoz létre és hajt végre, amely az eszköz konfigurációját alkalmazza a IoT fejlesztői készlet-eszközeire.

1. A távoli figyelés webes KEZELŐFELÜLETén navigáljon a **központi telepítések** lapra, és kattintson az **+ új központi telepítés**lehetőségre:

    ![Új központi telepítés](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Az **új központi telepítés** panelen hozzon létre egy központi telepítést a következő beállításokkal:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Belső vezérlőprogram frissítésének telepítése|
    |Csomag típusa|Eszközkonfiguráció|
    |Konfiguráció típusa|Belső vezérlőprogram|
    |Csomag|belső vezérlőprogram – Update. JSON|
    |Eszközcsoport|IoT fejlesztői készlet-eszközök|
    |Prioritás|10|

    ![Üzemelő példány létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Kattintson az **Alkalmaz** gombra. A **központi telepítések** lapon egy új központi telepítés jelenik meg, amely a következő metrikákat jeleníti meg:

    * A **cél** az eszközcsoport eszközeinek számát mutatja.
    * **Alkalmazott** a konfigurációs tartalommal frissített eszközök számát jeleníti meg.
    * A **sikeres** művelet során az üzemelő példányban lévő eszközök száma látható.
    * A **sikertelenül** jelzett központi telepítésben szereplő eszközök száma.

## <a name="monitor-the-deployment"></a>A telepítés monitorozása

Néhány perc elteltével a IoT fejlesztői készlet lekéri az új belső vezérlőprogram adatait, és megkezdi a letöltését az eszközre:

![OTA – 2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

A hálózat sebességétől függően a letöltés akár néhány percet is igénybe vehet. A belső vezérlőprogram letöltése után az eszköz ellenőrzi a fájl méretét és a CRC-értéket. **Ha az** ellenőrzés sikeres, a MXChip megjelenített képernyő jelenik meg.

![OTA-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Ha az ellenőrzések sikeresek, az eszköz újraindul. Az újraindítás előtt egy **5** és **0** közötti visszaszámlálást fog látni.

![OTA-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Az újraindítás után a IoT fejlesztői készlet bootloader frissíti a belső vezérlőprogramot az új verzióra. A frissítés több másodpercig is eltarthat. Ebben a szakaszban az RGB-eszköz LED-je piros, a képernyő pedig üres.

![OTA-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Ha az újraindítás befejeződött, a IoT fejlesztői készlet-eszköz mostantól a belső vezérlőprogram 1.0.1-es verzióját futtatja.

![OTA-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

A **központi telepítések** lapon kattintson a központi telepítésre, és tekintse meg az eszközök állapotát a frissítéskor. Megtekintheti az egyes eszközök állapotát az eszköz csoportjában, valamint a definiált egyéni metrikákat is.

![Központi telepítés részletei](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan frissítheti a megoldáshoz csatlakoztatott eszközök egy csoportjának belső vezérlőprogram-eszközét. Az eszközök frissítéséhez a megoldás automatikus eszközkezelés használatával működik. Ha többet szeretne megtudni a megoldás alapjául szolgáló IoT hub automatikus eszközkezelés szolgáltatásával kapcsolatban, tekintse meg a következő témakört: [IoT-eszközök konfigurálása és figyelése nagy méretekben a Azure Portal használatával](../iot-hub/iot-hub-auto-device-config.md).