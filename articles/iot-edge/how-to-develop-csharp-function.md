---
title: Azure-függvény modulok hibakeresése az Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio Code az Azure C#-függvények hibakeresése az Azure IoT Edge szolgáltatással
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bf2d1af66cc3ecc35dafe3bcd43bf10399d71641
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346715"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Fejlesztés és hibakeresés az Azure functions az Azure IoT Edge-hez a Visual Studio Code használatával

Ez a cikk bemutatja, hogyan használható [Visual Studio Code-ot (a VS Code)](https://code.visualstudio.com/) hibakeresése az Azure functions az Azure IoT Edge-ben.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy futtató fejlesztői gépen Windows vagy Linux rendszerű virtuális gépet használ. Az IoT Edge-eszközt egy másik fizikai eszköz lehet. Vagy az IoT Edge-eszköz szimulálhatja a fejlesztői gépen.

> [!NOTE]
> Hibakeresési Ez a cikk bemutatja, hogyan csatolhat egy folyamatot egy modul tárolóban, és a hibakeresés végrehajtása rajtuk a VS Code használatával. Csak a C#-függvények Linux amd64 tárolókban is hibakeresési. Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Ez a cikk a Visual Studio Code-ot használja a fő fejlesztési eszköz. A VS Code telepítése. Ezután adja hozzá a szükséges bővítmények: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, hozhat létre a projektmappában, Docker, a modul rendszerképének és a egy tároló-beállításjegyzéket, amely tárolja a modul rendszerképének létrehozása .NET szüksége:
* [A .NET core SDK 2.1-es](https://www.microsoft.com/net/download)
* [A docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen 
* [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható. 

A modul egy eszközön teszteléséhez van szüksége active IoT hub legalább egy IoT Edge-eszköz. Az IoT Edge-eszközöket a számítógép használja, kövesse a rövid útmutatóban a [Windows](quickstart.md) vagy [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozásához

Ezeket a lépéseket hozhat létre, amely rendelkezik egy C# függvény modul IoT Edge-megoldásokat. Egyes megoldások a több modulok is rendelkezhet.

1. Válassza ki a Visual Studio Code- **nézet** > **integrált terminálon**.
3. Válassza ki **nézet** > **paletta parancs**.
4. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: IoT Edge új megoldás**. 

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

5. Keresse meg a mappát, ahol szeretné létrehozni az új megoldásba. Válasszon **mappa kiválasztása**. 
6. Adja meg a megoldás nevét. 
7. Válasszon **Azure Functions – C#** a megoldás első modul sablonként.
8. Adja meg a modul nevét. Válassza ki, amely a tárolóregisztrációs adatbázis egyedi nevét. 
9. A modul adja meg a lemezképtárból. A VS Code autopopulates a modul neve a **localhost:5000**. Cserélje le a saját beállításjegyzék-információkat. Ha használja a helyi Docker-beállításjegyzék tesztelési, majd **localhost** nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**.

A VS Code vesz igénybe az adatokat a megadott, az Azure Functions-projektet hoz létre egy IoT Edge-megoldás és betölt egy új ablakban.

Nincsenek a megoldáson belül a négy elemek: 

* A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.
* A **modulok** a mappában megtalálja az egyes modulok almappát. Ezen a ponton csak eggyel rendelkezik. Azonban hozzáadhat további keresztül a paranccsal a parancskatalógus **Azure IoT Edge: IoT Edge-modul hozzáadása**.
* Egy **.env** sorolja a környezeti változók. Ha az Azure Container Registry a beállításjegyzékbe, lesz egy Azure Container Registry-felhasználónév és jelszó szerepel. 

   >[!NOTE]
   >A környezet fájl csak akkor jön létre, ha a modul adja meg egy lemezképtárban. Ha korábban elfogadta a localhost alapértelmezett tesztelése és hibakeresése helyileg, majd, nem kell környezeti változók deklarálása. 

* A **deployment.template.json** sorolja az új modul és a egy minta **tempSensor** modul, amely szimulálja az adatokat, teszteléshez használható. Hogyan az üzembe helyezés jegyzékfájlok munkahelyi kapcsolatos további információkért lásd: [megtudhatja, hogyan telepítési jegyzékek használatával hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](module-composition.md).

## <a name="develop-your-module"></a>A modul fejlesztése

Az alapértelmezett a megoldás az Azure-függvény kódjának következő helyen található **modulok** > **\<a modulnév\>**   >   **EdgeHubTrigger-Csharp** > **run.csx**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha már készen áll a saját kódját az Azure-függvény-sablon testre szabása, a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

## <a name="build-your-module-for-debugging"></a>A hibakeresési modul létrehozása
1. A hibakeresés, használjon **Dockerfile.amd64.debug** építse újra a docker-rendszerképet, és telepítse újra a biztonsági megoldás. A VS Code Explorerben keresse meg a `deployment.template.json` fájlt. A függvény URL-címe frissítés hozzáadásával `.debug` vége.

    ![Hibakeresési rendszerkép összeállítása](./media/how-to-debug-csharp-function/build-debug-image.png)

2. A megoldás újbóli létrehozása. A VS Code parancskatalógus adja meg, és futtassa a parancsot **Azure IoT Edge: Build IoT Edge solution**.
3. Az Azure IoT Hub-eszközök Explorerben kattintson a jobb gombbal egy IoT Edge-eszköz azonosítója, és válassza **üzembe helyezés az Edge-eszköz létrehozása**. Válassza ki a `deployment.json` fájlt a `config` mappát. Látni fogja az üzembe helyezés a VS Code integrált terminálon egy üzembe helyezési Azonosítóval rendelkező létrehozása sikerült.

Ellenőrizze a tároló állapotát, a Docker a VS Code Explorerben vagy futtatja az `docker images` parancsot a terminálon.

## <a name="start-debugging-c-functions-in-vs-code"></a>Hibakeresés a C#-függvények a VS Code-ban
1. A VS Code tartja a hibakeresés konfigurációs információinak egy `launch.json` fájlt egy `.vscode` a munkaterület mappájában. Ez `launch.json` fájl jött létre, ha létrehozott egy új IoT Edge-megoldás. Új modul, amely támogatja a hibakeresés hozzáadásakor minden alkalommal frissíti. Keresse meg a hibakeresési nézet. Válassza ki a megfelelő hibakeresési konfigurációs fájlt. A hibakeresési beállítás neve legyen hasonló **ModuleName távoli hibakeresés (.NET Core)**.

   ![Válassza ki a hibakeresési konfiguráció](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Nyissa meg a `run.csx` címet. Adjon hozzá egy töréspontot a függvényben.
3. Válassza ki **Start Debugging** , vagy válasszon **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.
4. A VS Code hibakeresési nézet láthatja a változókat a bal oldali panelen. 


> [!NOTE]
> Ez a példa bemutatja, hogyan lehet hibákat keresni a .net Core IoT Edge-funkciók a tárolók. A hibakeresési verzió az alapján a `Dockerfile.amd64.debug`, amely tartalmazza a .NET Core parancssori hibakereső VSDBG a tároló rendszerképének összeállítása során. Azt javasoljuk, hogy Ön közvetlenül használhatja, vagy testre szabhatja a `Dockerfile` nélkül VSDBG az éles használatra kész IoT Edge függvények a C#-függvények hibakeresése után.

## <a name="next-steps"></a>További lépések

A modul létrehozása, után megtudhatja, hogyan [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md).

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
