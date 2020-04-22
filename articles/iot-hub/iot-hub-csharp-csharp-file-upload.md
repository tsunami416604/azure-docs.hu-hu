---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubra a .NET | Microsoft dokumentumok
description: Fájlok feltöltése az eszközről a felhőbe az Azure IoT-eszköz SDK.How to upload files from a device to the cloud using Azure IoT device SDK for .NET. A feltöltött fájlok egy Azure storage blobtárolóban tárolódnak.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 14472e84d425bf03a3c6a0c2dc558d4b8225caec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733401"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Fájlok feltöltése az eszközről a felhőbe az IoT Hub (.NET) segítségével

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag az [IoT Hub-oktatóanyaggal rendelkező felhőből eszközre üzenetek küldése](iot-hub-csharp-csharp-c2d.md) című dokumentumban található kódra épül, amely bemutatja, hogyan használhatja az IoT Hub fájlfeltöltési képességeit. Ez megmutatja, hogyan:

* Biztonságosan biztosítson egy eszközt egy Azure blob URI-val egy fájl feltöltéséhez.

* Az IoT Hub fájlfeltöltési értesítései segítségével indítsa el a fájl feldolgozását az alkalmazás háttérrendszerében.

A [Telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-dotnet.md) rövid útmutató és [felhőbe az eszközre üzenetek küldése](iot-hub-csharp-csharp-c2d.md) az IoT Hub oktatóanyag az alapvető eszköz-felhő és felhő-eszköz üzenetkezelési funkció az IoT Hub. Az [Üzenet-útválasztás konfigurálása az IoT Hubbal](tutorial-routing.md) oktatóanyag ismerteti az eszközről a felhőbe irányuló üzenetek megbízható tárolásának módját a Microsoft Azure Blob storage-ban. Bizonyos esetekben azonban nem tudja könnyen leképezni az eszközöket küldött adatokat az IoT Hub által fogadott viszonylag kis méretű eszközről felhőbe irányuló üzenetekre. Például:

* Képeket tartalmazó nagyfájlok

* Videók

* Nagy frekvenciájú rezgésadatok

* Az előre feldolgozott adatok valamilyen formája

Ezeket a fájlokat általában kötegelt a felhőben olyan eszközök használatával, mint az [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) verem használatával. Ha fájlokat kell feltöltenie egy eszközről, továbbra is használhatja az IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két .NET konzolalkalmazást futtat:

* **SimulatedDevice**. Ez az alkalmazás feltölt egy fájlt a tárolóba az IoT hub által biztosított SAS URI használatával. Ez egy módosított változata az alkalmazás létrehozott a [felhőküldése az eszközre üzenetek küldése](iot-hub-csharp-csharp-c2d.md) az IoT Hub oktatóanyag.

* **ReadFileUploadNotification**. Ez az alkalmazás fájlfeltöltési értesítéseket kap az IoT hubról.

> [!NOTE]
> Az IoT Hub számos eszközplatformot és nyelvet támogat, például a C, a Java, a Python és a Javascript az Azure IoT-eszköz SDK-kon keresztül. Tekintse meg az [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) lépésenkénti utasításokat, hogyan csatlakoztathatja az eszközt az Azure IoT Hubhoz.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése eszközalkalmazásból

Ebben a szakaszban módosíthatja a [felhőből az eszközre üzenetek küldése az IoT Hubbal](iot-hub-csharp-csharp-c2d.md) létrehozott eszközalkalmazást, hogy az IoT hubról felhőből az eszközre irányuló üzeneteket fogadhassa.

1. A Visual Studio Solution Explorer programban kattintson a jobb gombbal a **SimulatedDevice** projektre, és válassza a Meglévő elem **hozzáadása** > **parancsot.** Keressen egy képfájlt, és foglalja bele a projektbe. Ez az oktatóanyag feltételezi, hogy a kép neve `image.jpg`.

1. Kattintson a jobb gombbal a képre, és válassza **a Tulajdonságok parancsot.** Győződjön meg arról, hogy **a Copy to Output Directory** mindig **másolása**beállítás van beállítva.

    ![A Másolás kimeneti könyvtárba lemezképtulajdonság ának megjelenítése](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. A **Program.cs** fájlban adja hozzá a fájl tetejére a következő állításokat:

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

    A `UploadToBlobAsync` metódus a feltöltendő fájl nevét és adatfolyam-forrását veszi fel, és kezeli a feltöltést a tárolóba. A konzolalkalmazás megjeleníti a fájl feltöltéséhez szükséges időt.

1. Közvetlenül a **fő** módszer következő `Console.ReadLine()`sorának hozzáadása közvetlenül a következő szöveghez:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket kell megvalósítania, például az exponenciális visszamaradást, ahogy azt [az átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)javasolta.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben egy háttérszolgáltatást hoz létre, amely fájlfeltöltési értesítési üzeneteket kap a [telemetria küldése az eszközről egy IoT hubra](quickstart-send-telemetry-dotnet.md)létrehozott IoT hubról. A fájlfeltöltési értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltési értesítés fogadása

Ebben a szakaszban egy .NET konzolalkalmazást ír, amely fájlfeltöltési értesítési üzeneteket kap az IoT Hubtól.

1. Az aktuális Visual Studio-megoldásban válassza az**Új** > **projekt** **fájlja** > lehetőséget. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Nevezze el a projektet *ReadFileUploadNotification*. A **Megoldás csoportban**válassza **a Hozzáadás a megoldáshoz**lehetőséget. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

    ![A ReadFileUploadNotification projekt konfigurálása a Visual Studióban](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. A Megoldáskezelőben kattintson a jobb gombbal a **ReadFileUploadNotification** projektre, és válassza **a NuGet-csomagok kezelése parancsot.**

1. A **NuGet csomagkezelőben**válassza a **Tallózás**lehetőséget. Keresse meg és válassza a **Microsoft.Azure.Devices**elemet, majd válassza **a Telepítés**lehetőséget.

    Ez a lépés letölti, telepíti, és hozzáad egy hivatkozást az [Azure IoT szolgáltatás SDK NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.Devices/) a **ReadFileUploadNotification** projektben.

1. A projekt **Program.cs** fájljában adja hozzá a fájl tetejére a következő utasítást:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje `{iot hub connection string}` le a helyőrző értéket az IoT hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzői közben:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

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

    Vegye figyelembe, hogy ez a fogadási minta megegyezik a felhőből az eszközre irányuló üzenetek fogadásához használt mintával az eszközalkalmazásból.

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza **az Indítási projektek beállítása parancsot.**

1. A Common Properties Startup Project **(Gyakori tulajdonságok** > **indítási projekt) programban**válassza a Több **indítási projekt**lehetőséget, majd válassza a **ReadFileUploadNotification** és a **SimulatedDevice indítási műveletét.** **Start** A módosítások mentéséhez kattintson az **OK** gombra.

1. Nyomja **le az F5 billentyűt.** Mindkét alkalmazásnak el kell indulnia. A feltöltés nek az egyik konzolalkalmazásban befejeződve kell lennie, a másik konzolalkalmazás által fogadott feltöltési értesítési üzenetnek. Az Azure [Portalon](https://portal.azure.com/) vagy a Visual Studio Server Explorer segítségével ellenőrizheti a feltöltött fájl jelenlétét az Azure Storage-fiókban.

    ![Képernyőkép a kimeneti képernyőről](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az IoT Hub fájlfeltöltési képességeit az eszközökről történő fájlfeltöltés egyszerűsítéséhez. Az IoT Hub funkcióit és forgatókönyveit a következő cikkekkel fedezheti fel:

* [IoT-központ létrehozása programozott módon](iot-hub-rm-template-powershell.md)

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
