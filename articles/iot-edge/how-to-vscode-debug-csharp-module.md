---
title: Visual Studio Code használatát C# modul Azure IoT oldala |} Microsoft Docs
description: A C# modul Visual Studio Code Azure IoT szegélyt a hibakereséshez.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: eecbc10b5e030f67382d72a7b702e441a2e5492c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Visual Studio Code használatát C# modul Azure IoT oldala
Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) hibakeresése az Azure IoT peremhálózati modulok fő fejlesztési eszközként.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használja. Az IoT-peremhálózati eszköz egy másik fizikai eszköz lehet, vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

> [!NOTE]
> Linux-amd64 tárolókban lévő modul C# csak megoldhassuk.

Ez a cikk útmutatása, előtt hajtsa végre a [egy IoT peremhálózati megoldást a Visual Studio Code több modulok](tutorial-multiple-modules-in-vscode.md). Ezt követően készen áll a következő elemeket kell rendelkezniük:
- A fejlesztői gépen futó helyi Docker beállításjegyzékbeli. Helyi Docker-beállításjegyzék prototípus és tesztelési célú használata javasolt. A tároló regisztrációs frissítheti a `module.json` minden modul fájlban.
- Az IoT peremhálózati megoldás projekt munkaterület C# modul almappája azt.
- A `Program.cs` fájl, a legújabb modul kóddal.
- Egy peremhálózati futtatókörnyezetet fut a fejlesztési számítógépén.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Az IoT peremhálózati C# modul hibakeresési összeállítása
1. A hibakeresés, kell használnia a **Dockerfile.amd64.debug** építse újra a docker-lemezképet, és telepítse újra a peremhálózati megoldás. A Visual STUDIO Code Intézőben navigáljon `deployment.template.json` fájlt. A függvény kép URL-CÍMÉT frissíteni hozzáadásával egy `.debug` a végén.

2. A megoldás újbóli létrehozása. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: Build IoT peremhálózati megoldás**.

3. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` alatt `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

> [!NOTE]
> Ellenőrizheti, hogy a tároló állapota a Visual STUDIO Code Docker explorer, vagy futtassa a `docker images` a Terminálszolgáltatások parancsot.

## <a name="start-debugging-c-module-in-vs-code"></a>Indítsa el a Visual STUDIO Code modul C#-hibakeresés
1. Visual STUDIO Code tartja a hibakeresés található konfigurációs információk egy `launch.json` található fájl egy `.vscode` mappa a munkaterületen. Ez `launch.json` fájl hozott létre egy új IoT peremhálózati megoldás létrehozásakor. És frissíti az új modul, amely támogatja a hibakeresés hozzáadásakor. Nyissa meg a hibakeresési nézetet, és válassza ki a megfelelő hibakeresési konfigurációs fájlt.
    ![Válassza ki a hibakeresési konfiguráció](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Nyissa meg a `program.cs` címet. Ebben a fájlban adja hozzá a töréspont.

3. Kattintson a Start Debugging gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.

4. A Visual STUDIO Code hibakeresési nézet a változók a bal oldali panelen látható. 

> [!NOTE]
> Az előző példa bemutatja, hogyan tárolók .NET Core IoT peremhálózati modulok hibakereséséhez. Hibakeresési alapul a `Dockerfile.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. A C# modulok hibakeresés befejezése után ajánlott közvetlenül a vagy testreszabása `Dockerfile` VSDBG, éles használatra kész IoT peremhálózati modulok nélkül.

## <a name="next-steps"></a>További lépések

[Visual Studio Code használata az Azure Functions Azure IoT oldala](how-to-vscode-debug-azure-function.md)

