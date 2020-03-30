---
title: Felhőből eszközre irányuló üzenetek az Azure IoT Hub (Java) használatával | Microsoft dokumentumok
description: Felhőből az eszközre irányuló üzenetek küldése egy eszközre egy Azure IoT-központból az Azure IoT Java-alapú SDK-k használatával. Egy szimulált eszközalkalmazást módosítasz a felhőből az eszközre irányuló üzenetek fogadásához, és módosíthat egy háttéralkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 518f8057f222a628f8c3cd077cad4a7362e2cac8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110822"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Felhőből eszközre irányuló üzenetek küldése az IoT Hubbal (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között. A [Telemetriaküldése egy eszközről egy IoT hub](quickstart-send-telemetry-java.md) rövid útmutató bemutatja, hogyan hozhat létre egy IoT hub, egy eszköz identitását, és a kód egy szimulált eszköz alkalmazás, amely elküldi az eszköz-felhő üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetriai adatok küldése eszközről egy IoT hubra](quickstart-send-telemetry-java.md)épül. Bemutatja, hogyan kell a következőket megtenni:

* A megoldás háttér-tartalék, felhőből az eszközre üzeneteket küldhet egyetlen eszközre az IoT Hubon keresztül.

* Felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldás háttérrendszeréről kérje a kézbesítési nyugtázást (*visszajelzést)* az IoT Hubról az eszközre küldött üzenetekhez.

A felhőből az [eszközre irányuló üzenetekről az IoT Hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Java konzolalkalmazást futtat:

* **szimulált eszköz,** az alkalmazás módosított verziója létrehozott [telemetriai adatok küldése egy eszközről egy IoT hub,](quickstart-send-telemetry-java.md)amely csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre üzeneteket.

* **send-c2d-messages**, amely egy felhőből az eszközre üzenetet küld a szimulált eszközalkalmazásnak az IoT Hubon keresztül, majd megkapja a kézbesítési nyugtázást.

> [!NOTE]
> Az IoT Hub sdk-támogatással rendelkezik számos eszközplatformhoz és nyelvhez (beleértve a C, Java, Python és Javascript- et) az Azure IoT-eszköz SDK-kon keresztül. Az Azure [IoT Developer Center](https://azure.microsoft.com/develop/iot)című témakörben olvashat részletesen arról, hogyan csatlakoztathatja az eszközt az oktatóanyag kódjához, és általában az Azure IoT Hubhoz.

## <a name="prerequisites"></a>Előfeltételek

* A [telemetriai adatok küldése egy eszközről egy IoT hub](quickstart-send-telemetry-java.md) gyorsindítás, vagy az [Üzenet-útválasztás konfigurálása](tutorial-routing.md) az IoT Hub oktatóanyag teljes működő verziója.

* [Java SE Fejlesztői Készlet 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Győződjön meg róla, hogy a **Java** **8-at** a Hosszú távú támogatás alatt választja, hogy eljusson a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszközalkalmazásban

Ebben a szakaszban módosítja a szimulált eszköz alkalmazást, amelyet létrehozott [telemetriai adatok küldése egy eszközről egy IoT hub](quickstart-send-telemetry-java.md) felhőből az eszközre üzeneteket az IoT hub.

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

2. Adja hozzá a következő **MessageCallback** osztályt beágyazott osztályként az **alkalmazásosztályon** belül. A **végrehajtási** metódus meghívása akkor történik meg, amikor az eszköz üzenetet kap az IoT Hubtól. Ebben a példában az eszköz mindig értesíti az IoT hubot, hogy befejezte az üzenetet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Módosítsa a **fő** módszert **egy AppMessageCallback** példány létrehozásához, és hívja meg a **setMessageCallback** metódust, mielőtt megnyitja az ügyfelet az alábbiak szerint:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Ha https-t használ az MQTT vagy az AMQP helyett átvitelként, a **DeviceClient-példány** ritkán (kevesebb, mint 25 percenként) ellenőrzi az IoT Hubból érkező üzeneteket. Az MQTT, az AMQP és a HTTPS-támogatás és az IoT Hub-szabályozás közötti különbségekről [az IoT Hub fejlesztői útmutatójának üzenetkezelési szakaszában](iot-hub-devguide-messaging.md)olvashat bővebben.

4. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben létrehoz egy háttérszolgáltatás felhőből az eszközre üzeneteket az IoT hub on létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hub.](quickstart-send-telemetry-java.md) Felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből eszközre irányuló üzenet küldése

Ebben a szakaszban hozzon létre egy Java konzolalkalmazást, amely felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazásnak. Szüksége van az eszköz azonosítóját az eszköz hozzáadott az [eszköz küldése telemetriai adatok](quickstart-send-telemetry-java.md) at egy eszköz egy IoT hub rövid útmutató. Szüksége van az IoT hub kapcsolati karakterláncra is, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string)

1. Hozzon létre egy **Send-c2d-messages** nevű Maven-projektet a következő parancs segítségével a parancssorból. Megjegyzés: ez a parancs egyetlen, hosszú parancs:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg az új send-c2d-messages mappát.

3. Szövegszerkesztő használatával nyissa meg a pom.xml fájlt a send-c2d-messages mappában, és adja hozzá a következő függőséget a **függőségi** csomóponthoz. A függőség hozzáadása lehetővé teszi, hogy az **iothub-java-service-client** csomagot használja az alkalmazásban az IoT hub szolgáltatással való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Mentse és zárja be a pom.xml fájlt.

5. Szövegszerkesztő használatával nyissa meg a send-c2d-messages\src\main\java\com\mycompany\app\App.java fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adja hozzá a következő osztályszintű **App** változókat az alkalmazásosztályhoz, és cserélje le **a(z) {yourhubconnectionstring}** és **a ({yourdeviceid} elemet** a korábban megadott értékekre) helyett:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Cserélje le a **fő** módszert a következő kódra. Ez a kód csatlakozik az IoT hubhoz, üzenetet küld az eszköznek, majd megvárja, amíg az eszköz nyugtázza, hogy az eszköz fogadta és feldolgozta az üzenetet:

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
    > Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell megvalósítania, ahogy azt a cikk, [átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című cikk ben javasolt.

9. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A szimulált eszköz mappájában lévő parancssorban futtassa a következő parancsot a telemetriai adatok IoT hubba küldésének megkezdéséhez és a központból küldött felhőből az eszközre küldött üzenetek figyeléséhez:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-java-java-c2d/receivec2d.png)

2. A send-c2d-messages mappa parancssorában futtassa a következő parancsot a felhőből az eszközre irányuló üzenet küldéséhez, és várja meg a visszajelzések nyugtázását:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![A parancs futtatása a felhőből az eszközre irányuló üzenet elküldéséhez](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat felhőből az eszközre irányuló üzeneteket.

Az IoT Hubot használó teljes körű megoldások példáinak megtekintéséhez tekintse meg [az Azure IoT-megoldásgyorsítókat.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)
