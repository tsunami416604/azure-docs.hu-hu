---
title: Állítsa be a környezetet – Machine Learning az Azure IoT Edge |} A Microsoft Docs
description: A környezet előkészítése a fejlesztéséhez és üzembe helyezéséhez, a machine learning, az edge-modulok.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f36b706213639c5bea383ec20ffc7f6d7ed01021
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155701"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Oktatóanyag: A machine learning, az IoT Edge-környezet beállítása

> [!NOTE]
> Ez a cikk egy sorozat része az IoT Edge-ben az Azure Machine Learning használatával kapcsolatos oktatóanyagot. Ha érkezett, ez a cikk közvetlenül, javasoljuk, hogy először a [először cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredmények.

Ez a cikk a teljes körű Azure Machine Learning, az IoT Edge-oktatóanyagban a segít a környezet előkészítése a fejlesztéséhez és üzembe helyezéséhez. Először állítsa be a szükséges eszközöket egy fejlesztői gépet. Ezután hozzon létre a szükséges felhőbeli erőforrásokat az Azure-ban.

## <a name="set-up-a-development-machine"></a>A fejlesztői gépen beállítása

Ezt a lépést általában egy felhőalapú fejlesztői hajtja végre. Szoftverfrissítések egy része is hasznos lehet a értenie az adatokhoz.

Ez a cikk folyamán többek között a kódolást, fordítás, konfigurálása és üzembe helyezése IoT Edge-modulok és IoT-eszközök különböző fejlesztési feladatok elvégzése. A használat megkönnyítése érdekében létrehoztunk egy PowerShell-parancsprogram, amely létrehoz egy Azure virtuális gépen a legtöbb az előfeltételeket már be van állítva. A virtuális Gépet hozunk létre, képesnek kell lennie kezelni [beágyazott virtualizálás](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), éppen ezért választottuk a DS8V3 gép méretét.

A fejlesztési virtuális Gépet hoznak létre a:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Windows asztal](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [A Git hitelesítőadat-kezelő Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [A VS Code-bővítmények](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

A fejlesztő virtuális gép esetében nem feltétlenül szükséges – a fejlesztői eszközökből egy helyi gépen futtatható. Azonban erősen ajánlott a Virtuálisgép-mozgásteret biztosítása érdekében.

Körülbelül 30 perc alatt létrehozni és konfigurálni a virtuális gép vesz igénybe.

### <a name="get-the-script"></a>A parancsprogram beszerzése

Klónozás vagy letöltés a PowerShell-parancsprogramot a [Machine Learning és az IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) mintaadattár.

### <a name="create-an-azure-virtual-machine"></a>Az Azure virtuális gép létrehozása

A DevVM könyvtár létrehozása egy Azure virtuális gép megfelelő, az oktatóprogram elvégzéséhez szükséges fájlokat tartalmazza.

1. Nyissa meg a Powershellt rendszergazdaként, és lépjen abba a könyvtárba, amelybe letöltötte a kódot. A gyökérkönyvtár, mint a forrás hivatkozni fogunk `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. A következő parancsot a parancsfájlok futtatását teszi lehetővé. Válasszon **Igen, az összeset** amikor a rendszer kéri.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Run Create-AzureDevVM.ps1 from this directory.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Amikor a rendszer kéri, adja meg a következő információkat:
      * **Azure-előfizetés azonosítója**: Az előfizetés-azonosítója, amely az Azure Portalon található
      * **Erőforráscsoport-nevet**: Egy új vagy meglévő erőforráscsoportot az Azure-ban nevét
      * **Hely**: Válassza ki az Azure-beli hely, ahol a virtuális gép létrejön. Ha például westus2 vagy northeurope. További információkért lásd: [Azure-helyen](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Adjon meg egy könnyen megjegyezhető nevet a rendszergazdai fiók létrehozása és használata a virtuális gépen szeretné.
      * **AdminPassword**: Állítsa be a rendszergazdai fiók jelszavát a virtuális gépen.

    * Ha nincs telepítve az Azure PowerShell, a parancsfájl telepíti [Az Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * A rendszer kéri majd, hogy jelentkezzen be az Azure-ba.

    * A parancsfájl megerősíti, hogy az adatokat a virtuális gép létrehozásához. Nyomja meg `y` vagy `Enter` folytatásához.

A szkript futtatása több percig, mert a következő lépések végrehajtása:

* Ha még nem létezik, hozza létre az erőforráscsoportot
* A virtuális gép üzembe helyezése
* A virtuális gép Hyper-V engedélyezése
* Fejlesztési szoftvert kell telepíteni, és klónozza a mintatárházat
* A virtuális gép újraindítása
* Hozzon létre egy RDP-fájlt az asztalon a virtuális Géphez való csatlakozáshoz

### <a name="set-auto-shutdown-schedule"></a>Automatikus leállítási ütemezés beállítása

A költségek csökkentése érdekében a virtuális gép létrejött, amely 1900 PST automatikus leállítási ütemezéssel. Szükség lehet az időzítési attól függően, a hely és az ütemezés frissítése. A leállítási ütemezés frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a virtuális gépet az előző szakaszban megadott erőforráscsoportban.

3. Válassza ki **automatikus leállítási** meg az ügyféloldali kezelő.

4. Adja meg az új leállítási időpont **ütemezett leállítást** vagy módosítsa a **időzóna** kattintson **mentése**.

### <a name="connect-and-configure-development-machine"></a>Csatlakozás és a fejlesztői gépen konfigurálása

Most, hogy hoztunk létre egy virtuális Gépet kell a az oktatóanyag elvégzéséhez szükséges szoftverek telepítésének befejezéséhez.

#### <a name="start-a-remote-desktop-session"></a>A távoli asztali munkamenet indítása

1. A virtuális gép létrehozási parancsprogrammal létrehozva egy RDP-fájlt az asztalon.

2. Kattintson duplán a fájl nevét a  **\<Azure virtuális gép neve\>.rdp**.

3. Megjelenik egy párbeszédpanel, amely arról tájékoztat a távoli kapcsolat közzétevője ismeretlen. Kattintson a **ne kérdezzen rá ismét számítógéphez való** jelölőnégyzetet, majd válassza **Connect**.

4. Amikor a rendszer kéri, adja meg, amellyel a parancsfájl futtatásakor állítsa be a virtuális Gépet, majd kattintson a AdminPassword **OK**.

5. A rendszer felkéri a virtuális Géphez tartozó tanúsítvány elfogadása. Válassza ki **ne kérdezzen rá ismét számítógéphez való** válassza **Igen**.

#### <a name="install-visual-studio-code-extensions"></a>A Visual Studio Code-bővítmények telepítése

Most, hogy csatlakozott a fejlesztői gépen, néhány hasznos bővítményeket adhat hozzá a Visual Studio Code az élményt egyszerűbb fejlesztés.

1. Egy PowerShell-ablakban keresse meg **C:\\forrás\\IoTEdgeAndMlSample\\DevVM**.

2. Írja be a virtuális gépen futtatandó szkriptek engedélyezése.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Futtassa a szkriptet.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. A parancsprogram futtatása néhány percet a VS code-bővítmények telepítése:

    * Azure IoT-eszközök
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Az IoT Hub és a tárolás beállítása

Ezeket a lépéseket általában egy felhőalapú fejlesztői hajtja végre.

Az Azure IoT Hub bármely IoT-alkalmazás a szív. IoT-eszközök és a felhő közötti biztonságos kommunikációt kezeli. Az IoT Edge-machine learning-megoldását működéséhez a fő koordináció pont.

* Az IoT Hub IoT-eszközökről érkező adatok más alárendelt szolgáltatásokkal való közvetlen útvonalakat használ. Azt veszi igénybe az IoT Hub eszköz adatokat küldeni az Azure Storage, amelyben azt által felhasználható az Azure Machine Learning, a fennmaradó hasznos élettartama (RUL) besorolás betanítására útvonalakat.

* Az oktatóanyag későbbi részében konfigurálását és kezelését az Azure IoT Edge-eszközt az IoT Hub használjuk.

Ebben a szakaszban egy parancsfájlt használhatja az Azure IoT hub és az Azure Storage-fiók létrehozásához. Ezt követően konfigurálja egy útvonalat, amely egy Azure Storage Blob-tárolóba az Azure portal használatával a hub által fogadott adatok továbbítja. Ezeket a lépéseket a befejezéséhez körülbelül 10 percet igénybe vehet.

### <a name="create-cloud-resources"></a>Felhőalapú erőforrások létrehozása

1. A fejlesztői gépén nyissa meg egy PowerShell-ablakot.

1. Módosítsa az IoTHub-címtárhoz.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. A létrehozási parancsprogrammal. Használja ugyanazokat az értékeket előfizetési azonosító, helyen és erőforráscsoportban csoport létrehozása a virtuális gép fejlesztési hasonló módon.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * A rendszer kéri majd, hogy jelentkezzen be az Azure-ba.
    * A parancsfájl megerősíti, hogy az adatokat az Eseményközpont és a Storage-fiók létrehozásához. Nyomja meg `y` vagy `Enter` folytatásához.

A parancsfájl nagyjából két percet vesz igénybe futtatni. Ha elkészült, a parancsfájl kimenetének a hubot és a tárfiók nevére.

### <a name="review-route-to-storage-in-iot-hub"></a>Tekintse át az IoT Hub tárolási útvonalat

Az IoT hub létrehozása, a parancsfájl, amely az előző szakaszban ütköztünk is létrehozott egyéni végpontok és a egy útvonalat. Az IoT Hub útvonalak áll, a lekérdezés és a egy végpontot. Ha egy üzenet megfelel a kifejezésnek, az adatokat küld az útvonalon a kapcsolódó végpont a. -Végpontok lehetnek Event Hubs, Service Bus-üzenetsorok és témakörök. Ebben az esetben a végpont nem egy storage-fiókban található Blob-tárolóba. Az Azure portal használatával tekintse át a szkript által létrehozott útvonalban.

1. Nyissa meg az [Azure Portalt](http://portal.azure.com).

1. Válassza ki az összes szolgáltatás a bal oldali navigator szolgáltatásból, IoT írja be a keresőmezőbe, majd válassza **az IoT Hub**.

1. Válassza ki az IoT Hub az előző lépésben létrehozott.

1. Az IoT Hub ügyféloldali kezelő válassza **üzenet-útválasztása**.

1. Útválasztási lapon a két lap található, az üzenet **útvonalak** és **egyéni végpontok**. Válassza ki a **egyéni végpontok** fülre.

1. A **a Blob storage-** válassza **turbofanDeviceStorage**.

1. Vegye figyelembe, hogy ezt a végpontot egy blob-tárolóra mutat nevű **devicedata** az előző lépésben létrehozott tárfiókot, amelynek neve **iotedgeandml\<egyedi utótagja\>** .

1. Azt is vegye figyelembe a **Blob fájlnévformátuma** az alapértelmezett formátumból, ehelyett helyezi, az utolsó elem neve az a partíció módosítva lett. Találtunk, ez a formátum akkor kényelmesebb, a fájl műveletek, az oktatóanyag későbbi részében fogunk jutni az Azure-jegyzetfüzeteket.

1. A végpont részletei panelen való visszatéréshez zárja be a **üzenet-útválasztása** lapot.

1. Válassza ki a **útvonalak** fülre.

1. Válassza ki a nevű útvonal **turbofanDeviceDataToStorage**.

1. Vegye figyelembe, hogy az útvonal a végpont a **turbofanDeviceStorage** egyéni végpont.

1. Tekintse meg az útválasztási lekérdezés, amely van beállítva **igaz**. Ez azt jelenti, hogy az összes eszköz telemetriai üzeneteket egyezni fog a ezt az útvonalat, és ezért minden üzenetet küld a **turbofanDeviceStorage** végpont.

1. Zárja be a útvonal részleteit.

## <a name="next-steps"></a>További lépések

Ebben a cikkben azt létrehozott egy IoT hubot és a egy útvonalat az Azure Storage-fiók konfigurálva. A következő cikkben küldünk adatok közül, az IoT hubon keresztül a szimulált eszközök a tárfiókba. Az oktatóanyag későbbi részében után az IoT Edge-eszköz és a modulok, konfigurált azt fogja nyissa meg újra az útvonalakat, és keresse meg az útválasztási lekérdezés egy kicsit több.

A lépéseket, az IoT Edge az oktatóanyag ezen részében a Machine Learning kezelt kapcsolatos további információkért lásd:

* [Azure IoT – alapok](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Az IoT Hub üzenet-útválasztás konfigurálása](../iot-hub/tutorial-routing.md)
* [Hozzon létre egy IoT hubra az Azure portal használatával](../iot-hub/iot-hub-create-through-portal.md)

Továbbra is a következő cikk figyelése egy szimulált eszköz létrehozása.

> [!div class="nextstepaction"]
> [Eszközadatok készítése](tutorial-machine-learning-edge-03-generate-data.md)
