---
title: Fájlok feltöltése az eszközökről az Azure IoT Hub a .NET-tel | Microsoft Docs
description: Fájlok feltöltése egy eszközről a felhőbe az Azure IoT Device SDK for .NET használatával. A feltöltött fájlok tárolása egy Azure Storage blob-tárolóban történik.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: f1001df52b3bbb54f3b872f23276957fa01a7da5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403199"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Fájlok feltöltése az eszközről a felhőbe IoT Hub .NET használatával

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag a felhőből az eszközre küldött [üzenetek küldésére](iot-hub-csharp-csharp-c2d.md) szolgáló kódot a IoT hub oktatóanyag segítségével mutatja be, amellyel megtudhatja, hogyan használhatja a IoT hub fájlfeltöltés funkcióit. A következőket mutatja be:

* A fájlok feltöltéséhez biztonságosan biztosítson egy Azure Blob URI-t tartalmazó eszközt.

* A fájl feltöltésével kapcsolatos értesítéseket használva aktiválja a fájlt az alkalmazás hátterében lévő IoT Hub.

A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-dotnet.md) gyors üzembe helyezése és a felhőből az eszközre irányuló [üzenetek küldése az IoT hub](iot-hub-csharp-csharp-c2d.md) oktatóanyaggal mutatja be a IoT hub alapszintű eszközről a felhőbe és a felhőből az eszközre való üzenetkezelés funkcióját. Az [üzenet-útválasztás konfigurálása IoT hub](tutorial-routing.md) oktatóanyagmal az eszközről a felhőbe irányuló üzenetek megbízható tárolásának módját ismerteti az Azure Blob Storage-ban. Bizonyos helyzetekben azonban nem lehet könnyedén leképezni az eszközök által a IoT Hub által elfogadott viszonylag kis eszközről a felhőbe küldött üzeneteket. Példa:

* Képeket tartalmazó nagyméretű fájlok
* Videók
* Nagy gyakorisággal vett vibrációs adatelemzés
* Az előfeldolgozott adatmennyiség valamilyen formája

Ezeket a fájlokat a rendszer általában a felhőben dolgozza fel a felhőben olyan eszközökkel, mint a [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről kell fájlokat feltölteni, továbbra is használhatja IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két .NET konzolos alkalmazást futtat:

* A **SimulatedDevice**a felhőből az [eszközre irányuló üzenetek küldése IoT hub](iot-hub-csharp-csharp-c2d.md) oktatóanyaggal létrehozott alkalmazás módosított verziója. Ez az alkalmazás feltölt egy fájlt a Storage-ba az IoT hub által biztosított SAS URI használatával.

* **ReadFileUploadNotification**, amely befogadja a fájlfeltöltés-értesítéseket az IoT hub-ból.

> [!NOTE]
> IoT Hub számos eszköz platformját és nyelvét támogatja (beleértve a C, Java és JavaScript rendszereket) az Azure IoT Device SDK-k használatával. Az eszköz Azure IoT Hubhoz való csatlakoztatásának részletes ismertetését az [Azure IoT fejlesztői központban](https://azure.microsoft.com/develop/iot) találja.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio

* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése egy eszköz alkalmazásból

Ebben a szakaszban a felhőből az eszközre irányuló [üzenetek küldése](iot-hub-csharp-csharp-c2d.md) az IoT hub-ból című témakörben található, a felhőből az eszközre irányuló üzenetek fogadásához létrehozott IoT hub.

1. A Visual Studióban kattintson a jobb gombbal a **SimulatedDevice** projektre, kattintson a **Hozzáadás**, majd a **meglévő elem**lehetőségre. Nyisson meg egy képfájlt, és vegye fel a projektbe. Ez az oktatóanyag feltételezi, hogy a `image.jpg`rendszerkép neve.

1. Kattintson a jobb gombbal a képre, majd kattintson a **Tulajdonságok**elemre. Győződjön meg arról, hogy a **Másolás a kimeneti könyvtárba** beállítás a **mindig másolás**.

    ![A rendszerkép tulajdonságának a másolás a kimeneti könyvtárba való frissítésének helye](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. A **program.cs** fájlban adja hozzá a következő utasításokat a fájl elejéhez:

    ```csharp
    using System.IO;
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    A `UploadToBlobAsync` metódus a feltöltött fájl fájlnevét és stream-forrását veszi fel, és kezeli a feltöltést a tárolóba. A konzol alkalmazás megjeleníti a fájl feltöltéséhez szükséges időt.

1. Adja hozzá a következő metódust a **Main** metódushoz, közvetlenül `Console.ReadLine()` a sor előtt:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például az exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely a fájlfeltöltés-értesítési üzeneteket fogadja a IoT hub-ból, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-dotnet.md)hozott létre. A fájlfeltöltés-értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltés-értesítés fogadása

Ebben a szakaszban olyan .NET-konzol alkalmazást ír, amely a IoT Hub címről érkező fájlfeltöltés-értesítési üzeneteket fogad.

1. A Visual Studio jelenlegi megoldásában hozzon létre egy C# vizuális Windows-projektet a **konzol alkalmazás** -projekt sablonnal. Nevezze el a projekt **ReadFileUploadNotification**.

    ![Új projekt a Visual Studióban](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. Megoldáskezelő kattintson a jobb gombbal a **ReadFileUploadNotification** projektre, majd kattintson a **NuGet-csomagok kezelése..** . elemre.

3. A **NuGet csomagkezelő** ablakban keresse meg a **Microsoft. Azure. Devices**kifejezést, kattintson a **telepítés**gombra, és fogadja el a használati feltételeket.

    Ez a művelet letölti, telepíti és hozzáadja az [Azure IoT Service SDK NuGet csomagra](https://www.nuget.org/packages/Microsoft.Azure.Devices/) mutató hivatkozást a **ReadFileUploadNotification** projektben.

4. A **program.cs** fájlban adja hozzá a következő utasításokat a fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le a IoT hub-beli, a korábban a [IoT hub-kapcsolatok karakterláncának](#get-the-iot-hub-connection-string)lekérése során átmásolt karakterláncra: `{iot hub connection string}`

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Megjegyzés: Ez a fogadási minta ugyanaz, mint a felhőből az eszközre irányuló üzenetek fogadása az eszköz alkalmazásból.

7. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

1. A Visual Studióban kattintson a jobb gombbal a megoldásra, majd válassza az **indítási projektek beállítása**lehetőséget. Válasszon **több indítási projektet**, majd válassza ki a **Start** műveletet a **ReadFileUploadNotification** és a **SimulatedDevice**.

2. Nyomja le az **F5**billentyűt. Mindkét alkalmazásnak el kell indulnia. Látnia kell a feltöltést egy adott konzol alkalmazásban, és a másik konzol alkalmazás által fogadott feltöltési értesítési üzenetet. A [Azure Portal](https://portal.azure.com/) vagy a Visual Studio Server Explorer segítségével megkeresheti a feltöltött fájl jelenlétét az Azure Storage-fiókban.

    ![A kimeneti képernyőt ábrázoló képernyőfelvétel](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a IoT Hub fájlfeltöltés funkcióit az eszközökről történő fájlfeltöltés egyszerűsítése érdekében. A IoT hub funkcióit és forgatókönyveit továbbra is megismerheti a következő cikkekkel:

* [IoT hub programozott módon történő létrehozása](iot-hub-rm-template-powershell.md)

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [AI üzembe helyezése az Edge-eszközökön Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
