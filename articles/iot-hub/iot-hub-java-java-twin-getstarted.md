---
title: Ismerkedés az Azure IoT Hub Device Twins (Java) szolgáltatással | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A Javához készült Azure IoT eszközoldali SDK segítségével megvalósíthatja az eszköz alkalmazást és a Javához készült Azure IoT Service SDK-t egy olyan szolgáltatásalkalmazás megvalósításához, amely hozzáadja a címkéket és futtatja a IoT Hub lekérdezést.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.openlocfilehash: 6d2d0540786f1aa4bec35cf4bec26212cb7df7ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404208"
---
# <a name="get-started-with-device-twins-java"></a>Ismerkedés az eszközök ikrekkel (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ebben az oktatóanyagban két Java-konzolos alkalmazást hoz létre:

* **Add-Tags-Query**, egy Java háttérbeli alkalmazás, amely hozzáadja a címkéket és a lekérdezési eszközökhöz tartozó ikreket.
* **szimulált-eszköz**, egy Java-eszköz alkalmazás, amely csatlakozik az IoT hubhoz, és jelentést készít a kapcsolati feltételéről egy jelentett tulajdonság használatával.

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k cikke olyan Azure IoT SDK-kat tartalmaz, amelyek segítségével mind az eszközök, mind a háttérbeli alkalmazások készíthetők.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy Java-alkalmazást hoz létre, amely a hely metaadatainak hozzáadását adja hozzá a **myDeviceId**-hez társított IoT hubhoz. Az alkalmazás először a IoT hub-t kérdezi le az USA-ban található eszközökhöz, majd a mobil hálózati kapcsolatot bejelentő eszközökhöz.

1. A fejlesztői gépen hozzon létre egy nevű `iot-java-twin-getstarted`üres mappát.

2. A `iot-java-twin-getstarted` mappában hozzon létre egy **Add-Tags-Query** nevű Maven-projektet a következő parancs használatával a parancssorba. Látható, hogy ez egyetlen hosszú parancs:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. A parancssorban navigáljon a `add-tags-query` mappához.

4. Egy szövegszerkesztővel nyissa meg a `pom.xml` fájlt a `add-tags-query` mappában, és adja hozzá a következő függőséget a **függőségek** csomóponthoz. Ezzel a függőséggel használhatja az **IOT-Service-Client** csomagot az alkalmazásban az IOT hub használatával folytatott kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > A **IOT-Service-Client** legújabb verzióját a [Maven Search](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)használatával tekintheti meg.

5. Adja hozzá a következő **Build** csomópontot a **függőségek** csomópont után. Ez a konfiguráció arra utasítja a Mavent, hogy a Java 1,8-et használja az alkalmazás létrehozásához:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Mentse és zárjuk be `pom.xml` a fájlt.

7. Egy szövegszerkesztővel nyissa meg a `add-tags-query\src\main\java\com\mycompany\app\App.java` fájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{youriothubconnectionstring}` le az IoT hub-t, amelyet korábban másolt a [IoT hub-kapcsolatok karakterláncának](#get-the-iot-hub-connection-string)lekérése:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Frissítse a **fő** metódus aláírását, hogy tartalmazza `throws` a következő záradékot:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Adja hozzá a következő kódot a **Main** metódushoz a **DeviceTwin** és a **DeviceTwinDevice** objektumok létrehozásához. A **DeviceTwin** objektum kezeli az IoT hub kommunikációját. A **DeviceTwinDevice** objektum az eszközt a Twin (Tulajdonságok) és a címkével együtt ábrázolja:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Adja hozzá a `try/catch` következő blokkot a **Main** metódushoz:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Ha frissíteni szeretné  a régiót **és a** berendezési eszköz Twin címkéit az eszköz Twin- `try` kódjában, adja hozzá a következő kódot a blokkhoz:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Az eszközök az IoT hub-ban való lekérdezéséhez adja hozzá a következő kódot `try` a blokkhoz az előző lépésben hozzáadott kód után. A kód két lekérdezést futtat. Minden lekérdezés legfeljebb 100 eszközt ad vissza:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. A `add-tags-query\src\main\java\com\mycompany\app\App.java` fájl mentése és lezárása

16. Hozza létre a **Add-Tags-Query** alkalmazást, és javítsa ki az esetleges hibákat. A parancssorban navigáljon a `add-tags-query` mappához, és futtassa a következő parancsot:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban egy Java-konzol alkalmazást hoz létre, amely a IoT Hub számára elküldött jelentett tulajdonság értékét állítja be.

1. A mappában hozzon létre egy szimulált eszköz nevű Maven-projektet a következő parancs futtatásával a parancssorban.  `iot-java-twin-getstarted` Látható, hogy ez egyetlen hosszú parancs:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban navigáljon a `simulated-device` mappához.

3. Egy szövegszerkesztővel nyissa meg a `pom.xml` fájlt a `simulated-device` mappában, és adja hozzá a következő függőségeket  a függőségek csomóponthoz. Ez a függőség lehetővé teszi, hogy a **IOT-Device-Client** csomagot használja az alkalmazásban az IOT hub-vel való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.14.2</version>
    </dependency>
    ```

    > [!NOTE]
    > A **IOT-Device-Client** legújabb verzióját a [Maven Search](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)használatával tekintheti meg.

4. Adja hozzá a következő **Build** csomópontot a **függőségek** csomópont után. Ez a konfiguráció arra utasítja a Mavent, hogy a Java 1,8-et használja az alkalmazás létrehozásához:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Mentse és zárjuk be `pom.xml` a fájlt.

6. Egy szövegszerkesztővel nyissa meg a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

7. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{youriothubname}` le a kifejezést az IoT hub nevére `{yourdevicekey}` , és az eszköz *identitásának létrehozása* szakaszban létrehozott eszköz kulcs értékével:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor. 

1. Adja hozzá a következő metódust az **app** osztályhoz a kettős frissítésekkel kapcsolatos információk kinyomtatásához:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

9. Adja hozzá a következő kódot a **Main** metódushoz:
    * Hozzon létre egy eszköz-ügyfelet a IoT Hubsal való kommunikációhoz.
    * Hozzon létre egy **eszköz** -objektumot az eszköz Twin tulajdonságainak tárolásához.

      ```java
      DeviceClient client = new DeviceClient(connString, protocol);

      // Create a Device object to store the device twin properties
      Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
      };
      ```

10. Adja hozzá a következő kódot a **Main** metódushoz egy **connectivityType** jelentett tulajdonság létrehozásához, és küldje el IoT hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

11. Adja hozzá a következő kódot a **Main** metódus végéhez. Ha az **ENTER** billentyűre vár, a IoT hub az eszköz kettős műveleteinek állapotát jelenti:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Módosítsa úgy a **main** metódus aláírását, hogy tartalmazza az alábbi kivételeket:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

1. Mentse és zárjuk be `simulated-device\src\main\java\com\mycompany\app\App.java` a fájlt.

13. Hozza létre a **szimulált eszköz** alkalmazást, és javítsa ki az esetleges hibákat. A parancssorban navigáljon a `simulated-device` mappához, és futtassa a következő parancsot:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzolon futó alkalmazások futtatására.

1. A `add-tags-query` mappában a parancssorban futtassa a következő parancsot az **Add-Tags-Query** szolgáltatás alkalmazás futtatásához:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatásalkalmazás a címkézési értékek frissítéséhez és az eszközök lekérdezésének futtatásához](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Megtekintheti az eszközhöz tartozó Twin-eszközökhöz hozzáadott **növény** és **régió** címkéket. Az első lekérdezés visszaadja az eszközt, de a második nem.

2. A `simulated-device` mappában a parancssorban futtassa a következő parancsot, hogy hozzáadja a **connectivityType** jelentett tulajdonságot az eszköz Twin:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Az eszköz ügyfele hozzáadja a * * connectivityType * * jelentett tulajdonságot](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. A `add-tags-query` mappában a parancssorban futtassa a következő parancsot az **Add-Tags-Query** szolgáltatás alkalmazás második alkalommal való futtatásához:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatásalkalmazás a címkézési értékek frissítéséhez és az eszközök lekérdezésének futtatásához](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Most, hogy az eszköz elküldte a **connectivityType** tulajdonságot a IoT hubnak, a második lekérdezés visszaadja az eszközt.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy eszközt az eszköz csatlakozási adatainak jelentéséhez az eszközök Twin-ben. Azt is megtanulta, hogyan kérdezheti le az eszköz Twin-információit az SQL-Like IoT Hub lekérdezési nyelv használatával.

Az alábbi források segítségével megismerheti a következőket:

* Telemetria küldése az eszközökről az első [lépések IoT hub](quickstart-send-telemetry-java.md) oktatóanyaggal.

* Vezérelheti az eszközöket interaktív módon (például egy felhasználó által vezérelt alkalmazásból származó ventilátor bekapcsolásával) a [Direct Methods oktatóanyag használatával](quickstart-control-device-java.md) .