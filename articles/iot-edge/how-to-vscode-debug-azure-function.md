---
title: Visual Studio Code használata az Azure Functions az Azure IoT peremhálózati |} Microsoft Docs
description: C# hibakeresése az Azure Functions Visual STUDIO Code Azure IoT oldala
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8da16ffe72ad265f0201c2fe7e00e585dfa255e8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Visual Studio Code használata az Azure Functions Azure IoT oldala

Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) hibakeresése az Azure Functions IoT oldal fő fejlesztési eszközként.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használ. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ellenőrizze, hogy a befejezett alábbi oktatóanyagok az Ez az útmutató megkezdése előtt.
- [Az IoT peremhálózati megoldás több modulok a Visual Studio Code fejlesztése](tutorial-multiple-modules-in-vscode.md)

Miután elvégzi az előző oktatóanyag, készen áll, a következő elemeket kell rendelkeznie
- A fejlesztői gépen futó helyi Docker beállításjegyzékbeli. Helyi Docker-beállításjegyzék prototípus és tesztelési célú használata javasolt. A tároló regisztrációs frissítheti a `module.json` minden modul fájlban.
- Az IoT peremhálózati megoldás projekt munkaterület azt egy Azure-függvény modul almappájában.
- A `run.csx` a funkciókódot fájlt.
- Egy peremhálózati futtatókörnyezetet fut a fejlesztési számítógépén.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Az IoT peremhálózati függvénymodul hibakeresési céllal összeállítása
1. A hibakeresés, kell használnia a **Dockerfile.amd64.debug** építse újra a docker-lemezképet, és telepítse újra a peremhálózati megoldás. A Visual STUDIO Code Intézőben navigáljon `deployment.template.json` fájlt. A függvény kép URL-CÍMÉT frissíteni hozzáadásával egy `.debug` a végén.

    ![Hibakeresési lemezkép](./media/how-to-debug-csharp-function/build-debug-image.png)

2. A megoldás újbóli létrehozása. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: Build IoT peremhálózati megoldás**.

3. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` alatt `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

> [!NOTE]
> Ellenőrizheti, hogy a tároló állapota a Visual STUDIO Code Docker explorer, vagy futtassa a `docker images` a Terminálszolgáltatások parancsot.

## <a name="start-debugging-c-function-in-vs-code"></a>Indítsa el a Visual STUDIO Code függvény C#-hibakeresés
1. Visual STUDIO Code tartja a hibakeresés található konfigurációs információk egy `launch.json` található fájl egy `.vscode` mappa a munkaterületen. Ez `launch.json` fájl hozott létre egy új IoT peremhálózati megoldás létrehozásakor. És frissíti az új modul, amely támogatja a hibakeresés hozzáadásakor. Nyissa meg a hibakeresési nézetet, és válassza ki a megfelelő hibakeresési konfigurációs fájlt.
    ![Válassza ki a hibakeresési konfiguráció](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Nyissa meg a `run.csx` címet. Adja hozzá a Töréspont a függvényben.

3. Kattintson a Start Debugging gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.

4. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 


> [!NOTE]
> A fenti példa bemutatja, hogyan hibakeresési .net Core IoT peremhálózati függvény a tárolók. Hibakeresési alapul a `Dockerfile.amd64.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati függvény a C# függvény hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-függvény létrehozása és telepítve lett a IoT peremhálózati hibakeresési célra, és elindította a Visual STUDIO Code-hibakeresés azt. Be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT peremhálózati Visual STUDIO Code fejlesztése során az alábbi oktatóanyagok tovább. 

> [!div class="nextstepaction"]
> [Az IoT peremhálózati megoldás több modulok a Visual Studio Code fejlesztése](tutorial-multiple-modules-in-vscode.md)

