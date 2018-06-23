---
title: Az Azure IoT peremhálózati folyamatos integrációt és folyamatos üzembe helyezés |} Microsoft Docs
description: A folyamatos integrációt és Azure IoT szegély folyamatos üzembe helyezés – áttekintés
author: shizn
manager: ''
ms.author: xshi
ms.date: 04/30/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5fbac725894d01b2dc622d3542f865a66b240743
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333776"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge---preview"></a>Folyamatos integrációt és folyamatos üzembe helyezés Azure IoT szegélyhez – előzetes
Ez az oktatóanyag bemutatja, hogyan használhatja a folyamatos integrációt és a folyamatos üzembe helyezési funkcióival Visual Studio Team Services (VSTS) és a Microsoft Team Foundation Server (TFS) hozhat létre, tesztelheti, és gyors és hatékony az alkalmazások központi telepítése a Az Azure IoT peremhálózati. 

Az oktatóanyag során a következőket fogja elsajátítani:
> [!div class="checklist"]
> * Hozzon létre, és ellenőrizze, hogy szerepel egy minta IoT peremhálózati megoldás tartalmazó egység tesztek.
> * A VSTS az Azure IoT Edge-kiterjesztés telepítése.
> * Állítsa be a folyamatos integrációt (CI) a megoldás felépítéséhez és az egység tesztek futtatásához.
> * Konfigurálhatja a folyamatos telepítést (CD) a megoldás üzembe helyezéséhez és válaszok megtekintése.

Az oktatóanyag elvégzéséhez 30 percig tart.

![CI és a CD-ről](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Visual Studio Code használó minta Azure IoT peremhálózati megoldás létrehozása

Ebben a szakaszban egy minta IoT peremhálózati megoldás tartalmazó egység teszteket hajthat végre a felépítési folyamat részeként hoz létre. Ebben a szakaszban útmutatása előtt hajtsa végre a [egy IoT peremhálózati megoldást a Visual Studio Code több modulok](tutorial-multiple-modules-in-vscode.md).

1. A Visual STUDIO Code parancs paletta, írja be, és futtassa a parancsot **peremhálózati: új IoT peremhálózati megoldás**. Válassza ki a munkaterület mappában, a megoldás nevét adja meg (az alapértelmezett név az **EdgeSolution**), és hozzon létre egy C# modult (**FilterModule**) ebben a megoldásban az első felhasználó modulként. Az első modulhoz meg kell adnia a Docker rendszerképadattárat is. Az alapértelmezett lemezképtárba egy helyi Docker beállításjegyzék alapul (`localhost:5000/filtermodule`). Módosítania kell az Azure-tároló beállításjegyzék (`<your container registry address>/filtermodule`) vagy Docker kiválasztásával további folyamatos integrációt.

    ![A telepítő ACR](./media/how-to-ci-cd/acr.png)

2. A Visual STUDIO Code ablak betölti a IoT peremhálózati megoldás munkaterületen. Lehetősége van írja be és futtassa **peremhálózati: hozzáadása IoT peremhálózati modul** további modulok hozzáadása. Van egy `modules` mappa, egy `.vscode` mappa, de a központi telepítés jegyzék sablon fájl a gyökérmappában. Minden felhasználó modul kód lesz a mappa almappái `modules`. A `deployment.template.json` van a jegyzékfájl központi telepítési sablont. Az ebben a fájlban paraméterek némelyike fog értelmezni a `module.json`, amely minden modul mappa létezik.

3. A minta IoT peremhálózati megoldás most már készen áll. Az alapértelmezett C# modul cső üzenet modul funkcionál. Az a `deployment.template.json`, látni fogja, ez a megoldás két lehetővé tevő modulokat tartalmaz. Az üzenet a jön létre a `tempSensor` modul, és a rendszer kell közvetlenül adatcsatornán keresztül `FilterModule`, majd küldi tovább az IoT hub. Cserélje le a teljes **Program.cs** tartalom alatt fájlt. További információ a következő kódrészletet, olvassa el a [IoT peremhálózati C# modul projekt létrehozása](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

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
                // The Edge runtime gives us the connection string we need -- it is injected as an environment variable
                string connectionString = Environment.GetEnvironmentVariable("EdgeHubConnectionString");

                // Cert verification is not yet fully functional when using Windows OS for the container
                bool bypassCertVerification = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
                if (!bypassCertVerification) InstallCert();
                Init(connectionString, bypassCertVerification).Wait();

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
            /// Add certificate in local cert store for use by client for secure connection to IoT Edge runtime
            /// </summary>
            static void InstallCert()
            {
                string certPath = Environment.GetEnvironmentVariable("EdgeModuleCACertificateFile");
                if (string.IsNullOrWhiteSpace(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing path to certificate file.");
                }
                else if (!File.Exists(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing certificate file.");
                }
                X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
                store.Open(OpenFlags.ReadWrite);
                store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
                Console.WriteLine("Added Cert: " + certPath);
                store.Close();
            }
            /// <summary>
            /// Initializes the DeviceClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init(string connectionString, bool bypassCertVerification = false)
            {
                Console.WriteLine("Connection String {0}", connectionString);

                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                // During dev you might want to bypass the cert verification. It is highly recommended to verify certs systematically in production
                if (bypassCertVerification)
                {
                    mqttSetting.RemoteCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true;
                }
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                DeviceClient ioTHubModuleClient = DeviceClient.CreateFromConnectionString(connectionString, settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

                // Read TemperatureThreshold from Module Twin Desired Properties
                var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
                var moduleTwinCollection = moduleTwin.Properties.Desired;
                try {
                    temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
                } catch(ArgumentOutOfRangeException) {
                    Console.WriteLine("Proerty TemperatureThreshold not exist");
                }

                // Attach callback for Twin desired properties updates
                await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
            }

            static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
            {
                try
                {
                    Console.WriteLine("Desired property change:");
                    Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                    if (desiredProperties["TemperatureThreshold"] != null)
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

            static async Task<MessageResponse> FilterMessages(Message message, object userContext)
            {
                try
                {
                    DeviceClient deviceClient = (DeviceClient)userContext;

                    var filteredMessage = filter(message);
                    if (filteredMessage != null)
                    {
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
        }
    }
    ```

4. Hozzon létre egy .net Core egység teszt projekt. A Visual STUDIO Code Fájlkezelőben, hozzon létre egy új mappát **tests\FilterModuleTest** a munkaterületen. Ezt a Visual STUDIO Code integrált Terminálszolgáltatások (**Ctrl + "**), futtassa a következő parancsokat a xunit teszt projekt létrehozása és hozzáadása a **FilterModule** projekt.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Mappaszerkezet](./media/how-to-ci-cd/add-test-project.png)

5. Az a **FilterModuleTest** mappa, fájl nevének frissítése **UnitTest1.cs** való **FilterModuleTest.cs**. Válassza ki, és nyissa meg a **FilterModuleTest.cs**, cserélje le a teljes kódot az alábbi kódrészletet, amely tartalmazza a egység tesztek a FilterModule projekthez.

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

6. Integrált Terminálszolgáltatások következő egység tesztek futtatásához helyi parancsokat adhat meg. 
    ```cmd
    dotnet test
    ```

    ![Egységek tesztelése](./media/how-to-ci-cd/unit-test.png)

7. Mentse el ezeket a projekteket, majd jelölje be a tárház VSTS és a TFS-be.
    

> [!NOTE]
> VSTS használatáról további információt a code tárházak találhatók, lásd: [a kód megosztása a Visual Studio és a VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Folyamatos integráció konfigurálása
Ez a szakasz során létrehoz egy build-definíciót, amely a módosítások a mintához IoT peremhálózati megoldás verziókezelőbe mentésekor automatikusan futtatásra van beállítva, és azt automatikusan végrehajtja a benne található egység tesztek.

1. Jelentkezzen be a VSTS-fiókjával (**https://**_az fiók_**. visualstudio.com webhelyre**), majd nyissa meg a projekt, ha bejelölte a mintaalkalmazás.

    ![Ellenőrizze a kódot](./media/how-to-ci-cd/init-project.png)

1. Látogasson el [VSTS az Azure IoT peremhálózati](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) VSTS piactéren. Kattintson a **első szabad** , és kövesse a varázsló a bővítmény telepítéséhez a VSTS-fiók vagy a TFS letöltése.

    ![A bővítmény telepítése](./media/how-to-ci-cd/install-extension.png)

1. A VSTS, nyissa meg a **Build &amp; kiadás** hub és a a **buildek** lapra, majd **+ új definition**. Vagy, ha már build definíciókkal rendelkeznek, válassza ki azt a **+ új** gombra. 

    ![Új build](./media/how-to-ci-cd/add-new-build.png)

1. Ha a rendszer kéri, válassza ki a **VSTS Git** adatforrástípust; válassza ki a projekt, a tárházhoz és a fiókiroda, ahol a kódban. Válasszon **továbbra is**.

    ![Válassza ki a VSTS git](./media/how-to-ci-cd/select-vsts-git.png)

1. A **válasszon olyan sablont,** ablakban válasszon **egy üres folyamat kezdődnie**.

    ![Üresen](./media/how-to-ci-cd/start-with-empty.png)

1. Kattintson a **+** jobb oldalán található **1. fázis** feladat hozzáadása a fázisban. Kereshet, és válassza ki **.Net Core**, és kattintson a **Hozzáadás** Ez a feladat hozzáadása a fázisban.

    ![DotNet tesztelése](./media/how-to-ci-cd/add-dot-net-core.png)

1. Frissítés a **megjelenített név** való **dotnet teszt**, majd a a **parancs** legördülő listában válassza ki **tesztelése**. Az elérési út alatt adja hozzá a **elérési útját projekt(ek)**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Dotnet teszt konfigurálása](./media/how-to-ci-cd/dotnet-test.png)

1. Kattintson a **+** jobb oldalán található **1. fázis** feladat hozzáadása a fázisban. Kereshet, és válassza ki **Azure IoT peremhálózati**, és kattintson a **hozzáadása** gomb **kétszer** feladatok hozzáadása a fázisban.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Az első Azure IoT peremhálózati feladat, frissítse a **megjelenített név** való **modul készítse el és leküldéses**, és a a **művelet** legördülő listában válassza ki **hozza létre, és leküldéses**. Az a **Module.json fájl** szövegmezőhöz felvétele alatti elérési utat. Válassza a **tároló beállítástípus**, ellenőrizze, hogy konfigurálja, és válassza ki ugyanazt a beállításjegyzékben a kódban. Ez a feladat elkészíti és a modulok leküldéses a megoldás és a megadott tároló beállításjegyzék közzététele. 

    ```
    **/module.json
    ```

    ![A modul Build és leküldéses](./media/how-to-ci-cd/module-build-push.png)

1. A második Azure IoT peremhálózati feladat, frissítse a **megjelenített név** való **az IoT-peremhálózati eszköz telepítéséhez**, majd a a **művelet** legördülő listában válassza ki **az IoT-Edge telepítéséhez eszköz**. Válassza ki az Azure-előfizetéshez, és adjon meg az IoT Hub nevét. Megadhat egy IoT peremhálózati telepítési Azonosítót és a központi telepítési prioritás. Választhatja azt is, egy vagy több eszközre telepíteni. Ha több eszközre telepít, meg kell adnia az eszköz cél feltétel. Például ha az eszköz címkék feltételként használni kívánt, módosítania a megfelelő eszközök címkék a központi telepítés előtt. 

    ![Peremhálózati telepítése](./media/how-to-ci-cd/deploy-to-edge.png)

1. Kattintson a **folyamat** , és győződjön meg arról, hogy a **ügynök várólista** van **üzemeltetett Linux előzetes**.

    ![Konfigurálás](./media/how-to-ci-cd/configure-env.png)

1. Nyissa meg a **eseményindítók** lapra, és kapcsolja be a **folyamatos integrációt** eseményindító. Ellenőrizze, hogy a fiókirodák a kódot tartalmazó része.

    ![Eseményindító](./media/how-to-ci-cd/configure-trigger.png)

1. Mentse az új build definícióban, és egy új build várólistára. Kattintson a **mentés és a feldolgozási sor** gombra.

1. Adja meg a hivatkozásra kattintva a build üzenet sávon. Vagy válassza a definíció, tekintse meg a legújabb aszinkron összeállítási feladat létrehozásához.

    ![Felépítés](./media/how-to-ci-cd/build-def.png)

1. A build befejeződését követően megjelenik az összefoglalása-minden tevékenység és a működés közbeni naplófájlt eredményez. 
    
    ![Befejezve](./media/how-to-ci-cd/complete.png)

1. Vissza a Visual STUDIO Code, és ellenőrizze az IoT Hub eszköz explorer. A peremhálózati eszköz, a modul webalkalmazásokba fut (Győződjön meg arról, hogy él futásidejű hozzáadott beállításjegyzék hitelesítő adatok).

    ![Peremhálózati fut](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Az IoT-peremeszközök folyamatos üzembe helyezés

Ahhoz, hogy a folyamatos üzembe helyezés, alapvetően kell megfelelő IoT peremeszközök, állítsa be a CI-feladatok engedélyezése a **eseményindítók** az elágazásokhoz a projekt esetében. A gyakorlatban egy klasszikus DevOps egy projektben két fő ág. A főágba kell a kódot a stabil verzióját, és a develop fiókirodai tartalmazza a legújabb kódmódosításokat. A csoport összes fejlesztő develop ág számára annak kell forkolhatja, vagy ki a develop fiókirodai elágazik saját szolgáltatás fiókirodai, ha a szolgáltatás indítása a kódot, vagyis minden véglegesíti frissítése történik. És minden egyes megnyomott véglegesítés után meg kell vizsgálni a CI rendszeren keresztül. Teljes körűen tesztelve helyileg a kódot, miután a szolgáltatás fiók össze kell vonni a develop ágra keresztül egy lekérési kérelmet. A fejlesztői fiókirodai kód tesztelésekor CI rendszeren keresztül egyesíthető keresztül egy lekérési kérelmet a főághoz.

Igen IoT peremeszközök telepítésekor nincsenek három fő környezetekben.
- A szolgáltatás fiókirodai szimulált IoT peremhálózati eszköz a fejlesztési számítógépen használhatja, vagy telepítheti egy fizikai IoT peremhálózati eszközön.
- A fejlesztése a fiókiroda, telepítsen egy fizikai IoT peremhálózati eszközön.
- A főágba a cél IoT peremeszközök kell az eszközökre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatja, hogyan használhatja a folyamatos integrációt és a folyamatos üzembe helyezési funkcióival VSTS vagy TFS. 

* IoT peremhálózati telepítését megértéséhez [megértéséhez IoT peremhálózati telepítések egyetlen eszközökhöz vagy léptékű](module-deployment-monitoring.md)
* A létrehozása, frissítése vagy törlése a központi telepítés lépéseit ismerteti [központi telepítése és figyelése IoT peremhálózati modulok léptékű](how-to-deploy-monitor.md).









