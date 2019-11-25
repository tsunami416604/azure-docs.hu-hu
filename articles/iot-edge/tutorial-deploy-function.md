---
title: Tutorial deploy an Azure function to a device - Azure IoT Edge | Microsoft Docs
description: In this tutorial, you develop an Azure function as an IoT Edge module, then deploy it to an edge device.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1db64c2cc68dc9c47d4f2cf5f63eb0667907d737
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456717"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Deploy Azure functions as IoT Edge modules

Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az Azure IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő Azure-függvény létrehozásának és a szimulált IoT Edge-eszközön való üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-függvény létrehozása a Visual Studio Code használatával.
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a VS Code és a Docker használatával.
> * A modul üzembe helyezése egy tárolóregisztrációs adatbázisból az IoT Edge-eszközön.
> * Szűrt adatok megtekintése.

<center>

![Diagram - Tutorial architecture, stage and deploy function module](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Az Azure Function moduljai [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhetők el az Azure IoT Edge szolgáltatásban. 

Az ebben az oktatóanyagban létrehozott Azure-függvény szűri az eszköze által létrehozott hőmérsékletadatokat. A függvény csak akkor küld felfelé irányuló üzeneteket az Azure IoT Hubra, amikor a hőmérséklet egy megadott küszöbérték felett van. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in with Azure Functions, install the following additional prerequisites on your development machine: 

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

The Azure IoT Tools for Visual Studio Code that you installed in the prerequisites provides management capabilities as well as some code templates. Ebben a szakaszban a Visual Studio Code használatával készíthet Azure-függvényt tartalmazó IoT Edge-megoldást. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Create a C# Function solution template that you can customize with your own code.

1. Nyissa meg a Visual Studio Code-ot a fejlesztői gépen.

2. A **View (Nézet)**  > **Command Palette (Parancskatalógus)** elem kiválasztásával nyissa meg a VS Code parancskatalógusát.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Value (Díj) |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Enter a descriptive name for your solution, like **FunctionSolution**, or accept the default. |
   | Select module template (Modulsablon kiválasztása) | Choose **Azure Functions - C#** . |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpFunction** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre fel van töltve az előző lépésből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. The final string looks like \<registry name\>.azurecr.io/CSharpFunction. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop C modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub.

1. In Visual Studio Code, open **modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Replace the contents of the **CSharpFunction.cs** file with the following code. This code receives telemetry about ambient and machine temperature, and only forwards the message on to IoT Hub if the machine temperature is above a defined threshold.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.       
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

1. Mentse a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **CSharpFunction** függvényhez, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet alacsonyabb az elfogadható határértéknél. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

In this section, you provide the credentials for your container registry for the second time (the first was in the **.env** file of your IoT Edge solution) by signing in locally from your development machine so that Visual Studio Code can push images to your registry.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet. 

2. Az integrált terminálban az alábbi paranccsal jelentkezzen be a tárolóregisztrációs adatbázisba. Használja azt a felhasználónevet és bejelentkezési kiszolgálót, amelyet az Azure Container Registry-adatbázisból másolt ki korábban.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    When you're prompted for the password, paste the password (it won't be visible in the terminal window) for your container registry and press **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

When you tell Visual Studio Code to build your solution, it first takes the information in the deployment template and generates a deployment.json file in a new folder named **config**. Then it runs two commands in the integrated terminal: `docker build` and `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a függvényeket, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

## <a name="view-your-container-image"></a>A tárolórendszerkép megtekintése

A Visual Studio Code sikert jelző üzenetet küld, miután leküldte a tárolórendszerképet a tárolóregisztrációs adatbázisba. Ha személyesen is ellenőrizni szeretné a műveletet, keresse meg a rendszerképet az adatbázisban. 

1. Az Azure Portalon tallózzon az Azure Container Registry-adatbázisra. 
2. Válassza az **Adattárak** elemet.
3. Ekkor a **csharpfunction** adattár jelenik meg a listában. Válassza ki az adattárat a további részletek megtekintéséhez.
4. A **Címkék** szakaszban megjelenik a **0.0.1-amd64** címke. Ez a címke jelzi a létrehozott rendszerkép verziószámát és platformját. Ezek az értékek a CSharpFunction mappa module.json fájljában találhatók meg. 

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A függvények modulját a gyors útmutatókban bemutatott módon az Azure Portal segítségével helyezheti üzembe az IoT Edge-eszközökön. A modulokat emellett a Visual Studio Code használatával is üzembe helyezheti és monitorozhatja. The following sections use the Azure IoT Tools for VS Code that was listed in the prerequisites. Ha még nem tette meg, telepítse most a bővítményt. 

1. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for single device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

3. Navigáljon a **CSharpFunction** függvényt tartalmazó megoldásmappához. Open the config folder, select the **deployment.json** file, and then choose **Select Edge Deployment Manifest**.

4. Frissítse az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. You should see the new **CSharpFunction** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**. It may take a few moments for the new modules to show up. Your IoT Edge device has to retrieve its new deployment information from IoT Hub, start the new containers, and then report the status back to IoT Hub. 

   ![Üzembe helyezett modulok megtekintése a VS Code-ban](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

You can see all of the messages that arrive at your IoT hub by running **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** in the command palette.

A nézetet szűrheti is, hogy csak a megjelölt eszközről az IoT Hub-központra érkező üzenetek jelenjenek meg. Right-click the device in the **Azure IoT Hub Devices** section and select **Start Monitoring Built-in Event Endpoint**.

To stop monitoring messages, run the command **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** in the command palette. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-függvénymodult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. When you're ready to build your own modules, you can learn more about how to [Develop with Azure IoT Edge for Visual Studio Code](how-to-vs-code-develop-module.md). 

Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Átlagértékek keresése lebegő Azure Stream Analytics-ablakban](tutorial-deploy-stream-analytics.md)
