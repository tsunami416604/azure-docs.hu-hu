---
title: Ismerkedés az Azure IoT Hub eszközfelügyelettel (Java) | Microsoft dokumentumok
description: Az Azure IoT Hub eszközfelügyeleti használata távoli eszköz újraindításának kezdeményezéséhez. Az Azure IoT-eszköz SDK Java-hoz egy szimulált eszközalkalmazást valósíthat meg, amely közvetlen metódust és az Azure IoT szolgáltatás SDK Java-hoz egy közvetlen metódust meghívó szolgáltatásalkalmazást valósít meg.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f68e25a618f5c6499ccc9d76c510eab8f1650330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110880"
---
# <a name="get-started-with-device-management-java"></a>Az eszközkezelés első lépései (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure Portalhasználatával hozzon létre egy IoT Hubot, és hozzon létre egy eszközidentitást az IoT-központban.

* Hozzon létre egy szimulált eszközalkalmazást, amely közvetlen módszert valósít meg az eszköz újraindításához. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy alkalmazást, amely meghívja az újraindítás közvetlen metódusa a szimulált eszköz alkalmazás az IoT hubon keresztül. Ez az alkalmazás ezután figyeli a jelentett tulajdonságokat az eszközről, hogy lássa, ha az újraindítási művelet befejeződött.

Az oktatóanyag végén két Java konzolalkalmazás található:

**szimulált eszköz.** Ez az alkalmazás:

* Az IoT hubhoz csatlakozik a korábban létrehozott eszközidentitással.

* Újraindítási közvetlen metódushívás fogadása.

* Fizikai újraindítást szimulál.

* A jelentett tulajdonságon keresztül imitolaz utolsó újraindítás időpontja.

**trigger-reboot**. Ez az alkalmazás:

* Közvetlen metódust hív meg a szimulált eszközalkalmazásban.

* A szimulált eszköz által küldött közvetlen metódushívásra adott választ jeleníti meg.

* Megjeleníti a frissített jelentett tulajdonságokat.

> [!NOTE]
> Az eszközökön és a megoldás háttér-tartalékrendszerein futtatható alkalmazások létrehozásához használható SDK-król az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* [Java SE Fejlesztői Készlet 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Győződjön meg róla, hogy a **Java** **8-at** a Hosszú távú támogatás alatt választja, hogy eljusson a JDK 8 letöltéséhez.

* [Maven 3](https://maven.apache.org/download.cgi)

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban olyan Java konzolalkalmazást hoz létre, amely:

1. Elindítja az újraindításközvetlen metódust a szimulált eszközalkalmazásban.

2. Megjeleníti a választ.

3. Lekérdezi az eszközről küldött jelentett tulajdonságokat, hogy megállapítsa, mikor fejeződik be az újraindítás.

Ez a konzolalkalmazás csatlakozik az IoT Hub a közvetlen metódus meghívásához, és olvassa el a jelentett tulajdonságokat.

1. Hozzon létre egy üres mappát, a **dm-get-started nevű mappát.**

2. A **dm-get-started** mappában hozzon létre egy **Trigger-reboot** nevű Maven projektet a következő parancs segítségével a parancssorból:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. A parancssorban keresse meg az **eseményindító-újraindítás** mappát.

4. Szövegszerkesztő használatával nyissa meg a **pom.xml** fájlt az **eseményindító-újraindítás** mappában, és adja hozzá a következő függőséget a **függőségi** csomóponthoz. Ez a függőség lehetővé teszi, hogy az iot-service-client csomagot használja az alkalmazásban az IoT-központtal való kommunikációhoz:

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

7. Szövegszerkesztő használatával nyissa meg az **eseményindító-reboot\src\main\java\com\mycompany\app\App.java** forrásfájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{youriothubconnectionstring}` le az IoT Hub kapcsolati karakterláncát, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Ha 10 másodpercenként szeretne olyan szálat megvalósítani, amely beolvassa az ikereszköz **App** jelentett tulajdonságait, adja hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Módosítsa a **fő** módszer aláírását a következő kivétel hez:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. A szimulált eszközön az újraindítási közvetlen metódus meghívásához cserélje le a **fő** metódusban lévő kódot a következő kódra:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. A rendszerlánc elindításához adja hozzá a következő kódot a **fő** metódushoz:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Az alkalmazás leállításának engedélyezéséhez adja hozzá a következő kódot a **fő** metódushoz:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Mentse és zárja be az **eseményindító-reboot\src\main\java\com\mycompany\app\App.java** fájlt.

16. Hozza létre az **eseményindító-újraindítás** háttéralkalmazást, és javítsa ki az esetleges hibákat. A parancssorban keresse meg az **eseményindító-újraindítás** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java konzolalkalmazást, amely szimulálja az eszközt. Az alkalmazás figyeli az újraindítás közvetlen metódus hívás az IoT hubról, és azonnal válaszol a hívásra. Az alkalmazás ezután egy ideig alvó állapotba kerül, hogy szimulálja az újraindítási folyamatot, mielőtt egy jelentett tulajdonsággal értesíti az **eseményindító-újraindítás** háttéralkalmazást, hogy az újraindítás befejeződött.

1. A **dm-get-started** mappában hozzon létre egy **szimulált eszköz** nevű Maven-projektet a következő parancs használatával a parancssorban:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A parancssorban keresse meg a **szimulált eszköz** mappát.

3. Szövegszerkesztő használatával nyissa meg a **pom.xml** fájlt a **szimulált eszközmappában,** és adja hozzá a következő függőséget a **függőségi** csomóponthoz. Ez a függőség lehetővé teszi, hogy az iot-service-client csomagot használja az alkalmazásban az IoT-központtal való kommunikációhoz:

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

7. Szövegszerkesztő használatával nyissa meg a **szimulált eszköz\src\main\java\com\mycompany\app\App.java** forrásfájlt.

8. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje `{yourdeviceconnectionstring}` le az [IoT hub szakaszúj eszköz regisztrálása](#register-a-new-device-in-the-iot-hub) című szakaszában megjegyzett eszközkapcsolati karakterláncra:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. A közvetlen metódusállapot-események visszahívási kezelőjének megvalósításához adja **App** hozzá a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Az ikereszközök állapoteseményeinek visszahíváskezelőjének megvalósításához adja hozzá **App** a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. A tulajdonságesemények visszahívási kezelőjének megvalósításához adja hozzá **App** a következő beágyazott osztályt az alkalmazásosztályhoz:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Az eszköz újraindítását szimuláló szál megvalósításához adja hozzá **App** a következő beágyazott osztályt az alkalmazásosztályhoz. A szál öt másodpercig alvó állapotban van, majd beállítja a **lastReboot** jelentett tulajdonságát:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. A közvetlen metódus eszközén való megvalósításhoz adja **App** hozzá a következő beágyazott osztályt az alkalmazásosztályhoz. Amikor a szimulált alkalmazás hívást kap az **újraindításközvetlen** metódushoz, nyugtát ad vissza a hívónak, majd elindítja a szálat az újraindítás feldolgozásához:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Módosítsa a **fő** módszer aláírását a következő kivételek esetén:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. **A DeviceClient**rendszerezéséhez cserélje le a **fő** metóduskódját a következő kódra:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. A közvetlen metódushívások figyeléséhez adja hozzá a következő kódot a **fő** metódushoz:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Az eszközszimulátor leállításához adja hozzá a következő kódot a **fő** metódushoz:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Mentse és zárja be a szimulált eszköz\src\main\java\com\mycompany\app\App.java fájlt.

20. Hozza létre a **szimulált eszközalkalmazást,** és javítsa ki az esetleges hibákat. A parancssorban keresse meg a **szimulált eszköz** mappát, és futtassa a következő parancsot:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **szimulált eszköz** mappájában lévő parancssorban futtassa a következő parancsot az IoT hubról érkező újraindítási metódushívások figyeléséhez:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szimulált eszközalkalmazás az újraindításközvetlen metódushívások figyelésére](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Az **eseményindító-újraindítás** mappában lévő parancssorból futtassa a következő parancsot az ioT hubról a szimulált eszközön lévő újraindítási módszer hívásához:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub szolgáltatás alkalmazás hívja meg az újraindítás közvetlen metódus](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. A szimulált eszköz válaszol az újraindítás közvetlen metódus hívás:

    ![A Java IoT Hub szimulált eszközalkalmazása válaszol a közvetlen metódushívásra](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
