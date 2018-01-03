---
title: "Visual Studio Code segítségével fejlesztésekor és telepítésekor az Azure Functions Azure IoT szegélyhez |} Microsoft Docs"
description: "Fejlesztésekor és telepítésekor egy C# Azure Functions az Azure IoT peremhálózati Visual STUDIO Code környezet közötti váltás nélkül"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9637986d10a0e89568b2f79ede3d7b7468bb99a7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Visual Studio Code segítségével fejlesztésekor és telepítésekor az Azure Functions Azure IoT szegélyhez

Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) fejlesztésére és központi telepítése az Azure Functions IoT oldal fő fejlesztési eszközként. 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használ. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ellenőrizze, hogy a befejezett alábbi oktatóanyagok az Ez az útmutató megkezdése előtt.
- A szimulált eszköz Azure IoT peremhálózati telepítése [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Az Azure Functions üzembe helyezése](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Íme egy ellenőrzőlistát, amely megjeleníti az elemek előző oktatóanyag befejezése után kell rendelkeznie.

- [A Visual Studio Code](https://code.visualstudio.com/). 
- [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7-es](https://www.python.org/downloads/)
- [Az IoT-Edge vezérlő parancsfájl](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeFunction sablon (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Egy aktív IoT hubot legalább egy IoT peremhálózati eszköz.

Ajánlott továbbá telepítendő [Docker támogatja a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) jobb kezelése érdekében a modul lemezképek és a tárolók.

> [!NOTE]
> Jelenleg az Azure Functions IoT oldal csak támogatja a C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Az Azure IoT funkciók Visual STUDIO Code telepítése
Az oktatóanyagban [központi telepítése az Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), frissíti, felépítéséhez, és a függvény modul képek közzététele a Visual STUDIO Code és az Azure portál Azure Functions telepítéséhez keresse fel. Ez a szakasz a Visual STUDIO Code segítségével telepítheti és figyelheti az Azure Functions vezet be.

### <a name="start-a-local-docker-registry"></a>Indítsa el a helyi docker beállításjegyzék
Ebben az oktatóanyagban a Docker-kompatibilis beállításjegyzék is használhatja. Két népszerű Docker beállításjegyzék szolgáltatások érhető el a felhőben vannak [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ennek a szakasznak a [helyi Docker beállításjegyzék](https://docs.docker.com/registry/deploying/), sokkal egyszerűbb, tesztelési célú a korai fejlesztése során.
Visual STUDIO Code **integrált Terminálszolgáltatások**(Ctrl + '), futtassa a következő parancsok futtatásával kezdheti meg a helyi beállításjegyzékben.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Példa fent látható beállításjegyzék között vannak olyanok, csak teszteléshez legmegfelelőbb. Éles használatra kész beállításjegyzékbeli TLS kell védeni, és egy hozzáférés-vezérlési mechanizmus ideális kell használnia. Ajánlott [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) éles használatra kész IoT peremhálózati modulok telepítése.

### <a name="create-a-function-project"></a>Függvény-projekt létrehozása
A következő lépéseket megjelenítése, hogyan hozzon létre egy IoT peremhálózati modult .NET alapján alapvető Visual Studio Code és az Azure IoT peremhálózati bővítmény 2.0 használatával. Ha ez a szakasz korábbi oktatóprogram befejeződött, ez a szakasz biztonságosan kihagyhatja.

1. A Visual Studio Code, válassza ki **nézet** > **integrált terminál** a Visual STUDIO Code integrált terminál megnyitásához.
2. Telepíteni (vagy frissítse) a **AzureIoTEdgeFunction** dotnet, a következő parancsot a integrált terminál-sablon:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Új modul projekt létrehozása. A következő parancs létrehozza a projektmappa **FilterFunction**, az aktuális munkakönyvtárban:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Válassza ki **fájl > Nyissa meg a mappa**, majd keresse meg a **FilterFunction** mappa, és nyissa meg a projektet a Visual STUDIO Code.
5. Keresse meg a **FilterFunction** mappa, és kattintson **Mappaválasztás** megnyitni a projektet a Visual STUDIO Code.
6. A Visual STUDIO Code Explorerben bontsa ki a **EdgeHubTrigger-c Sharp** mappát, majd nyissa meg a **run.csx** fájlt.
7. Cserélje le a fájl tartalmát az alábbira:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get;set;}
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
   ```

8. Mentse a fájlt.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzék

1. A Visual STUDIO Code Explorerben bontsa ki a **Docker** mappa. Majd bontsa ki a mappát, a tároló platform vagy **linux-x64** vagy **windows-nano**.
2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és kattintson a **Build IoT peremhálózati modul Docker kép**. 
3. Keresse meg a **FilterFunction** projektmappa, és kattintson **EXE_DIR mappában válassza ki**. 
4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filterfunction:latest`. Ha a helyi beállításjegyzékben telepít, meg kell `localhost:5000/filterfunction:latest`.
5. A Docker-tárház küldje le a lemezképet. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. Az azonos kép URL-cím használata fenti lépés szerepel.

### <a name="deploy-your-function-to-iot-edge"></a>A függvény IoT peremhálózati telepítése

1. Nyissa meg a `deployment.json` fájlt, hogy lecseréli **modulok** szakasz az alábbi tartalom:
   ```json
   "tempSensor": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
         "createOptions": ""
      }
   },
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Cserélje le a **útvonalak** szakasz az alábbi tartalom:
   ```json
   {
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   }
   ```
   > [!NOTE]
   > A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállításokkal a hőmérséklet-érzékelő a szűrő függvény "input1" végpont keresztül üzeneteit, vagyis a FilterMessages leírójú konfigurált végpont. A második útvonal szállításokkal IoT-központ a szűrő függvény üzeneteket. Ez az útvonal a felsőbb rétegbeli célja egy speciális, amely közli a peremhálózati Hub üzeneteket küldhet az IoT-központ.

3. Mentse a fájlt.
4. A parancs paletta, válassza ki **peremhálózati: peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki az IoT-Edge Eszközazonosítót a központi telepítés létrehozásához. Vagy kattintson a jobb gombbal az Eszközazonosítót az eszközlistában, és válasszon **peremhálózati eszköz a központi telepítés létrehozásához**.
5. Válassza ki a `deployment.json` friss. A kimeneti ablakban megjelenik az üzembe helyezéshez megfelelő kimenetek.
6. Indítsa el a peremhálózati futásidejű parancs palettát. **Peremhálózati: Start peremhálózati**
7. Az IoT peremhálózati futásidejű elindultak, a szimulált érzékelő és szűrő függvény a Docker explorer tekintheti meg.
8. Kattintson a jobb gombbal a peremhálózati eszköz Azonosítóját, és figyelheti a Visual STUDIO Code D2C üzeneteket.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure-függvény létrehozása IoT oldal, és telepítve lett a Visual STUDIO Code IoT peremhálózati eszköz. Be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT peremhálózati Visual STUDIO Code fejlesztése során az alábbi oktatóanyagok tovább.

> [!div class="nextstepaction"]
> [Az Azure Functions Visual STUDIO Code hibakeresése](how-to-vscode-debug-azure-function.md)
