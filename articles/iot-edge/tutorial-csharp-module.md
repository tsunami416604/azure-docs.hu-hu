---
title: Az Azure IoT peremhálózati C# modul |} Microsoft Docs
description: Hozzon létre egy IoT peremhálózati modul C#-kódban, és központilag telepítenie kell a peremhálózati eszköz
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1caa887a13453ce2b2b07e83b74f0ed57535b026
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Fejlesztés és a szimulált eszköz egy C# IoT peremhálózati modul telepítése – előzetes

Az IoT-Edge modulok, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag végigvezeti létrehozása és telepítése az IoT peremhálózati modul érzékelőadatait szűrő. A szimulált IoT peremhálózati eszköz, a központi telepítése Azure IoT peremhálózati a szimulált eszköz létrehozott fogjuk [Windows] [ lnk-tutorial1-win] vagy [Linux] [ lnk-tutorial1-lin]oktatóanyagok. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * Visual Studio Code segítségével hozzon létre egy IoT Edge-modul a .NET core 2.0 alapján
> * Visual Studio Code- és Docker segítségével hozzon létre egy docker-lemezképet, és tegye közzé a beállításjegyzék 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az IoT-Edge modul, amely ebben az oktatóanyagban létrehozhat szűrők az eszköz által létrehozott hőmérséklet adatokat. Ez csak akkor küldi el üzenetek előtt Ha hőmérséklete meghaladja a küszöbértéket. Ilyen típusú elemzés peremére akkor hasznos, ha továbbítani, és a felhőben tárolt adatok mennyisége csökkenteni. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, a gyors üzembe helyezés vagy első oktatóanyaga, amely létrehozta.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati karakterlánca.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) ugyanazon a számítógépen, amelyen a Visual Studio Code. A közösségi Edition (CE) is használhatók ehhez az oktatóanyaghoz. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.
2. Nevezze el a beállításjegyzéket, válasszon egy előfizetést, válasszon egy erőforráscsoportot, és állítsa be az **Alapszintű** termékváltozatot. 
3. Kattintson a **Létrehozás** gombra.
4. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. A Docker-lemezképet a beállításjegyzék közzétételekor, és amikor a beállításjegyzék hitelesítő adatokat ad hozzá a peremhálózati futásidejű az oktatóanyag későbbi részében fogja használni ezeket az értékeket. 

## <a name="create-an-iot-edge-module-project"></a>Az IoT-Edge modul projekt létrehozása
A következő lépéseket megjelenítése, hogyan hozzon létre egy IoT peremhálózati modult .NET alapján alapvető Visual Studio Code és az Azure IoT peremhálózati bővítmény 2.0 használatával.
1. A Visual Studio Code, válassza ki **nézet** > **integrált terminál** a Visual STUDIO Code integrált terminál megnyitásához.
2. Az integrált terminált, adja meg a következő parancs futtatásával telepítse (vagy frissítse) a **AzureIoTEdgeModule** dotnet-sablon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Hozzon létre egy projektet az új modulhoz. A következő parancs létrehozza a projektmappa **FilterModule**, és a tároló-tárház. Ha Azure-beli tárolóregisztrációs adatbázist használ, a második paraméternek a következő formátumban kell lennie: `<your container registry name>.azurecr.io`. Írja be a következő parancsot az aktuális munkakönyvtárba:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Válassza ki **fájl** > **nyissa meg a mappa**.
5. Keresse meg a **FilterModule** mappa, és kattintson **Mappaválasztás** megnyitni a projektet a Visual STUDIO Code.
6. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson **Program.cs** való megnyitásához.

   ![Nyissa meg a Program.cs][1]

7. Felső részén a **FilterModule** névtér esetén három hozzáadása `using` nyilatkozatait, később a használt:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Adja hozzá a `temperatureThreshold` változót a **Program** osztály. Ez a változó ahhoz, hogy az adatokat az IoT-központ küldendő beállítja az haladhatja meg a mért hőmérséklet értéket. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Adja hozzá a `MessageBody`, `Machine`, és `Ambient` az osztályokat a **Program** osztály. Ezeket az osztályokat határozza meg a várt séma a bejövő üzenet törzsét.

    ```csharp
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

10. Az a **Init** metódus, a kód létrehozza és konfigurálja a **DeviceClient** objektum. Ez az objektum lehetővé teszi, hogy a modul csatlakozni a helyi Azure IoT peremhálózati futtatási üzeneteket küldjön és fogadjon. A kapcsolati karakterlánc szerepel a **Init** metódus IoT peremhálózati futtatókörnyezet által biztosított modulhoz. Miután létrehozta a **DeviceClient**, a kódot a TemperatureThreshold beolvassa a modul iker kívánt tulajdonságokat, és regisztrálja a visszahívását üzenetek fogadása a peremhálózati IoT hub keresztül a **input1**végpont. Cserélje le a `SetInputMessageHandlerAsync` metódust használ egy új, és adja hozzá a `SetDesiredPropertyUpdateCallbackAsync` kívánt tulajdonságokkal frissítések metódus. Ennek a módosításnak cserélje le az utolsó sora a **Init** metódus a következő kóddal:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Property TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Adja hozzá a `onDesiredPropertiesUpdate` metódust a **Program** osztály. Ez a módszer a kívánt tulajdonságokkal frissítések kap a modul iker, és frissíti a **temperatureThreshold** felel meg a változót. Az összes modulnál megjelenik a saját modul iker, ahol konfigurálhatja a kódot közvetlenül a felhőből modul keretrendszeren belül fut.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

12. Cserélje le a `PipeMessage` metódust a `FilterMessages` metódust. Ezt a módszert nevezik, ha a modul üzenetet kap, a peremhálózati IoT hubról. Állítsa be a modul iker keresztül hőmérséklet küszöbérték alatt hőmérsékletek hoznak üzenetek kiszűri. Bővíti ezenkívül a **MessageType** tulajdonság értéke az üzenetben **riasztási**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

13. Mentse a fájlt.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker-rendszerkép létrehozása és közzététele a beállításjegyzékben

1. A VS Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   A parancsban használandó felhasználónév, jelszó és bejelentkezési kiszolgáló megkereséséhez nyissa meg az [Azure Portalt] (https://portal.azure.com). A **Minden erőforrás** területen kattintson az Azure tárolóregisztrációs adatbázis csempéjére a tulajdonságok megnyitásához, majd kattintson a **Hozzáférési kulcsok** elemre. Másolja a **Felhasználónév**, a **Jelszó** és a **Bejelentkezési kiszolgáló** mezők értékeit. 

2. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlra, és kattintson az **IoT Edge-modul Docker-rendszerképének összeállítása és leküldése** elemre. A VS Code-ablak tetején lévő előugró legördülő listájában válassza ki a tárolóplatformot: Linux-alapú tároló esetén az **amd64** Windows-alapú tároló esetén pedig a **windows-amd64** lehetőséget. Visual STUDIO Code majd összeállít a kódot, containerize a `FilterModule.dll` , és hogy a megadott tároló beállításjegyzék.


3. A VS Code integrált termináljában hozzáférhet a teljes tárolórendszerképhez címkével együtt. Az összeállítás és a leküldés meghatározásáról a `module.json` fájlban talál további információt.

## <a name="add-registry-credentials-to-edge-runtime"></a>Peremhálózati futásidejű beállításjegyzék hitelesítő adatok hozzáadása
Adja hozzá az Edge-futtatókörnyezethez a beállításjegyzék hitelesítő adatait azon a számítógépen, amelyen az Edge-eszközt futtatja. Ezek a hitelesítő adatok hozzáférést a futásidejű való lekérésére a tárolót. 

- Windowson futtassa az alábbi parancsot:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linuxon futtassa az alábbi parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>A megoldás futtatása

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT hubot.
2. Lépjen az **IoT Edge (előzetes verzió)** részhez, és válassza ki az IoT Edge-eszközt.
3. Válassza a **Modulok beállítása** lehetőséget. 
4. Ellenőrizze, hogy a **tempSensor** modul automatikusan feltöltődik értékkel. Ha nem, tegye a következőket veheti fel:
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul a többi beállítást, és kattintson a **Mentés** gombra.
5. Adja hozzá a **filterModule** modul, amely a korábbi szakaszokban létrehozott. 
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `filterModule`.
    3. A **Rendszerkép URI** mezőbe írja be a rendszerkép címét, például a következőt: `<your container registry address>/filtermodule:0.0.1-amd64`. A rendszerkép teljes címe az előző szakaszban található.
    4. Ellenőrizze a **engedélyezése** jelölőnégyzetet, hogy a modul iker szerkesztheti. 
    5. Cserélje le a JSON-t a szövegmező számára a modul iker a következő JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kattintson a **Save** (Mentés) gombra.
6. Kattintson a **Tovább** gombra.
7. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. Modulok összes üzenetet a peremhálózati futásidejű tegye közzé. A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállítja a keresztül a "input1" végpontot, amely a konfigurált végpont modulja a hőmérséklet-érzékelő üzenetek a **FilterMessages** kezelő. A második útvonal a szűrőmodulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az `upstream` egy speciális cél, amely alapján az Edge Hub az IoT Hubnak küldi az üzeneteket. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Kattintson a **Tovább** gombra.
9. A **Sablon áttekintése** lépésben kattintson a **Küldés** elemre. 
10. Térjen vissza az IoT Edge-eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Láthatja, hogy az új **filtermodule** fut, valamint a **tempSensor** modul és a **IoT peremhálózati futásidejű**. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Edge-eszközről az IoT hubra küldött, az eszközről a felhőbe irányuló üzenetek monitorozása:
1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati karakterlánccal az IoT hubhoz: 
    1. Nyissa meg a Visual STUDIO Code explorer kiválasztásával **nézet** > **Explorer**. 
    2. Kattintson a explorer **IOT HUB-ESZKÖZÖKNEK** majd **...** . Kattintson a **IoT Hub kapcsolati karakterlánc beállítása** és az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot. 

        Keresse meg a kapcsolati karakterláncot, kattintson a csempére az IoT hub, az Azure portálon, és kattintson a **megosztott elérési házirendek**. A **megosztott elérési házirendek**, kattintson a **iothubowner** házirend, és másolja az IoT-központ kapcsolati karakterlánc a **iothubowner** ablak.   

2. Az IoT hub érkező adatok figyelésére, válassza ki a **nézet** > **parancs paletta** , és keresse meg a **IoT: D2C üzenet figyelni** parancs. 
3. Az adatok figyelésének leállításához, használja a **IoT: D2C üzenet figyelés leállításának** menüparancshoz. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létre egy IoT biztonsági szűréséhez a nyers adatokat az IoT-peremhálózati eszköz által generált kódot tartalmazó modult. Be vagy más módszereket, amelyek segítségével Azure IoT peremhálózati tájékozódhat az adatok az üzleti elemzések készítése a peremhálózaton kapcsolja be az alábbi oktatóanyagok tovább.

> [!div class="nextstepaction"]
> [Azure-függvény modulként telepítése](tutorial-deploy-function.md)
> [telepítése Azure Stream Analytics modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
