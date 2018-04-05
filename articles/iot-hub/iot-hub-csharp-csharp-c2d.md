---
title: Felhő eszközre üzenetek Azure IoT Hub (.NET) |} Microsoft Docs
description: Hogyan küldhetők felhő-eszközre küldött üzenetek eszközökre az Azure IoT-központ az Azure IoT SDK-k használata a .NET-hez. Módosítja egy eszköz alkalmazásnak, hogy a felhő eszközre üzeneteket fogadni, és módosítsa a háttér-alkalmazásnak, hogy a felhő eszközre üzeneteket küldeni.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: a31c05ed-6ec0-40f3-99ab-8fdd28b1a89a
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: f3110e81a7229f8f279609a64949c7f0ce78d338
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Üzenetek küldése a felhőből az eszközre az IoT Hub (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Bevezetés
Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel eszközök millióira közötti megbízható és biztonságos kétirányú kommunikáció engedélyezése és a megoldás háttérrendszere. A [Ismerkedés az IoT-központ] oktatóanyag bemutatja, hogyan létrehoz egy IoT-központot, azt egy eszközidentitás kiépítéséhez és kód egy eszköz-alkalmazást, amelyet az eszköz a felhőbe küldött üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag épül [Ismerkedés az IoT-központ]. Megmutatja, hogyan számára:

* A megoldás háttérrendszeréhez, az IoT-központ keresztül egyetlen eszközt felhő eszközre üzeneteket küldeni.
* Az eszközön a felhőből eszközre üzeneteket fogadni.
* A megoldás háttérrendszeréhez, a kérelem kézbesítési nyugtázási (*visszajelzés*) IoT-központot egy eszközre küldött üzenetek esetében.

Felhő eszközre üzenetek további információt a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

Ez az oktatóanyag végén két .NET konzol alkalmazások futtatása:

* **SimulatedDevice**, az alkalmazás létrehozása a módosított változatát [Ismerkedés az IoT-központ], amely csatlakozik az IoT hub és felhő eszközre üzeneteket fogad.
* **SendCloudToDevice**, melyik felhőalapú eszközre üzenetet küld az IoT Hub eszköz alkalmazás, és a kézbesítési nyugtázási majd megkapja.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek (például C, Java és Javascript) SDK támogatása keresztül [Azure IoT-eszközök SDK-k]. Csatlakoztassa az eszközt, az oktatóanyag kódot, és általában Azure IoT Hub kapcsolatos lépésenkénti útmutatót lásd: a [IoT Hub fejlesztői útmutató].
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015-öt vagy a Visual Studio 2017
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

## <a name="receive-messages-in-the-device-app"></a>Az eszköz alkalmazásban az üzenetek fogadásához
Ebben a szakaszban módosítania kell az eszköz alkalmazás létrehozott [Ismerkedés az IoT-központ] felhő eszközre üzenetek fogadása az IoT-központot.

1. A Visual Studio a a **SimulatedDevice** projektre, adja hozzá a következő metódust a **Program** osztály.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    A `ReceiveAsync` metódus aszinkron módon visszaadja, amikor az eszköz megkapta a fogadott üzenethez. Adja vissza, *null* specifiable időtúllépés idő után (ebben az esetben a rendszer az alapértelmezett egy percig használja). Ha az alkalmazás fogad egy *null*, várja meg az új üzenetek továbbra is. Ez a követelmény az oka a `if (receivedMessage == null) continue` sor.
   
    A hívás `CompleteAsync()` értesíti az IoT-központot, hogy az üzenet feldolgozása sikeresen megtörtént. Az üzenet biztonságosan eltávolítható az eszközre üzenetsorból. Ha történt, amely miatt nem sikerült az eszköz alkalmazás az üzenet feldolgozása befejezését, IoT-központ továbbítja azt újra. Majd fontos, hogy az eszköz alkalmazás logikája üzenetfeldolgozási *idempotent*, így ugyanazt az eredményt adja ugyanazt az üzenetet fogadó több alkalommal. Egy alkalmazás átmenetileg is abandon egy üzenetet, amely az üzenet a várólistában a jövőbeni felhasználásához megőrzése IoT-központ eredményez. Vagy az alkalmazás elutasíthatja egy üzenetet, amely véglegesen eltávolítja az üzenetet az üzenetsorból. A felhő-eszközre küldött üzenetek életciklus kapcsolatos további információkért tekintse meg a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > Ha a HTTPS helyett MQTT vagy AMQP szolgáltatást átviteli eszközként, a `ReceiveAsync` metódus azonnal visszaadja. A támogatott minta felhő eszközre üzenetekhez HTTPS üzenetek ritkán (kevesebb mint 25 percenként) ellenőrizze, hogy időnként csatlakoztatott eszközök nem. Szabályozás az IoT-központ eredmények további HTTPS kiállító fogadása. MQTT, az amqp-t és a HTTPS PROTOKOLLT támogatja, és az IoT-központ sávszélesség-szabályozási közötti különbségekről további információkért lásd: a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].
   > 
   > 
2. Adja hozzá a következő metódust a **fő** módszer, mielőtt a jobb oldalon a `Console.ReadLine()` sor:
   
        ReceiveC2dAsync();

> [!NOTE]
> Az egyszerűség kedvéért tartozó szakét Ez az oktatóanyag nem valósítja meg a bármely újrapróbálkozási házirendje. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Felhő eszközre üzenet küldése
Ebben a szakaszban egy .NET-Konzolalkalmazás, amelyek a felhőből eszközre üzeneteket küld az eszköz alkalmazás írása.

1. A jelenlegi Visual Studio megoldás, hozzon létre egy Visual C# Asztalialkalmazás-projektet használatával a **Konzolalkalmazás** projektsablon. Nevet a projektnek **SendCloudToDevice**.
   
    ![A Visual Studio új projekt][20]
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, és kattintson **NuGet-csomagok kezelése megoldáshoz...** . 
   
    Ez a művelet megnyitja az **NuGet-csomagok kezelése** ablak.
3. Keresse meg **Microsoft.Azure.Devices**, kattintson a **telepítése**, és fogadja el a használati feltételeket. 
   
    Ez letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK NuGet-csomag].

4. Adja hozzá a következő `using` utasítást a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
5. Adja hozzá a **Program** osztályhoz a következő mezőket: Helyettesítse be a helyőrző értékét az IoT hub kapcsolati karakterlánccal [Ismerkedés az IoT-központ]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Ez a módszer új felhő eszközre üzenetet küld az eszköz a azonosítójú `myFirstDevice`. Módosítsa ezt a paramétert csak akkor, ha az használt módosított [Ismerkedés az IoT-központ].
7. Végül adja a következő sorokat a **Main** metódushoz:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. A Visual studióban, kattintson a jobb gombbal a megoldás, és válassza **állítsa be indítási projektek...** . Válassza ki **több kezdőprojekt**, majd jelölje be a **Start** művelet **ReadDeviceToCloudMessages**, **SimulatedDevice**, és **SendCloudToDevice**.
9. Nyomja le az **F5**. Mindhárom alkalmazás elindul. Válassza ki a **SendCloudToDevice** windows, és nyomja le az **Enter**. Az eszköz alkalmazás által fogadott üzenet kell megjelennie.
   
   ![A fogadó alkalmazás üzenet][21]

## <a name="receive-delivery-feedback"></a>Kézbesítési visszajelzést kap
Lehetőség a kérelem kézbesítési (vagy lejárati) nyugták az IoT-központ minden felhő eszközre üzenethez. Ez a beállítás lehetővé teszi, hogy a megoldás háttérrendszeréhez könnyen tájékoztatja az újra gombra vagy a kompenzáció logikát. Felhő eszközre visszajelzés kapcsolatos további információkért tekintse meg a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

Ebben a szakaszban módosítsa a **SendCloudToDevice** alkalmazásnak, hogy a visszajelzés, és az IoT-központ fogadása.

1. A Visual Studio a a **SendCloudToDevice** projektre, adja hozzá a következő metódust a **Program** osztály.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Vegye figyelembe, hogy a fogadási mintát, a felhő-eszközre küldött üzenetek fogadása az eszköz alkalmazás használt azonos egy.
2. Adja hozzá a következő metódust a **fő** metódus, a jobb oldali után a `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` sor:
   
        ReceiveFeedbackAsync();
3. A felhő eszközre üzenet kézbesítése visszajelzése kérelmezéséhez kell megadnia a tulajdonság a **SendCloudToDeviceMessageAsync** metódust. A következő sor hozzáadása után a `var commandMessage = new Message(...);` sor:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Az alkalmazások futtatásához billentyűkombináció lenyomásával **F5**. Meg kell jelennie a start mindhárom alkalmazás. Válassza ki a **SendCloudToDevice** windows, és nyomja le az **Enter**. Az üzenet, alatt az eszköz alkalmazás, és néhány másodpercen belül a visszajelzés üzenetet kapott mértékben fogadja a **SendCloudToDevice** alkalmazás.
   
   ![A fogadó alkalmazás üzenet][22]

> [!NOTE]
> Az egyszerűség kedvéért tartozó szakét Ez az oktatóanyag nem valósítja meg a bármely újrapróbálkozási házirendje. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése].
> 
> 

## <a name="next-steps"></a>További lépések
Ebben az oktatóprogramban megismerte felhő eszközre üzeneteket küldjön és fogadjon. 

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT szolgáltatás SDK NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Ismerkedés az IoT-központ]: iot-hub-csharp-csharp-getstarted.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT Suite]: https://docs.microsoft.com/azure/iot-suite/
[Azure IoT-eszközök SDK-k]: iot-hub-devguide-sdks.md