---
title: "Üzenetek az Azure IoT Hub (.Net) |} Microsoft Docs"
description: "How Azure IoT Hub eszközről a felhőbe üzenetek feldolgozásához átirányítani más háttérszolgáltatások üzenetek útválasztási szabályokat és az egyéni végpontokat használatával."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: d8fed08aa22577574b30b360ec164daf592ed456
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-net"></a>Üzenetek az IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Ez az oktatóanyag épít, a [Ismerkedés az IoT-központ] oktatóanyag. Az oktatóanyag:

* Bemutatja, hogyan könnyen és konfigurációalapú az eszközről a felhőbe üzenetek mennyi útválasztási szabályok használatával.
* Bemutatja, hogyan interaktív üzenetek további feldolgozásra a megoldás háttérből azonnali beavatkozást igénylő elkülönítéséhez. Például egy eszköz előfordulhat, hogy küldése riasztás, amely elindítja a jegy beszúrása egy CRM-rendszerbe. Adatpont üzenetek, például hőmérséklet telemetriai ellentétben be egy analytics hírcsatorna.

Ez az oktatóanyag végén három .NET konzol alkalmazások futtatása:

* **SimulatedDevice**, az alkalmazás létrehozása a módosított változatát a [Ismerkedés az IoT-központ] oktatóanyag adatpont eszközről a felhőbe üzeneteket küld másodpercenként, és interaktív eszközre felhő 10 másodpercenként küldött üzenetek.
* **ReadDeviceToCloudMessages** , amely a nem kritikus az eszköz alkalmazás által küldött telemetriai jeleníti meg.
* **ReadCriticalQueue** távolít el az eszköz-alkalmazás által a Service Bus-üzenetsorba küldött a kritikus üzeneteihez. A várólista az IoT hub van csatolva.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek, beleértve a C, Java és JavaScript SDK támogatása. Lecseréli a szimulált eszköz ebben az oktatóanyagban egy fizikai eszköz, lásd: a [Azure IoT fejlesztői központ].

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. <br/>Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) néhány percig.

Javasoljuk továbbá kapcsolatos tájékoztatás [Azure Storage] és [Azure Service Bus].

## <a name="send-interactive-messages"></a>Interaktív üzenetek küldése

Az eszköz alkalmazás létrehozott módosítása a [Ismerkedés az IoT-központ] interaktív küldéséhez alkalmanként oktatóanyag.

A Visual Studio a a **SimulatedDevice** projektre, cserélje le a `SendDeviceToCloudMessagesAsync` metódus a következő kóddal:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Ez a módszer véletlenszerűen hozzáadja a tulajdonság `"level": "critical"` és `"level": "storage"` az eszköz által küldött üzenetek, amely szimulálja egy üzenetet, amely az alkalmazás háttér-vagy, hogy véglegesen kell tárolni, hogy azonnali beavatkozást igényel. Az alkalmazás az információt továbbítja az üzenet tulajdonságai ahelyett, hogy az üzenet törzsében úgy, hogy az IoT-központ irányítani tudja a megfelelő üzenet célra az üzenetet.

> [!NOTE]
> Több, különböző esetekre, beleértve a cold-path feldolgozási mellett az itt bemutatott példában közbeni elérési üzenettulajdonságok üzenetek is használhatja.

> [!NOTE]
> Határozottan javasoljuk, hogy például az exponenciális leállítási, az MSDN-cikkben leírtak újrapróbálkozási házirendje megvalósítása [átmeneti hiba kezelése].

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Az IoT hub a várólistához üzenetek

Ebben a szakaszban:

* Hozzon létre egy Service Bus-üzenetsorba.
* Csatlakoztassa az IoT hub.
* Az IoT hub, az üzenetek küldése az üzenetsorba, annak alapján, hogy az üzenet egy tulajdonságának konfigurálása.

A Service Bus-üzenetsorok folyamat üzenetek módjáról további információkért lásd: [Ismerkedés a várólisták][Service Bus queue].

1. Hozzon létre egy Service Bus-üzenetsorba, a [Ismerkedés a várólisták][Service Bus queue]. A várólista az IoT hub, az előfizetés és a régióban kell lennie. Jegyezze fel az a névtér és a várólista nevét.

    > [!NOTE]
    > Service Bus-üzenetsorok és témakörök használatos az IoT-központok végpontjai nem lehet **munkamenetek** vagy **ismétlődő észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **Unreachable** az Azure portálon.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.
    
    ![Az IoT-központ végpontok][30]

3. Az a **végpontok** panelen kattintson **Hozzáadás** a sor hozzáadása az IoT hub tetején. A végpont neve **CriticalQueue** majd válassza ki a legördülő listákat **Service Bus-üzenetsorba**, ahol a várólista van a Service Bus-névtér és a várólista nevét. Amikor elkészült, kattintson a **mentése** alján.
    
    ![A végpont hozzáadása][31]
    
4. Most kattintson **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **DeviceTelemetry** a adatforrásként. Adja meg `level="critical"` feltételt, és válassza ki a várólista-útválasztási szabály végpontjának egyéni végpontként hozzáadott. Amikor elkészült, kattintson a **mentése** alján.
    
    ![Egy útvonal hozzáadása][32]
    
    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez az érték az alapértelmezett konfigurációjának megadása az IoT-központ.
    
    ![Tartalék útvonal][33]

## <a name="read-from-the-queue-endpoint"></a>A várólista végpontról olvasása

Ebben a szakaszban az üzenetek a várólista végpontról olvasni.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. Nevet a projektnek **ReadCriticalQueue**.

2. A Megoldáskezelőben kattintson a jobb gombbal a **ReadCriticalQueue** projektre, és kattintson a **NuGet-csomagok kezelése**. Ez a művelet hatására megjelenik a **NuGet-Csomagkezelő** ablak.

3. Keresse meg **WindowsAzure.ServiceBus**, kattintson a **telepítése**, és fogadja el a használati feltételeket. Ez a művelet tölti le, telepíti, és hozzáad egy hivatkozást az Azure Service Bus elemet minden függőségével.

4. Adja hozzá a következő **használatával** tetején lévő utasítások a **Program.cs** fájlt:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Végül adja hozzá a következő sorokat a **fő** metódust. Helyettesítse be a kapcsolati karakterlánc **figyelésére** a várólistához tartozó engedélyeket:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Készen áll arra, hogy futtassa az alkalmazásokat.

1. A Visual Studio, a Megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza ki **indítási projektek beállítása**. Válassza ki **több kezdőprojekt**, majd jelölje be **Start** művelet a **ReadDeviceToCloudMessages**, **SimulatedDevice**, és **ReadCriticalQueue** projektek.
2. Nyomja le az **F5** elindítani a három konzol alkalmazásokat. A **ReadDeviceToCloudMessages** az alkalmazás által küldött üzenetek csak nem kritikus rendelkezik a **SimulatedDevice** alkalmazás, és a **ReadCriticalQueue** alkalmazás még csak a kritikus üzeneteihez.
   
   ![Három konzol alkalmazás][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Választható) Az IoT hub és útvonal üzenetek hozzá a tároló hozzáadása

Ebben a szakaszban hozzon létre egy tárfiókot, csatlakoztassa az IoT hub, és konfigurálja az IoT hub üzeneteket küldhet a fiók, az üzenet tulajdonság alapján. Felügyelni a tárolást kapcsolatos további információkért lásd: [Ismerkedés az Azure Storage szolgáltatással][Azure Storage].

 > [!NOTE]
   > Ha nem csak egy **végpont**, előfordulhat, hogy a telepítő a **StorageContainer** kívül a **CriticalQueue** , és mindkét simulatneously futtassa.

1. Hozzon létre egy tárfiókot, [Azure Storage-dokumentációt] [lnk-tároló] leírtak szerint. Jegyezze fel a fiók nevét.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

3. Az a **végpontok** panelen válassza a **CriticalQueue** végpontot, és kattintson **törlése**. Kattintson a **Igen**, és kattintson a **Hozzáadás**. A végpont neve **StorageContainer** majd válassza ki a legördülő listákat **Azure-tárolót**, és hozzon létre egy **tárfiók** és egy **tárolási tároló**.  Jegyezze fel a neveket.  Amikor elkészült, kattintson a **OK** alján. 

 > [!NOTE]
   > Ha nem csak egy **végpont**, nem kell törölnie a **CriticalQueue**.

4. Kattintson a **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **eszközre küldött üzenetek** a adatforrásként. Adja meg `level="storage"` feltételt, és válassza a **StorageContainer** útválasztási szabály végpontjának egyéni végpontként. Kattintson a **mentése** alján.  

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

1. Győződjön meg arról, hogy az előző alkalmazások továbbra is futnak. 

1. Az Azure portálon lépjen a tárfiókhoz a **Blob szolgáltatás**, kattintson a **blobok Tallózás...** .  Jelölje ki a tárolót, keresse meg és kattintson a JSON-fájlt, és kattintson **letöltése** adatok megtekintéséhez.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudta, hogyan megbízhatóan átirányítani az eszköz a felhőbe küldött üzeneteket az üzenetet az IoT-központ útválasztási funkcióra használatával.

A [IoT hubbal felhő eszközre üzenetek küldése] [ lnk-c2d] bemutatja, hogyan üzenetek küldése az eszközöket a megoldás háttérből.

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite][lnk-suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

Az üzenetet az IoT hubon útválasztási kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Ismerkedés az IoT-központ]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT fejlesztői központ]: https://azure.microsoft.com/develop/iot
[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
