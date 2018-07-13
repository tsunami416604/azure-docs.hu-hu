---
title: Fájlok feltöltése eszközökről az Azure IoT hubhoz javával |} A Microsoft Docs
description: Hogyan tölthetők fel fájlok egy eszközről a felhőbe Javához készült Azure IoT eszközoldali SDK-val. Feltöltött fájlok vannak tárolva egy Azure storage blob-tárolóba.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 09580f3bb5d6f6f5ccb15adddf0cf1f9e19c2210
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38619403"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Töltse fel a fájlokat az eszközről a felhőbe, az IoT hubbal

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ebben az oktatóanyagban található kódot használja fel a [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-java-java-c2d.md) megmutatjuk, hogyan használható az oktatóanyagban a [fájl feltöltése IoT Hub képességeiről](iot-hub-devguide-file-upload.md) feltölteni egy fájlt [az Azure blob tárolási](../storage/index.yml). Ez az oktatóanyag a következőket mutatja be:

- Biztonságosan adja meg az eszköz egy Azure blob-URI-fájl feltöltése.
- Az IoT Hub fájl feltöltése értesítések használatával elindíthatja a feldolgozása a fájlt az alkalmazás háttérrendszere.

A [IoT Hub használatának első lépései](iot-hub-java-java-getstarted.md) és [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-java-java-c2d.md) oktatóanyagokból alapvető eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldési funkciói az IoT Hub. A [folyamat eszköz – felhő üzeneteket](tutorial-routing.md) az oktatóanyag leírja, úgy az eszközt a felhőbe irányuló üzenetek meghízható tárolására az Azure blob storage-ban. Bizonyos esetekben azonban leképezése nem tudja az eszközöket az IoT Hub elfogad viszonylag kis eszköz – felhő üzenetek küldése az adatok egyszerűen. Példa:

* Nagy méretű képeket tartalmazó fájlok
* Videók
* Rezgés adatok mintavételezése, nagyon gyakori
* Valamilyen előre feldolgozott adatokat.

Ezek a fájlok jellemzően a felhőben, mint például az eszközök használatával feldolgozott kötegelt [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről kell felvidéki fájlok, biztonságának és megbízhatóságának IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén két Java-konzolalkalmazással futtassa:

* **a szimulált eszköz**, az alkalmazás az [küldési felhőből az eszközre irányuló üzenetek IoT hubbal való] oktatóanyag során létrehozott egy módosított verziója. Ez az alkalmazás feltölt egy fájlt az IoT hub által biztosított SAS URI használatával.
* **olvasási-fájl – feltöltés – értesítés**, amely a fájl feltöltése értesítéseket fogad az IoT hubról.

> [!NOTE]
> IoT Hub keresztül az Azure IoT eszközoldali SDK-k által támogatott számos eszközplatformok és nyelveken (például Javascript, C és .NET). Tekintse meg a [Azure IoT fejlesztői központ] részletesen ismerteti, hogy az eszköz csatlakoztatása Azure IoT Hub számára.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Töltsön fel egy fájlt egy eszköz alkalmazásból

Ebben a szakaszban módosítsa az eszköz alkalmazás létrehozott [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-java-java-c2d.md) feltölthet egy fájlt az IoT hubnak.

1. A képfájl másolja a `simulated-device` mappát, és nevezze át `myimage.png`.

1. Egy szövegszerkesztővel nyissa meg a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adja hozzá a változódeklarációt, hogy a **alkalmazás** osztály:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Fájl feltöltése a visszahívás állapotüzenetek feldolgozásához, adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Képek feltöltése az IoT hubhoz, adja hozzá a következő metódust a **alkalmazás** tölthet fel képeket a IoT Hub osztály:

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

1. Módosítsa a **fő** metódus hívása a **uploadFile** metódus az alábbi kódrészletben látható módon:

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

1. A következő paranccsal hozhat létre a **simulated-device** alkalmazás és a hibák keresése:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltési üzenetet fogadni

Ebben a szakaszban egy Java-konzolalkalmazást, amely a fájl feltöltése értesítési üzeneteket fogad a IoT Hub létrehozása.

Van szüksége a **iothubowner** befejeződik ez a szakasz az IoT Hub kapcsolati karakterláncára. A kapcsolati karakterlánc található a [az Azure portal](https://portal.azure.com/) a a **megosztott hozzáférési szabályzat** panelen.

1. Hozzon létre egy nevű Maven-projektet **olvasási-fájl – feltöltés – értesítés** használja az alábbi parancsot a parancssorba. Megjegyzés: Ez a parancs nem egyetlen hosszú parancs:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. A parancssorban keresse meg az új `read-file-upload-notification` mappát.

1. Egy szövegszerkesztővel nyissa meg a `pom.xml` fájlt a `read-file-upload-notification` mappát, és adja hozzá a következő függőséget a **függőségek** csomópont. A függőség hozzáadása lehetővé teszi, hogy a **iothub-java-szolgáltatásügyfél** csomagot az alkalmazásban a IoT hub szolgáltatással való kommunikációra:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-service-client** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Mentse és zárja be a `pom.xml` fájlt.

1. Egy szövegszerkesztővel nyissa meg a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Adja hozzá a következő osztályszintű változókat az **alkalmazás** osztály:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. A fájl feltöltése a konzol adatainak nyomtatása, adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

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

1. Figyeli a fájl feltöltése értesítések szál indítása, adja hozzá a következő kódot a **fő** módszer:

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

1. A következő paranccsal hozhat létre a **olvasási-fájl – feltöltés – értesítés** alkalmazás és a hibák keresése:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A parancsot a parancssorba a `read-file-upload-notification` mappában futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A parancsot a parancssorba a `simulated-device` mappában futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Az alábbi képernyőfelvételen a kimenete a **simulated-device** alkalmazás:

![A szimulált eszközalkalmazás kimenete](media/iot-hub-java-java-upload/simulated-device.png)

Az alábbi képernyőfelvételen a kimenete a **olvasási-fájl – feltöltés – értesítés** alkalmazás:

![Olvasási-fájl – feltöltés – értesítés alkalmazás kimenete](media/iot-hub-java-java-upload/read-file-upload-notification.png)

A portál használatával beállított storage-tárolót a feltöltött fájl megtekintése:

![A feltöltött fájl](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan egyszerűsítheti a fájlok feltöltése eszközökről a fájl feltöltése képességeit az IoT Hub használatával. Folytathatja az IoT hub szolgáltatásainak, és az ezekben a cikkekben forgatókönyvek megismerése:

* [IoT hub létrehozása programozott módon][lnk-create-hub]
* [Bevezetés a C SDK-t][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Eszköz szimulálása az IoT Edge szolgáltatással][lnk-iotedge]

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


