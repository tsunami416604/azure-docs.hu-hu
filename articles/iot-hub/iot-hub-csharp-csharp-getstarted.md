<properties
    pageTitle="Az Azure IoT Hub használata a C# környezettel – Első lépések | Microsoft Azure"
    description="Az Azure IoT Hub használata a C# környezettel – Első lépéseket ismertető oktatóanyag | Microsoft Azure Az Azure IoT Hubot és a C# környezetet a Microsoft Azure IoT SDK-kal együtt használva az eszközök internetes hálózatán alapuló megoldást valósíthat meg."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Ismerkedés az Azure IoT Hub for .NET szolgáltatással

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Bevezetés

Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé az eszközök internetes hálózatához (IoT) csatlakozó több millió eszköz között, valamint megoldást biztosít a háttérrendszer kialakításához. Az IoT-projektek számára az egyik legnagyobb kihívást az jelenti, hogyan lehet megbízható és biztonságos módon csatlakoztatni az eszközöket a megoldás háttérrendszeréhez. A kihívás megoldására az IoT Hub a következőket kínálja:

- Megbízható nagy kapacitású üzenetkezelést kínál az eszközök és a felhő között mindkét irányban.
- Az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés segítségével lehetővé teszi a biztonságos kommunikációt.
- Tartalmazza a legnépszerűbb nyelvek és platformok eszközkönyvtárait.

Ez az oktatóanyag a következőket mutatja be:

- Egy IoT Hub létrehozása az Azure Portallal.
- Eszközidentitás létrehozása az IoT Hubban.
- Egy szimulált eszköz létrehozása, amely telemetriai adatokat küld a felhő háttérrendszerének, és parancsokat fogad a felhő háttérrendszeréből.

Az oktatóanyag végén három Windows-konzolalkalmazással fog rendelkezni:

* A **CreateDeviceIdentity** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközhöz.
* A **ReadDeviceToCloudMessages** megjeleníti a szimulált eszköz által küldött telemetriát.
* A **SimulatedDevice** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és másodpercenként telemetriai üzenetet küld az AMQPS protokoll használatával.

> [AZURE.NOTE] Különböző SDK-kat használhat az alkalmazások összeállításához, hogy eszközökön és a megoldás háttérrendszerén is fussanak. Ezekről az [IoT Hub SDK-k][lnk-hub-sdks] című témakörben talál további információt.

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

+ Microsoft Visual Studio 2015.

+ Aktív Azure-fiók. (Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Létrehozott egy IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és kapcsolati karakterlánccal.

## Eszközidentitás létrehozása

Ebben a szakaszban egy Windows-konzolalkalmazást fog létrehozni, amely egy új eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközidentitás-jegyzékbe. További információkért lásd az [Azure IoT Hub fejlesztői útmutató][lnk-devguide-identity] eszközidentitás-jegyzékekről szóló szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

1. A Visual Studióban adjon hozzá egy új Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **CreateDeviceIdentity** nevet.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]

2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **CreateDeviceIdentity** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez letölti és telepíti a [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] (Microsoft Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][11]

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.

        static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Adja hozzá a **Program** osztályhoz a következő módszert:

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Ez a metódus egy új eszközidentitást hoz létre a **myFirstDevice** azonosítóval. (Ha ez az eszköz már létezik a nyilvántartásban, a kód egyszerűen lekéri a meglévő eszközinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált eszközön fogja használni az IoT Hubhoz való csatlakozáshoz.

7. Végül adja a következő sorokat a **Main** metódushoz:

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Futtassa az alkalmazást, és jegyezze fel az eszköz kulcsát.

    ![Az alkalmazás által előállított eszközkulcs][12]

> [AZURE.NOTE] Az IoT Hub-identitásjegyzék csak a hub biztonságos elérésének biztosításához tárolja az eszközidentitást. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd az [Azure IoT Hub fejlesztői útmutatót][lnk-devguide-identity].

## Az eszközről a felhőbe irányuló üzenetek fogadása

Ebben a szakaszban egy Windows-konzolalkalmazást fog létrehozni, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Azure Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs-üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. (Ez az oktatóanyag az IoT Hub Event Hubs-kompatibilis végpontokra vonatkozik.)

> [AZURE.NOTE] Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hubs-kompatibilis végpontok mindig az AMQPS protokollt használják.

1. A Visual Studióban adjon hozzá egy új Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **ReadDeviceToCloudMessages** nevet.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]

2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **ReadDeviceToCloudMessages** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban keresse meg a **WindowsAzure.ServiceBus** csomagot, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket. Ez letölti és telepíti az [Azure Service Bus][lnk-servicebus-nuget] (Azure szolgáltatásbusz) elemet minden függőségével, és hozzáad egy rá mutató hivatkozást is. A csomag lehetővé teszi az alkalmazás számára, hogy csatlakozzon az Event Hubs-kompatibilis végponthoz az IoT Hubon.

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az „IoT Hub létrehozása” című szakaszban létrehozott IoT Hub kapcsolati karakterláncra.

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Adja hozzá a **Program** osztályhoz a következő módszert:

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

    Ez a metódus egy **EventHubReceiver** példánnyal fogadja az üzeneteket az IoT Hub összes eszközről a felhőbe irányuló fogadási partíciójáról. Figyelje meg, hogy az **EventHubReceiver** objektum létrehozásakor a rendszer átad egy `DateTime.Now` paramétert, így csak az elindítása után küldött üzeneteket fogja fogadni. Ez tesztkörnyezetben hasznos, mivel láthatja az üzenetek aktuális készletét, éles környezetben azonban a kódnak biztosítania kell, hogy az összes üzenetet feldolgozza. További információkért lásd az [eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

7. Végül adja a következő sorokat a **Main** metódushoz:

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

## Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Windows-konzolalkalmazást fog létrehozni, amely az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.

1. A Visual Studióban adjon hozzá egy új Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **SimulatedDevice** nevet.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]

2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **SimulatedDevice** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **Microsoft.Azure.Devices.Client** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices.Client** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez letölti és telepíti az [Azure IoT – Device SDK ][lnk-device-nuget] (Azure IoT – Eszköz SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

4. Adja hozzá a következő `using` utasítást a **Program.cs** fájl elejéhez:

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrzők értékeit helyettesítse az „IoT Hub létrehozása” című szakaszban lekért IoT Hub-állomásnévre, illetve az „Eszközidentitás létrehozása” szakaszban lekért eszközkulcsra.

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Adja hozzá a **Program** osztályhoz a következő módszert:

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Ez a metódus másodpercenként elküld egy új, az eszközről a felhőbe irányuló üzenetet. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával és egy véletlenszerűen előállított számmal, amely egy szélsebesség-érzékelőt szimulál.

7. Végül adja a következő sorokat a **Main** metódushoz:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  A **Create** metódus alapértelmezés szerint létrehoz egy **DeviceClient** példányt, amely az AMQP protokollt használja az IoT Hubbal való kommunikációra. A HTTPS protokoll használatához használja a **Create** metódus felülírását, amely lehetővé teszi a protokoll megadását. Ha a HTTPS protokollt használja, hozzá kell adnia a **Microsoft.AspNet.WebApi.Client** NuGet-csomagot a projekthez, hogy tartalmazza a **System.Net.Http.Formatting** névteret.

Ez az oktatóanyag végigvezeti egy IoT Hub-eszközügyfél létrehozásának lépésein. A [Connected Service for Azure IoT Hub][lnk-connected-service] (Csatlakoztatott szolgáltatás Azure IoT Hubhoz) Visual Studio-bővítmény használatával is hozzáadhatja a szükséges kódot az eszközügyfél-alkalmazáshoz.


> [AZURE.NOTE] Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.

## Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1.  A Visual Studióban a Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Set StartUp projects** (Indítási projektek beállítása) parancsra. Válassza a **Multiple startup projects** (Több indítási projekt) lehetőséget, majd válassza ki a **Start** (Indítás) elemet a **ProcessDeviceToCloudMessages** és a **SimulatedDevice** projektek műveleteként.

    ![Indítási projektek tulajdonságai][41]

2.  Nyomja le az **F5** billentyűt a két alkalmazás futtatásához. A **SimulatedDevice** alkalmazás konzolkimenete a szimulált eszköz által az IoT Hubnak küldött üzeneteket jeleníti meg. A **ProcessDeviceToCloudMessages** alkalmazás konzolkimenete az IoT Hub által fogadott üzeneteket jeleníti meg.

    ![Az alkalmazások konzolkimenetei][42]

3. Az [Azure Portal][lnk-portal] **Használat** csempéje a hubnak küldött üzenetek számát jeleníti meg:

    ![Az Azure Portal Használat csempéje][43]


## Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált a portálon, majd létrehozott egy eszközidentitást a hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön a hubnak. Emellett létrehozott egy alkalmazást, amely megjeleníti a hub által fogadott üzeneteket. A következő oktatóanyagokban folytathatja az IoT Hub szolgáltatásainak, valamint további IoT-forgatókönyveknek a felfedezését:

- [A felhőből az eszközre irányuló üzenetek IoT Hubbal való küldését][lnk-c2d-tutorial] ismertető oktatóanyagból megtudhatja, hogyan küldhet üzeneteket eszközökre, és hogyan dolgozhatja fel az IoT Hub által előállított kézbesítési visszajelzéseket.
- [Az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyag bemutatja, hogyan dolgozhatók fel megbízhatóan az eszközökről érkező telemetriai és interaktív üzenetek.
- A [Fájlok feltöltése eszközökről][lnk-upload-tutorial] című oktatóanyag egy mintázatot ismertet, amelyet a felhőből az eszközre irányuló üzenetek használnak az eszközökről történő fájlfeltöltések elősegítésére.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6



<!--HONumber=jun16_HO2-->


