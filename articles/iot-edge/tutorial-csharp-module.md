---
title: Az oktatóanyag fejlesztése C# Linux – Azure IoT Edge-modul |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Edge-modul a C# kódot, és a egy Linux IoT Edge-eszközön telepítheti.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: d98fd0c3417d54321c492b1e47679ec63f8c1603
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835171"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Fejlesztés a C# IoT Edge-modul a Linux rendszerű eszközök

Fejlesztés a Visual Studio Code használatával C# kódot, és üzembe helyezése az Azure IoT Edge-es Linux rendszerű eszközök. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * A Visual Studio Code használatával hozzon létre egy IoT Edge-modul, amely a .NET Core SDK 2.1-es alapul.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatókör

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy moduljában lévő **C#** használatával **Visual Studio Code**, és hogyan helyezhet üzembe, hogy egy **Linux eszköz**. Ha Windows-eszközökhöz modulok fejleszt, lépjen a [Develop egy C# IoT Edge-modul a Windows-eszközök](tutorial-csharp-module-windows.md) helyette.

A következő táblázat segítségével fejleszteni és üzembe helyezni a C modulok linuxra a lehetőségek megismerése: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux-AMD64** | ![C#a VS Code LinuxAMD64 modulokat](./media/tutorial-c-module/green-check.png) | ![C#a Visual studióban LinuxAMD64 modulok](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#a VS Code LinuxARM32 modulokat](./media/tutorial-c-module/green-check.png) | ![C#a Visual studióban LinuxARM64 modulok](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban a fejlesztési környezet beállítása [egy Linux rendszerű eszköz IoT Edge-modul fejlesztése](tutorial-develop-for-linux.md). Miután befejezte az oktatóanyag, már rendelkeznie kell a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Azure IoT Edge-es Linux rendszerű eszközök](quickstart-linux.md).
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio Code](https://code.visualstudio.com/) konfigurálva a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítmény.
* [A docker CE](https://docs.docker.com/install/) konfigurált Linux-tárolók futtatásához.

Ezekben az oktatóanyagokban végrehajtásához készítse elő a következő további előfeltételeket a fejlesztői gépen: 

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>A modul projekt létrehozása

Az alábbi lépéseket, hozzon létre egy IoT Edge modul projektet C# Visual Studio Code és az Azure IoT Tools bővítmény használatával. Miután létrehozott projektsablon, adjon hozzá új kódot úgy, hogy a modul üzeneteket a jelentett tulajdonságok alapján kiszűri. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat. 

1. A Visual Studio Code-ban a VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

2. A parancskatalógus, adja meg, és futtassa a parancsot **Azure: Jelentkezzen be a** , és kövesse az utasításokat követve jelentkezzen be az Azure-fiókjával. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

3. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon  **C# modul**. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/csharpmodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. A hitelesítő adatokat használja a **hozzáférési kulcsok** az Azure container registry szakaszában. 

1. A VS Code Explorerben nyissa meg a **.env** fájlt. 
2. Frissítse a mezőket a a **felhasználónév** és **jelszó** az Azure container registry értékeit. 
3. Mentse el ezt a fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél-architektúra

A Visual Studio Code jelenleg C modulok Linux AMD64- és Linux ARM32v7 eszközökhöz is fejleszthet. Kell választania, melyik architektúra célozza az egyes megoldások, mert a tároló összeállítása és futtatása eltérően az egyes architektúra. Az alapértelmezett érték a Linux-AMD64. 

1. A parancskatalógus megnyitásához, és keressen rá a **Azure IoT Edge: Állítsa be alapértelmezett célplatform peremhálózati megoldás**, vagy válassza ki a parancsikont a oldalsó sáv az ablak alján. 

2. A parancskatalógus válassza ki a cél-architektúra a lehetőségek listájából. Ebben az oktatóanyagban használjuk egy Ubuntu virtuális gép legyen az IoT Edge-eszköz, így megtartja az alapértelmezett **amd64**. 

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

5. Keresse meg a **Init** függvény. Ez a függvény létrehoz, és konfigurál egy **ModuleClient** objektum, amely lehetővé teszi, hogy a modul csatlakozni a helyi Azure IoT Edge-futtatókörnyezet, az üzenetek küldése és fogadása. A **ModuleClient** objektum létrehozása után a kód beolvassa a **temperatureThreshold** értéket a modul ikerdokumentumának kívánt tulajdonságaiból. A kód visszahívást regisztrál egy IoT Edge-központból érkező üzenetek fogadásához az **input1** végponton keresztül. Cserélje le a **SetInputMessageHandlerAsync** metódust egy másikra, és adjon hozzá egy **SetDesiredPropertyUpdateCallbackAsync** metódust a kívánt tulajdonságok frissítéséhez. A módosítás végrehajtásához cserélje le az **Init** metódus utolsó sorát az alábbi kódra:

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
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
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

11. Mentse a deployment.template.json fájlt.


## <a name="build-and-push-your-module"></a>És a modul leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzá kódot a CSharpModule, hogy a rendszer kiszűri azokat az üzeneteket a gép jelentett hőmérséklet esetén az elfogadható határokon belül. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet.

1. Jelentkezzen be a írja be a következő parancsot a terminálon. Jelentkezzen be a felhasználónév, jelszó és az Azure container Registry bejelentkezési kiszolgáló. Kérheti, hogy ezeket az értéteket a **hozzáférési kulcsok** szakasz a tárolójegyzék az Azure Portalon.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely társítja a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és egyéb megoldást. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Visual Studio Code Explorerben és az Azure IoT Tools bővítmény használatával a modul projekt telepítése az IoT Edge-eszköz. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a **deployment.json** config mappában található fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig.

1. A Visual Studio Code Explorerben bontsa ki a **Azure IoT Hub-eszközök** szakasz az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

5. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt. 

6. Kattintson a frissítés gombra. Látható, hogy az új **CSharpModule** fut a **TempSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** mellett.  

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal az IoT Edge-eszköz neve, és válassza ki **figyelési D2C-Messages Start**.

2. Az IoT Hub érkező üzenetek megjelenítése. Az üzenetek kimarad, míg igénybe vehet, mert az IoT Edge-eszköz kapni az új központi telepítést, és indítsa el a modult. Azt a CModule kóddal végzett módosításokat, majd várjon, amíg a gép hőmérséklet 25 fok eléri üzenetek elküldése előtt. Hozzáadja az üzenettípus **riasztási** , az üzenetek, eléri a hőmérséklet-küszöbérték. 

   ![Az IoT Hub érkező üzenetek megtekintése](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>A modul ikereszköz szerkesztése

CSharpModule ikermodulja manifest nasazení használjuk a hőmérséklet küszöbérték 25 fok beállítása. Az ikermodul segítségével anélkül, hogy a modul kódot kellene működésének módosításához.

1. A Visual Studio Code bontsa ki a Részletek területen az IoT Edge-eszköz a futó modulok megtekintéséhez. 

2. Kattintson a jobb gombbal **CSharpModule** válassza **szerkesztési ikermodul**. 

3. Keresés **TemperatureThreshold** a kívánt tulajdonságait. Módosítsa az értékét egy új hőmérséklet a legújabb jelentett hőmérséklet meghaladja a 10 értéket 5 fok. 

4. Mentse a modul ikereszköz fájlt.

5. Kattintson a jobb gombbal a Szerkesztés panelre és válassza az ikermodul bárhol **frissítés ikermodul**. 

5. A bejövő eszköz a felhőbe irányuló üzenetek figyeléséhez. Az üzenetek, amíg az új hőmérséklet küszöbérték elérése kell megjelennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások használt ebben a cikkben díjak elkerülése érdekében. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok hozhat létre, hogy többet is megtudhat [saját IoT Edge-modulok fejlesztése](module-development.md) , illetve [fejlesztése a Visual Studio Code-dal modulok](how-to-vs-code-develop-module.md). Továbbra is be a következő oktatóanyagok az Azure IoT Edge segítségével üzembe helyezheti az Azure cloud services, a peremhálózaton adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
