---
title: "Visual Studio Code használatát C# modul Azure IoT oldala |} Microsoft Docs"
description: "A C# modul Visual Studio Code Azure IoT szegélyt a hibakereséshez."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 46d9ca0bf6c9ddf95c147fc2eb62d275c973845e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Visual Studio Code használatát C# modul Azure IoT oldala
Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) hibakeresése az Azure IoT peremhálózati modulok fő fejlesztési eszközként.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használ. Az IoT-peremhálózati eszköz egy másik fizikai eszköz lehet, vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ez az útmutató megkezdése előtt hajtsa végre a következő oktatóanyagot:
- [Használja a Visual Studio Code fejlesztésére C# modul Azure IoT oldala](how-to-vscode-develop-csharp-module.md)

Az előző oktatóanyag befejezése után készen áll a következő elemeket kell:
- A fejlesztői gépen futó helyi Docker beállításjegyzékbeli. Ez a prototípusának és tesztelési célokra.
- A `Program.cs` fájl, a legújabb szűrő modul kóddal.
- Egy frissített `deployment.json` fájl az érzékelő és a szűrő modulban.
- Egy IoT peremhálózati futtatókörnyezetet, amely a fejlesztési számítógépen futtatja.

## <a name="build-your-iot-edge-module-for-debugging"></a>A hibakeresési IoT peremhálózati modul létrehozása
1. A hibakeresés, használjon **dockerfile.debug** építse újra a Docker-lemezképet, és telepítse újra az IoT-peremhálózati megoldás. Visual Studio Code Explorerben válassza ki a Docker mappára a megnyitásához. Válassza ki a **linux-x64** mappát, kattintson a jobb gombbal **Dockerfile.debug**, és válassza ki **Build IoT peremhálózati modul Docker kép**.

    ![Képernyőfelvétel a Visual STUDIO Code Explorer](./media/how-to-debug-csharp-module/build-debug-image.png)

3. Az a **Mappaválasztás** ablak, írja be vagy keresse meg a **./bin/Debug/netcoreapp2.0/publish**. Válassza ki **EXE_DIR mappában válassza ki**.
4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filtermodule:latest`. Ha a helyi beállításjegyzékben telepít, meg kell: `localhost:5000/filtermodule:latest`.
5. A Docker-tárház küldje le a lemezképet. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. A kép URL-CÍMÉRE az előző lépésben használt használja.
6. Újrahasználhatja a `deployment.json` újratelepíteni. Írja be a parancs paletta, és válassza ki **peremhálózati: Indítsa újra a peremhálózati** a a hibakeresési verzió fut modulja segítségével.

## <a name="start-debugging-in-vs-code"></a>Indítsa el a Visual STUDIO Code-hibakeresés
1. Ugrás a Visual STUDIO Code hibakeresési ablakban. Nyomja le az **F5**, és válassza ki **IoT Edge(.NET Core)**.

    ![Képernyőfelvétel a Visual STUDIO kód hibakeresési ablak](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. A `launch.json`, keresse meg a **Debug IoT peremhálózati egyéni modul (.NET Core)** szakasz. A **pipeArgs**, töltse ki a `<container_name>`. Meg kell `filtermodule` ebben az oktatóanyagban.

    ![Képernyőfelvétel a Visual STUDIO kód hibakeresési ablak](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Keresse meg a **Program.cs**. A Töréspont a `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Nyomja le az **F5** újra, és válassza ki a csatlakoztatni kívánt folyamatot. Ebben az oktatóanyagban a folyamat neve legyen `FilterModule.dll`.

    ![Képernyőfelvétel a Visual STUDIO kód hibakeresési ablak](./media/how-to-debug-csharp-module/attach-process.png)

5. A Visual STUDIO Code hibakeresési ablakban megtekintheti a változók a bal oldali panelen. 

> [!NOTE]
> Az előző példa bemutatja, hogyan tárolók .NET Core IoT peremhálózati modulok hibakereséséhez. Hibakeresési alapul a `Dockerfile.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. A C# modulok hibakeresés befejezése után ajánlott közvetlenül a vagy testreszabása `Dockerfile` VSDBG, éles használatra kész IoT peremhálózati modulok nélkül.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy IoT-Edge-modul létrehozása, és telepítve lett a hibakereséshez. A Visual STUDIO Code hibakeresését azt fut. Más esetekben Azure IoT peremhálózati Visual STUDIO Code fejlesztésekor, című témakörben olvashat: 

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor a Visual STUDIO Code modul C#](how-to-vscode-develop-csharp-module.md)
