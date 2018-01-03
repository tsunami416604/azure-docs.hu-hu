---
title: "Visual Studio Code segítségével egy C# modul fejlesztése az Azure IoT peremhálózati |} Microsoft Docs"
description: "Fejlesztésekor és telepítésekor egy C# modul Azure IoT oldala Visual STUDIO Code környezet közötti váltás nélkül"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Használja a Visual Studio Code fejlesztésére C# modul Azure IoT oldala
Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) való fejlesztésekor és telepítésekor az IoT-Edge modulok fő fejlesztési eszközként. 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használ. Az IoT-peremhálózati eszköz egy másik fizikai eszköz vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ellenőrizze, hogy a befejezett alábbi oktatóanyagok az Ez az útmutató megkezdése előtt.
- A szimulált eszköz Azure IoT peremhálózati telepítése [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Fejlesztés és a szimulált eszköz egy C# IoT peremhálózati modul telepítése](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Íme egy ellenőrzőlistát, amely megjeleníti az elemek előző oktatóanyag befejezése után kell rendelkeznie.

- [A Visual Studio Code](https://code.visualstudio.com/). 
- [Azure IoT Edge-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET 2.0 SDK alapvető](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7-es](https://www.python.org/downloads/)
- [Az IoT-Edge vezérlő parancsfájl](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule sablon (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Egy aktív IoT hubot legalább egy IoT peremhálózati eszköz.

Ajánlott továbbá telepítendő [Docker támogatja a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) jobb kezelése érdekében a modul lemezképek és a tárolók.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Egy Visual STUDIO Code Azure IoT Edge-modul telepítése

### <a name="list-your-iot-hub-devices"></a>Az IoT hub-eszközöknek felsorolása
Az IoT hub eszközöket a Visual STUDIO Code elemet két módja van. Kiválaszthatja, hogy mindkét módszer a folytatáshoz.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>VSCode az Azure-fiókjával bejelentkezhet, és válassza ki az IoT hub
1. A parancs paletta (F1 vagy Ctrl + Shift + P), írja be, és válassza ki **Azure: bejelentkezés**. Kattintson a  **másolási* & megnyitott** az előugró. Illessze be az (Ctrl + V) a kódot a böngészőben, és kattintson a Folytatás gombra. Ezután jelentkezzen be az Azure-fiókjával. A Visual STUDIO Code állapotsorban adatokkal tekintheti meg.
2. A parancs paletta (F1 vagy Ctrl + Shift + P), írja be, és válassza ki **IoT: válassza ki az IoT-központ**. Először válassza ki az előfizetést, amelyben létrehozta az IoT hub előző oktatóanyag. Ezután válasszon ki az IoT hub, amely tartalmazza az IoT-peremhálózati eszköz.


#### <a name="set-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterlánc beállítása
1. A parancs paletta (F1 vagy Ctrl + Shift + P), írja be, és válassza ki **IoT: állítsa be az IoT Hub kapcsolati karakterlánc**. Győződjön meg arról, illessze be a kapcsolati karakterláncot, a házirend **iothubowner** (megtalálja az IoT-központot, megosztott elérési házirendek az Azure portálon).
 

Az IoT Hub eszközöket Explorer bal oldali sávon az eszközök listája látható.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Indítsa el az IoT-Edge futásidejű és a modulok telepítése
Telepítse, és indítsa el az Azure IoT peremhálózati futásidejű az eszközön. És egy szimulált érzékelő modul, amely a telemetriai adatokat küld az IoT-központ telepítése.
1. A parancs paletta, válassza ki **peremhálózati: telepítő peremhálózati** , és válassza a IoT peremhálózati eszköz azonosítója. Vagy kattintson a jobb gombbal a peremhálózati eszköz azonosítója az eszközök listáját, és válasszon **telepítő peremhálózati**.
2. Parancs paletta, válassza ki **peremhálózati: Start peremhálózati** a peremhálózati futásidejű elindításához. Integrált terminálban megfelelő kimenetek tekintheti meg.
3. Ellenőrizze a Docker explorer a peremhálózati futásidejű állapotát. Zöld állapot azt jelenti, hogy fut-e. Az IoT-Edge futásidejű sikeresen elindult.
4. Most a peremhálózati futásidejű fut, ami azt jelenti, hogy a számítógép most a peremhálózati eszköz szimulálja. Következő lépés, amely tartja üzeneteket küld a peremhálózati eszköz egy sensorthing szimulálásához. A parancs paletta, írja be, és válassza ki **peremhálózati: peremhálózati készítése a konfigurációs fájl**. És adja meg a fájl létrehozásához. A generált deployment.json fájlban cserélje le a sor "<registry>/<image>:<tag>" rendelkező `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
5. Válassza ki **peremhálózati: peremhálózati eszköz a központi telepítés létrehozásához** , és válassza a peremhálózati eszköz azonosítója egy új központi telepítés létrehozásához. Kattintson a jobb gombbal a peremhálózati eszköz azonosítója az eszközlistában, és válassza ki **peremhálózati eszköz a központi telepítés létrehozásához**. 
6. Az IoT peremhálózati elindultak, a Docker Explorer, a szimulált érzékelő kell megjelennie. Kattintson a jobb gombbal a tárolót a Docker explorer. Minden modul docker naplók figyelemmel követheti.
7. Kattintson a jobb gombbal a peremhálózati eszköz Azonosítóját, és figyelheti a Visual STUDIO Code D2C üzeneteket.
8. Az IoT-Edge futásidejű és az érzékelő modul leállításához írja be és válassza ki **peremhálózati: leállítása peremhálózati** parancs paletta.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Fejlesztés és egy C# modul Visual STUDIO Code telepítése
Az oktatóanyagban [fejlesztésére C# modul](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), frissíti, felépítéséhez, és a modul lemezkép közzététele a Visual STUDIO Code és az Azure-portálon a C# modul telepítéséhez keresse fel. Ez a szakasz bemutatja a VS kód segítségével telepítheti és figyelheti a C# modul.

### <a name="start-a-local-docker-registry"></a>Indítsa el a helyi docker beállításjegyzék
Ebben az oktatóanyagban a Docker-kompatibilis beállításjegyzék is használhatja. Két népszerű Docker beállításjegyzék szolgáltatások érhető el a felhőben vannak [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ennek a szakasznak a [helyi Docker beállításjegyzék](https://docs.docker.com/registry/deploying/), sokkal egyszerűbb, tesztelési célú a korai fejlesztése során.
Visual STUDIO Code **integrált Terminálszolgáltatások**(Ctrl + '), futtassa a következő parancsok futtatásával kezdheti meg a helyi beállításjegyzékben.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Példa fent látható beállításjegyzék között vannak olyanok, csak teszteléshez legmegfelelőbb. Éles használatra kész beállításjegyzékbeli TLS kell védeni, és egy hozzáférés-vezérlési mechanizmus ideális kell használnia. Ajánlott [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) éles használatra kész IoT peremhálózati modulok telepítése.

### <a name="create-an-iot-edge-module-project"></a>Az IoT-Edge modul projekt létrehozása
A következő lépéseket megjelenítése, hogyan hozzon létre egy IoT peremhálózati modult .NET alapján alapvető Visual Studio Code és az Azure IoT peremhálózati bővítmény 2.0 használatával. Ha ez a szakasz korábbi oktatóprogram befejeződött, ez a szakasz biztonságosan kihagyhatja.
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


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzék

1. A Visual STUDIO Code Explorerben bontsa ki a **Docker** mappa. Majd bontsa ki a mappát, a tároló platform vagy **linux-x64** vagy **windows-nano**.
2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és kattintson a **Build IoT peremhálózati modul Docker kép**. 
3. Az a **Mappaválasztás** ablak, írja be vagy keresse meg a `./bin/Debug/netcoreapp2.0/publish`. Kattintson a **mappát adja meg a EXE_DIR**.
4. Az előugró szövegmezőben a Visual STUDIO Code ablak tetején adja meg a lemezkép nevét. Például: `<your container registry address>/filtermodule:latest`. Ha a helyi beállításjegyzékben telepít, meg kell `localhost:5000/filtermodule:latest`.
5. A Docker-tárház küldje le a lemezképet. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. Az azonos kép URL-cím használata fenti lépés szerepel.

### <a name="deploy-your-iot-edge-modules"></a>Az IoT-Edge-modulok telepítése

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
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Cserélje le a **útvonalak** szakasz az alábbi tartalom:
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ebben az oktatóanyagban kell két útvonalak. Az első útvonal szállításokkal a FilterMessages leírójú konfigurált végpont olyan üzenetek a "input1" végpont keresztül modulja a hőmérséklet-érzékelő. A második útvonal szállításokkal a modul az IoT hubhoz üzeneteit. Ez az útvonal a felsőbb rétegbeli célja egy speciális, amely közli a peremhálózati Hub üzeneteket küldhet az IoT-központ.

3. Mentse a fájlt.
4. A parancs paletta, válassza ki **peremhálózati: peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki az IoT-Edge Eszközazonosítót a központi telepítés létrehozásához. Vagy kattintson a jobb gombbal az Eszközazonosítót az eszközlistában, és válasszon **peremhálózati eszköz a központi telepítés létrehozásához**.
5. Válassza ki a `deployment.json` friss. A kimeneti ablakban megjelenik az üzembe helyezéshez megfelelő kimenetek.
6. Indítsa el a peremhálózati futásidejű parancs palettát. **Peremhálózati: Start peremhálózati**
7. A futásidejű elindultak, a Docker Explorer szimulált érzékelő és modulja IoT peremhálózati tekintheti meg.
8. Kattintson a jobb gombbal a peremhálózati eszköz Azonosítóját, és figyelheti a Visual STUDIO Code D2C üzeneteket.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy IoT-Edge-modul létrehozása, és telepítve lett a Visual STUDIO Code IoT peremhálózati eszköz. Be vagy egyéb forgatókönyvek tájékozódhat az Azure IoT peremhálózati Visual STUDIO Code fejlesztése során az alábbi oktatóanyagok tovább.

> [!div class="nextstepaction"]
> [Visual STUDIO Code modul C# hibakeresése](how-to-vscode-debug-csharp-module.md)
