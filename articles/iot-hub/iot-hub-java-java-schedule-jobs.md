---
title: Az Azure IoT Hub (Java) feladatok ütemezése |} Microsoft Docs
description: Hogyan kell egy Azure IoT Hub feladatot közvetlen metódus, és állítsa be a kívánt tulajdonságot több eszközön. Az Azure IoT-eszközök SDK Java segítségével valósítja meg a szimulált eszköz alkalmazások és az Azure IoT szolgáltatás Java SDK egy szolgáltatás alkalmazásnak, hogy a feladat végrehajtásához.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: b8b0742054b0348ded39b6357d00f6eac3449f99
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="schedule-and-broadcast-jobs-java"></a>Ütemezés és a feladatok (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub használatával ütemezése, illetve követheti nyomon, hogy az eszközök millióira frissítése feladatok. Feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok

Egy feladat becsomagolja az alábbi műveletek egyikét, és nyomon követi az eszközök végrehajtásának. Eszköz két lekérdezést meghatároz az eszközök a feldolgozás futtatása ellen. Például egy háttér-alkalmazás segítségével egy feladat meghívni egy 10 000 közvetlen metódust, amely az eszköz újraindul. Adjon meg az eszközök eszköz iker lekérdezéssel, és a feladat egy későbbi időpontban futtatásának ütemezését. A feladat nyomon követi előrehaladását, mint a eszközökhöz kapja meg, és a rendszer újraindítása közvetlen metódus végrehajtására.

Az egyes képességek kapcsolatos további információkért lásd:

* A két eszköz és a tulajdonságok: [eszköz twins az első lépései](iot-hub-java-java-twin-getstarted.md)
* A közvetlen módszer: [IoT Hub fejlesztői útmutató - közvetlen módszerek](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen módszerekkel](iot-hub-java-java-direct-methods.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy eszköz-alkalmazást, amely egy közvetlen a hívott metódus **lockDoor**. Az eszköz alkalmazás is fogad kívánt tulajdonságok módosítását a háttér-alkalmazást.
* Hozzon létre egy háttér-alkalmazást, amely létrehoz egy feladatot, amely hívja az **lockDoor** közvetlen módszer több eszközön. Egy másik feladat kívánt tulajdonság frissítései több eszközre elküldi.

Ez az oktatóanyag végén akkor egy java Konzolalkalmazás eszköz és a java háttér-Konzolalkalmazás:

**Szimulált eszköz** , amely csatlakozik az IoT hub valósít meg a **lockDoor** közvetlen metódust, és kezeli a szükséges tulajdonságok módosítását.

**feladatok ütemezése** feladatok segítségével, amely hívja az **lockDoor** közvetlen módszer, és frissítse a eszköztulajdonságok iker szükséges több eszközön.

> [!NOTE]
> A cikk [Azure IoT SDK-k](iot-hub-devguide-sdks.md) használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Használhatja a [IoT-bővítményt az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) hozzáad egy eszközt az IoT hub eszköz.

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása

Ebben a szakaszban egy feladatok használó Java-Konzolalkalmazás létrehozása:

* Hívja a **lockDoor** közvetlen módszer több eszközön.
* Kívánt tulajdonságokkal küldése több eszközre.

Az alkalmazás létrehozása:

1. A fejlesztői gépen, hozzon létre egy üres nevű `iot-java-schedule-jobs`.

1. Az a `iot-java-schedule-jobs` mappa, úgynevezett Maven-projekt létrehozása **feladatok ütemezése** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban navigáljon a `schedule-jobs` mappát.

1. Egy szövegszerkesztőben nyissa meg a `pom.xml` fájlt a `schedule-jobs` mappa, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy a **iot-szolgáltatásügyfél** csomag kommunikáljon az IoT hub az alkalmazásban:

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

1. Egy szövegszerkesztőben nyissa meg a `schedule-jobs\src\main\java\com\mycompany\app\App.java` fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{youriothubconnectionstring}` az IoT hub kapcsolati karakterlánccal feljegyzett a *létrehoz egy IoT-központot* szakasz:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Adja hozzá a következő metódust a **App** osztály frissítése feladat ütemezése a **épület** és **emelet** az eszköz a két tulajdonság szükséges:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

1. Hívása a feladat ütemezése a **lockDoor** módszer, adja hozzá a következő metódust a **App** osztály:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

1. Egy feladat figyeléséhez, adja hozzá a következő metódust a **App** osztály:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

1. A futtatott feladatok részletes adatainak lekérdezéséhez adja hozzá a következő metódust:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

1. Frissítés a **fő** tartalmazza a következő metódus-aláírás `throws` záradékban:

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Futtathatja, és figyelheti a két feladatok egymás után, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

1. Mentse és zárja be a `schedule-jobs\src\main\java\com\mycompany\app\App.java` fájl

1. Build a **feladatok ütemezése** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban navigáljon a `schedule-jobs` mappa, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amely a kívánt tulajdonságokkal, az IoT-központ által küldött kezeli, és megvalósítja az közvetlen metódus hívása.

1. Az a `iot-java-schedule-jobs` mappa, úgynevezett Maven-projekt létrehozása **szimulált eszköz** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor.

1. A konzol eszköz iker értesítések nyomtatni, adja hozzá a következő beágyazott osztály a **App** osztály:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. A konzol közvetlen módszer értesítések nyomtatni, adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Az IoT-központ közvetlen metódushívások kezeli, vegye fel a következő beágyazott osztály a **App** osztály:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

1. Frissítés a **fő** tartalmazza a következő metódus-aláírás `throws` záradékban:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Adja hozzá a következő kódot a **fő** módszert:
    * Hozzon létre egy ügyfél-kommunikáljon az IoT-központot.
    * Hozzon létre egy **eszköz** objektum iker eszköztulajdonságok tárolásához.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

1. Indítsa el az eszközt tartalmazó ügyfélszolgáltatások, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

1. Várja meg, hogy nyomja meg a felhasználó a **Enter** leállítása előtt kulcsban, adja hozzá a következő kódot végén a **fő** módszert:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Mentse és zárja be a `simulated-device\src\main\java\com\mycompany\app\App.java` fájlt.

1. Build a **szimulált eszköz** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban navigáljon a `simulated-device` mappa, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzol alkalmazások futtatásához.

1. A parancsot a parancssorba a `simulated-device` mappát, az eszköz alkalmazás figyel a kívánt tulajdonságok módosítását és a közvetlen metódushívások indításához a következő parancsot:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Az eszközügyfél kezdődik](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. A parancsot a parancssorba a `schedule-jobs` mappa futtatásához a következő parancsot a **feladatok ütemezése** service alkalmazás két feladatok futtatásához. Az első állítja be a kívánt tulajdonság értékével, a második meghívja a közvetlen módszer:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-központ szolgáltatás alkalmazás két feladatot hoz létre.](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. Az eszköz alkalmazás kezeli a kívánt tulajdonság módosításának és a közvetlen metódus hívása:

    ![Az eszközügyfél válaszol-e a módosításokat](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Létrehozott egy háttér-alkalmazás két feladatok futtatásához. Az első munkahelye kívánt tulajdonságértékei állíthatók, és a második feladat közvetlen metódus hívása.

A következő források segítségével megtudhatja, hogyan:

* Telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ](iot-hub-java-java-getstarted.md) oktatóanyag.
* Az interaktív (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) eszközeinek vezérléséhez a [közvetlen módszerekkel](iot-hub-java-java-direct-methods.md) oktatóanyag.
