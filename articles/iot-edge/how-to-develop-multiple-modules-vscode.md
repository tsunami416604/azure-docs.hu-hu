---
title: A VS Code-ban több Azure IoT Edge-modulok használata |} A Microsoft Docs
description: Az IoT-bővítmény a Visual Studio Code használatával több modul egyidejű fejlesztése az Azure IoT Edge-hez
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041250"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Egy IoT Edge-megoldás a Visual Studio Code egyszerre több modul fejlesztése

A Visual Studio Code segítségével fejleszthet több modult használó Azure IoT Edge-megoldásokat. Ebből a cikkből megtudhatja, hogyan hozhat létre, frissíthet és helyezhet üzembe a VS Code-ban egy szimulált IoT Edge-eszközön egy olyan IoT Edge-megoldást, amely átirányítja az érzékelőadatokat. 

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- A [a .NET Core SDK 2.1-es](https://www.microsoft.com/net/download)
- Egy aktív IoT Hub legalább egy IoT Edge-eszközzel

A rendszerképek és tárolók felügyeletéhez szüksége lesz az Azure IoT Hub Device Explorert integráló [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) eszközre.

## <a name="create-your-iot-edge-solution"></a>Az IoT Edge-megoldás létrehozása

1. A Visual Studio code-ban nyissa meg az integrált terminálon kiválasztásával **nézet** > **integrált terminálon**. 

1. A VS Code **Parancskatalógus**, adja meg, és futtassa a parancsot **Azure IoT Edge: IoT-Edge új megoldás**. Válassza ki a munkaterületi mappát, és nevezze el a megoldást (az alapértelmezett név az EdgeSolution). Hozzon létre egy C# modul (nevű **PipeModule**) Ez a megoldás első felhasználói modulként. Az alapértelmezett sablont, C# modul egy függőleges modult, amely közvetlenül csövek aktiválásához a felsőbb rétegbeli üzeneteit. Az első modulhoz meg kell adnia a Docker rendszerképadattárat is. Az alapértelmezett rendszerképadattár egy helyi Docker-tárolójegyzéken alapul (**localhost:5000/<first module name>**). Ezt módosíthatja az Azure Container Registryre vagy a Docker Hubra. 

2. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A gyökérmappa tartalmaz egy **modules** mappát, egy **.vscode** mappát és egy üzembe helyezési jegyzéksablonfájlt. A hibaelhárítási konfigurációk a .vscode mappában találhatók. Az összes felhasználói modul kódja a modules mappa alatti almappában található. A deployment.template.json fájl az üzembe helyezi jegyzék sablonja. A fájlban található paraméterek egy részének elemzése a minden modulmappában megtalálható module.json fájlból történik.

3. Adja hozzá a második modulját ehhez a megoldásprojekthez. Számos módon adjon hozzá egy új modul aktuális megoldáshoz. Adja meg, és futtassa a parancsot **Azure IoT Edge: hozzáadása IoT Edge-modul**. Válassza ki a frissíteni kívánt üzembe helyezési sablonfájlt. Vagy kattintson a jobb gombbal a modulok mappát, vagy kattintson a jobb gombbal a deployment.template.json fájlt, és válassza **IoT Edge-modul hozzáadása**. Ezután lesz egy legördülő lista adatforrásmodul-típus kiválasztásához. Válasszon egy **Azure Functions – C#** nevű modul **PipeFunction** és a Docker-rendszerkép tárházához. Az alapértelmezett sablont, C#-függvények modul egy függőleges modult, amely közvetlenül csövek aktiválásához a felsőbb rétegbeli üzeneteit.

4. Nyissa meg a deployment.template.json fájlt. Ellenőrizze, hogy a fájl deklarálja-e a három modult és a futtatókörnyezetet. Az üzenet a tempSensor modulból jön létre. Az üzenetet a rendszer közvetlenül a SampleModule és a SampleFunction modulokon át továbbítja, majd elküldi az IoT Hubra. 

5. Frissítse ezen modulok útvonalait az alábbi tartalommal:

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Mentse el ezt a fájlt.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Az IoT Edge-megoldás buildelése és üzembe helyezése

1. A VS Code **Parancskatalógus**, adja meg, és futtassa a parancsot **Azure IoT Edge: Build IoT Edge solution**. Az egyes modulmappákban található module.json fájl alapján a parancs elkezdi buildelni, tárolóba helyezni és továbbítani az egyes modulok Docker-rendszerképét. A parancs ezután továbbítja a deployment.template.json fájlnak a szükséges értéket, és a config mappából vett információkkal létrehozza a deployment.json fájlt. A VS Code integrált terminálja megjeleníti a buildelés előrehaladását. 

2. Az Azure IoT Hub **Device Explorerben** kattintson a jobb gombbal egy IoT Edge-eszköz azonosítójára, és válassza a **Create deployment for Edge device (Üzembe helyezés létrehozása Edge-eszközhöz)** parancsot. Válassza ki a deployment.json fájlt a config mappában. A VS Code integrált terminálja megjeleníti a sikeres üzembe helyezést az üzembe helyezés azonosítójával együtt.

3. Ha az IoT Edge-eszköz már szimulálására a fejlesztői gépen, nézze meg tekintse meg, hogy a modul kép tárolók összes start néhány percen belül.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** parancsot. Ezután válassza az **IoT: Start monitoring D2C message (D2C üzenet monitorozásának indítása)** parancsot. 
2. Az adatok monitorozásának leállításához használja a **parancskatalógus** **IoT: Stop monitoring D2C message (D2C üzenet monitorozásának leállítása)** parancsát. 

## <a name="next-steps"></a>További lépések

További forgatókönyvek Azure IoT Edge segítségével való fejlesztéshez Visual Studio Code-ban:

* A VS Code-modulok fejlesztése [C#](how-to-develop-csharp-module.md) vagy [Node.js](how-to-develop-node-module.md).
* A VS Code-ban az Azure Functions fejlesztése [C#](how-to-develop-csharp-function.md).

Az IoT Edge-eszközökön, a modulok fejlesztését [megismerése és használata az Azure IoT Hub SDK-k](../iot-hub/iot-hub-devguide-sdks.md).