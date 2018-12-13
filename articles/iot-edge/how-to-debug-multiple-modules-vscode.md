---
title: A Visual Studio Code - az Azure IoT Edge több modul hibakeresése |} A Microsoft Docs
description: Több modul hibakeresése az Azure IoT Edge segítségével a Visual Studio Code használatával
author: shizn
manager: philmea
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0c421eb1532536a3d11013073f0474f5ac37311b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100450"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Több modul hibakeresése az Azure IoT Edge segítségével a Visual Studio Code használatával
Ez a cikk nyújt részletes útmutatást a [Visual Studio (vagy) kód](https://code.visualstudio.com/) az IoT Edge-ben több modul hibakeresése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez [egyszerre több modul a Visual Studio Code egy IoT Edge-megoldás fejlesztése](tutorial-multiple-modules-in-vscode.md) , és ellenőrizze, hogy legalább két, az IoT Edge-eszközön futó modulok.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Több cél- és távoli hibakeresése a VS Code-ban
A VS Code és az Azure IoT Edge bővítménnyel egy tárolóban, a modul folyamat is csatlakoztatható, hogy a tároló fut-e a fejlesztői gépen, vagy egy távoli fizikai IoT Edge-eszközön. Hibakeresési mutiple modulok tárolókban futó van ténylegesen csatolása egynél több folyamat külön tárolókban. A VS Code [több cél hibakeresés](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) is használható, ha a hibakeresés több modulok.

   > [!TIP]
   > Ha a modul tároló egy távoli fizikai IoT Edge-eszközön fut, szüksége lehet a telepítő [a Docker Machine](https://docs.docker.com/machine/overview/) úgy, hogy a fejlesztői gépen a Docker-motor a távoli Docker-gazdagépek közötti kommunikáció.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Az IoT Edge modulok készítése hibakeresési célra
1. A több modul hibakeresés kell használnia **Dockerfile.amd64.debug** építse újra a docker-rendszerképeket, és telepítse újra a biztonsági megoldás. A VS Code Explorerben lépjen `deployment.template.json` fájlt. A kép URL-címek frissítéséhez adja hozzá a `.debug` a végén. A két modul lemezképet kell `.debug` legalább. Ha a megoldás az előző oktatóanyagban dolgozik, rendelkeznie kell egy C# funkciók modul és a egy C# modul. E két kép URL-címek frissítése hozzáadásával egy `.debug` a végén, és mentse a fájlt. 
2. A megoldás újbóli létrehozása. A VS Code parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: Build IoT Edge solution**.
3. Az Azure IoT Hub-eszközök Explorerben kattintson a jobb gombbal egy IoT Edge-eszköz azonosítója, majd válassza ki **üzembe helyezés az Edge-eszköz létrehozása**. Válassza ki a `deployment.json` fájlt a `config` mappát. Láthatja majd a központi telepítés sikeres létrehozása a központi telepítés azonosítója a VS Code integrált terminál.

A tároló állapota a Docker a VS Code Explorerben vagy futtatásával ellenőrizheti a `docker ps` parancsot a terminálon.

### <a name="start-debugging-c-function-in-vs-code"></a>A hibakeresés elindításához C# függvény a VS Code-ban
1. A VS Code tartja a hibakeresés konfigurációs információinak egy `launch.json` fájlt egy `.vscode` a munkaterület mappájában. Ez `launch.json` fájl jött létre, ha létrehozott egy új IoT Edge-megoldás. Új modul, amely támogatja a hibakeresés hozzáadásakor minden alkalommal frissíti. A hibakeresési nézet keresse meg és válassza a megfelelő hibakeresési konfigurációs fájljának C# funkciók modul távoli hibakeresés.
2. Nyissa meg a `run.csx` címet. Adjon hozzá egy töréspontot a függvényben.
3. Kattintson a **hibakeresés indítása** gombra vagy nyomja le a **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.
4. A változók a bal oldali panelen megjelenik a VS Code Debug nézetben. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>A hibakeresés elindításához C# modul egyszerre a VS Code-ban
1. A VS Code parancskatalógus írja be, és futtassa a "Munkaterület: ismétlődő munkaterületet az új ablakban" parancsot. Egy új VS Code-ablak ugyanazon a munkaterületen kezdődik.
2. A hibakeresési nézet keresse meg és válassza a megfelelő hibakeresési konfigurációs fájljának C# modul távoli hibakeresés.
3. Nyissa meg a `program.cs` címet. Adjon hozzá egy töréspontot a a C# modul.
4. Kattintson a **hibakeresés indítása** gombra vagy nyomja le a **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.
5. A változók a bal oldali panelen megjelenik a VS Code Debug nézetben. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Tekintse meg a több hibakeresési windows változók
1. Most már legalább két hibakeresése munkamenetében futó két VS Code-ablak. A töréspont egyik találati kell.
2. Nyomja meg `F10` vagy Átlépés gombjára a **hibakeresési eszköztár**.
3. A VS Code-ablak a töréspont kell lennie találati. 
4. Továbbra is a fenti két lépést, láthatja a változókat az mutilple modulok a hibakereséshez a windows több VS Code-ban.

> [!NOTE]
> A fenti példa bemutatja, hogyan a hibakeresés több modul az Azure IoT Edge segítségével. A hibakeresési verzió az alapján a `Dockerfile.amd64.debug`, amely tartalmazza VSDBG (a .NET Core parancssori hibakereső) a tároló rendszerképének összeállítása során. Azt javasoljuk, Ön közvetlenül használhatja, vagy testre szabhatja a `Dockerfile` nélkül VSDBG éles használatra kész IoT Edge függvény hibakeresés befejezése után a C# függvény.

## <a name="next-steps"></a>További lépések

Ha a beépített modul, megtudhatja, hogyan [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md) 0
