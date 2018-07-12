---
title: Az Azure IoT Hub-Eszközfelügyelet (Java) – első lépések |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub-Eszközfelügyelet egy távoli eszköz-újraindítás kezdeményezése. Az Azure IoT eszközoldali SDK a Javához készült segítségével megvalósítani egy szimulált eszközalkalmazás, amely tartalmazza a közvetlen metódus és az Azure IoT Szolgáltatásoldali SDK-val Java megvalósítása a service-alkalmazás, amely a közvetlen metódust hív meg.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6a4ba8c2b88520dff028610cf64aa9b3a6e3fefd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38481973"
---
# <a name="get-started-with-device-management-java"></a>Ismerkedés az eszközfelügyelettel (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure portal használatával hozzon létre egy IoT hubot, és hozzon létre egy új eszközidentitást az IoT hubban.
* Egy szimulált eszközalkalmazás létrehozása, amely valósítja meg az eszköz újraindítását közvetlen metódus. Közvetlen metódusok a felhő kerül meghívásra.
* Hozzon létre egy alkalmazást, amely az IoT hub segítségével a szimulált eszközalkalmazás újraindítás közvetlen metódust hív meg. Ez az alkalmazás és az eszközről, tekintse meg az újraindítás művelet befejezésekor a jelentett tulajdonságokat figyeli.

Ez az oktatóanyag végén két Java-konzolalkalmazással fog rendelkezni:

**a szimulált eszköz**. Ez az alkalmazás:

* A korábban létrehozott eszközidentitással csatlakozik az IoT hubnak.
* Újraindítás közvetlen metódus hívása kap.
* A fizikai számítógép újraindítása szimulálja.
* Jelentések a jelentett tulajdonságok keresztül az utolsó újraindítás időpontja.

**eseményindító-újraindítás**. Ez az alkalmazás:

* Közvetlen metódus a szimulált eszközalkalmazás-hívások.
* Megjeleníti a közvetlen metódus meghívása a szimulált eszköz által küldött válasz
* Megjeleníti a frissített jelentett tulajdonságokként.

> [!NOTE]
> Az SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához kapcsolatos információkért lásd: [Azure IoT SDK-k][lnk-hub-sdks].

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Java SE 8. <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a Javát ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* Maven 3  <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a [Mavent][lnk-maven] ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* [A node.js verzió: 0.10.0-s vagy újabb](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>A távoli közvetlen metódus az eszközön újra kell indítani az eseményindító

Ebben a szakaszban hoz létre egy Java-konzolalkalmazást, amely:

1. A szimulált eszközalkalmazás újraindítás közvetlen metódust hív meg.
1. A válasz megjeleníti.
1. A jelentett tulajdonságokat határozza meg, ha az újraindítás befejeződött-e az eszköz által küldött lekérdezések.

A Konzolalkalmazás csatlakozik az IoT Hub a közvetlen metódus meghívása, és olvassa el a jelentett tulajdonságokat.

1. Hozzon létre egy dm-get-started nevű üres mappát.

1. A dm-get-started mappában hozzon létre egy nevű Maven-projektet **eseményindító-újraindítás** használja az alábbi parancsot a parancssorba. Az alábbiakban látható egy egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban keresse meg az eseményindító-újraindítás mappát.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a trigger újraindítás mappában, és adja hozzá a következő függőséget a **függőségek** csomópont. Ezzel a függőséggel használhatja az iot-service-client csomagot az alkalmazásban, az IoT hubbal való kommunikációhoz:

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

1. Adja hozzá a következő **összeállítása** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a használható a Java 1.8-as hozhat létre az alkalmazást a maven használatával:

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

1. Egy szövegszerkesztővel nyissa meg a trigger-reboot\src\main\java\com\mycompany\app\App.java forrásfájl.

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

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{youriothubconnectionstring}` feljegyzett IoT hub kapcsolati karakterláncra az *hozzon létre egy IoT hubot* szakaszban:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. A hozzászóláslánc, amely 10 másodpercenként beolvassa a jelentett tulajdonságok az ikereszközről implementálásához adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

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

1. Módosítsa az aláírást, a **fő** metódust a következő meghiúsuló:

    ```java
    public static void main(String[] args) throws IOException
    ```

1. A szimulált eszköz újraindítását közvetlen metódus meghívása, adja hozzá a következő kódot a **fő** módszer:

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

1. A szimulált eszköz a jelentett tulajdonságok lekérdezni a szál indítása, adja hozzá a következő kódot a **fő** módszer:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

1. Ahhoz, hogy állítsa le az alkalmazást, adja hozzá a következő kódot a **fő** módszer:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

1. Mentse és zárja be a trigger-reboot\src\main\java\com\mycompany\app\App.java fájlt.

1. Hozhat létre a **eseményindító-újraindítás** háttéralkalmazáshoz, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg az eseményindító-újraindítás mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Java-konzolalkalmazást, amely a küldő eszközt szimulál hoz létre. Az alkalmazás figyeli, az újraindítás a közvetlen metódus meghívása az IoT hubról, és azonnal Ez a meghívás válaszol. Az alkalmazást, majd egy ideig, az újraindítás folyamat szimulálásához előtt értesíteni a jelentett tulajdonságok alszik a **eseményindító-újraindítás** a háttéralkalmazás, hogy az újraindítás befejeződött.

1. A dm-get-started mappában hozzon létre egy nevű Maven-projektet **simulated-device** használja az alábbi parancsot a parancssorba. A következő egy egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban lépjen a simulated-device mappára.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a simulated-device mappában, és adja hozzá a következő függőséget a **függőségek** csomópont. Ezzel a függőséggel használhatja az iot-service-client csomagot az alkalmazásban, az IoT hubbal való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával][lnk-maven-device-search] tekintheti meg.

1. Adja hozzá a következő **összeállítása** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a használható a Java 1.8-as hozhat létre az alkalmazást a maven használatával:

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

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java forrásfájl.

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

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{yourdeviceconnectionstring}` az eszköz kapcsolati karakterlánccal feljegyzett a *eszközidentitás létrehozása* szakaszban:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

1. Egy visszahívás-kezelő a közvetlen metódus állapotesemények implementálásához adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Egy visszahívás-kezelő a device twin állapoteseményeit implementálásához adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Egy visszahívás-kezelő tulajdonság események implementálásához adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

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

1. Az eszköz-újraindítás szimulálásához szál implementálásához adja hozzá a következő beágyazott osztály a **alkalmazás** osztály. A szál öt másodpercre alvó állapotba vált, és ezután beállítja a **lastReboot** jelentett tulajdonság:

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

1. A közvetlen metódus megvalósításához az eszközön, adja hozzá a következő beágyazott osztály a **alkalmazás** osztály. Amikor a szimulált alkalmazások fogadja a hívást a **újraindítás** közvetlen módszer azt adja vissza a hívónak nyugtázást, majd elindítja az újraindítás feldolgozására szolgáló szálat:

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

1. Módosítsa az aláírást, a **fő** metódus állíthatunk munkába a következő kivételek érvényesek:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Példányosítása egy **DeviceClient**, adja hozzá a következő kódot a **fő** módszer:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

1. Megkezdeni a figyelést a közvetlen metódust hívja, adja hozzá a következő kódot a **fő** módszer:

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

1. Állítsa le a készülékszimulátort, adja hozzá a következő kódot a **fő** módszer:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

1. Hozhat létre a **simulated-device** háttéralkalmazáshoz, és kijavíthatja az esetleges hibákat. A parancssorban lépjen a simulated-device mappában, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A simulated-device mappában egy parancssorban futtassa a következő parancsot, amellyel megkezdheti a figyeli a újraindítás metódust hívja az IoT hubról:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![A Java IoT Hub szimulált eszközalkalmazás figyelését, indítsa újra a közvetlen metódust hívja][1]

1. Az eseményindító-újraindítás mappában egy parancssorban futtassa a következő parancsot az újraindítás metódusának meghívása a szimulált eszközt az IoT hubról:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![A Java IoT Hub-szolgáltatásalkalmazás újraindítás közvetlen metódus meghívása][2]

1. A szimulált eszköz válaszol az újraindítás közvetlen metódus hívása:

    ![A Java IoT Hub szimulált eszközalkalmazás válaszol a közvetlen metódus meghívása][3]

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