---
title: Felhőből az eszközre irányuló üzeneteket az Azure IoT Hub (Java) szolgáltatással |} A Microsoft Docs
description: Annak a felhőből az eszközre irányuló üzeneteket küld egy eszköz az Azure IoT SDK használata Javához készült Azure IoT hubról. Módosítja egy szimulált eszközalkalmazás felhőből az eszközre irányuló üzenetek fogadása és módosíthat egy háttér-alkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 9032a6903833ba819e09fd1ca11cd6b43d5485cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541689"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Az IoT Hub (Java) szolgáltatással felhőből az eszközre irányuló üzenetek küldéséhez

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel engedélyezheti a megbízható és biztonságos kétirányú kommunikációt több millió eszköz között, és megoldást biztosít a háttérrendszer. A [telemetriát küldjön az eszközről a Hub (Java)](quickstart-send-telemetry-java.md) az oktatóanyag bemutatja, hogyan hozzon létre egy IoT hubot, azt az eszközidentitás létrehozását és kód az eszköz a felhőbe irányuló üzeneteket küld egy szimulált eszközalkalmazás.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ebben az oktatóanyagban épül [telemetriát küldjön az eszközről az IoT Hub (Java)](quickstart-send-telemetry-java.md). Ez bemutatja, hogyan végezze el a következőket:

* A megoldás háttérrendszerének, a felhőből az eszközre irányuló üzenetek küldése IoT hubon keresztül egy adott eszköz.

* Az eszközön a felhőből az eszközre irányuló üzeneteket fogadni.

* A megoldás háttérrendszerének, a kérelmek kézbesítési nyugtázás (*visszajelzés*) az IoT Hub az eszközökre küldött üzenetek.

További információt talál a [felhőből az eszközre irányuló üzeneteket az IoT Hub fejlesztői útmutatójának](iot-hub-devguide-messaging.md).

Ez az oktatóanyag végén két Java-konzolalkalmazással futtassa:

* **a szimulált eszköz**, a létrehozott alkalmazás egy módosított verziója [telemetriát küldjön az eszközről a Hub (Java)](quickstart-send-telemetry-java.md), amely csatlakozik az IoT hubhoz, és megkapja a felhőből az eszközre.

* **küldési-c2d-üzenetek**, amely a felhőből az eszközre üzenetet küld az IoT hubon keresztül a szimulált eszközalkalmazásnak, és annak kézbesítési nyugtázási majd kap.

> [!NOTE]
> Az IoT Hub SDK számos eszközplatformok és nyelveken (például a C, Java és Javascript) keresztül az Azure IoT eszközoldali SDK-k támogatással rendelkezik. Az eszköz csatlakoztatása, ebben az oktatóanyagban a kódot, és általában az Azure IoT hubba a részletes útmutatót lásd: a [Azure IoT fejlesztői központ](https://azure.microsoft.com/develop/iot).

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy teljes működő verziójához a [telemetriát küldjön az eszközről a Hub (Java)](quickstart-send-telemetry-java.md) vagy a [konfigurálása az IoT Hub üzenet-útválasztása](tutorial-routing.md) oktatóanyag.

* A legújabb [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Aktív Azure-fiók. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban

Ebben a szakaszban módosítsa a szimulált eszközalkalmazás létrehozott [telemetriát küldjön az eszközről a Hub (Java)](quickstart-send-telemetry-java.md) felhőből az eszközre irányuló üzenetek fogadása az IoT hubról.

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

2. Adja hozzá a következő **MessageCallback** osztályhoz tartozik, mint egy beágyazott osztály belül a **alkalmazás** osztály. A **végrehajtása** metódus meghívásakor kerül, ha az eszköz megkapja egy üzenetet az IoT hubról. Ebben a példában az eszköz minden esetben értesítést küld az IoT hub, hogy befejeződött-e az üzenet:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Módosítsa a **fő** metódussal hoz létre egy **AppMessageCallback** példány, és hívja a **setMessageCallback** megnyitása előtt szállítják az ügyfél a következő metódust:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Ha az átvitelhez mqtt-ről vagy AMQP helyett a HTTPS PROTOKOLLT használja. a **DeviceClient** példány ellenőrzi az üzeneteket az IoT hub felől ritkán (kevesebb mint 25 percenként). MQTT, AMQP és a HTTPS-támogatás és az IoT Hub szabályozás közötti különbségekkel kapcsolatos további információkért lásd: a [üzenetkezelés az IoT Hub fejlesztői útmutatójának szakaszában](iot-hub-devguide-messaging.md).

4. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Ebben a szakaszban egy Java-konzolalkalmazást, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazás létrehozása. Az eszköz azonosítója, az eszköz a hozzáadott van szüksége a [telemetriát küldjön az eszközről a Hub (Java)](quickstart-send-telemetry-java.md) rövid. Emellett az IoT Hub kapcsolati karakterláncra, amely találhatja meg a hub a [az Azure portal](https://portal.azure.com).

1. Hozzon létre egy nevű Maven-projektet **c2d-üzeneteket küldhetünk** használja az alábbi parancsot a parancssorba. Megjegyzés: Ez a parancs nem egyetlen hosszú parancs:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg az új send-c2d-messages mappát.

3. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a send-c2d-messages mappában, és adja hozzá a következő függőséget a **függőségek** csomópont. A függőség hozzáadása lehetővé teszi, hogy a **iothub-java-szolgáltatásügyfél** csomagot az alkalmazásban a IoT hub szolgáltatással való kommunikációra:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-service-client** használatával [Maven keresési](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Mentse és zárja be a pom.xml fájlt.

5. Egy szövegszerkesztővel nyissa meg a send-c2d-messages\src\main\java\com\mycompany\app\App.java fájlt.

6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adja hozzá a következő osztályszintű változókat az **alkalmazás** osztályhoz, lecserélve **{yourhubconnectionstring}** és **{yourdeviceid}** azokra az értékekre korábban feljegyzett:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Cserélje le a **fő** módszer a következő kóddal. Ez a kód csatlakozik az IoT hubhoz, egy üzenetet küld az eszközre, és majd megvárja, amíg egy visszaigazolás, hogy az eszköz és az üzenetet dolgoz:
   
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
    > Az egyszerűség kedvéért a rizspálinkát Ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).


9. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A simulated-device mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a telemetriai adatok küldését az IoT hub és a hubról küldött felhőből az eszközre irányuló üzenetek figyeléséhez:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-java-java-c2d/receivec2d.png)

2. A Küldés-c2d-messages mappában egy parancssorban futtassa a következő parancsot a felhőből az eszközre irányuló üzenetküldés, és várjon, amíg a visszajelzés visszaigazolás:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Futtassa a parancsot a felhőből az eszközre irányuló üzenetek küldéséhez](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan a felhőből az eszközre irányuló üzenetek küldése és fogadása. 

Példák teljes, végpontok közötti megoldások, amely az IoT Hub használata a megtekintéséhez lásd: [Azure IoT-Megoldásgyorsítók](https://azure.microsoft.com/documentation/suites/iot-suite/).

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide.md).