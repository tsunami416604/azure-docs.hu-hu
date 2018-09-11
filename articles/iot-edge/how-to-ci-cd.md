---
title: Az Azure IoT Edge folyamatos integrációs és folyamatos üzembe helyezés |} A Microsoft Docs
description: A folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge – áttekintés
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5099ca70503ba2ed4ae8f4969a9199816c4986fb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302571"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Folyamatos integráció és folyamatos üzembe helyezés az Azure IoT Edge-ben

Ez a cikk bemutatja, hogyan lehet a a folyamatos integráció és folyamatos üzembe helyezési funkcióival az Azure DevOps-szolgáltatásokkal és a Microsoft Team Foundation Server (TFS) hozhat létre, tesztelheti és gyorsan és hatékonyan helyezhetnek üzembe alkalmazásokat az Azure IoT Edge-ben. 

Ez a cikk azt ismerteti, hogyan lehet:
* Hozzon létre, és ellenőrizze a minta az IoT Edge-megoldás tartalmazó egység teszteket.
* Az Azure IoT Edge-bővítmény telepítése az Azure fejlesztők és üzemeltetők számára.
* Állítsa be a folyamatos integrációs (CI) a megoldás felépítéséhez és futtatni az egységteszteket.
* Állítsa be a folyamatos készregyártás (CD) a megoldás üzembe helyezése, és megtekintheti a válaszokat.

A jelen cikkben ismertetett lépések 30 percet vesz igénybe.

![CI és CD-ről](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Hozzon létre egy minta Azure IoT Edge megoldást a Visual Studio Code használatával

Ebben a szakaszban létrehoz egy mintául szolgáló IoT Edge megoldást tartalmazó egységteszteket, amely az összeállítási folyamat részeként hajthat végre. Ebben a szakaszban található útmutatást követve, előtt hajtsa végre a [egyszerre több modul a Visual Studio Code egy IoT Edge-megoldás fejlesztése](tutorial-multiple-modules-in-vscode.md).

1. A VS Code parancskatalógus, írja be, és futtassa a parancsot **Edge: IoT-Edge új megoldás**. Válassza ki a munkaterület-mappába, adja meg a megoldás nevét (alapértelmezés szerint ez **EdgeSolution**), és hozzon létre egy C# modul (**FilterModule**) Ez a megoldás első felhasználói modulként. Az első modulhoz meg kell adnia a Docker rendszerképadattárat is. Az alapértelmezett lemezképtárban alapul egy helyi Docker-beállításjegyzék (`localhost:5000/filtermodule`). Módosítania azt az Azure Container Registrybe (`<your container registry address>/filtermodule`) vagy a Docker Hub további folyamatos integrációhoz.

    ![A telepítő ACR](./media/how-to-ci-cd/acr.png)

2. A VS Code-ablak betölti az IoT Edge-megoldás munkaterülethez. Szükség esetén írja be és futtassa **Edge: hozzáadása IoT Edge-modul** további modulok hozzáadása. Van egy `modules` mappában egy `.vscode` mappát, és a egy központi telepítési jegyzékfájl sablon fájl a gyökérmappában. Minden felhasználó modul kód lesz a mappa almappái `modules`. A `deployment.template.json` az alkalmazásjegyzék központi telepítési sablon. Egyes paraméterek a fájlban a fog értelmezni a `module.json`, amely minden modul mappában van.

3. A minta az IoT Edge-megoldást most már készen áll. Az alapértelmezett C# modul egy függőleges vonal üzenet-modult funkcionál. Az a `deployment.template.json`, látni fogja, ez a megoldás két lehetővé tevő modulokat tartalmaz. Az üzenet akkor jöjjön létre a `tempSensor` modult, és a rendszer kell közvetlenül adatcsatornán keresztül `FilterModule`, majd az IoT hubnak küldött. Cserélje le a teljes **Program.cs** fájlt az alábbi tartalmat. Ez a kódrészlet kapcsolatos további információkért olvassa el [hozzon létre egy IoT Edge C# modul projektet](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

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
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. Hozzon létre egy .net Core egység tesztelő projektet. A VS Code a Fájlkezelőben, hozzon létre egy új mappát **tests\FilterModuleTest** a munkaterületén. Ezt a VS Code integrált termináljában (**Ctrl + '**) futtassa a következő parancsokat, hozzon létre egy xunit tesztelő projektet, és hozzá hivatkozást a **FilterModule** projekt.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Gyökérmappa-szerkezetében](./media/how-to-ci-cd/add-test-project.png)

5. Az a **FilterModuleTest** mappa, fájl nevének frissítéséhez **UnitTest1.cs** való **FilterModuleTest.cs**. Válassza ki, és nyissa meg a **FilterModuleTest.cs**, cserélje le a teljes kódot az alábbi kódrészletet, amely tartalmazza a FilterModule projekthez az egységteszteket.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. Az integrált terminálon adhatja meg az alábbi parancsokat a helyileg futtatni az egységteszteket. 
    ```cmd
    dotnet test
    ```

    ![Test jednotky](./media/how-to-ci-cd/unit-test.png)

7. Ezek a projektek mentse, majd ellenőrizze azt az Azure DevOps, vagy a TFS-adattárba.
    

> [!NOTE]
> Az Azure-kódtárak használatával kapcsolatos további információkért lásd: [megosztani a kódot a Visual Studio és az Azure-Adattárakkal](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Folyamatos integráció konfigurálása
Ebben a szakaszban létre fog hozni egy build folyamatot, amely automatikusan futnak, amikor ellenőrizheti a módosításokat a minta az IoT Edge-megoldás van beállítva, és automatikusan fogja végrehajtani, benne az egységteszteket.

1. Jelentkezzen be az Azure DevOps-szervezet (**https://**_-fiók_**. visualstudio.com**), és nyissa meg a projekt, ha bejelölte a mintaalkalmazást.

    ![Ellenőrizze a kód](./media/how-to-ci-cd/init-project.png)

1. Látogasson el [Azure fejlesztők és üzemeltetők számára az Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) DevOps Azure Marketplace-en. Kattintson a **regisztráljon ingyenesen** , és kövesse a varázsló az Azure DevOps-szervezet és a letöltés a TFS, a bővítmény telepítéséhez.

    ![A bővítmény telepítése](./media/how-to-ci-cd/install-extension.png)

1. Az Azure DevOps, nyissa meg a **hozhat létre &amp; kiadási** hub és az a **buildek** lapra, majd **+ új adatcsatorna**. Vagy, ha már rendelkezik hozhat létre folyamatokat, válassza ki a **+ új** gombra. 

    ![Új build](./media/how-to-ci-cd/add-new-build.png)

1. Ha a rendszer kéri, válassza ki a **Azure DevOps Git** adatforrástípust; válassza ki a projektet, a tárházat és a fiókiroda, ahol a kód megtalálható. Válasszon **továbbra is**.

    ![Válassza ki az Azure DevOps-git](./media/how-to-ci-cd/select-vsts-git.png)

1. A **válasszon ki egy sablont** ablakban válassza a **egy üres folyamatot kezdődnie**.

    ![Üresen](./media/how-to-ci-cd/start-with-empty.png)

1. Kattintson a **+** jobb oldalán található **1. fázis** feladat hozzáadása a fázis. Majd keresse meg és válassza ki **.Net Core**, és kattintson a **hozzáadása** szerenté felvenni ezt feladatot fázisa.

    ![DotNet teszt](./media/how-to-ci-cd/add-dot-net-core.png)

1. Frissítés a **megjelenített név** való **dotnet teszt**, majd a a **parancs** legördülő listában válassza **tesztelése**. Adja hozzá az elérési úton a **elérési útját projekt(ek)**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Dotnet teszt konfigurálása](./media/how-to-ci-cd/dotnet-test.png)

1. Kattintson a **+** jobb oldalán található **1. fázis** feladat hozzáadása a fázis. Majd keresse meg és válassza ki **Azure IoT Edge**, és kattintson a **Hozzáadás** gomb **kétszer** ezek a feladatok hozzáadása a fázis.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Az első Azure IoT Edge-feladat frissítése a **megjelenített neve** való **modul létrehozása és leküldése**, majd a a **művelet** legördülő listában válassza **ésleküldéses**. Az a **Module.json fájl** szövegmezőbe elérési úton kell hozzáadnia. Válassza a **Tárolóregisztrációs adatbázis típusa**, ellenőrizze, hogy konfigurálja, és válassza ki ugyanazt a beállításjegyzékben a kódban. Ez a feladat létrehozása és leküldése az összes modult a megoldásban és közzététele a megadott tárolóregisztrációs adatbázisba. Ha a modul el lesz küldve különböző beállításjegyzékek, rendelkezhet több **modul létrehozása és leküldése** feladatokat.

    ```
    **/module.json
    ```

    ![A modul a Build és a leküldéses](./media/how-to-ci-cd/module-build-push.png)

1. A második Azure IoT Edge-feladat, frissítse a **megjelenített név** való **üzembe helyezés az IoT Edge-eszköz**, majd a a **művelet** legördülő listában válassza **üzembe helyezés az IoT Edge eszköz**. Válassza ki az Azure-előfizetéshez, és adjon meg az IoT Hub nevére. Az IoT Edge üzemelő példány azonosítója és az üzembe helyezési prioritás is megadhat. Azt is beállíthatja egy vagy több eszközökre történő telepítéséhez. Ha több eszközre telepít, adja meg az eszköz célfeltétel szeretné. Például ha azt szeretné, eszköz-címkék használata a feltételt, frissítenie a megfelelő eszközök címkék az üzembe helyezés előtt. 

    ![Edge-ben üzembe helyezése](./media/how-to-ci-cd/deploy-to-edge.png)

1. Kattintson a **folyamat** , és győződjön meg arról, hogy a **fronta Agenta** van **üzemeltetett Linux előzetes**.

    ![Konfigurálás](./media/how-to-ci-cd/configure-env.png)

1. Nyissa meg a **eseményindítók** lapra, és kapcsolja be a **folyamatos integráció** eseményindító. Ellenőrizze, hogy az ág a kódot tartalmazó részét képezi.

    ![Eseményindító](./media/how-to-ci-cd/configure-trigger.png)

1. Mentse az új buildelési folyamat, és a egy új build várólistára. Kattintson a **várólistára & mentése** gombra.

1. Válassza ki a hivatkozásra kattintva a build megjelenő üzenet sávon. Vagy keresse fel a buildelési folyamat megtekintéséhez a legújabb várólistára helyezett fordítási feladatot.

    ![Felépítés](./media/how-to-ci-cd/build-def.png)

1. A létrehozás befejezése után, megnézzük az összesítést, mindegyik tevékenység és az élő naplófájlt eredményez. 
    
    ![Befejezve](./media/how-to-ci-cd/complete.png)

1. Lépjen vissza a VS Code, és ellenőrizze az IoT Hub device explorer. A peremhálózati eszköz, a modul el kell indulnia, fut (Győződjön meg arról, hogy a tárolójegyzék hitelesítő adatainak hozzáadott az Edge-futtatókörnyezet).

    ![Futó Edge](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>IoT Edge-eszközökön való folyamatos üzembe helyezés

Folyamatos üzembe helyezés engedélyezése alapvetően kell állítsa be a CI-feladatok megfelelő IoT Edge-eszközök engedélyezése a **eseményindítók** a projektben az ágak esetén. A gyakorlatban egy klasszikus DevOps-projekt tartalmazza a két fő ág. A főág kell lennie a kód stabil verzióját, és a develop ág tartalmazza a legújabb kódmódosításokat. A csoport minden fejlesztőnek kell ágaztatnia a develop ág számára annak, vagy saját funkció ágat, ha a szolgáltatás indítása a kódot, ami azt jelenti, minden véglegesítés frissítése történik a develop ág ágazik. És minden egyes leküldött véglegesítési kell tesztelni a CI-rendszeren keresztül. Teljes körűen tesztelve helyileg a kódot, miután a szolgáltatás ág össze kell vonni a develop ágra keresztül irányuló lekéréses kérelmet. A kód a fejlesztői ág tesztelésekor a CI-rendszer keresztül egyesíthető főágba irányuló lekéréses kérelmet keresztül.

Tehát üzembe helyezése IoT Edge-eszközökön, ha nincsenek három fő környezetekben.
- A szolgáltatás ágon szimulált IoT Edge-eszköz használata a fejlesztési számítógépén, vagy fizikai IoT Edge-eszköz üzembe helyezése.
- Ág a fejlesztése, üzembe kell helyeznie egy fizikai IoT Edge-eszköz.
- A master ággal a cél IoT Edge-eszközök a gyártási eszközöket kell lennie.

## <a name="next-steps"></a>További lépések

* Megismerheti az IoT Edge üzemelő példány [ismertetése IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben](module-deployment-monitoring.md)
* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md).
