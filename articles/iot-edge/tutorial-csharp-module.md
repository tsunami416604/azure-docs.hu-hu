---
title: Oktatóanyag a C# Linux rendszerhez készült modul fejlesztéséhez – Azure IoT Edge | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre C# kódot tartalmazó IoT Edge modult, és hogyan helyezheti üzembe egy linuxos IoT Edge eszközön.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7714d065d8ac449d10d9b022005e7799f8280bda
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104454"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Oktatóanyag: C# IoT Edge modul fejlesztése linuxos eszközökhöz

A Visual Studio Code használatával programkódot fejleszthet C# , és üzembe helyezheti Azure IoT Edge-t futtató linuxos eszközön. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * A Visual Studio Code használatával hozzon létre egy IoT Edge-modul, amely a .NET Core SDK 2.1-es alapul.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet modult a **C#** **Visual Studio Code**használatával, és hogyan telepítheti azt egy **Linux**-eszközre. Ha Windows-eszközökhöz fejleszt modulokat, ugorjon a [ C# IoT Edge modul fejlesztése Windows](tutorial-csharp-module-windows.md) -eszközökhöz lehetőségre.

A következő táblázat segítségével megismerheti a C# modulok Linux rendszeren történő fejlesztésének és üzembe helyezésének lehetőségeit: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#LinuxAMD64-modulok a VS Code-ban](./media/tutorial-c-module/green-check.png) | ![C#LinuxAMD64-modulok a Visual Studióban](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#LinuxARM32-modulok a VS Code-ban](./media/tutorial-c-module/green-check.png) | ![C#LinuxARM64-modulok a Visual Studióban](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>A Linux ARM64-eszközök támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el. További információ: [ARM64 IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio Code-ban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna haladnia az előző oktatóanyagon, hogy beállítsa a fejlesztési környezetet, [fejlesszen egy IoT Edge modult egy Linux](tutorial-develop-for-linux.md)-eszközhöz. Az oktatóanyag elvégzése után már a következő előfeltételek szükségesek: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Linux-eszköz](quickstart-linux.md).
* Egy tároló-beállításjegyzék, például [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

Az oktatóanyagok végrehajtásához készítse elő a következő további előfeltételeket a fejlesztői gépen: 

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

A következő lépések a Visual Studio Code és az C# Azure IoT Tools bővítmény használatával létrehoznak egy IoT Edge modul-projektet. Miután létrehozta a projekt sablonját, vegyen fel új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságok alapján. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat. 

1. A Visual Studio Code-ban a VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)**  > **Command Palette (Parancskatalógus)** elemet. 

2. A parancs palettáján írja be és futtassa az Azure **parancsot: Jelentkezzen** be, és kövesse az Azure-fiókba való bejelentkezéshez szükséges utasításokat. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

3. A Command paletta írja be és futtassa a parancsot **Azure IoT Edge: Új IoT Edge megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon  **C# modul**. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve az utolsó lépésben megadott névvel. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>A rendszerkép utolsó tárháza a \<következőhöz\>hasonló: beállításjegyzék neve. azurecr.IO/csharpmodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure Container Registry **hozzáférési kulcsok** szakaszának hitelesítő adatait. 

1. A VS Code Explorerben nyissa meg a **.env** fájlt. 
2. Frissítse a mezőket a **felhasználónévvel** és a **jelszó** értékével az Azure Container registryben. 
3. Mentse el ezt a fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg Linux AMD64 C# és Linux rendszerű ARM32v7-eszközökhöz is fejlesztheti a modulokat. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64. 

1. Nyissa meg a parancssort, **és keressen rá Azure IoT Edge: Állítsa be az Edge-megoldás**alapértelmezett célként megadott platformját, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban. 

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **modules** > **CSharpModule** > **Program.cs** modult.

2. A **CSharpModule** névtér tetején adjon hozzá három **using** utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adja hozzá a **temperatureThreshold** változót a **Program** osztályhoz. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adja hozzá a **MessageBody**, a **Machine** és az **Ambient** osztályokat a **Program** osztályhoz. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

5. Keresse meg az **init** függvényt. Ez a függvény egy **ModuleClient** objektumot hoz létre és konfigurál, amely lehetővé teszi, hogy a modul a helyi Azure IoT Edge futtatókörnyezethez kapcsolódjon üzenetek küldéséhez és fogadásához. A **ModuleClient** objektum létrehozása után a kód beolvassa a **temperatureThreshold** értéket a modul ikerdokumentumának kívánt tulajdonságaiból. A kód visszahívást regisztrál egy IoT Edge-központból érkező üzenetek fogadásához az **input1** végponton keresztül. Cserélje le a **SetInputMessageHandlerAsync** metódust egy másikra, és adjon hozzá egy **SetDesiredPropertyUpdateCallbackAsync** metódust a kívánt tulajdonságok frissítéséhez. A módosítás végrehajtásához cserélje le az **Init** metódus utolsó sorát az alábbi kódra:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Adja hozzá az **onDesiredPropertiesUpdate** metódust a **Program** osztályhoz. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

7. Cserélje le a **PipeMessage** metódust a **FilterMessages** metódusra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket. Ezenkívül a **MessageType** tulajdonságot is hozzáadja az üzenethez, amelynek értéke **Alert** (Figyelmeztetés). 

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

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Mentse a Program.cs fájlt.

9. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. 

10. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **modulesContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-csharp-module/module-twin.png)

11. Mentse a Deployment. template. JSON fájlt.


## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a CSharpModule, amely kiszűri azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható határértékeken belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet.

1. Jelentkezzen be a Docker-be a következő parancs beírásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, `--password-stdin`amely a használatát javasolja. Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a Docker [bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a konfigurációban, amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja `docker push` , hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már van előkészítve egy üzembe helyezési jegyzékfájl a forgatókönyvhöz, a **telepítési. JSON** fájl a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorerben bontsa ki az **Azure IoT hub-eszközök** szakaszt a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

5. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt. 

6. Kattintson a frissítés gombra. Meg kell jelennie az új **CSharpModule** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub**fut.  

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **figyelés beépített esemény végpontjának indítása**lehetőséget.

2. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mivel a IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután megvárja a CModule-kód módosításait, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú riasztást is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez. 

   ![IoT Hubra érkező üzenetek megtekintése](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A CSharpModule modult az üzembe helyezési jegyzékben használta, hogy 25 fokos hőmérsékleti küszöbértéket állítson be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studio Code-ban bontsa ki a IoT Edge eszköz alatti részleteket a futó modulok megtekintéséhez. 

2. Kattintson a jobb gombbal a **CSharpModule** elemre, és válassza a **modul Twin szerkesztése**lehetőséget. 

3. A kívánt tulajdonságok között keresse meg a **TemperatureThreshold** . Módosítsa az értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél pedig 10 fokkal magasabbra. 

4. Mentse a modul különálló fájlját.

5. Kattintson a jobb gombbal a modul dupla szerkesztési paneljén bárhová, és válassza a **modul Twin frissítése**elemet. 

5. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok létrehozására, többet is megtudhat a [saját IoT Edge moduljainak fejlesztéséről](module-development.md) , illetve a [Visual Studio Code](how-to-vs-code-develop-module.md)-hoz készült modulok fejlesztéséről. Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan hozhatja Azure IoT Edge az Azure Cloud Services üzembe helyezését az adathordozón lévő adatfeldolgozás és-elemzés során.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
