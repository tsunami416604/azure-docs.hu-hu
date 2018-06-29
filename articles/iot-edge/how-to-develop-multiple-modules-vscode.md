---
title: Visual STUDIO Code több Azure IoT biztonsági modulok használata |} Microsoft Docs
description: A Visual Studio Code IoT kiterjesztése használatával kialakított egyszerre több modult Azure IoT él
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 4e9aac5f19fa75613dee2aba3853a0243d7d966b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048260"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Az IoT peremhálózati megoldás több modulok a Visual Studio Code fejlesztése

A Visual Studio Code segítségével fejleszthet több modult használó Azure IoT Edge-megoldásokat. Ebből a cikkből megtudhatja, hogyan hozhat létre, frissíthet és helyezhet üzembe a VS Code-ban egy szimulált IoT Edge-eszközön egy olyan IoT Edge-megoldást, amely átirányítja az érzékelőadatokat. 

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- A [.NET Core SDK 2.1-es](https://www.microsoft.com/net/download)
- Egy aktív IoT Hub legalább egy IoT Edge-eszközzel

A rendszerképek és tárolók felügyeletéhez szüksége lesz az Azure IoT Hub Device Explorert integráló [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) eszközre.

## <a name="create-your-iot-edge-solution"></a>Az IoT-peremhálózati megoldás létrehozása

1. A Visual Studio code, nyissa meg a integrált terminál kiválasztásával **nézet** > **integrált Terminálszolgáltatások**. 

1. A Visual STUDIO Code **parancs paletta**, adja meg, és futtassa a parancsot **Azure IoT peremhálózati: új IoT peremhálózati megoldás**. Válassza ki a munkaterületi mappát, és nevezze el a megoldást (az alapértelmezett név az EdgeSolution). Hozzon létre egy C# modult (nevű **PipeModule**) ebben a megoldásban az első felhasználó modulként. Az alapértelmezett sablon C#-modul a cső modult, amely közvetlenül kiszolgálókészletéhez a láncban üzeneteit után. Az első modulhoz meg kell adnia a Docker rendszerképadattárat is. Az alapértelmezett rendszerképadattár egy helyi Docker-tárolójegyzéken alapul (**localhost:5000/<first module name>**). Ezt módosíthatja az Azure Container Registryre vagy a Docker Hubra. 

2. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A gyökérmappa tartalmaz egy **modules** mappát, egy **.vscode** mappát és egy üzembe helyezési jegyzéksablonfájlt. A hibaelhárítási konfigurációk a .vscode mappában találhatók. Az összes felhasználói modul kódja a modules mappa alatti almappában található. A deployment.template.json fájl az üzembe helyezi jegyzék sablonja. A fájlban található paraméterek egy részének elemzése a minden modulmappában megtalálható module.json fájlból történik.

3. Adja hozzá a második modulját ehhez a megoldásprojekthez. Számos módon új modul hozzáadása aktuális megoldáshoz. Adja meg, és futtassa a parancsot **Azure IoT peremhálózati: hozzáadása IoT peremhálózati modul**. Válassza ki a frissíteni kívánt üzembe helyezési sablonfájlt. Vagy kattintson a jobb gombbal a modulok mappa, vagy kattintson a jobb gombbal a deployment.template.json fájlt, és válasszon **IoT peremhálózati modul hozzáadása**. Ezután nem lesznek egy legördülő listán jelölje be a modult. Válasszon egy **Azure Functions - C#** nevű modul **PipeFunction** és a Docker lemezképtárba. Az alapértelmezett sablon C# funkciók modul a cső modult, amely közvetlenül kiszolgálókészletéhez után a felsőbb rétegbeli üzeneteit.

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

1. A Visual STUDIO Code **parancs paletta**, adja meg, és futtassa a parancsot **Azure IoT peremhálózati: Build IoT peremhálózati megoldás**. Az egyes modulmappákban található module.json fájl alapján a parancs elkezdi buildelni, tárolóba helyezni és továbbítani az egyes modulok Docker-rendszerképét. A parancs ezután továbbítja a deployment.template.json fájlnak a szükséges értéket, és a config mappából vett információkkal létrehozza a deployment.json fájlt. A VS Code integrált terminálja megjeleníti a buildelés előrehaladását. 

2. Az Azure IoT Hub **Device Explorerben** kattintson a jobb gombbal egy IoT Edge-eszköz azonosítójára, és válassza a **Create deployment for Edge device (Üzembe helyezés létrehozása Edge-eszközhöz)** parancsot. Válassza ki a deployment.json fájlt a config mappában. A VS Code integrált terminálja megjeleníti a sikeres üzembe helyezést az üzembe helyezés azonosítójával együtt.

3. Ha egy IoT peremhálózati eszköz a fejlesztői gépen még szimulálva, figyelheti az megtekintéséhez, hogy az összes modul lemezkép tárolóra start néhány percen belül.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** parancsot. Ezután válassza az **IoT: Start monitoring D2C message (D2C üzenet monitorozásának indítása)** parancsot. 
2. Az adatok monitorozásának leállításához használja a **parancskatalógus** **IoT: Stop monitoring D2C message (D2C üzenet monitorozásának leállítása)** parancsát. 

## <a name="next-steps"></a>További lépések

További forgatókönyvek Azure IoT Edge segítségével való fejlesztéshez Visual Studio Code-ban:

* [A C# modul Visual STUDIO Code fejlesztése](how-to-develop-csharp-module.md)
* [A C# függvény Visual STUDIO Code fejlesztése](how-to-develop-csharp-function.md)
