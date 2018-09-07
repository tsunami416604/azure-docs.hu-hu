---
title: C# modul hibakeresése az Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio Code segítségével hozhatja létre, hozhat létre, és a egy C# modul hibakeresése az Azure IoT Edge-hez
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a66a17a0f8daed5f61753dd7c20ed5d9987c0b15
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053900"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Fejlesztés és az Azure IoT Edge-hez a C# modul hibakeresése Visual Studio Code használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ez a cikk bemutatja, hogyan fejleszthet és C# modul hibakeresése Visual Studio Code-ot (a VS Code) adatokként a fő eszköz.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a Windows, macOS vagy Linux rendszerű, a fejlesztői gépén futó virtuális gép használja. Az IoT Edge-eszközt egy másik fizikai eszköz lehet.

> [!NOTE]
> Ez hibakeresési bemutatja két jellemző módon való hibakeresés a C# modul a VS Code-ban. Egyik módja egy folyamatot egy modul tárolóban, csatlakoztassa, míg a másik pedig a lanuch modul hibakeresési módban. Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Mivel ez a cikk a fő fejlesztőeszközt, Visual Studio Code-ot használ, a VS Code telepítése. Ezután adja hozzá a szükséges bővítmények:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, hozhat létre a projektmappában, Docker, a modul rendszerképének és a egy tároló-beállításjegyzéket, amely tárolja a modul rendszerképének létrehozása .NET szüksége:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [A docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen. 
* [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható. 

Beállítani a helyi fejlesztési környezet hibakeresése, futtassa, és az IoT Edge-megoldás teszteléséhez kell, hogy [Azure IoT EdgeHub fejlesztőeszközt](https://pypi.org/project/iotedgehubdev/). Telepítés [Python (2.7-es és 3.6) és a Pip](https://www.python.org/). Ezután telepítse **iotedgehubdev** futtassa az alábbi parancsot a terminálon.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Ha tesztelni szeretné a modul egy eszközön, aktív IoT hub létrehozott legalább egy IoT Edge Eszközazonosítót használó kell. IoT Edge-démon futtatja a fejlesztői gépen, ha szüksége lehet, hogy leállította EdgeHub és EdgeAgent helyez át a következő lépéssel. 

## <a name="create-a-new-solution-with-c-module"></a>Új megoldás létrehozása a C# modul

Hozzon létre egy IoT Edge-modul a Visual Studio Code és az Azure IoT Edge-bővítmény használata a .NET Core 2.0 alapján ezeket a lépéseket. Először hozzon létre egy megoldást, és ezután az első modul létrehozása ebben a megoldásban. Egyes megoldások tartalmazhatnak egynél több modul. 

1. Válassza ki a Visual Studio Code- **nézet** > **integrált terminálon**.
3. Válassza ki **nézet** > **paletta parancs**. 
4. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: IoT Edge új megoldás**.

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

5. Keresse meg a mappát, ahol szeretné létrehozni az új megoldásba. Válasszon **mappa kiválasztása**. 
6. Adja meg a megoldás nevét. 
7. Válassza ki **C# modul** a megoldás első modul sablonként.
8. Adja meg a modul nevét. Válassza ki, amely a tárolóregisztrációs adatbázis egyedi nevét. 
9. Adja meg a lemezképtárban a modul nevét. A VS Code autopopulates a modul neve a **localhost:5000**. Cserélje le a saját beállításjegyzék-információkat. Ha használja a helyi Docker-beállításjegyzék tesztelési, majd **localhost** nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**. Csak a sztring localhost részét cserélje le, ne törölje a modul nevét.

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-csharp-module/repository.png)

A VS Code időt vesz igénybe, a megadott információt, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban.

   ![IoT Edge-megoldás megtekintése](./media/how-to-develop-csharp-module/view-solution.png)

Nincsenek a megoldáson belül a négy elemek: 
* A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.
* A **modulok** a mappában megtalálja az egyes modulok almappát. Ezen a ponton csak eggyel rendelkezik. A paranccsal a parancskatalógus több adhat hozzá, de **Azure IoT Edge: IoT Edge-modul hozzáadása**. 
* Egy **.env** sorolja a környezeti változók. Ha az Azure Container Registry a beállításjegyzékbe, lesz egy Azure Container Registry-felhasználónév és jelszó szerepel. 

   >[!NOTE]
   >A környezet fájl csak akkor jön létre, ha a modul adja meg egy lemezképtárban. Ha korábban elfogadta a localhost alapértelmezett tesztelése és hibakeresése helyileg, majd, nem kell környezeti változók deklarálása. 

* A **deployment.template.json** sorolja az új modul és a egy minta **tempSensor** modul, amely szimulálja az adatokat, teszteléshez használható. Hogyan az üzembe helyezés jegyzékfájlok munkahelyi kapcsolatos további információkért lásd: [megtudhatja, hogyan telepítési jegyzékek használatával hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](module-composition.md). 

## <a name="develop-your-module"></a>A modul fejlesztése

Az alapértelmezett C# modul kód, amely a megoldás a következő helyen található **modulok** > ** [Ez a modul neve] ** > **Program.cs**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha készen áll a C#-sablont a saját kód testreszabása, használja a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

A C# támogatása a VS Code-ban a platformfüggetlen .NET Core fejlesztési van optimalizálva. Tudjon meg többet [használata a VS Code-ban a C#](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Indítsa el, és a tároló nélkül a modul-kód hibaelhárítása
Az IoT Edge C# modul az a .net Core-alkalmazás. És az Azure IoT C# eszközoldali SDK függ. Az alapértelmezett modul kódban inicializálása egy **ModuleClient** környezeti beállítások, a bemeneti név, ami azt jelenti, hogy az IoT Edge C# modul igényel az indításához, és futtassa a környezeti beállítások, és is szeretne küldeni, vagy az üzenetek a a bemeneti csatornák. Az alapértelmezett C# modul csak egy bemeneti csatorna tartalmaz, és a név **input1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>IoT Edge-szimulátor egy modul alkalmazás beállítása

1. A VS Code parancskatalógus, írja be, majd a szimulátor indításához, és válassza ki **Azure IoT Edge: Start IoT Edge hubot szimulátor egyetlen modul**. Emellett meg kell adnia egy modul alkalmazásához, írja be a bemeneti nevek **input1** nyomja le az Enter billentyűt. A parancs elindítja a **iotedgehubdev** CLI és az IoT Edge-szimulátor és a egy tesztelési segédprogram modul tárolót. A kimenetek alább az integrált terminálon láthatja, ha a szimulátor lett egy modul módban sikeresen elindult. Emellett megtekintheti a `curl` parancs segítségével keresztül küldött. Erre később még szüksége lesz.

   ![IoT Edge-szimulátor egy modul alkalmazás beállítása](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Helyezze át a Docker Explorert, és tekintse meg a modul futási állapotát.

   ![Simulátor modul állapota](media/how-to-develop-csharp-module/simulator-status.png)

   A **edgeHubDev** tároló-e a fő elemei a helyi IoT Edge-szimulátort. Azt a fejlesztési számítógépén az IoT Edge biztonsági démon nélkül futtathatja és környezeti beállítások megadása a natív modul vagy a modul tárolók. A **bemeneti** tároló elérhetővé tett restAPIs híd üzeneteket, amelyekre a modul bemeneti csatorna segítségével.

2. A VS Code parancskatalógus, írja be, és válassza ki **Azure IoT Edge: a modul hitelesítő adatok beállítása a felhasználói beállítások** beállítása a modul be környezeti beállítások `azure-iot-edge.EdgeHubConnectionString` és `azure-iot-edge.EdgeModuleCACertificateFile` a felhasználói beállítások. Annak a környezeti beállítások a hivatkozott **.vscode** > **launch.json** és [VS Code felhasználói beállítások](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>A modul alkalmazás fejlesztése és hibakeresése indítási módban

1. Az integrált terminálon módosítsa a könyvtárat a **CSharpModule** mappában futtassa a következő parancsot hozhat létre a .net Core-alkalmazás.

    ```cmd
    dotnet build
    ```

2. Nyissa meg a `program.cs` címet. Ebben a fájlban adja hozzá egy töréspontot.

3. Nyissa meg a VS Code hibakeresési nézetet. Válassza ki a hibakeresési konfigurációt **ModuleName helyi hibakeresés (.NET Core)**. 

4. Kattintson a **Start Debugging** vagy nyomja le az **F5**. A hibakeresési munkamenet indul.

5. A VS Code integrált termináljában, futtassa a következő parancsot, küldhet egy **Hello World** üzenet a modulnak. Ez a parancs az előző lépésekben bemutatta az IoT Edge-szimulátor sikeresen telepítő.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows használja, ha van gondoskodik róla, hogy a rendszerhéj, a VS Code integrált termináljában **a Git Bash** vagy **WSL Bash**. Nem futtathat `curl` parancsot a PowerShellben vagy a parancssor használatával. 
   
   > [!TIP]
   > Is [PostMan](https://www.getpostman.com/) vagy más API-eszközök helyett keresztül üzenetek küldéséhez `curl`.

6. A VS Code hibakereső nézet láthatja a változókat a bal oldali panelen. 

    ![Változók Watch](media/how-to-develop-csharp-module/single-module-variables.png)

7. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra vagy nyomja meg **Shift + F5**. És a VS Code parancskatalógus, írja be, és válassza ki **Azure IoT Edge: IoT Edge szimulátor leállítása** állítsa le, és tisztítsa meg a szimulátor.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>A modul tároló hibakereséshez és a hibakeresési build az attach mód

Az alapértelmezés szerinti megoldás két modult tartalmaz, egy egy szimulált hőmérsékleti érzékelő modul a másik pedig a C# cső modul. A szimulált hőmérséklet-érzékelő tartja üzenetek küldése az C# modul függőleges vonal, és az üzenetek vannak eredményez az IoT hubnak. A modul mappában létrehozott nincsenek különböző tároló esetében több Docker-fájlok. Ezeket a fájlokat, amelyek a bővítmény végződhet bármelyike **.debug** hozhat létre a teszteléshez modul. Jelenleg a C# modul támogatási hibakeresés csak a Linux-amd64 tárolók csatolása mód. 

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-szimulátort az IoT Edge-megoldás beállítása

A fejlesztői gépen elindíthatja az IoT Edge-szimulátor helyett az IoT Edge-megoldás futtatásához az IoT Edge biztonsági démon telepítésével. 

1. A device Explorerben bal oldalán kattintson a jobb gombbal a IoT Edge-eszköz azonosítója, válassza a **beállítása IoT Edge-szimulátor** a szimulátor indításához az eszköz kapcsolati karakterlánccal.

2. Láthatja, hogy az IoT Edge-szimulátor sikeresen megtörtént a telepítő az integrált terminálon.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Létrehozásához és futtatásához hibakereséshez és a hibakeresési tároló a csatolása mód

1. A VS Code-ban keresse meg a `deployment.template.json` fájlt. Adja hozzá a C# modul kép URL-címe frissítés **.debug** vége.

   ![Adjon hozzá x .debug, a rendszerkép neve](./media/how-to-develop-csharp-module/image-debug.png)

2. Nyissa meg a `program.cs` címet. Ebben a fájlban adja hozzá egy töréspontot.
3. A VS Code Fájlkezelőben válassza ki a `deployment.template.json` fájlt a megoldás a helyi menüben kattintson a **készítése és futtatása az IoT Edge-szimulátorban történő megoldás**. Megnézheti a modul összes tároló-naplók ugyanabban az ablakban. A Docker Explorert, és tekintse meg a tároló állapota is elérheti.

   ![Változók Watch](media/how-to-develop-csharp-module/view-log.png)

5. Nyissa meg a VS Code hibakeresési nézetet. Válassza ki a hibakeresési konfigurációs fájlt a modul. A hibakeresési beállítás neve legyen hasonló **ModuleName távoli hibakeresés (.NET Core)**

   ![Válassza ki a konfiguráció](media/how-to-develop-csharp-module/debug-config.png)

6. Válassza ki **Start Debugging** , vagy válasszon **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.

7. A VS Code hibakereső nézet láthatja a változókat a bal oldali panelen.

8. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra vagy nyomja meg **Shift + F5**. A VS Code parancskatalógus, írja be és válassza a **Azure IoT Edge: IoT Edge szimulátor leállítása**.

> [!NOTE]
> Ez a példa bemutatja, hogyan hibakeresése a .NET Core IoT Edge-modulok a tárolók. A hibakeresési verzió az alapján `Dockerfile.debug`, amely tartalmazza a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Miután a C# modul hibakeresése, azt javasoljuk, hogy Ön közvetlenül használhatja, vagy testre szabhatja `Dockerfile` VSDBG az éles használatra kész IoT Edge-modulok nélkül.

## <a name="next-steps"></a>További lépések

A modul létrehozása, után megtudhatja, hogyan [üzembe helyezése a Visual Studio Code-ból az Azure IoT Edge-modulok](how-to-deploy-modules-vscode.md).

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
