---
title: "Visual Studio Code használata az Azure Functions az Azure IoT peremhálózati |} Microsoft Docs"
description: "C# hibakeresése az Azure Functions Visual STUDIO Code Azure IoT oldala"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Visual Studio Code használata az Azure Functions Azure IoT oldala

Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) hibakeresése az Azure Functions IoT oldal fő fejlesztési eszközként.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használ. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ellenőrizze, hogy a befejezett alábbi oktatóanyagok az Ez az útmutató megkezdése előtt.
- [Visual Studio Code segítségével fejlesztésekor és telepítésekor az Azure Functions Azure IoT szegélyhez](how-to-vscode-develop-azure-function.md)

Miután elvégzi az előző oktatóanyag, készen áll, a következő elemeket kell rendelkeznie
- A fejlesztői gépen futó helyi Docker beállításjegyzékbeli. Helyi Docker-beállításjegyzék prototípus és tesztelési célú használata javasolt.
- A `run.csx` fájl legújabb szűrő függvény kóddal.
- Egy frissített `deployment.json` az érzékelő és szűrő függvény modul fájlt.
- Egy peremhálózati futtatókörnyezetet fut a fejlesztési számítógépén.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>A hibakeresési célú IoT peremhálózati modul létrehozása
1. A hibakeresés, kell használnia a **dockerfile.debug** építse újra a docker-lemezképet, és telepítse újra a peremhálózati megoldás. Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson a Docker mappára a megnyitásához. Kattintson a `linux-x64` mappát, kattintson a jobb gombbal a **Dockerfile.debug**, és kattintson a **Build IoT peremhálózati modul Docker kép**.

    ![Hibakeresési lemezkép](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Az a **Mappaválasztás** ablakban, keresse meg a **FilterFunction** projektre, kattintson **EXE_DIR mappában válassza ki**.
3. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filterfunction:latest`. Ha a helyi beállításjegyzékben telepít, meg kell `localhost:5000/filterfunction:latest`.

    ![Leküldéses kép](./media/how-to-debug-csharp-function/push-image.png)

4. A Docker-tárház küldje le a lemezképet. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. Az azonos kép URL-cím használata fenti lépés szerepel.
5. Újrahasználhatja a `deployment.json` újratelepíteni. Paletta parancsot, írja be, és válassza ki **peremhálózati: Indítsa újra a biztonsági** a szűrő függvény a hibakeresési verzió fut eléréséhez.

## <a name="start-debugging-in-vs-code"></a>Indítsa el a Visual STUDIO Code-hibakeresés
1. Ugrás a Visual STUDIO Code hibakeresési ablakban. Nyomja le az **F5** válassza **IoT Edge(.Net Core)**

    ![Nyomja le az F5 billentyűt](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. A `launch.json`, navigáljon a **Debug IoT peremhálózati funkciót (.NET Core)** szakaszt, és töltse ki a `<container_name>`alatt `pipeArgs`. Meg kell `filterfunction` ebben az oktatóanyagban.

    ![Frissítés launch.json](./media/how-to-debug-csharp-function/update-launch-json.png)

3. Navigáljon a run.csx. Adja hozzá a Töréspont a függvényben.
4. Navigáljon a hibakeresési ablakban (Ctrl + Shift + D), válassza a **Debug IoT peremhálózati funkciót (.NET Core)** a legördülő listából. 

    ![Válassza ki a hibakeresési mód](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. Kattintson a Start Debugging gombra vagy nyomja le az **F5**, és válassza ki a csatlakoztatni kívánt folyamatot.

    ![Függvény folyamat csatolása](./media/how-to-debug-csharp-function/attach-function-process.png)

6. A Visual STUDIO Code Debug ablakban megtekintheti a változók a bal oldali panelen. 

> [!NOTE]
> A fenti példa bemutatja, hogyan hibakeresési .net Core IoT peremhálózati függvény a tárolók. Hibakeresési alapul a `Dockerfile.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati függvény a C# függvény hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-függvény létrehozása és telepítve lett a IoT peremhálózati hibakeresési célra, és elindította a Visual STUDIO Code-hibakeresés azt. Be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT peremhálózati Visual STUDIO Code fejlesztése során az alábbi oktatóanyagok tovább. 

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor a Visual STUDIO Code modul C#](how-to-vscode-develop-csharp-module.md)

