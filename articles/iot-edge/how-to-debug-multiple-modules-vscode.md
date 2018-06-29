---
title: Több modul hibakeresése a Visual STUDIO Code Azure IoT szegélyt |} Microsoft Docs
description: Visual Studio Code használata több modul Azure IoT oldala
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049591"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Visual Studio Code használata több modul Azure IoT oldala
Ez a cikk részletes utasításokat biztosít [Visual Studio (VS) kód](https://code.visualstudio.com/) IoT oldal több modul hibakereséséhez.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezése [egy IoT peremhálózati megoldást a Visual Studio Code több modulok](tutorial-multiple-modules-in-vscode.md) , és győződjön meg arról, hogy az IoT-peremhálózati eszközön futó legalább két modulok.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Több cél- és távoli Visual STUDIO Code-hibakeresés
VS kódot és az Azure IoT Edge-bővítménnyel hogy a tároló fut a fejlesztési számítógépen vagy egy távoli fizikai IoT peremhálózati eszköz csatolhat tároló, a modul folyamat. A tárolók futó hibakeresési mutiple modulok ténylegesen hozzácsatolása ehhez a külön tárolókban lévő több folyamat. Visual STUDIO Code [több cél hibakeresés](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) mutiple modulok hibakeresés során használhatók.

   > [!TIP]
   > Ha a modul tároló egy távoli fizikai IoT peremhálózati eszközön fut, szükség lehet a telepítő [Docker gép](https://docs.docker.com/machine/overview/) , hogy a fejlesztő gépen Docker-motorhoz képes kommunikálni a távoli Docker-gazdagépekből.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Az IoT-Edge modulok build hibakeresési célra
1. A több modulból álló hibakeresés kell használnia **Dockerfile.amd64.debug** építse újra a docker lemezképeket, és telepítse újra a peremhálózati megoldás. A Visual STUDIO Code Intézőben navigáljon `deployment.template.json` fájlt. A kép URL-címének frissítése hozzáadásával egy `.debug` a végén. A két modul lemezképet kell `.debug` legalább. Ha a korábbi oktatóanyagot megoldásban dolgozik, a C# funkciók és egy C# modul kell rendelkeznie. Két kép URL-frissítés hozzáadásával egy `.debug` a végén, és mentse a fájlt. 
2. A megoldás újbóli létrehozása. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **Azure IoT peremhálózati: Build IoT peremhálózati megoldás**.
3. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` a fájlt a `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

A tároló állapota a Visual STUDIO Code Docker explorer vagy futtatásával ellenőrizheti a `docker ps` a Terminálszolgáltatások parancsot.

### <a name="start-debugging-c-function-in-vs-code"></a>Indítsa el a Visual STUDIO Code függvény C#-hibakeresés
1. Visual STUDIO Code tartja a hibakeresés található konfigurációs információk egy `launch.json` található fájl egy `.vscode` mappa a munkaterületen. Ez `launch.json` fájl jött létre egy új IoT peremhálózati megoldás létrehozása után. Új modul, amely támogatja a hibakeresés hozzáadásakor frissíti. Nyissa meg a hibakeresési nézetet, és válassza ki a megfelelő hibakeresési konfigurációs fájlt a C# funkciók modul távoli hibakereséshez.
2. Nyissa meg a `run.csx` címet. Adja hozzá a Töréspont a függvényben.
3. Kattintson a **Start Debugging** gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.
4. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Indítsa el a hibakeresést C# modul egyszerre Visual STUDIO Code
1. A Visual STUDIO Code parancs paletta írja be, és futtassa a parancsot "Munkaterület: duplikált munkaterület az új ablakban". Egy új Visual STUDIO Code ablakban ugyanazon a munkaterületen kezdődik.
2. Nyissa meg a hibakeresési nézetet, és válassza ki a megfelelő hibakeresési konfigurációs fájlt a C# modul távoli hibakereséshez.
3. Nyissa meg a `program.cs` címet. Adja hozzá a Töréspont a C# modulban.
4. Kattintson a **Start Debugging** gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.
5. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Lásd: a több hibakeresési windows változók
1. Most, hogy legalább két hibakeresési munkamenetben futtassa két Visual STUDIO Code-ablakban. A töréspont egyikét kell találati.
2. Nyomja le az `F10` vagy Átlépés gombra kattintva a **hibakeresési eszköztár**.
3. A töréspont egy másik Visual STUDIO Code ablakban kattintson a kell. 
4. Továbbra is két lépésben fent, változók mutilple modulokban több Visual STUDIO Code windows hibakeresés megtekintheti.

> [!NOTE]
> A fenti példa bemutatja, hogyan több modul hibakeresésre Azure IoT oldala. Hibakeresési alapul a `Dockerfile.amd64.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati függvény a C# függvény hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések

Miután a beépített modul, megtudhatja, hogyan [a Visual Studio Code telepítése Azure IoT peremhálózati modulok](how-to-deploy-modules-vscode.md) 0
