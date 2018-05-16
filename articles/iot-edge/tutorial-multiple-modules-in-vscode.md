---
title: Visual STUDIO Code több Azure IoT peremhálózati modulok kezelése |} Microsoft Docs
description: Használja a Visual Studio Code IoT peremhálózati megoldás több modul használó fejlesztéséhez.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Az IoT peremhálózati megoldást a Visual Studio Code több modulok – előzetes
Használhatja a Visual Studio Code az IoT-peremhálózati megoldás több modulok fejlesztéséhez. Ez a cikk bemutatja, hogyan létrehozása, frissítése és telepítése az IoT-peremhálózati megoldás, hogy csövek érzékelőadatait Visual Studio Code szimulált IoT peremhálózati eszközön keresztül. 

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben minden lépést befejezéséhez rendelkezik a következő előfeltételek teljesülését:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [A Visual Studio Code Azure IoT Edge-bővítményt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- AzureIoTEdgeModule sablon (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Legalább egy IoT peremhálózati eszköz egy aktív IoT hubot


* [Visual STUDIO Code docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) explorer integrálása a képeket és tárolók kezelése.


## <a name="prepare-your-first-iot-edge-solution"></a>Az első IoT peremhálózati megoldás előkészítése
1. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: új IoT peremhálózati megoldás**. Válassza ki a munkaterület mappában, a megoldás nevét adja meg (az alapértelmezett név az **EdgeSolution**), és hozzon létre egy C# modult (**SampleModule**) ebben a megoldásban az első felhasználó modulként. Meg kell adnia a Docker lemezképtárba az első modul is. Az alapértelmezett lemezképtárba egy helyi Docker beállításjegyzék alapul (`localhost:5000/<first module name>`). Is módosíthatja azt az Azure-tárolót beállításjegyzék- vagy Docker központ.

   > [!NOTE]
   > Egy helyi Docker beállításjegyzék használatakor, hogy a beállításjegyzék fut a parancs beírásával `docker run -d -p 5000:5000 --restart=always --name registry registry:2` a konzolablakban.

2. A Visual STUDIO Code ablak betölti a IoT peremhálózati megoldás munkaterületen. A legfelső szintű mappa tartalmaz egy `modules` mappa, egy `.vscode` mappa, és a központi telepítési jegyzék sablonfájl. Látható konfigurációja debug `.vscode` mappát. Minden felhasználó modul kód lesz a mappa almappái `modules`. A `deployment.template.json` van a jegyzékfájl központi telepítési sablont. Az ebben a fájlban paraméterek némelyike fog értelmezni a `module.json`, amely minden modul mappa létezik.

3. A második modul hozzá lesz adva a megoldás projektbe. Írja be a megadott idő, és futtassa **peremhálózati: hozzáadása IoT peremhálózati modul** jelölje ki a központi telepítési sablon fájlt frissíteni. Válassza ki az **Azure-függvény - C#** nevű **SampleFunction** és a Docker lemezképtárba.

4. Nyissa meg a `deployment.template.json` fájlt, és győződjön meg arról, hogy a futtatókörnyezet mellett három modulok deklarál. Az üzenet a jön létre a `tempSensor` modul, és a rendszer kell közvetlenül adatcsatornán keresztül `SampleModule` és `SampleFunction`, majd küldi tovább az IoT hub. 
5. Ezekhez a modulokhoz útvonalak frissítése a következő tartalommal:
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Mentse a fájlt.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Hozza létre, és az IoT-peremhálózati megoldás üzembe helyezéséhez
1. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: Build IoT peremhálózati megoldás**. Alapján a `module.json` minden modul mappában, a parancs kezdési felépítéséhez, containerize, és minden modul docker kép leküldéses fájlt. Ezután átadja a szükséges érték `deployment.template.json` állít elő, és a `deployment.json` fájl adataival a `config` mappa. A létrehozási folyamat állapotát a Visual STUDIO Code integrált Terminálszolgáltatások tekintheti meg.

2. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` alatt `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

3. Ha egy IoT peremhálózati eszköz a fejlesztői gépen vannak szimulálva, látni fogja, hogy a modul kép tárolók néhány perc múlva indul.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához válassza a **Nézet** > **Parancskatalógus...**  elemet, és keressen rá az **IoT: D2C üzenet monitorozásának megkezdése** kifejezésre. 
2. Az adatok monitorozásának leállításához használja a Parancskatalógus **IoT: D2C üzenet monitorozásának leállítása** parancsát. 

## <a name="next-steps"></a>További lépések

Más esetekben fejlesztéséhez a Visual Studio Code Azure IoT szegélyt megismerése:

* [A C# modul Visual STUDIO Code hibakeresése](how-to-vscode-debug-csharp-module.md)
* [A C# függvény Visual STUDIO Code hibakeresése](how-to-vscode-debug-azure-function.md)