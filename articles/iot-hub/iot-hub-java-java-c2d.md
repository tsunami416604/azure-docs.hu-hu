---
title: Az Azure IoT Hub (Java) felhő eszközre üzenetek |} Microsoft Docs
description: Hogyan küldhetők felhő-eszközre küldött üzenetek egy eszközre egy Azure IoT hub Java SDK az Azure IoT-k használatával. A szimulált eszköz alkalmazásnak, hogy a felhő-eszközre küldött üzenetek fogadására és módosíthat egy háttér-alkalmazást a felhőből eszközre küldéséhez módosítása.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 83fe0e2e39d414120bb09ad58d17b5081caef745
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Az IoT-központ (Java) felhő eszközre-üzenetek
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel eszközök millióira közötti megbízható és biztonságos kétirányú kommunikáció engedélyezése és a megoldás háttérrendszere. A [Ismerkedés az IoT-központ] oktatóanyag bemutatja, hogyan létrehoz egy IoT-központot, azt egy eszközidentitás kiépítéséhez és kód egy szimulált eszköz alkalmazást, amelyet az eszköz a felhőbe küldött üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag épül [Ismerkedés az IoT-központ]. Megmutatja, hogyan számára:

* A megoldás háttérrendszeréhez, az IoT-központ keresztül egyetlen eszközt felhő eszközre üzeneteket küldeni.
* Az eszközön a felhőből eszközre üzeneteket fogadni.
* A megoldás háttérrendszeréhez, a kérelem kézbesítési nyugtázási (*visszajelzés*) IoT-központot egy eszközre küldött üzenetek esetében.

Felhő eszközre üzenetek további információt a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

Ez az oktatóanyag végén két Java konzol alkalmazások futtatása:

* **Szimulált eszköz**, az alkalmazás létrehozása a módosított változatát [Ismerkedés az IoT-központ], amely csatlakozik az IoT hub és felhő eszközre üzeneteket fogad.
* **küldési-c2d-üzenetek**, melyik felhőalapú eszközre üzenetet küld az IoT-központ szimulált eszköz alkalmazás, és a szállítási nyugtázási majd megkapja.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek (például C, Java és Javascript) keresztül Azure IoT eszközoldali SDK-k SDK támogatása. Csatlakoztassa az eszközt, az oktatóanyag kódot, és általában Azure IoT Hub kapcsolatos lépésenkénti útmutatót lásd: a [Azure IoT fejlesztői központ].

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A teljes működő verziójához a [Ismerkedés az IoT-központ](iot-hub-java-java-getstarted.md) vagy [folyamat IoT Hub eszköz-a-felhőbe küldött üzeneteket](iot-hub-java-java-process-d2c.md) oktatóanyag.
* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

## <a name="receive-messages-in-the-simulated-device-app"></a>A szimulált eszköz alkalmazás üzeneteket fogadni

Ebben a szakaszban módosítsa a szimulált eszköz alkalmazás létrehozott [Ismerkedés az IoT-központ] felhő eszközre üzenetek fogadása az IoT-központot.

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

2. Adja hozzá a következő **MessageCallback** osztályhoz tartozik, mint egy beágyazott osztály belsejében a **App** osztály. A **hajtható végre** metódus van meghívva, amikor az eszköz megkapja egy üzenetet az IoT-központ. Ebben a példában az eszköz mindig értesíti az IoT-központot, hogy befejeződött-e az üzenetet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Módosítsa a **fő** metódus létrehozása egy **AppMessageCallback** példány és a hívás a **setMessageCallback** módszert az alábbiak szerint az ügyfél megnyitása előtt:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Ha a átvitelhez MQTT vagy AMQP helyett a HTTPS PROTOKOLLT használja. a **DeviceClient** példány ellenőrzi az üzeneteket az IoT-központ ritkán (kevesebb mint 25 percenként). MQTT, az amqp-t és a HTTPS PROTOKOLLT támogatja, és az IoT-központ sávszélesség-szabályozási közötti különbségekről további információkért lásd: a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

4. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Felhő eszközre üzenet küldése

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amelyek a felhőből eszközre üzeneteket küld a szimulált eszköz alkalmazás. A hozzáadott eszköz az Eszközazonosítót van szüksége a [Ismerkedés az IoT-központ] oktatóanyag. Az IoT-központ kapcsolati karakterlánc, amely megtalálható a központ is kell a [Azure-portálon].

1. Nevű Maven-projekt létrehozása **c2d-üzenetküldés** parancsot a parancssorba az alábbi parancs segítségével. Megjegyzés: Ez a parancs egy egyetlen, hosszú parancsot:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg az új c2d-üzenetküldés mappát.

3. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a küldési-c2d-üzenetek mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség hozzáadása lehetővé teszi, hogy a **IOT hubbal-java-szolgáltatásügyfél** csomag kommunikáljon az IoT-központ szolgáltatás az alkalmazásban:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával][lnk-maven-service-search] tekintheti meg.

4. Mentse és zárja be a pom.xml fájlt.

5. Egy szövegszerkesztőben nyissa meg a send-c2d-messages\src\main\java\com\mycompany\app\App.java fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. A következő osztály szintű változók hozzáadásával a **App** osztály cseréje **{yourhubconnectionstring}** és **{yourdeviceid}** értékekkel a beállításértékeket korábban:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Cserélje le a **fő** metódus a következő kóddal. Ez a kód csatlakozik az IoT hub, egy üzenetet küld az eszköz és majd megvárja, hogy az eszköz kapott, és az üzenet feldolgozása nyugtázás:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért tartozó szakét Ez az oktatóanyag nem valósítja meg a bármely újrapróbálkozási házirendje. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése].


9. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorba a szimulált eszköz mappában futtassa a következő parancsot, telemetriai adatok küldése az IoT hub megkezdéséhez és a felhő eszközre üzenetek a központ által küldött figyelésére:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![A szimulált eszköz alkalmazás futtatása][img-simulated-device]

2. A parancssorba a küldési-c2d-üzenetek mappában futtassa a felhőből eszközre küldött és a visszajelzés visszaigazolás várja meg a következő parancsot:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Futtassa a parancsot a felhőből eszközre üzenet küldése][img-send-command]

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte felhő eszközre üzeneteket küldjön és fogadjon. 

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Ismerkedés az IoT-központ]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portálon]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22