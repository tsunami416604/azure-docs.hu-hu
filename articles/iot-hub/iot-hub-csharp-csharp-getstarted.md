---
title: "Az Azure IoT Hub használata a C# környezettel – Első lépések | Microsoft Docs"
description: "Az Azure IoT Hub használata a C# környezettel – Első lépéseket ismertető oktatóanyag | Microsoft Azure Az Azure IoT Hubot és a C# környezetet a Microsoft Azure IoT SDK-kal együtt használva az eszközök internetes hálózatán alapuló megoldást valósíthat meg."
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
ms.date: 09/12/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97c5d3c3e78ce6ec23f260bd26a0b41dc7c7197a


---
# <a name="get-started-with-azure-iot-hub-for-net"></a>Ismerkedés az Azure IoT Hub for .NET szolgáltatással
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Az oktatóanyag végén három Windows-konzolalkalmazással fog rendelkezni:

* A **CreateDeviceIdentity** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközhöz.
* A **ReadDeviceToCloudMessages** megjeleníti a szimulált eszköz által küldött telemetriát.
* A **SimulatedDevice** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és másodpercenként telemetriai üzenetet küld az AMQP protokoll használatával.

> [!NOTE]
> Különböző SDK-kat használhat az alkalmazások összeállításához, hogy eszközökön és a megoldás háttérrendszerén is fussanak. Ezekről az [IoT Hub SDK-k][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Microsoft Visual Studio 2015.
* Aktív Azure-fiók. (Ha nincs fiókja, néhány perc alatt létrehozhat egy [ ingyenes próbafiókot][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Létrehozott egy IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és kapcsolati karakterlánccal.

## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása
Ebben a szakaszban egy Windows-konzolalkalmazást fog létrehozni, amely egy eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközidentitás-jegyzékbe. További információkért lásd az [Azure IoT Hub fejlesztői útmutató][lnk-devguide-identity] eszközidentitás-jegyzékekről szóló szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **CreateDeviceIdentity** nevet.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]
2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **CreateDeviceIdentity** projektre, majd kattintson a **Manage Nuget Packages** (Nuget-csomagok kezelése) parancsra.
3. A **Nuget Package Manager** (Nuget-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza az **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti a [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] Nuget-csomagot és függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![Nuget Package Manager (Nuget-csomagkezelő) ablak][11]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
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
   
    Ez a metódus egy eszközidentitást hoz létre a **myFirstDevice** azonosítóval. (Ha ez az eszköz már létezik a nyilvántartásban, a kód egyszerűen lekéri a meglévő eszközinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált eszközön fogja használni az IoT Hubhoz való csatlakozáshoz.
7. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Futtassa az alkalmazást, és jegyezze fel az eszköz kulcsát.
   
    ![Az alkalmazás által előállított eszközkulcs][12]

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak a hub biztonságos elérésének biztosításához tárolja az eszközidentitást. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd az [Azure IoT Hub fejlesztői útmutatót][lnk-devguide-identity].
> 
> 

## <a name="receive-devicetocloud-messages"></a>Az eszközről a felhőbe irányuló üzenetek fogadása
Ebben a szakaszban egy Windows-konzolalkalmazást hoz létre, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Azure Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs-üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. (Ez az oktatóanyag az IoT Hub Event Hub-kompatibilis végpontokra vonatkozik.)

> [!NOTE]
> Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hub-kompatibilis végpontok mindig az AMQP protokollt használják.
> 
> 

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **ReadDeviceToCloudMessages** nevet.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]
2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **ReadDeviceToCloudMessages** projektre, majd kattintson a **Manage Nuget Packages** (Nuget-csomagok kezelése) parancsra.
3. A **Nuget Package Manager** (Nuget-csomagkezelő) ablakban keresse meg a **WindowsAzure.ServiceBus** csomagot, válassza az **Install** (Telepítés) lehetőséget, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure Service Bus][lnk-servicebus-nuget] elemet minden függőségével, és hozzáad egy rá mutató hivatkozást is. A csomag lehetővé teszi az alkalmazás számára, hogy csatlakozzon az Event Hub-kompatibilis végponthoz az IoT Hubon.
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
   
    Ez a metódus egy **EventHubReceiver** példánnyal fogadja az üzeneteket az IoT Hub összes eszközről a felhőbe irányuló fogadási partíciójáról. Figyelje meg, hogy az **EventHubReceiver** objektum létrehozásakor a rendszer átad egy `DateTime.Now` paramétert, így csak az elindítása után küldött üzeneteket fogja fogadni. Ez a szűrő tesztkörnyezetben hasznos, mivel Ön láthatja vele az aktuális üzeneteket. Éles környezetben azonban a kódnak gondoskodnia kell az összes üzenet feldolgozásáról. További információkért lásd az [eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.
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

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy olyan eszközt szimuláló Windows-konzolalkalmazást hoz létre, amely az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldi.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **SimulatedDevice** nevet.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]
2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **SimulatedDevice** projektre, majd kattintson a **Manage Nuget Packages** (Nuget-csomagok kezelése) parancsra.
3. A **Nuget Package Manager** (Nuget-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **Microsoft.Azure.Devices.Client** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices.Client** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT – Device SDK ][lnk-device-nuget] Nuget-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
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
   
   A **Create** metódus alapértelmezés szerint létrehoz egy **DeviceClient** példányt, amely az AMQP protokollt használja az IoT Hubbal való kommunikációra. A HTTP protokoll használatához használja a **Create** metódus felülírását, amely lehetővé teszi a protokoll megadását. Ha a HTTP protokollt használja, hozzá kell adnia a **Microsoft.AspNet.WebApi.Client** Nuget-csomagot a projekthez, hogy tartalmazza a **System.Net.Http.Formatting** névteret.

Ez az oktatóanyag végigvezeti egy IoT Hub-eszközügyfél létrehozásának lépésein. A [Connected Service for Azure IoT Hub][lnk-connected-service] (Csatlakoztatott szolgáltatás Azure IoT Hubhoz) Visual Studio-bővítmény használatával is hozzáadhatja a szükséges kódot az eszközügyfél-alkalmazáshoz.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.
> 
> 

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A Visual Studióban a Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Set StartUp projects** (Indítási projektek beállítása) parancsra. Válassza a **Multiple startup projects** (Több indítási projekt) lehetőséget, majd válassza a **Start** (Indítás) elemet a **ReadDeviceToCloudMessages** és a **SimulatedDevice** projekt műveleteként.
   
    ![Indítási projektek tulajdonságai][41]
2. Nyomja le az **F5** billentyűt a két alkalmazás futtatásához. A **SimulatedDevice** alkalmazás konzolkimenete a szimulált eszköz által az IoT Hubnak küldött üzeneteket jeleníti meg. A **ReadDeviceToCloudMessages** alkalmazás konzolkimenete az IoT Hub által fogadott üzeneteket jeleníti meg.
   
    ![Az alkalmazások konzolkimenetei][42]
3. Az [Azure Portal][lnk-portal] **Használat** csempéje a hubnak küldött üzenetek számát jeleníti meg:
   
    ![Az Azure Portal Használat csempéje][43]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy IoT Hubot konfigurált az Azure portálon, majd létrehozott egy eszközidentitást a hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön a hubnak. Emellett létrehozott egy alkalmazást, amely megjeleníti a hub által fogadott üzeneteket. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszköz csatlakoztatása][lnk-connect-device]
* [Eszközfelügyelet – első lépések][lnk-device-management]
* [IoT átjáró SDK – első lépések][lnk-gateway-SDK]

Az IoT-megoldás kibővítésével és az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban tekintse meg [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Nov16_HO2-->


