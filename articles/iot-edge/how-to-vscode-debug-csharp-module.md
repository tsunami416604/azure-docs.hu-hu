---
title: "Visual Studio Code használatát C# modul Azure IoT oldala |} Microsoft Docs"
description: "Visual STUDIO Code Azure IoT szegélyt a C# modul hibakereséshez"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 01d321dce439e153b494dfd0de52c100dab78f39
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Visual Studio Code Azure IoT peremhálózati hibakeresési C# modul használata
Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) hibakeresése az IoT-Edge modulok fő fejlesztési eszközként.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használ. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ellenőrizze, hogy a befejezett alábbi oktatóanyagok az Ez az útmutató megkezdése előtt.
- [Használja a Visual Studio Code fejlesztésére C# modul Azure IoT oldala](how-to-vscode-develop-csharp-module.md)

Miután elvégzi az előző oktatóanyag, készen áll, a következő elemeket kell rendelkeznie
- A fejlesztői gépen futó helyi Docker beállításjegyzékbeli. Helyi Docker-beállításjegyzék prototípus és tesztelési célú használata javasolt.
- A `Program.cs` fájl legújabb szűrő modul kóddal.
- Egy frissített `deployment.json` az érzékelő és szűrő modul fájlt.
- Egy peremhálózati futtatókörnyezetet fut a fejlesztési számítógépén.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>A hibakeresési célú IoT peremhálózati modul létrehozása
1. A hibakeresés, kell használnia a **dockerfile.debug** építse újra a docker-lemezképet, és telepítse újra a peremhálózati megoldás. Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson a Docker mappára a megnyitásához. Kattintson a `linux-x64` mappát, kattintson a jobb gombbal a **Dockerfile.debug**, és kattintson a **Build IoT peremhálózati modul Docker kép**.
3. Az a **Mappaválasztás** ablak, írja be vagy keresse meg a `./bin/Debug/netcoreapp2.0/publish`. Kattintson a **mappát adja meg a EXE_DIR**.
4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filtermodule:latest`. Ha a helyi beállításjegyzékben telepít, meg kell `localhost:5000/filtermodule:latest`.
5. A Docker-tárház küldje le a lemezképet. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. Az azonos kép URL-cím használata fenti lépés szerepel.
6. Újrahasználhatja a `deployment.json` újratelepíteni. Paletta parancsot, írja be, és válassza ki **peremhálózati: Indítsa újra a peremhálózati** a a hibakeresési verzió fut modulja segítségével.

## <a name="start-debugging-in-vs-code"></a>Indítsa el a Visual STUDIO Code-hibakeresés
1. Ugrás a Visual STUDIO Code hibakeresési ablakban. Nyomja le az **F5** válassza **IoT Edge(.Net Core)**
2. A `launch.json`, navigáljon a **Debug IoT peremhálózati egyéni modul (.NET Core)** szakaszt, és töltse ki a `<container_name>`alatt `pipeArgs`. Meg kell `filtermodule` ebben az oktatóanyagban.
3. Keresse meg a program.cs fájlt. A Töréspont a `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Nyomja le az **F5** újra. És válassza ki a csatlakoztatni kívánt folyamatot. Ebben az oktatóanyagban a folyamat nevét kell megadni`FilterModule.dll`
5. A Visual STUDIO Code Debug ablakban megtekintheti a változók a bal oldali panelen. 

> [!NOTE]
> Újabb példa bemutatja, hogyan hibakeresési .net Core IoT peremhálózati modulok tárolók. Hibakeresési alapul a `Dockerfile.debug`, mely tartalmazza (a .NET Core parancssori hibakereső) VSDBG a tároló lemezkép összeállítása során. Azt javasoljuk, hogy közvetlenül használhatja vagy testre szabhatja a `Dockerfile` nélkül VSDBG, éles használatra kész IoT peremhálózati modulok a C# modulok hibakeresés befejezése után.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy IoT-Edge-modul létrehozása és hibakeresési célra telepítve lett, és elindította a Visual STUDIO Code-hibakeresés azt. Be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT peremhálózati Visual STUDIO Code fejlesztése során az alábbi oktatóanyagok tovább. 

> [!div class="nextstepaction"]
> [Fejlesztésekor és telepítésekor a Visual STUDIO Code modul C#](how-to-vscode-develop-csharp-module.md)
