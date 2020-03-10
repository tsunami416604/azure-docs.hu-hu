---
title: Modulok fejlesztése és hibakeresése Azure IoT Edgehoz | Microsoft Docs
description: A Visual Studio Code használatával olyan modulokat fejleszthet, építhet ki és kereshet Azure IoT Edge a C#, Python, Node. js, Java vagy C használatával.
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944300"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Modulok fejlesztése és hibakeresése a Visual Studio Code használatával Azure IoT Edge

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code-ot a modulok fejlesztésére és hibakeresésére szolgáló fő eszközként.

A Visual Studio Code-ban C#a (z), a Node. js vagy a Java nyelveken írt modulok hibakeresése kétféleképpen lehetséges: a modul-tárolóban létrehozhat egy folyamatot, vagy hibakeresési módban elindíthatja a modul kódját. A Pythonban vagy C-ben írt modulok hibakereséséhez csak Linux amd64-tárolókban lévő folyamatokhoz lehet kapcsolódni.

Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el a [hibakeresést](https://code.visualstudio.com/Docs/editor/debugging)ismertető cikkét.

Ez a cikk a modulok különböző nyelveken történő fejlesztéséhez és hibakereséséhez nyújt útmutatást több architektúrához. A Visual Studio Code jelenleg a, a C, a C#Python, a Node. js és a Java nyelven írt modulokhoz nyújt támogatást. A támogatott eszközök architektúrái az x64 és a ARM32. További információ a támogatott operációs rendszerekről, nyelvekről és architektúráról: [nyelvi és architektúra-támogatás](module-development.md#language-and-architecture-support).

>[!NOTE]
>A Linux ARM64-eszközök fejlesztésének és hibakeresésének támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el. További információ: [ARM64 IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio Code-ban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Előfeltételek

Fejlesztői számítógépként egy Windows, macOS vagy Linux rendszerű számítógépet vagy virtuális gépet is használhat. Windows rendszerű számítógépeken Windows-és Linux-modulokat is fejleszthet. Windows-modulok fejlesztéséhez használjon 1809/Build 17763 vagy újabb verziót futtató Windows-számítógépet. Linux-modulok fejlesztéséhez használjon olyan Windows-számítógépet, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Először telepítse a [Visual Studio Code](https://code.visualstudio.com/) -ot, majd adja hozzá a következő bővítményeket:

- [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- A fejlesztéshez használt nyelvhez tartozó Visual Studio-bővítmény (ek):
  - C#, beleértve a Azure functions: [ C# bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [a Visual Studio Code-hoz készült Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [c/C++ bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Emellett további, nyelvfüggő eszközöket is telepítenie kell a modul fejlesztéséhez:

- C#, beleértve a Azure Functions: [.net Core 2,1 SDK](https://www.microsoft.com/net/download)

- Python: Python és [pip](https://pip.pypa.io/en/stable/installing/#installation) [a Python-](https://www.python.org/downloads/) csomagok telepítéséhez (jellemzően a Python-telepítés részeként).

- Node. js: [Node. js](https://nodejs.org). Emellett telepítenie kell a [Yeoman](https://www.npmjs.com/package/yo) -t és a [Azure IoT Edge Node. js-modul generátort](https://www.npmjs.com/package/generator-azure-iot-edge-module)is.

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) és [Maven](https://maven.apache.org/). [Be kell állítania a `JAVA_HOME` környezeti változót](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) , hogy a JDK-telepítésre mutasson.

A modul lemezképének létrehozásához és üzembe helyezéséhez a Docker-nek létre kell hoznia a modul lemezképét és egy tároló-beállításjegyzéket a modul lemezképének tárolásához:

- A [Docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható.

Ha nem fejleszti a modult a C-ben, akkor a Python-alapú [Azure IoT EdgeHub dev eszközre](https://pypi.org/project/iotedgehubdev/) is szüksége lesz a helyi fejlesztési környezet beállításához a IoT Edge megoldás hibakereséséhez, futtatásához és teszteléséhez. Ha még nem tette meg, telepítse a [Pythont (2.7/3.6/3.7) és a pip](https://www.python.org/) -et, majd telepítse a **iotedgehubdev** a parancs futtatásával a terminálon.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> A iotedgehubdev jelenleg a Python 3,8-es verzióval nem kompatibilis Docker-a kódtárat használ.
>
> Ha több Python is van, beleértve az előre telepített Python 2,7-et (például Ubuntu vagy macOS rendszeren), győződjön meg arról, hogy a megfelelő `pip` vagy `pip3`t használja a **iotedgehubdev** telepítéséhez

A modul eszközön való teszteléséhez egy aktív IoT hub szükséges legalább egy IoT Edge eszközzel. Ha a számítógépet IoT Edge eszközként szeretné használni, kövesse a [Linux](quickstart-linux.md) vagy [Windows rendszerhez](quickstart.md)készült gyors útmutató lépéseit. Ha IoT Edge démont futtat a fejlesztői gépen, előfordulhat, hogy a következő lépésre való áttérés előtt le kell állítania a EdgeHub és a EdgeAgent.

## <a name="create-a-new-solution-template"></a>Új megoldás sablonjának létrehozása

A következő lépések bemutatják, hogyan hozhat létre IoT Edge modult az előnyben részesített fejlesztői nyelvben (beleértve C#a Azure Functionst is) a Visual Studio Code és az Azure IoT Tools használatával. Először hozzon létre egy megoldást, majd hozza létre a megoldás első modulját. Minden megoldás több modult is tartalmazhat.

1. Válassza a **nézet** > a **parancs-paletta**lehetőséget.

1. A Command paletta írja be és futtassa a parancsot **Azure IoT Edge: új IoT Edge megoldás**.

   ![Új IoT Edge megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

1. Tallózással keresse meg azt a mappát, amelyben létre szeretné hozni az új megoldást, majd válassza a **mappa kiválasztása**lehetőséget.

1. Adja meg a megoldás nevét.

1. Válasszon ki egy modul-sablont az előnyben részesített fejlesztői nyelvhez, hogy az a megoldás első modulja legyen.

1. Adja meg a modul nevét. Válassza ki a tároló beállításjegyzékében egyedi nevet.

1. Adja meg a modul rendszerkép-tárházának nevét. A Visual Studio Code automatikusan feltölti a modul nevét a **localhost: 5000/< a modul neve\>** . Cserélje le a saját beállításjegyzék-információkat. Ha a teszteléshez helyi Docker-beállításjegyzéket használ, a **localhost** rendben van. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló úgy néz ki, mint  **_\<beállításjegyzék neve\>_ . azurecr.IO**. Csak a karakterlánc következő helyére írja a **localhost: 5000** részét, hogy a végeredmény a **\<*beállításjegyzék neve*\>. azurecr.io/ _\<a modul neve\>_** .

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-csharp-module/repository.png)

A Visual Studio Code felveszi a megadott adatokat, létrehoz egy IoT Edge megoldást, majd betölti azt egy új ablakban.

A megoldásban négy elem található:

- A **. vscode** mappa hibakeresési konfigurációkat tartalmaz.

- A **modulok** mappa almappákkal rendelkezik az egyes modulokhoz.  Az egyes modulok mappáján belül található a **Module. JSON**fájl, amely azt szabályozza, hogyan épül fel és helyezhetők üzembe a modulok.  Ezt a fájlt módosítani kell, hogy módosítsa a modul üzembe helyezési tárolójának beállításjegyzékét a localhost-ból egy távoli beállításjegyzékbe. Ezen a ponton csak egy modul van.  A parancssorban azonban további részleteket is hozzáadhat a **Azure IoT Edge: add IoT Edge modul**parancshoz.

- A **. env** fájl felsorolja a környezeti változókat. Ha Azure Container Registry a beállításjegyzékben, akkor Azure Container Registry felhasználónevét és jelszavát fogja tartalmazni.

  > [!NOTE]
  > A környezeti fájl csak akkor jön létre, ha megadja a modulhoz tartozó rendszerkép-tárházat. Ha elfogadta a localhost alapértelmezéseket a helyi teszteléshez és hibakereséshez, akkor nem kell deklarálnia környezeti változókat.

- A **Deployment. template. JSON** fájl felsorolja az új modult, valamint egy minta **SimulatedTemperatureSensor** modult, amely szimulálja a teszteléshez használható adatforrásokat. További információ az üzembe helyezési jegyzékek működéséről: az üzembe helyezési [jegyzékek használata modulok üzembe helyezéséhez és útvonalak létrehozásához](module-composition.md).

A szimulált hőmérséklet-modul működésének megtekintéséhez tekintse meg a [SimulatedTemperatureSensor. csproj forráskódot](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>További modulok hozzáadása

Ha további modulokat szeretne felvenni a megoldásba, futtassa a következő parancsot **Azure IoT Edge: IoT Edge modul hozzáadása** a parancssorból. Azt is megteheti, hogy a jobb gombbal a **modulok** mappára vagy a `deployment.template.json` fájlra kattint a Visual Studio Code Explorer nézetében, majd kiválasztja **IoT Edge modul hozzáadása**lehetőséget.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldás alapértelmezett moduljának kódja a következő helyen található:

- Azure Function (C#): **modulok > *&lt;a modul neve&gt;*  >  *&lt;a modul neve&gt;* . cs**
- C#: **modulok > *&lt;a modul neve&gt;* > program.cs**
- Python: **modulok > *&lt;a modul neve&gt;* > Main.py**
- Node. js: **modulok > *&lt;a modul neve&gt;* > app. js**
- Java: **modulok > *&lt;a modul neve&gt;* > src > main > java > com > edgemodulemodules > app. Java**
- C: **modulok > *&lt;a modul neve&gt;* > Main. c**

A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul úgy van felépítve, hogy egyszerűen egy forrásból (ebben az esetben az adatokat szimuláló SimulatedTemperatureSensor modulból) beírja a bemenetet, és átadja a IoT Hubnak.

Ha készen áll a sablon testre szabására a saját kódjával, az [Azure IoT hub SDK](../iot-hub/iot-hub-devguide-sdks.md) -k segítségével olyan modulokat hozhat létre, amelyek a IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság szempontjából szükségesek.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Modul hibakeresése tároló nélkül (C#, Node. js, Java)

Ha a-ben, C#a Node. js-ben vagy a Java-ban fejleszt, a modulnak egy **ModuleClient** -objektumot kell használnia az alapértelmezett modul kódjában, hogy el tudja indítani, futtatni és továbbítani az üzeneteket. Az alapértelmezett bemeneti csatorna **input1** is használhatja, ha a modul üzeneteket fogad.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge szimulátor beállítása IoT Edge megoldáshoz

A fejlesztői gépen elindíthat egy IoT Edge szimulátort a IoT Edge biztonsági démon telepítése helyett, hogy futtatni tudja a IoT Edge megoldást.

1. A bal oldalon található Eszközkezelőben kattintson a jobb gombbal a IoT Edge eszköz AZONOSÍTÓJÁRA, majd válassza a **telepítő IoT Edge szimulátor** lehetőséget, hogy elindítsa a szimulátort az eszköz kapcsolódási karakterláncával.
1. A IoT Edge szimulátort úgy is megtekintheti, hogy sikeresen beolvasta a folyamat részleteit az integrált terminálon.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>IoT Edge szimulátor beállítása egyetlen Modulos alkalmazáshoz

A szimulátor beállításához és elindításához futtassa a **következő parancsot Azure IoT Edge: start IoT Edge hub Simulator egyetlen modulhoz** a Visual Studio Code Command paletta használatával. Ha a rendszer kéri, használja a **input1** értéket az alapértelmezett modul kódjából (vagy a kód megfelelő értékével) az alkalmazás bemeneti neveként. A parancs elindítja a **iotedgehubdev** CLI-t, majd elindítja a IoT Edge szimulátort és a tesztelési segédprogram moduljának tárolóját. Az alábbi kimeneteket az integrált terminálon tekintheti meg, ha a szimulátort egy Modulos módban indította el sikeresen. `curl` parancs is megjelenik, amely segítséget nyújt az üzenetek küldéséhez. Erre később még szüksége lesz.

   ![IoT Edge szimulátor beállítása egyetlen Modulos alkalmazáshoz](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   A modul futó állapotának megtekintéséhez használhatja a Docker Explorer nézetet a Visual Studio Code-ban.

   ![Szimulátor modul állapota](media/how-to-develop-csharp-module/simulator-status.png)

   A **edgeHubDev** tároló a helyi IoT Edge szimulátor magja. A IoT Edge biztonsági démon nélkül is futtatható a fejlesztői gépen, és környezeti beállításokat biztosít a natív modul-alkalmazáshoz vagy modul-tárolóhoz. A **bemeneti** tároló REST API-kat tesz elérhetővé, hogy az üzeneteket a modul cél bemeneti csatornáján keresztül áthidalja.

### <a name="debug-module-in-launch-mode"></a>Hibakeresési modul indítási módban

1. Készítse elő a környezetet a hibakereséshez a fejlesztési nyelv követelményeinek megfelelően, állítson be egy töréspontot a modulban, és válassza ki a használni kívánt hibakeresési konfigurációt:
   - **C#**
     - A Visual Studio Code integrált terminálban módosítsa a könyvtárat a ***&lt;a modul neve&gt;*** mappába, majd futtassa a következő parancsot a .net Core-alkalmazás létrehozásához.

       ```cmd
       dotnet build
       ```

     - Nyissa meg `Program.cs` fájlt, és adjon hozzá egy töréspontot.

     - Navigáljon a Visual Studio Code hibakeresés nézetéhez, és válassza a **megtekintés > hibakeresés**lehetőséget. Válassza ki a hibakeresési konfigurációt,  ***&lt;a modul neve&gt;* helyi hibakeresés (.net Core)** elemet a legördülő listából.

        > [!NOTE]
        > Ha a .NET Core `TargetFramework` nem konzisztens a program elérési útjának `launch.json`jában, akkor manuálisan kell frissítenie a program elérési útját a `launch.json`ban, hogy az megfeleljen a. csproj fájl `TargetFramework`ának, hogy a Visual Studio Code sikeresen elindítsa a programot.

   - **Node.js**
     - A Visual Studio Code integrált terminálban módosítsa a könyvtárat a ***&lt;a modul neve&gt;*** mappába, majd futtassa a következő parancsot a csomópont-csomagok telepítéséhez.

       ```cmd
       npm install
       ```

     - Nyissa meg `app.js` fájlt, és adjon hozzá egy töréspontot.

     - Navigáljon a Visual Studio Code hibakeresés nézetéhez, és válassza a **megtekintés > hibakeresés**lehetőséget. Válassza ki a hibakeresési konfigurációt,  ***&lt;a modul neve&gt;* helyi hibakeresés (node. js)** a legördülő listából.
   - **Java**
     - Nyissa meg `App.java` fájlt, és adjon hozzá egy töréspontot.

     - Navigáljon a Visual Studio Code hibakeresés nézetéhez, és válassza a **megtekintés > hibakeresés**lehetőséget. Válassza ki a hibakeresési konfigurációt,  ***&lt;a modul neve&gt;* helyi hibakeresés (Java)** lehetőséget a legördülő listából.

1. A hibakeresési munkamenet elindításához kattintson a **hibakeresés indítása** elemre, vagy nyomja le az **F5** billentyűt.

1. A Visual Studio Code integrált terminálján futtassa a következő parancsot, hogy **"Helló világ!" alkalmazás** üzenetet küldjön a modulnak. Ez a parancs IoT Edge szimulátor beállításakor az előző lépésekben látható.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Ha Windows rendszert használ, győződjön meg róla, hogy a Visual Studio Code integrált termináljának rendszerhéja **git bash** vagy **WSL bash**. A `curl` parancs nem futtatható PowerShellből vagy parancssorból.
   > [!TIP]
   > A [Poster](https://www.getpostman.com/) vagy más API-eszközöket is használhatja a `curl`helyett az üzenetek küldéséhez.

1. A Visual Studio Code hibakeresés nézetében a változók a bal oldali panelen jelennek meg.

1. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra, vagy nyomja le a **SHIFT + F5**billentyűkombinációt, majd futtassa Azure IoT Edge: a parancs paletta leállításához állítsa le a **IoT Edge szimulátort** , és törölje a szimulátort.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Hibakeresés csatolt módban IoT Edge szimulátorral (C#, Node. js, Java, Azure functions)

Az alapértelmezett megoldás két modult tartalmaz, az egyik egy szimulált hőmérséklet-érzékelő modul, a másik pedig a cső modul. A szimulált hőmérséklet-érzékelő üzeneteket küld a pipe-modulnak, majd az üzenetek a IoT Hubba lesznek átirányítva. A létrehozott modul mappában több Docker-fájl is található a különböző típusú tárolók esetében. Használja a bővítmény **. debug** kiterjesztésű fájlok bármelyikét, hogy kiépítse a modult a teszteléshez.

Jelenleg a csatolási módban végzett hibakeresés csak a következőképpen támogatott:

- C#a modulok, beleértve a Azure Functionst, támogatják a hibakeresést a Linux amd64-tárolókban
- A Node. js modulok támogatják a Linux amd64 és a arm32v7-tárolók, valamint a Windows amd64-tárolók hibakeresését.
- A Java-modulok támogatják a hibakeresést a Linux amd64 és a arm32v7-tárolókban

> [!TIP]
> A IoT Edge megoldás alapértelmezett platformjának beállításai között válthat a Visual Studio Code állapotsorban található elemre kattintva.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge szimulátor beállítása IoT Edge megoldáshoz

A fejlesztői gépen elindíthat egy IoT Edge szimulátort a IoT Edge biztonsági démon telepítése helyett, hogy futtatni tudja a IoT Edge megoldást.

1. A bal oldalon található Eszközkezelőben kattintson a jobb gombbal a IoT Edge eszköz AZONOSÍTÓJÁRA, majd válassza a **telepítő IoT Edge szimulátor** lehetőséget, hogy elindítsa a szimulátort az eszköz kapcsolódási karakterláncával.

1. A IoT Edge szimulátort úgy is megtekintheti, hogy sikeresen beolvasta a folyamat részleteit az integrált terminálon.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Tároló létrehozása és futtatása a hibakereséshez és a hibakereséshez csatolási módban

1. Nyissa meg a modul fájlját (`Program.cs`, `app.js`, `App.java`vagy `<your module name>.cs`), és adjon hozzá egy töréspontot.

1. A Visual Studio Code Explorer nézetben kattintson a jobb gombbal a megoldás `deployment.debug.template.json` fájljára, majd válassza a **IoT Edge megoldás létrehozása és futtatása szimulátorban**lehetőséget. Megtekintheti az összes modul-tároló naplóját ugyanabban az ablakban. A tároló állapotának megtekintéséhez a Docker-nézetet is használhatja.

   ![Változók megtekintése](media/how-to-vs-code-develop-module/view-log.png)

1. Navigáljon a Visual Studio Code hibakeresés nézetéhez, és válassza ki a modul hibakeresési konfigurációs fájlját. A hibakeresési lehetőség nevének hasonlónak kell lennie ahhoz,  ***&lt;a modul neve&gt;* távoli hibakeresés**

1. Válassza a **hibakeresés elindítása** vagy az **F5**billentyűt. Válassza ki a csatolni kívánt folyamatot.

1. A Visual Studio Code hibakeresés nézetében a változók a bal oldali panelen jelennek meg.

1. A hibakeresési munkamenet leállításához először válassza a Leállítás gombot, vagy nyomja le a **SHIFT + F5**billentyűkombinációt, majd válassza a **Azure IoT Edge: IoT Edge Simulator leállítása** a parancssorból lehetőséget.

> [!NOTE]
> Az előző példa bemutatja, hogyan lehet hibakeresést végezni a tárolók IoT Edge moduljaiban. Hozzá lett adva a portok a modul tárolójának `createOptions` beállításaihoz. A modulok hibakeresésének befejezése után javasoljuk, hogy távolítsa el ezeket a kitett portokat az éles használatra kész IoT Edge modulokhoz.
>
> A (z) C#rendszerben Azure functions írt modulok esetében ez a példa a `Dockerfile.amd64.debug`hibakeresési verziójára épül, amely magában foglalja a .net Core parancssori hibakeresőt (VSDBG) a tároló rendszerképében a létrehozás során. A C# modulok hibakeresése után javasoljuk, hogy közvetlenül a Docker VSDBG nélkül használja az éles használatra kész IoT Edge modulokhoz.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Modul hibakeresése a IoT Edge futtatókörnyezettel

Minden modul mappában több Docker-fájl található a különböző típusú tárolók esetében. Használja a bővítmény **. debug** kiterjesztésű fájlok bármelyikét, hogy kiépítse a modult a teszteléshez.

Ha ezt a metódust használó modulok hibakeresését végzi, a modulok a IoT Edge futtatókörnyezeten felül futnak. A IoT Edge eszköz és a Visual Studio-kód ugyanazon a gépen lehet, vagy általában a Visual Studio Code a fejlesztői gépen található, és a IoT Edge futtatókörnyezet és a modulok egy másik fizikai gépen futnak. A Visual Studio Code-ból való hibakereséshez a következőket kell tennie:

- Állítsa be IoT Edge eszközét, hozza létre a IoT Edge modul (oka) t a **. debug** Docker, majd telepítse a IoT Edge eszközre.
- Tegye elérhetővé a modul IP-címét és portját, hogy a hibakereső csatolható legyen.
- Frissítse a `launch.json`t, hogy a Visual Studio Code képes legyen a távoli gépen lévő tárolóban lévő folyamathoz csatolni. Ez a fájl a munkaterület `.vscode` mappájában található, és minden alkalommal frissül, amikor új modult ad hozzá, amely támogatja a hibakeresést.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>A modul felépítése és üzembe helyezése a IoT Edge eszközön

1. A Visual Studio Code-ban nyissa meg a `deployment.debug.template.json` fájlt, amely a megfelelő `createOptions` értékekkel rendelkező modul-lemezképek hibakeresési verzióját tartalmazza.

1. Ha a modult a Pythonban fejleszti, kövesse az alábbi lépéseket a továbblépés előtt:
   - Nyissa meg `main.py` fájlt, és adja hozzá ezt a kódot az importálás szakasz után:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adja hozzá a következő egysoros programkódot a hibakereséshez használni kívánt visszahíváshoz:

      ```python
      ptvsd.break_into_debugger()
      ```

     Ha például az `receive_message_listener` függvényt szeretné lekérdezni, a kódot a lent látható módon kell beszúrnia:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. A Visual Studio Code parancs palettáján:
   1. Futtassa a **Azure IoT Edge parancsot: IoT Edge-megoldás létrehozása és leküldése**.

   1. Válassza ki a megoldás `deployment.debug.template.json`-fájlját.

1. A Visual Studio Code Explorer nézet **Azure IoT hub eszközök** szakaszában:
   1. Kattintson a jobb gombbal egy IoT Edge eszköz AZONOSÍTÓra, majd válassza **a központi telepítés létrehozása egyetlen eszközhöz**lehetőséget.

      > [!TIP]
      > Annak ellenőrzéséhez, hogy a kiválasztott eszköz egy IoT Edge eszköz-e, válassza ki a modulok listájának kibontásához és a **$edgeHub** és a **$edgeAgent**meglétének ellenőrzéséhez. Minden IoT Edge eszköz tartalmazza ezt a két modult.

   1. Navigáljon a megoldás **konfigurációs** mappájához, válassza ki a `deployment.debug.amd64.json` fájlt, majd válassza az **Edge központi telepítési jegyzék kiválasztása**lehetőséget.

A központi telepítés sikeresen létrejött a központi telepítési AZONOSÍTÓval az integrált terminálon.

A tároló állapotát úgy tekintheti meg, ha a terminálon a `docker ps` parancsot futtatja. Ha a Visual Studio Code és a IoT Edge Runtime ugyanazon a gépen fut, akkor a Visual Studio Code Docker nézetben is megtekintheti az állapotot.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Tegye elérhetővé a hibakereső moduljának IP-címét és portját

Ezt a szakaszt kihagyhatja, ha a modulok ugyanazon a gépen futnak, mint a Visual Studio Code, mivel a localhost használatával csatlakozik a tárolóhoz, és már rendelkezik a megfelelő portbeállítások a (z **). debug** Docker, a modul tárolójában `createOptions` beállítások és `launch.json` fájl. Ha a modulok és a Visual Studio Code különálló gépeken fut, kövesse a fejlesztői nyelv lépéseit.

- **C#, beleértve a Azure Functions**

  [Konfigurálja az SSH-csatornát a fejlesztői gépen, és IoT Edge eszközön](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) , majd szerkessze `launch.json` fájlt a csatoláshoz.

- **Node.js**

  - Ellenőrizze, hogy fut-e a számítógép modulja, és készen áll-e a Hibakeresők csatolására, és hogy az 9229-es port külsőleg elérhető-e. Ezt úgy ellenőrizheti, ha megnyitja `http://<target-machine-IP>:9229/json` a hibakereső gépen. Ennek az URL-címnek meg kell jelenítenie a dehibakereső Node. js-modul információit.
  
  - A fejlesztői gépen nyissa meg a Visual Studio Code-ot, majd szerkessze `launch.json` úgy, hogy az  ***&lt;a modul neve&gt;* távoli hibakeresés (node. js)** profil (vagy  ***&lt;a modul neve&gt;* távoli hibakeresés (node. js a Windows-tárolóban)** profilt, ha a modul Windows-tárolóként fut, a hibakereső gép IP-címe.

- **Java**

  - Hozzon létre egy SSH-alagutat a számítógépnek a `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`futtatásával.
  
  - A fejlesztői gépen nyissa meg a Visual Studio Code-ot, és szerkessze a  ***&lt;a modul neve&gt;* távoli hibakeresési (Java)** profilban a `launch.json`, hogy csatolható legyen a célszámítógépen. Ha többet szeretne megtudni a Visual Studio Code-ban található `launch.json` szerkesztéséről és a Java hibakereséséről, tekintse meg a [hibakereső konfigurálásának](https://code.visualstudio.com/docs/java/java-debugging#_configuration)szakaszát.

- **Python**

  - Győződjön meg arról, hogy a számítógép 5678-es portja nyitva van és elérhető a hibakereséshez.

  - A `main.py`ba korábban beszúrt kódban `ptvsd.enable_attach(('0.0.0.0', 5678))` a kipróbálni kívánt gép IP-címére módosítsa a **0.0.0.0** -et. Hozza létre, küldje el és telepítse újra IoT Edge modulját.

  - A fejlesztői gépen nyissa meg a Visual Studio Code-ot, majd szerkessze `launch.json` úgy, hogy a  ***modul neve&gt;* Remote debug (Python) profil&lt;** `host` értéke `localhost`helyett a célszámítógép IP-címét használja.

### <a name="debug-your-module"></a>A modul hibakeresése

1. A Visual Studio Code hibakeresés nézetében válassza ki a modul hibakeresési konfigurációs fájlját. A hibakeresési lehetőség nevének hasonlónak kell lennie ahhoz,  ***&lt;a modul neve&gt;* távoli hibakeresés**

1. Nyissa meg a modul-fájlt a fejlesztési nyelvhez, és adjon hozzá egy töréspontot:

   - **Azure Function (C#)** : adja hozzá a töréspontot a fájlhoz `<your module name>.cs`.
   - **C#** : Adja hozzá a töréspontot a fájlhoz `Program.cs`.
   - **Node. js**: adja hozzá a töréspontot a fájlhoz `app.js`.
   - **Java**: adja hozzá a töréspontot a fájlhoz `App.java`.
   - **Python**: adja hozzá a töréspontot a fájlhoz `main.py`a visszahívási metódusban, ahol a `ptvsd.break_into_debugger()` sort adta hozzá.
   - **C**: adja hozzá a töréspontot a fájlhoz `main.c`.

1. Válassza a **hibakeresés indítása** lehetőséget, vagy válassza az **F5 billentyűt**. Válassza ki a csatolni kívánt folyamatot.

1. A Visual Studio Code hibakeresés nézetében a változók a bal oldali panelen jelennek meg.

> [!NOTE]
> Az előző példa bemutatja, hogyan lehet hibakeresést végezni a tárolók IoT Edge moduljaiban. Hozzá lett adva a portok a modul tárolójának `createOptions` beállításaihoz. A modulok hibakeresésének befejezése után javasoljuk, hogy távolítsa el ezeket a kitett portokat az éles használatra kész IoT Edge modulokhoz.

## <a name="build-and-debug-a-module-remotely"></a>Modul létrehozása és hibakeresése távolról

Az SSH-kapcsolatok támogatásához a Docker és a Moby Engine legújabb változásai, valamint az Azure IoT-eszközök új beállítása, amely lehetővé teszi a környezeti beállítások beadását a Visual Studio Code Command paletta és Azure IoT Edge terminálok számára, mostantól felépítheti és hibakeresést végezhet távoli eszközökön található modulok.

További információért és részletes utasításokért tekintse meg ezt a [IoT fejlesztői blogbejegyzését](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) .

## <a name="next-steps"></a>További lépések

A modul létrehozása után megtudhatja, hogyan [helyezhet üzembe Azure IoT Edge modulokat a Visual Studio Code-ból](how-to-deploy-modules-vscode.md).

A IoT Edge-eszközökhöz készült modulok fejlesztéséhez, az [Azure IoT hub SDK-k megismeréséhez és használatához](../iot-hub/iot-hub-devguide-sdks.md).
