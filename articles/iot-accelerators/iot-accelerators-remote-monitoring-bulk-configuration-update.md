---
title: A távoli figyeléshez csatlakoztatott eszközök kezelése ömlesztve - Azure | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan kezelheti a távoli figyelési megoldáshoz tömegesen csatlakoztatott eszközöket.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565578"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Oktatóanyag: A csatlakoztatott eszközök tömeges kezelése

Ebben az oktatóanyagban a Távoli figyelési megoldás gyorsítót használja a csatlakoztatott eszközök tömeges kezeléséhez.

A Contoso operátoraként egy új belső vezérlőprogram-verzióval rendelkező eszközcsoportot kell konfigurálnia. Nem szeretné, hogy minden eszközön külön-külön kell-e frissítenie a belső vezérlőprogramot. A belső vezérlőprogram frissítése egy eszközcsoporton, eszközcsoportok és automatikus eszközkezelés a Távoli figyelési megoldás gyorsítóban. Az eszközcsoporthoz hozzáadott eszközök a legújabb belső vezérlőprogramot kapják meg, amint az eszköz online állapotba kerül.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Eszközcsoport létrehozása
> * A firmware előkészítése és üzemeltetése
> * Eszközkonfiguráció létrehozása az Azure Portalon
> * Eszközkonfiguráció importálása a távfigyelési megoldásba
> * A konfiguráció telepítése az eszközcsoport eszközeire
> * A telepítés monitorozása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell a távoli monitorozási megoldásgyorsító üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe a távoli monitorozási megoldásgyorsítót, végezze el [Távoli felhőalapú monitorozási megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md) rövid útmutatóban leírtakat.

A belső vezérlőprogram-fájlok üzemeltetéséhez Azure-tárfiókra van szükség. Használhatja a meglévő tárfiókot, vagy [hozzon létre egy új tárfiókot](../storage/common/storage-account-create.md) az előfizetésben.

Az oktatóanyag egy [IoT DevKit-eszközt](https://microsoft.github.io/azure-iot-developer-kit/) használ mintaeszközként.

A következő szoftvereket kell telepíteni a helyi számítógépre:

* [Visual Studio kód (VS kód)](https://code.visualstudio.com/).
* Az [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code bővítmény.

Előkészületek:

* Győződjön meg arról, hogy az [IoT DevKit-eszköz rendszertöltője az 1.4.0-s vagy újabb verzióban van.](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)
* Győződjön meg arról, hogy az IoT DevKit SDK ugyanazon a verzión van, mint a bootloader. Az IoT DevKit SDK frissítheti az Azure IoT Workbench vs-kódban. Nyissa meg a parancs paletta, és adja **Arduino: Board Manager**. További információ: [Prepare the development environment](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Emellett legalább egy IoT DevKit-eszközt csatlakoztatnia kell a távoli figyelési megoldásgyorsítóhoz. Ha még nem csatlakoztatott ioT DevKit-eszközt, olvassa el [az MXChip IoT DevKit AZ3166 csatlakoztatása az IoT Remote Monitoring megoldásgyorsítóhoz című témakört.](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

## <a name="navigate-to-the-dashboard"></a>Lépjen az irányítópultra

A távoli monitorozási megoldás irányítópultjának megtekintéséhez először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#dashboard) területre. A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Ezután kattintson az **Indítás** elemre a [rövid útmutató](quickstart-remote-monitoring-deploy.md) során üzembe helyezett távoli monitorozási megoldásgyorsító csempéjén.

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Az eszközök egy csoportjának belső vezérlőprogramjának automatikus frissítéséhez az eszközöknek a távfigyelési megoldás eszközcsoportjának tagjainak kell lenniük:

1. Az **Eszközök** lapon válassza ki az összes **IoT DevKit-eszközt,** amelyet a megoldásgyorsítóhoz csatlakoztatott. Ezután kattintson a **Jobs** (Feladatok) gombra.

1. A **Feladatok** panelen válassza a **Címkék**lehetőséget, állítsa a feladat nevét **AddDevKitTag**értékre, majd adjon hozzá egy **IsDevKitDevice** nevű szövegcímkét **Y**értékkel. Ezután kattintson **az Alkalmaz gombra.**

1. Most már használhatja a címke értékeket, hogy hozzon létre egy eszközcsoportot. Az **Eszközök** lapon kattintson az **Eszközcsoportok kezelése gombra.**

1. Hozzon létre egy szöveges szűrőt, amely az **IsDevKitDevice** címkenevet és az **Y** értéket használja a feltételben. Mentse az eszközcsoportot **IoT DevKit-eszközökként.**

Az oktatóanyag későbbi részében ezt az eszközcsoportot használja egy olyan eszközkonfiguráció alkalmazására, amely frissíti az összes tag belső vezérlőprogramját.

## <a name="prepare-and-host-the-firmware"></a>A firmware előkészítése és üzemeltetése

Az [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code bővítmény tartalmazza a minta eszköz kódját a belső vezérlőprogram frissítése.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>A firmware OTA-minta megnyitása vs kódban

1. Győződjön meg arról, hogy az IoT DevKit nincs csatlakoztatva a számítógéphez. Indítsa el a VS-kódot, majd csatlakoztassa a DevKit-et a számítógéphez.

1. Nyomja meg az **F1 billentyűt** a parancspaletta megnyitásához, írja be és válassza az **IoT Workbench: Examples**lehetőséget. Ezután válassza az **IoT DevKit-et** táblaként.

1. Keresse meg **a firmware OTA-t,** és kattintson **a Minta megnyitása gombra.** Megnyílik egy új VS-kód ablak, amely a **firmware_ota** projektmappát jeleníti meg:

    ![IoT Workbench, válassza a Firmware OTA példa](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Az új firmware létrehozása

Az eszköz belső vezérlőprogramjának kezdeti verziója 1.0.0. Az új belső vezérlőprogramnak magasabb verziószámmal kell rendelkeznie.

1. A VS Code fájlban nyissa meg a **FirmwareOTA.ino** fájlt, és módosítsa a `currentFirmwareVersion` következőre: `1.0.0` `1.0.1`

    ![Belső vezérlőprogram verziójának módosítása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Nyissa meg a parancspalettát, majd írja be, és válassza az **IoT-munkaterület: Eszköz**lehetőséget. Ezután válassza **az Eszközfordítás lehetőséget** a kód fordításához:

    ![Eszköz fordítása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    A VS Code a lefordított `.build` fájlt a projekt mappájába menti. A beállításoktól függően a VS `.build` Code elrejtheti a mappát a felfedező nézetben.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>A CRC-érték létrehozása és a belső vezérlőprogram fájlméretének kiszámítása

1. Nyissa meg a parancspalettát, majd írja be, és válassza az **IoT-munkaterület: Eszköz**lehetőséget. Ezután válassza **a CRC létrehozása lehetőséget:**

    ![CRC létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. A VS Code létrehozza és kinyomtatja a CRC-értéket, a belső vezérlőprogram fájlnevét és elérési útját, valamint a kimeneti ablakban lévő fájlméretet. Jegyezze fel ezeket az értékeket későbbre:

    ![CRC-adatok](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Töltse fel a firmware-t a felhőbe

Azure-tárfiók használatával az új belső vezérlőprogram-fájlt a felhőben tárolhatja.

1. Az Azure Portalon nyissa meg a tárfiókot. A Szolgáltatások csoportban válassza a **Blobok**lehetőséget. Hozzon létre egy belső **vezérlőprogram** nevű nyilvános tárolót a belső vezérlőprogram fájljainak tárolásához:

    ![Mappa létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. A belső vezérlőprogram fájljának a tárolóba való feltöltéséhez jelölje ki a **belső vezérlőprogram-tárolót,** és kattintson a **Feltöltés gombra.**

1. Válassza ki a **FirmwareOTA.ino.bin**elemet. Az előző szakaszban feljegyezte a fájl teljes elérési útját.

1. Miután a firmware fájl feltöltése befejeződött, jegyezze fel a fájl URL-címét.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Az eredeti belső vezérlőprogram létrehozása és feltöltése az IoT DevKit-eszközre

1. A VS Code fájlban nyissa meg a **FirmwareOTA.ino** fájlt, és módosítsa a `currentFirmwareVersion` hátat a következőre: `1.0.0`

    ![1.0.0-s verzió](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Nyissa meg a parancspalettát, majd írja be, és válassza az **IoT-munkaterület: Eszköz**lehetőséget. Ezután válassza **az Eszköz feltöltése lehetőséget:**

    ![Eszköz feltöltése](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. A VS Code ellenőrzi és feltölti a kódot az IoT DevKit-eszközre.

1. Amikor a feltöltés befejeződik, az IoT DevKit-eszköz újraindul. Amikor az újraindítás befejeződött, az IoT DevKit képernyője az **FW 1.0.0 verzióját**jeleníti meg, és az új belső vezérlőprogramot ellenőrzi:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Eszközkonfiguráció létrehozása

Az eszközkonfiguráció határozza meg az eszközök kívánt állapotát. Általában egy fejlesztő [hozza létre a konfigurációt](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) az **IoT-eszköz konfigurációs** lapján az Azure Portalon. Az eszközkonfiguráció egy JSON-dokumentum, amely meghatározza az eszközök kívánt állapotát és a metrikák készletét.

Mentse a következő konfigurációt **firmware-update.json** nevű fájlként a helyi számítógépre. Cserélje `YOURSTRORAGEACCOUNTNAME`le `YOURCHECKSUM`a `YOURPACKAGESIZE` , és a helyőrzőket azokkal az értékekkel, amelyeket korábban feljegyzett:

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

Ebben a szakaszban importálja az eszköz konfigurációját csomagként a távoli figyelési megoldás gyorsítóba. Általában egy operátor fejezi be ezt a feladatot.

1. A Távoli figyelés webes felhasználói felületén keresse meg a **Csomagok** lapot, és kattintson **a + Új csomag gombra:**

    ![Új csomag](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Az **Új csomag** panelen válassza az **Eszközkonfiguráció** lehetőséget csomagtípusként és **belső vezérlőprogramként** a konfigurációtípusként lehetőséget. A **Tallózás gombra** kattintva keresse meg a **firmware-update.json** fájlt a helyi számítógépen, majd kattintson a **Feltöltés gombra:**

    ![Csomag feltöltése](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A csomagok listája most már tartalmazza a **firmware-frissítési** csomagot.

## <a name="deploy-the-configuration-to-your-devices"></a>A konfiguráció telepítése az eszközökre

Ebben a szakaszban hozzon létre és hajtson végre egy központi telepítést, amely az eszköz konfigurációját alkalmazza az IoT DevKit-eszközökre.

1. A Távoli figyelés webes felhasználói felületén keresse meg a **Központi telepítések** lapot, és kattintson a **+ Új telepítés**elemre:

    ![Új telepítés](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Az **Új telepítés** panelen hozzon létre egy központi telepítést a következő beállításokkal:

    |Beállítás|Érték|
    |---|---|
    |Név|Belső vezérlőprogram frissítésének telepítése|
    |Csomag típusa|Eszközkonfiguráció|
    |Konfiguráció típusa|Belső vezérlőprogram|
    |Csomag|firmware-update.json|
    |Eszközcsoport|IoT DevKit-eszközök|
    |Prioritás|10|

    ![Üzemelő példány létrehozása](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Kattintson az **Alkalmaz** gombra. Megjelenik egy új központi telepítés a **Központi telepítések** lapon, amely a következő mutatókat jeleníti meg:

    * **A Célzott** eszközcsoportban lévő eszközök számát jeleníti meg.
    * **Az alkalmazott** a konfigurációs tartalommal frissített eszközök számát jeleníti meg.
    * A sikeres azt **mutatja,** hogy a központi telepítésben a sikeres nek kivett eszközök száma látható.
    * **Sikertelen a** központi telepítésben lévő hibák számát mutatja.

## <a name="monitor-the-deployment"></a>A telepítés monitorozása

Néhány perc múlva az IoT DevKit lekéri az új belső vezérlőprogram adatait, és elkezdi letölteni az eszközre:

![ota-2 között](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

A hálózat sebességétől függően a letöltés akár néhány percet is igénybe vehet. A belső vezérlőprogram letöltése után az eszköz ellenőrzi a fájlméretet és a CRC-értéket. Az MXChip kijelzők képernyője **sikeres,** ha az ellenőrzés sikeres.

![ota-3 között](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Ha az ellenőrzés sikeres, az eszköz újraindul. 5 **és** **0** között jelenik meg a visszaszámlálás az újraindítás előtt.

![ota-4 között](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Az újraindítás után az IoT DevKit rendszertöltő frissíti a belső vezérlőprogramot az új verzióra. A frissítés több másodpercet is igénybe vehet. Ebben a szakaszban a készülék RGB LED-je piros, a képernyő pedig üres.

![ota-5 között](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Az újraindítás befejezése után az IoT DevKit-eszköz most a belső vezérlőprogram 1.0.1-es verzióját futtatja.

![ota-6 között](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

A **Központi telepítések** lapon kattintson egy központi telepítésállapotának megtekintéséhez az eszközök frissítés közben. Megtekintheti az eszközcsoportban lévő egyes eszközök állapotát és a megadott egyéni mutatókat.

![A telepítés részletei](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatja, hogyan frissítheti a megoldáshoz csatlakoztatott eszközök csoportjának belső vezérlőprogramját. Az eszközök frissítéséhez a megoldás automatikus eszközkezelést használ. Ha többet szeretne megtudni a megoldás alapjául szolgáló IoT-központ automatikus eszközkezelési funkciójáról, olvassa [el az IoT-eszközök konfigurálása és figyelése az Azure Portalhasználatával.](../iot-hub/iot-hub-auto-device-config.md)