---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubba Java-val | Microsoft dokumentumok
description: Fájlok feltöltése az eszközről a felhőbe az Azure IoT-eszköz Java-alapú SDK használatával. A feltöltött fájlok egy Azure storage blobtárolóban tárolódnak.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f0753827fe5f7f2b866726683d4cb1f205da4599
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732465"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>Fájlok feltöltése az eszközről a felhőbe az IoT Hub (Java) segítségével

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag az [IoT Hubmal való felhőből eszközre üzenetek küldése](iot-hub-java-java-c2d.md) című oktatóanyagban található kódra épül, amely bemutatja, hogyan használhatja az [IoT Hub fájlfeltöltési funkcióit](iot-hub-devguide-file-upload.md) egy fájl [Azure blobstorage-ba](../storage/index.yml)való feltöltéséhez. Ez az oktatóanyag a következőket mutatja be:

* Biztonságosan biztosítson egy eszközt egy Azure blob URI-val egy fájl feltöltéséhez.

* Az IoT Hub fájlfeltöltési értesítései segítségével indítsa el a fájl feldolgozását az alkalmazás háttérrendszerében.

A [Telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-java.md) rövid útmutató és [felhőbe az eszközre üzenetek küldése](iot-hub-java-java-c2d.md) az IoT Hub oktatóanyag az alapvető eszköz-felhő és felhő-eszköz üzenetkezelési funkció az IoT Hub. Az [Üzenetek útválasztásának konfigurálása az IoT Hub-mal](tutorial-routing.md) oktatóanyag ismerteti az eszközök közötti üzenetek megbízható tárolásának módját az Azure blob storage-ban. Bizonyos esetekben azonban nem tudja könnyen leképezni az eszközöket küldött adatokat az IoT Hub által fogadott viszonylag kis méretű eszközről felhőbe irányuló üzenetekbe. Például:

* Képeket tartalmazó nagyfájlok
* Videók
* Nagy frekvenciájú rezgésadatok
* Valamilyen előre feldolgozott adat.

Ezeket a fájlokat általában kötegelt a felhőben olyan eszközök használatával, mint az [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) verem használatával. Ha egy eszközről kell felnyitott fájlokat használnia, továbbra is használhatja az IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két Java konzolalkalmazást futtat:

* **szimulált eszköz**, az alkalmazás módosított verziója a [Felhőből eszközre üzenetek küldése az IoT Hubba] oktatóanyagban. Ez az alkalmazás feltölt egy fájlt a tárolóba az IoT hub által biztosított SAS URI használatával.

* **read-file-upload-notification**, amely fájlfeltöltési értesítéseket kap az IoT hubról.

> [!NOTE]
> Az IoT Hub számos eszközplatformot és nyelvet támogat (például a C, a .NET és a Javascript) az Azure IoT-eszköz SDK-kon keresztül. Tekintse meg az [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) lépésenkénti utasításokat, hogyan csatlakoztathatja az eszközt az Azure IoT Hubhoz.

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Fejlesztői Készlet 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Győződjön meg róla, hogy a **Java** **8-at** a Hosszú távú támogatás alatt választja, hogy eljusson a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése eszközalkalmazásból

Ebben a szakaszban módosíthatja a [felhőből az eszközre üzenetek küldése az IoT Hubbal](iot-hub-java-java-c2d.md) létrehozott eszközalkalmazást, hogy egy fájlt töltsön fel az IoT hubra.

1. Másolja a képfájlt `simulated-device` a mappába, és nevezze át. `myimage.png`

2. Szövegszerkesztő használatával nyissa `simulated-device\src\main\java\com\mycompany\app\App.java` meg a fájlt.

3. Adja hozzá a **App** változódeklarációt az alkalmazásosztályhoz:

    ```java
    private static String fileName = "myimage.png";
    ```

4. A fájlfeltöltési állapot-visszahívási üzenetek feldolgozásához **App** adja hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Képek feltöltéséhez az IoT Hubba, **App** adja hozzá a következő módszert az alkalmazásosztályba, hogy képeket töltsön fel az IoT Hubra:

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

6. Módosítsa a **fő** metódust a **uploadFile** metódus hívásához a következő kódrészletben látható módon:

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

7. A következő paranccsal megépítheti a **szimulált eszközalkalmazást,** és ellenőrizze a hibákat:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben létrehoz egy háttérszolgáltatást, amely fájlfeltöltési értesítési üzeneteket kap az [Eszközről egy IoT hubra](quickstart-send-telemetry-java.md)létrehozott IoT hubról. A fájlfeltöltési értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltési értesítés fogadása

Ebben a szakaszban hozzon létre egy Java konzolalkalmazást, amely fájlfeltöltési értesítési üzeneteket kap az IoT Hubtól.

1. Hozzon létre egy Maven **projektet, amelyet read-file-upload-notification** -nek neveznek a következő paranccsal a parancssorból. Megjegyzés: ez a parancs egyetlen, hosszú parancs:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg `read-file-upload-notification` az új mappát.

3. Szövegszerkesztő használatával nyissa `pom.xml` meg a `read-file-upload-notification` mappában lévő fájlt, és adja hozzá a következő függőséget a **függőségi** csomóponthoz. A függőség hozzáadása lehetővé teszi, hogy az **iothub-java-service-client** csomagot használja az alkalmazásban az IoT hub szolgáltatással való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Mentse és `pom.xml` zárja be a fájlt.

5. Szövegszerkesztő használatával nyissa `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` meg a fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{Your IoT Hub connection string}` le a helyőrző értéket az IoT hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzői közben:](#get-the-iot-hub-connection-string)

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. A fájlfeltöltéssel kapcsolatos információk konzolra történő nyomtatásához **App** adja hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
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

9. A fájlfeltöltési értesítéseket figyelő hozzászóláslánc elindításához adja hozzá a következő kódot a **fő** metódushoz:

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

10. Mentse és `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` zárja be a fájlt.

11. A következő paranccsal hozhassa létre az **olvasási fájl-feltöltési értesítési** alkalmazást, és ellenőrizze a hibákat:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A `read-file-upload-notification` mappa parancssorában futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A `simulated-device` mappa parancssorában futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

A következő képernyőképen a **szimulált eszközalkalmazás** kimenete látható:

![Kimenet szimulált eszközalkalmazásból](media/iot-hub-java-java-upload/simulated-device.png)

A következő képernyőképen a **read-file-upload-notification** alkalmazás kimenete látható:

![Kimenet a read-file-upload-notification alkalmazásból](media/iot-hub-java-java-upload/read-file-upload-notification.png)

A portál segítségével megtekintheti a feltöltött fájlt a konfigurált tárolóban:

![Feltöltött fájl](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az IoT Hub fájlfeltöltési képességeit az eszközökről történő fájlfeltöltés egyszerűsítéséhez. Az IoT hub funkcióit és forgatókönyveit a következő cikkekkel folytathatja:

* [IoT-központ létrehozása programozott módon](iot-hub-rm-template-powershell.md)

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Eszköz szimulálása IoT Edge-el](../iot-edge/tutorial-simulate-device-linux.md)
