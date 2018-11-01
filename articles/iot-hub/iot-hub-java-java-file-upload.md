---
title: Fájlok feltöltése eszközökről az Azure IoT hubhoz javával |} A Microsoft Docs
description: Hogyan tölthetők fel fájlok egy eszközről a felhőbe Javához készült Azure IoT eszközoldali SDK-val. Feltöltött fájlok vannak tárolva egy Azure storage blob-tárolóba.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: a3019a18fe28840f69a6e14b20dddc72516aa593
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419537"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Töltse fel a fájlokat az eszközről a felhőbe, az IoT hubbal

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ebben az oktatóanyagban található kódot használja fel a [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-java-java-c2d.md) megmutatjuk, hogyan használható az oktatóanyagban a [fájl feltöltése IoT Hub képességeiről](iot-hub-devguide-file-upload.md) feltölteni egy fájlt [az Azure blob tárolási](../storage/index.yml). Ez az oktatóanyag a következőket mutatja be:

* Biztonságosan adja meg az eszköz egy Azure blob-URI-fájl feltöltése.

* Az IoT Hub fájl feltöltése értesítések használatával elindíthatja a feldolgozása a fájlt az alkalmazás háttérrendszere.

A [telemetriát küldjön az IoT Hub (Java)](quickstart-send-telemetry-java.md) és [üzenetküldés felhőből az eszközre az IoT Hub (Java) szolgáltatással](iot-hub-java-java-c2d.md) oktatóanyagokból alapvető eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldési funkciói az IoT Hub. A [konfigurálása az IoT Hub üzenet-útválasztása](tutorial-routing.md) az oktatóanyag leírja, úgy az eszközt a felhőbe irányuló üzenetek meghízható tárolására az Azure blob storage-ban. Bizonyos esetekben azonban leképezése nem tudja az eszközöket az IoT Hub elfogad viszonylag kis eszköz – felhő üzenetek küldése az adatok egyszerűen. Példa:

* Nagy méretű képeket tartalmazó fájlok
* Videók
* Rezgés adatok mintavételezése, nagyon gyakori
* Valamilyen előre feldolgozott adatokat.

Ezek a fájlok jellemzően a felhőben, mint például az eszközök használatával feldolgozott kötegelt [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről kell felvidéki fájlok, biztonságának és megbízhatóságának IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén két Java-konzolalkalmazással futtassa:

* **a szimulált eszköz**, az alkalmazás az [küldési felhőből az eszközre irányuló üzenetek IoT hubbal való] oktatóanyag során létrehozott egy módosított verziója. Ez az alkalmazás feltölt egy fájlt az IoT hub által biztosított SAS URI használatával.

* **olvasási-fájl – feltöltés – értesítés**, amely a fájl feltöltése értesítéseket fogad az IoT hubról.

> [!NOTE]
> IoT Hub keresztül az Azure IoT eszközoldali SDK-k által támogatott számos eszközplatformok és nyelveken (például Javascript, C és .NET). Tekintse meg a [Azure IoT fejlesztői központ](http://azure.microsoft.com/develop/iot) részletesen ismerteti, hogy az eszköz csatlakoztatása Azure IoT Hub számára.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Töltsön fel egy fájlt egy eszköz alkalmazásból

Ebben a szakaszban módosítsa az eszköz alkalmazás létrehozott [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-java-java-c2d.md) feltölthet egy fájlt az IoT hubnak.

1. A képfájl másolja a `simulated-device` mappát, és nevezze át `myimage.png`.

2. Egy szövegszerkesztővel nyissa meg a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

3. Adja hozzá a változódeklarációt, hogy a **alkalmazás** osztály:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Fájl feltöltése a visszahívás állapotüzenetek feldolgozásához, adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Képek feltöltése az IoT hubhoz, adja hozzá a következő metódust a **alkalmazás** tölthet fel képeket a IoT Hub osztály:

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

6. Módosítsa a **fő** metódus hívása a **uploadFile** metódus az alábbi kódrészletben látható módon:

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

7. A következő paranccsal hozhat létre a **simulated-device** alkalmazás és a hibák keresése:

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

2. A parancssorban keresse meg az új `read-file-upload-notification` mappát.

3. Egy szövegszerkesztővel nyissa meg a `pom.xml` fájlt a `read-file-upload-notification` mappát, és adja hozzá a következő függőséget a **függőségek** csomópont. A függőség hozzáadása lehetővé teszi, hogy a **iothub-java-szolgáltatásügyfél** csomagot az alkalmazásban a IoT hub szolgáltatással való kommunikációra:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-service-client** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Mentse és zárja be a `pom.xml` fájlt.

5. Egy szövegszerkesztővel nyissa meg a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Adja hozzá a következő osztályszintű változókat az **alkalmazás** osztály:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. A fájl feltöltése a konzol adatainak nyomtatása, adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

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

9. Figyeli a fájl feltöltése értesítések szál indítása, adja hozzá a következő kódot a **fő** módszer:

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

10. Mentse és zárja be a `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` fájlt.

11. A következő paranccsal hozhat létre a **olvasási-fájl – feltöltés – értesítés** alkalmazás és a hibák keresése:

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

* [IoT hub létrehozása programozott módon](iot-hub-rm-template-powershell.md)
* [Bevezetés a C SDK-t](iot-hub-device-sdk-c-intro.md)
* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Eszköz szimulálása az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)