---
title: "Az Azure IoT peremhálózati C# modul |} Microsoft Docs"
description: "Hozzon létre egy IoT peremhálózati modul C#-kódban, és központilag telepítenie kell a peremhálózati eszköz"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 35a38be747f7bb75ed8865dedbd446a43e5397a8
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Fejlesztés és a szimulált eszköz egy C# IoT peremhálózati modul telepítése – előzetes

Az IoT-Edge modulok, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag végigvezeti létrehozása és telepítése az IoT peremhálózati modul érzékelőadatait szűrő. A szimulált IoT peremhálózati eszköz, a központi telepítése Azure IoT peremhálózati a szimulált eszköz létrehozott fogjuk [Windows] [ lnk-tutorial1-win] vagy [Linux] [ lnk-tutorial1-lin]oktatóanyagok. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * Visual Studio Code segítségével hozzon létre egy IoT Edge-modul a .NET core 2.0 alapján
> * Visual Studio Code- és Docker segítségével hozzon létre egy docker-lemezképet, és tegye közzé a beállításjegyzék 
> * A modul az IoT-peremhálózati eszköz telepítése
> * Adatok generált megtekintése


Az IoT-Edge modul, amely ebben az oktatóanyagban létrehozhat szűrők az eszköz által létrehozott hőmérséklet adatokat. Ez csak akkor küldi el üzenetek előtt Ha hőmérséklete meghaladja a küszöbértéket. Ilyen típusú elemzés peremére akkor hasznos, ha továbbítani, és a felhőben tárolt adatok mennyisége csökkenteni. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, a gyors üzembe helyezés vagy első oktatóanyaga, amely létrehozta.
* Az IoT-peremhálózati eszköz elsődleges kulcs kapcsolati karakterláncát.  
* [A Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) ugyanazon a számítógépen, amelyen a Visual Studio Code. A közösségi Edition (CE) is használhatók ehhez az oktatóanyaghoz. 
* [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban használhatja a Visual STUDIO Code Azure IoT peremhálózati kiterjesztését olyan modul létrehozása, és hozzon létre egy **tároló kép** a fájlokból. Ezzel a lemezképpel leküldéses, majd egy **beállításjegyzék** , amely tárolja, és a képek kezeli. Végezetül telepít a lemezképet a beállításjegyzékből, futtassa az IoT-peremhálózati eszközön.  

Ebben az oktatóanyagban a Docker-kompatibilis beállításjegyzék is használhatja. Két népszerű Docker beállításjegyzék szolgáltatások érhető el a felhőben vannak [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure-tároló beállításjegyzék használja. 

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **hozzon létre egy erőforrást** > **tárolók** > **Azure tároló beállításjegyzék** .
2. Nevezze el a beállításjegyzékben, válasszon egy előfizetést, válasszon egy erőforráscsoportot és beállítása a Termékváltozat **alapvető**. 
3. Kattintson a **Létrehozás** gombra.
4. A tároló beállításkulcs létrehozása után keresse meg a fájlt, és válassza ki **hívóbetűk**. 
5. Váltás **rendszergazdai jogú felhasználó** való **engedélyezése**.
6. Másolja a **bejelentkezési kiszolgáló**, **felhasználónév**, és **jelszó**. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni. 

## <a name="create-an-iot-edge-module-project"></a>Az IoT-Edge modul projekt létrehozása
A következő lépéseket megjelenítése, hogyan hozzon létre egy IoT peremhálózati modult .NET alapján alapvető Visual Studio Code és az Azure IoT peremhálózati bővítmény 2.0 használatával.
1. A Visual Studio Code, válassza ki **nézet** > **integrált terminál** a Visual STUDIO Code integrált terminál megnyitásához.
3. Az integrált terminált, adja meg a következő parancs futtatásával telepítse (vagy frissítse) a **AzureIoTEdgeModule** dotnet-sablon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Új modul projekt létrehozása. A következő parancs létrehozza a projektmappa **FilterModule**, az aktuális munkakönyvtárban:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Válassza ki **fájl** > **nyissa meg a mappa**.
4. Keresse meg a **FilterModule** mappa, és kattintson **Mappaválasztás** megnyitni a projektet a Visual STUDIO Code.
5. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson **Program.cs** való megnyitásához.

   ![Nyissa meg a Program.cs][1]

6. Adja hozzá a `temperatureThreshold` változót a **Program** osztály. Ez a változó ahhoz, hogy az adatokat az IoT-központ küldendő beállítja az haladhatja meg a mért hőmérséklet értéket. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Adja hozzá a `MessageBody`, `Machine`, és `Ambient` az osztályokat a **Program** osztály. Ezeket az osztályokat határozza meg a várt séma a bejövő üzenet törzsét.

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

8. Az a **Init** metódus, a kód létrehozza és konfigurálja a **DeviceClient** objektum. Ez az objektum lehetővé teszi, hogy a modul csatlakozni a helyi Azure IoT peremhálózati futtatási üzeneteket küldjön és fogadjon. A kapcsolati karakterlánc szerepel a **Init** metódus IoT peremhálózati futtatókörnyezet által biztosított modulhoz. Létrehozása után a **DeviceClient**, a kód egy visszahívás üzenetek fogadása a peremhálózati IoT hub keresztül regisztrálja a **input1** végpont. Cserélje le a `SetInputMessageHandlerAsync` metódust használ egy új, és adja hozzá a `SetDesiredPropertyUpdateCallbackAsync` kívánt tulajdonságokkal frissítések metódus. Ennek a módosításnak cserélje le az utolsó sora a **Init** metódus a következő kóddal:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Adja hozzá a `onDesiredPropertiesUpdate` metódust a **Program** osztály. Ez a módszer a kívánt tulajdonságokkal frissítések kap a modul iker, és frissíti a **temperatureThreshold** felel meg a változót. Az összes modulnál megjelenik a saját modul iker, ahol konfigurálhatja a kódot közvetlenül a felhőből modul keretrendszeren belül fut.

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

10. Cserélje le a `PipeMessage` metódust a `FilterMessages` metódust. Ezt a módszert nevezik, ha a modul üzenetet kap, a peremhálózati IoT hubról. Állítsa be a modul iker keresztül hőmérséklet küszöbérték alatt hőmérsékletek hoznak üzenetek kiszűri. Bővíti ezenkívül a **MessageType** tulajdonság értéke az üzenetben **riasztási**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
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
            DeviceClient deviceClient = (DeviceClient)userContext;
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

11. A projekt létrehozásához kattintson a jobb gombbal a **FilterModule.csproj** a Explorer, majd kattintson a fájl **Build IoT peremhálózati modul**. Ez a folyamat lefordítja a modult, és exportálja a bináris és annak függőségeit a Docker-lemezkép létrehozásához használt mappába.

   ![Az IoT-Edge modul létrehozása][2]

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzék

1. A Visual STUDIO Code Explorerben bontsa ki a **Docker** mappa. Majd bontsa ki a mappát, a tároló platform vagy **linux-x64** vagy **windows-nano**.

   ![Válassza ki a Docker-tároló platform][3]

2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és kattintson a **Build IoT peremhálózati modul Docker kép**. 
3. Az a **Mappaválasztás** ablak, írja be vagy keresse meg a `./bin/Debug/netcoreapp2.0/publish`. Kattintson a **mappát adja meg a EXE_DIR**.
4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filtermodule:latest`. A tároló beállításjegyzék cím megegyezik a bejelentkezési kiszolgáló másolt a beállításjegyzékből. Meg kell formájában `<your container registry name>.azurecr.io`.
5. Jelentkezzen be a Docker a Visual STUDIO Code integrált terminálban a következő parancs beírásával: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Használja a felhasználónév, jelszó és bejelentkezési kiszolgáló másolt az Azure-tárolót beállításkulcs létrehozása után.

3. A Docker-tárház küldje le a lemezképet. Válassza ki **nézet** > **parancs paletta** , és keresse meg a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancs. A Visual STUDIO Code ablak tetején az előugró mezőben adja meg a lemezkép nevét. Használja a kép néven lépésben használt 1.d.

## <a name="add-registry-credentials-to-edge-runtime"></a>Peremhálózati futásidejű beállításjegyzék hitelesítő adatok hozzáadása
A peremhálózati futásidejű a peremhálózati eszköz futtató számítógépen adja hozzá a rendszerleíró adatbázis hitelesítő adatait. Ezek a hitelesítő adatok hozzáférést a futásidejű való lekérésére a tárolót. 

- A Windows a következő parancsot:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- A Linux a következő parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Futtassa a megoldás

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az IoT hub.
2. Ugrás a **IoT peremhálózati (előzetes verzió)** és az IoT-peremhálózati eszköz kiválasztásához.
3. Válassza ki **modulok beállítása**. 
2. Ellenőrizze, hogy a **tempSensor** modul automatikusan feltöltődik értékkel. Ha nem, tegye a következőket veheti fel:
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    2. Az a **neve** mezőbe írja be `tempSensor`.
    3. Az a **lemezkép URI** mezőbe írja be `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul az egyéb beállításokat, és kattintson **mentése**.
9. Adja hozzá a **filterModule** modul, amely a korábbi szakaszokban létrehozott. 
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    2. Az a **neve** mezőbe írja be `filterModule`.
    3. Az a **lemezkép URI** mezőbe írja be a kép címét, például `<your container registry address>/filtermodule:latest`.
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
12. Kattintson a **Tovább** gombra.
13. Az a **útvonalak megadása** . lépés:, másolja az alábbi JSON a szövegmezőbe. Modulok összes üzenetet a peremhálózati futásidejű tegye közzé. A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállítja a keresztül a "input1" végpontot, amely a konfigurált végpont modulja a hőmérséklet-érzékelő üzenetek a **FilterMessages** kezelő. A második útvonal szállításokkal a modul az IoT hubhoz üzeneteit. Ez az útvonal `upstream` egy különös cél, amely közli a peremhálózati Hub üzeneteket küldhet az IoT-központ. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Kattintson a **Tovább** gombra.
5. Az a **felülvizsgálati sablonja** lépésre, a **Submit**. 
6. Térjen vissza az IoT-peremhálózati eszköz részleteit megjelenítő oldalra, és kattintson a **frissítése**. Láthatja, hogy az új **filtermodule** fut, valamint a **tempSensor** modul és a **IoT peremhálózati futásidejű**. 

## <a name="view-generated-data"></a>Adatok generált megtekintése

A felhőbe küldött üzeneteket küld az IoT hub az IoT-peremhálózati eszköz eszköz figyelésére:
1. Az Azure IoT eszközkészlet mező konfigurálására a kapcsolati karakterlánc az IoT hub: 
    1. Nyissa meg a Visual STUDIO Code explorer kiválasztásával **nézet** > **Explorer**. 
    3. Kattintson a explorer **IOT HUB-ESZKÖZÖKNEK** majd **...** . Kattintson a **IoT Hub kapcsolati karakterlánc beállítása** és az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot. 

        Keresse meg a kapcsolati karakterláncot, kattintson a csempére az IoT hub, az Azure portálon, és kattintson a **megosztott elérési házirendek**. A **megosztott elérési házirendek**, kattintson a **iothubowner** házirend, és másolja az IoT-központ kapcsolati karakterlánc a **iothubowner** ablak.   

1. Az IoT hub érkező adatok figyelésére, válassza ki a **nézet** > **parancs paletta** , és keresse meg a **IoT: D2C üzenet figyelni** parancs. 
2. Az adatok figyelésének leállításához, használja a **IoT: D2C üzenet figyelés leállításának** menüparancshoz. 

## <a name="next-steps"></a>Következő lépések

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
