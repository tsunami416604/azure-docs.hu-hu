---
title: Azure-függvény üzembe helyezése az Azure IoT Edge használatával | Microsoft Docs
description: Azure-függvény üzembe helyezése modulként Edge-eszközre
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f1c6b5cd07752c6b29234a365b3298d76b639b3a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Azure-függvény üzembe helyezése IoT Edge-modulként
Az Azure Functions használatával olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure-függvény létrehozásán és üzembe helyezésén, amely érzékelőadatokat szűr az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-tutorial1-win]vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközön. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:     

> [!div class="checklist"]
> * Azure-függvény létrehozása a Visual Studio Code használatával
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a VS Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott Azure-függvény szűri az eszköze által létrehozott hőmérsékletadatokat, és csak akkor küld üzeneteket az Azure IoT Hubra, amikor a hőmérséklet egy megadott küszöbérték felett van. 

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban vagy az előző oktatóanyagban létrehozott Azure IoT Edge-eszköz.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). A platform Community Edition (CE) kiadása elegendő ehhez az oktatóanyaghoz. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.
2. Nevezze el a beállításjegyzéket, válasszon egy előfizetést, válasszon egy erőforráscsoportot, és állítsa be az **Alapszintű** termékváltozatot. 
3. Kattintson a **Létrehozás** gombra.
4. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása
A következő lépések azt mutatják be, hogyan hozhat létre IoT Edge-függvényt a Visual Studio Code és az Azure IoT Edge bővítmény használatával.
1. Nyissa meg a Visual Studio Code-ot.
2. A VS Code integrált termináljának megnyitásához válassza a **Nézet** > **Integrált terminál** elemet.
3. Az **AzureIoTEdgeFunction** sablon .NET-ben való telepítéséhez (vagy frissítéséhez) futtassa a következő parancsot az integrált terminálon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Hozzon létre egy projektet az új modulhoz. A következő parancs létrehozza a **FilterFunction** nevű projektmappát a tárolóadattárral együtt. Ha Azure-beli tárolóregisztrációs adatbázist használ, a második paraméternek a következő formátumban kell lennie: `<your container registry name>.azurecr.io`. Írja be a következő parancsot az aktuális munkakönyvtárba:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Válassza a **Fájl** > **Mappa megnyitása** elemet, majd keresse meg a **FilterFunction** mappát, és nyissa meg a projektet a VS Code-ban.
4. A VS Code Explorerben bontsa ki az **EdgeHubTrigger-Csharp** mappát, majd nyissa meg a **run.csx** fájlt.
5. Cserélje le a fájl tartalmát a következő kódra:

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
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
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

11. Mentse a fájlt.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker-rendszerkép létrehozása és közzététele a beállításjegyzékben

1. A VS Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   A parancsban használandó felhasználónév, jelszó és bejelentkezési kiszolgáló megkereséséhez nyissa meg az [Azure Portalt] (https://portal.azure.com). A **Minden erőforrás** területen kattintson az Azure tárolóregisztrációs adatbázis csempéjére a tulajdonságok megnyitásához, majd kattintson a **Hozzáférési kulcsok** elemre. Másolja a **Felhasználónév**, a **Jelszó** és a **Bejelentkezési kiszolgáló** mezők értékeit. 

2. Nyissa meg a **module.json** fájlt. A `"version"` elemet frissítheti pl. **„1.0”** értékre. Azon adattár neve is látható, amelyet a `dotnet new aziotedgefunction` `-r` paraméterében megadott.

3. Mentse a **module.json** fájlt.

4. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlra, és kattintson az **IoT Edge-modul Docker-rendszerképének összeállítása és leküldése** elemre. A VS Code-ablak tetején lévő előugró legördülő listájában válassza ki a tárolóplatformot: Linux-alapú tároló esetén az **amd64** Windows-alapú tároló esetén pedig a **windows-amd64** lehetőséget. A VS Code ezután tárolókba helyezi a függvénykódokat, majd leküldi őket a megadott tárolóregisztrációs adatbázisba.

5. A VS Code integrált termináljában hozzáférhet a teljes tárolórendszerképhez címkével együtt. Az összeállítás és a leküldés meghatározásáról a `module.json` fájlban talál további információt.

## <a name="add-registry-credentials-to-your-edge-device"></a>Beállításjegyzékhez tartozó hitelesítő adatok hozzáadása az Edge-eszközhöz
Adja hozzá az Edge-futtatókörnyezethez a beállításjegyzék hitelesítő adatait azon a számítógépen, amelyen az Edge-eszközt futtatja. Ez hozzáférést nyújt a futtatókörnyezetnek a tároló lekéréséhez. 

- Windowson futtassa az alábbi parancsot:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linuxon futtassa az alábbi parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>A megoldás futtatása

1. Az **Azure Portalon** keresse meg az IoT hubot.
2. Lépjen az **IoT Edge (előzetes verzió)** részhez, és válassza ki az IoT Edge-eszközt.
1. Válassza a **Modulok beállítása** lehetőséget. 
2. Ha már telepítette a **tempSensor** modult erre az eszközre, akkor lehet, hogy automatikusan ki van töltve. Ha még nem telepítette, a következő lépésekkel adhatja hozzá:
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul a többi beállítást, és kattintson a **Mentés** gombra.
1. Adja hozzá a **filterFunction** modult.
    1. Válassza ismét az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `filterFunction`.
    3. A **Rendszerkép URI** mezőbe írja be a rendszerkép címét, például a következőt: `<your container registry address>/filterfunction:0.0.1-amd64`. A rendszerkép teljes címe az előző szakaszban található.
    74. Kattintson a **Save** (Mentés) gombra.
2. Kattintson a **Tovább** gombra.
3. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. Az első útvonal a hőmérséklet-érzékelőből a szűrőmodulba szállítja az üzeneteket az input1 végponton keresztül. A második útvonal a szűrőmodulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az `$upstream` egy speciális cél, amely alapján az Edge Hub az IoT Hubnak küldi az üzeneteket. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Kattintson a **Tovább** gombra.
5. A **Sablon áttekintése** lépésben kattintson a **Küldés** elemre. 
6. Térjen vissza az IoT Edge-eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Látható, hogy az új **filterfunction** modul fut a **tempSensor** modullal és az **IoT Edge-futtatókörnyezettel** együtt. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Edge-eszközről az IoT hubra küldött, az eszközről a felhőbe irányuló üzenetek monitorozása:
1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati karakterlánccal az IoT hubhoz: 
    1. Az Azure Portalon keresse meg az IoT hubot, és válassza a **Megosztott elérési szabályzatok** elemet. 
    2. Válassza az **iothubowner** elemet, majd másolja a **Kapcsolati karakterlánc – elsődleges kulcs** értékét.
    3. A VS Code Explorerben kattintson az **IOT HUB-ESZKÖZÖK** elemre, majd kattintson a **...** elemre. 
    4. Válassza az **IoT Hub kapcsolati karakterlánc beállítása** elemet, és írja be az IoT Hub kapcsolati karakterláncát az előugró ablakba. 

2. Az IoT Hubra érkező adatok monitorozásához válassza a **Nézet** > **Parancskatalógus...**  elemet, és keressen rá az **IoT: D2C üzenet monitorozásának megkezdése** kifejezésre. 
3. Az adatok monitorozásának leállításához használja a Parancskatalógus **IoT: D2C üzenet monitorozásának leállítása** parancsát. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó Azure-függvényt hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Az Azure IoT Edge felfedezésének folytatásához ismerje meg, hogyan használhatja az IoT Edge-eszközöket átjáróként. 

> [!div class="nextstepaction"]
> [IoT Edge-átjáróeszköz létrehozása](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
