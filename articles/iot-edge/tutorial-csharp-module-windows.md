---
title: Oktatóanyag – C#-modul fejlesztése a Windows rendszerhez Azure IoT Edge használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy IoT Edge-modult C#-kóddal, és hogyan telepítheti azt egy Windows IoT Edge-eszközre.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- amqp
ms.openlocfilehash: 105dbed66b67f16b305cea74b9761abbef64d5fd
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439776"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Oktatóanyag: C# IoT Edge modul fejlesztése Windows-eszközökhöz

A Visual Studióval C#-kódot fejleszthet, és a Azure IoT Edge rendszert futtató Windows-eszközre telepítheti.

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio használatával hozzon létre egy IoT Edge modult, amely a C# SDK-ra épül.
> * A Visual Studio és a Docker használatával hozzon létre egy Docker-rendszerképet, és tegye közzé azt a beállításjegyzékben.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet egy modult a **C#** -ban a **Visual Studio 2019** használatával, és hogyan telepítheti azt egy **Windows-eszközre**. Ha Linux-eszközökhöz fejleszt modulokat, ugorjon a [C# IoT Edge modul létrehozása Linux-eszközökhöz](tutorial-csharp-module.md) című lépésre.

A következő táblázat segítségével megismerheti a C#-modulok Windows-eszközökre való fejlesztésének és üzembe helyezésének lehetőségeit:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 fejlesztés** | ![C#-modulok fejlesztése a WinAMD64 a VS Code-ban](./media/tutorial-c-module/green-check.png) | ![C#-modulok fejlesztése a WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 hibakeresés** |   | ![C#-modulok hibakeresése a WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna haladnia az előző oktatóanyagon, hogy beállítsa a fejlesztési környezetet, [IoT Edge modult fejlesszen ki egy Windows-eszközhöz](tutorial-develop-for-windows.md). Az oktatóanyag elvégzése után már a következő előfeltételek szükségesek:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Windows-eszköz](quickstart.md).
* Egy tároló-beállításjegyzék, például [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* A [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) konfigurálva van a [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítménnyel.
* A [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) Windows-tárolók futtatására van konfigurálva.

> [!TIP]
> Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verziót) használja, a plrease letöltheti és telepítheti [Azure IoT Edge eszközeit](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) a vs 2017-hoz a Visual Studio piactérről

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

Az alábbi lépések a Visual Studio és a Azure IoT Edge Tools bővítmény használatával IoT Edge modul-projektet hoznak létre. Miután létrehozta a projekt sablonját, vegyen fel új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságok alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

A Azure IoT Edge-eszközök a Visual Studióban a támogatott IoT Edge modul összes nyelvéhez biztosítanak Project sablonokat. Ezekben a sablonokban minden olyan fájl és kód szerepel, amelyre a IoT Edge teszteléséhez egy munkamodult kell telepítenie, vagy egy kiindulási pontot kell adnia a sablon saját üzleti logikával való testreszabásához.

1. Indítsa el a Visual Studio 2019 alkalmazást, és válassza az **új projekt létrehozása**lehetőséget.

2. Keresse meg **IoT Edge** , és válassza ki a **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra.

   ![Új Azure IoT Edge projekt létrehozása](./media/tutorial-csharp-module-windows/new-project.png)

3. Nevezze át a projektet és a megoldást egy olyan leíróra, mint a **CSharpTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra.

   ![Új Azure IoT Edge projekt konfigurálása](./media/tutorial-csharp-module-windows/configure-project.png)

4. Konfigurálja a projektet a következő értékekkel:

   | Mező | Érték |
   | ----- | ----- |
   | Sablon kiválasztása | Válassza a **C# modul**lehetőséget. |
   | Modul projekt neve | A modulnak adja a **CSharpModule** nevet. |
   | Docker-rendszerkép tárháza | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve a modul projekt neve értékből. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal. <br><br> A rendszerkép utolsó tárháza a következőhöz hasonlít: \<registry name\> . azurecr.IO/csharpmodule. |

   ![A projekt konfigurálása a céleszköz, a modul típusa és a tároló-beállításjegyzék számára](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. A projekt létrehozásához válassza a **Hozzáadás** lehetőséget.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Az üzembe helyezési jegyzék megosztja a tároló beállításjegyzékének hitelesítő adatait a IoT Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure Container Registry **hozzáférési kulcsok** szakaszának hitelesítő adatait.

1. A Visual Studio Solution Explorerben nyissa meg a **deployment.template.js** fájlt.

2. Keresse meg a **registryCredentials** tulajdonságot a $edgeAgent kívánt tulajdonságban. A szolgáltatásnak a projekt létrehozásakor megadott információk alapján kell kitöltenie a beállításjegyzékbeli címeket, majd a Felhasználónév és a jelszó mezőben a változók nevét kell tartalmaznia. Például:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Nyissa meg a **. env** fájlt a modul-megoldásban. (Alapértelmezés szerint rejtett a Megoldáskezelőban, ezért előfordulhat, hogy az **összes fájl megjelenítése** gombra kell kattintania a megjelenítéséhez.) A. env fájlnak ugyanazt a Felhasználónév és jelszó változót kell tartalmaznia, amelyet a fájl deployment.template.jsban látott.

4. Adja hozzá a **Felhasználónév** és a **jelszó** értékét az Azure Container registryből.

5. Mentse a módosításokat a. env fájlba.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modul kódja üzeneteket fogad egy bemeneti várólistán, és egy kimeneti várólistán keresztül továbbítja azokat. Vegyünk fel néhány további kódot, hogy a modul feldolgozza az üzeneteket a peremen, mielőtt továbbítaná őket a IoT Hubba. Frissítse a modult úgy, hogy az minden üzenetben elemezze a hőmérsékleti adatokat, és csak akkor küldje el az üzenetet IoT Hub, ha a hőmérséklet meghaladja az adott küszöbértéket.

1. A Visual Studióban nyissa meg a **CSharpModule**  >  **program.cs**.

2. A **CSharpModule** névtér tetején adjon hozzá három **using** utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adja hozzá a **temperatureThreshold** változót a **program** osztályhoz a számláló változó után. A temperatureThreshold változó állítja be azt az értéket, amelyet a mért hőmérsékletnek meg kell haladnia az IoT hub számára küldendő adat esetében.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adja hozzá a **MessageBody**, a **gépet**és a **környezeti** osztályt a **program** osztályhoz a változó deklarációja után. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

5. Keresse meg az **init** metódust. Ez a metódus egy **ModuleClient** objektumot hoz létre és konfigurál, amely lehetővé teszi, hogy a modul a helyi Azure IoT Edge futtatókörnyezethez kapcsolódjon üzenetek küldéséhez és fogadásához. A kód emellett regisztrálja a visszahívást, hogy egy IoT Edge hub üzeneteit a **input1** -végponton keresztül fogadja.

   Cserélje le a teljes init metódust a következő kódra:

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

   Ez a frissített init-módszer továbbra is beállítja a IoT Edge futtatókörnyezethez való kapcsolódást a ModuleClient, de új funkciókat is biztosít. A **temperatureThreshold** értékének lekéréséhez beolvassa a modulhoz tartozó dupla kívánt tulajdonságokat. Ezután létrehoz egy visszahívást, amely figyeli a modulhoz tartozó dupla kívánt tulajdonságok jövőbeli frissítéseit. Ezzel a visszahívással távolról frissítheti a modul hőmérsékleti küszöbértékét, és a módosítások bekerülnek a modulba.

   A frissített init metódus a meglévő **SetInputMessageHandlerAsync** metódust is módosítja. A mintakód a *input1* lévő bejövő üzeneteket a *PipeMessage* függvénnyel dolgozza fel, de módosítani szeretnénk ezt a *FilterMessages* -függvényt, amelyet a következő lépésekben hozunk létre.

6. Adjon hozzá egy új **onDesiredPropertiesUpdate** metódust a **program** osztályhoz. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

7. Távolítsa el a minta **PipeMessage** metódust, és cserélje le egy új **FilterMessages** metódusra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket. Ezenkívül a **MessageType** tulajdonságot is hozzáadja az üzenethez, amelynek értéke **Alert** (Figyelmeztetés).

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
                using(var filteredMessage = new Message(messageBytes))
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

9. Nyissa meg a **deployment.template.jst** a IoT Edge-megoldás fájljában. Ez a fájl közli a IoT Edge-ügynökkel, hogy mely modulokat kívánja telepíteni, ebben az esetben a **SimulatedTemperatureSensor** és a **CSharpModule**, és közli a IoT Edge hub-val, hogyan irányítja az üzeneteket közöttük.

10. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **modulesContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Modul Twin hozzáadása a központi telepítési sablonhoz](./media/tutorial-csharp-module-windows/module-twin.png)

11. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **CSharpModule** modulhoz az olyan üzenetek kiszűrésére, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A következő parancs használatával jelentkezzen be a Docker-be a fejlesztői gépre. Használja a felhasználónevet, a jelszót és a bejelentkezési kiszolgálót az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, amely a használatát javasolja `--password-stdin` . Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

2. A Visual Studio Solution Explorerben kattintson a jobb gombbal a létrehozni kívánt projekt nevére. Az alapértelmezett név a **AzureIotEdgeApp1** , és a Windows-modul létrehozása óta a bővítménynek **Windows. Amd64**-nek kell lennie.

3. Válassza ki **az IoT Edge-modulok kiépítése és leküldése**lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Cloud Explorer és a Azure IoT Edge Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már rendelkezik egy, a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájlval, a konfigurációs mappában található fájl **deployment.windows-amd64.js** . Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Cloud Explorerben bontsa ki az erőforrásokat a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal annak a IoT Edge eszköznek a nevére, amelyről szeretné fogadni a központi telepítést.

3. Válassza a **központi telepítés létrehozása**lehetőséget.

4. A Fájlkezelőben válassza ki a **deployment.windows-amd64.js** fájlt a megoldás konfigurációs mappájába.

5. Frissítse a Cloud Explorert, és tekintse meg az eszköz alatt felsorolt telepített modulokat.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

A IoT Edge Tools bővítmény használatával megtekintheti az üzeneteket, ahogy megérkeznek a IoT Hub.

1. A Visual Studio Cloud Explorerben válassza ki a IoT Edge eszköz nevét.

2. A **műveletek** listában válassza a **figyelés beépített esemény végpontjának elindítása**lehetőséget.

3. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mert a CSharpModule-kód módosításai megvárná, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez.

   ![IoT Hubra érkező üzenetek megtekintése](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A 2. CSharpModule modult használtuk a hőmérsékleti küszöbérték 25 fokos beállításához. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studióban nyissa meg a **deployment.windows-amd64.js** fájlt. (Nem a Deployment. template fájl. Ha nem látja a telepítési jegyzékfájlt a konfigurációs fájlban a megoldás Explorerben, jelölje be az **összes fájl megjelenítése** ikont az Explorer eszköztárán.)

2. Keresse meg a CSharpModule Twin értéket, és módosítsa a **temperatureThreshold** paraméter értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél 10 fokkal magasabbra.

3. Mentse a **deployment.windows-amd64.js** fájlt.

4. Kövesse az üzembe helyezési lépéseket, és alkalmazza a frissített üzembe helyezési jegyzéket az eszközre.

5. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok létrehozására, többet is megtudhat a [saját IoT Edge moduljainak fejlesztéséről](module-development.md) , illetve a [Visual Studióval történő modulok fejlesztéséről](how-to-visual-studio-develop-module.md). IoT Edge modulokra, például a szimulált hőmérsékleti modulra példaként lásd: [IoT Edge modul minták](https://github.com/Azure/iotedge/tree/master/edge-modules).

Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan hozhatja Azure IoT Edge az Azure Cloud Services üzembe helyezését az adathordozón lévő adatfeldolgozás és-elemzés során.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)
