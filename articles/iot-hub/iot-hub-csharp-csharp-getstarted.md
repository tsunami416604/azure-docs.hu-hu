---
title: "Ismerkedés az Azure IoT Hub (.NET) szolgáltatással | Microsoft Docs"
description: "Megtudhatja, hogyan küldhet eszközről a felhőbe irányuló üzeneteket az Azure IoT Hubba a .NET-hez készült IoT SDK-k használatával. Szimulált eszközt és szolgáltatásalkalmazásokat hozhat létre az eszköz regisztrálásához, üzenetek küldéséhez és üzenetek olvasásához az IoT Hubról."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24b75e4021d9d16aed317b48c2f963ff9dea04ac
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>Az eszköz csatlakoztatása az IoT Hubhoz .NET-tel

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Az oktatóanyag végén három .NET-konzolalkalmazással fog rendelkezni:

* A **CreateDeviceIdentity** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat az eszközalkalmazáshoz.
* A **ReadDeviceToCloudMessages** megjeleníti az eszközalkalmazások által küldött telemetriát.
* A **SimulatedDevice** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és az MQTT protokoll használatával másodpercenként telemetriai üzenetet küld.

Letöltheti vagy klónozhatja a Githubról származó három alkalmazást tartalmazó Visual Studio-megoldást.

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Létrehozta a IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati karakterlánccal.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek fogadása
Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Azure Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs-üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. (Ez az oktatóanyag az IoT Hub Event Hub-kompatibilis végpontokra vonatkozik.)

> [!NOTE]
> Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hub-kompatibilis végpontok mindig az AMQP protokollt használják.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **ReadDeviceToCloudMessages** nevet.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10a]

2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **ReadDeviceToCloudMessages** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban keresse meg a **WindowsAzure.ServiceBus** csomagot, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure Service Bus][lnk-servicebus-nuget] (Azure szolgáltatásbusz) elemet minden függőségével, és hozzáad egy rá mutató hivatkozást is. A csomag lehetővé teszi az alkalmazás számára, hogy csatlakozzon az Event Hub-kompatibilis végponthoz az IoT Hubon.

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az „IoT Hub létrehozása” című szakaszban létrehozott IoT Hub kapcsolati karakterláncra.

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

6. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
    ```

    Ez a metódus egy **EventHubReceiver** példánnyal fogadja az üzeneteket az IoT Hub összes eszközről a felhőbe irányuló fogadási partíciójáról. Figyelje meg, hogy az **EventHubReceiver** objektum létrehozásakor a rendszer átad egy `DateTime.Now` paramétert, így csak az elindítása után küldött üzeneteket fogja fogadni. Ez a szűrő tesztkörnyezetben hasznos, mivel Ön láthatja vele az aktuális üzeneteket. Éles környezetben azonban a kódnak gondoskodnia kell az összes üzenet feldolgozásáról. További információkért lásd az [eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

7. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
    ```

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre, amely egy, az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **SimulatedDevice** nevet.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10b]

2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **SimulatedDevice** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **Microsoft.Azure.Devices.Client** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices.Client** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT – Device SDK ][lnk-device-nuget] (Azure IoT – eszközoldali SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

4. Adja hozzá a következő `using` utasítást a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Az `{iot hub hostname}` értéket cserélje le az IoT Hub létrehozását ismertető szakaszban lekért IoT Hub-gazdanévre. A `{device key}` értéket cserélje le az Eszközidentitás létrehozását ismertető szakaszban lekért eszközkulcsra.

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

6. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    Ez a metódus másodpercenként elküld egy új, az eszközről a felhőbe irányuló üzenetet. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával és véletlenszerűen előállított számokkal, amelyek egy hőmérséklet- és egy páratartalom-érzékelőt szimulálnak.

7. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);

    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    Alapértelmezés szerint a **Create** metódus a .NET-keretrendszerre épülő alkalmazásokban létrehoz egy **DeviceClient** példányt, amely az AMQP protokollt használja az IoT Hubbal való kommunikációhoz. Az MQTT vagy a HTTPS protokoll használatához használja a **Create** metódus felülbírálását, amely lehetővé teszi a protokoll megadását. Az UWP- és a PLC-ügyfelek alapértelmezés szerint a HTTPS protokollt használják. Ha a HTTPS protokollt használja, hozzá kell adnia a **Microsoft.AspNet.WebApi.Client** NuGet-csomagot a projekthez, hogy tartalmazza a **System.Net.Http.Formatting** névteret.

Ez az oktatóanyag végigvezeti egy IoT Hub-eszközalkalmazás létrehozásának lépésein. A [Connected Service for Azure IoT Hub][lnk-connected-service] (Csatlakoztatott szolgáltatás Azure IoT Hubhoz) Visual Studio-bővítmény használatával is hozzáadhatja a szükséges kódot az eszközalkalmazáshoz.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A Visual Studióban a Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Set StartUp projects** (Indítási projektek beállítása) parancsra. Válassza a **Multiple startup projects** (Több indítási projekt) lehetőséget, majd válassza a **Start** (Indítás) elemet a **ReadDeviceToCloudMessages** és a **SimulatedDevice** projekt műveleteként.

    ![Indítási projektek tulajdonságai][41]

2. Nyomja le az **F5** billentyűt a két alkalmazás futtatásához. A **SimulatedDevice** alkalmazás konzolkimenete az eszközalkalmazás által az IoT Hubnak küldött üzeneteket jeleníti meg. A **ReadDeviceToCloudMessages** alkalmazás konzolkimenete az IoT Hub által fogadott üzeneteket jeleníti meg.

    ![Az alkalmazások konzolkimenetei][42]

3. Az [Azure Portal][lnk-portal] **Használat** csempéje az IoT Hubnak küldött üzenetek számát jeleníti meg:

    ![Az Azure Portal Használat csempéje][43]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy IoT Hubot konfigurált az Azure Portalon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette az eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön az IoT Hubnak. Emellett létrehozott egy alkalmazást, amely megjeleníti az IoT Hub által fogadott üzeneteket.

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Kapcsolódás az eszközhöz][lnk-connect-device]
* [Eszközfelügyelet – első lépések][lnk-device-management]
* [Ismerkedés az IoT Edge szolgáltatással][lnk-iot-edge]

Az IoT-megoldás kibővítésével és az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban tekintse meg [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
