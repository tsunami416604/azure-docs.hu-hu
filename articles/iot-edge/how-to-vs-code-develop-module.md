---
title: Modulok fejlesztése és hibakeresése az Azure IoT Edge-hez | Microsoft dokumentumok
description: A Visual Studio-kód használatával fejleszthet, hozhat létre és debugolhatonja az Azure IoT Edge modulját C#, Python, Node.js, Java vagy C használatával
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944300"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Az Azure IoT Edge moduljainak fejlesztése és hibakeresése a Visual Studio-kóddal

Az üzleti logikát az Azure IoT Edge moduljaivá alakíthatja. Ez a cikk bemutatja, hogyan használhatja a Visual Studio Code-ot a modulok fejlesztésének és hibakeresésének fő eszközeként.

A Visual Studio-kódban C#, Node.js vagy Java nyelven írt modulok at kétféleképpen lehet hibakeresést indítani: A modultárolóban csatolhat egy folyamatot, vagy hibakeresési módban elindíthatja a modulkódot. Pythonban vagy C-ben írt modulok hibakereséséhez csak Linux amd64 tárolókban csatolhat egy folyamathoz.

Ha nem ismeri a Visual Studio-kód hibakeresési funkcióit, olvassa el a [Hibakeresést](https://code.visualstudio.com/Docs/editor/debugging)című!

Ez a cikk több nyelven, több architektúra moduljainak fejlesztésére és hibakeresésére vonatkozó utasításokat tartalmaz. Jelenleg a Visual Studio Code támogatja a C#, C, Python, Node.js és Java nyelven írt modulokat. A támogatott eszközarchitektúrák az X64 és az ARM32. A támogatott operációs rendszerekről, nyelvekről és architektúrákról a [Nyelvi és architektúra támogatása](module-development.md#language-and-architecture-support)című témakörben talál további információt.

>[!NOTE]
>A Linux ARM64-eszközök fejlesztéséhez és hibakereséséhez nyújtott támogatás [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)érhető el. További információ: [ARM64 IoT Edge modulok fejlesztése és hibakeresése a Visual Studio Code programban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Előfeltételek

Fejlesztői gépként windowsos, macOS vagy Linux operációs rendszert futtató számítógépet vagy virtuális gépet is használhat. Windows rendszerű számítógépeken Windows vagy Linux modulok at fejleszthet. A Windows-modulok fejlesztéséhez használja a 1809/build 17763-as vagy újabb verziójú Windows számítógépet. Linux-modulok fejlesztéséhez olyan Windows-számítógépet használjon, amely megfelel a [Docker Desktop követelményeinek.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Először telepítse a [Visual Studio-kódot,](https://code.visualstudio.com/) majd adja hozzá a következő bővítményeket:

- [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- A Visual Studio bővítmény(eke)t a fejlesztési nyelvre jellemző:
  - C#, beleértve az Azure Functions: [C# kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java extension pack for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

A modul fejlesztéséhez további, nyelvspecifikus eszközöket is telepítenie kell:

- C#, beleértve az Azure Functionst: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) és [Pip](https://pip.pypa.io/en/stable/installing/#installation) python-csomagok telepítéséhez (általában a Python-telepítés része).

- Node.js: [Node.js](https://nodejs.org). Érdemes telepíteni e [Yeoman](https://www.npmjs.com/package/yo) és az [Azure IoT Edge Node.js modulgenerátor.](https://www.npmjs.com/package/generator-azure-iot-edge-module)

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) és [Maven](https://maven.apache.org/). Be kell [állítania `JAVA_HOME` a környezeti változót,](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) hogy a JDK-telepítésre mutasson.

A modulrendszer képének létrehozásához és üzembe helyezéséhez a Modulrendszerkép és a modulrendszer-rendszerkép tárolására a Docker-lemezkép létrehozásához szüksége van:

- [Docker Community Edition](https://docs.docker.com/install/) a fejlesztőgépen.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > A felhőbeállítás-beállításjegyzék helyett használhat egy helyi Docker-beállításjegyzéket prototípus- és tesztelési célokra.

Hacsak nem c-ben fejleszti a modult, a Python-alapú [Azure IoT EdgeHub dev-eszközre](https://pypi.org/project/iotedgehubdev/) is szüksége van a helyi fejlesztési környezet beállításához az IoT Edge-megoldás hibakereséséhez, futtatásához és teszteléséhez. Ha még nem tette meg, telepítse a [Pythont (2.7/3.6/3.7) és a Pip-et,](https://www.python.org/) majd telepítse az **iotedgehubdev-et** a parancs terminálon való futtatásával.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Jelenleg iotedgehubdev használ egy docker-py könyvtár, amely nem kompatibilis a Python 3.8.Currently, iotedgehubdev használ egy docker-py könyvtár, amely nem kompatibilis a Python 3.8.Currently, iotedgehubdev használ egy docker-py könyvtárat.
>
> Ha több Python, beleértve az előre telepített python 2.7 (például ubuntu vagy macOS), győződjön meg róla, hogy a megfelelő `pip` vagy `pip3` telepíteni **iotedgehubdev**

A modul teszteléséhez egy eszközön, szüksége lesz egy aktív IoT hub legalább egy IoT Edge-eszközzel. A számítógép IoT Edge-eszközként való használatához kövesse a [Linux](quickstart-linux.md) vagy [Windows](quickstart.md)gyorshasználatának lépéseit. Ha iot edge démont futtat a fejlesztői gépen, előfordulhat, hogy le kell állítania az EdgeHubot és az EdgeAgentet, mielőtt továbblépne a következő lépésre.

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy IoT Edge-modult a preferált fejlesztési nyelven (beleértve az Azure Functions,C#-ben írt) a Visual Studio-kód és az Azure IoT-eszközök használatával. Először hozzon létre egy megoldást, majd hozza létre az első modult a megoldásban. Minden megoldás több modult is tartalmazhat.

1. Válassza **a Nézet** > **parancspaletta**lehetőséget .

1. A parancspalettán adja meg és futtassa az **Azure IoT Edge: Új IoT Edge-megoldás parancsot.**

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

1. Tallózással keresse meg azt a mappát, amelyben létre szeretné hozni az új megoldást, majd válassza **a Mappa kijelölése**lehetőséget.

1. Adja meg a megoldás nevét.

1. Válasszon ki egy modulsablont a kívánt fejlesztési nyelvhez, hogy a megoldás első modulja legyen.

1. Adja meg a modul nevét. Válasszon olyan nevet, amely egyedi a tároló beállításjegyzékében.

1. Adja meg a modul képtárának nevét. A Visual Studio-kód automatikusan feltölti a modul nevét a **localhost:5000/<modulnevével.\>** Cserélje le a saját rendszerleíró adatbázisadataira. Ha egy helyi Docker-beállításjegyzéket használ a teszteléshez, akkor a **localhost** rendben van. Ha az Azure Container Registry, majd használja a bejelentkezési kiszolgálót a rendszerleíró adatbázis beállításait. A bejelentkezési kiszolgáló a ** _ \<rendszerleíró adatbázis "azurecr.io\>._** Csak a string **localhost:5000** részét cserélje le úgy, hogy a végeredmény a ** \< *rendszerleíró adatbázis nevére*\>(azurecr.io/_\<a modul nevére\>_**.

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-csharp-module/repository.png)

A Visual Studio Code átveszi a megadott információkat, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakba.

A megoldáson belül négy elem található:

- A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.

- A **modulok mappája** minden modulhoz almappákat tartalmazó.  Az egyes modulok mappájában van egy fájl, **module.json**, amely szabályozza a modulok felépítését és üzembe helyezését.  Ezt a fájlt módosítani kell ahhoz, hogy a modul központi telepítési tárolójának rendszerleíró adatbázisa localhostról távoli beállításjegyzékre változzon. Ezen a ponton csak egy modul van.  De az **Azure IoT Edge: IoT Edge-modul hozzáadása**paranccsal további lehetőségeket is hozzáadhat a parancspalettában.

- Az **.env** fájl felsorolja a környezeti változókat. Ha az Azure Container Registry a beállításjegyzék, akkor egy Azure Container Registry felhasználónevet és jelszót.

  > [!NOTE]
  > A környezeti fájl csak akkor jön létre, ha a modulhoz lemezkép-tárházat biztosít. Ha elfogadta a localhost alapértelmezéseit a helyi teszteléshez és hibakereséshez, akkor nem kell deklarálnia a környezeti változókat.

- A **deployment.template.json** fájl felsorolja az új modult egy **minta SimulatetTemperatureSensor** modullal együtt, amely szimulálja a teszteléshez használható adatokat. A központi telepítési jegyzékek működéséről [a További információ a központi telepítési jegyzékek modulok üzembe helyezéséről és útvonalak létrehozásáról című témakörben olvashat](module-composition.md)bővebben.

A szimulált hőmérsékletmodul működésének megtekintéséhez tekintse meg a [SimulatedTemperatureSensor.csproj forráskódot.](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)

## <a name="add-additional-modules"></a>További modulok hozzáadása

További modulok hozzáadása a megoldáshoz, futtassa az **Azure IoT Edge: Add IoT Edge Module** a parancspaletta. A **Visual** Studio Kódkezelő nézetében `deployment.template.json` a jobb gombbal is kattinthat a modulok mappájára vagy a fájlra, majd válassza **az IoT Edge Module hozzáadása parancsot**.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldáshoz kapcsolódó alapértelmezett modulkód a következő helyen található:

- Azure Függvény (C#): **modulok > * &lt;a modul&gt;nevét* > *&lt;a modul nevét&gt;*.cs**
- C#: **modulok > * &lt;a&gt; modul nevét* > Program.cs**
- Python: **modulok > * &lt;a&gt; modul nevét* > main.py**
- Node.js: **modulok > * &lt;a&gt; modul nevét* > app.js**
- Java: **modulok > * &lt;a&gt; modul nevét,* > src > fő > java > com > edgemodulemodules > App.java**
- C: **modulok > * &lt;a&gt; modul nevét* > main.c**

A modul és a deployment.template.json fájl úgy van beállítva, hogy a megoldást létre tudja hozni, leküldheti a tároló beállításjegyzékébe, és telepítheti egy eszközre, hogy bármilyen kód érintése nélkül elkezdhesse a tesztelést. A modul úgy van kialakítva, hogy egyszerűen egy forrásból (ebben az esetben az adatokat szimuláló SimulatedTemperatureSensor modulból) adatokat vegyen be, és az IoT Hubra vigye.

Ha készen áll a sablon testreszabására a saját kódjával, az [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) segítségével olyan modulokat hozhat létre, amelyek megfelelnek az IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság kulcsfontosságú igényeinek.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Modul hibakeresése tároló nélkül (C#, Node.js, Java)

Ha C#, Node.js vagy Java nyelven fejleszt, a modul hoz egy **ModuleClient** objektumot az alapértelmezett modulkódban, hogy eltudja indítani, futtathatja és továbbíthassa az üzeneteket. Az alapértelmezett bemeneti csatorna **bemeneti1** használatával is végrehajthatja az üzenetek fogadásakor a műveletet.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-szimulátor beállítása IoT Edge-megoldáshoz

A fejlesztői gépen elindíthategy IoT Edge-szimulátort az IoT Edge biztonsági démon telepítése helyett, hogy futtathassa az IoT Edge-megoldást.

1. Az eszközkezelő a bal oldalon, kattintson a jobb gombbal az IoT Edge eszköz azonosítóját, majd válassza **az IoT Edge Simulator beállítása** a szimulátor elindításához az eszköz kapcsolati karakterlánc.
1. Láthatja, hogy az IoT Edge Simulator sikeresen beállított, ha elolvassa az integrált terminál folyamatrészleteit.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>IoT Edge-szimulátor beállítása egymodulos alkalmazáshoz

A szimulátor beállítása és indítása, futtassa az **Azure IoT Edge: Start IoT Edge Hub Simulator egymodulas** a Visual Studio Code parancspaletta. Amikor a rendszer kéri, használja az alapértelmezett modulkódból (vagy a kód ezzel egyenértékű értékét) az alkalmazás bemeneti nevéből származó **input1** értéket. A parancs elindítja az **iotedgehubdev** CLI-t, majd elindítja az IoT Edge-szimulátort és egy tesztelési segédprogrammodul-tárolót. Az integrált terminál kimenetei akkor láthatók az integrált terminálon, ha a szimulátor sikeresen elindult egymodulos módban. Az üzenetküldést segítő `curl` parancs is megjelenik. Erre később még szüksége lesz.

   ![IoT Edge-szimulátor beállítása egymodulos alkalmazáshoz](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   A Visual Studio-kód Docker Explorer nézetének használatával megtekintheti a modul futási állapotát.

   ![Szimulátor modul állapota](media/how-to-develop-csharp-module/simulator-status.png)

   Az **edgeHubDev-tároló** a helyi IoT Edge-szimulátor magja. A fejlesztői gépen az IoT Edge biztonsági démon nélkül futtatható, és környezeti beállításokat biztosít a natív modulalkalmazáshoz vagy modultárolókhoz. A **bemeneti** tároló elérhetővé teszi a REST API-kat, hogy segítsen áthidalni az üzeneteket a modul cél bemeneti csatornájára.

### <a name="debug-module-in-launch-mode"></a>Debug modul indítási módban

1. Készítse elő a környezetet a fejlesztési nyelv követelményeinek megfelelő hibakeresésre, állítson be egy töréspontot a modulban, és válassza ki a használni kívánt hibakeresési konfigurációt:
   - **C #**
     - A Visual Studio Code integrált terminálján módosítsa a könyvtárat a *** &lt;modulnév&gt; *** mappára, majd futtassa a következő parancsot a .NET Core alkalmazás létrehozásához.

       ```cmd
       dotnet build
       ```

     - Nyissa meg `Program.cs` a fájlt, és adjon hozzá egy töréspontot.

     - Keresse meg a Visual Studio kódhiba-keresési nézetét a **Nézet > a Hibakeresés**lehetőséget választva. Válassza ki a hibakeresési ** * &lt;konfigurációt a modul&gt; neve* Local Debug (.NET Core)** a legördülő menüből.

        > [!NOTE]
        > Ha a .NET `TargetFramework` Core nem felel `launch.json`meg a program elérési útvonalának a `launch.json` alkalmazásban, `TargetFramework` manuálisan kell frissítenie a program elérési útját, hogy megfeleljen a .csproj fájlban lévőnek, hogy a Visual Studio Kód sikeresen elindíthassa ezt a programot.

   - **Node.js**
     - A Visual Studio Code integrált terminálján módosítsa a könyvtárat a *** &lt;modulnév&gt; *** mappára, majd futtassa a következő parancsot a Node csomagok telepítéséhez

       ```cmd
       npm install
       ```

     - Nyissa meg `app.js` a fájlt, és adjon hozzá egy töréspontot.

     - Keresse meg a Visual Studio kódhiba-keresési nézetét a **Nézet > a Hibakeresés**lehetőséget választva. Válassza ki a hibakeresési ** * &lt;konfigurációt a modul&gt; neve* Local Debug (Node.js)** a legördülő menüből.
   - **Java**
     - Nyissa meg `App.java` a fájlt, és adjon hozzá egy töréspontot.

     - Keresse meg a Visual Studio kódhiba-keresési nézetét a **Nézet > a Hibakeresés**lehetőséget választva. Válassza ki a hibakeresési ** * &lt;konfigurációt a modul&gt; neve* Local Debug (Java)** a legördülő menüből.

1. Kattintson a **Hibakeresés indítása gombra,** vagy nyomja le az **F5** billentyűt a hibakeresési munkamenet elindításához.

1. A Visual Studio Code integrált terminálján futtassa a következő parancsot, és küldjön **Hello World** üzenetet a modulnak. Ez az ioT edge-szimulátor beállításakor az előző lépésekben látható parancs.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows használata esetén győződjön meg arról, hogy a Visual Studio Code integrált termináljának héja **Git Bash** vagy **WSL Bash**. A `curl` parancs nem futtatható PowerShell ből vagy parancssorból.
   > [!TIP]
   > [A postman](https://www.getpostman.com/) vagy más API-eszközök segítségével is `curl`küldhet üzeneteket a helyett.

1. A Visual Studio kódhiba-hibakeresési nézetében a változók a bal oldali panelen jelennek meg.

1. A hibakeresési munkamenet leállításához kattintson a Stop gombra, vagy nyomja le a **Shift + F5**billentyűkombinációt, majd futtassa az **Azure IoT Edge: Stop IoT Edge Simulator parancsot** a parancspalettán a szimulátor leállításához és a karbantartáshoz.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Hibakeresés csatolási módban az IoT Edge-szimulátorral (C#, Node.js, Java, Azure Functions)

Az alapértelmezett megoldás két modult tartalmaz, az egyik egy szimulált hőmérséklet-érzékelő modul, a másik pedig a csőmodul. A szimulált hőmérséklet-érzékelő üzeneteket küld a csőmodulnak, majd az üzenetek et az IoT Hubhoz küldi. A létrehozott modulmappában több Docker-fájl található a különböző tárolótípusokhoz. A **.debug** kiterjesztéssel végződő fájlok bármelyikével felépítheti a modult tesztelésre.

Jelenleg a hibakeresés csatolási módban csak az alábbiak szerint támogatott:

- C# modulok, beleértve az Azure Functions moduljait is, támogatják a hibakeresést Linux amd64 tárolókban
- A Node.js modulok támogatják a hibakeresést Linux amd64 és arm32v7 tárolókban, valamint a Windows amd64 tárolókban
- A Java modulok támogatják a hibakeresést Linux amd64 és arm32v7 tárolókban

> [!TIP]
> Az IoT Edge-megoldás alapértelmezett platformjának beállításai között válthat, ha a Visual Studio-kód állapotsorában az elemre kattint.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-szimulátor beállítása IoT Edge-megoldáshoz

A fejlesztői gépben elindíthat egy IoT Edge-szimulátort az IoT Edge biztonsági démon telepítése helyett, hogy futtathassa az IoT Edge-megoldást.

1. Az eszközkezelő a bal oldalon, kattintson a jobb gombbal az IoT Edge eszköz azonosítóját, majd válassza **az IoT Edge Simulator beállítása** a szimulátor elindításához az eszköz kapcsolati karakterlánc.

1. Láthatja, hogy az IoT Edge Simulator sikeresen beállított, ha elolvassa az integrált terminál folyamatrészleteit.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Tároló létrehozása és futtatása hibakereséshez és hibakereséshez csatolási módban

1. Nyissa meg a`Program.cs` `app.js`modulfájlt ( , , `App.java`vagy `<your module name>.cs`) , és adjon hozzá egy töréspontot.

1. A Visual Studio Code Explorer nézetben `deployment.debug.template.json` kattintson a jobb gombbal a megoldás fájljára, majd válassza **az IoT Edge-megoldás összeállítása és futtatása parancsot a Szimulátorban.** Az összes modultároló-naplót ugyanabban az ablakban nézheti meg. A Docker nézetben is megnyithatja a tároló állapotát.

   ![Nézd változók](media/how-to-vs-code-develop-module/view-log.png)

1. Nyissa meg a Visual Studio kódhiba-kereső nézetét, és válassza ki a modul hibakeresési konfigurációs fájlját. A hibakeresési beállítás nevének hasonlónak kell lennie ** * &lt;a modul&gt; nevéhez:* Remote Debug**

1. Válassza **a Hibakeresés indítása lehetőséget,** vagy nyomja **le az F5 billentyűt.** Válassza ki azt a folyamatot, amelyhez csatolni szeretné.

1. A Visual Studio kódhiba-kereső nézetében a változók a bal oldali panelen jelennek meg.

1. A hibakeresési munkamenet leállításához először kattintson a Stop gombra, vagy nyomja le a **Shift + F5**billentyűkombinációt, majd válassza az **Azure IoT Edge: Stop IoT Edge Simulator parancsot** a parancspalettáról.

> [!NOTE]
> Az előző példa bemutatja, hogyan debug IoT Edge-modulok tárolókon. A modul tárolóbeállításaihoz `createOptions` hozzáadott egy kitett portot. Miután befejezte a modulok hibakeresését, azt javasoljuk, hogy távolítsa el ezeket a kitett portokat az éles környezetre kész IoT Edge-modulokhoz.
>
> A C#-ban írt modulok, beleértve az Azure Functions-t `Dockerfile.amd64.debug`is, ez a példa a hibakeresési verzióján alapul, amely tartalmazza a .NET Core parancssori hibakeresőt (VSDBG) a tárolórendszerképben az építés során. A C# modulok hibakeresése után azt javasoljuk, hogy közvetlenül használja a Dockerfile vsdbg nélkül éles kész IoT Edge-modulok.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Modul hibakeresése az IoT Edge futásidejűével

Minden modulmappában több Docker-fájl található a különböző tárolótípusokhoz. A **.debug** kiterjesztéssel végződő fájlok bármelyikével felépítheti a modult tesztelésre.

Ha ezzel a módszerrel hibakeresés, a modulok futnak az IoT Edge futtatóöni. Az IoT Edge-eszköz és a Visual Studio-kód lehet ugyanazon a gépen, vagy még inkább, A Visual Studio-kód a fejlesztői gépen, és az IoT Edge futtatótér és a modulok futnak egy másik fizikai gépen. A Visual Studio-kóddal való hibakereséshez a következőket kell tennie:

- Állítsa be az IoT Edge-eszközt, hozza létre az IoT Edge-modul(ok)at a **.debug** Dockerfile,majd üzembe az IoT Edge-eszközön.
- Tegye elérhetővé a modul IP-címét és portját, hogy a hibakereső csatlakoztatható legyen.
- Frissítse `launch.json` a alkalmazást, hogy a Visual Studio-kód csatolható a folyamathoz a távoli számítógépen lévő tárolóban. Ez a fájl `.vscode` a munkaterület mappájában található, és minden alkalommal frissül, amikor olyan új modult ad hozzá, amely támogatja a hibakeresést.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>A modul létrehozása és üzembe helyezése az IoT Edge-eszközre

1. A Visual Studio-kódban nyissa meg a `deployment.debug.template.json` fájlt, amely a `createOptions` modulképek hibakeresési verzióját tartalmazza a megfelelő értékekkel.

1. Ha a modult pythonban fejleszti, a folytatás előtt kövesse az alábbi lépéseket:
   - Nyissa meg `main.py` a fájlt, és adja hozzá ezt a kódot az importálási szakasz után:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adja hozzá a következő egyetlen kódsort a hibakereséshez kívánt visszahíváshoz:

      ```python
      ptvsd.break_into_debugger()
      ```

     Ha például hibakeresést szeretne `receive_message_listener` a funkcióval, akkor az alábbi módon szúrja be ezt a kódsort:

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

1. A Visual Studio Kód parancspalettán:
   1. Futtassa az **Azure IoT Edge parancsot: IoT Edge-megoldás létrehozása és leküldése.**

   1. Válassza `deployment.debug.template.json` ki a megoldáshoz való fájlt.

1. A Visual Studio Code Explorer nézet **Azure IoT Hub-eszközök** szakaszában:
   1. Kattintson a jobb gombbal egy IoT Edge-eszközazonosítóra, majd válassza **a Központi telepítés létrehozása egyetlen eszközhöz parancsot.**

      > [!TIP]
      > Annak ellenőrzéséhez, hogy a kiválasztott eszköz egy IoT Edge-eszköz, jelölje ki, hogy bővítse a modulok listáját, és ellenőrizze a **$edgeHub** és **$edgeAgent**jelenlétét. Minden IoT Edge-eszköz tartalmazza ezt a két modult.

   1. Keresse meg a megoldás **konfigurációs** mappáját, jelölje ki a `deployment.debug.amd64.json` fájlt, majd válassza az Edge Deployment Manifest **kiválasztása**lehetőséget.

Látni fogja, hogy a központi telepítés sikeresen létrehozott egy központi telepítési azonosítóval az integrált terminálon.

A tároló állapotát a `docker ps` terminálon lévő parancs futtatásával ellenőrizheti. Ha a Visual Studio-kód és az IoT Edge futtatótime ugyanazon a gépen fut, a Visual Studio Code Docker nézetben is ellenőrizheti az állapotát.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Tegye elérhetővé a modul IP-címének és portjának a hibakereső számára

Ezt a szakaszt kihagyhatja, ha a modulok ugyanazon a gépen futnak, mint a Visual Studio-kód, mivel localhost használatával csatlakozik a `createOptions` tárolóhoz, `launch.json` és már rendelkezik a megfelelő portbeállításokkal a **.debug** Dockerfile, a modul tárolóbeállításai és a fájlban. Ha a modulok és a Visual Studio-kód külön gépeken futnak, kövesse a fejlesztési nyelvhez szükséges lépéseket.

- **C#, beleértve az Azure-függvényeket is**

  [Konfigurálja az SSH-csatornát a fejlesztői gépen és az IoT Edge-eszközön,](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) majd szerkesztse `launch.json` a csatoláshoz a fájlt.

- **Node.js**

  - Győződjön meg arról, hogy a hibakeresésre váró készülék en a modul fut, és készen áll a hibakeresők csatolására, és hogy a 9229-es port külsőleg is elérhető. Ezt a hibakereső `http://<target-machine-IP>:9229/json` gépen való megnyitással ellenőrizheti. Ennek az URL-címnek meg kell jelenlennie a hibakereséshez szükséges Node.js modul adataiközött.
  
  - A fejlesztőgépen nyissa meg a Visual `launch.json` Studio Code alkalmazást, majd úgy szerkesztheti, hogy a ** * &lt;&gt; modulnév* Remote Debug (Node.js)** (vagy ** * &lt;a&gt; modulneve* Remote Debug (Node.js a Windows Tárolóban)** címértéke, ha a modul Windows-tárolóként fut) a hibakeresés alatt álló gép IP-címe legyen.

- **Java**

  - Hozzon létre egy SSH-alagutat a `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`géphez, hogy a futtatásával hibakeresést lehessen készíteni.
  
  - A fejlesztői gépen nyissa meg a Visual Studio-kódot, és szerkesztheti `launch.json` a ** * &lt;modulnevét,&gt; * a Távoli hibakeresési (Java)** profilt, hogy csatolhassa a célgéphez. Ha többet szeretne `launch.json` tudni a Java Visual Studio-kóddal történő módosításáról és hibakereséséről, olvassa el [a hibakereső konfigurálásáról](https://code.visualstudio.com/docs/java/java-debugging#_configuration)szóló szakaszt.

- **Python**

  - Győződjön meg arról, hogy a hibakeresésre kerülő gépen az 5678-as port nyitva van és elérhető.

  - A korábban beszúrt `ptvsd.enable_attach(('0.0.0.0', 5678))` kódban `main.py`módosítsa a **0.0.0.0-t** a hibakereséshez a gép IP-címére. Az IoT Edge-modul újra megépüljön, leküldéses és üzembe helyezése.

  - A fejlesztői gépen nyissa meg a `launch.json` Visual Studio `host` Code alkalmazást, majd úgy szerkesztheti, hogy a ** * &lt;modulnév&gt; * Távoli hibakeresési (Python)** profil értéke a célgép IP-címét használja a helyett. `localhost`

### <a name="debug-your-module"></a>A modul hibakeresése

1. A Visual Studio kódhiba-hibakeresési nézetében jelölje ki a modul hibakeresési konfigurációs fájlját. A hibakeresési beállítás nevének hasonlónak kell lennie ** * &lt;a modul&gt; nevéhez:* Remote Debug**

1. Nyissa meg a fejlesztői nyelv modulfájlját, és adjon hozzá egy töréspontot:

   - **Azure függvény (C#)**: Adja hozzá `<your module name>.cs`a töréspontot a fájlhoz.
   - **C#**: Adja hozzá a `Program.cs`töréspontot a fájlhoz.
   - **Node.js**: Adja hozzá a `app.js`töréspontot a fájlhoz.
   - **Java**: Adja hozzá a `App.java`töréspontot a fájlhoz.
   - **Python:** Adja hozzá a `main.py`töréspontot a fájlhoz a `ptvsd.break_into_debugger()` visszahívási metódusban, ahol hozzáadta a vonalat.
   - **C**: Adja hozzá a `main.c`töréspontot a fájlhoz .

1. Válassza **a Hibakeresés indítása** vagy **az F5**lehetőséget. Válassza ki azt a folyamatot, amelyhez csatolni szeretné.

1. A Visual Studio kódhiba-hibakeresési nézetében a változók a bal oldali panelen jelennek meg.

> [!NOTE]
> Az előző példa bemutatja, hogyan debug IoT Edge-modulok tárolókon. A modul tárolóbeállításaihoz `createOptions` hozzáadott egy kitett portot. Miután befejezte a modulok hibakeresését, azt javasoljuk, hogy távolítsa el ezeket a kitett portokat az éles környezetre kész IoT Edge-modulokhoz.

## <a name="build-and-debug-a-module-remotely"></a>Modul létrehozása és hibakeresése távolról

Az SSH-kapcsolatokat támogató Docker- és Moby-motorok legutóbbi módosításaival, valamint az Azure IoT Tools új beállításával, amely lehetővé teszi a környezeti beállítások injektálását a Visual Studio Code parancspalettába és az Azure IoT Edge-terminálokba, most már létrehozhat és debugolhat modulokat a távoli eszközökön.

Tekintse meg ezt az [IoT Developer blog bejegyzést](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) további információkért és részletes utasításokért.

## <a name="next-steps"></a>További lépések

Miután elkészítette a modult, ismerje meg, hogyan [telepítheti az Azure IoT Edge-modulokat a Visual Studio-kódból.](how-to-deploy-modules-vscode.md)

Az IoT Edge-eszközökhöz tartozó modulok fejlesztéséhez [ismerje meg és használja az Azure IoT Hub SDK-kat.](../iot-hub/iot-hub-devguide-sdks.md)
