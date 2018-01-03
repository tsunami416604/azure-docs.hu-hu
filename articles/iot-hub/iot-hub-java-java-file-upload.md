---
title: "Az Azure IoT Hub Java eszközökről fájlok feltöltése |} Microsoft Docs"
description: "Hogyan tölt fel az eszközről a felhőbe Javához készült Azure IoT-eszközök SDK használatával. Egy Azure blob tároló feltöltött fájlok tárolják."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: b6e1ef5a5b3f9298134a7c312ac7d1927cf41a7f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Az eszközről a felhőbe, IoT-központ fájlok feltöltése

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag épít, a kód a [IoT Hub-felhő eszközre üzenetek](iot-hub-java-java-c2d.md) mutatjuk be, hogyan használható az oktatóanyag a [fájl feltöltése képességeit az IoT-központ](iot-hub-devguide-file-upload.md) feltölteni a fájlt [Azure blob tárolási](../storage/index.yml). Az oktatóanyag bemutatja, hogyan számára:

- Biztonságosan adjon meg egy eszközt az Azure blob URI-fájl feltöltése.
- Az IoT-központ fájl feltöltése értesítések használatával indul el, az alkalmazás háttérbeli fájl feldolgozása.

A [Ismerkedés az IoT-központ](iot-hub-java-java-getstarted.md) és [IoT Hub-felhő eszközre üzenetek](iot-hub-java-java-c2d.md) oktatóanyagok alapvető eszköz-felhő és a felhő eszközre üzenetkezelési funkcióit az IoT-központ megjelenítése. A [folyamat eszköz felhőbe küldött üzeneteket](iot-hub-java-java-process-d2c.md) az oktatóanyag leírja, hogy megbízhatóan tárolja az eszköz a felhőbe küldött üzeneteket az Azure blob storage. Bizonyos esetekben azonban leképezése nem az eszközök elküldik üzenetbe a viszonylag kis eszközről a felhőbe, amely az IoT-központ fogadja az adatokat könnyen. Példa:

* Nagy fájlok, amelyek képeket
* Videók
* Nagy gyakoriságú lekérdező vibráció adatok
* Valamilyen előre feldolgozott adatokat.

Ezek a fájlok jellemzően eszközökkel, mint a felhőben feldolgozható [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.md) verem. Ha egy eszközről kell felvidéki fájlok, a biztonsága és megbízhatósága szempontjából az IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén két Java konzol alkalmazások futtatása:

* **Szimulált eszköz**, az alkalmazás létrehozása az [IoT hubbal küldési felhőből eszközre küldött üzenetek] oktatóanyag módosított változatát. Ez az alkalmazás az IoT hub által biztosított SAS URI-k használata tárolási feltölt egy fájlt.
* **olvasási-fájl-feltöltési-értesítési**, amely fájl feltöltése értesítéseket fogad az IoT hub.

> [!NOTE]
> Az IoT-központ Azure IoT-eszközök SDK-k segítségével számos eszköz platformok és nyelvek (például C, a .NET és a Javascript) támogatja. Tekintse meg a [Azure IoT fejlesztői központ] az eszköz csatlakoztatása az Azure IoT Hub részletes útmutatást.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Egy eszköz alkalmazás-fájl feltöltése

Ebben a szakaszban módosítsa az eszköz alkalmazás létrehozott [IoT Hub-felhő eszközre üzenetek](iot-hub-java-java-c2d.md) lehet feltölteni a fájlt az IoT-központot.

1. Egy képfájl másolja a `simulated-device` mappa, és nevezze át `myimage.png`.

1. Egy szövegszerkesztőben nyissa meg a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adjon hozzá a változó; a **App** osztály:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Fájl feltöltése visszahívási állapotüzenetek feldolgozásához, adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Az IoT-központ képek feltöltése, adja hozzá az alábbi metódust a **App** osztályt a képek feltöltése az IoT-központ:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

1. Módosítsa a **fő** metódust kell meghívni a **uploadFile** módszer, ahogy az a következő kódrészletet:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

1. A következő paranccsal hozhat létre a **szimulált eszköz** alkalmazás és a hibák:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Fájl feltöltése értesítést

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, hogy a fájl feltöltése értesítési üzeneteket fogad az IoT-központot.

Van szüksége a **iothubowner** kapcsolati karakterlánc az IoT hub, ez a szakasz befejezéséhez. A kapcsolati karakterlánc található a [Azure-portálon](https://portal.azure.com/) a a **megosztott hozzáférési házirend** panelen.

1. Nevű Maven-projekt létrehozása **olvasás-fájl-feltöltési-értesítési** parancsot a parancssorba az alábbi parancs segítségével. Megjegyzés: Ez a parancs egy egyetlen, hosszú parancsot:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. A parancssorban keresse meg az új `read-file-upload-notification` mappát.

1. Egy szövegszerkesztőben nyissa meg a `pom.xml` fájlt a `read-file-upload-notification` mappa, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség hozzáadása lehetővé teszi, hogy a **IOT hubbal-java-szolgáltatásügyfél** csomag kommunikáljon az IoT-központ szolgáltatás az alkalmazásban:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-szolgáltatásügyfél** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Mentse és zárja be a `pom.xml` fájlt.

1. Egy szövegszerkesztőben nyissa meg a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. A következő osztály szintű változók hozzáadásával a **App** osztály:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. A fájl feltöltése, a konzol adatainak nyomtatásához adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Indítsa el a szál, amely figyeli a fájl feltöltése értesítések, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

1. Mentse és zárja be a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

1. A következő paranccsal hozhat létre a **olvasás-fájl-feltöltési-értesítési** alkalmazás és a hibák:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A parancsot a parancssorba a `read-file-upload-notification` mappa, a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A parancsot a parancssorba a `simulated-device` mappa, a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Az alábbi képernyőfelvételen látható kimenetét a **szimulált eszköz** alkalmazást:

![Szimulált eszköz alkalmazás kimenete](media/iot-hub-java-java-upload/simulated-device.png)

Az alábbi képernyőfelvételen látható kimenetét a **olvasás-fájl-feltöltési-értesítési** alkalmazást:

![Olvasási-fájl-feltöltési-értesítés alkalmazás kimenete](media/iot-hub-java-java-upload/read-file-upload-notification.png)

A portál segítségével a tároló, konfigurálta a feltöltött fájl megtekintése:

![Fájl feltöltése](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte fájlfeltöltéseket eszközökről leegyszerűsítése érdekében az IoT-központ fájl feltöltése funkcióinak használatát. IoT hub-szolgáltatások és a forgatókönyvet a következő cikkek az eltérések felfedezése továbbra is:

* [Programozott módon létrehoz egy IoT-központot][lnk-create-hub]
* [C SDK bemutatása][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Egy eszköz szimulálva IoT oldala][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


