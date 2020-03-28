---
title: Oktatóanyag – C# modul fejlesztése Linuxhoz az Azure IoT Edge használatával
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Edge-modult C# kóddal, és üzembe helyezheti egy Linux IoT Edge-eszközön.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 53e1863f6f3421a6d8df9112f463f16443cff93e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943048"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Oktatóanyag: C# IoT Edge modul fejlesztése Linux-eszközökhöz

A Visual Studio-kód használatával C# kódot fejleszthet, és üzembe helyezheti egy Azure IoT Edge-et futtató Linux-eszközre.

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezése szimulált eszközön [Windows](quickstart.md) vagy [Linux](quickstart-linux.md) rendszeren című rövid útmutatóban létrehozott szimulált IoT Edge-eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio Code használatával hozzon létre egy IoT Edge-modult, amely a .NET Core 2.1 SDK-n alapul.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet ki egy modult **C#** nyelven a **Visual Studio Code** használatával, és hogyan telepítheti **linuxos eszközre.** Ha windowsos eszközökhöz fejleszt modulokat, keresse [fel a C# IoT Edge-modul fejlesztése Windows-eszközökhöz](tutorial-csharp-module-windows.md) lehetőséget.

Az alábbi táblázat segítségével megismerheti a C# modulok linuxos fejlesztésének és üzembe helyezésének lehetőségeit:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C# modulok LinuxAMD64-hez a VS-kódban](./media/tutorial-c-module/green-check.png) | ![C# modulok LinuxAMD64-hez a Visual Studióban](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C# modulok LinuxARM32-hez a VS-kódban](./media/tutorial-c-module/green-check.png) | ![C# modulok LinuxARM64-hez a Visual Studióban](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>A Linux ARM64-eszközök támogatása [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)érhető el. További információ: [ARM64 IoT Edge modulok fejlesztése és hibakeresése a Visual Studio Code programban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon a fejlesztői környezet, [egy IoT Edge modul fejlesztése Linux-eszközhöz](tutorial-develop-for-linux.md). Befejezése után, hogy az oktatóanyag, akkor már rendelkeznie kell a következő előfeltételeket:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Linux-eszköz.](quickstart-linux.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

Az alábbi oktatóanyagok befejezéséhez készítse elő a következő további előfeltételeket a fejlesztői gépen:

* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

A következő lépések hozzon létre egy IoT Edge modul projekt C# a Visual Studio Code és az Azure IoT Tools bővítmény használatával. Miután létrehozott egy projektsablont, adjon hozzá új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságaik alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat.

1. A Visual Studio-kód ban válassza a**Parancspaletta** **megtekintése** > lehetőséget a VS Code parancspaletta megnyitásához.

2. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, vagy fogadja el az alapértelmezett **EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **C# modul lehetőséget.** |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CSharpModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve az utolsó lépésben megadott névből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>A végső képtár \<háztár\>néz ki, mint a rendszerleíró adatbázis neve .azurecr.io/csharpmodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure-tároló beállításjegyzékének **Access-kulcsok** szakaszában található hitelesítő adatokat.

1. A VS Code intézőben nyissa meg az **.env** fájlt.
2. Frissítse a mezőket a **felhasználónév** és **a jelszó** értékeit az Azure container registry.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code C# modulokat fejleszthet Linux AMD64 és Linux ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert a tároló az egyes architektúratípusokhoz másképp en fut. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont.

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code explorer ben nyissa meg a**CSharpModule** > **Program.cs** **modulokat.** > 

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

5. Keresse meg az **Init** függvényt. Ez a függvény létrehoz és konfigurál egy **ModuleClient** objektumot, amely lehetővé teszi, hogy a modul csatlakozzon a helyi Azure IoT Edge futásidő üzenetek küldéséhez és fogadásához. A **ModuleClient** objektum létrehozása után a kód beolvassa a **temperatureThreshold** értéket a modul ikerdokumentumának kívánt tulajdonságaiból. A kód visszahívást regisztrál egy IoT Edge-központból érkező üzenetek fogadásához az **input1** végponton keresztül. Cserélje le a **SetInputMessageHandlerAsync** metódust egy másikra, és adjon hozzá egy **SetDesiredPropertyUpdateCallbackAsync** metódust a kívánt tulajdonságok frissítéséhez. A módosítás végrehajtásához cserélje le az **Init** metódus utolsó sorát az alábbi kódra:

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

9. A VS Code explorer, nyissa meg a **deployment.template.json** fájlt az IoT Edge-megoldás munkaterületén.

10. Adja hozzá a **CSharpModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **modulesContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Ikermodul hozzáadása a telepítési sablonhoz](./media/tutorial-csharp-module/module-twin.png)

11. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Építsd meg és nyomd meg a modult

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és kódot adott hozzá a CSharpModule-hoz. Az új kód kiszűri az üzeneteket, ahol a gép jelentett hőmérséklete az elfogadható határokon belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a VS Code integrált terminált a **Terminál megtekintése** > **Terminal**lehetőséget választva.

1. Jelentkezzen be a Dockerbe a következő parancs megadásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure-tároló beállításjegyzékéből. Ezeket az értékeket az Azure Portalon a beállításjegyzék **Access-kulcsok** szakaszából kérheti le.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, beépített információk a központi telepítési sablon és más megoldásfájlokat. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény segítségével telepítse a modulprojektet az IoT Edge-eszközre. Már rendelkezik egy központi telepítési jegyzékfájllal a forgatókönyvhöz, a **deployment.json** fájlhoz a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. A Visual Studio Kódkezelőben bontsa ki az **Azure IoT Hub-eszközök** szakaszt az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

4. Kattintson a frissítés gombra. Meg kell jelennie az új **CSharpModule** futó együtt **SimulatedTemperatureSensor** modul és a **$edgeAgent** és **$edgeHub**.  

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Kódkezelőjében kattintson a jobb gombbal az IoT Edge-eszköz nevére, és válassza **a Beépített eseményvégpont indítása parancsot.**

2. Tekintse meg az IoT Hubhoz érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mert az IoT Edge-eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután a CModule kódon végrehajtott módosítások megvárják, amíg a gép hőmérséklete eléri a 25 fokot, mielőtt üzeneteket küldene. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri az adott hőmérsékleti küszöbértéket.

   ![Az IoT Hubra érkező üzenetek megtekintése](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul szerkesztése iker

A CSharpModule modul ikerelemét használtuk a központi telepítési jegyzékben, hogy a hőmérsékleti küszöbértéket 25 fokra állítsuk be. Az ikermodul segítségével a modulkód frissítése nélkül módosíthatja a funkciót.

1. A Visual Studio-kódban bontsa ki a részleteket az IoT Edge-eszköz alatt a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal a **CSharpModule elemre,** és válassza **az Ikermodul szerkesztése parancsot.**

3. Keresse **meg a TemperatureThreshold** tulajdonságot a kívánt tulajdonságokban. Változtassa meg az értékét egy új hőmérséklet 5 fokkal 10 fokkal magasabb, mint a legutóbbi jelentett hőmérséklet.

4. Mentse a modul ikerfájlját.

5. Kattintson a jobb gombbal a modul ikerszerkesztő ablaktáblájának tetszőleges pontjára, és válassza **az Ikermodul frissítése parancsot**.

6. Figyelje a bejövő eszközről a felhőbe irányuló üzeneteket. Az új hőmérsékleti küszöbérték eléréséig meg kell jelennie az üzeneteknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások, amelyek ebben a cikkben a költségek elkerülése érdekében.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat [saját IoT Edge-modulok fejlesztéséről,](module-development.md) illetve arról, hogyan [fejleszthet modulokat a Visual Studio-kóddal.](how-to-vs-code-develop-module.md) Példák az IoT Edge-modulok, beleértve a szimulált hőmérséklet modul, lásd: [IoT Edge modul minták.](https://github.com/Azure/iotedge/tree/master/edge-modules)

Folytathatja a következő oktatóanyagokat, hogy megtudja, az Azure IoT Edge hogyan segíthet az Azure felhőszolgáltatásainak üzembe helyezésében a peremhálózati adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Egyéni vision szolgáltatás](tutorial-deploy-custom-vision.md)
