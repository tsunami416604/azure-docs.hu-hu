---
title: Az oktatóanyag fejlesztése C# modul a Windows - az Azure IoT Edge |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Edge-modul a C# code, és telepítse a Windows IoT Edge-eszköz.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 046398af8678e708784614dfdc231778454ed945
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576599"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Oktatóanyag: Fejlesztés a C# IoT Edge-modul a Windows-eszközök

Fejlesztés a Visual Studio használatával C# kódot, és üzembe helyezése az Azure IoT Edge-es Windows eszközök. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * A Visual Studio használatával hozzon létre egy IoT Edge-modul, amely alapján a C# SDK-t.
> * A Visual Studio és a Docker használatával egy Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatókör

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy moduljában lévő **C#** használatával **Visual Studio 2017**, és hogyan helyezhet üzembe, hogy egy **Windows eszköz**. Ha a Linux rendszerű eszközök modulok fejleszt, lépjen a [Develop egy C# IoT Edge-modul a Linux rendszerű eszközök](tutorial-csharp-module.md) helyette. 

A következő táblázat segítségével fejleszteni és üzembe helyezni a C-modulok Windows-eszközökre a lehetőségek megismerése: 

| C# | Visual Studio Code | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Windows-AMD64 fejlesztése** | ![Fejlesztés C# WinAMD64 a VS Code-ban a modulok](./media/tutorial-c-module/green-check.png) | ![Fejlesztés C# modulok az WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |
| **Windows-AMD64 hibakeresési** |   | ![Hibakeresési C# modulok az WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban a fejlesztési környezet beállítása [fejlesztése az IoT Edge-modul a Windows-eszköz](tutorial-develop-for-windows.md). Miután befejezte az oktatóanyag, már rendelkeznie kell a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Windows-eszközök Azure IoT Edge futtatásakor](quickstart.md).
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), konfigurált 15.7 vagy újabb verzió, a [Azure IoT Edge-eszközök](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) bővítmény.
* [A docker CE](https://docs.docker.com/install/) konfigurált Windows-tárolók futtatásához.

## <a name="create-a-module-project"></a>A modul projekt létrehozása

Az alábbi lépéseket az IoT Edge-modul projekt létrehozása a Visual Studio és az Azure IoT Edge Tools bővítmény használatával. Miután létrehozott projektsablon, adjon hozzá új kódot úgy, hogy a modul üzeneteket a jelentett tulajdonságok alapján kiszűri. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Az Azure IoT Tools bővítmény biztosít projektsablonok az összes támogatott IoT Edge modul nyelveken a Visual Studio 2017-ben. Ezek a sablonok rendelkezik a fájlok és a kódot, amely üzembe kell helyeznie egy működő modul tesztelheti az IoT Edge, vagy a saját üzleti logikája a sablon testreszabásához kiindulási pontot biztosítanak. 

1. Futtassa a Visual Studiót rendszergazdaként.

2. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget. 

3. Az új projekt ablakról, válassza ki a **Azure IoT** típus projektre, és válassza ki a **Azure IoT Edge** projekt. Nevezze át a projekt és a megoldás leíró valami hasonló **CSharpTutorialApp**. A projekt létrehozásához válassza az **OK** lehetőséget. 

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-csharp-module-windows/new-project.png)

4. Az IoT Edge-alkalmazás és a modul ablakban, a projekt konfigurálásához a következő értékeket: 

   | Mező | Érték |
   | ----- | ----- |
   | Alkalmazásplatform | Törölje a jelet **Linux Amd64**, és ellenőrizze **WindowsAmd64**. |
   | Sablonválasztás | Válassza ki  **C# modul**. | 
   | A modul projekt neve | A modulnak adja a **CSharpModule** nevet. | 
   | Docker-rendszerkép tárház | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének előre van töltve, a modul projekt neve értékből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/csharpmodule. |

   ![A céleszközön, modultípus és tároló-beállításjegyzék-projekt konfigurálása](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Válassza ki **OK** alkalmazza a módosításokat. 

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Manifest nasazení az IoT Edge-futtatókörnyezet osztanak meg a tárolóregisztrációs adatbázis hitelesítő adatait. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. A hitelesítő adatokat használja a **hozzáférési kulcsok** az Azure container registry szakaszában. 

1. A Visual Studio megoldáskezelőjében nyissa meg a **deployment.template.json** fájlt. 

2. Keresse meg a **registryCredentials** tulajdonság a $edgeAgent a kívánt tulajdonságok. 

3. Frissítse a hitelesítő adatait, ez a formátum a következő tulajdonság: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 

1. In Visual Studio, open **CSharpModule** > **Program.cs**.

2. At the top of the **CSharpModule** namespace, add three **using** statements for types that are used later:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adja hozzá a **temperatureThreshold** változót a **Program** osztály után a számlálóba. A temperatureThreshold változó mért hőmérséklete túl kell lépnie az adatok küldését az IoT hub értéket állítja be. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adja hozzá a **MessageBody tulajdonság**, **gép**, és **környezeti** osztályok, hogy a **Program** osztály a változók deklarációja után. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

5. Keresse meg a **Init** metódust. Ezzel a módszerrel hoz létre, és konfigurálja a **ModuleClient** objektum, amely lehetővé teszi, hogy a modul csatlakozni a helyi Azure IoT Edge-futtatókörnyezet, az üzenetek küldése és fogadása. A kód is fogad üzeneteket egy IoT Edge hubot használatával egy visszahívást regisztrálja a **input1** végpont.

   Cserélje le a teljes inicializálási metódusnak az alábbi kódra:
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   A frissített inicializálási metódusnak még állítja be a kapcsolatot a ModuleClient az IoT Edge-futtatókörnyezet, de is hozzáadja az új funkciókat. Olvassa be, hogy a modul ikercímkéire kívánt tulajdonságokat lekérni a **temperatureThreshold** értéket. Ezután létrehoz egy visszahívást, amelyet az összes jövőbeli frissítései az ikermodul kívánt tulajdonságai figyeli. A visszahívási távolról frissítheti az ikermodul a hőmérséklet küszöbértéke, és a módosításokat a modul kell építeni. 

   A frissített inicializálási metódusnak is megváltozik a meglévő **SetInputMessageHandlerAsync** metódust. A mintakód a beérkező üzeneteket a *input1* dolgozza fel a *PipeMessage* funkciót, de szeretné ezt módosítani, használja a *FilterMessages* függvény, amely a következő lépésben hozunk létre. 

6. Vegyen fel egy új **onDesiredPropertiesUpdate** metódust a **Program** osztály. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

7. Távolítsa el a minta **PipeMessage** metódust, és cserélje le egy új **FilterMessages** metódust. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket. Ezenkívül a **MessageType** tulajdonságot is hozzáadja az üzenethez, amelynek értéke **Alert** (Figyelmeztetés). 

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

9. Nyissa meg a **deployment.template.json** fájlt az IoT Edge-megoldását. A fájl arra utasítja az IoT Edge-ügynök, mely modulok üzembe helyezéséhez ebben az esetben **tempSensor** és **CSharpModule**, és az IoT Edge hubot jelzi, hogyan irányítsa az üzenetek közöttük.

10. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **modulesContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-csharp-module-windows/module-twin.png)

11. Mentse a deployment.template.json fájlt.


## <a name="build-and-push-your-module"></a>És a modul leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **CSharpModule** modulhoz az olyan üzenetek kiszűrésére, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A következő paranccsal jelentkezzen be a fejlesztői gépen. A felhasználónév, jelszó és bejelentkezési kiszolgáló használata az Azure container registryben. Kérheti, hogy ezeket az értéteket a **hozzáférési kulcsok** szakasz a tárolójegyzék az Azure Portalon.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

2. A Visual Studio megoldáskezelőben kattintson a jobb gombbal a projekt nevét, amely kíván létrehozni. Alapértelmezés szerint ez **AzureIotEdgeApp1** és a egy Windows-modul létrehozásakor, mert a bővítmény lehet **Windows.Amd64**. 

3. Válassza ki **hozhat létre, és küldje le az IoT Edge-modulok**. 

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely társítja a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és egyéb megoldást. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból. 

## <a name="deploy-modules-to-device"></a>Eszköz modulok telepítése

A Visual Studio cloud explorer és az Azure IoT Edge Tools bővítmény használatával a modul projekt telepítése az IoT Edge-eszköz. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a **deployment.json** config mappában található fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig. 

1. A Visual Studio cloud explorer bontsa ki az erőforrásokat az IoT-eszközök listájának megtekintéséhez. 

2. Kattintson a jobb gombbal az IoT Edge-eszköz, amely szeretne kapni a központi telepítés nevét. 

3. Válassza ki **. üzemelő példány létrehozása**.

4. A Fájlkezelőben válassza ki a **deployment.windows-amd64** , hogy a megoldás config mappában található fájl. 

5. Frissítse a cloud explorer felsorolva az eszköz a telepített modulok megtekintéséhez. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak. 

Használhatja az IoT Edge Tools bővítmény üzenetek megtekintéséhez, hogy az IoT Hub érkezik. 

1. A Visual Studio cloud explorer válassza ki az IoT Edge-eszköz nevét. 

2. Az a **műveletek** listáról válassza ki **figyelési D2C üzenet Start**. 

3. Az IoT Hub érkező üzenetek megjelenítése. Az üzenetek kimarad, míg igénybe vehet, mert a módosításokat végeztünk el a CSharpModule kód Várjon, amíg a gép hőmérséklet 25 fok eléri üzenetek elküldése előtt. Hozzáadja az üzenettípus **riasztási** , az üzenetek, eléri a hőmérséklet-küszöbérték. 

   ![Az IoT Hub érkező üzenetek megtekintése](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul ikereszköz szerkesztése

CSharpModule ikermodulja segítségével állítsa be a hőmérséklet küszöbérték 25 fok. Az ikermodul segítségével anélkül, hogy a modul kódot kellene működésének módosításához.

1. A Visual Studióban nyissa meg a **deployment.windows-amd64.json** fájlt. (Nem a deployment.template fájlt. Ha nem látja az üzembe helyezés eredményezi a konfigurációs fájlt a megoldáskezelőben, válassza ki a **minden fájl megjelenítése** ikonra az explorer eszköztárán.)

2. Keresse meg a CSharpModule iker, és módosítsa az értéket, a **temperatureThreshold** paraméter a legújabb jelentett hőmérséklet meghaladja a 10 értéket 5 fokkal új hőmérséklet. 

3. Mentse a **deployment.windows-amd64.json** fájlt.

4. Hajtsa végre újra a központi telepítési lépéseket az üzemelő példány frissítése jegyzékfájl alkalmazza az eszközre. 

5. A bejövő eszköz a felhőbe irányuló üzenetek figyeléséhez. Az üzenetek, amíg az új hőmérséklet küszöbérték elérése kell megjelennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások használt ebben a cikkben díjak elkerülése érdekében. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok hozhat létre, hogy többet is megtudhat [saját IoT Edge-modulok fejlesztése](module-development.md) , illetve [fejlesztése a Visual Studióval modulok](how-to-visual-studio-develop-module.md). Továbbra is be a következő oktatóanyagok az Azure IoT Edge segítségével üzembe helyezheti az Azure cloud services, a peremhálózaton adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
