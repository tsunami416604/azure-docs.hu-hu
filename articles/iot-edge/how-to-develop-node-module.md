---
title: Hibakeresés a Node.js modulok az Azure IoT Edge |} A Microsoft Docs
description: Fejlesztés és hibakeresés a Node.js modulok az Azure IoT Edge-hez a Visual Studio Code használatával
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 22049ae0903d2735e4c1974c1071eb7582be9823
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049983"
---
# <a name="use-visual-studio-code-to-develop-and-debug-nodejs-modules-for-azure-iot-edge"></a>Fejlesztés és hibakeresés a Node.js modulok az Azure IoT Edge-hez a Visual Studio Code használatával

Elküldheti az üzleti logikát a peremhálózaton csempefolyamot modulokat az Azure IoT Edge megfelelően működjenek. Ez a cikk részletesen ismerteti a Visual Studio Code (a VS Code), a fő fejlesztőeszközt, a Node.js modulok fejlesztését.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a Windows, macOS vagy Linux rendszerű, a fejlesztői gépén futó virtuális gép használja. Az IoT Edge-eszközt egy másik fizikai eszköz lehet.

> [!NOTE]
> Ez hibakeresési hibakeresése a VS Code-ban a Node.js modult tipikus kétféleképpen bemutatja. Egyik módja egy folyamatot egy modul tárolóban, csatlakoztassa, míg a másik pedig a lanuch modul hibakeresési módban. Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Mivel ez a cikk a fő fejlesztőeszközt, Visual Studio Code-ot használ, a VS Code telepítése, és vegye fel a szükséges bővítmények:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, Node.js, amely tartalmazza az npm segítségével való hozhat létre a projektmappában, a modul rendszerképének és a egy tároló-beállításjegyzéket, amely tárolja a modul rendszerképének létrehozása Docker szüksége:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Használhat egy helyi Docker-beállításjegyzék készíthet prototípusokat és tesztelési célra, egy felhőbeli beállításjegyzék helyett. 

Beállítani a helyi fejlesztési környezet hibakeresése, futtassa, és az IoT Edge-megoldás teszteléséhez kell, hogy [Azure IoT EdgeHub fejlesztőeszközt](https://pypi.org/project/iotedgehubdev/). Telepítés [Python (2.7-es és 3.6) és a Pip](https://www.python.org/). Ezután telepítse **iotedgehubdev** futtassa az alábbi parancsot a terminálon.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Ha tesztelni szeretné a modul egy eszközön, aktív IoT hub létrehozott legalább egy IoT Edge Eszközazonosítót használó kell. IoT Edge-démon futtatja a fejlesztői gépen, ha szüksége lehet, hogy leállította EdgeHub és EdgeAgent helyez át a következő lépéssel. 


## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozásához

A következő lépések bemutatják, hogyan hozhat létre egy Node.js-alapú, IoT Edge-modul a Visual Studio Code és az Azure IoT Edge bővítmény használatával. Indítsa el a megoldás létrehozásával és majd létrehozni ebben a megoldásban az első modult. Egyes megoldások a több modul is tartalmazhat. 

1. Válassza ki a Visual Studio Code- **nézet** > **integrált terminálon**.
2. Az integrált terminálon írja be a következő parancsot az Azure IoT Edge-modul sablon legújabb verziójának telepítéséhez (vagy frissítéséhez) node.js-hez készült:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. Válassza a Visual Studio Code-ban a **Nézet** > **Parancskatalógus** lehetőséget. 
4. A parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: IoT Edge új megoldás**.

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

5. Keresse meg a mappát, ahol szeretné az új megoldás létrehozása, és kattintson a **mappa kiválasztása**. 
6. Adja meg a megoldás nevét. 
7. Válasszon **Node.js modult** a megoldás első modul sablonként.
8. Adja meg a modul nevét. Válassza ki, amely a tárolóregisztrációs adatbázis egyedi nevét. 
9. A modul adja meg a lemezképtárból. A VS Code autopopulates a modul neve, így csak kell cserélni **localhost:5000** a saját beállításjegyzék-információkat. Ha tesztelése helyi Docker-tárolójegyzék használja, localhost nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**. Csak a sztring localhost részét cserélje le, ne törölje a modul nevét.

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-node-module/repository.png)

A VS Code vesz igénybe az adatokat a megadott, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban.

A megoldáson belül, akkor három elemet: 
* A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.
* A **modulok** mappát az egyes modulok almappát tartalmaz. Jelenleg csak eggyel rendelkezik, de a paranccsal a parancskatalógus több felvehet **Azure IoT Edge: IoT Edge-modul hozzáadása**. 
* A **.env** sorolja a környezeti változók. Ha az Azure Container Registry a beállításjegyzékbe, lesz egy Azure Container Registry-felhasználónév és jelszó szerepel.

   >[!NOTE]
   >A környezet fájl csak akkor jön létre, ha a modul adja meg egy lemezképtárban. Ha korábban elfogadta a localhost alapértelmezett tesztelése és hibakeresése helyileg, majd, nem kell környezeti változók deklarálása. 

* A **deployment.template.json** sorolja az új modul és a egy minta **tempSensor** modul, amely szimulálja az adatokat, amelyek a teszteléshez használható. Hogyan az üzembe helyezés jegyzékfájlok munkahelyi kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

## <a name="develop-your-module"></a>A modul fejlesztése

Az alapértelmezett Node.js-kód, amely a megoldás a következő helyen található **modulok** > [a modul neve] > **app.js**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha már készen áll a saját kód a Node.js-sablon testre szabása, a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

A Visual Studio Code Node.js támogatással rendelkezik. Tudjon meg többet [használata a VS Code-ban Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Indítsa el, és a tároló nélkül a modul-kód hibaelhárítása
Az IoT Edge Node.js modult az Azure IoT készült Node.js eszközoldali SDK függ. Az alapértelmezett modul kódban inicializálása egy **ModuleClient** környezeti beállítások, a bemeneti név, ami azt jelenti, hogy az IoT Edge Node.js modult igényel elindításához, és futtassa a környezet beállításokat, és is kell elküldeni, vagy üzenetek a bemeneti csatornához. Az alapértelmezett Node.js modult csak egy bemeneti csatorna tartalmaz, és a név **input1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>IoT Edge-szimulátor egy modul alkalmazás beállítása

1. A VS Code parancskatalógus, írja be, majd a szimulátor indításához, és válassza ki **Azure IoT Edge: Start IoT Edge hubot szimulátor egyetlen modul**. Emellett meg kell adnia egy modul alkalmazásához, írja be a bemeneti nevek **input1** nyomja le az Enter billentyűt. A parancs elindítja a **iotedgehubdev** CLI és az IoT Edge-szimulátor és a egy tesztelési segédprogram modul tárolót. A kimenetek alább az integrált terminálon láthatja, ha a szimulátor lett egy modul módban sikeresen elindult. Emellett megtekintheti a `curl` parancs segítségével keresztül küldött. Erre később még szüksége lesz.

   ![IoT Edge-szimulátor egy modul alkalmazás beállítása](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Helyezze át a Docker Explorert, és tekintse meg a modul futási állapotát.

   ![Simulátor modul állapota](media/how-to-develop-csharp-module/simulator-status.png)

   A **edgeHubDev** tároló-e a fő elemei a helyi IoT Edge-szimulátort. Azt a fejlesztési számítógépén az IoT Edge biztonsági démon nélkül futtathatja és környezeti beállítások megadása a natív modul vagy a modul tárolók. A **bemeneti** tároló elérhetővé tett restAPIs híd üzeneteket, amelyekre a modul bemeneti csatorna segítségével.

2. A VS Code parancskatalógus, írja be, és válassza ki **Azure IoT Edge: a modul hitelesítő adatok beállítása a felhasználói beállítások** beállítása a modul be környezeti beállítások `azure-iot-edge.EdgeHubConnectionString` és `azure-iot-edge.EdgeModuleCACertificateFile` a felhasználói beállítások. Annak a környezeti beállítások a hivatkozott **.vscode** > **launch.json** és [VS Code felhasználói beállítások](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-nodejs-module-in-launch-mode"></a>Hibakeresés a Node.js modult indítási módban

1. Az integrált terminálon módosítsa a könyvtárat a **NodeModule** mappában futtassa a következő parancsot a Node-csomagok telepítéséhez

   ```cmd
   npm install
   ```

2. Nyissa meg a `app.js` címet. Ebben a fájlban adja hozzá egy töréspontot.

3. Nyissa meg a VS Code hibakeresési nézetet. Válassza ki a hibakeresési konfigurációt **ModuleName helyi hibakeresés (Node.js)**. 

4. Kattintson a **Start Debugging** vagy nyomja le az **F5**. A hibakeresési munkamenet indul.

5. A VS Code integrált termináljában, futtassa a következő parancsot, küldhet egy **Hello World** üzenet a modulnak. Ez a parancs az előző lépésekben bemutatta az IoT Edge-szimulátor sikeresen telepítő. Szüksége lehet létrehozni, vagy egy másik integrált terminálon váltson, ha jelenlegivel le van tiltva.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows használja, ha van gondoskodik róla, hogy a rendszerhéj, a VS Code integrált termináljában **a Git Bash** vagy **WSL Bash**. Nem futtathat `curl` parancsot a PowerShellben vagy a parancssor használatával. 
   
   > [!TIP]
   > Is [PostMan](https://www.getpostman.com/) vagy más API-eszközök helyett keresztül üzenetek küldéséhez `curl`.

6. A VS Code hibakereső nézet láthatja a változókat a bal oldali panelen. 

7. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra vagy nyomja meg **Shift + F5**. És a VS Code parancskatalógus, írja be, és válassza ki **Azure IoT Edge: IoT Edge szimulátor leállítása** leállítása és tisztítsa meg a szimulátor.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>A modul tároló hibakereséshez és a hibakeresési build az attach mód

Az alapértelmezés szerinti megoldás két modult tartalmaz, egy szimulált hőmérsékleti érzékelő modul a másik pedig a Node.js-cső modul. A szimulált hőmérséklet-érzékelő tartja üzenetek küldése az Node.js cső modult, és az üzenetek vannak eredményez az IoT hubhoz. A modul mappában létrehozott nincsenek különböző tároló esetében több Docker-fájlok. Ezeket a fájlokat, amelyek a bővítmény végződhet bármelyike **.debug** hozhat létre a teszteléshez modul. Jelenleg a Node.js modulok csak támogatja a linux-amd64, a windows-amd64 és a linux-arm32v7 tárolók hibakeresését.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge-szimulátort az IoT Edge-megoldás beállítása

A fejlesztői gépen elindíthatja az IoT Edge-szimulátor helyett az IoT Edge-megoldás futtatásához az IoT Edge biztonsági démon telepítésével. 

1. A device Explorerben bal oldalán kattintson a jobb gombbal a IoT Edge-eszköz azonosítója, válassza a **beállítása IoT Edge-szimulátor** a szimulátor indításához az eszköz kapcsolati karakterlánccal.

2. Láthatja, hogy az IoT Edge-szimulátor sikeresen megtörtént a telepítő az integrált terminálon.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Létrehozásához és futtatásához hibakereséshez és a hibakeresési tároló a csatolása mód

1. A VS Code-ban keresse meg a `deployment.template.json` fájlt. A modul kép URL-címe frissítés hozzáadásával **.debug** vége.

2. Cserélje le a Node.js-modul createOptions a **deployment.template.json** az alábbi tartalmat, és mentse a fájlt: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

5. Nyissa meg a VS Code hibakeresési nézetet. Válassza ki a hibakeresési konfigurációs fájlt a modul. A hibakeresési beállítás nevét kell kinéznie **ModuleName távoli hibakeresés (Node.js)** vagy **ModuleName távoli hibakeresés (Node.js Windows-tárolóban)**, amely a fejlesztői gépen a tároló típusa attól függ.

6. Válassza ki **Start Debugging** , vagy válasszon **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.

7. A VS Code hibakereső nézet láthatja a változókat a bal oldali panelen.

8. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra vagy nyomja meg **Shift + F5**. A VS Code parancskatalógus, írja be és válassza a **Azure IoT Edge: IoT Edge szimulátor leállítása**.

> [!NOTE]
> Az előző példa bemutatja, hogyan debug a Node.js IoT Edge-modulok a tárolókat. Ez a modul tároló createOptions elérhetővé tett port egészül ki. Miután befejezte a hibakeresés a Node.js modulok, javasoljuk, éles használatra kész IoT Edge-modulok számára elérhetővé tett portokon távolítsa el.

## <a name="next-steps"></a>További lépések

Ha a beépített modul, megtudhatja, hogyan [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md)

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
