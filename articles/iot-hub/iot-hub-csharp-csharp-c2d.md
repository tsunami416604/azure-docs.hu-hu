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
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110202"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Üzenetek küldése a felhőből az eszközre IoT Hub (.NET) használatával

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. Az [eszközről az IoT hub-ra való telemetria küldése](quickstart-send-telemetry-dotnet.md) azt mutatja be, hogyan lehet létrehozni egy IoT hubot, kiépíteni egy eszköz identitását, és az eszközről a felhőbe irányuló üzeneteket küldő eszköz-alkalmazást.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md)épít. A következő feladatok elvégzését mutatja be:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.

* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekkel kapcsolatos további információk a [D2C és a C2D üzenetküldés IoT hub](iot-hub-devguide-messaging.md)használatával című témakörben találhatók.

Az oktatóanyag végén két .NET-konzol alkalmazást futtat.

* **SimulatedDevice**. Ez az alkalmazás csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket. Ez az alkalmazás a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md)létrehozott alkalmazás módosított verziója.

* **SendCloudToDevice**. Ez az alkalmazás egy felhőből az eszközre irányuló üzenetet küld az eszköz alkalmazásnak IoT Hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

> [!NOTE]
> A IoT Hub számos eszköz-platformhoz és nyelvhez rendelkezik SDK-támogatással, beleértve a C, Java, Python és JavaScript eszközöket az [Azure IoT Device SDK](iot-hub-devguide-sdks.md)-k segítségével. Az eszköznek az oktatóanyag kódjához való csatlakoztatásának részletes ismertetését, és általában az Azure IoT Hubt a [IoT hub fejlesztői útmutatójában](iot-hub-devguide.md)találja.
>

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Üzenetek fogadása az eszköz alkalmazásában

Ebben a szakaszban a [telemetria küldése az eszközről az IoT hub](quickstart-send-telemetry-dotnet.md) -ra létrehozott alkalmazásban módosítsa a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

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

1. Adja hozzá a következő metódust a **Main** metódushoz, közvetlenül a `Console.ReadLine()` sor előtt:

   ```csharp
   ReceiveC2dAsync();
   ```

A `ReceiveAsync` metódus aszinkron módon visszaadja a kapott üzenetet, amikor az eszköz megkapja azt. Egy megadható időtúllépési időszak után *Null* értéket ad vissza. Ebben a példában az alapértelmezett érték egy perc. Ha az alkalmazás *Null értéket*kap, akkor továbbra is várnia kell az új üzeneteket. Ez a követelmény a `if (receivedMessage == null) continue` sor oka.

A `CompleteAsync()` a rendszer értesítést küld arról, IoT Hub az üzenetet sikeresen feldolgozták. Az üzenet biztonságosan eltávolítható az eszköz várólistáról. Ha valami történt, amely meggátolta, hogy az eszköz nem teljesíti az üzenet feldolgozását, IoT Hub a szolgáltatás újra elérhetővé válik. Az *idempotens*logikának kell lennie az eszköz alkalmazásában, hogy ugyanazt az üzenetet kapja többször is ugyanez az eredmény.

Egy alkalmazás átmenetileg is kihagyhat egy üzenetet, ami azt eredményezi, hogy a IoT hub a jövőbeli felhasználás érdekében megőrzi az üzenetet a várólistában. Vagy az alkalmazás elutasíthat egy üzenetet, amely véglegesen eltávolítja az üzenetet a várólistából. A felhőből az eszközre irányuló üzenetek életciklusával kapcsolatos további információkért lásd: [D2C és C2D-üzenetküldés a IoT hub használatával](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Ha MQTT vagy AMQP helyett HTTPS-t használ, a `ReceiveAsync` metódus azonnal visszaadja. A HTTPS-alapú felhőből az eszközre irányuló üzenetek támogatott mintája időnként olyan eszközökhöz csatlakozik, amelyek ritkán keresik az üzeneteket (kevesebb, mint 25 percenként). Ha több HTTPS-t ad meg, a kérések szabályozása IoT Hub eredményez. A MQTT, a AMQP és a HTTPS támogatásával, valamint a szabályozás IoT Hubával kapcsolatos további információkért lásd: [D2C és C2D üzenetküldés a IoT hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Most ír egy .NET-konzol alkalmazást, amely a felhőből az eszközre irányuló üzeneteket küld az eszköz alkalmazásnak.

1. Az aktuális Visual Studio-megoldásban válassza a **fájl** > **új** > **projekt**lehetőséget. Az **új projekt létrehozása**területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább**gombra.

1. Nevezze el a projekt *SendCloudToDevice*. A **megoldás**területen válassza a **Hozzáadás a megoldáshoz** lehetőséget, és fogadja el a .NET-keretrendszer legújabb verzióját. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

   ![Új projekt konfigurálása a Visual Studióban](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. A Megoldáskezelőban kattintson a jobb gombbal az új megoldásra, majd válassza a **NuGet-csomagok kezelése**lehetőséget.

1. A **NuGet-csomagok kezelése**lapon válassza a **Tallózás**lehetőséget, majd keresse meg és válassza ki a **Microsoft. Azure. Devices**elemet. Válassza a **telepítés**lehetőséget.

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Service SDK NuGet csomagra](https://www.nuget.org/packages/Microsoft.Azure.Devices/)mutató hivatkozást.

1. Adja hozzá a következő `using` utasítást a **program.cs** fájl elejéhez.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét a korábban átmásolt IoT hub-beli [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)elemre.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Adja hozzá a **Program** osztályhoz a következő metódust. Állítsa be az eszköz nevét arra az értékre, amelyet az eszköznek az [eszközről az IoT hubhoz való telemetria](quickstart-send-telemetry-dotnet.md)való megadásakor használt.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Ez a metódus egy új, a felhőből az eszközre irányuló üzenetet küld az eszköznek a következő AZONOSÍTÓval: `myFirstDevice`. Ezt a paramétert csak akkor módosítsa, ha módosította a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md).

1. Végül adja hozzá a következő sorokat a **Main** metódushoz.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. A Solutions Explorerben kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása**lehetőséget.

1. Az **Általános tulajdonságok** > **indítási projekt**területen válassza a **több indítási projekt**lehetőséget, majd válassza ki a **ReadDeviceToCloudMessages**, a **SimulatedDevice** **és a** **SendCloudToDevice**indítási műveletét. A módosítások mentéséhez kattintson **az OK gombra** .

1. Nyomja le az **F5**billentyűt. Mindhárom alkalmazásnak el kell indulnia. Válassza ki a **SendCloudToDevice** Windowst, majd nyomja le az **ENTER**billentyűt. Ekkor meg kell jelennie az eszköz által fogadott üzenetnek.

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

1. Adja hozzá a következő sort a **Main** metódushoz közvetlenül a `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`után.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Ha visszajelzést szeretne küldeni a felhőből az eszközre irányuló üzenet kézbesítéséről, meg kell adnia egy tulajdonságot a **SendCloudToDeviceMessageAsync** metódusban. Adja hozzá a következő sort közvetlenül a `var commandMessage = new Message(...);` sor után.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Futtassa az alkalmazásokat az **F5**billentyű lenyomásával. Mindhárom alkalmazást meg kell kezdeni. Válassza ki a **SendCloudToDevice** Windowst, majd nyomja le az **ENTER**billentyűt. Ekkor meg kell jelennie az eszköz által fogadott üzenetnek, és néhány másodperc elteltével a **SendCloudToDevice** -alkalmazás fogadja a visszajelzési üzenetet.

   ![Üzenet fogadása](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. A termelési kódban az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults)szolgáló újrapróbálkozási házirendeket (például exponenciális leállítási) kell végrehajtania.
>

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT távoli monitorozási megoldásának gyorsítása](https://docs.microsoft.com/azure/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
