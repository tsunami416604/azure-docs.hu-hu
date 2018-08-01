---
title: Hibakeresés a Node.js modulok az Azure IoT Edge |} A Microsoft Docs
description: Fejlesztés és hibakeresés a Node.js modulok az Azure IoT Edge-hez a Visual Studio Code használatával
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a5ab49beed79a8ea3a7ded0848c09acad27a5fb1
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390537"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Fejlesztés és hibakeresés a Node.js modulok az Azure IoT Edge segítségével a Visual Studio Code

Elküldheti az üzleti logikát a peremhálózaton csempefolyamot modulokat az Azure IoT Edge megfelelően működjenek. Ez a cikk részletesen ismerteti a fő fejlesztési eszközként, C# modul fejlesztése a Visual Studio Code (a VS Code).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy egy számítógép vagy futtató fejlesztői gépen Windows vagy Linux rendszerű virtuális gépet használ. Az IoT Edge-eszköz lehet egy másik fizikai eszközt, vagy az IoT Edge-eszköz szimulálhatja a fejlesztői gépen.

> [!NOTE]
> A hibakeresési oktatóanyag leírja, hogyan csatolhat egy folyamatot egy modul tárolóban, és a hibakeresés végrehajtása rajtuk a VS Code használatával. A linux-amd64, a windows és a tárolók arm32 Node.js modulok is hibakeresést. Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el [Debugging](https://code.visualstudio.com/Docs/editor/debugging). 

Mivel ez a cikk a fő fejlesztőeszközt, Visual Studio Code-ot használ, a VS Code telepítése, és vegye fel a szükséges bővítmények:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Az Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Hozzon létre egy modult, Node.js, amely tartalmazza az npm segítségével való hozhat létre a projektmappában, a modul rendszerképének és a egy tároló-beállításjegyzéket, amely tárolja a modul rendszerképének létrehozása Docker szüksége:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Az Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Használhat egy helyi Docker-beállításjegyzék készíthet prototípusokat és tesztelési célra, egy felhőbeli beállításjegyzék helyett. 

A modul egy eszközön teszteléséhez van szüksége active IoT hub legalább egy IoT Edge-eszköz. Ha azt szeretné, használhatja a számítógépet, IoT Edge-eszköz, megteheti a oktatóanyagok a lépéseit követve [Windows](quickstart.md) vagy [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozásához

A következő lépések bemutatják, hogyan hozhat létre egy IoT Edge-modul a .NET Core 2.0-alapú Visual Studio Code és az Azure IoT Edge bővítmény használatával. Indítsa el a megoldás létrehozásával és majd létrehozni ebben a megoldásban az első modult. Egyes megoldások a több modul is tartalmazhat. 

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
9. A modul adja meg a lemezképtárból. A VS Code autopopulates a modul neve, így csak kell cserélni **localhost:5000** a saját beállításjegyzék-információkat. Ha tesztelése helyi Docker-tárolójegyzék használja, localhost nem okoz gondot. Ha használja az Azure Container Registry, használja a bejelentkezési kiszolgáló, a beállításjegyzék-beállításai közül. A bejelentkezési kiszolgáló néz  **\<beállításjegyzék neve\>. azurecr.io**.

A VS Code vesz igénybe az adatokat a megadott, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban.

A megoldáson belül, akkor három elemet: 
* A **.vscode** mappa hibakeresési konfigurációkat tartalmaz.
* A **modulok** mappát az egyes modulok almappát tartalmaz. Jelenleg csak eggyel rendelkezik, de a paranccsal a parancskatalógus több felvehet **Azure IoT Edge: IoT Edge-modul hozzáadása**. 
* A **.env** sorolja a környezeti változók. Ha Ön ACR, a beállításjegyzék megfelelő most már rendelkezik ACR felhasználónévvel és jelszóval. 

   >[!NOTE]
   >A környezet fájl csak akkor jön létre, ha a modul adja meg egy lemezképtárban. Ha korábban elfogadta a localhost alapértelmezett tesztelése és hibakeresése helyileg, majd, nem kell környezeti változók deklarálása. 

* A **deployment.template.json** sorolja az új modul és a egy minta **tempSensor** modul, amely szimulálja az adatokat, amelyek a teszteléshez használható. Hogyan az üzembe helyezés jegyzékfájlok munkahelyi kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

## <a name="devlop-your-module"></a>A modul Devlop

Az alapértelmezett a megoldás az Azure-függvény kódjának következő helyen található **modulok** > **\<a modulnév\>**   >   **App.js**. A modul és a deployment.template.json fájl legyenek beállítva, hogy a megoldás felépítéséhez, küldje le azt a tárolóregisztrációs adatbázisba, és telepítheti az eszközöket, hogy a kód módosítása nélkül tesztelés megkezdése. A modul csak igénybe vehet a bemeneti forrásból (ami jelen esetben a tempSensor modul, amely szimulálja az adatokat), és átadhatja azt az IoT hubhoz való használatra készült. 

Ha már készen áll a saját kód a Node.js-sablon testre szabása, a [Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md) hozhat létre a modulok a kulcsot kell IoT-megoldások, például a biztonság, a kezelés és a megbízhatóság címmel. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Készíthet és helyezhet üzembe a modul a hibakereséshez

Minden modul mappában nincsenek különböző tároló esetében több Docker-fájl. Bármilyen ezeket a fájlokat, amelyek a bővítmény végződhet **.debug** hozhat létre a teszteléshez modul. Jelenleg a C# modul csak támogatja a linux-amd64 tárolók hibakeresését.

1. A VS Code-ban keresse meg a `deployment.template.json` fájlt. A modul kép URL-címe frissítés hozzáadásával **.debug** vége.
2. Cserélje le a Node.js-modul createOptions a **deployment.template.json** az alábbi tartalmat, és mentse a fájlt: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. A VS Code parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: Build IoT Edge solution**.
3. Válassza ki a `deployment.template.json` a megoldás a parancskatalógus a fájlt. 
4. Az Azure IoT Hub-eszközök Explorerben kattintson a jobb gombbal egy IoT Edge-eszköz azonosítója, majd válassza ki **hozzon létre IoT Edge-eszköz üzembe helyezése**. 
5. Nyissa meg a **config** mappában, hogy a megoldás, majd válassza ki a `deployment.json` fájlt. Kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. 

Láthatja majd a központi telepítés sikeres létrehozása a központi telepítés azonosítója a VS Code integrált terminál.

Ellenőrizheti a tárolót a Docker a VS Code Explorerben, vagy futtassa a `docker ps` parancsot a terminálon.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Hibakeresés a Node.Js-modul a VS Code-ban

A VS Code tartja a hibakeresés konfigurációs információinak egy `launch.json` fájlt egy `.vscode` a munkaterület mappájában. Ez `launch.json` fájl jött létre, ha létrehozott egy új IoT Edge-megoldás. Új modul, amely támogatja a hibakeresés hozzáadásakor minden alkalommal frissíti. 

1. Keresse meg a VS Code hibakeresési nézet, és válassza ki a hibakeresési konfigurációs fájlt a modul.

2. Nyissa meg a `app.js` címet. Ebben a fájlban adja hozzá egy töréspontot.

3. Kattintson a **hibakeresés indítása** gombra vagy nyomja le a **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.

4. A változók a bal oldali panelen megjelenik a VS Code Debug nézetben. 

Az előző példa bemutatja, hogyan debug a Node.js IoT Edge-modulok a tárolókat. Ez a modul tároló createOptions elérhetővé tett port egészül ki. Miután befejezte a hibakeresés a Node.js modulok, javasoljuk, éles használatra kész IoT Edge-modulok számára elérhetővé tett portokon távolítsa el.

## <a name="next-steps"></a>További lépések

Ha a beépített modul, megtudhatja, hogyan [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md)

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).
