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
ms.openlocfilehash: c778c412bf6d65c5b6ee92d603aac7acfa6139eb
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Fejlesztés és a szimulált eszköz egy C# IoT peremhálózati modul telepítése – előzetes

Az IoT-Edge modulok, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag végigvezeti létrehozása és telepítése az IoT peremhálózati modul érzékelőadatait a szimulált IoT peremhálózati eszközön létrehozott tartozó telepítése Azure IoT Edge a szimulált eszköz a szűrő [Windows] [ lnk-tutorial1-win]vagy [Linux] [ lnk-tutorial1-lin] oktatóanyagok. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * Visual Studio Code segítségével hozzon létre egy IoT Edge-modul a .NET core 2.0 alapján
> * Visual Studio Code- és Docker segítségével hozzon létre egy docker-lemezképet, és tegye közzé a beállításjegyzék 
> * A modul az IoT-peremhálózati eszköz telepítése
> * Adatok generált megtekintése


Az IoT-Edge modul, amely ebben az oktatóanyagban létrehozhat szűrők az eszköz által létrehozott hőmérséklet adatokat, és csak küld üzeneteket előtt hőmérséklete meghaladja a küszöbértéket. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, amelyet a gyors üzembe helyezés vagy az oktatóanyag előző hozott létre.
* Az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az IoT-központ kapcsolati karakterláncát.  
* [A Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Telepítheti a bővítményt a Visual Studio Code bővítmények panelén.)
* [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Telepítheti a bővítményt a Visual Studio Code bővítmények panelén.)
* [Docker](https://docs.docker.com/engine/installation/). A közösségi Edition (CE) a platformhoz is elegendő ehhez az oktatóanyaghoz. Ellenőrizze, hogy a számítógépen futó, Visual STUDIO Code telepítése.
* [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Válasszon vagy regisztráljon egy Docker-beállításjegyzék
Ebben az oktatóanyagban használhatja a Visual STUDIO Code Azure IoT peremhálózati kiterjesztését olyan modul létrehozása, és hozzon létre egy [Docker kép](https://docs.docker.com/glossary/?term=image). Ezután Docker rendszerkép leküldéses egy [Docker-tárház](https://docs.docker.com/glossary/?term=repository) üzemeltet egy [Docker beállításjegyzék](https://docs.docker.com/glossary/?term=registry). Végezetül a csomagolt Docker-lemezkép központi telepítése egy [Docker-tároló](https://docs.docker.com/glossary/?term=container) az IoT-peremhálózati eszközön a beállításjegyzékből.  

Ebben az oktatóanyagban a Docker-kompatibilis beállításjegyzék is használhatja. Két népszerű Docker beállításjegyzék szolgáltatások érhető el a felhőben vannak **Azure tároló beállításjegyzék** és **Docker Hub**:

- [Az Azure tároló beállításjegyzék](https://docs.microsoft.com/en-us/azure/container-registry/) érhető el egy [előfizetéssel](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Ebben az oktatóanyagban a **alapvető** előfizetés is elegendő. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) egy szabad titkos tárházat biztosít, ha létrehoz egy (ingyenes) Docker-azonosító. 
    1. Létrehoz egy Docker-Azonosítót, kövesse az utasításokat a [egy Docker-Azonosítót regisztrálni](https://docs.docker.com/docker-id/#register-for-a-docker-id) a Docker helyen. 

    2. Hozzon létre egy saját Docker-tárházat, kövesse a [Docker hub új-tárház létrehozása](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) a Docker helyen.

Ez az oktatóanyag teljes adott esetben parancsok nyújtanak Azure tároló beállításjegyzék és a Docker központ.

## <a name="create-an-iot-edge-module-project"></a>Az IoT-Edge modul projekt létrehozása
A következő lépéseket megjelenítése, hogyan hozzon létre egy IoT peremhálózati modult .NET alapján alapvető Visual Studio Code és az Azure IoT peremhálózati bővítmény 2.0 használatával.
1. Megnyitás Visual STUDIO Code.
2. Használja a **nézet |} Terminálszolgáltatások integrált** nyissa meg a Visual STUDIO Code integrált terminál parancsra.
3. Az integrált terminált, adja meg a következő parancs futtatásával telepítse (vagy frissítse) a **AzureIoTEdgeModule** dotnet-sablon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Az integrált terminált adja meg az új modul projekt létrehozása a következő parancsot:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Ezzel a paranccsal létrejön a projektmappa **FilterModule**, az aktuális munkakönyvtárban. Ha azt szeretné, egy másik helyen létrehozásához, módosítsa a könyvtárat a parancs futtatása előtt.
 
3. Használja a **fájl |} Nyissa meg a mappa** parancs, keresse meg a **FilterModule** mappára, majd kattintson **Mappaválasztás** megnyitni a projektet a Visual STUDIO Code.
4. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson **Program.cs** való megnyitásához.
5. A következő mezőt a **Program** osztály.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. A következő osztályokat lehet felvenni a **Program** osztály. Ezeket az osztályokat határozza meg a várt séma a bejövő üzenet törzsét.

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

1. Az a **Init** metódus, a kód létrehozza és konfigurálja a **DeviceClient** objektum. Ez az objektum lehetővé teszi, hogy a modul csatlakozni a helyi Azure IoT peremhálózati futtatási üzeneteket küldjön és fogadjon. A kapcsolati karakterlánc-paraméter szerepel a **Init** metódus IoT peremhálózati futtatókörnyezet a környezeti változóban megadott modulhoz **EdgeHubConnectionString**. Létrehozása után a **DeviceClient**, a kód egy visszahívás üzenetek fogadása a peremhálózati IoT hub keresztül regisztrálja a **input1** végpont. Cserélje le a visszahívás egy új üzenetek kezelésére használt módszer, és csatolja a modul iker kívánt tulajdonságokkal a frissítések a visszahívását. Ennek a módosításnak cserélje le az utolsó sora a **Init** metódus a következő kóddal:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Adja hozzá a következő metódust a **Program** osztály frissíteni a **temperatureThreshold** mező a modul iker keresztül a háttér-szolgáltatás által küldött kívánt tulajdonságok alapján. Minden modul rendelkezik a saját modul iker. Egy modul iker lehetővé teszi, hogy a háttér-szolgáltatás beállítása a modul belül futó kódot.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. Cserélje le a **PipeMessage** metódus a következő módszerrel. Ezt a módszert nevezik, ha a modul a peremhálózati központi küld egy üzenet. Üzenetek az üzenet törzsében hőmérséklet érték alapján, és a hőmérséklet küszöbérték beállítása a modul iker keresztül szűri.

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

11. A projekt felépítése. Használja a **nézet |} Explorer** menüparancs a Visual STUDIO Code explorer megnyitásához. A explorer kattintson a jobb gombbal a **FilterModule.csproj** fájlt, és kattintson a **Build IoT peremhálózati modul** fordításához a modult, és exportálja a bináris és annak függőségeit, hogy a Docker-lemezkép létrejött egy mappába a következő lépésben.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzék

1. Összeállíthatja a Docker-lemezképét.
    1. A Visual STUDIO Code Explorerben (megoldáskezelőben) kattintson a **Docker** mappára a megnyitásához. Válassza ki a mappát, a tároló platform vagy **linux-x64** vagy **windows-nano**. 
    2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és kattintson a **Build IoT peremhálózati modul Docker kép**. 
    3. Az a **Mappaválasztás** mezőbe, vagy a Tallózás gombra, vagy adjon meg `./bin/Debug/netcoreapp2.0/publish`. Kattintson a **mappát adja meg a EXE_DIR**.
    4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például:`<docker registry address>/filtermodule:latest`; ahol *docker beállításjegyzék cím* Docker Hub használata a rendszer a Docker-Azonosítóját vagy hasonló `<your registry name>.azurecr.io`, Azure tároló beállításjegyzék használata.
 
4. Jelentkezzen be a Docker. Integrált terminált adja meg a következő parancsot: 

    - Docker Hub (adja meg a hitelesítő adatokat):
        
        ```csh/sh
        docker login
        ```

    - Az Azure-tárolót beállításjegyzék:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        A felhasználónév, jelszó és a bejelentkezési kiszolgáló, a parancs használatával megkereséséhez nyissa meg az [Azure portálra] (https://portal.azure.com). A **összes erőforrás**, kattintson az Azure-tárolót beállításjegyzék nyissa meg a tulajdonságait, majd kattintson a csempére **hívóbetűk**. Az értékek másolása a **felhasználónév**, **jelszó**, és **bejelentkezési kiszolgáló** mezőket. A bejelentkezési kiszolgáló sould a kötelező forma: `<your registry name>.azurecr.io`.

3. A Docker-tárház küldje le a lemezképet. Használja a **nézet |} Paletta... parancs |} Peremhálózati: Leküldéses IoT peremhálózati modul Docker kép** menü parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Használja a kép néven lépésben használt 1.c.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Adja hozzá a peremhálózati eszköz peremhálózati runtime beállításjegyzék hitelesítő adatokat
A peremhálózati futásidejű a peremhálózati eszköz futtató számítógépen adja hozzá a rendszerleíró adatbázis hitelesítő adatait. Ezáltal a tároló lekéréses futásidejű elérésére. 

- A Windows a következő parancsot:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- A Linux a következő parancsot:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Futtassa a megoldás

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az IoT hub.
2. Ugrás a **IoT peremhálózati (előzetes verzió)** és az IoT-peremhálózati eszköz kiválasztásához.
3. Válassza ki **modulok beállítása**. 
2. Adja hozzá a **tempSensor** modul. Ez a lépés csak akkor szükséges, ha korábban még nem telepítette a **tempSensor** modul az IoT-peremhálózati eszközön.
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    2. Az a **neve** mezőbe írja be `tempSensor`.
    3. Az a **lemezkép URI** mezőbe írja be `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul az egyéb beállításokat, és kattintson **mentése**.
9. Adja hozzá a **filtermodule**
    1. Válassza ki **IoT peremhálózati modul hozzáadása** újra.
    2. Az a **neve** mezőbe írja be `filtermodule`.
    3. Az a **kép** mezőbe írja be a kép címét, például `<docker registry address>/filtermodule:latest`.
    4. Ellenőrizze a **szerkesztés modul iker** mezőbe.
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
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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
    1. Használja a **nézet |} Explorer** menüparancs a Visual STUDIO Code explorer megnyitásához. 
    3. Kattintson a explorer **IOT HUB-ESZKÖZÖKNEK** majd **...** . Kattintson a **IoT Hub kapcsolati karakterlánc beállítása** és az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az előugró ablakban adja meg a kapcsolati karakterláncot. 

        Keresse meg a kapcsolati karakterláncot, kattintson a csempére az IoT hub, az Azure portálon, és kattintson a **megosztott elérési házirendek**. A **megosztott elérési házirendek**, kattintson a **iothubowner** házirend, és másolja az IoT-központ kapcsolati karakterlánc a **iothubowner** ablak.   

1. Az IoT hub érkező adatok figyeléséről az **nézet |} Paletta... parancs |} IoT: Figyelni D2C üzenet** menüparancshoz. 
2. Az adatok figyelésének leállításához, használja a **nézet |} Paletta... parancs |} IoT: Állítsa le a figyelő D2C üzenete** menüparancshoz. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre egy IoT biztonsági szűréséhez a nyers adatokat az IoT-peremhálózati eszköz által generált kódot tartalmazó modult. Be vagy más módszereket, amelyek segítségével Azure IoT peremhálózati tájékozódhat az adatok az üzleti elemzések készítése a peremhálózaton kapcsolja be az alábbi oktatóanyagok tovább.

> [!div class="nextstepaction"]
> [Azure-függvény modulként telepítése](tutorial-deploy-function.md)
> [telepítése Azure Stream Analytics modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
