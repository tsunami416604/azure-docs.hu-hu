---
title: Ismerkedés az Azure IoT Hub-eszközök ikreivel (Java) | Microsoft dokumentumok
description: Az Azure IoT Hub-eszközök twins használata címkék hozzáadásához, majd egy IoT Hub-lekérdezés használatához. Az Azure IoT-eszköz SDK Java-hoz az eszközalkalmazás és az Azure IoT szolgáltatás SDK Java-hoz egy szolgáltatás alkalmazás, amely hozzáadja a címkéket, és futtatja az IoT Hub-lekérdezést.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e0114c37b2204a7ad1d7b0cf9c7f336dcd85883a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110490"
---
# <a name="get-started-with-device-twins-java"></a>Az ikereszközök első lépései (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ebben az oktatóanyagban két Java konzolalkalmazást hoz létre:

* **add-tags-query**, a Java háttéralkalmazás, amely címkéket és lekérdezéseket ad az eszközök twins.add-tags-query , a Java back-end app that adds tags and queries device twins.
* **szimulált eszköz, egy Java-eszköz**alkalmazás, amely csatlakozik az IoT hubhoz, és jelenti a kapcsolódási állapotát egy jelentett tulajdonság használatával.

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) az Azure IoT SDK-k, amelyek segítségével eszköz- és háttéralkalmazásokat hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Fejlesztői Készlet 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Győződjön meg róla, hogy a **Java** **8-at** a Hosszú távú támogatás alatt választja, hogy eljusson a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban létrehoz egy Java-alkalmazást, amely címkeként helymetaadatokat ad hozzá az ikereszközhöz a **myDeviceId-hoz**társított IoT Hubban. Az alkalmazás először lekérdezi az IoT hubot az Egyesült Államokban található eszközökre, majd azolyan eszközökre, amelyek mobilhálózati kapcsolatot jelentenek.

1. A fejlesztői gépen hozzon létre egy **iot-java-twin-getstarted**nevű üres mappát.

2. Az **iot-java-twin-getstarted** mappában hozzon létre egy **Add-tags nevű** Maven projektet a következő paranccsal a parancssorban:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. A parancssorban keresse meg a **hozzáadáscímkék-lekérdezés** mappát.

4. Szövegszerkesztő használatával nyissa meg a **pom.xml** fájlt az **add-tags-query** mappában, és adja hozzá a következő függőséget a **függőségi** csomóponthoz. Ez a függőség lehetővé teszi, hogy az **iot-service-client** csomagot használja az alkalmazásban az IoT-központtal való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

5. Adja hozzá a következő **buildcsomópontot** a **függőségi** csomópont után. Ez a konfiguráció arra utasítja a Mavent, hogy java 1.8-as használatával készítse el az alkalmazást.

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

6. Mentse és zárja be a **pom.xml** fájlt.

7. Szövegszerkesztő használatával nyissa meg az **add-tags-query\src\main\java\com\mycompany\app\App.java** fájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{youriothubconnectionstring}` le az IoT hub kapcsolati karakterlánc másolt [az IoT hub kapcsolati karakterlánc bekapása.](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Frissítse a **fő** metódus aláírását, hogy tartalmazza a következő `throws` záradékot:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Cserélje le a kódot a **fő** módszer a következő kódot, hogy megteremtse a **DeviceTwin** és **DeviceTwinDevice** objektumokat. A **DeviceTwin** objektum kezeli az IoT hubbal való kommunikációt. A **DeviceTwinDevice** objektum az ikereszközt jelöli a tulajdonságaival és címkéivel:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Adja hozzá `try/catch` a következő blokkot a **fő** módszerhez:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. A **régió** és a **plant** device twin címkék frissítéséhez az `try` ikereszközben adja hozzá a következő kódot a blokkban:

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

14. Az imperátusok az IoT hubban `try` az eszköz twins lekérdezéséhez adja hozzá a következő kódot a blokkhoz az előző lépésben hozzáadott kód után. A kód két lekérdezést futtat. Minden lekérdezés legfeljebb 100 eszközt ad vissza.

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

15. Az **add-tags-query\src\main\java\com\mycompany\app\App.java** fájl mentése és bezárása

16. Hozza létre a **bővítménycímkék-lekérdezésalkalmazást,** és javítsa ki az esetleges hibákat. A parancssorban keresse meg a **hozzáadáscímkék-lekérdezés** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java konzolalkalmazást, amely beállítja az IoT Hubnak küldött jelentett tulajdonságértéket.

1. Az **iot-java-twin-getstarted** mappában hozzon létre egy **szimulált eszköz** nevű Maven-projektet a következő paranccsal a parancssorban:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg a **szimulált eszköz** mappát.

3. Szövegszerkesztő használatával nyissa meg a **pom.xml** fájlt a **szimulált eszközmappában,** és adja hozzá a következő függőségeket a **függőségi** csomóponthoz. Ez a függőség lehetővé teszi, hogy az **iot-device-client** csomag használatával kommunikáljon az IoT hub.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Adja hozzá a következő függőséget a **függőségi** csomóponthoz. Ez a függőség nop-t konfigurál az Apache [SLF4J](https://www.slf4j.org/) naplózási homlokzatához, amelyet az eszközügyfél SDK a naplózás megvalósításához használ. Ez a konfiguráció nem kötelező, de ha kihagyja, az alkalmazás futtatásakor figyelmeztetés jelenhet meg a konzolon. Az eszközügyfél SDK-ba való naplózásról további információt az *Azure IoT-eszköz Java-alapú SDK-fájlmintáinak* [naplózása](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) című témakörben talál.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Adja hozzá a következő **buildcsomópontot** a **függőségi** csomópont után. Ez a konfiguráció arra utasítja a Mavent, hogy java 1.8-as verziót használjon az alkalmazás létrehozásához:

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

6. Mentse és zárja be a **pom.xml** fájlt.

7. Szövegszerkesztő használatával nyissa meg a **szimulált eszköz\src\main\java\com\mycompany\app\App.java** fájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{yourdeviceconnectionstring}` le az [IoT hubban új eszköz regisztrálása](#register-a-new-device-in-the-iot-hub)című részben másolt eszközkapcsolati karakterláncot.

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor.

10. Adja hozzá a következő módszert az **alkalmazásosztályhoz** az ikerfrissítésekre vonatkozó információk nyomtatásához:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Cserélje le a **fő** metódusban lévő kódot a következő kódra:

    * Hozzon létre egy eszköz-ügyfél kommunikálni Az IoT Hub.

    * **Hozzon** létre egy eszközobjektumot az ikereszköz tulajdonságainak tárolásához.

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

12. Adja hozzá a következő kódot a **fő** metódushoz egy **connectivityType** reported tulajdonság létrehozásához, és küldje el az IoT Hubnak:

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

13. Adja hozzá a következő kódot a **fő** módszer végéhez. Az **Enter** kulcsra való várakozás lehetővé teszi, hogy az IoT Hub jelentse az ikereszköz-műveletek állapotát.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Módosítsa úgy a **main** metódus aláírását, hogy tartalmazza az alábbi kivételeket:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Mentse és zárja be a **szimulált eszköz\src\main\java\com\mycompany\app\App.java** fájlt.

16. Hozza létre a **szimulált eszközalkalmazást,** és javítsa ki az esetleges hibákat. A parancssorban keresse meg a **szimulált eszköz** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzolos alkalmazások futtatására.

1. Az **add-tags-query** mappa parancssorában futtassa a következő parancsot a **bővítménycímkék-lekérdezési** szolgáltatás alkalmazás futtatásához:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatásalkalmazás a címkeértékek frissítéséhez és az eszközlekérdezések futtatásához](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Láthatja az ikereszközhöz hozzáadott **növény-** és **régiócímkéket.** Az első lekérdezés visszaadja az eszközt, de a második nem.

2. A **szimulált eszköz** mappájában lévő parancssorban futtassa a következő parancsot a **connectivityType** reported tulajdonság nak az ikereszközhöz való hozzáadásához:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Az eszközügyfél hozzáadja a **connectivityType** jelentett tulajdonságot](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. A **bővítménycímkék-lekérdezés** mappában lévő parancssorban futtassa a következő parancsot a **bővítménycímkék-lekérdezési** szolgáltatás alkalmazás második futtatásához:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatásalkalmazás a címkeértékek frissítéséhez és az eszközlekérdezések futtatásához](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Most, hogy az eszköz elküldte a **connectivityType** tulajdonságot az IoT Hubnak, a második lekérdezés visszaadja az eszközt.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttéralkalmazásból, és írt egy eszközalkalmazást az eszközkapcsolati adatok jelentéséhez az ikereszközben. Azt is megtanulta, hogyan lehet lekérdezni az eszköz ikeradatait az SQL-szerű IoT Hub lekérdezési nyelv használatával.

Az alábbi források segítségével megtudhatja, hogyan:

* Telemetriai adatok küldése az eszközökről az [IoT Hub első lépései](quickstart-send-telemetry-java.md) oktatóanyaggal.

* Az eszközök interaktív vezérlése (például egy ventilátor bekapcsolása egy felhasználó által vezérelt alkalmazásból) a [Közvetlen módszerek használata](quickstart-control-device-java.md) oktatóanyaggal.
