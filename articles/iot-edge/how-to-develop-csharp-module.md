---
title: C# modulok hibakeresése az Azure IoT peremhálózati |} Microsoft Docs
description: Visual Studio Code segítségével fejleszthet, elkészítéséhez és egy C# modul debug Azure IoT szegély
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c237b1eb9874357afa922f809109cf8f2181561e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048186"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Visual Studio Code fejlesztésére és C# modulok hibakeresése az Azure IoT Edge használata

Az üzleti logikát a peremhálózaton által, hogy a modulok Azure IoT szegély működéséhez küldhet. Ez a cikk részletes útmutatást a fő fejlesztőeszköz C# modulok fejlesztéséhez a Visual Studio (kód VS) használata.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használja. Az IoT-peremhálózati eszköz egy másik fizikai eszköz lehet, vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

> [!NOTE]
> Ez az oktatóanyag hibakeresési modul tároló folyamat csatolja, és azt a Visual STUDIO Code hibakereséshez ismerteti. Linux-amd64 tárolókban lévő funkciók C# csak megoldhassuk. Ha nem ismeri a Visual Studio Code hibakeresési lehetőségeket, olvassa el [hibakeresés](https://code.visualstudio.com/Docs/editor/debugging). 

Mivel ez a cikk a Visual Studio Code a fő fejlesztési eszközként használ, telepítse a Visual STUDIO Code, és adja meg a szükséges kiterjesztéseket:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-bővítményt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, amely hoz létre a projektmappában, Docker hozhat létre a modul lemezképet, és ahhoz, hogy a modul lemezképet tároló beállításjegyzékbeli .NET kell:
* [A .NET core SDK 2.1-es](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) a fejlesztési számítógépén. 
* [Azure-tárolót beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Egy helyi Docker beállításjegyzék prototípus és tesztelési célokra, a felhő beállításjegyzék helyett használhat. 

A modul az eszközön teszteléséhez szükséges legalább egy IoT peremhálózati eszköz az aktív IoT-központ. Ha szeretné használni a számítógép IoT peremhálózati eszköz, azt is megteheti az oktatóanyag a következő lépéseket követve [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) 

## <a name="create-a-new-solution-template"></a>Hozzon létre egy új megoldássablon

A következő lépések bemutatják a .NET Core 2.0 alapján egy IoT Edge-modul létrehozása Visual Studio Code és az Azure IoT peremhálózati bővítmény használatával. Indítsa el a megoldás létrehozásával, és majd létrehozásakor az első modult az adott megoldáshoz. Egyes megoldások tartalmazhatnak több modul. 

1. A Visual Studio Code, válassza ki **nézet** > **integrált Terminálszolgáltatások**.
3. Válassza ki **nézet** > **paletta parancs**. 
4. A parancs paletta, írja be, és futtassa a parancsot **Azure IoT peremhálózati: új IoT peremhálózati megoldás**.

   ![Új IoT peremhálózati megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

5. Keresse meg a mappát, ahová az új megoldás létrehozása, és kattintson a **jelölje ki a mappát**. 
6. Adjon meg egy nevet a megoldás. 
7. Válasszon **C# modul** , az első moduljában a megoldás sablonját.
8. Adja meg a modul nevét. Válasszon, amely a tároló beállításjegyzék belül egyedi nevet. 
9. A lemezképtár a modul adja meg. Visual STUDIO Code autopopulates a modul nevével, így cserélni kell **localhost:5000** a saját beállításjegyzék-információkat. Ha egy helyi Docker rendszerleíró tesztelési, majd localhost rendben. Ha Azure-tároló rendszerleíró, majd használja a beállításjegyzék-beállítások a bejelentkezési kiszolgáló. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**.

Visual STUDIO Code átveszi az adatokat a megadott, létrehoz egy IoT peremhálózati megoldás, majd betölti azt egy új ablakban.

   ![Az IoT-peremhálózati megoldás megtekintése](./media/how-to-develop-csharp-module/view-solution.png)

A megoldáson három elemek közül választhat: 
* A **.vscode** mappa hibakeresési konfigurációkat tartalmazza.
* A **modulok** mappa almappákat minden modul tartalmazza. Jobb gombbal a most csak van, de a parancs paletta a parancs több hozzáadhatja **Azure IoT peremhálózati: IoT peremhálózati modul hozzáadása**. 
* A **.env** fájlt a környezeti változók sorolja fel. Ha ACR, a beállításjegyzék megfelelő most ACR felhasználónév és jelszó van benne. 
* A **deployment.template.json** fájl tartalmazza az új modul együtt egy minta **tempSensor** modul, amely a tesztelési használható adatok szimulálja. Hogyan telepítési módjától munkahelyi kapcsolatos további információkért lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Hozza létre, és a hibakereséshez modul telepítése

Minden modul mappában különböző tároló esetében több Docker fájl van. Ezeket a fájlokat, amelyek a kiterjesztéssel végződik bármelyikét használhatja **.debug** hozni a tesztelési modult. Jelenleg C# modulok csak támogatja a linux-amd64 tárolók hibakeresését.

1. A Visual STUDIO Code, keresse meg a `deployment.template.json` fájlt. A függvény kép URL-CÍMÉT frissíteni hozzáadásával **.debug** végéig.

   ![A lemezkép neve .debug hozzáadása](./media/how-to-develop-csharp-module/image-debug.png)

2. Írja be a Visual STUDIO Code parancs paletta, és futtassa a parancsot **peremhálózati: Build IoT peremhálózati megoldás**.
3. Válassza ki a `deployment.template.json` fájl a megoldás a parancs palettáról. 
4. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **IoT peremhálózati eszköz a központi telepítés létrehozásához**. 
5. Nyissa meg a **config** mappa a megoldás, majd válassza ki a `deployment.json` fájlt. Kattintson a **válassza ki a peremhálózati üzembe helyezési jegyzék**. 

Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

Ellenőrizheti, hogy a tároló állapota a Visual STUDIO Code Docker explorer, vagy futtassa a `docker ps` a Terminálszolgáltatások parancsot.

## <a name="start-debugging-c-module-in-vs-code"></a>Indítsa el a Visual STUDIO Code modul C#-hibakeresés
Visual STUDIO Code tartja a hibakeresés található konfigurációs információk egy `launch.json` található fájl egy `.vscode` mappa a munkaterületen. Ez `launch.json` fájl jött létre egy új IoT peremhálózati megoldás létrehozása után. Új modul, amely támogatja a hibakeresés hozzáadásakor frissíti. 

1. Nyissa meg a Visual STUDIO Code hibakeresési nézetet, és válassza ki a hibakeresési konfigurációs fájlt a modulhoz. A hibakeresési beállítás neve legyen például **ModuleName távoli hibakereséshez (.NET Core)** ![válassza hibakeresési konfiguráció](./media/how-to-develop-csharp-module/debug-config.png)

2. Nyissa meg a `program.cs` címet. Ebben a fájlban adja hozzá a töréspont.

3. Kattintson a **Start Debugging** gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.

4. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 

> [!NOTE]
> Az előző példa bemutatja, hogyan tárolók .NET Core IoT peremhálózati modulok hibakereséséhez. Hibakeresési alapul a `Dockerfile.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. A C# modulok hibakeresés befejezése után ajánlott közvetlenül a vagy testreszabása `Dockerfile` VSDBG, éles használatra kész IoT peremhálózati modulok nélkül.

## <a name="next-steps"></a>További lépések

Miután a beépített modul, megtudhatja, hogyan [a Visual Studio Code telepítése Azure IoT Edge-modulok](how-to-deploy-modules-vscode.md)

