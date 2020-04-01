---
title: 'Oktatóanyag: Környezet beállítása – Machine Learning az Azure IoT Edge-en'
description: 'Oktatóanyag: Készítse elő a környezetet a modulok fejlesztéséhez és üzembe helyezéséhez a gépi tanulás szélén.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c776d4d6c3973e7c222c9c9adf3e5105f6c84f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296802"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Oktatóanyag: Hozzon létre egy környezetet az IoT Edge-en a gépi tanuláshoz

> [!NOTE]
> Ez a cikk az Azure Machine Learning IoT Edge-en való használatával kapcsolatos oktatóanyag sorozatának része. Ha megérkezett ezt a cikket közvetlenül, javasoljuk, hogy kezdődik az [első cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredményt.

Ez a cikk segít felkészülni a környezet fejlesztési és üzembe helyezési. Először hozzon létre egy fejlesztőgépet minden szükséges eszközzel. Ezután hozza létre a szükséges felhőbeli erőforrásokat az Azure-ban.

## <a name="set-up-the-development-vm"></a>A fejlesztői virtuális gép beállítása

Ezt a lépést általában egy felhőalapú fejlesztő hajtja végre. Néhány szoftver is hasznos lehet egy adattudós.

Létrehoztunk egy PowerShell-parancsfájlt, amely létrehoz egy Azure virtuális gépet, amely számos előfeltételt már konfigurált. Az általunk létrehozott virtuális gépnek képesnek kell lennie a [beágyazott virtualizáció kezelésére,](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)ezért választottuk [a Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) gépméretet.

A fejlesztő im a következővel lesz beállítva:

* Windows 10
* [Csokoládés](https://chocolatey.org/)
* [Docker Asztal Windowshoz](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [Git hitelesítő adatok kezelője windowsos](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Piton 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS kódbővítmények](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker között](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A fejlesztői virtuális gép nem feltétlenül szükséges – az összes fejlesztői eszköz futtatható egy helyi gépen. Azonban azt javasoljuk, hogy a virtuális gép használata az egyenlő versenyfeltételek biztosítása érdekében.

A virtuális gép létrehozása és konfigurálása körülbelül 30 percet vesz igénybe.

1. Klónozza vagy töltse le a Machine Learning és az [IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) mintatára a helyi számítógépre.

1. Nyissa meg a PowerShellt rendszergazdaként, és keresse meg az **\IoTEdgeAndMlSample\DevVM** könyvtárat, amely a kódot letöltő gyökérkönyvtár alatt található. A forrás gyökérkönyvtárára a . `srcdir`

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   A DevVM-könyvtár tartalmazza az oktatóanyag befejezéséhez megfelelő Azure virtuális gép létrehozásához szükséges fájlokat.

1. Futtassa a következő parancsot a parancsfájlok végrehajtásának engedélyezéséhez. A rákérdezéskor válassza **az Igen az összesre** lehetőséget.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Futtassa a Create-AzureDevVM.ps1.runt.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Amikor a rendszer kéri, adja meg a következő információkat:

    * **Azure-előfizetés-azonosító:** Az előfizetés-azonosító, amely megtalálható az [Azure-előfizetések](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) portálon.
    * **Erőforráscsoport neve:** Egy új vagy meglévő erőforráscsoport neve az Azure-ban.
    * **Hely**: Válasszon egy Azure-helyet, ahol a virtuális gép jön létre. Például "Us2 nyugat-európai" vagy "Észak-Európa". További információ: [Azure locations](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Felhasználónév**: Adja meg a virtuális gép rendszergazdai fiókjának emlékezetes nevét.
    * **Jelszó:** Állítsa be a jelszót a rendszergazdai fiók a virtuális gép.

   A parancsfájl néhány percig fut, mivel végrehajtja a következő lépéseket:

    1. Telepíti az [Azure PowerShell Az modult.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
    1. Kéri, hogy jelentkezzen be az Azure-ba.
    1. Megerősíti a virtuális gép létrehozásához szükséges információkat. A folytatáshoz nyomja le az **y** vagy az **Enter** billentyűt.
    1. Létrehozza az erőforráscsoportot, ha nem létezik.
    1. Telepíti a virtuális gépet.
    1. Engedélyezi a Hyper-V-t a virtuális gépen.
    1. Telepíti a fejlesztéshez szükséges szoftvert, és klónozza a mintatárolót.
    1. Újraindítja a virtuális gép.
    1. Létrehoz egy RDP-fájlt az asztalon a virtuális géphez való csatlakozáshoz.

   Ha a rendszer kéri, hogy a virtuális gép neve indítsa újra, a parancsfájl kimenetéről másolhatja a nevét. A kimenet is mutatja az elérési utat az RDP-fájl a virtuális géphez való csatlakozáshoz.

### <a name="set-auto-shutdown-schedule"></a>Automatikus leállításütemezés beállítása

A költségek csökkentése érdekében a fejlesztői virtuális gép egy 1900 PST-re beállított automatikus leállítási ütemezéssel jött létre. Előfordulhat, hogy a tartózkodási helytől és az ütemezéstől függően frissítenie kell ezt a beállítást. A leállítási ütemezés frissítése:

1. Az Azure Portalon keresse meg a parancsfájl által létrehozott virtuális gép.

1. A bal oldali ablaktábla **menüjében**a Műveletek csoportban válassza az **Automatikus leállítás**lehetőséget.

1. Szükség szerint módosítsa az **ütemezett leállítást** és **az időzónát,** és válassza a **Mentés gombot.**

## <a name="connect-to-the-development-vm"></a>Csatlakozás a fejlesztői virtuális géphez

Most, hogy létrehoztunk egy virtuális gép van szükségünk, hogy befejezze a szoftver telepítéséhez szükséges a bemutató befejezéséhez.

1. Kattintson duplán arra az RDP-fájlra, amelyet a parancsfájl az asztalon hozott létre.

1. Megjelenik egy párbeszédablak, amely szerint a távoli kapcsolat közzétevője ismeretlen. Ez elfogadható, ezért válassza a **Csatlakozás**lehetőséget.

1. Adja meg a virtuális gép létrehozásához megadott rendszergazdai jelszót, és kattintson az **OK**gombra.

1. A rendszer kéri, hogy fogadja el a virtuális gép tanúsítványát. Válassza az **Igen** lehetőséget.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio-kódbővítmények telepítése

Most, hogy csatlakozott a fejlesztői géphez, adjon hozzá néhány hasznos bővítményt a Visual Studio Code-hoz, hogy megkönnyítse a fejlesztési élményt.

1. Csatlakozzon a fejlesztői virtuális géphez, nyisson meg egy PowerShell-ablakot, és keresse meg a **C:\source\IoTEdgeAndMlSample\DevVM** könyvtárat. Ezt a könyvtárat a virtuális gép létrehozásához készült parancsfájl hozta létre.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Futtassa a következő parancsot a parancsfájlok végrehajtásának engedélyezéséhez. A rákérdezéskor válassza **az Igen az összesre** lehetőséget.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Futtassa a Visual Studio Code bővítménybővítmény-parancsfájlt.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. A parancsfájl néhány percig fog futni a VS-kódbővítmények telepítésével:

    * Azure IoT-eszközök
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Az IoT Hub és a Storage beállítása

Ezeket a lépéseket általában egy felhőfejlesztő hajtja végre.

Az Azure IoT Hub minden IoT-alkalmazás szíve, mivel biztonságos kommunikációt kezel az IoT-eszközök és a felhő között. Ez az IoT Edge gépi tanulási megoldás működésének fő koordinációs pontja.

* Az IoT Hub útvonalakat használ az IoT-eszközökről érkező adatok más alsóbb rétegbeli szolgáltatásokba történő irányításához. Az IoT Hub-útvonalak előnyeit kihasználva eszközadatokat küldünk az Azure Storage-ba. Az Azure Storage-ban az eszközadatokat az Azure Machine Learning használja fel a fennmaradó hasznos élettartam (RUL) osztályozóbe tanításához.

* Az oktatóanyag későbbi részében az IoT Hub használatával konfiguráljuk és kezeljük az Azure IoT Edge-eszközünket.

Ebben a szakaszban egy parancsfájl használatával hozzon létre egy Azure IoT hubot és egy Azure Storage-fiókot. Ezután az Azure Portalon konfigurálegy útvonalat, amely továbbítja a hub által fogadott adatokat egy Azure Storage-tárolóba. Ezek a lépések körülbelül 10 percet vesz igénybe.

1. Csatlakozzon a fejlesztői virtuális géphez, nyisson meg egy PowerShell-ablakot, és keresse meg az **IoTHub-címtárban.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Futtassa a létrehozási parancsfájlt. Ugyanazokat az értékeket használja az előfizetés-azonosítóhoz, a helyhez és az erőforráscsoporthoz, mint a fejlesztői virtuális gép létrehozásakor.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * A rendszer kéri majd, hogy jelentkezzen be az Azure-ba.
    * A parancsfájl megerősíti a Hub és Storage-fiók létrehozásához szükséges információkat. A folytatáshoz nyomja le az **y** vagy az **Enter** billentyűt.

A szkript futása körülbelül két percet vesz igénybe. Miután elkészült, a parancsfájl kimeneti az IoT hub nevét és a tárfiókot.

## <a name="review-route-to-storage-in-iot-hub"></a>Útvonal áttekintése az IoT Hub ban tárolt tárhelyhez

Az IoT hub létrehozása részeként az előző szakaszban futtatott parancsfájl is létrehozott egy egyéni végpontot és egy útvonalat. Az IoT Hub-útvonalak egy lekérdezési kifejezésből és egy végpontból állnak. Ha egy üzenet megegyezik a kifejezéssel, az adatok a társított végponthoz vezető útvonal mentén lesznek elküldve. A végpontok lehetnek eseményközpontok, szolgáltatásbusz-várólisták és témakörök. Ebben az esetben a végpont egy blob tároló egy tárfiókban. Használjuk az Azure Portalon a szkriptünk által létrehozott útvonal áttekintéséhez.

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com) és nyissa meg az oktatóanyaghoz használt erőforráscsoportot.

1. Az erőforrások listájában válassza ki a parancsfájl által létrehozott IoT Hub. Ez lesz a neve végződő `IotEdgeAndMlHub-jrujej6de6i7w`véletlenszerű karakterek, mint például .

1. A bal oldali ablaktábla **Menüjében**az Üzenetek csoportban válassza az **Üzenettovábbítás**lehetőséget.

1. Az **Üzenetút-tervezés** lapon válassza az **Egyéni végpontok** lapot.

1. **Bontsa** ki a Tároló szakaszt:

   ![Ellenőrizze turbofanDeviceStorage az egyéni végpontok listája](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Látjuk **turbofanDeviceStorage** az egyéni végpontok listáját. Vegye figyelembe a végpontra jellemző alábbi jellemzőket:

   * A **tároló neve**szerint megnevezett `devicedata` blobstorage-tárolóra mutat.
   * **A fájlnév formátuma** a név utolsó elemeként partícióval rendelkezik. Úgy találjuk, hogy ez a formátum kényelmesebb az Azure Notebookok az oktatóanyag későbbi részében az Azure Notebookokkal végzett fájlműveletekhez.
   * **Az állapotának** egészségesnek kell lennie.

1. Válassza az **Útvonalak** lapot.

1. Válassza ki a **turbofanDeviceDataToStorage**nevű útvonalat.

1. Az **Útvonalak részletei** lapon vegye figyelembe, hogy az útvonal végpontja a **turbofanDeviceStorage** végpont.

   ![Tekintse át a részleteket a turbofanDeviceDataToStorage útvonal](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Tekintse meg az **Útvonaltervezés lekérdezést,** amely **igaz**értékre van állítva. Ez a beállítás azt jelenti, hogy az összes eszköz telemetriai üzenet megfelel ennek az útvonalnak; és ezért minden üzenet lesz elküldve a **turbofanDeviceStorage** végpont.

1. Mivel nem történt szerkesztés, csak zárja be ezt az oldalt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehoztunk egy IoT Hubot, és konfiguráltunk egy útvonalat egy Azure Storage-fiókhoz. Ezután a szimulált eszközök egy készletéről küldünk adatokat az IoT Hubon keresztül a tárfiókba. Később a bemutató, miután konfigurálta az IoT Edge-eszköz és modulok, akkor újra útvonalakat, és nézd meg egy kicsit az útválasztási lekérdezést.

A Machine Learning ioT Edge-oktatóanyag ezen részében ismertetett lépésekről a következő témakörben talál további információt:

* [Azure IoT – alapok](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Üzenet-útválasztás konfigurálása az IoT Hubbal](../iot-hub/tutorial-routing.md)
* [IoT-központ létrehozása az Azure Portalon](../iot-hub/iot-hub-create-through-portal.md)

Folytassa a következő cikkel, hogy hozzon létre egy szimulált eszköz figyelésére.

> [!div class="nextstepaction"]
> [Eszközadatok létrehozása](tutorial-machine-learning-edge-03-generate-data.md)
