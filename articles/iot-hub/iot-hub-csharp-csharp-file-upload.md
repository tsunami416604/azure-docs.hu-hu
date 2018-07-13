---
title: Fájlok feltöltése eszközökről az Azure IoT hubba a .NET-tel |} A Microsoft Docs
description: Hogyan tölthetők fel fájlok egy eszközről a felhőbe az Azure IoT eszközoldali SDK-val .NET-keretrendszerhez készült. Feltöltött fájlok vannak tárolva egy Azure storage blob-tárolóba.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 8c57f93a755d01dc17b369e712285c2ac8f0ef37
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309913"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Töltse fel a fájlokat az eszközről a felhőbe az IoT hubhoz .NET használatával

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ebben az oktatóanyagban található kódot használja fel a [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-csharp-csharp-c2d.md) oktatóanyag megmutatjuk, hogyan használhatja az IoT Hub fájl feltöltése képességeit. Azt mutatja, hogy:

- Biztonságosan adja meg az eszköz egy Azure blob-URI-fájl feltöltése.
- Az IoT Hub fájl feltöltése értesítések használatával elindíthatja a feldolgozása a fájlt az alkalmazás háttérrendszere.

A [IoT Hub használatának első lépései](iot-hub-csharp-csharp-getstarted.md) és [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-csharp-csharp-c2d.md) oktatóanyagokból alapvető eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldési funkciói az IoT Hub. A [folyamat eszköz – felhő üzeneteket](tutorial-routing.md) az oktatóanyag leírja, úgy az eszközt a felhőbe irányuló üzenetek meghízható tárolására az Azure blob storage-ban. Bizonyos esetekben azonban leképezése nem tudja az eszközöket az IoT Hub elfogad viszonylag kis eszköz – felhő üzenetek küldése az adatok egyszerűen. Példa:

* Nagy méretű képeket tartalmazó fájlok
* Videók
* Rezgés adatok mintavételezése, nagyon gyakori
* Valamilyen előre feldolgozott adatok

Ezek a fájlok jellemzően a felhőben, mint például az eszközök használatával feldolgozott kötegelt [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Amikor szüksége van egy eszközön a fájlok feltöltéséhez, biztonságának és megbízhatóságának IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén két .NET-konzolalkalmazással futtassa:

* **SimulatedDevice**, a létrehozott alkalmazás egy módosított verziója a [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-csharp-csharp-c2d.md) oktatóanyag. Ez az alkalmazás feltölt egy fájlt az IoT hub által biztosított SAS URI használatával.
* **ReadFileUploadNotification**, amely a fájl feltöltése értesítéseket fogad az IoT hubról.

> [!NOTE]
> IoT Hub keresztül az Azure IoT eszközoldali SDK-k által támogatott számos eszközplatformok és nyelveken (például a C, Java és Javascript). Tekintse meg a [Azure IoT fejlesztői központ] részletesen ismerteti, hogy az eszköz csatlakoztatása Azure IoT Hub számára.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Töltsön fel egy fájlt egy eszköz alkalmazásból

Ebben a szakaszban módosítsa az eszköz alkalmazás létrehozott [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-csharp-csharp-c2d.md) felhőből az eszközre irányuló üzenetek fogadása az IoT hubról.

1. A Visual Studióban kattintson a jobb gombbal a **SimulatedDevice** projektre, kattintson a **Hozzáadás**, és kattintson a **meglévő elem**. Lépjen egy képfájlt, és adja hozzá a projekthez. Ez az oktatóanyag feltételezi, hogy a lemezkép neve `image.jpg`.

1. Kattintson a jobb gombbal a lemezképet, és kattintson **tulajdonságok**. Győződjön meg arról, hogy **Copy to Output Directory** értékre van állítva **mindig Másolás**.

    ![][1]

1. Az a **Program.cs** fájlt, adja hozzá a következő utasításokat a fájl elejéhez:

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

    A `UploadToBlobAsync` módszer vesz igénybe a fájl neve és a stream forrás fájl tölthető fel, és a feltöltés, a storage kezeli. A Konzolalkalmazás jeleníti meg a fájl feltöltéséhez szükséges idő.

1. Adja hozzá a következő metódust a **fő** metódus, mielőtt a jobb oldalon a `Console.ReadLine()` sor:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Az egyszerűség kedvéért a rizspálinkát Ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), az MSDN-cikkben leírtak implementálandó [Átmeneti hibák kezelése].

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltési üzenetet fogadni

Ebben a szakaszban egy .NET-konzolalkalmazást, amely a fájl feltöltése értesítési üzeneteket fogad a IoT Hub írhat.

1. A jelenlegi Visual Studio-megoldásban, hozzon létre egy Visual C# Windows projektet a **Konzolalkalmazás** projektsablonnal. Adja a projektnek **ReadFileUploadNotification**.

    ![A Visual Studio új projekt][2]

1. A Megoldáskezelőben kattintson a jobb gombbal a **ReadFileUploadNotification** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. Az a **NuGet-Csomagkezelő** ablakban, keresse meg **Microsoft.Azure.Devices**, kattintson a **telepítése**, és fogadja el a használati feltételeket.

    Ez a művelet letölti, telepíti, és hozzáad egy hivatkozást a [Az Azure IoT szolgáltatás SDK NuGet-csomagot] a a **ReadFileUploadNotification** projekt.

1. Az a **Program.cs** fájlt, adja hozzá a következő utasításokat a fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az IoT hub kapcsolati karakterláncra az [Ismerkedés az IoT Hub]:

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

    Vegye figyelembe a receive minta a felhőből az eszközre irányuló üzenetek fogadása az eszközalkalmazástól érkező ugyanaz.

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

1. A Visual Studióban kattintson a jobb gombbal a megoldás, és válassza ki **állítsa be indítási projektek**. Válassza ki **több kezdőprojekt**, majd válassza ki a **Start** műveletet **ReadFileUploadNotification** és **SimulatedDevice**.

1. Nyomja meg **F5**. Mindkét alkalmazás kell kezdenie. A feltöltés befejeződött egy konzolalkalmazást, és a konzol más alkalmazás által fogadott feltöltési értesítő üzenetet kell megjelennie. Használhatja a [Azure Portal] vagy a Visual Studio Server Explorer az Azure Storage-fiókban a feltöltött fájl meglétének ellenőrzése.

    ![][50]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan egyszerűsítheti a fájlok feltöltése eszközökről a fájl feltöltése képességeit az IoT Hub használatával. Folytathatja az IoT hub szolgáltatásainak, és az ezekben a cikkekben forgatókönyvek megismerése:

* [IoT hub létrehozása programozott módon][lnk-create-hub]
* [Bevezetés a C SDK-t][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure Portal]: https://portal.azure.com/

[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot

[Átmeneti hibák kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Az Azure IoT szolgáltatás SDK NuGet-csomagot]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
