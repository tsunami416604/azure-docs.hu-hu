---
title: Az Azure IoT biztonsági funkciók modulok Debug |} Microsoft Docs
description: Visual Studio Code használata az Azure Functions C# Azure IoT oldala
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cd870d8f5c3fff87b121ab777a086f21df07cfbc
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Visual Studio Code használata az Azure Functions Azure IoT oldala

Ez a cikk részletes utasításokat biztosít [Visual Studio (VS) kód](https://code.visualstudio.com/) hibakeresése az Azure Functions IoT oldal.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használja. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

> [!NOTE]
> Linux-amd64 tárolókban lévő funkciók C# csak megoldhassuk.

Ez a cikk útmutatása, előtt hajtsa végre a [egy IoT peremhálózati megoldást a Visual Studio Code több modulok](tutorial-multiple-modules-in-vscode.md). Ezt követően készen áll a következő elemeket kell rendelkezniük:
- A fejlesztői gépen futó helyi Docker beállításjegyzékbeli. Helyi Docker-beállításjegyzék prototípus és tesztelési célú használata javasolt. A tároló regisztrációs frissítheti a `module.json` minden modul fájlban.
- Az IoT peremhálózati megoldás projekt munkaterület azt egy Azure-függvény modul almappájában.
- A `run.csx` a funkciókódot fájlt.
- Egy peremhálózati futtatókörnyezetet fut a fejlesztési számítógépén.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Az IoT peremhálózati függvénymodul hibakeresési céllal összeállítása
1. A hibakeresés, kell használnia **Dockerfile.amd64.debug** építse újra a docker-lemezképet, és telepítse újra a peremhálózati megoldás. A Visual STUDIO Code Intézőben navigáljon `deployment.template.json` fájlt. A függvény kép URL-CÍMÉT frissíteni hozzáadásával egy `.debug` a végén.

    ![Hibakeresési lemezkép](./media/how-to-debug-csharp-function/build-debug-image.png)

2. A megoldás újbóli létrehozása. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: Build IoT peremhálózati megoldás**.
3. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` a fájlt a `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

A tároló állapota a Visual STUDIO Code Docker explorer vagy futtatásával ellenőrizheti a `docker images` a Terminálszolgáltatások parancsot.

## <a name="start-debugging-c-function-in-vs-code"></a>Indítsa el a Visual STUDIO Code függvény C#-hibakeresés
1. Visual STUDIO Code tartja a hibakeresés található konfigurációs információk egy `launch.json` található fájl egy `.vscode` mappa a munkaterületen. Ez `launch.json` fájl jött létre egy új IoT peremhálózati megoldás létrehozása után. Új modul, amely támogatja a hibakeresés hozzáadásakor frissíti. Nyissa meg a hibakeresési nézetet, és válassza ki a megfelelő hibakeresési konfigurációs fájlt.
    ![Válassza ki a hibakeresési konfiguráció](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Nyissa meg a `run.csx` címet. Adja hozzá a Töréspont a függvényben.
3. Kattintson a **Start Debugging** gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.
4. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 


> [!NOTE]
> A fenti példa bemutatja, hogyan hibakeresési .net Core IoT peremhálózati függvény a tárolók. Hibakeresési alapul a `Dockerfile.amd64.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati függvény a C# függvény hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések


[Visual Studio Code használatát C# modul Azure IoT oldala](how-to-vscode-debug-csharp-module.md)

