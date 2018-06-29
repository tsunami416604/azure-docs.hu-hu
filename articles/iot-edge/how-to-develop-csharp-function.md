---
title: Az Azure IoT biztonsági funkciók modulok Debug |} Microsoft Docs
description: Visual Studio Code használata az Azure Functions C# Azure IoT oldala
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052827"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Használja a Visual Studio Code fejlesztésére és hibakeresése az Azure Functions Azure IoT szegély

Ez a cikk részletes utasításokat biztosít [Visual Studio (VS) kód](https://code.visualstudio.com/) hibakeresése az Azure Functions IoT oldal.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használja. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

> [!NOTE]
> Ez az oktatóanyag hibakeresési modul tároló folyamat csatolja, és azt a Visual STUDIO Code hibakereséshez ismerteti. Linux-amd64 tárolók modulok C# csak megoldhassuk. Ha nem ismeri a Visual Studio Code hibakeresési lehetőségeket, olvassa el [hibakeresés](https://code.visualstudio.com/Docs/editor/debugging). 

Ez a cikk a Visual Studio Code használja, mint a fő fejlesztőeszköz. Visual STUDIO Code telepítése, majd adja hozzá a szükséges kiterjesztéseket: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge-bővítményt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, .NET hozhat létre a projektmappában, Docker hozhat létre a modul lemezképet, és ahhoz, hogy a modul lemezképet tároló beállításjegyzékbeli kell:
* [A .NET core SDK 2.1-es](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) a fejlesztési számítógépén. 
* [Azure-tárolót beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Egy helyi Docker beállításjegyzék prototípus és tesztelési célokra, a felhő beállításjegyzék helyett használhat. 

A modul az eszközön teszteléséhez szükséges legalább egy IoT peremhálózati eszköz az aktív IoT-központ. Ha szeretné használni a számítógép IoT peremhálózati eszköz, azt is megteheti az oktatóanyag a következő lépéseket követve [Windows](quickstart.md) vagy [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Hozzon létre egy új megoldássablon

A következő lépések bemutatják egy C# függvénymodul tartalmazó IoT peremhálózati megoldás létrehozása. Egyes megoldások tartalmazhatnak több modul.

1. A Visual Studio Code, válassza ki **nézet** > **integrált Terminálszolgáltatások**.
3. Válassza ki **nézet** > **paletta parancs**.
4. A parancs paletta, írja be, és futtassa a parancsot **Azure IoT peremhálózati: új IoT peremhálózati megoldás**. 

   ![Új IoT peremhálózati megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

5. Keresse meg a mappát, ahová az új megoldás létrehozása, és kattintson a **jelölje ki a mappát**. 
6. Adjon meg egy nevet a megoldás. 
7. Válasszon **Azure Functions - C#** , az első moduljában a megoldás sablonját.
8. Adja meg a modul nevét. Válasszon, amely a tároló beállításjegyzék belül egyedi nevet. 
9. A lemezképtár a modul adja meg. Visual STUDIO Code autopopulates a modul nevével, így cserélni kell **localhost:5000** a saját beállításjegyzék-információkat. Ha egy helyi Docker rendszerleíró tesztelési, majd localhost rendben. Ha Azure-tároló rendszerleíró, majd használja a beállításjegyzék-beállítások a bejelentkezési kiszolgáló. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**.

Visual STUDIO Code átveszi az adatokat a megadott, létrehoz egy IoT peremhálózati megoldás függvény projekttel, majd betölti azt egy új ablakban.

A megoldáson három elemek közül választhat: 

* A **.vscode** hibakeresési beállításokat tartalmazó mappát.
* A **modulok** almappát minden modul tartalmazó mappát. Jobb gombbal a most csak van, de érdemes felvenni több keresztül a parancs paletta paranccsal **Azure IoT peremhálózati: IoT peremhálózati modul hozzáadása**.
* A **.env** fájlt a környezeti változók sorolja fel. Ha ACR, a beállításjegyzék megfelelő most ACR felhasználónév és jelszó van benne. 
* A **deployment.template.json** fájl tartalmazza az új modul együtt egy minta **tempSensor** modult, amely a tesztelési használható adatok szimulálja. Hogyan telepítési módjától munkahelyi kapcsolatos további információkért lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Az IoT peremhálózati függvénymodul hibakeresési céllal összeállítása
1. A hibakeresés, kell használnia **Dockerfile.amd64.debug** építse újra a docker-lemezképet, és telepítse újra a peremhálózati megoldás. A Visual STUDIO Code Intézőben navigáljon `deployment.template.json` fájlt. A függvény kép URL-CÍMÉT frissíteni hozzáadásával egy `.debug` a végén.

    ![Hibakeresési lemezkép](./media/how-to-debug-csharp-function/build-debug-image.png)

2. A megoldás újbóli létrehozása. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **Azure IoT peremhálózati: Build IoT peremhálózati megoldás**.
3. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` a fájlt a `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

A tároló állapota a Visual STUDIO Code Docker explorer vagy futtatásával ellenőrizheti a `docker images` a Terminálszolgáltatások parancsot.

## <a name="start-debugging-c-function-in-vs-code"></a>Indítsa el a Visual STUDIO Code függvény C#-hibakeresés
1. Visual STUDIO Code tartja a hibakeresés található konfigurációs információk egy `launch.json` található fájl egy `.vscode` mappa a munkaterületen. Ez `launch.json` fájl jött létre egy új IoT peremhálózati megoldás létrehozása után. Új modul, amely támogatja a hibakeresés hozzáadásakor frissíti. Nyissa meg a hibakeresési nézetet, és válassza ki a megfelelő hibakeresési konfigurációs fájlt. A hibakeresési beállítás neve legyen például **ModuleName távoli hibakereséshez (.NET Core)** ![válassza hibakeresési konfiguráció](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Nyissa meg a `run.csx` címet. Adja hozzá a Töréspont a függvényben.
3. Kattintson a **Start Debugging** gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.
4. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 


> [!NOTE]
> A fenti példa hogyan hibakeresési .net Core IoT peremhálózati függvény a tárolók. Hibakeresési alapul a `Dockerfile.amd64.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati függvény a C# függvény hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések

Miután a beépített modul, megtudhatja, hogyan [a Visual Studio Code telepítése Azure IoT Edge-modulok](how-to-deploy-modules-vscode.md)
