---
title: Feladatok ütemezése az Azure IoT Hubbal (Java) | Microsoft dokumentumok
description: Egy Azure IoT Hub-feladat ütemezése egy közvetlen metódus meghívására, és állítsa be a kívánt tulajdonságot több eszközön. Az Azure IoT-eszköz SDK Java-hoz a szimulált eszközalkalmazások és az Azure IoT szolgáltatás SDK Java-hoz valósíthatja meg a szolgáltatás alkalmazást a feladat futtatásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: 9227192b2f7c554943fb3716ba1d1066f814c447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110320"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Feladatok ütemezése és közvetítése (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub használatával több millió eszközt frissítő feladatokat ütemezheti és követheti nyomon. A feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

A feladat becsomagolja az egyik ilyen műveletet, és nyomon követi a végrehajtást egy eszközkészleten. Az ikereszköz-lekérdezés határozza meg azokat az eszközöket, amelyeken a feladat végrehajtja. Például egy háttéralkalmazás egy feladat segítségével meghívhat egy közvetlen metódust 10 000 eszközön, amely újraindítja az eszközöket. Megadhatja az eszközök egy eszköz iker lekérdezést, és ütemezi a feladat futtatásához egy későbbi időpontban. A feladat nyomon követi a folyamatot, ahogy az egyes eszközök megkapják és végrehajtják az újraindításközvetlen metódust.

Az egyes képességekről az alábbi témakörben olvashat bővebben:

* Ikereszköz és tulajdonságok: [Első lépések az ikereszközökkel](iot-hub-java-java-twin-getstarted.md)

* Közvetlen módszerek: [IoT Hub fejlesztői útmutató - közvetlen módszerek](iot-hub-devguide-direct-methods.md) és [oktatóanyag: Közvetlen módszerek használata](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy eszközalkalmazást, amely megvalósítja a lockDoor nevű közvetlen **metódust.** Az eszközalkalmazás a háttéralkalmazástól is megkapja a kívánt tulajdonságmódosításokat.

* Hozzon létre egy háttéralkalmazás, amely létrehoz egy feladatot, hogy hívja meg a **lockDoor** közvetlen metódus több eszközön. Egy másik feladat több eszközre küldi a kívánt tulajdonságfrissítéseket.

Az oktatóanyag végén van egy Java konzoleszköz-alkalmazás és egy java konzol háttéralkalmazás:

**szimulált eszköz,** amely csatlakozik az IoT hubhoz, megvalósítja a **lockDoor** közvetlen metódust, és kezeli a kívánt tulajdonságváltozásokat.

**ütemezési feladatok,** amelyek feladatokat használnak a **lockDoor** közvetlen metódus hívására és az eszköz iker kívánt tulajdonságainak frissítésére több eszközön.

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

Az [Azure CLI-eszköz IoT-bővítményével](https://github.com/Azure/azure-iot-cli-extension) is hozzáadhat egy eszközt az IoT hubhoz.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban olyan Java konzolalkalmazást hoz létre, amely feladatokat használ a következőkre:

* Hívja meg a **lockDoor** közvetlen metódust több eszközön.

* A kívánt tulajdonságok elküldése több eszközre.

Az alkalmazás létrehozása:

1. A fejlesztői gépen hozzon létre egy **iot-java-schedule-jobs**nevű üres mappát.

2. Az **iot-java-schedule-jobs** mappában hozzon létre egy **Schedule-feladatok** nevű Maven projektet a következő parancs használatával a parancssorban. Látható, hogy ez egyetlen hosszú parancs:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. A parancssorban keresse meg az **ütemezési feladatok** mappát.

4. Szövegszerkesztő használatával nyissa meg a **pom.xml** fájlt az **ütemezési feladatok** mappában, és adja hozzá a következő függőséget a **függőségi** csomóponthoz. Ez a függőség lehetővé teszi, hogy az **iot-service-client** csomagot használja az alkalmazásban az IoT-központtal való kommunikációhoz:

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

7. Szövegszerkesztő használatával nyissa meg az **ütemezési feladatok\src\main\java\com\mycompany\app\App.java** fájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

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

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{youriothubconnectionstring}` le az IoT hub kapcsolati karakterláncát, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Adja hozzá a következő módszert az **alkalmazásosztályhoz,** **Floor** hogy ütemezzen egy feladatot az **ikereszköz kívánt** tulajdonságainak frissítéséhez:

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

11. Ha a **lockDoor** metódus hívására irányuló feladatot szeretne ütemezni, adja hozzá a következő módszert az **alkalmazásosztályhoz:**

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

12. Feladat figyeléséhez adja hozzá a **App** következő módszert az alkalmazásosztályhoz:

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

13. A futtatott feladatok részleteinek lekérdezéséhez adja hozzá a következő módszert:

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

14. Frissítse a **fő** metódus aláírását, hogy tartalmazza a következő `throws` záradékot:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Két feladat egymás utáni futtatásához és figyeléséhez cserélje le a **fő** metódus kódját a következő kódra:

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

16. Az **ütemezési feladatok mentése\src\main\java\com\mycompany\app\App.java** fájl

17. Hozza létre az **ütemezési feladatok alkalmazást,** és javítsa ki az esetleges hibákat. A parancssorban keresse meg az **ütemezési feladatok** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java konzolalkalmazást, amely kezeli az IoT Hubról küldött kívánt tulajdonságokat, és megvalósítja a közvetlen metódushívás.

1. Az **iot-java-schedule-jobs** mappában hozzon létre egy **szimulált eszköz** nevű Maven-projektet a következő parancs használatával a parancssorban. Látható, hogy ez egyetlen hosszú parancs:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. A parancssorban keresse meg a **szimulált eszköz** mappát.

3. Szövegszerkesztő használatával nyissa meg a **pom.xml** fájlt a **szimulált eszközmappában,** és adja hozzá a következő függőségeket a **függőségi** csomóponthoz. Ez a függőség lehetővé teszi, hogy az **iot-device-client** csomagot használja az alkalmazásban az IoT hubbal való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22) tekintheti meg.

4. Adja hozzá a következő függőséget a **függőségi** csomóponthoz. Ez a függőség nop-t konfigurál az Apache [SLF4J](https://www.slf4j.org/) naplózási homlokzatához, amelyet az eszközügyfél SDK a naplózás megvalósításához használ. Ez a konfiguráció nem kötelező, de ha kihagyja, az alkalmazás futtatásakor figyelmeztetés jelenhet meg a konzolon. Az eszközügyfél SDK-ba való naplózásról további információt az *Azure IoT-eszköz Java-alapú SDK-fájlmintáinak* [naplózása](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)című témakörben talál.

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

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{yourdeviceconnectionstring}` le a korábban másolt eszközkapcsolati karakterláncot az [IoT hub szakaszúj eszköz regisztrálása szakaszban:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor.

10. Az ikereszköz-értesítések konzolra történő nyomtatásához adja **App** hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Ha közvetlen metódusértesítéseket szeretne nyomtatni a konzolra, **App** adja hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Az IoT Hubról érkező közvetlen metódushívások kezeléséhez adja **App** hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

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

13. Frissítse a **fő** metódus aláírását, hogy tartalmazza a következő `throws` záradékot:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Cserélje le a **fő** metódusban lévő kódot a következő kódra:
    * Hozzon létre egy eszköz-ügyfél kommunikálni Az IoT Hub.
    * **Hozzon** létre egy eszközobjektumot az ikereszköz tulajdonságainak tárolásához.

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

15. Az eszközügyfél-szolgáltatások elindításához adja hozzá a következő kódot a **fő** metódushoz:

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

16. Ha meg szeretné várni, hogy a felhasználó leállítás előtt megnyomja az **Enter** billentyűt, adja hozzá a következő kódot a **fő** metódus végéhez:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Mentse és zárja be a **szimulált eszköz\src\main\java\com\mycompany\app\App.java** fájlt.

18. Hozza létre a **szimulált eszközalkalmazást,** és javítsa ki az esetleges hibákat. A parancssorban keresse meg a **szimulált eszköz** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzolos alkalmazások futtatására.

1. A **szimulált eszköz** mappájában lévő parancssorban futtassa a következő parancsot a kívánt tulajdonságmódosítások és közvetlen metódushívások figyeléséhez szükséges eszközalkalmazás elindításához:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Az eszközügyfél elindul](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. A `schedule-jobs` mappa parancssorában futtassa a következő parancsot az **ütemezési feladatok** szolgáltatásalkalmazás futtatásához két feladat futtatásához. Az első beállítja a kívánt tulajdonságértékeket, a második meghívja a közvetlen metódust:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![A Java IoT Hub szolgáltatásalkalmazás két feladatot hoz létre](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Az eszközalkalmazás kezeli a kívánt tulajdonságváltozást és a közvetlen metódushívást:

   ![Az eszközügyfél válaszol a változásokra](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat segítségével ütemezte a közvetlen metódust egy eszközre, és az ikereszköz tulajdonságainak frissítését.

Az alábbi források segítségével megtudhatja, hogyan:

* Telemetriai adatok küldése az eszközökről az [IoT Hub első lépései](quickstart-send-telemetry-java.md) oktatóanyaggal.

* Az eszközök interaktív vezérlése (például egy ventilátor bekapcsolása egy felhasználó által vezérelt alkalmazásból) a [Közvetlen módszerek használata](quickstart-control-device-java.md) oktatóanyaggal.s
