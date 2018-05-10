---
title: Az Azure IoT hubot .NET eszközökről fájlok feltöltése |} Microsoft Docs
description: Hogyan tölthet fel a fájlokat az eszközről a felhőbe, .NET-keretrendszerhez készült Azure IoT-eszközök SDK használatával. Egy Azure blob tároló feltöltött fájlok tárolják.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 901b4b6c631d47a6c37eb232f66d8350faa9be76
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Tölt fel az eszközről a felhőbe az IoT hubbal .NET használatával

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag épít, a kód a [IoT Hub-felhő eszközre üzenetek](iot-hub-csharp-csharp-c2d.md) oktatóanyag mutatjuk be, hogyan használható az IoT-központ fájl feltöltése képességeit. Megmutatja, hogyan számára:

- Biztonságosan adjon meg egy eszközt az Azure blob URI-fájl feltöltése.
- Az IoT-központ fájl feltöltése értesítések használatával indul el, az alkalmazás háttérbeli fájl feldolgozása.

A [Ismerkedés az IoT-központ](iot-hub-csharp-csharp-getstarted.md) és [IoT Hub-felhő eszközre üzenetek](iot-hub-csharp-csharp-c2d.md) oktatóanyagok alapvető eszköz-felhő és a felhő eszközre üzenetkezelési funkcióit az IoT-központ megjelenítése. A [folyamat eszköz felhőbe küldött üzeneteket](iot-hub-csharp-csharp-process-d2c.md) az oktatóanyag leírja, hogy megbízhatóan tárolja az eszköz a felhőbe küldött üzeneteket az Azure blob storage. Bizonyos esetekben azonban leképezése nem az eszközök elküldik üzenetbe a viszonylag kis eszközről a felhőbe, amely az IoT-központ fogadja az adatokat könnyen. Példa:

* Nagy fájlok, amelyek képeket
* Videók
* Nagy gyakoriságú lekérdező vibráció adatok
* Néhány előre feldolgozott adatok formája

Ezek a fájlok jellemzően eszközökkel, mint a felhőben feldolgozható [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) verem. Ha egy eszközről tölt fel van szüksége, a biztonsága és megbízhatósága szempontjából az IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén két .NET konzol alkalmazások futtatása:

* **SimulatedDevice**, az alkalmazás létrehozása a módosított változatát a [IoT Hub-felhő eszközre üzenetek](iot-hub-csharp-csharp-c2d.md) oktatóanyag. Ez az alkalmazás az IoT hub által biztosított SAS URI-k használata tárolási feltölt egy fájlt.
* **ReadFileUploadNotification**, amely fájl feltöltése értesítéseket fogad az IoT hub.

> [!NOTE]
> Az IoT-központ Azure IoT-eszközök SDK-k segítségével számos eszköz platformok és nyelvek (például C, Java és Javascript) támogatja. Tekintse meg a [Azure IoT fejlesztői központ] az eszköz csatlakoztatása az Azure IoT Hub részletes útmutatást.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Egy eszköz alkalmazás-fájl feltöltése

Ebben a szakaszban módosítsa az eszköz alkalmazás létrehozott [IoT Hub-felhő eszközre üzenetek](iot-hub-csharp-csharp-c2d.md) felhő eszközre üzenetek fogadása az IoT-központot.

1. A Visual Studióban, kattintson a jobb gombbal a **SimulatedDevice** projektre, kattintson **Hozzáadás**, és kattintson a **meglévő cikk**. Navigáljon a képfájl, és adja hozzá a projektben. Ez az oktatóanyag azt feltételezi, hogy a lemezkép neve `image.jpg`.

1. Kattintson a jobb gombbal a rendszerképen, és kattintson **tulajdonságok**. Győződjön meg arról, hogy **másolása a kimeneti könyvtárba** értéke **mindig másolása**.

    ![][1]

1. Az a **Program.cs** fájlt, adja hozzá az alábbi utasításokat a fájl elejéhez:

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

    A `UploadToBlobAsync` metódus vesz igénybe a fájl nevét és az adatfolyam forrásból a fájl tölthető fel, és kezeli a feltöltés tárhelyre. A Konzolalkalmazás jeleníti meg, hogy a fájl feltöltéséhez szükséges idő.

1. Adja hozzá a következő metódust a **fő** módszer, mielőtt a jobb oldalon a `Console.ReadLine()` sor:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Az egyszerűség kedvéért tartozó szakét Ez az oktatóanyag nem valósítja meg a bármely újrapróbálkozási házirendje. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése].

## <a name="receive-a-file-upload-notification"></a>Fájl feltöltése értesítést

Ebben a szakaszban egy .NET-Konzolalkalmazás, hogy a fájl feltöltése értesítési üzeneteket fogad az IoT-központ írni.

1. A jelenlegi Visual Studio-megoldásban Visual C# Windows-projekt létrehozása használatával a **Konzolalkalmazás** projektsablon. Nevet a projektnek **ReadFileUploadNotification**.

    ![A Visual Studio új projekt][2]

1. A Megoldáskezelőben kattintson a jobb gombbal a **ReadFileUploadNotification** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. Az a **NuGet-Csomagkezelő** ablakban, keresse meg **Microsoft.Azure.Devices**, kattintson a **telepítése**, és fogadja el a használati feltételeket.

    Ez a művelet tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK NuGet-csomag] a a **ReadFileUploadNotification** projekt.

1. Az a **Program.cs** fájlt, adja hozzá az alábbi utasításokat a fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Helyettesítse be a helyőrző értékét az IoT hub kapcsolati karakterlánccal [Ismerkedés az IoT-központ]:

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
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Vegye figyelembe, hogy a fogadási mintát, a felhő-eszközre küldött üzenetek fogadása az eszköz alkalmazás használt azonos egy.

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

1. A Visual Studióban, kattintson a jobb gombbal a megoldás, és válassza ki **állítsa be indítási projektek**. Válassza ki **több kezdőprojekt**, majd jelölje be a **Start** művelet **ReadFileUploadNotification** és **SimulatedDevice**.

1. Nyomja le az **F5**. Mindkét alkalmazás elindul. Meg kell jelennie egy konzolalkalmazás befejeződött a feltöltés és a konzol más alkalmazás által fogadott feltöltés értesítési üzenetet. Használhatja a [Azure-portálon] vagy a Visual Studio Server Explorer Azure-tárfiókba, a feltöltött fájl meglétének ellenőrzése.

    ![][50]

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte fájlfeltöltéseket eszközökről leegyszerűsítése érdekében az IoT-központ fájl feltöltése funkcióinak használatát. IoT hub-szolgáltatások és a forgatókönyvet a következő cikkek az eltérések felfedezése továbbra is:

* [Programozott módon létrehoz egy IoT-központot][lnk-create-hub]
* [C SDK bemutatása][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure-portálon]: https://portal.azure.com/

[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot

[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure IoT szolgáltatás SDK NuGet-csomag]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
