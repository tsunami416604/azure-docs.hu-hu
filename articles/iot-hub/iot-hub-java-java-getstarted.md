---
title: Az Azure IoT Hub használata a Javával – első lépések | Microsoft Docs
description: Az Azure IoT Hub használata a Javával – első lépések oktatóanyag. Az Azure IoT Hubot és a Javát a Microsoft Azure IoT SDK-kal együtt használva az eszközök internetes hálózatán alapuló megoldást valósíthat meg.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: dobett

---
# Ismerkedés az Azure IoT Hub for Java szolgáltatással
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Az oktatóanyag végén három Java-konzolalkalmazással fog rendelkezni:

* a **create-device-identity** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközhöz.
* a **read-d2c-messages** megjeleníti a szimulált eszköz által küldött telemetriát.
* a **simulated-device** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és másodpercenként telemetriai üzenetet küld az AMQPS protokoll használatával.

> [!NOTE]
> Különböző SDK-kat használhat az alkalmazások összeállításához, hogy eszközökön és a megoldás háttérrendszerén is fussanak. Ezekről az [IoT Hub SDKs][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Java SE 8. <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a Javát ehhez az oktatóprogramhoz Windows vagy Linux rendszeren.
* Maven 3  <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a Mavent ehhez az oktatóprogramhoz Windows vagy Linux rendszeren.
* Aktív Azure-fiók. (Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Az utolsó lépésben jegyezze fel az **Elsődleges kulcs** értékét, majd kattintson az **Üzenetküldés** lehetőségre. Az **Üzenetküldés** panelen jegyezze fel az **Event Hub-kompatibilis nevet** és az **Event Hub-kompatibilis végpontot**. Erre a három értékre szüksége lesz a **read-d2c-messages** alkalmazás létrehozásakor.

![Az Azure Portal IoT Hub Üzenetküldés panelje][6]

Létrehozott egy IoT Hubot, és rendelkezik az oktatóanyag teljesítéséhez szükséges IoT Hub-állomásnévvel és kapcsolati karakterlánccal, IoT Hub elsődleges kulccsal, valamint Event Hubs-kompatibilis névvel és végponttal.

## Eszközidentitás létrehozása
Ebben a szakaszban egy Java-konzolalkalmazást hoz létre, amely egy új eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az eszközidentitás-jegyzékbe. További információkért lásd az [Azure IoT Hub fejlesztői útmutató][lnk-devguide-identity] **Eszközidentitás-jegyzék** című szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

1. Hozzon létre egy új, iot-java-get-started nevű üres mappát. Az iot-java-get-started mappában hozzon létre egy új, **create-device-identity** nevű Maven-projektet a következő beírásával a parancssorba. Látható, hogy ez egyetlen hosszú parancs:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. A parancssorban keresse meg az új create-device-identity mappát.
3. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a create-device-identity mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ezzel használhatja az iothub-service-sdk csomagot az alkalmazásban:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
4. Mentse és zárja be a pom.xml fájlt.
5. Egy szövegszerkesztővel nyissa meg a create-device-identity\src\main\java\com\mycompany\app\App.java fájlt.
6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:
   
    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Adja a következő osztályszintű változókat az **App** osztályhoz, lecserélve a **{yourhubconnectionstring}** helyőrzőt a korábban feljegyzett értékkel:
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Módosítsa úgy a **main** metódus aláírását, hogy tartalmazza az alábbi kivételeket:
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Adja hozzá a következő kódot a **main** metódus törzseként. Ez a kód egy *javadevice* nevű eszközt hoz létre az IoT Hub identitásjegyzékében, ha még nem létezik. Ezután megjeleníti az eszközazonosítót és a kulcsot, amelyekre később szüksége lesz:
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Mentse és zárja be az App.java fájlt.
11. Ha a **create-device-identity** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a create-device-identity mappában:
    
    ```
    mvn clean package -DskipTests
    ```
12. Ha a **create-device-identity** alkalmazást a Maven használatával szeretné futtatni, futtassa a következő parancsot a parancssorban a create-device-identity mappában:
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Jegyezze fel az **eszköz azonosítóját** és az **eszköz kulcsát**. Ezekre később szüksége lesz, amikor az IoT Hubhoz eszközként csatlakozó alkalmazást hoz létre.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak a hub biztonságos elérésének biztosításához tárolja az eszközidentitást. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információt az [IoT Hub fejlesztői útmutatóban][lnk-devguide-identity] talál.
> 
> 

## Az eszközről a felhőbe irányuló üzenetek fogadása
Ebben a szakaszban egy Java-konzolalkalmazást hoz létre, amely az eszközről a felhőbe irányuló üzeneteket olvas az IoT Hubról. Az IoT Hub egy [Event Hub][lnk-event-hubs-overview]-kompatibilis végpontot tesz közzé, hogy lehetővé tegye az eszközről a felhőbe irányuló üzenetek olvasását. Az egyszerűség érdekében ez az oktatóanyag egy alapszintű olvasót hoz létre, amely nem alkalmas nagy átviteli sebességű üzemelő példányokhoz. Az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban lásd [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot. Az Event Hubs szolgáltatástól érkező üzenetek feldolgozásával kapcsolatos további információkért lásd a [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. Ez az IoT Hub Event Hub-kompatibilis végpontjaira érvényes.

> [!NOTE]
> Az eszközről a felhőbe irányuló üzenetek olvasásához használt Event Hubs-kompatibilis végpontok mindig az AMQPS protokollt használják.
> 
> 

1. Az *Eszközidentitás létrehozása* szakaszban létrehozott iot-java-get-started mappában hozzon létre egy új **read-d2c-messages** nevű Maven-projektet a következő parancs beírásával a parancssorba. Látható, hogy ez egyetlen hosszú parancs:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. A parancssorban keresse meg az új read-d2c-messages mappát.
3. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a read-d2c-messages mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ezzel használhatja az eventhubs-client csomagot az alkalmazásban az Event Hub-kompatibilis végpontról végzett olvasáshoz:
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```
4. Mentse és zárja be a pom.xml fájlt.
5. Egy szövegszerkesztővel nyissa meg a read-d2c-messages\src\main\java\com\mycompany\app\App.java fájlt.
6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
   
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```
7. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le a **{youriothubkey}**, **{youreventhubcompatibleendpoint}** és **{youreventhubcompatiblename}** helyőrzőket a korábban feljegyzett értékekre:
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Adja hozzá a következő **receiveMessages** metódust az **App** osztályhoz. Ez a metódus egy **EventHubClient** példányt hoz létre, amely az Event Hub-kompatibilis végponthoz csatlakozik, majd aszinkron módon létrehoz egy **PartitionReceiver** példányt, amely egy Event Hub-partícióról olvas. Folyamatosan hurkokat alkot, és kinyomtatja az üzenet részleteit az alkalmazás befejezéséig.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Ez a metódus szűrőt használ a fogadó létrehozásakor, hogy a fogadó csak a fogadó futtatásának megkezdése után az IoT Hubra küldött üzeneteket olvassa. Ez tesztkörnyezetben hasznos, mivel láthatja az aktuális üzeneteket. Éles környezetben azonban a kódnak biztosítania kell, hogy az összes üzenetet feldolgozza. További információért lásd a következő oktatóanyagot: [Eszközről a felhőbe irányuló IoT Hub-üzenetek feldolgozása][lnk-process-d2c-tutorial].
   > 
   > 
9. Módosítsa úgy a **main** metódus aláírását, hogy tartalmazza az alábbi kivételt:
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Adja hozzá a következő kódot a **main** metódushoz az **App** osztályban. Ez a kód létrehozza a két **EventHubClient** és **PartitionReceiver** példányt, és lehetővé teszi, hogy bezárja az alkalmazást az üzenetek feldolgozásának befejezése után:
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > Ez a kód azt feltételezi, hogy az IoT hubot az F1 (ingyenes) rétegen hozta létre. Az ingyenes IoT hub két, „0” és „1” nevű partícióval rendelkezik.
    > 
    > 
11. Mentse és zárja be az App.java fájlt.
12. Ha a **read-d2c-messages** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a read-d2c-messages mappában:
    
    ```
    mvn clean package -DskipTests
    ```

## Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy Java-konzolalkalmazást hoz létre, amely egy, az eszközről a felhőbe irányuló üzeneteket egy IoT Hubra küldő eszközt szimulál.

1. Az *Eszközidentitás létrehozása* szakaszban létrehozott iot-java-get-started mappában hozzon létre egy új, **simulated-device** nevű Maven-projektet a következő parancs beírásával a parancssorba. Látható, hogy ez egyetlen hosszú parancs:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. A parancssorban keresse meg az új simulated-device mappát.
3. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a simulated-device mappában, és adja hozzá a következő függőségeket a **függőségek** csomóponthoz. Ezzel használhatja az iothub-java-client csomagot az alkalmazásban, hogy kommunikáljon az IoT hubbal, és hogy Java-objektumokat szerializáljon JSON formátumban:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Mentse és zárja be a pom.xml fájlt.
5. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.
6. Adja hozzá a következő **importálási** utasításokat a fájlhoz:
   
    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz, lecserélve a **{youriothubname}** helyőrzőt az IoT hub nevére, a **{yourdevicekey}** helyőrzőt pedig az *Eszközidentitás létrehozása* szakaszban létrehozott eszközkulcsértékre:
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Ez a mintaalkalmazás a **protocol** változót használja **DeviceClient** objektum példányának létrehozásakor. A HTTPS vagy az AMQPS protokollt használja az IoT Hubbal való kommunikációhoz.
8. Adja hozzá a következő beágyazott **TelemetryDataPoint** osztályt az **App** osztályon belül az eszköz által az IoT hubnak küldött telemetriai adatok meghatározásához:
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Adja hozzá a következő beágyazott **EventCallback** osztályt az **App** osztályon belül az IoT hub által visszaadott nyugtázási állapot megjelenítéséhez, amikor szimulált eszközről érkező üzenetet dolgoz fel. Ez a metódus az alkalmazás fő szálát is értesíti, amikor üzenetet dolgozott fel:
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. Adja hozzá a következő beágyazott **MessageSender** osztályt az **App** osztályon belül. Ezen osztály **run** metódusa minta telemetriai adatokat hoz létre, amelyeket elküld az IoT hubnak, és nyugtázásra vár a következő üzenet elküldése előtt:
    
    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```
    
    Ez a metódus elküld egy új, az eszközről a felhőbe irányuló üzenetet egy másodperccel azután, hogy az IoT hub nyugtázza az előző üzenetet. Az üzenet egy JSON-szerializált objektumot tartalmaz az eszköz azonosítójával és egy véletlenszerűen előállított számmal, amely egy szélsebesség-érzékelőt szimulál.
11. Cserélje le a **main** metódust a következő kódra, amely létrehoz egy szálat az eszközről a felhőbe irányuló üzenetek elküldéséhez az IoT Hubra:
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Mentse és zárja be az App.java fájlt.
13. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.
> 
> 

## Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A read-d2c mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti az IoT Hub első partíciójának megfigyelését:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![A Java IoT Hub szolgáltatásoldali ügyfélalkalmazás az eszközről a felhőbe irányuló üzenetek figyeléséhez][7]
2. A simulated-device mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a telemetriai adatok küldését az IoT Hubnak:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![A Java IoT Hub eszközoldali ügyfélalkalmazás az eszközről a felhőbe irányuló üzenetek küldéséhez][8]
3. Az [Azure Portal][lnk-portal] **Használat** csempéje a hubnak küldött üzenetek számát jeleníti meg:
   
    ![Az Azure Portalon az IoT Hubnak küldött üzenetek számát megjelenítő Használat csempe][43]

## Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált a portálon, majd létrehozott egy eszközidentitást a hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön a hubnak. Emellett létrehozott egy alkalmazást, amely megjeleníti a hub által fogadott üzeneteket. 

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszköz csatlakoztatása][lnk-connect-device]
* [Eszközfelügyelet – első lépések][lnk-device-management]
* [Átjáró SDK – első lépések][lnk-gateway-SDK]

Az IoT-megoldás kibővítésével és az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban tekintse meg [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Oct16_HO3-->


