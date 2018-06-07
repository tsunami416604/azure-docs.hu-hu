---
title: Ismerkedés az Azure IoT Hub kezelés (Java) |} Microsoft Docs
description: Hogyan használható az Azure IoT Hub kezelés távoli eszköz újraindítás kezdeményezése. Az Azure IoT-eszközök SDK Java segítségével valósítja meg a szimulált eszköz alkalmazást, amely közvetlen módszer és az Azure IoT szolgáltatást megvalósítása, amely a közvetlen módszer hívja service-alkalmazást Java SDK tartalmazza.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636188"
---
# <a name="get-started-with-device-management-java"></a>Eszközkezelés (Java) az első lépései

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure-portál használatával létrehoz egy IoT-központot, és az IoT hub hozzon létre egy eszközidentitás.
* Létrehoz egy szimulált eszköz alkalmazást, amely az eszköz újraindítását közvetlen módszer. Közvetlen módszerek a felhőből hívják.
* Hozzon létre egy alkalmazást, amely a szimulált eszköz alkalmazásának keresztül az IoT hub újraindítás közvetlen metódust hívja. Ezt az alkalmazást az eszközről, tekintse meg az újraindítás művelet befejezésekor jelentett tulajdonságok figyeli.

Ez az oktatóanyag végén két Java konzol alkalmazások közül választhat:

**Szimulált eszköz**. Ezt az alkalmazást:

* Az IoT hub csatlakozik a korábban létrehozott eszközidentitás.
* Újraindítás közvetlen módszer hívást kap.
* A fizikai számítógép újraindítása szimulálja.
* A jelentés az utolsó újraindítás jelentett tulajdonságon keresztül idején.

**eseményindító-újraindítás**. Ezt az alkalmazást:

* A közvetlen módszer meghívja a szimulált eszköz alkalmazásban.
* Megjeleníti a közvetlen módszer hívásához a szimulált eszköz által küldött válasz
* Megjeleníti a módosított tulajdonságok jelentett.

> [!NOTE]
> Az SDK-k használó alkalmazások futtatásához eszközökön és a megoldás háttérrendszeréhez kapcsolatos információkért lásd: [Azure IoT SDK-k][lnk-hub-sdks].

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Java SE 8. <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a Javát ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* Maven 3  <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a [Mavent][lnk-maven] ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* [NODE.js-verzió 0.10.0-s vagy újabb](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Az eszközön, a közvetlen módszer használatával távoli újraindítás eseményindító

Ebben a szakaszban hoz létre egy Java-Konzolalkalmazás, amely:

1. Meghívja a szimulált eszköz alkalmazásának újraindítás közvetlen metódust.
1. A válasz megjeleníti.
1. Szavazások a jelentésben szereplő tulajdonságok az eszköz számára küldött határozza meg, ha az újraindítás befejeződött.

A Konzolalkalmazás csatlakozik az IoT Hub meghívni a közvetlen metódust, és a jelentett tulajdonságainak olvasása.

1. Hozzon létre egy üres nevű dm-get-started.

1. A dm-get-started mappában hozzon létre egy Maven project nevű **eseményindító-újraindítás** parancsot a parancssorba az alábbi parancs segítségével. Az alábbiakban látható egy egyetlen, hosszú parancsot:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban keresse meg az eseményindító-újraindítás mappát.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt az eseményindító-újraindítás mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával][lnk-maven-service-search] tekintheti meg.

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

1. Mentse és zárja be a pom.xml fájlt.

1. Egy szövegszerkesztőben nyissa meg a trigger-reboot\src\main\java\com\mycompany\app\App.java forrásfájl.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

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

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{youriothubconnectionstring}` az IoT hub kapcsolati karakterlánccal feljegyzett a *létrehoz egy IoT-központot* szakasz:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. A szál, amely a jelentésben szereplő tulajdonságok beolvassa az eszköz kettős 10 másodpercenként alkalmazásához adja hozzá a következő beágyazott osztály a **App** osztály:

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

1. Módosítsa a aláírása a **fő** metódust kell küldeni a következő kivétel miatt:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. A rendszer újraindítása a szimulált eszköz közvetlen metódus meghívásához, adja hozzá az alábbi kódot a **fő** módszert:

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

1. És kérdezze le a jelentett tulajdonságok a szimulált eszköz eltávolítása a szálat indítani, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Ahhoz, hogy állítsa le az alkalmazást, adja hozzá a következő kódot a **fő** módszert:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Mentse és zárja be a trigger-reboot\src\main\java\com\mycompany\app\App.java fájlt.

1. Build a **eseményindító-újraindítás** háttér-alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg az eseményindító-újraindítás mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amely az eszköz szimulálja. Az alkalmazás figyeli, az újraindítást követően a közvetlen metódus hívása az IoT hub, és azonnal válaszol-e, hogy a hívást. A alkalmazást, majd a alszik egy ideig, hogy újraindítás szimulálja értesíteni jelentett tulajdonság használata előtt a **eseményindító-újraindítás** háttér-alkalmazást, hogy az újraindítás befejeződött.

1. A dm-get-started mappában hozzon létre egy Maven project nevű **szimulált eszköz** parancsot a parancssorba az alábbi parancs segítségével. A következő egy olyan nagyméretű, sok parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban lépjen a simulated-device mappára.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a szimulált eszköz mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával][lnk-maven-device-search] tekintheti meg.

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

1. Mentse és zárja be a pom.xml fájlt.

1. Egy szövegszerkesztőben nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java forrásfájl.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

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

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{yourdeviceconnectionstring}` feljegyzett eszköz kapcsolati karakterlánccal rendelkező a *hozzon létre egy eszközidentitás* szakasz:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Egy visszahívás-kezelő, a közvetlen módszer állapoteseményeit alkalmazásához adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Egy visszahívás-kezelő eszköz iker állapoteseményeit a alkalmazásához adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Egy visszahívás-kezelő tulajdonság események alkalmazásához adja hozzá a következő beágyazott osztályt a **App** osztály:

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

1. A szál az eszköz újraindítás szimulálásához alkalmazásához adja hozzá a következő beágyazott osztályt a **App** osztály. A szál öt másodpercenként alvó állapotba kerül, és ezután beállítja a **lastReboot** tulajdonság jelentette:

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

1. Az eszközön a közvetlen módszer alkalmazásához adja hozzá a következő beágyazott osztály a **App** osztály. Ha a szimulált alkalmazások a hívást kap a **újraindítás** közvetlen módszer azt nyugtázást visszatér a hívó, majd elindítja az újraindítás feldolgozására szolgáló szálat:

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

1. Aláírását, valamint módosíthatja a **fő** metódust kell küldeni a következő kivételekkel:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Példányt létrehozni egy **DeviceClient**, adja hozzá a következő kódot a **fő** módszert:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Megkezdeni a figyelést közvetlen metódushívások, adja hozzá a következő kódot a **fő** módszert:

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

1. Állítsa le az eszköz szimulátor, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

1. Build a **szimulált eszköz** háttér-alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg a szimulált eszköz mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. Parancsot egy parancssorba a szimulált eszköz mappában futtassa a következő parancsot az IoT hub az újraindítás metódushívások figyelését:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub szimulált eszköz alkalmazás figyelését, indítsa újra közvetlen metódushívások][1]

1. A parancssorba az eseményindító-újraindítás mappában futtassa a következő parancsot a rendszer újraindítása metódusát hívja a szimulált eszköz az IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-központ szolgáltatás alkalmazásnak, hogy a rendszer újraindítása közvetlen metódus hívása][2]

1. A szimulált eszköz válaszol-e a rendszer újraindítása közvetlen metódus hívása:

    ![Java IoT Hub szimulált eszköz alkalmazásának válaszol-e a közvetlen metódus hívása][3]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[1]: ./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png
[2]: ./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png
[3]: ./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png
<!-- Links -->

[lnk-maven]: https://maven.apache.org/what-is-maven.html

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22