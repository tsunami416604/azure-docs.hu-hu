---
title: C# modul hibakeresése az Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio Code segítségével hozhatja létre, hozhat létre, és a egy C# modul hibakeresése az Azure IoT Edge-hez
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a4d6d7bfe259e8616a51100162594315a5d37546
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382427"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Fejlesztés és az Azure IoT Edge-hez a C# modul hibakeresése Visual Studio Code használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ez a cikk bemutatja, hogyan fejleszthet és C# modul hibakeresése Visual Studio Code-ot (a VS Code) adatokként a fő eszköz.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy futtató fejlesztői gépen Windows vagy Linux rendszerű virtuális gépet használ. Az IoT Edge-eszközt egy másik fizikai eszköz lehet. Vagy az IoT Edge-eszköz szimulálhatja a fejlesztői gépen.

> [!NOTE]
> Hibakeresési Ez a cikk bemutatja, hogyan csatolhat egy folyamatot egy modul tárolóban, és a hibakeresés végrehajtása rajtuk a VS Code használatával. Csak a C#-modulokat: Linux-tárolók amd64 is hibakeresési. Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

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

A modul egy eszközön teszteléséhez van szüksége active IoT hub legalább egy IoT Edge-eszköz. Az IoT Edge-eszközöket a számítógép használja, kövesse a rövid útmutatóban a [Windows](quickstart.md) vagy [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozásához

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

Az alapértelmezett a megoldás az Azure-függvény kódjának következő helyen található **modulok** > [a modul neve] > **Program.cs**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha készen áll a C#-sablont a saját kód testreszabása, használja a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Készíthet és helyezhet üzembe a modul a hibakereséshez

Minden modul mappában nincsenek különböző tároló esetében több Docker-fájlok. Ezeket a fájlokat, amelyek a bővítmény végződhet bármelyike **.debug** hozhat létre a teszteléshez modul. Jelenleg a C# modul támogatja a csak a Linux-tárolók amd64 hibakeresését.

1. A VS Code-ban keresse meg a `deployment.template.json` fájlt. A függvény URL-címe frissítés hozzáadásával **.debug** vége.

   ![Adjon hozzá x .debug, a rendszerkép neve](./media/how-to-develop-csharp-module/image-debug.png)

2. A VS Code parancskatalógus adja meg, és futtassa a parancsot **Edge: Build IoT Edge solution**.
3. Válassza ki a `deployment.template.json` a megoldás a parancskatalógus a fájlt. 
4. Az Azure IoT Hub Device Explorer kattintson a jobb gombbal egy IoT Edge-eszköz. Válassza ki **hozzon létre telepítést az adott eszköz**. 
5. Nyissa meg a megoldást **config** mappát. Válassza ki a `deployment.json` fájlt. Válasszon **válassza ki a peremhálózati Manifest Nasazení**. 

Látni fogja az üzembe helyezés a VS Code integrált terminálon egy üzembe helyezési Azonosítóval rendelkező létrehozása sikerült.

Ellenőrizze a tároló állapotát, a Docker a VS Code Explorerben vagy futtatja az `docker ps` parancsot a terminálon.

## <a name="start-debugging-c-module-in-vs-code"></a>Indítsa el a VS Code-ban a C# modul hibakeresése
A VS Code tartja a hibakeresés konfigurációs információinak egy `launch.json` fájlt egy `.vscode` a munkaterület mappájában. Ez `launch.json` fájl jött létre, ha létrehozott egy új IoT Edge-megoldás. Új modul, amely támogatja a hibakeresés hozzáadásakor minden alkalommal frissíti. 

1. Nyissa meg a VS Code hibakeresési nézetet. Válassza ki a hibakeresési konfigurációs fájlt a modul. A hibakeresési beállítás neve legyen hasonló **ModuleName távoli hibakeresés (.NET Core)**

   ![Válassza ki a hibakeresési konfiguráció](./media/how-to-develop-csharp-module/debug-config.png).

2. Nyissa meg a `program.cs` címet. Ebben a fájlban adja hozzá egy töréspontot.

3. Válassza ki **Start Debugging** , vagy válasszon **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.

4. A VS Code hibakereső nézet láthatja a változókat a bal oldali panelen. 

> [!NOTE]
> Ez a példa bemutatja, hogyan hibakeresése a .NET Core IoT Edge-modulok a tárolók. A hibakeresési verzió az alapján `Dockerfile.debug`, amely tartalmazza a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Miután a C# modul hibakeresése, azt javasoljuk, hogy Ön közvetlenül használhatja, vagy testre szabhatja `Dockerfile` VSDBG az éles használatra kész IoT Edge-modulok nélkül.

## <a name="next-steps"></a>További lépések

A modul létrehozása, után megtudhatja, hogyan [üzembe helyezése a Visual Studio Code-ból az Azure IoT Edge-modulok](how-to-deploy-modules-vscode.md).

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
