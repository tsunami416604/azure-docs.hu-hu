---
title: Felhőből az eszközre irányuló üzeneteket az Azure IoT Hub (.NET) |} A Microsoft Docs
description: Annak a felhőből az eszközre irányuló üzeneteket küld egy eszköz az Azure IoT SDK-k használata a .NET-hez készült Azure IoT hubról. Módosítja egy eszközalkalmazásnak, hogy a felhőből az eszközre irányuló üzenetek fogadása és módosíthat egy háttér-alkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: d16f57db6a3c39be34c13663db62d7be50749f57
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010515"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Üzenetküldés a felhőből az eszközre az IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Bevezetés

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel engedélyezheti a megbízható és biztonságos kétirányú kommunikációt több millió eszköz között, és megoldást biztosít a háttérrendszer. [Telemetria küldése egy eszközről IoT hubra... ](quickstart-send-telemetry-dotnet.md) bemutatja, hogyan hozzon létre egy IoT hubot, azt az eszközidentitás létrehozását és egy eszköz – felhő üzeneteket eszközalkalmazás code.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ebben az oktatóanyagban ez a rövid útmutató számos tekintetben [telemetriát küldjön az eszközről az IoT hub... ](quickstart-send-telemetry-dotnet.md). Ez bemutatja, hogyan hajtsa végre a következő lépéseket:

* A megoldás háttérrendszerének, a felhőből az eszközre irányuló üzenetek küldése IoT hubon keresztül egy adott eszköz.

* Az eszközön a felhőből az eszközre irányuló üzeneteket fogadni.

* A megoldás háttérrendszerének, a kérelmek kézbesítési nyugtázás (*visszajelzés*) az IoT Hub az eszközökre küldött üzenetek.

További információ a felhőből az eszközre irányuló üzenetek annak [D2C és az IoT Hub üzenetküldési C2D](iot-hub-devguide-messaging.md).

Ez az oktatóanyag végén két .NET-konzolalkalmazással fogja futtatni.

* **SimulatedDevice**, a létrehozott alkalmazás egy módosított verziója [telemetriát küldjön az eszközről az IoT hub... ](quickstart-send-telemetry-dotnet.md), amely csatlakozik az IoT hubhoz, és megkapja a felhőből az eszközre.

* **SendCloudToDevice**, amely a felhőből az eszközre üzenetet küld az IoT hubon keresztül az eszköz alkalmazás, és annak kézbesítési nyugtázási majd kap.

> [!NOTE]
> Az IoT Hub SDK számos eszközplatformok és nyelveken (például a C, Java és Javascript) támogatással rendelkezik az keresztül [Azure IoT eszközoldali SDK-k](iot-hub-devguide-sdks.md). Az eszköz csatlakoztatása, ebben az oktatóanyagban a kódot, és általában az Azure IoT hubba a részletes útmutatót lásd: a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide.md).
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2017

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

## <a name="receive-messages-in-the-device-app"></a>Üzenetek fogadása az eszköz alkalmazásban

Ebben a szakaszban módosítását fogja elvégezni az eszköz alkalmazás létrehozott [telemetriát küldjön az eszközről az IoT hub... ](quickstart-send-telemetry-dotnet.md) felhőből az eszközre irányuló üzenetek fogadása az IoT hubról.

1. A Visual Studióban az a **SimulatedDevice** projektre, adja hozzá a következő metódust a **Program** osztály.

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

   A `ReceiveAsync` metódus aszinkron módon visszaadja, amelyek az eszköz megkapta a fogadott üzenethez. Adja vissza, *null* specifiable időtúllépési idő után (ebben az esetben egy perc az alapértelmezett szerepel). Ha az alkalmazás megkapja egy *null*, az új üzenetek várakozási továbbra is. Ez a követelmény nem az az oka az `if (receivedMessage == null) continue` sor.

    A hívás `CompleteAsync()` értesítést küld az IoT Hub, hogy az üzenet feldolgozása sikeresen megtörtént. Az üzenet biztonságosan eltávolítható az eszköz üzenetsorból. Hiba történt, amely ebben az esetben az eszköz alkalmazást az üzenetek feldolgozásának befejezése, ha az IoT Hub elküldi azt újra. Ezután fontos, hogy az üzenet az eszközalkalmazás lévő logika feldolgozási *idempotens*, így ugyanazt az eredményt adja ugyanazt az üzenetet fogadó több alkalommal. 

    Egy alkalmazás ideiglenesen elvetheti a egy üzenetet, amely az IoT hub, az üzenet a várólistában a jövőbeli fogyasztás megőrzése eredményez. Vagy az alkalmazás képes utasítsa el egy üzenetet, amely véglegesen eltávolítja az üzenetet az üzenetsorból. A felhőből az eszközre irányuló üzenetek életciklusának kapcsolatos további információkért lásd: [D2C és C2D az IoT Hub üzenetküldési](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Helyett MQTT, AMQP vagy HTTPS-en keresztül szolgáltatást átviteli eszközként, amikor a `ReceiveAsync` metódus azonnal visszatér. A támogatott a HTTPS-felhőből eszközre irányuló üzenetek egyik csak időszakosan kapcsolódó eszközök, amelyek ellenőrzik ritkán üzenetek (kevesebb mint 25 percenként). Eredmények további HTTPS kiállító kap az IoT Hub a kérelmek szabályozása. MQTT, AMQP és a HTTPS-támogatás és az IoT Hub szabályozás közötti különbségekkel kapcsolatos további információkért lásd: [D2C és C2D az IoT Hub üzenetküldési](iot-hub-devguide-messaging.md).
   >

2. Adja hozzá a következő metódust a **fő** metódus, mielőtt a jobb oldalon a `Console.ReadLine()` sor:

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Az IoT Hub kapcsolati karakterláncának beszerzése

Először lekérni a portálon az IoT Hub kapcsolati karakterláncra.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com)válassza **erőforráscsoportok**.

2. Válassza ki az ebben az útmutatóban használt erőforráscsoportot.

3. Válassza ki az IoT Hub használ.

4. Jelölje ki a hub ablaktáblán **megosztott elérési házirendek**.

5. Válassza ki **iothubowner**. Azt jeleníti meg a kapcsolati karakterláncokkal az **iothubowner** panel. Válassza a Másolás ikonjára a **kapcsolati karakterlánc – elsődleges kulcs**. A kapcsolati karakterlánc későbbi használatra mentse.

   ![Az IoT Hub kapcsolati karakterláncának beszerzése](./media/iot-hub-csharp-csharp-c2d/get-iot-hub-connection-string.png)

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Most már írhat olyan .NET-konzolalkalmazást, amely a felhőből az eszközre irányuló üzeneteket küld az eszköz alkalmazás.

1. A jelenlegi Visual Studio-megoldásban kattintson a jobb gombbal a megoldásra, majd válassza a Hozzáadás > Új projekt. Válassza ki **Windows asztali** , majd **Console App (.NET Framework)**. Adja a projektnek **SendCloudToDevice** , és válassza ki a .NET-keretrendszer legújabb verzióját, majd válassza ki **OK** a projekt létrehozásához.

   ![A Visual Studio új projekt](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, és kattintson **NuGet-csomagok kezelése megoldáshoz...** .

   Ez a művelet megnyitja a **NuGet-csomagok kezelése** ablak.

3. Keresse meg **Microsoft.Azure.Devices**, válassza a Tallózás lapon. Ha megtalálta a csomagot, kattintson a **telepítése**, és fogadja el a használati feltételeket.

   Ez letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Adja hozzá a következő `using` nyilatkozat tetején a **Program.cs** fájlt.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az ebben a szakaszban korábban mentett IoT hub kapcsolati karakterláncra. 

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Adja hozzá a **Program** osztályhoz a következő metódust. Állítsa be az eszköz nevét, mire az eszköz meghatározásakor a [telemetriát küldjön az eszközről az IoT hub... ](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myDevice", commandMessage);
   }
   ```

   Ez a módszer új felhőből az eszközre irányuló üzenetet küld az ID, az eszköz `myFirstDevice`. Módosítsa ezt a paramétert csak akkor, ha a használt a módosított [telemetriát küldjön az eszközről az IoT hub... ](quickstart-send-telemetry-dotnet.md).

7. Végül adja hozzá a következő sorokat a **fő** metódust.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. A Visual studióban kattintson a jobb gombbal a megoldás, és válassza **állítsa be indítási projektek...** . Válassza ki **több kezdőprojekt**, majd válassza ki a **Start** műveletet **ReadDeviceToCloudMessages**, **SimulatedDevice**, és **SendCloudToDevice**.

9. Nyomja meg **F5**. Mindhárom alkalmazás kell kezdenie. Válassza ki a **SendCloudToDevice** windows, majd nyomja meg **Enter**. Az eszköz alkalmazás által fogadott üzenetnek kell megjelennie.

   ![Alkalmazás fogadó üzenet](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Kézbesítési visszajelzéseket kap

Lehetőség arra kérelem kézbesítési (vagy lejárati) nyugtázás az IoT hubról felhőből az eszközre irányuló üzenetek. Ez a beállítás lehetővé teszi, hogy újra, vagy a kompenzációs logika egyszerűen tájékoztatja a megoldás háttérrendszere. Felhőből az eszközre visszajelzés kapcsolatos további információkért lásd: [D2C és az IoT Hub üzenetküldési C2D](iot-hub-devguide-messaging.md).

Ebben a szakaszban módosítsa a **SendCloudToDevice** alkalmazás visszajelzést, és fogadjon, az IoT hubról.

1. A Visual Studióban az a **SendCloudToDevice** projektre, adja hozzá a következő metódust a **Program** osztály.

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

    Vegye figyelembe a receive minta a felhőből az eszközre irányuló üzenetek fogadása az eszközalkalmazástól érkező ugyanaz.

2. Adja hozzá a következő metódust a **fő** metódus után jobb a `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` sor.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. A felhőből az eszközre irányuló üzenet kézbesítése visszajelzés kéréséhez, meg kell adnia a tulajdonságot a **SendCloudToDeviceMessageAsync** metódust. A következő sorban hozzáadása után a `var commandMessage = new Message(...);` sor.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Az alkalmazások futtatása lenyomásával **F5**. Indítsa el az összes három alkalmazást kell megjelennie. Válassza ki a **SendCloudToDevice** windows, majd nyomja meg **Enter**. Az üzenet, fogadja az eszközalkalmazások által, és néhány másodperc elteltével a visszajelzés üzenet fogadja a **SendCloudToDevice** alkalmazás.

   ![Alkalmazás fogadó üzenet](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Az egyszerűség kedvéért a rizspálinkát Ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megismerkedhetett a felhőből az eszközre irányuló üzenetek küldése és fogadása.

Példák teljes, végpontok közötti megoldások, amely az IoT Hub használata a megtekintéséhez lásd: [Azure IoT távoli figyelési megoldásgyorsító](https://docs.microsoft.com/azure/iot-suite/).

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide.md).