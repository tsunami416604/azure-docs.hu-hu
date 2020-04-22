---
title: Felhőből eszközre irányuló üzenetek az Azure IoT Hub (.NET) használatával | Microsoft dokumentumok
description: Felhőből az eszközre irányuló üzenetek küldése egy eszközre egy Azure IoT-központból az Azure IoT SDK-k használatával . Módosítja az eszközalkalmazást a felhőből az eszközre irányuló üzenetek fogadásához, és módosít egy háttéralkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 41c29e55f04f9edf06ba375ad4539e5fb3f82c18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733418"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Üzenetek küldése a felhőből az eszközére az IoT Hub (.NET) segítségével

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között. A [Telemetriaküldése egy eszközről egy IoT hub](quickstart-send-telemetry-dotnet.md) rövid útmutató bemutatja, hogyan hozhat létre egy IoT hub, egy eszköz identitását, és a kód egy eszköz alkalmazás, amely elküldi az eszköz-felhő üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetriai adatok küldése eszközről egy IoT hubra](quickstart-send-telemetry-dotnet.md)épül. Bemutatja, hogyan kell elvégezni a következő feladatokat:

* A megoldás háttér-tartalék, felhőből az eszközre üzeneteket küldhet egyetlen eszközre az IoT Hubon keresztül.

* Felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldás háttérrendszeréről kérje a kézbesítési nyugtázást (*visszajelzést)* az IoT Hubról az eszközre küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről a [D2C és a C2D Messaging az IoT Hub segítségével](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két .NET konzolalkalmazást futtat.

* **SimulatedDevice**. Ez az alkalmazás csatlakozik az IoT hubhoz, és felhőből az eszközre irányuló üzeneteket fogad. Ez az alkalmazás az alkalmazás módosított verziója, amelyet a [Telemetria küldése az eszközről egy IoT hubra](quickstart-send-telemetry-dotnet.md)hozott létre.

* **SendCloudToDevice**. Ez az alkalmazás egy felhőből az eszközre irányuló üzenetet küld az eszközalkalmazásnak az IoT Hubon keresztül, majd megkapja a kézbesítési nyugtázást.

> [!NOTE]
> Az IoT Hub számos eszközplatformhoz és nyelvhez támogatja az SDK-t, beleértve a C, Java, Python és Javascript nyelvet [az Azure IoT-eszköz SDK-kon](iot-hub-devguide-sdks.md)keresztül. Az eszköz nek az oktatóanyag kódjához és általában az Azure IoT Hubhoz való csatlakoztatásának részletes útmutatásait az [IoT Hub fejlesztői útmutatójában](iot-hub-devguide.md)találja.
>

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="receive-messages-in-the-device-app"></a>Üzenetek fogadása az eszközalkalmazásban

Ebben a szakaszban módosítsa a [telemetriai adatok küldése az eszközről egy IoT](quickstart-send-telemetry-dotnet.md) hubfelhőbe az IoT hubról létrehozott eszközalkalmazást.

1. A Visual Studio **SzimuláltEszköz** projektben adja hozzá a következő módszert a **Program** osztályhoz.

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

1. Adja hozzá a **Main** következő módszert a `Console.ReadLine()` Fő metódushoz, közvetlenül a sor elé:

   ```csharp
   ReceiveC2dAsync();
   ```

A `ReceiveAsync` metódus aszinkron módon adja vissza a fogadott üzenetet az eszköz általi fogadáskor. Null *értéket* ad vissza egy meghatározott időtúlidőszak után. Ebben a példában az alapértelmezett egy perc van használatban. Amikor az alkalmazás *null értéket*kap, továbbra is várnia kell az új üzenetekre. Ez a követelmény az `if (receivedMessage == null) continue` oka a sornak.

Az IoT Hub hívása `CompleteAsync()` értesíti az IoT Hubot, hogy az üzenet feldolgozása sikeresen megtörtént. Az üzenet biztonságosan eltávolítható az eszközvárólistából. Ha valami történt, amely megakadályozta, hogy az eszközalkalmazás befejezi az üzenet feldolgozását, az IoT Hub ismét kézbesíti azt. Az eszközalkalmazásban az üzenetfeldolgozási logikának *idempotensnek*kell lennie, hogy ugyanazt az üzenetet többször is megkapja, ugyanazt az eredményt hozza.

Egy alkalmazás is ideiglenesen elhagyhat egy üzenetet, ami azt eredményezi, hogy az IoT hub megtartja az üzenetet a várólistában a későbbi felhasználás érdekében. Vagy az alkalmazás elutasíthat egy üzenetet, amely véglegesen eltávolítja az üzenetet a várólistából. A felhőből az eszközre irányuló üzenetek életciklusáról további információt a [D2C és C2D üzenetküldés az IoT Hubbal című](iot-hub-devguide-messaging.md)témakörben talál.

   > [!NOTE]
   > Ha https-t használ az MQTT vagy az `ReceiveAsync` AMQP átvitel helyett, a metódus azonnal visszatér. A HTTPS protokollt használó, a felhőből az eszközre irányuló üzenetek támogatott mintája időnként csatlakoztatott eszközök, amelyek ritkán (kevesebb mint 25 percenként) ellenőrzik az üzeneteket. További HTTPS-kérelem kiadása eredményeket kap az IoT Hub a kérelmek szabályozásával. Az MQTT, az AMQP és a HTTPS-támogatás és az IoT Hub-szabályozás közötti különbségekről további információt a [D2C és C2D üzenetküldés az IoT Hubbal című](iot-hub-devguide-messaging.md)témakörben talál.
   >

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben hozzon létre egy háttér-szolgáltatás felhőből eszközre üzeneteket az IoT hub on létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hub.](quickstart-send-telemetry-dotnet.md) Felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből eszközre irányuló üzenet küldése

Most egy .NET konzolalkalmazást ír, amely felhőből az eszközre irányuló üzeneteket küld az eszközalkalmazásnak.

1. Az aktuális Visual Studio-megoldásban válassza az**Új** > **projekt** **fájlja** > lehetőséget. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Nevezze el a *projektsendcloudtodevice*. A **Megoldás**csoportban válassza a **Hozzáadás a megoldáshoz** lehetőséget, és fogadja el a .NET keretrendszer legújabb verzióját. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

   ![Új projekt konfigurálása a Visual Studióban](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. A Megoldáskezelőben kattintson a jobb gombbal az új megoldásra, majd válassza **a NuGet-csomagok kezelése parancsot.**

1. A **NuGet-csomagok kezelése**csoportban válassza a **Tallózás**lehetőséget, majd keresse meg és válassza a **Microsoft.Azure.Devices**lehetőséget. Válassza a **Telepítés**lehetőséget.

   Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az [Azure IoT szolgáltatás SDK NuGet csomag.](https://www.nuget.org/packages/Microsoft.Azure.Devices/)

1. Adja hozzá `using` a következő utasítást a **Program.cs** fájl tetején.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értéket az IoT hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string)

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Adja hozzá a következő módszert a **Program** osztályhoz. Állítsa be az eszköz nevét, hogy mit használt, amikor definiálja az eszközt az [eszköz küldése az eszközről egy IoT hub.](quickstart-send-telemetry-dotnet.md)

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Ez a módszer új, felhőből az eszközre irányuló `myFirstDevice`üzenetet küld az azonosítóval rendelkező eszköznek. Csak akkor módosítsa ezt a paramétert, ha módosította azt a [telemetriai adatok küldése az eszközről egy IoT hubra](quickstart-send-telemetry-dotnet.md)használt paraméterből.

1. Végül adja hozzá a következő sorokat a **Fő** metódushoz.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza **az Indítási projektek beállítása parancsot.**

1. A Common Properties Startup Project **(Közös tulajdonságok** > **indítási projekt) programban**válassza a Több **indítási projekt**lehetőséget, majd válassza a **Start** műveletet a **ReadDeviceToCloudMessages**, **SimulatedDevice**és **SendCloudToDevice**számára. A módosítások mentéséhez kattintson az **OK** gombra.

1. Nyomja **le az F5 billentyűt.** Mindhárom alkalmazásnak el kell indulnia. Válassza ki a **SendCloudToDevice** ablakokat, és nyomja **le az Enter billentyűt.** Látnia kell az eszközalkalmazás által fogadott üzenetet.

   ![Alkalmazás fogadási üzenete](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Kézbesítési visszajelzés fogadása

Az IoT Hubtól minden felhőből az eszközre irányuló üzenethez kézbesítési (vagy lejárati) nyugtázást kérhet. Ez a beállítás lehetővé teszi, hogy a megoldás háttérrendszere könnyen tájékoztassa az újrapróbálkozási vagy kompenzációs logikát. A felhőből az eszközre irányuló visszajelzésekről a [D2C és a C2D Messaging with IoT Hub](iot-hub-devguide-messaging.md)című témakörben talál további információt.

Ebben a szakaszban módosítja a **SendCloudToDevice** alkalmazást, hogy visszajelzést kérjen, és megkapja azt az IoT hubról.

1. A Visual Studio **SendCloudToDevice** projektjében adja hozzá a következő módszert a **Program** osztályhoz.

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

    Vegye figyelembe, hogy ez a fogadási minta megegyezik a felhőből az eszközre irányuló üzenetek fogadásához használt mintával az eszközalkalmazásból.

1. Adja hozzá a **Main** következő sort a `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`Fő metódushoz, közvetlenül a után .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Ha visszajelzést szeretne kérni a felhőből az eszközre irányuló üzenet kézbesítéséhez, meg kell adnia egy tulajdonságot a **SendCloudToDeviceMessageAsync** metódusban. Adja hozzá a következő `var commandMessage = new Message(...);` sort, közvetlenül a sor után.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Futtassa az alkalmazásokat az **F5**billentyű lenyomásával. Látnia kell, hogy mindhárom alkalmazás elindul. Válassza ki a **SendCloudToDevice** ablakokat, és nyomja **le az Enter billentyűt.** Látnia kell az üzenetet, amelyet az eszköz alkalmazás fogadott, és néhány másodperc múlva a **SendCloudToDevice** alkalmazás által fogadott visszajelzési üzenetet.

   ![Alkalmazás fogadási üzenete](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket kell megvalósítania, például az exponenciális visszamaradást, ahogy azt [az átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)javasolta.
>

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan küldhet és fogadhat felhőből az eszközre irányuló üzeneteket.

Az IoT Hubot használó teljes körű megoldások példáinak megtekintéséhez tekintse meg az [Azure IoT Remote Monitoring megoldásgyorsító című témakört.](https://docs.microsoft.com/azure/iot-suite/)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)
