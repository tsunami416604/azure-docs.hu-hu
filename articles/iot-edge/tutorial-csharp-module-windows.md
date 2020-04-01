---
title: Oktatóanyag – C# modul fejlesztése Windows-hoz az Azure IoT Edge használatával
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Edge-modult C# kóddal, és hogyan telepítheti azt egy Windows IoT Edge-eszközre.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4fa3fb17f4eace8d389738fb46267a097610f175
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760419"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Oktatóanyag: C# IoT Edge modul fejlesztése Windows-eszközökhöz

A Visual Studio segítségével c# kódot fejleszthet, és üzembe helyezheti egy Azure IoT Edge-et futtató Windows-eszközre.

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio segítségével hozzon létre egy IoT Edge-modult, amely a C# SDK-n alapul.
> * A Visual Studio és a Docker segítségével hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzékben.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet ki egy modult **C#** nyelven a **Visual Studio 2019** használatával, és hogyan telepítheti **azt Egy Windows-eszközre.** Ha linuxos eszközökhöz fejleszt modulokat, keresse [fel a C# IoT Edge-modul linuxos eszközökhöz való fejlesztése](tutorial-csharp-module.md) lehetőséget.

Az alábbi táblázat segítségével megismerheti a C# modulok Windows-eszközökre történő fejlesztésének és telepítésének lehetőségeit:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 fejlesztése** | ![C# modulok fejlesztése a WinAMD64-hez a VS Code-ban](./media/tutorial-c-module/green-check.png) | ![C# modulok fejlesztése a WinAMD64-hez a Visual Studióban](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 hibakeresés** |   | ![A WinAMD64 Hibakeresési C# moduljai a Visual Studio alkalmazásban](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon a fejlesztői környezet, [az IoT Edge modul fejlesztése Windows-eszközhöz](tutorial-develop-for-windows.md). Befejezése után, hogy az oktatóanyag, akkor már rendelkeznie kell a következő előfeltételeket:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Windows-eszköz.](quickstart.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítménysel konfigurált Visual Studio [2019.](https://docs.microsoft.com/visualstudio/install/install-visual-studio)
* [A Docker Desktop](https://docs.docker.com/docker-for-windows/install/) windowsos tárolók futtatására van konfigurálva.

> [!TIP]
> Ha a Visual Studio 2017-et (15.7-es vagy újabb verzió) használja, töltse le és telepítse az [Azure IoT Edge-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) a VS 2017-hez a Visual Studio piactérről

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

A következő lépések hozzon létre egy IoT Edge modul projekt segítségével a Visual Studio és az Azure IoT Edge Tools bővítmény használatával. Miután létrehozott egy projektsablont, adjon hozzá új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságaik alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Az Azure IoT Edge-eszközök projektsablonokat biztosít a Visual Studio összes támogatott IoT Edge-modulnyelvéhez. Ezek a sablonok rendelkeznek az összes olyan fájllal és kóddal, amelyegy működő modul üzembe helyezéséhez szükséges az IoT Edge teszteléséhez, vagy kiindulópontot biztosít a sablon saját üzleti logikával történő testreszabásához.

1. Indítsa el a Visual Studio 2019-et, és válassza **az Új projekt létrehozása lehetőséget.**

2. Keressen az **IoT Edge-et,** és válassza az **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra.

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-csharp-module-windows/new-project.png)

3. Nevezze át a projektet és a megoldást valami leíróra, mint a **CSharpTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra.

   ![Új Azure IoT Edge-projekt konfigurálása](./media/tutorial-csharp-module-windows/configure-project.png)

4. Konfigurálja a projektet a következő értékekkel:

   | Mező | Érték |
   | ----- | ----- |
   | Sablon kiválasztása | Válassza a **C# modul lehetőséget.** |
   | Modulprojekt neve | A modulnak adja a **CSharpModule** nevet. |
   | Docker-rendszertár | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve a modulprojekt névértékéből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> A végső képtár \<háztár\>néz ki, mint a rendszerleíró adatbázis neve .azurecr.io/csharpmodule. |

   ![A projekt konfigurálása céleszközhöz, modultípushoz és tárolóbeállítás-beállításjegyzékhez](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. A projekt létrehozásához válassza a **Hozzáadás** lehetőséget.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A központi telepítési jegyzékfájl megosztja a tároló beállításjegyzék hitelesítő adatait az IoT Edge futásidejű. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure-tároló beállításjegyzékének **Access-kulcsok** szakaszában található hitelesítő adatokat.

1. Nyissa meg a **Deployment.template.json** fájlt a Visual Studio-megoldáskezelőben.

2. Keresse meg a **registryCredentials tulajdonságot** a kívánt tulajdonságok $edgeAgent. A beállításjegyzék címét automatikusan ki kell tölteni a projekt létrehozásakor megadott adatokból, majd a felhasználónév és a jelszó mezőknek változóneveket kell tartalmazniuk. Példa:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Nyissa meg az **.env** fájlt a modulmegoldásban. (Alapértelmezés szerint el van rejtve a Megoldáskezelőben, ezért előfordulhat, hogy a megjelenítéshez ki kell **választania** az Összes fájl megjelenítése gombot.) Az .env fájlnak ugyanazokat a felhasználónevet és jelszóváltozókat kell tartalmaznia, mint a deployment.template.json fájlban.

4. Adja hozzá a **felhasználónév** és **a jelszó** értékeket az Azure-tároló beállításjegyzékből.

5. Mentse a módosításokat az .env fájlba.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modulkód fogadja az üzeneteket egy bemeneti várólistán, és továbbítja azokat egy kimeneti várólistán keresztül. Adjunk hozzá néhány további kódot, hogy a modul feldolgozza az üzeneteket a szélén, mielőtt továbbítana őket az IoT Hubra. Frissítse a modult úgy, hogy elemezze az egyes üzenetekben lévő hőmérsékleti adatokat, és csak akkor küldi el az üzenetet az IoT Hubnak, ha a hőmérséklet túllép egy bizonyos küszöbértéket.

1. A Visual Studio programban nyissa meg a **CSharpModule** > **Program.cs**.

2. A **CSharpModule** névtér tetején adjon hozzá három **using** utasítást a későbbiekben használt típusokhoz:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adja hozzá a **temperatureThreshold** változót a **Program** osztályhoz a számláló változó után. A temperatureThreshold változó beállítja azt az értéket, amelyet a mért hőmérsékletnek meg kell haladnia az IoT hubnak küldendő adatokhoz.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adja hozzá a **MessageBody**, **Gép**és **Ambient** osztályokat a **Program** osztályhoz a változó deklarációk után. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

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

5. Keresse meg az **Init** metódust. Ez a módszer létrehoz és konfigurál egy **ModuleClient** objektumot, amely lehetővé teszi, hogy a modul csatlakozzon a helyi Azure IoT Edge futásidő üzenetek küldéséhez és fogadásához. A kód is regisztrál egy visszahívást, hogy üzeneteket fogadjon egy IoT Edge hub a **input1** végponton keresztül.

   Cserélje le a teljes Init metódust a következő kódra:

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

   Ez a frissített Init metódus továbbra is beállítja a kapcsolatot az IoT Edge futásidejű a ModuleClient, de új funkciókat is hozzáad. Beolvassa a modul iker kívánt tulajdonságait a **temperatureThreshold** érték lekéréséhez. Ezután létrehoz egy visszahívást, amely figyeli a modul iker kívánt tulajdonságainak jövőbeli frissítéseit. Ezzel a visszahívással távolról frissítheti a modul ikerhőmérsékleti küszöbértékét, és a módosítások beépülnek a modulba.

   A frissített Init metódus a meglévő **SetInputMessageHandlerAsync metódust is módosítja.** A mintakódban a *bejövő* üzenetek a *PipeMessage* függvénnyel kerülnek feldolgozásra, de ezt módosítani szeretnénk a *FilterMessages* függvény használatához, amelyet a következő lépésekben hozunk létre.

6. Adjon hozzá egy új **onDesiredPropertiesUpdate** metódust a **Program** osztályhoz. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

9. Nyissa meg a **deployment.template.json** fájlt az IoT Edge-megoldásban. Ez a fájl közli az IoT Edge-ügynök, amely modulokat telepíteni, ebben az esetben **SimulatedTemperatureSensor** és **CSharpModule**, és megmondja az IoT Edge hub, hogyan irányítsa az üzeneteket közöttük.

10. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **modulesContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ikermodul hozzáadása a telepítési sablonhoz](./media/tutorial-csharp-module-windows/module-twin.png)

11. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Építsd meg és nyomd meg a modult

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a **CSharpModule** modulhoz az olyan üzenetek kiszűrésére, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A következő paranccsal jelentkezzen be a Docker be a fejlesztői gépen. Használja a felhasználónevet, a jelszót és a bejelentkezési kiszolgálót az Azure-tároló beállításjegyzékéből. Ezeket az értékeket az Azure Portalon a beállításjegyzék **Access-kulcsok** szakaszából kérheti le.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

2. A Visual Studio megoldáskezelőjében kattintson a jobb gombbal a létrehozandó projektnevére. Az alapértelmezett név **az AzureIotEdgeApp1,** és mivel Windows-modult hoz, a bővítménynek **Windows.Amd64-nek**kell lennie.

3. Válassza **a Build and Push IoT Edge Modules (IoT edge-modulok) lehetőséget.**

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, beépített információk a központi telepítési sablon és más megoldásfájlokat. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

## <a name="deploy-modules-to-device"></a>Modulok telepítése az eszközre

A Visual Studio felhőkezelőés az Azure IoT Edge Tools bővítmény segítségével telepítse a modulprojektet az IoT Edge-eszközre. Már rendelkezik egy központi telepítési jegyzékfájllal a forgatókönyvhöz, a **deployment.json** fájlhoz a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. A Visual Studio felhőkezelőjében bontsa ki az erőforrásokat az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal annak az IoT Edge-eszköznek a nevére, amelyet a központi telepítéshez szeretne fogadni.

3. Válassza **a Központi telepítés létrehozása**lehetőséget.

4. A fájlkezelőben válassza ki a **deployment.windows-amd64** fájlt a megoldás konfigurációs mappájában.

5. Frissítse a felhőkezelőt az eszköz alatt felsorolt telepített modulok megtekintéséhez.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge Tools-bővítmény használatával megtekintheti az üzeneteket, amint azok megérkeznek az IoT Hubba.

1. A Visual Studio felhőkezelőben válassza ki az IoT Edge-eszköz nevét.

2. A **Műveletek** listában válassza a **Beépített eseményvégpont figyelésének megkezdése**lehetőséget.

3. Tekintse meg az IoT Hubhoz érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mert a CSharpModule kódmódosításaink megvárják, amíg a gép hőmérséklete eléri a 25 fokot az üzenetek küldése előtt. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri az adott hőmérsékleti küszöbértéket.

   ![Az IoT Hubra érkező üzenetek megtekintése](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul szerkesztése iker

A CSharpModule modul ikermodulját használtuk, hogy a hőmérsékleti küszöböt 25 fokra állítsuk. Az ikermodul segítségével a modulkód frissítése nélkül módosíthatja a funkciót.

1. Nyissa meg a **Deployment.windows-amd64.json** fájlt a Visual Studióban. (Nem a deployment.template fájlt. Ha a telepítési jegyzékfájl nem látható a megoldáskezelő konfigurációs fájljában, jelölje be az **Összes fájl megjelenítése** ikont a felfedező eszköztárán.)

2. Keresse meg a CSharpModule iker és módosítsa az értékét a **hőmérsékletKüszöb** paraméter egy új hőmérséklet 5 fokkal 10 fokkal magasabb, mint a legutóbbi jelentett hőmérséklet.

3. Mentse a **deployment.windows-amd64.json** fájlt.

4. Kövesse a központi telepítési lépéseket ismét a frissített központi telepítési jegyzékfájl eszközre való alkalmazásához.

5. Figyelje a bejövő eszközről a felhőbe irányuló üzeneteket. Az új hőmérsékleti küszöbérték eléréséig meg kell jelennie az üzeneteknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások, amelyek ebben a cikkben a költségek elkerülése érdekében.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat [saját IoT Edge-moduljainak fejlesztéséről,](module-development.md) illetve arról, hogyan [fejleszthet modulokat a Visual Studio segítségével.](how-to-visual-studio-develop-module.md) Példák az IoT Edge-modulok, beleértve a szimulált hőmérséklet modul, lásd: [IoT Edge modul minták.](https://github.com/Azure/iotedge/tree/master/edge-modules)

Folytathatja a következő oktatóanyagokat, hogy megtudja, az Azure IoT Edge hogyan segíthet az Azure felhőszolgáltatásainak üzembe helyezésében a peremhálózati adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Egyéni vision szolgáltatás](tutorial-deploy-custom-vision.md)
