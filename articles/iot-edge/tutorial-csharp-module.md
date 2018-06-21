---
title: Azure IoT Edge C#-modul | Microsoft Docs
description: IoT Edge-modul létrehozása C#-kóddal, majd üzembe helyezése egy peremhálózati eszközön
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1da3a246a2ad33a4563f491058f5d4d115f3954d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631071"
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>C#-alapú IoT Edge-modul fejlesztése és üzembe helyezése szimulált eszközön – bemutató

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-tutorial1-win] vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * .NET core 2.0-alapú IoT Edge-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban vagy az első oktatóanyagban létrehozott Azure IoT Edge-eszköz.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) ugyanazon a számítógépen, amelyre telepítve van a Visual Studio Code. A Community Edition (CE) kiadás elegendő ehhez az oktatóanyaghoz. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Azure Container Registry** elemet.
2. Nevezze el a beállításjegyzéket, válasszon egy előfizetést, válasszon egy erőforráscsoportot, és állítsa be az **Alapszintű** termékváltozatot. 
3. Kattintson a **Létrehozás** gombra.
4. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet. 
5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni, amikor közzéteszi a Docker-rendszerképet a beállításjegyzékben, és amikor a beállításjegyzékhez tartozó hitelesítő adatokat ad hozzá az Edge-futtatókörnyezethez. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
A következő lépések azt mutatják be, hogyan hozhat létre egy .NET core 2.0-alapú IoT Edge-modult a Visual Studio Code és az Azure IoT Edge bővítmény használatával.
1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.
2. Az integrált terminálban írja be a következő parancsot az **AzureIoTEdgeModule** sablon dotnetben való telepítéséhez (vagy frissítéséhez):

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Hozzon létre egy projektet az új modulhoz. A következő parancs létrehozza a **FilterModule** nevű projektmappát a tárolóadattárral együtt. Ha Azure-beli tárolóregisztrációs adatbázist használ, a második paraméternek a következő formátumban kell lennie: `<your container registry name>.azurecr.io`. Írja be a következő parancsot az aktuális munkakönyvtárba:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Válassza a **File** > **Open Folder** (Fájl, Mappa megnyitása) elemet.
5. Lépjen a **FilterModule** mappához, és kattintson a **Select Folder** (Mappa kiválasztása) elemre a projekt VS Code-ban való megnyitásához.
6. A VS Code Explorerben kattintson a **Program.cs** elemre a megnyitásához.

   ![Program.cs megnyitása][1]

7. A **FilterModule** névtér tetején adjon hozzá három `using` utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Adja hozzá a `temperatureThreshold` változót a **Program** osztályhoz. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Adja hozzá a `MessageBody`, `Machine` és az `Ambient` változót a **Program** osztályhoz. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

10. Az **Init** metódus használata esetén a kód létrehoz és konfigurál egy **DeviceClient** objektumot. Ez az objektum lehetővé teszi a modul helyi Azure IoT Edge-futtatókörnyezethez való csatlakozását üzenetek küldése és fogadása céljából. Az **Init** metódusban használt kapcsolati sztringet az IoT Edge-futtatókörnyezet biztosítja a modulnak. A **DeviceClient** objektum létrehozása után a kód beolvassa a TemperatureThreshold változót a modul ikerdokumentumának kívánt tulajdonságaiból, és visszahívást regisztrál üzenetek fogadásához az IoT Edge-központból az **input1** végponton keresztül. Cserélje le a `SetInputMessageHandlerAsync` metódust egy újra, és adjon hozzá egy `SetDesiredPropertyUpdateCallbackAsync` metódust a kívánt tulajdonságfrissítések végrehajtásához. A módosítás végrehajtásához cserélje le az **Init** metódus utolsó sorát az alábbi kódra:

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

11. Adja hozzá az `onDesiredPropertiesUpdate` metódust a **Program** osztályhoz. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

12. Cserélje le a `PipeMessage` metódust a `FilterMessages` metódusra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket. Ezenkívül a **MessageType** tulajdonságot is hozzáadja az üzenethez, amelynek értéke **Alert** (Figyelmeztetés). 

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

13. Mentse el ezt a fájlt.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker-rendszerkép létrehozása és közzététele a beállításjegyzékben

1. A VS Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   A parancsban használandó felhasználónév, jelszó és bejelentkezési kiszolgáló megkereséséhez nyissa meg az [Azure Portalt] (https://portal.azure.com). A **Minden erőforrás** területen kattintson az Azure tárolóregisztrációs adatbázis csempéjére a tulajdonságok megnyitásához, majd kattintson a **Hozzáférési kulcsok** elemre. Másolja a **Felhasználónév**, a **Jelszó** és a **Bejelentkezési kiszolgáló** mezők értékeit. 

2. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlra, és kattintson az **IoT Edge-modul Docker-rendszerképének összeállítása és leküldése** elemre. A VS Code-ablak tetején lévő előugró legördülő listájában válassza ki a tárolóplatformot: Linux-alapú tároló esetén az **amd64** Windows-alapú tároló esetén pedig a **windows-amd64** lehetőséget. A VS Code ezután felépíti a kódot, tárolókba helyezi a `FilterModule.dll` elemet, majd leküldi a megadott tárolóregisztrációs adatbázisba.


3. A VS Code integrált termináljában hozzáférhet a teljes tárolórendszerképhez címkével együtt. Az összeállítás és a leküldés meghatározásáról a `module.json` fájlban talál további információt.

## <a name="add-registry-credentials-to-edge-runtime"></a>Beállításjegyzékhez tartozó hitelesítő adatok hozzáadása az Edge-futtatókörnyezethez
Adja hozzá az Edge-futtatókörnyezethez a beállításjegyzék hitelesítő adatait azon a számítógépen, amelyen az Edge-eszközt futtatja. Ezek a hitelesítő adatok hozzáférést nyújtanak a futtatókörnyezetnek a tároló lekéréséhez. 

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
4. Ellenőrizze, hogy a rendszer automatikusan feltölti-e adatokkal a **tempSensor** modult. Ha nem, akkor az alábbi lépéseket követve adhatja meg az adatokat:
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Hagyja változatlanul a többi beállítást, és kattintson a **Mentés** gombra.
5. Adja hozzá az előző szakaszokban létrehozott **filterModule** modult. 
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `filterModule`.
    3. A **Rendszerkép URI** mezőbe írja be a rendszerkép címét, például a következőt: `<your container registry address>/filtermodule:0.0.1-amd64`. A rendszerkép teljes címe az előző szakaszban található.
    4. Jelölje be az **Engedélyezés** jelölőnégyzetet, hogy szerkeszthesse a modul ikerdokumentumát. 
    5. Cserélje le a modul ikerdokumentumához tartozó szövegmezőben lévő JSON-t a következő JSON-ra: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kattintson a **Save** (Mentés) gombra.
6. Kattintson a **Tovább** gombra.
7. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. A modulok minden üzenetet az Edge-futtatókörnyezetben tesznek közzé. A futtatókörnyezetben lévő deklaratív szabályok határozzák meg az üzenetek célját. Ebben az oktatóanyagban két útvonalra lesz szüksége. Az első útvonal a hőmérséklet-érzékelőből a szűrőmodulba szállítja az üzeneteket az input1 végponton keresztül, amelyet a **FilterMessages** kezelővel konfigurált. A második útvonal a szűrőmodulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az `upstream` egy speciális cél, amely alapján az Edge Hub az IoT Hubnak küldi az üzeneteket. 

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
10. Térjen vissza az IoT Edge-eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Látható, hogy az új **filtermodule** fut a **tempSensor** modullal és az **IoT Edge-futtatókörnyezettel** együtt. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Edge-eszközről az IoT hubra küldött, az eszközről a felhőbe irányuló üzenetek monitorozása:
1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati sztringgel az IoT hubhoz: 
    1. Nyissa meg a VS Code Explorert a **View** > **Explorer** (Nézet, Explorer) elem kiválasztásával. 
    2. Az Explorerben kattintson az **IOT HUB DEVICES** (IoT Hub-eszközök), majd a **...** elemre. Kattintson a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre, és a felugró ablakban adja meg annak az IoT-központnak a kapcsolati sztringjét, amelyhez az IoT Edge-eszköz csatlakozik. 

        A kapcsolati sztring kereséséhez kattintson az IoT-központ csempéjére az Azure Portalon, majd kattintson a **Megosztott elérési szabályzatok** elemre. A **Megosztott elérési szabályzatok** területen kattintson az **iothubowner** szabályzatra, és másolja ki az **iothubowner** ablakban látható IoT Hub kapcsolati sztringet.   

2. Az IoT-központra érkező adatok monitorozásához válassza a **Nézet** > **Parancskatalógus** elemet, és keressen rá az **IoT: D2C üzenet monitorozásának megkezdése** menüparancsra. 
3. Az adatok monitorozásának leállításához használja az **IoT: D2C üzenet monitorozásának leállítása** menüparancsot. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Továbbléphet bármelyik alábbi oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Az Azure Function üzembe helyezése modulként](tutorial-deploy-function.md)
> [Az Azure Stream Analytics üzembe helyezése modulként](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
