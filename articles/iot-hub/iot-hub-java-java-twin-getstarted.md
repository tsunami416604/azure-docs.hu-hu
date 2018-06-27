---
title: Ismerkedés az Azure IoT Hub eszköz twins (Java) |} Microsoft Docs
description: Hogyan használható az Azure IoT Hub eszköz twins címkéket, majd az IoT Hub-lekérdezést. Az Azure IoT-eszközök SDK Java segítségével valósítja meg az eszköz alkalmazás és az Azure IoT szolgáltatás Java SDK egy szolgáltatás-alkalmazást, amely hozzáadja a címkéket és az IoT Hub-lekérdezés futtatása végrehajtásához.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: dobett
ms.openlocfilehash: 96cad0fc7f387c5f0cb14996ae6ac015c104b81d
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016699"
---
# <a name="get-started-with-device-twins-java"></a>Ismerkedés az eszköz twins (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ebben az oktatóanyagban létrehoz két Java-konzol alkalmazásokhoz:

* **Adja hozzá címkék-lekérdezés**, a Java háttér-alkalmazást, amely címkét ad hozzá, és lekérdezi az eszköz twins.
* **Szimulált eszköz**, a Java eszköz alkalmazást, amely csatlakozik az IoT hub és jelentések jelentett tulajdonsággal kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k](iot-hub-devguide-sdks.md) használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása

Ebben a szakaszban hoz létre egy Java-alkalmazást, amely hozzáadja a hely metaadatokat, az IoT-központ az eszköz a két címkét társított **myDeviceId**. Az alkalmazás első lekérdezi az IoT-központ az Egyesült Államokban lévő eszközök, majd az eszközök mobilhálózati kapcsolat hoznak.

1. A fejlesztői gépen, hozzon létre egy üres nevű `iot-java-twin-getstarted`.

1. A a `iot-java-twin-getstarted` mappa, úgynevezett Maven-projekt létrehozása **hozzáadása-címkék-lekérdezés** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban navigáljon a `add-tags-query` mappát.

1. Egy szövegszerkesztőben nyissa meg a `pom.xml` fájlt a `add-tags-query` mappa, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy a **iot-szolgáltatásügyfél** csomag kommunikáljon az IoT hub az alkalmazásban:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-szolgáltatásügyfél** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Adja hozzá a következő **build** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a Java 1.8 az lehetővé teszi az alkalmazás Maven:

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

1. Mentse és zárja be a `pom.xml` fájlt.

1. Egy szövegszerkesztőben nyissa meg a `add-tags-query\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{youriothubconnectionstring}` az IoT hub kapcsolati karakterlánccal feljegyzett a *létrehoz egy IoT-központot* szakasz:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

1. Frissítés a **fő** tartalmazza a következő metódus-aláírás `throws` záradékban:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Adja hozzá a következő kódot a **fő** metódus létrehozása a **DeviceTwin** és **DeviceTwinDevice** objektumok. A **DeviceTwin** objektum az IoT hub kommunikációt kezeli. A **DeviceTwinDevice** objektum által jelképezett tulajdonságok és címkék az eszköz iker:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

1. Adja hozzá a következő `try/catch` megakadályozza a **fő** módszert:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

1. Frissítése a **régió** és **gépek** eszköz iker címkék az eszköz iker adja hozzá az alábbi kódot a a `try` letiltása:

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

1. Az eszköz twins az IoT hubon lekérdezéséhez adja hozzá az alábbi kódot a `try` blokk az előző lépésben felvett kód után. A kód lefut két lekérdezést. Minden egyes lekérdezés visszaadja a legfeljebb 100 eszközöket:

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

1. Mentse és zárja be a `add-tags-query\src\main\java\com\mycompany\app\App.java` fájl

1. Build a **hozzáadása-címkék-lekérdezés** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban navigáljon a `add-tags-query` mappa, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amely beállítja a küldi el az IoT-központ jelentett tulajdonság értéke.

1. Az a `iot-java-twin-getstarted` mappa, úgynevezett Maven-projekt létrehozása **szimulált eszköz** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban navigáljon a `simulated-device` mappát.

1. Egy szövegszerkesztőben nyissa meg a `pom.xml` fájlt a `simulated-device` mappa, és adja hozzá az alábbi függőségeket a **függőségek** csomópont. A függőség lehetővé teszi, hogy a **iot-eszközügyfél** csomag kommunikáljon az IoT hub az alkalmazásban:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-eszközügyfél** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Adja hozzá a következő **build** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a Java 1.8 az lehetővé teszi az alkalmazás Maven:

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

1. Mentse és zárja be a `pom.xml` fájlt.

1. Egy szövegszerkesztőben nyissa meg a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. A mag cseréje `{youriothubname}` az IoT hub nevű és `{yourdevicekey}` a eszközkulcs értékre hozott létre a *hozzon létre egy eszközidentitás* szakasz:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor. 

1. Adja hozzá a következő kódot a **fő** módszert:
    * Hozzon létre egy ügyfél-kommunikáljon az IoT-központot.
    * Hozzon létre egy **eszköz** objektum iker eszköztulajdonságok tárolásához.

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

1. Adja hozzá a következő kódot a **fő** metódussal hozzon létre egy **connectivityType** tulajdonság jelentette, és elküldi a IoT-központ:

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
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Adja hozzá a következő kódot végén a **fő** metódust. Várakozás a **Enter** kulcs lehetővé teszi, hogy az eszköz iker műveletek állapotának jelentésének IoT-központ számára:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Mentse és zárja be a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

1. Build a **szimulált eszköz** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban navigáljon a `simulated-device` mappa, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzol alkalmazások futtatásához.

1. A parancsot a parancssorba a `add-tags-query` mappa futtatásához a következő parancsot a **hozzáadása-címkék-lekérdezés** service-alkalmazást:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-központ szolgáltatás alkalmazás címke értékeinek frissítéséhez és eszköz-lekérdezések futtatása](media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Megtekintheti a **gépek** és **régió** címkéket, az eszköz iker hozzáadni. Az első lekérdezés visszaadja az eszközt, de a második viszont nem.

1. A parancsot a parancssorba a `simulated-device` mappa, a következő parancs futtatásával adja hozzá a **connectivityType** jelentett tulajdonság az eszköz iker:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Az eszközügyfél hozzáadja a ** connectivityType ** jelentett tulajdonság](media/iot-hub-java-java-twin-getstarted/device-app-1.png)

1. A parancsot a parancssorba a `add-tags-query` mappa futtatásához a következő parancsot a **hozzáadása-címkék-lekérdezés** service-alkalmazást még egyszer:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-központ szolgáltatás alkalmazás címke értékeinek frissítéséhez és eszköz-lekérdezések futtatása](media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Miután elküldte az eszköz a **connectivityType** IoT-központ tulajdonságot, a második lekérdezés visszaadja az eszközt.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Fel van véve eszköz metaadatait címkék egy háttér-alkalmazásból, és egy eszköz alkalmazásának megírt az eszköz a két jelentés eszköz kapcsolódási adatok. Is megismerte az SQL-szerű IoT Hub lekérdezési nyelv használatával kettős eszközadatokat lekérdezése.

A következő források segítségével megtudhatja, hogyan:

* Telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ](iot-hub-java-java-getstarted.md) oktatóanyag.
* Az interaktív (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) eszközeinek vezérléséhez a [közvetlen módszerekkel](iot-hub-java-java-direct-methods.md) oktatóanyag.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-twin-getstarted/invoke-method.png
[8]: ./media/iot-hub-java-java-twin-getstarted/device-listen.png
[9]: ./media/iot-hub-java-java-twin-getstarted/device-respond.png

<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
