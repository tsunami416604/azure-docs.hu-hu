---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (.NET) szolgáltatással | Microsoft Docs
description: Felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről az Azure IoT SDK-k használatával a .NET-hez. A felhőből az eszközre irányuló üzenetek fogadásához és a háttérbeli alkalmazások módosításához a felhőből az eszközre irányuló üzenetek küldéséhez módosítania kell egy eszköz alkalmazást.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 5a7cb4ecde599a76b2d42cf874420d9cbcfda3d5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402647"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Üzenetek küldése a felhőből az eszközre IoT Hub (.NET) használatával

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Bevezetés

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. Az [eszközről az IoT hub-ra való telemetria küldése](quickstart-send-telemetry-dotnet.md) azt mutatja be, hogyan lehet létrehozni egy IoT hubot, kiépíteni egy eszköz identitását, és az eszközről a felhőbe irányuló üzeneteket küldő eszköz-alkalmazást.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md)épít. Bemutatja, hogyan hajthatja végre a következő lépéseket:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.

* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekkel kapcsolatos további információk a [D2C és a C2D üzenetküldés IoT hub](iot-hub-devguide-messaging.md)használatával című témakörben találhatók.

Az oktatóanyag végén két .NET-konzol alkalmazást futtat.

* A **SimulatedDevice**az alkalmazás egy olyan módosított verziója, amely a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md), amely csatlakozik a IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* **SendCloudToDevice**, amely egy felhőből az eszközre irányuló üzenetet küld az eszköz alkalmazásnak IoT hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

> [!NOTE]
> A IoT Hub számos eszköz-platform és nyelv (például C, Java és JavaScript) támogatásával rendelkezik az [Azure IoT Device SDK](iot-hub-devguide-sdks.md)-k használatával. Az eszköznek az oktatóanyag kódjához való csatlakoztatásának részletes ismertetését, és általában az Azure IoT Hubt a [IoT hub fejlesztői útmutatójában](iot-hub-devguide.md)találja.
>

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio

* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="receive-messages-in-the-device-app"></a>Üzenetek fogadása az eszköz alkalmazásában

Ebben a szakaszban a [telemetria-eszközök küldése az eszközről az IoT hub](quickstart-send-telemetry-dotnet.md) -ba létrehozott eszközt úgy módosítja, hogy fogadja a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

1. A Visual Studióban a **SimulatedDevice** projektben adja hozzá a következő metódust a **program** osztályhoz.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   A `ReceiveAsync` metódus aszinkron módon visszaadja a kapott üzenetet, amikor az eszköz megkapja azt. A függvény *Null* értéket ad vissza a megadható időtúllépési időtartam után (ebben az esetben az alapértelmezett egy percet használja). Ha az alkalmazás *Null értéket*kap, akkor továbbra is várnia kell az új üzeneteket. Ez a követelmény a `if (receivedMessage == null) continue` sor oka.

    Az üzenet sikeres `CompleteAsync()` feldolgozását IoT hub értesítési hívás. Az üzenet biztonságosan eltávolítható az eszköz várólistáról. Ha valami történt, amely meggátolta, hogy az eszköz nem teljesíti az üzenet feldolgozását, IoT Hub a szolgáltatás újra elérhetővé válik. Ezt követően fontos, hogy az *idempotens*logikája az eszköz alkalmazásban legyen, így ugyanazt az üzenetet több alkalommal is ugyanazt az eredményt kapja. 

    Egy alkalmazás átmenetileg is kihagyhat egy üzenetet, ami azt eredményezi, hogy a IoT hub a jövőbeli felhasználás érdekében megőrzi az üzenetet a várólistában. Vagy az alkalmazás elutasíthat egy üzenetet, amely véglegesen eltávolítja az üzenetet a várólistából. A felhőből az eszközre irányuló üzenetek életciklusával kapcsolatos további információkért lásd: [D2C és C2D-üzenetküldés a IoT hub használatával](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Ha MQTT vagy AMQP helyett HTTPS protokollt használ, a `ReceiveAsync` metódus azonnal visszaadja. A HTTPS-alapú felhőből az eszközre irányuló üzenetek támogatott mintája időnként olyan eszközökhöz csatlakozik, amelyek ritkán keresik az üzeneteket (kevesebb, mint 25 percenként). Ha több HTTPS-t ad meg, a kérések szabályozása IoT Hub eredményez. A MQTT, a AMQP és a HTTPS támogatásával, valamint a szabályozás IoT Hubával kapcsolatos további információkért lásd: [D2C és C2D üzenetküldés a IoT hub](iot-hub-devguide-messaging.md).
   >

2. Adja hozzá a következő metódust a **Main** metódushoz, közvetlenül `Console.ReadLine()` a sor előtt:

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebből a cikkből megtudhatja, hogyan küldhet el egy háttér-szolgáltatást a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése az eszközről az IoT hubhoz](quickstart-send-telemetry-dotnet.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Most ír egy .NET-konzol alkalmazást, amely a felhőből az eszközre irányuló üzeneteket küld az eszköz alkalmazásnak.

1. Az aktuális Visual Studio-megoldásban kattintson a jobb gombbal a megoldásra, és válassza a Hozzáadás > új projekt lehetőséget. Válassza a **Windows Desktop** , majd a **Console app (.NET-keretrendszer)** lehetőséget. Nevezze el a projekt **SendCloudToDevice** , és válassza ki a .NET-keretrendszer legújabb verzióját, majd kattintson az **OK** gombra a projekt létrehozásához.

   ![Új projekt a Visual Studióban](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. A Megoldáskezelőban kattintson a jobb gombbal a megoldásra, majd kattintson a **megoldás NuGet-csomagjainak kezelése**lehetőségre.

   Ez a művelet megnyitja a **NuGet-csomagok kezelése** ablakot.

3. Keressen rá a **Microsoft. Azure. Devices**kifejezésre, majd válassza a Tallózás lapot. Ha megtalálta a csomagot, kattintson a **telepítés**gombra, és fogadja el a használati feltételeket.

   Ez letölti, telepíti és hozzáadja az [Azure IoT Service SDK NuGet csomagra](https://www.nuget.org/packages/Microsoft.Azure.Devices/)mutató hivatkozást.

4. Adja hozzá a `using` következő utasítást a **program.cs** fájl elejéhez.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Helyettesítse be a helyőrző értékét a korábban a [IoT hub](#get-the-iot-hub-connection-string)-IoT lekérése során másolt.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Adja hozzá a **Program** osztályhoz a következő metódust. Állítsa be az eszköz nevét arra az értékre, amelyet az eszköznek az eszközről az [IoT hubhoz való telemetria](quickstart-send-telemetry-dotnet.md)való megadásakor használt.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Ez a metódus egy új, `myFirstDevice`a felhőből az eszközre irányuló üzenetet küld az eszköznek a következő azonosítóval:. Ezt a paramétert csak akkor módosítsa, ha módosította a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md).

7. Végül adja hozzá a következő sorokat a **Main** metódushoz.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása...** lehetőséget. Válasszon **több indítási projektet**, majd válassza ki a **Start** műveletet a **ReadDeviceToCloudMessages**, a **SimulatedDevice**és a **SendCloudToDevice**.

9. Nyomja le az **F5**billentyűt. Mindhárom alkalmazásnak el kell indulnia. Válassza ki a **SendCloudToDevice** Windowst, majd nyomja le az **ENTER**billentyűt. Ekkor meg kell jelennie az eszköz által fogadott üzenetnek.

   ![Üzenet fogadása](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Kézbesítési visszajelzés fogadása

Az egyes felhőből az eszközre irányuló üzenetek esetében a IoT Hub kézbesítési (vagy lejárati) nyugták is igényelhetők. Ez a beállítás lehetővé teszi, hogy a megoldás háttérrendszer egyszerűen tájékoztassa az újrapróbálkozási vagy a kompenzációs logikát. További információ a felhőből az eszközre irányuló visszajelzésekről: a [D2C és a C2D üzenetküldés a IoT hub használatával](iot-hub-devguide-messaging.md).

Ebben a szakaszban a **SendCloudToDevice** alkalmazást úgy módosítja, hogy visszajelzést kérjen, és megkapja az IoT hub-ból.

1. A Visual Studióban a **SendCloudToDevice** projektben adja hozzá a következő metódust a **program** osztályhoz.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Megjegyzés: Ez a fogadási minta ugyanaz, mint a felhőből az eszközre irányuló üzenetek fogadása az eszköz alkalmazásból.

2. Adja hozzá a következő metódust a **Main** metódushoz, közvetlenül `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` a sor után.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Ha visszajelzést szeretne küldeni a felhőből az eszközre irányuló üzenet kézbesítéséről, meg kell adnia egy tulajdonságot a **SendCloudToDeviceMessageAsync** metódusban. Adja hozzá a következő sort a `var commandMessage = new Message(...);` sor után jobbra.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Futtassa az alkalmazásokat az **F5**billentyű lenyomásával. Mindhárom alkalmazást meg kell kezdeni. Válassza ki a **SendCloudToDevice** Windowst, majd nyomja le az **ENTER**billentyűt. Ekkor meg kell jelennie az eszköz által fogadott üzenetnek, és néhány másodperc elteltével a **SendCloudToDevice** -alkalmazás fogadja a visszajelzési üzenetet.

   ![Üzenet fogadása](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például az exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT távoli monitorozási megoldásának gyorsítása](https://docs.microsoft.com/azure/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
