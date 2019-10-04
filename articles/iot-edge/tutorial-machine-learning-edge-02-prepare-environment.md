---
title: Környezet beállítása – Machine Learning a Azure IoT Edgeon | Microsoft Docs
description: Készítse elő a környezetet a gépi tanuláshoz készült modulok fejlesztéséhez és üzembe helyezéséhez a peremhálózat szélén.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1db94e683a0dfb3b60b12bc5ac205c766d405d0a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299822"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Oktatóanyag: Környezet beállítása a gépi tanuláshoz IoT Edge

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ez a cikk a IoT Edge oktatóanyagának végpontok közötti Azure Machine Learningával segíti elő a környezet fejlesztését és üzembe helyezését. Először állítson be egy fejlesztői gépet minden szükséges eszközzel. Ezután hozza létre a szükséges Felhőbeli erőforrásokat az Azure-ban.

## <a name="set-up-a-development-machine"></a>Fejlesztői gép beállítása

Ezt a lépést általában egy felhőalapú fejlesztő hajtja végre. Néhány szoftver hasznos lehet egy adattudós számára is.

Ebben a cikkben a különböző fejlesztői feladatokat hajtjuk végre, például a kódolást, a fordítást, a konfigurálást és az üzembe helyezést IoT Edge modulokat és IoT eszközöket. Az egyszerű használat érdekében egy PowerShell-szkriptet hoztunk létre, amely egy Azure-beli virtuális gépet hoz létre, amely számos előfeltételt már konfigurált. Az általunk létrehozott virtuális gépnek képesnek kell lennie a [beágyazott virtualizálás](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)kezelésére, ezért a [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) -gép méretét választotta.

A fejlesztői virtuális gép a következőket fogja beállítani:

* Windows 10
* [Csokoládés](https://chocolatey.org/)
* [Docker asztali Windows rendszerhez](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [A git Hitelesítőadat-kezelő a Windows rendszerhez](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code-bővítmények](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A fejlesztői virtuális gép nem feltétlenül szükséges – az összes fejlesztői eszköz futtatható a helyi gépen. Javasoljuk azonban, hogy a virtuális gép használatával biztosítson egy szintű játékteret.

A virtuális gép létrehozása és konfigurálása körülbelül 30 percet vesz igénybe.

### <a name="get-the-script"></a>A parancsfájl letöltése

A PowerShell-szkript klónozása vagy letöltése a [Machine learning és IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) minta adattárból.

### <a name="create-an-azure-virtual-machine"></a>Azure-beli virtuális gép létrehozása

A DevVM könyvtár tartalmazza az oktatóanyag elvégzéséhez megfelelő Azure-beli virtuális gép létrehozásához szükséges fájlokat.

1. Nyissa meg a PowerShellt rendszergazdaként, és navigáljon ahhoz a könyvtárhoz, ahová letöltötte a kódot. A forrás gyökérkönyvtárát a következő módon `<srcdir>`fogjuk megtekinteni:.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Futtassa a következő parancsot a parancsfájlok végrehajtásának engedélyezéséhez. Ha **a rendszer kéri, válassza az igen** lehetőséget.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Futtassa a Create-AzureDevVM. ps1 mappát erről a címtárból.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Ha a rendszer kéri, adja meg a következő információkat:
      * **Azure-előfizetés azonosítója**: Az előfizetés-azonosítója, amely a Azure Portal található
      * **Erőforráscsoport neve**: Egy új vagy meglévő erőforráscsoport neve az Azure-ban
      * **Hely**: Válassza ki azt az Azure-helyet, ahová a virtuális gépet létre kívánja hozni. Például: westus2 vagy northeurope. További információ: Azure-beli [telephelyek](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Adjon egy emlékezetes nevet a virtuális gépen létrehozni és használni kívánt rendszergazdai fióknak.
      * **AdminPassword**: Állítsa be a rendszergazdai fiók jelszavát a virtuális gépen.

    * Ha nincs Azure PowerShell telepítve, a parancsfájl telepíti [Azure PowerShell az modult](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * A rendszer kéri majd, hogy jelentkezzen be az Azure-ba.

    * A parancsfájl megerősíti a virtuális gép létrehozásához szükséges információkat. Nyomja `y` meg `Enter` a vagy a gombot a folytatáshoz.

A szkript több percig fut, ahogy a következő lépéseket hajtja végre:

* Hozzon létre egy erőforráscsoportot, ha az nem létezik
* A virtuális gép üzembe helyezése
* Hyper-V engedélyezése a virtuális gépen
* A minta-adattár fejlesztéséhez és klónozásához szükséges szoftverek telepítése
* A virtuális gép újraindítása
* RDP-fájl létrehozása az asztalon a virtuális géphez való csatlakozáshoz

### <a name="set-auto-shutdown-schedule"></a>Automatikus leállítási ütemterv beállítása

A Cost csökkentése érdekében a virtuális gép a 1900 PST-re beállított automatikus leállítási ütemtervtel lett létrehozva. Előfordulhat, hogy a helytől és az ütemezéstől függően frissítenie kell ezt az időzítést. A leállítási ütemterv frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon a virtuális géphez az előző szakaszban megadott erőforráscsoporthoz.

3. Válassza az **automatikus leállítás** lehetőséget az oldalsó Navigátorban.

4. Adjon meg egy új leállítási időt az **ütemezett leállítás** során, vagy módosítsa az **időzónát** , majd kattintson a **Mentés**gombra.

### <a name="connect-and-configure-development-machine"></a>A fejlesztési gép összekapcsolásának és konfigurálásának engedélyezése

Most, hogy létrehozott egy virtuális gépet, be kell fejeznie az oktatóanyag befejezéséhez szükséges szoftver telepítését.

#### <a name="start-a-remote-desktop-session"></a>Távoli asztali munkamenet elindítása

1. A virtuális gép létrehozási parancsfájlja egy RDP-fájlt hozott létre az asztalon.

2. Kattintson duplán az  **\<Azure-beli virtuális gép\>neve. rdp**nevű fájlra.

3. Ekkor megjelenik egy párbeszédpanel, amely azt jelzi, hogy a távoli kapcsolatok közzétevője ismeretlen. Kattintson a **ne Kérdezzen rá ismét a számítógép kapcsolatai** jelölőnégyzetre, majd válassza a **Csatlakozás**lehetőséget.

4. Ha a rendszer kéri, adja meg a virtuális gép beállításához a parancsfájl futtatásakor használt AdminPassword, és kattintson **az OK**gombra.

5. A rendszer kérni fogja, hogy fogadja el a virtuális gép tanúsítványát. Válassza a **ne Kérdezzen újra a számítógép kapcsolataihoz** lehetőséget, és válassza az **Igen**lehetőséget.

#### <a name="install-visual-studio-code-extensions"></a>Visual Studio Code-bővítmények telepítése

Most, hogy csatlakozott a fejlesztői géphez, adjon hozzá néhány hasznos bővítményt a Visual Studio Code-hoz, hogy egyszerűbbé váljon a fejlesztési élmény.

1. Egy PowerShell-ablakban navigáljon a **C:\\Source\\IoTEdgeAndMlSample\\DevVM**.

2. Parancsfájlok futtatásának engedélyezése a virtuális gépen a beírásával.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Futtassa a szkriptet.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. A szkript néhány percig fut a VS Code-bővítmények telepítése során:

    * Azure IoT-eszközök
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub és tárterület beállítása

Ezeket a lépéseket általában egy felhőalapú fejlesztő hajtja végre.

Az Azure IoT Hub bármely IoT-alkalmazás szíve. Kezeli a IoT-eszközök és a felhő közötti biztonságos kommunikációt. Ez a fő koordinációs pont a IoT Edge Machine learning megoldás működéséhez.

* A IoT Hub útvonalak használatával irányítja a bejövő adatok IoT-eszközökről más alsóbb rétegbeli szolgáltatásokba. Kihasználjuk IoT Hub útvonalakat az eszközök Azure Storage-ba való küldéséhez, ahol a Azure Machine Learning felhasználható a hátralévő hasznos élettartam (RUL) besorolás betanításához.

* Az oktatóanyag későbbi részében a IoT Hub használatával konfigurálhatja és kezelheti Azure IoT Edge eszközét.

Ebben a szakaszban egy parancsfájl használatával létrehoz egy Azure IoT hubot és egy Azure Storage-fiókot. Ezután konfiguráljon egy útvonalat, amely továbbítja a hub által fogadott adatAzure Storage Blob tárolónak a Azure Portal használatával. Ezek a lépések körülbelül 10 percet vesznek igénybe.

### <a name="create-cloud-resources"></a>Felhőbeli erőforrások létrehozása

1. Nyisson meg egy PowerShell-ablakot a fejlesztői gépen.

1. Váltson át a IoTHub könyvtárra.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Futtassa a létrehozási parancsfájlt. Használja ugyanazokat az értékeket az előfizetés-AZONOSÍTÓhoz, a helyhez és az erőforráscsoporthoz, mint a fejlesztési virtuális gép létrehozásakor.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * A rendszer kéri majd, hogy jelentkezzen be az Azure-ba.
    * A parancsfájl megerősíti a hub és a Storage-fiók létrehozásához szükséges információkat. Nyomja `y` meg `Enter` a vagy a gombot a folytatáshoz.

A szkript körülbelül két percet vesz igénybe. Ha elkészült, a parancsfájl a központ és a Storage-fiók nevét adja meg.

### <a name="review-route-to-storage-in-iot-hub"></a>Áttekintheti a tárolási útvonalat IoT Hub

Az IoT hub létrehozásának részeként az előző szakaszban futtatott szkript létrehozta az egyéni végpontot és egy útvonalat is. IoT Hub útvonalak egy lekérdezési kifejezésből és egy végpontból állnak. Ha egy üzenet megfelel a kifejezésnek, az adatküldés a társított végpont útvonalán történik. A végpontok lehetnek Event Hubs, Service Bus várólisták és témakörök. Ebben az esetben a végpont egy blob-tároló egy Storage-fiókban. A szkripttel létrehozott útvonal áttekintéséhez használja a Azure Portal.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Válassza a minden szolgáltatás lehetőséget a bal oldali navigátorban, írja be a IoT kifejezést a keresőmezőbe, majd válassza a **IoT hub**lehetőséget.

1. Válassza ki az előző lépésben létrehozott IoT Hub.

1. Az IoT Hub oldali Navigátorban válassza az **üzenet-útválasztás**elemet.

1. Az üzenet-útválasztási lapon két lap, **útvonal** és **Egyéni végpont**található. Válassza az **Egyéni végpontok** fület.

1. A **blob Storage**területen válassza a **turbofanDeviceStorage**lehetőséget.

1. Vegye figyelembe, hogy ez a végpont egy **devicedata** nevű blob-tárolóra mutat az utolsó lépésben létrehozott Storage-fiókban, amelynek neve **\<iotedgeandml\>egyedi utótag**.

1. Azt is vegye figyelembe, hogy a **blob fájlnevének formátuma** az alapértelmezett formátumból lett módosítva ahelyett, hogy a partíciót a név utolsó elemeként helyezze el. Ezt a formátumot úgy találjuk, hogy az oktatóanyag későbbi részében Azure Notebooks a fájl műveleteihez.

1. A végpont részletei panel bezárásával térjen vissza az **üzenet-útválasztási** lapra.

1. Válassza az **útvonalak** fület.

1. Válassza ki a **turbofanDeviceDataToStorage**nevű útvonalat.

1. Vegye figyelembe, hogy az útvonal végpontja a **turbofanDeviceStorage** egyéni végpontja.

1. Tekintse meg az útválasztási lekérdezést, amely **igaz**értékre van beállítva. Ez azt jelenti, hogy minden telemetria-üzenet megfelel ennek az útvonalnak, ezért a rendszer minden üzenetet elküld az **turbofanDeviceStorage** -végpontnak.

1. Az útvonal részleteinek lezárása.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehoztunk egy IoT Hub, és konfiguráltunk egy útvonalat egy Azure Storage-fiókhoz. A következő cikkben a szimulált eszközökből származó adatok küldését a IoT Hub a Storage-fiókba. Az oktatóanyag későbbi részében, miután konfiguráltuk IoT Edge eszközt és modulokat, újra felkeresjük az útvonalakat, és egy kicsit többet is megvizsgálunk az útválasztási lekérdezésben.

A IoT Edge oktatóanyag Machine Learning ezen részében ismertetett lépésekkel kapcsolatos további információkért lásd:

* [Azure IoT – alapok](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Üzenet-útválasztás konfigurálása IoT Hub](../iot-hub/tutorial-routing.md)
* [IoT hub létrehozása a Azure Portal használatával](../iot-hub/iot-hub-create-through-portal.md)

Folytassa a következő cikkel a figyeléshez szimulált eszköz létrehozásához.

> [!div class="nextstepaction"]
> [Eszköz-adategységek előállítása](tutorial-machine-learning-edge-03-generate-data.md)
