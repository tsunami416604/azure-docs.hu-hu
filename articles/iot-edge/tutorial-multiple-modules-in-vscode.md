---
title: A Visual Studio Code több IoT biztonsági modulok használata |} Microsoft Docs
description: Azure Machine Learning peremhálózati eszköz egy modul telepítése
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Az IoT peremhálózati megoldást a Visual Studio Code több modulok – előzetes
Használhatja a Visual Studio Code az IoT-peremhálózati megoldás több modulok fejlesztéséhez. Ez a cikk bemutatja, hogyan létrehozása, frissítése és telepítése az IoT-peremhálózati megoldás, hogy csövek érzékelőadatait Visual Studio Code szimulált IoT peremhálózati eszközön keresztül. Ebből a cikkből megismerheti, hogyan:

* Az IoT-peremhálózati megoldás létrehozása a Visual Studio Code segítségével
* Új modul hozzáadása a munkát a VS kód segítségével IoT peremhálózati megoldás. 
* Az IoT-peremhálózati eszköz a IoT peremhálózati megoldás (több modulok) telepítése
* A létrejött adatok megtekintése

## <a name="prerequisites"></a>Előfeltételek
* Alább oktatóanyagok befejezése
  * [C# modul telepítése](tutorial-csharp-module.md)
  * [C# funkció telepítése](tutorial-deploy-function.md)
  * [Python modul telepítése](tutorial-python-module.md)
* [Visual STUDIO Code docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) explorer integrálása a képeket és tárolók kezelése.


## <a name="prepare-your-first-iot-edge-solution"></a>Az első IoT peremhálózati megoldás előkészítése
1. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: új IoT peremhálózati megoldás**. Válassza ki a munkaterület mappában, a megoldás nevét adja meg (az alapértelmezett név az **EdgeSolution**), és hozzon létre egy C# modult (**SampleModule**) ebben a megoldásban az első felhasználó modulként. Meg kell adnia a Docker lemezképtárba az első modul is. Az alapértelmezett lemezképtárba egy helyi Docker beállításjegyzék alapul (`localhost:5000/<first module name>`). Is módosíthatja azt az Azure-tárolót beállításjegyzék- vagy Docker központ.

> [!NOTE]
> Ha egy helyi Docker beállításjegyzék használ, ellenőrizze, hogy a parancs beírásával fut a beállításjegyzék `docker run -d -p 5000:5000 --restart=always --name registry registry:2` a konzolablakban.

2. A Visual STUDIO Code ablak betölti a IoT peremhálózati megoldás munkaterületen. Van egy `modules` mappa, egy `.vscode` mappa és a központi telepítés manifest sablon fájl a gyökérmappában. Látható konfigurációja debug `.vscode` mappát. Minden felhasználó modul kód lesz a mappa almappái `modules`. A `deployment.template.json` van a jegyzékfájl központi telepítési sablont. Az ebben a fájlban paraméterek némelyike fog értelmezni a `module.json`, amely minden modul mappa létezik.

3. A második modul hozzá lesz adva a megoldás projektbe. Írja be a megadott idő, és futtassa **peremhálózati: hozzáadása IoT peremhálózati modul** jelölje ki a központi telepítési sablon fájlt frissíteni. Válassza ki az **Azure-függvény - C#** nevű **SampleFunction** és a Docker lemezképtárba hozzáadásához.

4. Az első IoT peremhálózati megoldás két alapvető modulok most már készen áll. Az alapértelmezett C# modul cső üzenet modul funkcionál, amíg a C# Funtion úgy működik, mint a cső üzenet függvény. Az a `deployment.template.json`, látni fogja, ez a megoldás három lehetővé tevő modulokat tartalmaz. Az üzenet a jön létre a `tempSensor` modul, és a rendszer kell közvetlenül adatcsatornán keresztül `SampleModule` és `SampleFunction`, majd küldi tovább az IoT hub. Az ezekhez a modulokhoz a tartalom alatt az útvonalak frissítése. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Mentse a fájlt.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Hozza létre, és az IoT-peremhálózati megoldás üzembe helyezéséhez
1. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: Build IoT peremhálózati megoldás**. Alapján a `module.json` minden modul mappában, a parancs fájlját a rendszer ellenőrizze, majd létre, containerize, és minden modul docker kép leküldéses start. Ezután azt fogja elemezni a szükséges érték `deployment.template.json`, készítése a `deployment.json` a tényleges érték `config` mappát. A létrehozási folyamat állapotát a Visual STUDIO Code integrált Terminálszolgáltatások tekintheti meg.

2. Az Azure IoT Hub-eszközöknek Explorerben (megoldáskezelőben) kattintson a jobb gombbal egy IoT peremhálózati eszköz azonosítója, majd válasszon **peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki a `deployment.json` alatt `config` mappát. Láthatja majd a telepítés sikeres létrehozása a Visual STUDIO Code azonosító integrált telepítés terminál.

3. Ha Ön [IoT peremhálózati eszköz szimulálva](tutorial-simulate-device-linux.md) a fejlesztési számítógépén. Látni fogja, hogy a modul kép tárolók indul néhány perc múlva.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához válassza a **Nézet** > **Parancskatalógus...**  elemet, és keressen rá az **IoT: D2C üzenet monitorozásának megkezdése** kifejezésre. 
2. Az adatok monitorozásának leállításához használja a Parancskatalógus **IoT: D2C üzenet monitorozásának leállítása** parancsát. 

## <a name="next-steps"></a>További lépések

Továbbra is be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT szegélyt Visual Studio Code fejlesztése során a következő cikkeket:

* [A C# modul Visual STUDIO Code hibakeresése](how-to-vscode-debug-csharp-module.md)
* [A C# függvény Visual STUDIO Code hibakeresése](how-to-vscode-debug-azure-function.md)