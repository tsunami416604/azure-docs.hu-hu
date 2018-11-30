---
title: Hibakeresés a C-modulok az Azure IoT Edge |} A Microsoft Docs
description: A Visual Studio Code segítségével hozhatja létre, hozhat létre, és a egy C modul hibakeresése az Azure IoT Edge-hez
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b8d8223647d42213eff53c2ff8310bed0cfe6cdb
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446738"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Fejlesztés és hibakeresés a C-modulok az Azure IoT Edge-hez a Visual Studio Code használatával

Az Azure IoT Edge kapcsolhatja az üzleti logikára modulokat. Ez a cikk bemutatja, hogyan a fő eszközként Visual Studio Code-ot (a VS Code) használatával fejlesztés és hibakeresés a C modulok.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy futtató fejlesztői gépen Windows vagy Linux rendszerű virtuális gépet használ. És az IoT Edge-eszköz szimulálása az IoT Edge biztonsági démon a fejlesztési számítógépén.

> [!NOTE]
> Hibakeresési Ez a cikk bemutatja, hogyan csatolhat egy folyamatot egy modul tárolóban, és a hibakeresés végrehajtása rajtuk a VS Code használatával. Csak a Linux-tárolók amd64 C modulokat is hibakeresési. Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Mivel ez a cikk a fő fejlesztőeszközt, Visual Studio Code-ot használ, a VS Code telepítése. Ezután adja hozzá a szükséges bővítmények:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C/C++-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) a Visual Studio Code-hoz.
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, a modul rendszerképének és a egy tároló-beállításjegyzéket, amely tárolja a modul rendszerképének létrehozása Docker szüksége:
* [A docker Community Edition](https://docs.docker.com/install/) a fejlesztői gépen. 
* [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Egy helyi Docker-beállításjegyzék prototípus és a egy felhőbeli beállításjegyzék helyett tesztelési célokra használható. 

A modul egy eszközön teszteléséhez van szüksége active IoT hub legalább egy IoT Edge-eszköz. Az IoT Edge-eszközöket a számítógép használja, kövesse a rövid útmutatóban a [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozásához

Hozzon létre egy IoT Edge-modul alapján az Azure IoT C SDK-ban a Visual Studio Code és az Azure IoT Edge-bővítmény ezeket a lépéseket. Először hozzon létre egy megoldást, és ezután az első modul létrehozása ebben a megoldásban. Egyes megoldások tartalmazhatnak egynél több modul. 

1. Válassza ki a Visual Studio Code- **nézet** > **integrált terminálon**.

2. Válassza ki **nézet** > **paletta parancs**. 

3. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: IoT Edge új megoldás**.

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

4. Keresse meg a mappát, ahol szeretné létrehozni az új megoldásba. Válasszon **mappa kiválasztása**. 

5. Adja meg a megoldás nevét. 

6. Válassza ki **C modul** a megoldás első modul sablonként.

7. Adja meg a modul nevét. Válassza ki, amely a tárolóregisztrációs adatbázis egyedi nevét. 

8. Adja meg a lemezképtárban a modul nevét. A VS Code autopopulates a modul neve a **localhost:5000**. Cserélje le a saját beállításjegyzék-információkat. Ha használja a helyi Docker-beállításjegyzék tesztelési, majd **localhost** nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**. Csak a sztring localhost részét cserélje le, ne törölje a modul nevét. Néz ki a végső karakterláncban \<beállításjegyzék neve\>.azurecr.io/\<modulename\>.

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-c-module/repository.png)

A VS Code időt vesz igénybe, a megadott információt, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban.

   ![IoT Edge-megoldás megtekintése](./media/how-to-develop-c-module/view-solution.png)

Nincsenek a megoldáson belül a négy elemek: 
* A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.
* A **modulok** a mappában megtalálja az egyes modulok almappát. Ezen a ponton csak eggyel rendelkezik. A paranccsal a parancskatalógus több adhat hozzá, de **Azure IoT Edge: IoT Edge-modul hozzáadása**. 
* Egy **.env** sorolja a környezeti változók. Ha az Azure Container Registry a beállításjegyzékbe, lesz egy Azure Container Registry-felhasználónév és jelszó szerepel. 

   > [!NOTE]
   > A környezet fájl csak akkor jön létre, ha a modul adja meg egy lemezképtárban. Ha korábban elfogadta a localhost alapértelmezett tesztelése és hibakeresése helyileg, majd, nem kell környezeti változók deklarálása. 

* A **deployment.template.json** sorolja az új modul és a egy minta **tempSensor** modul, amely szimulálja az adatokat, teszteléshez használható. Hogyan az üzembe helyezés jegyzékfájlok munkahelyi kapcsolatos további információkért lásd: [megtudhatja, hogyan telepítési jegyzékek használatával hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat](module-composition.md). 

## <a name="develop-your-module"></a>A modul fejlesztése

Az alapértelmezett C modul kód, amely a megoldás tartalmaz a következő helyen található **modulok** > [a modul neve] > **main.c**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha már készen áll a saját kód C-sablon testre szabása, a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Készíthet és helyezhet üzembe a modul a hibakereséshez

Minden modul mappában nincsenek különböző tároló esetében több Docker-fájlok. Ezeket a fájlokat, amelyek a bővítmény végződhet bármelyike **.debug** hozhat létre a teszteléshez modul. C modulok jelenleg támogatja a csak a Linux-tárolók amd64 hibakeresését.

1. A VS Code-ban keresse meg a `deployment.debug.template.json` fájlt. Ez a fájl tartalmazza a modul a hibakeresési verzió képek a megfelelő beállítások létrehozása. 
2. A VS Code parancskatalógus adja meg, és futtassa a parancsot **Azure IoT Edge: Build, és küldje le az IoT Edge megoldás**.
3. Válassza ki a `deployment.debug.template.json` a megoldás a parancskatalógus a fájlt. 
4. Az Azure IoT Hub Device Explorer kattintson a jobb gombbal egy IoT Edge-eszköz. Válassza ki **hozzon létre telepítést az adott eszköz**. 
5. Nyissa meg a megoldást **config** mappát. Válassza ki a `deployment.debug.amd64.json` fájlt. Válasszon **válassza ki a peremhálózati Manifest Nasazení**. 

Látni fogja az üzembe helyezés a VS Code integrált terminálon egy üzembe helyezési Azonosítóval rendelkező létrehozása sikerült.

Ellenőrizze a tároló állapotát, a Docker a VS Code Explorerben vagy futtatja az `docker ps` parancsot a terminálon.

## <a name="start-debugging-c-module-in-vs-code"></a>A VS Code-modulja C hibakeresés indítása
A VS Code tartja a hibakeresés konfigurációs információinak egy `launch.json` fájlt egy `.vscode` a munkaterület mappájában. Ez `launch.json` fájl jött létre, ha létrehozott egy új IoT Edge-megoldás. Új modul, amely támogatja a hibakeresés hozzáadásakor minden alkalommal frissíti. 

1. Nyissa meg a VS Code hibakeresési nézetet. Válassza ki a hibakeresési konfigurációs fájlt a modul. A hibakeresési beállítás neve legyen hasonló **ModuleName Remote Debug (C)**

   ![Válassza ki a hibakeresési konfiguráció](./media/how-to-develop-c-module/debug-config.png)

2. Nyissa meg a `main.c` címet. Ebben a fájlban adja hozzá egy töréspontot.

3. Válassza ki **Start Debugging** , vagy válasszon **F5**. Jelölje be a csatlakoztatni kívánt folyamatot.

4. A VS Code hibakereső nézet láthatja a változókat a bal oldali panelen. 

Az előző példa bemutatja, hogyan lehet hibákat keresni a C IoT Edge-modulok a tárolók. Ez a modul tároló createOptions elérhetővé tett port egészül ki. Miután elvégezte a C modulok hibakeresés, javasoljuk, hogy eltávolítja ezeket a közzétett portokat éles használatra kész IoT Edge-modulok.

## <a name="next-steps"></a>További lépések

A modul létrehozása, után megtudhatja, hogyan [üzembe helyezése a Visual Studio Code-ból az Azure IoT Edge-modulok](how-to-deploy-modules-vscode.md).

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
