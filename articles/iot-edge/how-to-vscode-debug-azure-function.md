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
ms.openlocfilehash: 4344a450d218a7424cd055cf086c1e4865c9af10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
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
3. Az a **Mappaválasztás** ablakban, keresse meg a **FilterFunction** projektre, kattintson **EXE_DIR mappában válassza ki**.
4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filterfunction:latest`. Ha a helyi beállításjegyzékben telepít, meg kell `localhost:5000/filterfunction:latest`.
5. A Docker-tárház küldje le a lemezképet. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. Az azonos kép URL-cím használata fenti lépés szerepel.
6. Újrahasználhatja a `deployment.json` újratelepíteni. Paletta parancsot, írja be, és válassza ki **peremhálózati: Indítsa újra a biztonsági** a szűrő függvény a hibakeresési verzió fut eléréséhez.

## <a name="start-debugging-in-vs-code"></a>Indítsa el a Visual STUDIO Code-hibakeresés
1. Ugrás a Visual STUDIO Code hibakeresési ablakban. Nyomja le az **F5** válassza **IoT Edge(.Net Core)**
2. A `launch.json`, navigáljon a **Debug IoT peremhálózati funkciót (.NET Core)** szakaszt, és töltse ki a `<container_name>`alatt `pipeArgs`. Meg kell `filterfunction` ebben az oktatóanyagban.
3. Navigáljon a run.csx. Adja hozzá a Töréspont a függvényben.
4. Nyomja le az **F5** újra. És válassza ki a csatlakoztatni kívánt folyamatot.
5. A Visual STUDIO Code Debug ablakban megtekintheti a változók a bal oldali panelen. 

> [!NOTE]
> A fenti példa bemutatja, hogyan hibakeresési .net Core IoT peremhálózati függvény a tárolók. Hibakeresési alapul a `Dockerfile.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati függvény a C# függvény hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-függvény létrehozása és telepítve lett a IoT peremhálózati hibakeresési célra, és elindította a Visual STUDIO Code-hibakeresés azt. Be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT peremhálózati Visual STUDIO Code fejlesztése során az alábbi oktatóanyagok tovább. 

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor a Visual STUDIO Code modul C#](how-to-vscode-develop-csharp-module.md)

