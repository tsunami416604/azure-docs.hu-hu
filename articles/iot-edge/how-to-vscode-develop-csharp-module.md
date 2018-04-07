---
title: Visual Studio Code segítségével egy C# modul fejlesztése az Azure IoT peremhálózati |} Microsoft Docs
description: Fejleszthet, és egy C# modul központi telepítése a Visual Studio Code Azure IoT szegélyt környezet közötti váltás nélkül.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 48c6cacebdeb7505c8dc2bcaed099c33862589ac
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Visual Studio Code használata egy C# modul fejlesztéséhez Azure IoT oldala
Ez a cikk részletes utasításokat biztosít [Visual Studio Code](https://code.visualstudio.com/) fejlesztésére és központi telepítése az Azure IoT peremhálózati modulok fő fejlesztési eszközként. 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy számítógép vagy a fejlesztői számítógépén, a Windows vagy Linux rendszerű virtuális gép használja. Az IoT-peremhálózati eszköz egy másik fizikai eszköz lehet, vagy az IoT-peremhálózati eszköz szimulálhatja a fejlesztési számítógépén.

Ez az útmutató megkezdése előtt hajtsa végre az alábbi oktatóanyagok:
- A szimulált eszköz Azure IoT peremhálózati telepítése [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) vagy [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Fejlesztés és a szimulált eszköz egy C# IoT peremhálózati modul telepítése](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Íme egy ellenőrzőlistát, amely megjeleníti az elemek a fenti oktatóanyag befejezése után kell rendelkeznie:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [A Visual Studio Code Azure IoT Edge-bővítményt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# (OmniSharp technológiával) Visual Studio Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [Az IoT-Edge vezérlő parancsfájl](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule sablon (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Legalább egy IoT peremhálózati eszköz egy aktív IoT hubot

Akkor is célszerű telepíteni [Docker támogatja a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) jobb kezelése érdekében a modul lemezképek és a tárolók.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Egy Visual STUDIO Code Azure IoT Edge-modul telepítése

### <a name="list-your-iot-hub-devices"></a>Az IoT hub-eszközöknek felsorolása
Az IoT hub-eszközök Visual STUDIO Code elemet két módja van. Kiválaszthatja, hogy mindkét módszer a folytatáshoz.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Jelentkezzen be az Azure-fiókjával a Visual STUDIO Code, és válassza ki az IoT hub
1. Írja be a parancs paletta (F1 vagy Ctrl + Shift + P), és válassza ki **Azure: bejelentkezés**. Válassza ki **nyissa meg a & másolása**. Illessze be az (Ctrl + V) a kódot a böngészőben, és válassza ki **Folytatás**. Jelentkezzen be az Azure-fiókjával. A fiók adatait, a Visual STUDIO Code állapotsorban tekintheti meg.
2. Írja be a parancs paletta, és válassza ki **IoT: válassza ki az IoT-központ**. Először is válassza ki az előfizetést, amelyben létrehozta az IoT hub az előző oktatóanyag. Ezután válassza az IoT hub, amely tartalmazza az IoT-peremhálózati eszköz.

    ![Képernyőkép a eszközök listája](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterlánc beállítása
Írja be a parancs paletta, és válassza ki **IoT: állítsa be az IoT Hub kapcsolati karakterlánc**. Győződjön meg arról, illessze be a kapcsolati karakterláncot, a házirend **iothubowner**. (Megtalálja a megosztott elérési házirendek az Azure-portálon az IoT hub.)
 
Az oldalsó sávon a bal oldali IoT Hub eszközöket Explorer, az eszközök listája látható.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Indítsa el az IoT-Edge futásidejű és a modulok telepítése
Telepítse, és indítsa el az Azure IoT peremhálózati futásidejű az eszközön. A szimulált érzékelő modul, amely a telemetriai adatokat küld az Azure IoT-központ telepítése.
1. A parancs paletta, válassza ki **peremhálózati: telepítő peremhálózati** , és válassza a IoT peremhálózati eszköz azonosítója. Másik lehetőségként kattintson a jobb gombbal az IoT-peremhálózati eszköz azonosítója a **eszközök listája**, és válassza ki **telepítő peremhálózati**.

    ![Képernyőfelvétel a telepítő peremhálózati futásidejű](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. A parancs paletta, válassza ki **peremhálózati: Start peremhálózati** az IoT-Edge futásidejű elindításához. Az integrált terminálban megfelelő kimenetek tekintheti meg.

    ![Peremhálózati Start képernyőkép futásidejű](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Ellenőrizze a Docker Explorer IoT peremhálózati futásidejű állapotát. Zöld állapot azt jelenti, hogy fut-e, és az IoT-Edge futásidejű sikeresen elindult. A számítógép most szimulálja IoT peremhálózati eszköz.

    ![Képernyőfelvétel a peremhálózati futási állapota](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Szimulálja, amely az IoT-peremhálózati eszközön üzenetküldésre tartja érzékelő. Írja be a parancs paletta, és válassza ki **peremhálózati: peremhálózati készítése a konfigurációs fájl**. Válasszon egy mappát a fájl létrehozásához. A generált deployment.json fájlban cserélje le a tartalmat `<registry>/<image>:<tag>` rendelkező `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`, és mentse a fájlt.

    ![Képernyőkép a érzékelő modul](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Válassza ki **peremhálózati: peremhálózati eszköz a központi telepítés létrehozásához**, és válassza ki az IoT él Eszközazonosító egy új központi telepítés létrehozásához. Másik lehetőségként kattintson a jobb gombbal az IoT-Edge Eszközazonosítót az eszközlistában, és válassza **peremhálózati eszköz a központi telepítés létrehozásához**. 

6. Az IoT peremhálózati elindultak, a Docker Explorer, a szimulált érzékelő kell megjelennie. Kattintson a jobb gombbal a tárolót a Docker Explorer. Minden modul Docker naplók figyelemmel követheti. A Modullista is, megtekintheti az eszközök listáját.

    ![Modullista képernyőképe](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Kattintson a jobb gombbal a IoT peremhálózati eszköz azonosítója, és figyelheti a Visual STUDIO Code D2C üzeneteket.
8. Az IoT-Edge futásidejű és az érzékelő modul leállításához írja be, és válassza ki **peremhálózati: leállítása peremhálózati** a parancs paletta.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Fejlesztés és egy C# modul Visual STUDIO Code telepítése
Az oktatóanyag [fejlesztésére C# modul](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), frissítése, elkészítéséhez és a modul lemezkép közzététele a Visual STUDIO Code. Ezután lépjen az Azure portálra a C# modul telepítése. Ez a szakasz bemutatja a VS kód segítségével telepítheti és figyelheti a C# modul.

### <a name="start-a-local-docker-registry"></a>Indítsa el a helyi Docker-beállításjegyzék
Ez a cikk a Docker-kompatibilis beállításjegyzék használható. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ennek a szakasznak a [helyi Docker beállításjegyzék](https://docs.docker.com/registry/deploying/), sokkal egyszerűbb, tesztelési a korai fejlesztése során.
A Visual STUDIO Code **integrált Terminálszolgáltatások** (Ctrl + '), a helyi beállításjegyzékben elindítani a következő parancsot:  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Ez a példa bemutatja, amelyek csak teszteléshez legmegfelelőbb beállításjegyzék-konfigurációk. Egy éles használatra kész beállításjegyzék kell védeni a TLS és az ideális esetben használjon egy hozzáférés-vezérlési mechanizmus. Ajánlott [Azure tároló beállításjegyzék](https://docs.microsoft.com/azure/container-registry/) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) éles használatra kész IoT peremhálózati modulok telepítése.

### <a name="create-an-iot-edge-module-project"></a>Az IoT-Edge modul projekt létrehozása
A következő lépések bemutatják egy Visual Studio Code és az Azure IoT peremhálózati bővítmény használatával a .NET Core 2.0 alapján IoT Edge-modul létrehozása. Ha ez a szakasz az előző oktatóanyag befejeződött, ez a szakasz biztonságosan kihagyhatja.
1. A Visual Studio Code, válassza ki **nézet** > **integrált terminál** a Visual STUDIO Code integrált terminál megnyitásához.
3. Az integrált terminált, adja meg a következő parancs futtatásával telepítse (vagy frissítse) a **AzureIoTEdgeModule** dotnet-sablon:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Hozzon létre egy projektet az új modulhoz. A következő parancs létrehozza a projektmappa **FilterModule**, az aktuális munkakönyvtárban:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Válassza ki **fájl** > **nyissa meg a mappa**.
4. Keresse meg a **FilterModule** mappára, majd kattintson **Mappaválasztás** nyissa meg a projektet a Visual STUDIO Code számára.
5. Válassza ki a Visual STUDIO Code Intézőben **Program.cs** való megnyitásához. Felső részén **program.cs**, a következő névterek tartalmazza:
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

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

8. Az a **Init** metódus, a kód létrehozza és konfigurálja a **DeviceClient** objektum. Ez az objektum lehetővé teszi, hogy a modul csatlakozni a helyi IoT peremhálózati futtatási üzeneteket küldjön és fogadjon. Az IoT-Edge futásidejű a kapcsolati karakterlánc szerepel a modul számára biztosított a **Init** metódust. Létrehozása után a **DeviceClient** objektum, a kód egy visszahívás üzenetek fogadása a peremhálózati IoT hub keresztül regisztrálja a **input1** végpont. Cserélje le a `SetInputMessageHandlerAsync` metódust használ egy új, és adja hozzá a `SetDesiredPropertyUpdateCallbackAsync` kívánt tulajdonságokkal frissítések metódus. Ennek a módosításnak cserélje le az utolsó sora a **Init** metódus a következő kóddal:

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

10. Cserélje le a `PipeMessage` metódust a `FilterMessages` metódust. Ezt a módszert nevezik, ha a modul üzenetet kap, a peremhálózati IoT-központ. Állítsa be a modul iker keresztül hőmérséklet küszöbérték alatt hőmérsékletek hoznak üzenetek kiszűri. Bővíti ezenkívül a **MessageType** tulajdonság értéke az üzenetben **riasztási**. 

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

11. A projekt létrehozásához kattintson a jobb gombbal a **FilterModule.csproj** Explorer fájlt, és válassza ki **Build IoT peremhálózati modul**. Ez a folyamat lefordítja a modult, és exportálja a bináris és annak függőségeit a Docker-lemezkép létrehozásához használt mappába. 

    ![Képernyőfelvétel a Visual STUDIO Code Explorer](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzék

1. A Visual STUDIO Code Explorerben bontsa ki a **Docker** mappa. Ezután bontsa ki a tárolóplatform mappáját, amely a **linux-x64** vagy a **windows-nano** mappa.
2. Kattintson a jobb gombbal a **Dockerfile** fájlt, és válassza ki **Build IoT peremhálózati modul Docker kép**. 

    ![Képernyőfelvétel a Visual STUDIO Code Explorer](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. Az a **Mappaválasztás** ablak, írja be vagy keresse meg a `./bin/Debug/netcoreapp2.0/publish`. Válassza ki **mappát adja meg a EXE_DIR**.
4. A VS Code-ablak tetején lévő előugró szövegmezőbe írja be a rendszerkép nevét. Például: `<your container registry address>/filtermodule:latest`. Ha a helyi beállításjegyzékben telepít, meg kell `localhost:5000/filtermodule:latest`.
5. Küldje le a rendszerképet a Docker-adattárba. Használja a **peremhálózati: leküldéses IoT peremhálózati modul Docker kép** parancsot, és az előugró mezőben a Visual STUDIO Code ablak tetején adja meg a kép URL-címe. A kép URL-CÍMÉRE az előző lépésben használt használja. A konzol naplóban ellenőrizze, hogy a kép sikeresen leküldve.

    ![Képernyőkép a Docker lemezkép terjesztése a](./media/how-to-vscode-develop-csharp-module/push-image.png) ![konzolbeli naplóit képernyőképe](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Az IoT-Edge-modulok telepítése

1. Nyissa meg a `deployment.json` fájlt, és cserélje le a **modulok** a következő szakaszt:
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

2. Cserélje le a **útvonalak** a következő szakaszt:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > A futásidejű deklaratív szabályok határozzák meg, ahol az üzenetek áramlását. Ez a cikk két útvonalak kell. Az első útvonal a hőmérséklet-érzékelőből a szűrőmodulba szállítja az üzeneteket az input1 végponton keresztül. Ez az a FilterMessages leírójú konfigurált végpont. A második útvonal a szűrőmodulból az IoT Hubra szállítja az üzeneteket. Ez az útvonal a felsőbb rétegbeli célja egy speciális, amely közli az IoT-Edge központ üzeneteket küldhet az IoT-központ.

3. Mentse a fájlt.
4. Válassza ki a parancs paletta **peremhálózati: peremhálózati eszköz a központi telepítés létrehozásához**. Válassza ki az IoT-Edge Eszközazonosítót a központi telepítés létrehozásához. Vagy kattintson a jobb gombbal az Eszközazonosítót az eszközlistában, és válassza ki **peremhálózati eszköz a központi telepítés létrehozásához**.

    ![Képernyőfelvétel a létrehozása központi telepítési lehetőség](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Válassza ki a `deployment.json` friss. A kimeneti ablakban megjelenik az üzembe helyezéshez megfelelő kimenetek.

    ![Képernyőkép a kimeneti ablakban](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Indítsa el az IoT-Edge futásidejű a parancs paletta (válasszon **peremhálózati: Start peremhálózati**).
7. A futásidejű elindultak, a Docker Explorer szimulált érzékelő és modulja IoT peremhálózati tekintheti meg.

    ![Képernyőkép a Docker Explorer](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Kattintson a jobb gombbal a IoT peremhálózati eszköz azonosítója, és figyelheti a Visual STUDIO Code D2C üzeneteket.


## <a name="next-steps"></a>További lépések

[Visual STUDIO Code modul C# hibakeresése](how-to-vscode-debug-csharp-module.md)
