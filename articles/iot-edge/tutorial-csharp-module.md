---
title: Azure IoT Edge C# oktatóanyag | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult C#-kóddal, és hogyan helyezheti üzembe egy peremhálózati eszközön
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 73b6397ecc97b9e289749aabddfdc4c6161375d4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38667345"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Oktatóanyag: C#-alapú IoT Edge-modul fejlesztése és üzembe helyezése szimulált eszközön

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows][lnk-tutorial1-win] vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * .NET core 2.0-alapú IoT Edge-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával 
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

* A [Linux](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójában létrehozott Azure IoT Edge-eszköz.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code-hoz. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) a fejlesztői gépen. 


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
A következő lépések azt mutatják be, hogyan hozhat létre egy .NET core 2.0-alapú IoT Edge-modulprojektet a Visual Studio Code és az Azure IoT Edge bővítmény használatával.
1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.
2. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 
3. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
4. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 
   1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni. 
   2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
   3. A modul sablonjaként válassza a **C# modult**. 
   4. A modulnak adja a **CSharpModule** nevet. 
   5. Adja meg az előző szakaszban létrehozott Azure Container Registryt az első modul rendszerképadattáraként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére. A sztring végül a következőképp néz ki: **\<regisztrációs adatbázis neve\>.azurecr.io/csharpmodule**.
 
4. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. Egy **modules** mappát, egy **.vscode** mappát, egy üzembe helyezési jegyzéksablonfájlt és egy **.env** fájlt tartalmaz. Nyissa meg a következőt: **modules** > **CSharpModule** > **Program.cs**.

5. A **CSharpModule** névtér tetején adjon hozzá három `using` utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

6. Adja hozzá a `temperatureThreshold` változót a **Program** osztályhoz. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Adja hozzá a `MessageBody`, `Machine` és az `Ambient` változót a **Program** osztályhoz. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

8. Az **Init** metódus használata esetén a kód létrehoz és konfigurál egy **ModuleClient** objektumot. Ez az objektum lehetővé teszi a modul helyi Azure IoT Edge-futtatókörnyezethez való csatlakozását üzenetek küldése és fogadása céljából. Az **Init** metódusban használt kapcsolati sztringet az IoT Edge-futtatókörnyezet biztosítja a modulnak. A **ModuleClient** objektum létrehozása után a kód beolvassa a TemperatureThreshold változót a modul ikerdokumentumának kívánt tulajdonságaiból, és visszahívást regisztrál üzenetek fogadásához az IoT Edge-központból az **input1** végponton keresztül. Cserélje le a `SetInputMessageHandlerAsync` metódust egy újra, és adjon hozzá egy `SetDesiredPropertyUpdateCallbackAsync` metódust a kívánt tulajdonságfrissítések végrehajtásához. A módosítás végrehajtásához cserélje le az **Init** metódus utolsó sorát az alábbi kódra:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Adja hozzá az `onDesiredPropertiesUpdate` metódust a **Program** osztályhoz. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

10. Cserélje le a `PipeMessage` metódust a `FilterMessages` metódusra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket. Ezenkívül a **MessageType** tulajdonságot is hozzáadja az üzenethez, amelynek értéke **Alert** (Figyelmeztetés). 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
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
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Mentse el ezt a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a CSharpModule modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe, ahonnan a modul rendszerképét leküldheti az Azure Container Registrybe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registryből kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy kérje le őket ismét az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. Az `$edgeAgent` a fájlból tudja meg, hogy üzembe kell helyezni két modult: a **tempSensor** és a **CSharpModule** modult. A `CSharpModule.image` értéke a rendszerkép Linux amd64-es verziójára lett beállítva. További információt az üzembehelyezési jegyzékekről az [IoT Edge-modulok használatát, konfigurálását és újrahasznosítását](module-composition.md) ismertető cikkben olvashat.

3. A **deployment.template.json** fájl tartalmaz egy **registryCredentials** szakaszt, amely tárolja a Docker beállításjegyzék hitelesítő adatait. A tényleges felhasználónév és jelszó párokat az .env fájl tárolja, amelyet a git figyelmen kívül hagy.

4. Adja hozzá a CSharpModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után: 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Mentse el ezt a fájlt.
5. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build IoT Edge solution** (IoT Edge-megoldás összeállítása) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy `deployment.json` fájlt egy új **config** mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a `CSharpModule.dll` elemet, majd leküldi a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A rendszerkép címe a `module.json` fájlban lévő információk alapján áll össze az **\<adattár\>:\<verzió\>-\<platform\>** formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: **registryname.azurecr.io/csharpmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati sztringgel az IoT hubhoz: 
    1. Nyissa meg a VS Code Explorert a **View** > **Explorer** (Nézet, Explorer) elem kiválasztásával. 
    2. Az Explorerben kattintson az **AZURE IOT HUB DEVICES** (Azure IoT Hub-eszközök), majd a **...** elemre. Kattintson a **Select IoT Hub** (IoT Hub kiválasztása) lehetőségre. Kövesse az utasításokat az Azure-fiókba való bejelentkezéshez és az IoT-központ kiválasztásához. 
       A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. A felugró ablakban adja meg annak az IoT-központnak a kapcsolati sztringjét, amelyhez az IoT Edge-eszköz csatlakozik.

2. Az Azure Iot Hub Devices Explorerben kattintson a jobb gombbal az IoT Edge-eszközére, majd kattintson a **Create Deployment for IoT Edge device** (Üzembe helyezés létrehozása Edge-eszközhöz) elemre. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre.

3. Kattintson a frissítés gombra. Látható, hogy az új **CSharpModule** fut a **TempSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** mellett. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához kattintson a **...** elemre, majd válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Az adatok monitorozásának leállításához futtassa a parancskatalógusban az **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: D2C üzenetek monitorozásának leállítása) parancsot. 
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza ki az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, és kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítheti a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt, és megkeresheti helyileg a futó modulokat a Docker Explorerben. A helyi menüben kattintson a **Show Logs** (Naplók megjelenítése) lehetőségre az integrált terminálban való megtekintéshez.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat és konfigurációkat.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

> [!IMPORTANT]
> Az Azure-erőforrások és -erőforráscsoportok törlése nem vonható vissza. A törlést követően az erőforráscsoport és a benne foglalt erőforrások véglegesen törölve lesznek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Ha csak az IoT Hubot szeretné törölni, hajtsa végre az alábbi parancsot a saját hubja és a saját erőforráscsoportja nevével:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


A teljes erőforráscsoport név alapján való törléséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **Szűrés név alapján...** mezőbe írja be az IoT Hubot tartalmazó erőforráscsoport nevét. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [a C#-modulok a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](how-to-develop-csharp-module.md) ismertető cikkben talál. Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
