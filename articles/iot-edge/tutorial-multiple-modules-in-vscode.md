---
title: Több Azure IoT Edge-modul felügyelete VS Code-ban | Microsoft Docs
description: A Visual Studio Code segítségével több modult használó Azure IoT Edge-megoldásokat fejleszthet.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763034"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Több modult használó IoT Edge-megoldás fejlesztése Visual Studio Code előzetes verziójában

A Visual Studio Code segítségével fejleszthet több modult használó Azure IoT Edge-megoldásokat. Ebből a cikkből megtudhatja, hogyan hozhat létre, frissíthet és helyezhet üzembe a VS Code-ban egy szimulált IoT Edge-eszközön egy olyan IoT Edge-megoldást, amely átirányítja az érzékelőadatokat. 

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- AzureIoTEdgeModule sablon (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Egy aktív IoT Hub legalább egy IoT Edge-eszközzel

A rendszerképek és tárolók felügyeletéhez szüksége lesz az Azure IoT Hub Device Explorert integráló [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) eszközre.

## <a name="prepare-your-first-iot-edge-solution"></a>Az első IoT Edge-megoldás előkészítése

1. Adja meg és futtassa az **Edge: New IoT Edge solution (Új IoT Edge-megoldás)** parancsot a VS Code **parancskatalógusában**. Válassza ki a munkaterületi mappát, és nevezze el a megoldást (az alapértelmezett név az EdgeSolution). Hozzon létre egy C# modult (**SampleModule** néven) a megoldás első felhasználói moduljaként. Az első modulhoz meg kell adnia a Docker rendszerképadattárat is. Az alapértelmezett rendszerképadattár egy helyi Docker-tárolójegyzéken alapul (**localhost:5000/<first module name>**). Ezt módosíthatja az Azure Container Registryre vagy a Docker Hubra.

   > [!NOTE]
   > Ha helyi Docker-tárolójegyzéket használ, győződjön meg arról, hogy a tárolójegyzék fut. Írja be a következő parancsot a konzolablakba:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A gyökérmappa tartalmaz egy **modules** mappát, egy **.vscode** mappát és egy üzembe helyezési jegyzéksablonfájlt. A hibaelhárítási konfigurációk a .vscode mappában találhatók. Az összes felhasználói modul kódja a modules mappa alatti almappában található. A deployment.template.json fájl az üzembe helyezi jegyzék sablonja. A fájlban található paraméterek egy részének elemzése a minden modulmappában megtalálható module.json fájlból történik.

3. Adja hozzá a második modulját ehhez a megoldásprojekthez. Írja be és futtassa az **Edge: Add IoT Edge module (Hozzáadás az IoT Edge modulhoz)** parancsot. Válassza ki a frissíteni kívánt üzembe helyezési sablonfájlt. Válasszon egy **SampleFunction** nevű **Azure Function - C#** modult és a hozzá tartozó Docker rendszerképadattárat.

4. Nyissa meg a deployment.template.json fájlt. Ellenőrizze, hogy a fájl deklarálja-e a három modult és a futtatókörnyezetet. Az üzenet a tempSensor modulból jön létre. Az üzenetet a rendszer közvetlenül a SampleModule és a SampleFunction modulokon át továbbítja, majd elküldi az IoT Hubra. 

5. Frissítse ezen modulok útvonalait az alábbi tartalommal:

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Mentse el ezt a fájlt.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Az IoT Edge-megoldás buildelése és üzembe helyezése

1. A VS Code **parancskatalógusában** írja be és futtassa az **Edge: Build IoT Edge solution (IoT Edge-megoldás buildelése)** parancsot. Az egyes modulmappákban található module.json fájl alapján a parancs elkezdi buildelni, tárolóba helyezni és továbbítani az egyes modulok Docker-rendszerképét. A parancs ezután továbbítja a deployment.template.json fájlnak a szükséges értéket, és a config mappából vett információkkal létrehozza a deployment.json fájlt. A VS Code integrált terminálja megjeleníti a buildelés előrehaladását.

2. Az Azure IoT Hub **Device Explorerben** kattintson a jobb gombbal egy IoT Edge-eszköz azonosítójára, és válassza a **Create deployment for Edge device (Üzembe helyezés létrehozása Edge-eszközhöz)** parancsot. Válassza ki a deployment.json fájlt a config mappában. A VS Code integrált terminálja megjeleníti a sikeres üzembe helyezést az üzembe helyezés azonosítójával együtt.

3. Ha a fejlesztői gépén szimulál egy IoT Edge-eszközt, figyelje meg, hogy a modul összes rendszerképtárolója néhány percen belül elindul.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** parancsot. Ezután válassza az **IoT: Start monitoring D2C message (D2C üzenet monitorozásának indítása)** parancsot. 
2. Az adatok monitorozásának leállításához használja a **parancskatalógus** **IoT: Stop monitoring D2C message (D2C üzenet monitorozásának leállítása)** parancsát. 

## <a name="next-steps"></a>További lépések

További forgatókönyvek Azure IoT Edge segítségével való fejlesztéshez Visual Studio Code-ban:

* [C# modul hibakeresése a VS Code-ban](how-to-vscode-debug-csharp-module.md)
* [C# függvény hibakeresése a VS Code-ban](how-to-vscode-debug-azure-function.md)
